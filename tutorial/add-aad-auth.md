<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la bibliothèque [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) dans l’application.

Créez un fichier nommé `.env` file à la racine de votre application et ajoutez le code suivant.

```text
OAUTH_APP_ID=YOUR_APP_ID_HERE
OAUTH_APP_PASSWORD=YOUR_APP_PASSWORD_HERE
OAUTH_REDIRECT_URI=http://localhost:3000/auth/callback
OAUTH_SCOPES='profile offline_access user.read calendars.read'
OAUTH_AUTHORITY=https://login.microsoftonline.com/common
OAUTH_ID_METADATA=/v2.0/.well-known/openid-configuration
OAUTH_AUTHORIZE_ENDPOINT=/oauth2/v2.0/authorize
OAUTH_TOKEN_ENDPOINT=/oauth2/v2.0/token
```

Remplacez `YOUR APP ID HERE` par l’ID de l’application dans le portail d’inscription de `YOUR APP SECRET HERE` l’application et remplacez par le mot de passe que vous avez généré.

> [!IMPORTANT]
> Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `.env` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application et de votre mot de passe.

Ouvrez `./app.js` et ajoutez la ligne suivante en haut du fichier pour charger le `.env` fichier.

```js
require('dotenv').config();
```

## <a name="implement-sign-in"></a>Mettre en œuvre la connexion

Localisez la `var indexRouter = require('./routes/index');` ligne `./app.js`dans. Insérez le code suivant **avant** cette ligne.

```js
var passport = require('passport');
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Configure passport

// In-memory storage of logged-in users
// For demo purposes only, production apps should store
// this in a reliable storage
var users = {};

// Passport calls serializeUser and deserializeUser to
// manage users
passport.serializeUser(function(user, done) {
  // Use the OID property of the user as a key
  users[user.profile.oid] = user;
  done (null, user.profile.oid);
});

passport.deserializeUser(function(id, done) {
  done(null, users[id]);
});

// Callback function called once the sign-in is complete
// and an access token has been obtained
async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
  if (!profile.oid) {
    return done(new Error("No OID found in user profile."), null);
  }

  // Save the profile and tokens in user storage
  users[profile.oid] = { profile, accessToken };
  return done(null, users[profile.oid]);
}

// Configure OIDC strategy
passport.use(new OIDCStrategy(
  {
    identityMetadata: `${process.env.OAUTH_AUTHORITY}${process.env.OAUTH_ID_METADATA}`,
    clientID: process.env.OAUTH_APP_ID,
    responseType: 'code id_token',
    responseMode: 'form_post',
    redirectUrl: process.env.OAUTH_REDIRECT_URI,
    allowHttpForRedirectUrl: true,
    clientSecret: process.env.OAUTH_APP_PASSWORD,
    validateIssuer: false,
    passReqToCallback: false,
    scope: process.env.OAUTH_SCOPES.split(' ')
  },
  signInComplete
));
```

