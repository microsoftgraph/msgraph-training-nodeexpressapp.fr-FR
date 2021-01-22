<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="256e7-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="256e7-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="256e7-102">Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="256e7-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="256e7-103">Dans cette étape, vous allez intégrer la bibliothèque [msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="256e7-103">In this step you will integrate the [msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) library into the application.</span></span>

1. <span data-ttu-id="256e7-104">Créez un fichier nommé **.env** à la racine de votre application et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="256e7-104">Create a new file named **.env** in the root of your application, and add the following code.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/example.env":::

    <span data-ttu-id="256e7-105">Remplacez-le par l’ID de l’application à partir du portail d’inscription des applications et par la `YOUR_CLIENT_SECRET_HERE` secret client que vous avez `YOUR_APP_SECRET_HERE` générée.</span><span class="sxs-lookup"><span data-stu-id="256e7-105">Replace `YOUR_CLIENT_SECRET_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the client secret you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="256e7-106">Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier **.env** du contrôle source afin d’éviter toute fuite accidentelle de votre ID d’application et mot de passe.</span><span class="sxs-lookup"><span data-stu-id="256e7-106">If you're using source control such as git, now would be a good time to exclude the **.env** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="256e7-107">Ouvrez **./app.js** et ajoutez la ligne suivante en haut du fichier pour charger **le fichier .env.**</span><span class="sxs-lookup"><span data-stu-id="256e7-107">Open **./app.js** and add the following line to the top of the file to load the **.env** file.</span></span>

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="256e7-108">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="256e7-108">Implement sign-in</span></span>

1. <span data-ttu-id="256e7-109">Recherchez la `var app = express();` ligne **dans ./app.js**.</span><span class="sxs-lookup"><span data-stu-id="256e7-109">Locate the line `var app = express();` in **./app.js**.</span></span> <span data-ttu-id="256e7-110">Insérez le code **suivant après** cette ligne.</span><span class="sxs-lookup"><span data-stu-id="256e7-110">Insert the following code **after** that line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="MsalInitSnippet":::

    <span data-ttu-id="256e7-111">Ce code initialise la bibliothèque msal-node avec l’ID d’application et le mot de passe de l’application.</span><span class="sxs-lookup"><span data-stu-id="256e7-111">This code initializes the msal-node library with the app ID and password for the app.</span></span>

1. <span data-ttu-id="256e7-112">Créez un fichier dans le répertoire **./routes** nommé **auth.js** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="256e7-112">Create a new file in the **./routes** directory named **auth.js** and add the following code.</span></span>

    ```javascript
    var router = require('express-promise-router')();

    /* GET auth callback. */
    router.get('/signin',
      async function (req, res) {
        const urlParameters = {
          scopes: process.env.OAUTH_SCOPES.split(','),
          redirectUri: process.env.OAUTH_REDIRECT_URI
        };

        try {
          const authUrl = await req.app.locals
            .msalClient.getAuthCodeUrl(urlParameters);
          res.redirect(authUrl);
        }
        catch (error) {
          console.log(`Error: ${error}`);
          req.flash('error_msg', {
            message: 'Error getting auth URL',
            debug: JSON.stringify(error, Object.getOwnPropertyNames(error))
          });
          res.redirect('/');
        }
      }
    );

    router.get('/callback',
      async function(req, res) {
        const tokenRequest = {
          code: req.query.code,
          scopes: process.env.OAUTH_SCOPES.split(','),
          redirectUri: process.env.OAUTH_REDIRECT_URI
        };

        try {
          const response = await req.app.locals
            .msalClient.acquireTokenByCode(tokenRequest);

          // TEMPORARY!
          // Flash the access token for testing purposes
          req.flash('error_msg', {
            message: 'Access token',
            debug: response.accessToken
          });
        } catch (error) {
          req.flash('error_msg', {
            message: 'Error completing authentication',
            debug: JSON.stringify(error, Object.getOwnPropertyNames(error))
          });
        }

        res.redirect('/');
      }
    );

    router.get('/signout',
      async function(req, res) {
        // Sign out
        if (req.session.userId) {
          // Look up the user's account in the cache
          const accounts = await req.app.locals.msalClient
            .getTokenCache()
            .getAllAccounts();

          const userAccount = accounts.find(a => a.homeAccountId === req.session.userId);

          // Remove the account
          if (userAccount) {
            req.app.locals.msalClient
              .getTokenCache()
              .removeAccount(userAccount);
          }
        }

        // Destroy the user's session
        req.session.destroy(function (err) {
          res.redirect('/');
        });
      }
    );

    module.exports = router;
    ```

    <span data-ttu-id="256e7-113">Cela définit un routeur avec trois itinéraires : `signin` `callback` , et `signout` .</span><span class="sxs-lookup"><span data-stu-id="256e7-113">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

    <span data-ttu-id="256e7-114">`signin`L’itinéraire appelle la fonction pour générer l’URL de `getAuthCodeUrl` connexion, puis redirige le navigateur vers cette URL.</span><span class="sxs-lookup"><span data-stu-id="256e7-114">The `signin` route calls the `getAuthCodeUrl` function to generate the login URL, then redirects the browser to that URL.</span></span>

    <span data-ttu-id="256e7-115">`callback`L’itinéraire est l’endroit où Azure redirige une fois la signature terminée.</span><span class="sxs-lookup"><span data-stu-id="256e7-115">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="256e7-116">Le code appelle la `acquireTokenByCode` fonction pour échanger le code d’autorisation contre un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="256e7-116">The code calls the `acquireTokenByCode` function to exchange the authorization code for an access token.</span></span> <span data-ttu-id="256e7-117">Une fois le jeton obtenu, il est redirigé vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire.</span><span class="sxs-lookup"><span data-stu-id="256e7-117">Once the token is obtained, it redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="256e7-118">Nous allons l’utiliser pour vérifier que notre connectez-vous fonctionne avant de passer à autre chose.</span><span class="sxs-lookup"><span data-stu-id="256e7-118">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="256e7-119">Avant de tester, nous devons configurer l’application Express pour utiliser le nouveau routeur à partir **de ./routes/auth.js**.</span><span class="sxs-lookup"><span data-stu-id="256e7-119">Before we test, we need to configure the Express app to use the new router from **./routes/auth.js**.</span></span>

    <span data-ttu-id="256e7-120">La `signout` méthode déconnecte l’utilisateur et détruit la session.</span><span class="sxs-lookup"><span data-stu-id="256e7-120">The `signout` method logs the user out and destroys the session.</span></span>

1. <span data-ttu-id="256e7-121">Ouvrez **./app.js** et insérez le code suivant **avant** la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="256e7-121">Open **./app.js** and insert the following code **before** the `var app = express();` line.</span></span>

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. <span data-ttu-id="256e7-122">Insérez le code **suivant après** la `app.use('/', indexRouter);` ligne.</span><span class="sxs-lookup"><span data-stu-id="256e7-122">Insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

    ```javascript
    app.use('/auth', authRouter);
    ```

<span data-ttu-id="256e7-123">Démarrez le serveur et accédez à `https://localhost:3000` .</span><span class="sxs-lookup"><span data-stu-id="256e7-123">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="256e7-124">Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="256e7-124">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="256e7-125">Connectez-vous avec votre compte Microsoft et consentez aux autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="256e7-125">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="256e7-126">Le navigateur vous redirige vers l’application, affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="256e7-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="256e7-127">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="256e7-127">Get user details</span></span>

