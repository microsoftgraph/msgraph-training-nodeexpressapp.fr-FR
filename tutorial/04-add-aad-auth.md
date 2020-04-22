<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ecdca-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="ecdca-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="ecdca-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ecdca-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="ecdca-103">Dans cette étape, vous allez intégrer la bibliothèque [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="ecdca-103">In this step you will integrate the [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) library into the application.</span></span>

1. <span data-ttu-id="ecdca-104">Créez un fichier nommé `.env` file à la racine de votre application et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ecdca-104">Create a new file named `.env` file in the root of your application, and add the following code.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/.env.example":::

    <span data-ttu-id="ecdca-105">Remplacez `YOUR APP ID HERE` par l’ID de l’application dans le portail d’inscription de `YOUR APP SECRET HERE` l’application et remplacez par le mot de passe que vous avez généré.</span><span class="sxs-lookup"><span data-stu-id="ecdca-105">Replace `YOUR APP ID HERE` with the application ID from the Application Registration Portal, and replace `YOUR APP SECRET HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="ecdca-106">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `.env` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application et de votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="ecdca-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="ecdca-107">Ouvrez `./app.js` et ajoutez la ligne suivante en haut du fichier pour charger le `.env` fichier.</span><span class="sxs-lookup"><span data-stu-id="ecdca-107">Open `./app.js` and add the following line to the top of the file to load the `.env` file.</span></span>

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="ecdca-108">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="ecdca-108">Implement sign-in</span></span>

1. <span data-ttu-id="ecdca-109">Localisez la `var indexRouter = require('./routes/index');` ligne `./app.js`dans.</span><span class="sxs-lookup"><span data-stu-id="ecdca-109">Locate the line `var indexRouter = require('./routes/index');` in `./app.js`.</span></span> <span data-ttu-id="ecdca-110">Insérez le code suivant **avant** cette ligne.</span><span class="sxs-lookup"><span data-stu-id="ecdca-110">Insert the following code **before** that line.</span></span>

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

    <span data-ttu-id="ecdca-111">Ce code initialise la bibliothèque [Passport. js](http://www.passportjs.org/) pour utiliser la `passport-azure-ad` Bibliothèque et la configure avec l’ID d’application et le mot de passe de l’application.</span><span class="sxs-lookup"><span data-stu-id="ecdca-111">This code initializes the [Passport.js](http://www.passportjs.org/) library to use the `passport-azure-ad` library, and configures it with the app ID and password for the app.</span></span>

1. <span data-ttu-id="ecdca-112">Localisez la `app.use('/', indexRouter);` ligne `./app.js`dans.</span><span class="sxs-lookup"><span data-stu-id="ecdca-112">Locate the line `app.use('/', indexRouter);` in `./app.js`.</span></span> <span data-ttu-id="ecdca-113">Insérez le code suivant **avant** cette ligne.</span><span class="sxs-lookup"><span data-stu-id="ecdca-113">Insert the following code **before** that line.</span></span>

    ```javascript
    // Initialize passport
    app.use(passport.initialize());
    app.use(passport.session());
    ```

1. <span data-ttu-id="ecdca-114">Créez un fichier dans le `./routes` répertoire nommé `auth.js` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ecdca-114">Create a new file in the `./routes` directory named `auth.js` and add the following code.</span></span>

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

    <span data-ttu-id="ecdca-115">Cette définition définit un routeur avec trois itinéraires `signin`: `callback`, et `signout`.</span><span class="sxs-lookup"><span data-stu-id="ecdca-115">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

    <span data-ttu-id="ecdca-116">L' `signin` itinéraire appelle la `passport.authenticate` méthode, ce qui entraîne la redirection de l’application vers la page de connexion Azure.</span><span class="sxs-lookup"><span data-stu-id="ecdca-116">The `signin` route calls the `passport.authenticate` method, causing the app to redirect to the Azure login page.</span></span>

    <span data-ttu-id="ecdca-117">L' `callback` itinéraire est l’endroit où Azure redirige une fois la connexion terminée.</span><span class="sxs-lookup"><span data-stu-id="ecdca-117">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="ecdca-118">Le code appelle de `passport.authenticate` nouveau la méthode, ce `passport-azure-ad` qui entraîne la demande d’un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="ecdca-118">The code calls the `passport.authenticate` method again, causing the `passport-azure-ad` strategy to request an access token.</span></span> <span data-ttu-id="ecdca-119">Une fois le jeton obtenu, le gestionnaire suivant est appelé, qui redirige vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire.</span><span class="sxs-lookup"><span data-stu-id="ecdca-119">Once the token is obtained, the next handler is called, which redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="ecdca-120">Nous allons l’utiliser pour vérifier que notre connexion fonctionne avant de poursuivre.</span><span class="sxs-lookup"><span data-stu-id="ecdca-120">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="ecdca-121">Avant de tester, nous devons configurer l’application Express de sorte qu’elle utilise le nouveau `./routes/auth.js`routeur à partir de.</span><span class="sxs-lookup"><span data-stu-id="ecdca-121">Before we test, we need to configure the Express app to use the new router from `./routes/auth.js`.</span></span>

    <span data-ttu-id="ecdca-122">La `signout` méthode déconnecte l’utilisateur et détruit la session.</span><span class="sxs-lookup"><span data-stu-id="ecdca-122">The `signout` method logs the user out and destroys the session.</span></span>

1. <span data-ttu-id="ecdca-123">Ouvrez `./app.js` et insérez le code suivant **avant** la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="ecdca-123">Open `./app.js` and insert the following code **before** the `var app = express();` line.</span></span>

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. <span data-ttu-id="ecdca-124">Insérez le code suivant **après** la `app.use('/', indexRouter);` ligne.</span><span class="sxs-lookup"><span data-stu-id="ecdca-124">Insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

    ```javascript
    app.use('/auth', authRouter);
    ```

<span data-ttu-id="ecdca-125">Démarrez le serveur et accédez à `https://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="ecdca-125">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="ecdca-126">Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="ecdca-126">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="ecdca-127">Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="ecdca-127">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="ecdca-128">Le navigateur vous redirige vers l’application, affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="ecdca-128">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="ecdca-129">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="ecdca-129">Get user details</span></span>

1. <span data-ttu-id="ecdca-130">Créez un fichier à la racine du projet nommé `graph.js` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ecdca-130">Create a new file in the root of the project named `graph.js` and add the following code.</span></span>

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

    <span data-ttu-id="ecdca-131">Cette méthode exporte la `getUserDetails` fonction, qui utilise le kit de développement logiciel `/me` (SDK) Microsoft Graph pour appeler le point de terminaison et renvoyer le résultat.</span><span class="sxs-lookup"><span data-stu-id="ecdca-131">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="ecdca-132">Ouvrez `/app.js` et ajoutez les instructions `require` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="ecdca-132">Open `/app.js` and add the following `require` statements to the top of the file.</span></span>

    ```javascript
    var graph = require('./graph');
    ```

1. <span data-ttu-id="ecdca-133">Remplacez la fonction `signInComplete` existante par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ecdca-133">Replace the existing `signInComplete` function with the following code.</span></span>

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

    <span data-ttu-id="ecdca-134">Le nouveau code met à jour le `profile` passeport fourni pour ajouter une `email` propriété à l’aide des données renvoyées par Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ecdca-134">The new code updates the `profile` provided by Passport to add an `email` property, using the data returned by Microsoft Graph.</span></span>

1. <span data-ttu-id="ecdca-135">Ajoutez ce qui **after** suit après `app.use(passport.session());` la ligne.</span><span class="sxs-lookup"><span data-stu-id="ecdca-135">Add the following **after** the `app.use(passport.session());` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="AddProfileSnippet":::

    <span data-ttu-id="ecdca-136">Ce code charge le profil utilisateur dans la `locals` propriété de la réponse.</span><span class="sxs-lookup"><span data-stu-id="ecdca-136">This code loads the user profile into the `locals` property of the response.</span></span> <span data-ttu-id="ecdca-137">Cela permet de le mettre à la disposition de toutes les vues dans l’application.</span><span class="sxs-lookup"><span data-stu-id="ecdca-137">This will make it available to all of the views in the app.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="ecdca-138">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="ecdca-138">Storing the tokens</span></span>

<span data-ttu-id="ecdca-139">Maintenant que vous pouvez obtenir des jetons, nous vous conseillons d’implémenter un moyen de les stocker dans l’application.</span><span class="sxs-lookup"><span data-stu-id="ecdca-139">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="ecdca-140">Actuellement, l’application stocke le jeton d’accès brut dans le stockage utilisateur en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ecdca-140">Currently, the app is storing the raw access token in the in-memory user storage.</span></span> <span data-ttu-id="ecdca-141">Étant donné qu’il s’agit d’un exemple d’application, par souci de simplicité, vous continuerez à les stocker.</span><span class="sxs-lookup"><span data-stu-id="ecdca-141">Since this is a sample app, for simplicity's sake, you'll continue to store them there.</span></span> <span data-ttu-id="ecdca-142">Une application réelle utilise une solution de stockage sécurisé plus fiable, comme une base de données.</span><span class="sxs-lookup"><span data-stu-id="ecdca-142">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="ecdca-143">Toutefois, le fait de stocker uniquement le jeton d’accès ne vous permet pas de vérifier l’expiration ou d’actualiser le jeton.</span><span class="sxs-lookup"><span data-stu-id="ecdca-143">However, storing just the access token doesn't allow you to check expiration or refresh the token.</span></span> <span data-ttu-id="ecdca-144">Pour l’activer, mettez à jour l’exemple pour encapsuler les jetons dans `AccessToken` un objet à `simple-oauth2` partir de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="ecdca-144">In order to enable that, update the sample to wrap the tokens in an `AccessToken` object from the `simple-oauth2` library.</span></span>

1. <span data-ttu-id="ecdca-145">Ouvrez `./app.js` et ajoutez le code suivant **avant** la `signInComplete` fonction.</span><span class="sxs-lookup"><span data-stu-id="ecdca-145">Open `./app.js` and add the following code **before** the `signInComplete` function.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="ConfigureOAuth2Snippet":::

1. <span data-ttu-id="ecdca-146">Remplacez la fonction `signInComplete` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ecdca-146">Replace the existing `signInComplete` function with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SignInCompleteSnippet" highlight="17-18, 21":::

1. <span data-ttu-id="ecdca-147">Remplacez l’itinéraire de `./routes/auth.js` rappel existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ecdca-147">Replace the existing callback route in  `./routes/auth.js` with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackRouteSnippet" highlight="17-18":::

1. <span data-ttu-id="ecdca-148">Redémarrez le serveur et suivez le processus de connexion.</span><span class="sxs-lookup"><span data-stu-id="ecdca-148">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="ecdca-149">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.</span><span class="sxs-lookup"><span data-stu-id="ecdca-149">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. <span data-ttu-id="ecdca-151">Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="ecdca-151">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="ecdca-152">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="ecdca-152">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="ecdca-154">Actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="ecdca-154">Refreshing tokens</span></span>

<span data-ttu-id="ecdca-155">À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API.</span><span class="sxs-lookup"><span data-stu-id="ecdca-155">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="ecdca-156">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ecdca-156">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="ecdca-157">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="ecdca-157">However, this token is short-lived.</span></span> <span data-ttu-id="ecdca-158">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="ecdca-158">The token expires an hour after it is issued.</span></span> <span data-ttu-id="ecdca-159">C’est là que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="ecdca-159">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="ecdca-160">Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="ecdca-160">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

1. <span data-ttu-id="ecdca-161">Créez un fichier à la racine du projet nommé `tokens.js` pour conserver les fonctions de gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="ecdca-161">Create a new file in the root of the project named `tokens.js` to hold token management functions.</span></span> <span data-ttu-id="ecdca-162">Ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ecdca-162">Add the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/tokens.js" id="TokensSnippet":::

<span data-ttu-id="ecdca-163">Cette méthode vérifie d’abord si le jeton d’accès a expiré ou s’il arrive à expiration.</span><span class="sxs-lookup"><span data-stu-id="ecdca-163">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="ecdca-164">Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis il met à jour le cache et renvoie le nouveau jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="ecdca-164">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span> <span data-ttu-id="ecdca-165">Vous utiliserez cette méthode chaque fois que vous aurez besoin de récupérer le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="ecdca-165">You'll use this method whenever you need to get the access token out of storage.</span></span>
