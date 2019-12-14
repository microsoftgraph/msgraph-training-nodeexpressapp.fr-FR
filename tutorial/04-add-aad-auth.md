<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c722d-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c722d-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="c722d-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c722d-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="c722d-103">Dans cette étape, vous allez intégrer la bibliothèque [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c722d-103">In this step you will integrate the [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) library into the application.</span></span>

<span data-ttu-id="c722d-104">Créez un fichier nommé `.env` file à la racine de votre application et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c722d-104">Create a new file named `.env` file in the root of your application, and add the following code.</span></span>

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

<span data-ttu-id="c722d-105">Remplacez `YOUR APP ID HERE` par l’ID de l’application dans le portail d’inscription de `YOUR APP SECRET HERE` l’application et remplacez par le mot de passe que vous avez généré.</span><span class="sxs-lookup"><span data-stu-id="c722d-105">Replace `YOUR APP ID HERE` with the application ID from the Application Registration Portal, and replace `YOUR APP SECRET HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c722d-106">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `.env` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application et de votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c722d-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="c722d-107">Ouvrez `./app.js` et ajoutez la ligne suivante en haut du fichier pour charger le `.env` fichier.</span><span class="sxs-lookup"><span data-stu-id="c722d-107">Open `./app.js` and add the following line to the top of the file to load the `.env` file.</span></span>

```js
require('dotenv').config();
```

## <a name="implement-sign-in"></a><span data-ttu-id="c722d-108">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="c722d-108">Implement sign-in</span></span>

<span data-ttu-id="c722d-109">Localisez la `var indexRouter = require('./routes/index');` ligne `./app.js`dans.</span><span class="sxs-lookup"><span data-stu-id="c722d-109">Locate the line `var indexRouter = require('./routes/index');` in `./app.js`.</span></span> <span data-ttu-id="c722d-110">Insérez le code suivant **avant** cette ligne.</span><span class="sxs-lookup"><span data-stu-id="c722d-110">Insert the following code **before** that line.</span></span>

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

<span data-ttu-id="c722d-111">Ce code initialise la bibliothèque [Passport. js](http://www.passportjs.org/) pour utiliser la `passport-azure-ad` Bibliothèque et la configure avec l’ID d’application et le mot de passe de l’application.</span><span class="sxs-lookup"><span data-stu-id="c722d-111">This code initializes the [Passport.js](http://www.passportjs.org/) library to use the `passport-azure-ad` library, and configures it with the app ID and password for the app.</span></span>

<span data-ttu-id="c722d-112">À présent, `passport` transmettez l’objet à l’application expresse.</span><span class="sxs-lookup"><span data-stu-id="c722d-112">Now pass the `passport` object to the Express app.</span></span> <span data-ttu-id="c722d-113">Localisez la `app.use('/', indexRouter);` ligne `./app.js`dans.</span><span class="sxs-lookup"><span data-stu-id="c722d-113">Locate the line `app.use('/', indexRouter);` in `./app.js`.</span></span> <span data-ttu-id="c722d-114">Insérez le code suivant **avant** cette ligne.</span><span class="sxs-lookup"><span data-stu-id="c722d-114">Insert the following code **before** that line.</span></span>

```js
// Initialize passport
app.use(passport.initialize());
app.use(passport.session());
```

<span data-ttu-id="c722d-115">Créez un fichier dans le `./routes` répertoire nommé `auth.js` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c722d-115">Create a new file in the `./routes` directory named `auth.js` and add the following code.</span></span>

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

<span data-ttu-id="c722d-116">Cette définition définit un routeur avec trois itinéraires `signin`: `callback`, et `signout`.</span><span class="sxs-lookup"><span data-stu-id="c722d-116">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

<span data-ttu-id="c722d-117">L' `signin` itinéraire appelle la `passport.authenticate` méthode, ce qui entraîne la redirection de l’application vers la page de connexion Azure.</span><span class="sxs-lookup"><span data-stu-id="c722d-117">The `signin` route calls the `passport.authenticate` method, causing the app to redirect to the Azure login page.</span></span>

<span data-ttu-id="c722d-118">L' `callback` itinéraire est l’endroit où Azure redirige une fois la connexion terminée.</span><span class="sxs-lookup"><span data-stu-id="c722d-118">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="c722d-119">Le code appelle de `passport.authenticate` nouveau la méthode, ce `passport-azure-ad` qui entraîne la demande d’un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="c722d-119">The code calls the `passport.authenticate` method again, causing the `passport-azure-ad` strategy to request an access token.</span></span> <span data-ttu-id="c722d-120">Une fois le jeton obtenu, le gestionnaire suivant est appelé, qui redirige vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire.</span><span class="sxs-lookup"><span data-stu-id="c722d-120">Once the token is obtained, the next handler is called, which redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="c722d-121">Nous allons l’utiliser pour vérifier que notre connexion fonctionne avant de poursuivre.</span><span class="sxs-lookup"><span data-stu-id="c722d-121">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="c722d-122">Avant de tester, nous devons configurer l’application Express de sorte qu’elle utilise le nouveau `./routes/auth.js`routeur à partir de.</span><span class="sxs-lookup"><span data-stu-id="c722d-122">Before we test, we need to configure the Express app to use the new router from `./routes/auth.js`.</span></span>

<span data-ttu-id="c722d-123">La `signout` méthode déconnecte l’utilisateur et détruit la session.</span><span class="sxs-lookup"><span data-stu-id="c722d-123">The `signout` method logs the user out and destroys the session.</span></span>

<span data-ttu-id="c722d-124">Insérez le code suivant **avant** la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="c722d-124">Insert the following code **before** the `var app = express();` line.</span></span>

```js
var authRouter = require('./routes/auth');
```

<span data-ttu-id="c722d-125">Insérez ensuite le code suivant **après** la `app.use('/', indexRouter);` ligne.</span><span class="sxs-lookup"><span data-stu-id="c722d-125">Then insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

```js
app.use('/auth', authRouter);
```

<span data-ttu-id="c722d-126">Démarrez le serveur et accédez à `https://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="c722d-126">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="c722d-127">Cliquez sur le bouton de connexion et vous devez être redirigé vers `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="c722d-127">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="c722d-128">Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="c722d-128">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="c722d-129">Le navigateur redirige vers l’application en affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="c722d-129">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="c722d-130">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="c722d-130">Get user details</span></span>

