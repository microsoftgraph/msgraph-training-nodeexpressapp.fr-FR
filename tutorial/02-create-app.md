<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0c9d2-101">Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application Web.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span>

1. <span data-ttu-id="0c9d2-102">Ouvrez votre interface CLI, accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez la commande suivante pour créer une nouvelle application expresse qui utilise des [guidés](http://handlebarsjs.com/) comme moteur de rendu.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-102">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    <span data-ttu-id="0c9d2-103">Le générateur Express crée un nouveau répertoire appelé `graph-tutorial` et génère un squelette d’application Express.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-103">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span>

1. <span data-ttu-id="0c9d2-104">Accédez au `graph-tutorial` répertoire et entrez la commande suivante pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-104">Navigate to the `graph-tutorial` directory and enter the following command to install dependencies.</span></span>

    ```Shell
    npm install
    ```

1. <span data-ttu-id="0c9d2-105">Exécutez la commande suivante pour mettre à jour les packages de nœuds avec les vulnérabilités signalées.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-105">Run the following command to update Node packages with reported vulnerabilities.</span></span>

    ```Shell
    npm audit fix
    ```

1. <span data-ttu-id="0c9d2-106">Exécutez la commande suivante pour mettre à jour la version d’Express et d’autres dépendances.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-106">Run the following command to update the version of Express and other dependencies.</span></span>

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. <span data-ttu-id="0c9d2-107">Utilisez la commande suivante pour démarrer un serveur Web local.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-107">Use the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="0c9d2-108">Ouvrez votre navigateur et accédez à `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-108">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="0c9d2-109">Si tout fonctionne, le message « Bienvenue dans Express » s’affiche.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-109">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="0c9d2-110">Si vous ne voyez pas ce message, consultez le [Guide de prise](http://expressjs.com/starter/generator.html)en main de Express.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-110">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

## <a name="install-node-packages"></a><span data-ttu-id="0c9d2-111">Installer des packages de nœuds</span><span class="sxs-lookup"><span data-stu-id="0c9d2-111">Install Node packages</span></span>

<span data-ttu-id="0c9d2-112">Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :</span><span class="sxs-lookup"><span data-stu-id="0c9d2-112">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="0c9d2-113">[dotenv](https://github.com/motdotla/dotenv) pour le chargement de valeurs à partir d’un fichier. env.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-113">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="0c9d2-114">[moment](https://github.com/moment/moment/) de mise en forme des valeurs de date/heure.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-114">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="0c9d2-115">[Windows-IANA](https://github.com/rubenillodo/windows-iana) pour traduire les noms des fuseaux horaires Windows en ID de fuseau horaire IANA.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-115">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zone names to IANA time zone IDs.</span></span>
- <span data-ttu-id="0c9d2-116">[Connect-Flash](https://github.com/jaredhanson/connect-flash) to Flash error messages in the App.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-116">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="0c9d2-117">[Express-session](https://github.com/expressjs/session) pour stocker des valeurs dans une session côté serveur en mémoire.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-117">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="0c9d2-118">[express-promise-routeur](https://github.com/express-promise-router/express-promise-router) pour permettre aux gestionnaires de route de renvoyer une promesse.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-118">[express-promise-router](https://github.com/express-promise-router/express-promise-router) to allow route handlers to return a Promise.</span></span>
- <span data-ttu-id="0c9d2-119">[Express-validation](https://github.com/express-validator/express-validator) pour l’analyse et la validation des données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-119">[express-validator](https://github.com/express-validator/express-validator) for parsing and validating form data.</span></span>
- <span data-ttu-id="0c9d2-120">[MSAL-nœud](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) pour l’authentification et l’obtention de jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-120">[msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="0c9d2-121">[UUID](https://github.com/uuidjs/uuid) utilisé par MSAL-Node pour générer des GUID.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-121">[uuid](https://github.com/uuidjs/uuid) used by msal-node to generate GUIDs.</span></span>
- <span data-ttu-id="0c9d2-122">[Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-122">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="0c9d2-123">[isomorphic-FETCH](https://github.com/matthew-andrews/isomorphic-fetch) pour polyfill pour le nœud FETCH for.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-123">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span></span> <span data-ttu-id="0c9d2-124">Un Polyfill de Fetch est requis pour la `microsoft-graph-client` bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-124">A fetch polyfill is required for the `microsoft-graph-client` library.</span></span> <span data-ttu-id="0c9d2-125">Pour plus d’informations, consultez le site wiki de la [bibliothèque cliente JavaScript Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) .</span><span class="sxs-lookup"><span data-stu-id="0c9d2-125">See the [Microsoft Graph JavaScript client library wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) for more information.</span></span>

1. <span data-ttu-id="0c9d2-126">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-126">Run the following command in your CLI.</span></span>

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
    ```

    > [!TIP]
    > <span data-ttu-id="0c9d2-127">Les utilisateurs de Windows peuvent obtenir le message d’erreur suivant lors de la tentative d’installation de ces packages sur Windows.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-127">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > <span data-ttu-id="0c9d2-128">Pour résoudre l’erreur, exécutez la commande suivante pour installer les outils de génération Windows à l’aide d’une fenêtre de terminal avec élévation de privilèges (administrateur) qui installe les outils de génération VS et Python.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-128">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. <span data-ttu-id="0c9d2-129">Mettez à jour l’application de façon à utiliser le `connect-flash` et le `express-session` middleware.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-129">Update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="0c9d2-130">Ouvrez **./app.js** et ajoutez l' `require` instruction suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-130">Open **./app.js** and add the following `require` statement to the top of the file.</span></span>

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. <span data-ttu-id="0c9d2-131">Ajoutez le code suivant immédiatement après la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-131">Add the following code immediately after the `var app = express();` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="0c9d2-132">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="0c9d2-132">Design the app</span></span>

<span data-ttu-id="0c9d2-133">Dans cette section, vous allez implémenter l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-133">In this section you will implement the UI for the app.</span></span>

1. <span data-ttu-id="0c9d2-134">Ouvrez **./views/Layout.HBs** et remplacez tout le contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-134">Open **./views/layout.hbs** and replace the entire contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    <span data-ttu-id="0c9d2-135">Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-135">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="0c9d2-136">Il définit également une disposition globale avec une barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-136">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="0c9d2-137">Ouvrez **./public/StyleSheets/style.CSS** et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-137">Open **./public/stylesheets/style.css** and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. <span data-ttu-id="0c9d2-138">Ouvrez **./views/index.HBs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-138">Open **./views/index.hbs** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. <span data-ttu-id="0c9d2-139">Ouvrez **/routes/index.js** et remplacez le code existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-139">Open **./routes/index.js** and replace the existing code with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. <span data-ttu-id="0c9d2-140">Enregistrez toutes vos modifications et redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-140">Save all of your changes and restart the server.</span></span> <span data-ttu-id="0c9d2-141">À présent, l’application doit être très différente.</span><span class="sxs-lookup"><span data-stu-id="0c9d2-141">Now, the app should look very different.</span></span>

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
