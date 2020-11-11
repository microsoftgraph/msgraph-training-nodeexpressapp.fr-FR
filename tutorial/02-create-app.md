<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application Web.

1. Ouvrez votre interface CLI, accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez la commande suivante pour créer une nouvelle application expresse qui utilise des [guidés](http://handlebarsjs.com/) comme moteur de rendu.

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    Le générateur Express crée un nouveau répertoire appelé `graph-tutorial` et génère un squelette d’application Express.

1. Accédez au `graph-tutorial` répertoire et entrez la commande suivante pour installer les dépendances.

    ```Shell
    npm install
    ```

1. Exécutez la commande suivante pour mettre à jour les packages de nœuds avec les vulnérabilités signalées.

    ```Shell
    npm audit fix
    ```

1. Exécutez la commande suivante pour mettre à jour la version d’Express et d’autres dépendances.

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. Utilisez la commande suivante pour démarrer un serveur Web local.

    ```Shell
    npm start
    ```

1. Ouvrez votre navigateur et accédez à `http://localhost:3000`. Si tout fonctionne, le message « Bienvenue dans Express » s’affiche. Si vous ne voyez pas ce message, consultez le [Guide de prise](http://expressjs.com/starter/generator.html)en main de Express.

## <a name="install-node-packages"></a>Installer des packages de nœuds

Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :

- [dotenv](https://github.com/motdotla/dotenv) pour le chargement de valeurs à partir d’un fichier. env.
- [moment](https://github.com/moment/moment/) de mise en forme des valeurs de date/heure.
- [Windows-IANA](https://github.com/rubenillodo/windows-iana) pour traduire les noms des fuseaux horaires Windows en ID de fuseau horaire IANA.
- [Connect-Flash](https://github.com/jaredhanson/connect-flash) to Flash error messages in the App.
- [Express-session](https://github.com/expressjs/session) pour stocker des valeurs dans une session côté serveur en mémoire.
- [express-promise-routeur](https://github.com/express-promise-router/express-promise-router) pour permettre aux gestionnaires de route de renvoyer une promesse.
- [Express-validation](https://github.com/express-validator/express-validator) pour l’analyse et la validation des données de formulaire.
- [MSAL-nœud](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) pour l’authentification et l’obtention de jetons d’accès.
- [UUID](https://github.com/uuidjs/uuid) utilisé par MSAL-Node pour générer des GUID.
- [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.
- [isomorphic-FETCH](https://github.com/matthew-andrews/isomorphic-fetch) pour polyfill pour le nœud FETCH for. Un Polyfill de Fetch est requis pour la `microsoft-graph-client` bibliothèque. Pour plus d’informations, consultez le site wiki de la [bibliothèque cliente JavaScript Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) .

1. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
    ```

    > [!TIP]
    > Les utilisateurs de Windows peuvent obtenir le message d’erreur suivant lors de la tentative d’installation de ces packages sur Windows.
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > Pour résoudre l’erreur, exécutez la commande suivante pour installer les outils de génération Windows à l’aide d’une fenêtre de terminal avec élévation de privilèges (administrateur) qui installe les outils de génération VS et Python.
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. Mettez à jour l’application de façon à utiliser le `connect-flash` et le `express-session` middleware. Ouvrez **./app.js** et ajoutez l' `require` instruction suivante en haut du fichier.

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. Ajoutez le code suivant immédiatement après la `var app = express();` ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez implémenter l’interface utilisateur de l’application.

1. Ouvrez **./views/Layout.HBs** et remplacez tout le contenu par le code suivant.

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples. Il définit également une disposition globale avec une barre de navigation.

1. Ouvrez **./public/StyleSheets/style.CSS** et remplacez l’intégralité de son contenu par ce qui suit.

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. Ouvrez **./views/index.HBs** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. Ouvrez **/routes/index.js** et remplacez le code existant par ce qui suit.

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. Enregistrez toutes vos modifications et redémarrez le serveur. À présent, l’application doit être très différente.

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