<span data-ttu-id="c722d-131">Commencez par créer un fichier qui contiendra tous vos appels Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c722d-131">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="c722d-132">Créez un fichier à la racine du projet nommé `graph.js` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c722d-132">Create a new file in the root of the project named `graph.js` and add the following code.</span></span>

```js
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

<span data-ttu-id="c722d-133">Cette méthode exporte la `getUserDetails` fonction, qui utilise le kit de développement logiciel `/me` (SDK) Microsoft Graph pour appeler le point de terminaison et renvoyer le résultat.</span><span class="sxs-lookup"><span data-stu-id="c722d-133">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="c722d-134">Mettez à `signInComplete` jour la `/app.js` méthode dans pour appeler cette fonction.</span><span class="sxs-lookup"><span data-stu-id="c722d-134">Update the `signInComplete` method in `/app.js` to call this function.</span></span> <span data-ttu-id="c722d-135">Tout d’abord, ajoutez `require` les instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="c722d-135">First, add the following `require` statements to the top of the file.</span></span>

```js
var graph = require('./graph');
```

<span data-ttu-id="c722d-136">Remplacez la fonction `signInComplete` existante par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c722d-136">Replace the existing `signInComplete` function with the following code.</span></span>

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

<span data-ttu-id="c722d-137">Le nouveau code met à jour le `profile` passeport fourni pour ajouter une `email` propriété à l’aide des données renvoyées par Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c722d-137">The new code updates the `profile` provided by Passport to add an `email` property, using the data returned by Microsoft Graph.</span></span>

<span data-ttu-id="c722d-138">Enfin, ajoutez du code `./app.js` pour charger le profil utilisateur dans la `locals` propriété de la réponse.</span><span class="sxs-lookup"><span data-stu-id="c722d-138">Finally, add code to `./app.js` to load the user profile into the `locals` property of the response.</span></span> <span data-ttu-id="c722d-139">Cela permet de le mettre à la disposition de toutes les vues dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c722d-139">This will make it available to all of the views in the app.</span></span>

<span data-ttu-id="c722d-140">Ajoutez ce qui \*\*\*\* suit après `app.use(passport.session());` la ligne.</span><span class="sxs-lookup"><span data-stu-id="c722d-140">Add the following **after** the `app.use(passport.session());` line.</span></span>

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

## <a name="storing-the-tokens"></a><span data-ttu-id="c722d-141">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="c722d-141">Storing the tokens</span></span>

<span data-ttu-id="c722d-142">Maintenant que vous pouvez obtenir des jetons, il est temps de mettre en œuvre un moyen de les stocker dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c722d-142">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="c722d-143">Actuellement, l’application stocke le jeton d’accès brut dans le stockage utilisateur en mémoire.</span><span class="sxs-lookup"><span data-stu-id="c722d-143">Currently, the app is storing the raw access token in the in-memory user storage.</span></span> <span data-ttu-id="c722d-144">Étant donné qu’il s’agit d’un exemple d’application, par souci de simplicité, vous continuerez à les stocker.</span><span class="sxs-lookup"><span data-stu-id="c722d-144">Since this is a sample app, for simplicity's sake, you'll continue to store them there.</span></span> <span data-ttu-id="c722d-145">Une application réelle utiliserait une solution de stockage sécurisé plus fiable, comme une base de données.</span><span class="sxs-lookup"><span data-stu-id="c722d-145">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="c722d-146">Toutefois, le fait de stocker uniquement le jeton d’accès ne vous permet pas de vérifier l’expiration ou d’actualiser le jeton.</span><span class="sxs-lookup"><span data-stu-id="c722d-146">However, storing just the access token doesn't allow you to check expiration or refresh the token.</span></span> <span data-ttu-id="c722d-147">Pour l’activer, mettez à jour l’exemple pour encapsuler les jetons dans `AccessToken` un objet à `simple-oauth2` partir de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="c722d-147">In order to enable that, update the sample to wrap the tokens in an `AccessToken` object from the `simple-oauth2` library.</span></span>

<span data-ttu-id="c722d-148">Tout d’abord `./app.js`, dans, ajoutez le code suivant `signInComplete` **avant** la fonction.</span><span class="sxs-lookup"><span data-stu-id="c722d-148">First, in `./app.js`, add the following code **before** the `signInComplete` function.</span></span>

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

<span data-ttu-id="c722d-149">Ensuite, mettez à `signInComplete` jour la fonction pour `AccessToken` créer un à partir des jetons bruts transmis et stockez celui-ci dans le stockage utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c722d-149">Then, update the `signInComplete` function to create an `AccessToken` from the raw tokens passed in and store that in the user storage.</span></span> <span data-ttu-id="c722d-150">Remplacez la fonction `signInComplete` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c722d-150">Replace the existing `signInComplete` function with the following.</span></span>

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

<span data-ttu-id="c722d-151">Mettez à `callback` jour l' `./routes/auth.js` itinéraire dans pour `req.flash` supprimer la redirection manuelle et indiquez le `successRedirect` paramètre à `passport.authenticate`.</span><span class="sxs-lookup"><span data-stu-id="c722d-151">Update the `callback` route in `./routes/auth.js` to remove the `req.flash` and manual redirect, and provide the `successRedirect` parameter to `passport.authenticate`.</span></span> <span data-ttu-id="c722d-152">L' `callback` itinéraire doit ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c722d-152">The `callback` route should look like the following.</span></span>

```js
router.post('/callback',
  function(req, res, next) {
    passport.authenticate('azuread-openidconnect',
      {
        response: res,
        failureRedirect: '/',
        failureFlash: true,
        successRedirect: '/'
      }
    )(req,res,next);
  }
);
```

<span data-ttu-id="c722d-153">Redémarrez le serveur et suivez le processus de connexion.</span><span class="sxs-lookup"><span data-stu-id="c722d-153">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="c722d-154">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.</span><span class="sxs-lookup"><span data-stu-id="c722d-154">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

<span data-ttu-id="c722d-156">Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="c722d-156">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="c722d-157">Cliquez sur **déconnexion** pour réinitialiser la session et revenir à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="c722d-157">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Capture d’écran du menu déroulant avec le lien Déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="c722d-159">Actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="c722d-159">Refreshing tokens</span></span>

<span data-ttu-id="c722d-160">À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API.</span><span class="sxs-lookup"><span data-stu-id="c722d-160">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="c722d-161">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c722d-161">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="c722d-162">Toutefois, ce jeton est éphémère.</span><span class="sxs-lookup"><span data-stu-id="c722d-162">However, this token is short-lived.</span></span> <span data-ttu-id="c722d-163">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="c722d-163">The token expires an hour after it is issued.</span></span> <span data-ttu-id="c722d-164">C’est ici que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="c722d-164">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="c722d-165">Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans demander à l’utilisateur de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="c722d-165">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="c722d-166">Pour ce faire, créez un fichier à la racine du projet nommé `tokens.js` pour conserver les fonctions de gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="c722d-166">To manage this, create a new file in the root of the project named `tokens.js` to hold token management functions.</span></span> <span data-ttu-id="c722d-167">Ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c722d-167">Add the following code.</span></span>

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

<span data-ttu-id="c722d-168">Cette méthode vérifie d’abord si le jeton d’accès a expiré ou s’il arrive à expiration.</span><span class="sxs-lookup"><span data-stu-id="c722d-168">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="c722d-169">Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis il met à jour le cache et renvoie le nouveau jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="c722d-169">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span> <span data-ttu-id="c722d-170">Vous utiliserez cette méthode chaque fois que vous aurez besoin de récupérer le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="c722d-170">You'll use this method whenever you need to get the access token out of storage.</span></span>
