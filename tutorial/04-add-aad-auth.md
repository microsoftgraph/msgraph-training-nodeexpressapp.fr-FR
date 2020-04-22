<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la bibliothèque [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) dans l’application.

1. Créez un fichier nommé `.env` file à la racine de votre application et ajoutez le code suivant.

    :::code language="ini" source="../demo/graph-tutorial/.env.example":::

    Remplacez `YOUR APP ID HERE` par l’ID de l’application dans le portail d’inscription de `YOUR APP SECRET HERE` l’application et remplacez par le mot de passe que vous avez généré.

    > [!IMPORTANT]
    > Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `.env` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application et de votre mot de passe.

1. Ouvrez `./app.js` et ajoutez la ligne suivante en haut du fichier pour charger le `.env` fichier.

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a>Implémentation de la connexion

1. Localisez la `var indexRouter = require('./routes/index');` ligne `./app.js`dans. Insérez le code suivant **avant** cette ligne.

    ```javascript
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
        return done(new Error("No OID found in user profile."));
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

1. Localisez la `app.use('/', indexRouter);` ligne `./app.js`dans. Insérez le code suivant **avant** cette ligne.

    ```javascript
    // Initialize passport
    app.use(passport.initialize());
    app.use(passport.session());
    ```

1. Créez un fichier dans le `./routes` répertoire nommé `auth.js` et ajoutez le code suivant.

    ```javascript
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
            failureFlash: true,
            successRedirect: '/'
          }
        )(req,res,next);
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

1. Ouvrez `./app.js` et insérez le code suivant **avant** la `var app = express();` ligne.

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. Insérez le code suivant **après** la `app.use('/', indexRouter);` ligne.

    ```javascript
    app.use('/auth', authRouter);
    ```

Démarrez le serveur et accédez à `https://localhost:3000`. Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`. Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées. Le navigateur vous redirige vers l’application, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

1. Créez un fichier à la racine du projet nommé `graph.js` et ajoutez le code suivant.

    ```javascript
    var graph = require('@microsoft/microsoft-graph-client');
    require('isomorphic-fetch');

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

1. Ouvrez `/app.js` et ajoutez les instructions `require` suivantes en haut du fichier.

    ```javascript
    var graph = require('./graph');
    ```

1. Remplacez la fonction `signInComplete` existante par le code suivant.

    ```javascript
    async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
      if (!profile.oid) {
        return done(new Error("No OID found in user profile."));
      }

      try{
        const user = await graph.getUserDetails(accessToken);

        if (user) {
          // Add properties to profile
          profile['email'] = user.mail ? user.mail : user.userPrincipalName;
        }
      } catch (err) {
        return done(err);
      }

      // Save the profile and tokens in user storage
      users[profile.oid] = { profile, accessToken };
      return done(null, users[profile.oid]);
    }
    ```

    Le nouveau code met à jour le `profile` passeport fourni pour ajouter une `email` propriété à l’aide des données renvoyées par Microsoft Graph.

1. Ajoutez ce qui **after** suit après `app.use(passport.session());` la ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="AddProfileSnippet":::

    Ce code charge le profil utilisateur dans la `locals` propriété de la réponse. Cela permet de le mettre à la disposition de toutes les vues dans l’application.

## <a name="storing-the-tokens"></a>Stockage des jetons

Maintenant que vous pouvez obtenir des jetons, nous vous conseillons d’implémenter un moyen de les stocker dans l’application. Actuellement, l’application stocke le jeton d’accès brut dans le stockage utilisateur en mémoire. Étant donné qu’il s’agit d’un exemple d’application, par souci de simplicité, vous continuerez à les stocker. Une application réelle utilise une solution de stockage sécurisé plus fiable, comme une base de données.

Toutefois, le fait de stocker uniquement le jeton d’accès ne vous permet pas de vérifier l’expiration ou d’actualiser le jeton. Pour l’activer, mettez à jour l’exemple pour encapsuler les jetons dans `AccessToken` un objet à `simple-oauth2` partir de la bibliothèque.

1. Ouvrez `./app.js` et ajoutez le code suivant **avant** la `signInComplete` fonction.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="ConfigureOAuth2Snippet":::

1. Remplacez la fonction `signInComplete` existante par ce qui suit.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SignInCompleteSnippet" highlight="17-18, 21":::

1. Remplacez l’itinéraire de `./routes/auth.js` rappel existant par ce qui suit.

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackRouteSnippet" highlight="17-18":::

1. Redémarrez le serveur et suivez le processus de connexion. Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** . Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualisation des jetons

À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. C’est là que le jeton d’actualisation devient utile. Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter.

1. Créez un fichier à la racine du projet nommé `tokens.js` pour conserver les fonctions de gestion des jetons. Ajoutez le code suivant.

    :::code language="javascript" source="../demo/graph-tutorial/tokens.js" id="TokensSnippet":::

Cette méthode vérifie d’abord si le jeton d’accès a expiré ou s’il arrive à expiration. Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis il met à jour le cache et renvoie le nouveau jeton d’accès. Vous utiliserez cette méthode chaque fois que vous aurez besoin de récupérer le jeton d’accès.
