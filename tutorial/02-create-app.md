<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application web.

1. Ouvrez votre CLI, accédez à un répertoire dans lequel vous avez le droit de créer des fichiers et exécutez la commande suivante pour créer une application Express qui utilise [handlebars](http://handlebarsjs.com/) comme moteur de rendu.

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    Le générateur Express crée un répertoire appelé et génère la création de `graph-tutorial` modèles pour une application Express.

1. Accédez au `graph-tutorial` répertoire et entrez la commande suivante pour installer les dépendances.

    ```Shell
    npm install
    ```

1. Exécutez la commande suivante pour mettre à jour les packages node avec les vulnérabilités signalées.

    ```Shell
    npm audit fix
    ```

1. Exécutez la commande suivante pour mettre à jour la version d’Express et d’autres dépendances.

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. Utilisez la commande suivante pour démarrer un serveur web local.

    ```Shell
    npm start
    ```

1. Ouvrez votre navigateur et accédez à `http://localhost:3000`. Si tout fonctionne, vous verrez un message « Bienvenue dans Express ». Si vous ne voyez pas ce message, consultez le guide de mise en [ligne Express.](http://expressjs.com/starter/generator.html)

## <a name="install-node-packages"></a>Installer des packages node

Avant de passer à la suite, installez des packages supplémentaires que vous utiliserez ultérieurement :

- [pour charger des](https://github.com/motdotla/dotenv) valeurs à partir d’un fichier .env.
- [moment de](https://github.com/moment/moment/) mise en forme des valeurs de date/heure.
- [windows-iana pour](https://github.com/rubenillodo/windows-iana) la traduction des noms de fuseau horaire Windows en ID de fuseau horaire IANA.
- [connect-flash](https://github.com/jaredhanson/connect-flash) pour flasher les messages d’erreur dans l’application.
- [pour stocker des](https://github.com/expressjs/session) valeurs dans une session côté serveur en mémoire.
- [express-promise-routeur](https://github.com/express-promise-router/express-promise-router) pour permettre aux routeurs de renvoyer une promesse.
- [validateur express pour](https://github.com/express-validator/express-validator) l’analyse et la validation des données de formulaire.
- [msal-node pour l’authentification](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) et l’obtention de jetons d’accès.
- [uuid utilisé](https://github.com/uuidjs/uuid) par msal-node pour générer des GUID.
- [microsoft-graph-client pour](https://github.com/microsoftgraph/msgraph-sdk-javascript) effectuer des appels à Microsoft Graph.
- [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node. Un polyfill de récupération est requis pour la `microsoft-graph-client` bibliothèque. Pour plus [d’informations,](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) voir le Wiki de la bibliothèque cliente JavaScript Microsoft Graph.

1. Exécutez la commande suivante dans votre CLI.

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 express-promise-router@4.0.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
    ```

    > [!TIP]
    > Les utilisateurs Windows peuvent obtenir le message d’erreur suivant lors de la tentative d’installation de ces packages sur Windows.
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > Pour résoudre l’erreur, exécutez la commande suivante pour installer les outils de build Windows à l’aide d’une fenêtre terminal avec élévation de niveaux (administrateur) qui installe les outils de build VS et Python.
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. Mettez à jour l’application pour utiliser `connect-flash` `express-session` l’intermédiaire. Ouvrez **./app.js** et ajoutez l’instruction `require` suivante en haut du fichier.

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. Ajoutez le code suivant immédiatement après la `var app = express();` ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez implémenter l’interface utilisateur de l’application.

1. Ouvrez **./views/layout.hbs et** remplacez tout le contenu par le code suivant.

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples. Il définit également une disposition globale avec une barre de navigation.

1. Ouvrez **./public/stylesheets/style.css** et remplacez tout son contenu par ce qui suit.

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. Ouvrez **./views/index.hbs** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. Ouvrez **./routes/index.js** et remplacez le code existant par ce qui suit.

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. Enregistrez toutes vos modifications et redémarrez le serveur. L’application doit maintenant avoir une apparence très différente.

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
