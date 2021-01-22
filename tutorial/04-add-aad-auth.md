<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la bibliothèque [msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) dans l’application.

1. Créez un fichier nommé **.env** à la racine de votre application et ajoutez le code suivant.

    :::code language="ini" source="../demo/graph-tutorial/example.env":::

    Remplacez-le par l’ID de l’application à partir du portail d’inscription des applications et par la `YOUR_CLIENT_SECRET_HERE` secret client que vous avez `YOUR_APP_SECRET_HERE` générée.

    > [!IMPORTANT]
    > Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier **.env** du contrôle source afin d’éviter toute fuite accidentelle de votre ID d’application et mot de passe.

1. Ouvrez **./app.js** et ajoutez la ligne suivante en haut du fichier pour charger **le fichier .env.**

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a>Implémentation de la connexion

1. Recherchez la `var app = express();` ligne **dans ./app.js**. Insérez le code **suivant après** cette ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="MsalInitSnippet":::

    Ce code initialise la bibliothèque msal-node avec l’ID d’application et le mot de passe de l’application.

1. Créez un fichier dans le répertoire **./routes** nommé **auth.js** et ajoutez le code suivant.

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

    Cela définit un routeur avec trois itinéraires : `signin` `callback` , et `signout` .

    `signin`L’itinéraire appelle la fonction pour générer l’URL de `getAuthCodeUrl` connexion, puis redirige le navigateur vers cette URL.

    `callback`L’itinéraire est l’endroit où Azure redirige une fois la signature terminée. Le code appelle la `acquireTokenByCode` fonction pour échanger le code d’autorisation contre un jeton d’accès. Une fois le jeton obtenu, il est redirigé vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire. Nous allons l’utiliser pour vérifier que notre connectez-vous fonctionne avant de passer à autre chose. Avant de tester, nous devons configurer l’application Express pour utiliser le nouveau routeur à partir **de ./routes/auth.js**.

    La `signout` méthode déconnecte l’utilisateur et détruit la session.

1. Ouvrez **./app.js** et insérez le code suivant **avant** la `var app = express();` ligne.

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. Insérez le code **suivant après** la `app.use('/', indexRouter);` ligne.

    ```javascript
    app.use('/auth', authRouter);
    ```

Démarrez le serveur et accédez à `https://localhost:3000` . Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`. Connectez-vous avec votre compte Microsoft et consentez aux autorisations demandées. Le navigateur vous redirige vers l’application, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

1. Créez un fichier à la racine du projet nommé **graph.js** et ajoutez le code suivant.

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

    Cela exporte la fonction, qui utilise le SDK Microsoft Graph pour appeler le point de terminaison `getUserDetails` `/me` et renvoyer le résultat.

1. Ouvrez **./routes/auth.js** et ajoutez les `require` instructions suivantes en haut du fichier.

    ```javascript
    var graph = require('../graph');
    ```

1. Remplacez l’itinéraire de rappel existant par le code suivant.

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackSnippet" highlight="13-23":::

    Le nouveau code enregistre l’ID de compte de l’utilisateur dans la session, obtient les détails de l’utilisateur à partir de Microsoft Graph et l’enregistre dans le stockage utilisateur de l’application.

1. Redémarrez le serveur et traversez le processus de sign-in. Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes en cours de signature.

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. Cliquez sur l’avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion. Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Stockage et actualisation des jetons

À ce stade, votre application dispose d’un jeton d’accès, qui est envoyé dans l’en-tête des `Authorization` appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph au nom de l’utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. C’est là que le jeton d’actualisation devient utile. La spécification OAuth introduit un jeton d’actualisation, qui permet à l’application de demander un nouveau jeton d’accès sans que l’utilisateur ne se connecte à nouveau.

Étant donné que l’application utilise le package msal-node, vous n’avez pas besoin d’implémenter de logique de stockage ou d’actualisation de jeton. L’application utilise le cache de jetons en mémoire msal-node par défaut, ce qui est suffisant pour un exemple d’application. Les applications de production doivent fournir leur propre [plug-in](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) de mise en cache pour sérialiser le cache de jetons dans un moyen de stockage sécurisé et fiable.
