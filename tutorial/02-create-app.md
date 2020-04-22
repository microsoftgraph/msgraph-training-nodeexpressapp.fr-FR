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

1. Utilisez la commande suivante pour démarrer un serveur Web local.

    ```Shell
    npm start
    ```

1. Ouvrez votre navigateur et accédez à `http://localhost:3000`. Si tout fonctionne, le message « Bienvenue dans Express » s’affiche. Si vous ne voyez pas ce message, consultez le [Guide de prise](http://expressjs.com/starter/generator.html)en main de Express.

## <a name="install-node-packages"></a>Installer des packages de nœuds

Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :

- [dotenv](https://github.com/motdotla/dotenv) pour le chargement de valeurs à partir d’un fichier. env.
- [moment](https://github.com/moment/moment/) de mise en forme des valeurs de date/heure.
- [Connect-Flash](https://github.com/jaredhanson/connect-flash) to Flash error messages in the App.
- [Express-session](https://github.com/expressjs/session) pour stocker des valeurs dans une session côté serveur en mémoire.
- [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) pour l’authentification et l’obtention de jetons d’accès.
- [simple-oauth2](https://github.com/lelylan/simple-oauth2) pour la gestion des jetons.
- [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.
- [isomorphic-FETCH](https://github.com/matthew-andrews/isomorphic-fetch) pour polyfill pour le nœud FETCH for. Un Polyfill de Fetch est requis pour `microsoft-graph-client` la bibliothèque. Pour plus d’informations, consultez le site wiki de la [bibliothèque cliente JavaScript Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) .

1. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    npm install dotenv@8.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.17.0 isomorphic-fetch@2.2.1
    npm install passport-azure-ad@4.2.1 simple-oauth2@3.3.0 @microsoft/microsoft-graph-client@2.0.0
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

1. Mettez à jour l’application de `connect-flash` façon `express-session` à utiliser le et le middleware. Ouvrez le `./app.js` fichier et ajoutez l’instruction `require` suivante en haut du fichier.

    ```javascript
    var session = require('express-session');
    var flash = require('connect-flash');
    ```

1. Ajoutez le code suivant immédiatement après la `var app = express();` ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez implémenter l’interface utilisateur de l’application.

1. Ouvrez le `./views/layout.hbs` fichier et remplacez l’intégralité du contenu par le code suivant.

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples. Il définit également une disposition globale avec une barre de navigation.

1. Ouvrez `./public/stylesheets/style.css` et remplacez l’intégralité de son contenu par ce qui suit.

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. Ouvrez le `./views/index.hbs` fichier et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. Ouvrez le `./routes/index.js` fichier et remplacez le code existant par ce qui suit.

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. Enregistrez toutes vos modifications et redémarrez le serveur. À présent, l’application doit être très différente.

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