1. <span data-ttu-id="256e7-128">Créez un fichier à la racine du projet nommé **graph.js** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="256e7-128">Create a new file in the root of the project named **graph.js** and add the following code.</span></span>

    ```javascript
    var graph = require('@microsoft/microsoft-graph-client');
    require('isomorphic-fetch');

    module.exports = {
      getUserDetails: async function(accessToken) {
        const client = getAuthenticatedClient(accessToken);

        const user = await client
          .api('/me')
          .select('displayName,mail,mailboxSettings,userPrincipalName')
          .get();
        return user;
      },
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

    <span data-ttu-id="256e7-129">Cela exporte la fonction, qui utilise le SDK Microsoft Graph pour appeler le point de terminaison `getUserDetails` `/me` et renvoyer le résultat.</span><span class="sxs-lookup"><span data-stu-id="256e7-129">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="256e7-130">Ouvrez **./routes/auth.js** et ajoutez les `require` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="256e7-130">Open **./routes/auth.js** and add the following `require` statements to the top of the file.</span></span>

    ```javascript
    var graph = require('../graph');
    ```

1. <span data-ttu-id="256e7-131">Remplacez l’itinéraire de rappel existant par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="256e7-131">Replace the existing callback route with the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackSnippet" highlight="13-23":::

    <span data-ttu-id="256e7-132">Le nouveau code enregistre l’ID de compte de l’utilisateur dans la session, obtient les détails de l’utilisateur à partir de Microsoft Graph et l’enregistre dans le stockage utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="256e7-132">The new code saves the user's account ID in the session, gets the user's details from Microsoft Graph, and saves it in the app's user storage.</span></span>

1. <span data-ttu-id="256e7-133">Redémarrez le serveur et traversez le processus de sign-in.</span><span class="sxs-lookup"><span data-stu-id="256e7-133">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="256e7-134">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes en cours de signature.</span><span class="sxs-lookup"><span data-stu-id="256e7-134">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. <span data-ttu-id="256e7-136">Cliquez sur l’avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion.</span><span class="sxs-lookup"><span data-stu-id="256e7-136">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="256e7-137">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="256e7-137">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="256e7-139">Stockage et actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="256e7-139">Storing and refreshing tokens</span></span>

<span data-ttu-id="256e7-140">À ce stade, votre application dispose d’un jeton d’accès, qui est envoyé dans l’en-tête des `Authorization` appels d’API.</span><span class="sxs-lookup"><span data-stu-id="256e7-140">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="256e7-141">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph au nom de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="256e7-141">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="256e7-142">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="256e7-142">However, this token is short-lived.</span></span> <span data-ttu-id="256e7-143">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="256e7-143">The token expires an hour after it is issued.</span></span> <span data-ttu-id="256e7-144">C’est là que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="256e7-144">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="256e7-145">La spécification OAuth introduit un jeton d’actualisation, qui permet à l’application de demander un nouveau jeton d’accès sans que l’utilisateur ne se connecte à nouveau.</span><span class="sxs-lookup"><span data-stu-id="256e7-145">The OAuth specification introduces a refresh token, which allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="256e7-146">Étant donné que l’application utilise le package msal-node, vous n’avez pas besoin d’implémenter de logique de stockage ou d’actualisation de jeton.</span><span class="sxs-lookup"><span data-stu-id="256e7-146">Because the app is using the msal-node package, you do not need to implement any token storage or refresh logic.</span></span> <span data-ttu-id="256e7-147">L’application utilise le cache de jetons en mémoire msal-node par défaut, ce qui est suffisant pour un exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="256e7-147">The app uses the default msal-node in-memory token cache, which is sufficient for a sample application.</span></span> <span data-ttu-id="256e7-148">Les applications de production doivent fournir leur propre [plug-in](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) de mise en cache pour sérialiser le cache de jetons dans un moyen de stockage sécurisé et fiable.</span><span class="sxs-lookup"><span data-stu-id="256e7-148">Production applications should provide their own [caching plugin](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) to serialize the token cache in a secure, reliable storage medium.</span></span>