Ce code initialise la bibliothèque [Passport. js](http://www.passportjs.org/) pour utiliser la `passport-azure-ad` Bibliothèque et la configure avec l’ID d’application et le mot de passe de l’application.

À présent, `passport` transmettez l’objet à l’application expresse. Localisez la `app.use('/', indexRouter);` ligne `./app.js`dans. Insérez le code suivant **avant** cette ligne.

```js
// Initialize passport
app.use(passport.initialize());
app.use(passport.session());
```

Créez un fichier dans le `./routes` répertoire nommé `auth.js` et ajoutez le code suivant.

```js
var express = require('express');
var passport = require('passport');
var router = express.Router();

/* GET auth callback. */
router.get('/signin',
  function  (req, res, next) {
    passport.authenticate('azuread-openidconnect',
      {
        response: res,
        prompt: 'login',
        failureRedirect: '/',
        failureFlash: true
      }
    )(req,res,next);
  },
  function(req, res) {
    res.redirect('/');
  }
);

router.post('/callback',
  function(req, res, next) {
    passport.authenticate('azuread-openidconnect',
      {
        response: res,
        failureRedirect: '/',
        failureFlash: true
      }
    )(req,res,next);
  },
  function(req, res) {
    // TEMPORARY!
    // Flash the access token for testing purposes
    req.flash('error_msg', {message: 'Access token', debug: req.user.accessToken});
    res.redirect('/');
  }
);

router.get('/signout',
  function(req, res) {
    req.session.destroy(function(err) {
      req.logout();
      res.redirect('/');
    });
  }
);

module.exports = router;
```

Cette définition définit un routeur avec trois itinéraires `signin`: `callback`, et `signout`.

L' `signin` itinéraire appelle la `passport.authenticate` méthode, ce qui entraîne la redirection de l’application vers la page de connexion Azure.

L' `callback` itinéraire est l’endroit où Azure redirige une fois la connexion terminée. Le code appelle de `passport.authenticate` nouveau la méthode, ce `passport-azure-ad` qui entraîne la demande d’un jeton d’accès. Une fois le jeton obtenu, le gestionnaire suivant est appelé, qui redirige vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire. Nous allons l’utiliser pour vérifier que notre connexion fonctionne avant de poursuivre. Avant de tester, nous devons configurer l’application Express de sorte qu’elle utilise le nouveau `./routes/auth.js`routeur à partir de.

La `signout` méthode déconnecte l’utilisateur et détruit la session.

Insérez le code suivant **avant** la `var app = express();` ligne.

```js
var authRouter = require('./routes/auth');
```

Insérez ensuite le code suivant **après** la `app.use('/', indexRouter);` ligne.

```js
app.use('/auth', authRouter);
```

Démarrez le serveur et accédez à `https://localhost:3000`. Cliquez sur le bouton de connexion et vous devez être redirigé vers `https://login.microsoftonline.com`. Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées. Le navigateur redirige vers l’application en affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Commencez par créer un fichier qui contiendra tous vos appels Microsoft Graph. Créez un fichier à la racine du projet nommé `graph.js` et ajoutez le code suivant.

```js
var graph = require('@microsoft/microsoft-graph-client');

module.exports = {
  getUserDetails: async function(accessToken) {
    const client = getAuthenticatedClient(accessToken);

    const user = await client.api('/me').get();
    return user;
  }
};

function getAuthenticatedClient(accessToken) {
  // Initialize Graph client
  const client = graph.Client.init({
    // Use the provided access token to authenticate
    // requests
    authProvider: (done) => {
      done(null, accessToken);
    }
  });

  return client;
}
```

Cette méthode exporte la `getUserDetails` fonction, qui utilise le kit de développement logiciel `/me` (SDK) Microsoft Graph pour appeler le point de terminaison et renvoyer le résultat.

Mettez à `signInComplete` jour la `/app.s` méthode dans pour appeler cette fonction. Tout d’abord, ajoutez `require` les instructions suivantes en haut du fichier.

```js
var graph = require('./graph');
```

Remplacez la fonction `signInComplete` existante par le code suivant.

```js
async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
  if (!profile.oid) {
    return done(new Error("No OID found in user profile."), null);
  }

  try{
    const user = await graph.getUserDetails(accessToken);

    if (user) {
      // Add properties to profile
      profile['email'] = user.mail ? user.mail : user.userPrincipalName;
    }
  } catch (err) {
    done(err, null);
  }

  // Save the profile and tokens in user storage
  users[profile.oid] = { profile, accessToken };
  return done(null, users[profile.oid]);
}
```

Le nouveau code met à jour le `profile` passeport fourni pour ajouter une `email` propriété à l’aide des données renvoyées par Microsoft Graph.

Enfin, ajoutez du code `./app.js` pour charger le profil utilisateur dans la `locals` propriété de la réponse. Cela permet de le mettre à la disposition de toutes les vues dans l’application.

Ajoutez ce qui **** suit après `app.use(passport.session());` la ligne.

```js
app.use(function(req, res, next) {
  // Set the authenticated user in the
  // template locals
  if (req.user) {
    res.locals.user = req.user.profile;
  }
  next();
});
```

## <a name="storing-the-tokens"></a>Stockage des jetons

Maintenant que vous pouvez obtenir des jetons, il est temps de mettre en œuvre un moyen de les stocker dans l’application. Actuellement, l’application stocke le jeton d’accès brut dans le stockage utilisateur en mémoire. Étant donné qu’il s’agit d’un exemple d’application, par souci de simplicité, vous continuerez à les stocker. Une application réelle utiliserait une solution de stockage sécurisé plus fiable, comme une base de données.

Toutefois, le fait de stocker uniquement le jeton d’accès ne vous permet pas de vérifier l’expiration ou d’actualiser le jeton. Pour l’activer, mettez à jour l’exemple pour encapsuler les jetons dans `AccessToken` un objet à `simple-oauth2` partir de la bibliothèque.

Tout d’abord `./app.js`, dans, ajoutez le code suivant `signInComplete` **avant** la fonction.

```js
// Configure simple-oauth2
const oauth2 = require('simple-oauth2').create({
  client: {
    id: process.env.OAUTH_APP_ID,
    secret: process.env.OAUTH_APP_PASSWORD
  },
  auth: {
    tokenHost: process.env.OAUTH_AUTHORITY,
    authorizePath: process.env.OAUTH_AUTHORIZE_ENDPOINT,
    tokenPath: process.env.OAUTH_TOKEN_ENDPOINT
  }
});
```

Ensuite, mettez à `signInComplete` jour la fonction pour `AccessToken` créer un à partir des jetons bruts transmis et stockez celui-ci dans le stockage utilisateur. Remplacez la fonction `signInComplete` existante par ce qui suit.

```js
async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
  if (!profile.oid) {
    return done(new Error("No OID found in user profile."), null);
  }

  try{
    const user = await graph.getUserDetails(accessToken);

    if (user) {
      // Add properties to profile
      profile['email'] = user.mail ? user.mail : user.userPrincipalName;
    }
  } catch (err) {
    done(err, null);
  }

  // Create a simple-oauth2 token from raw tokens
  let oauthToken = oauth2.accessToken.create(params);

  // Save the profile and tokens in user storage
  users[profile.oid] = { profile, oauthToken };
  return done(null, users[profile.oid]);
}
```

Mettez à `callback` jour l' `./routes/auth.js` itinéraire dans pour `req.flash` supprimer la ligne avec le jeton d’accès. L' `callback` itinéraire doit ressembler à ce qui suit.

```js
router.post('/callback',
  function(req, res, next) {
    passport.authenticate('azuread-openidconnect',
      {
        response: res,
        failureRedirect: '/',
        failureFlash: true
      }
    )(req,res,next);
  },
  function(req, res) {
    res.redirect('/');
  }
);
```

Redémarrez le serveur et suivez le processus de connexion. Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour **** accéder au lien Déconnexion. Cliquez **** sur Déconnexion pour réinitialiser la session et revenir à la page d’accueil.

![Capture d’écran du menu déroulant avec le lien Déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualisation des jetons

À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.

Toutefois, ce jeton est éphémère. Le jeton expire une heure après son émission. C’est ici que le jeton d’actualisation devient utile. Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans demander à l’utilisateur de se reconnecter.

Pour ce faire, créez un fichier à la racine du projet nommé `tokens.js` pour conserver les fonctions de gestion des jetons. Ajoutez le code suivant.

```js
module.exports = {
  getAccessToken: async function(req) {
    if (req.user) {
      // Get the stored token
      var storedToken = req.user.oauthToken;

      if (storedToken) {
        if (storedToken.expired()) {
          // refresh token
          var newToken = await storedToken.refresh();

          // Update stored token
          req.user.oauthToken = newToken;
          return newToken.token.access_token;
        }

        // Token still valid, just return it
        return storedToken.token.access_token;
      }
    }
  }
};
```

Cette méthode vérifie d’abord si le jeton d’accès a expiré ou s’il arrive à expiration. Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis il met à jour le cache et renvoie le nouveau jeton d’accès. Vous utiliserez cette méthode chaque fois que vous aurez besoin de récupérer le jeton d’accès.
