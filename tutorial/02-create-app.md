<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d4c53-101">Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application Web.</span><span class="sxs-lookup"><span data-stu-id="d4c53-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span>

1. <span data-ttu-id="d4c53-102">Ouvrez votre interface CLI, accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez la commande suivante pour créer une nouvelle application expresse qui utilise des [guidés](http://handlebarsjs.com/) comme moteur de rendu.</span><span class="sxs-lookup"><span data-stu-id="d4c53-102">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    <span data-ttu-id="d4c53-103">Le générateur Express crée un nouveau répertoire appelé `graph-tutorial` et génère un squelette d’application Express.</span><span class="sxs-lookup"><span data-stu-id="d4c53-103">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span>

1. <span data-ttu-id="d4c53-104">Accédez au `graph-tutorial` répertoire et entrez la commande suivante pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="d4c53-104">Navigate to the `graph-tutorial` directory and enter the following command to install dependencies.</span></span>

    ```Shell
    npm install
    ```

1. <span data-ttu-id="d4c53-105">Exécutez la commande suivante pour mettre à jour les packages de nœuds avec les vulnérabilités signalées.</span><span class="sxs-lookup"><span data-stu-id="d4c53-105">Run the following command to update Node packages with reported vulnerabilities.</span></span>

    ```Shell
    npm audit fix
    ```

1. <span data-ttu-id="d4c53-106">Utilisez la commande suivante pour démarrer un serveur Web local.</span><span class="sxs-lookup"><span data-stu-id="d4c53-106">Use the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="d4c53-107">Ouvrez votre navigateur et accédez à `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="d4c53-107">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="d4c53-108">Si tout fonctionne, le message « Bienvenue dans Express » s’affiche.</span><span class="sxs-lookup"><span data-stu-id="d4c53-108">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="d4c53-109">Si vous ne voyez pas ce message, consultez le [Guide de prise](http://expressjs.com/starter/generator.html)en main de Express.</span><span class="sxs-lookup"><span data-stu-id="d4c53-109">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

## <a name="install-node-packages"></a><span data-ttu-id="d4c53-110">Installer des packages de nœuds</span><span class="sxs-lookup"><span data-stu-id="d4c53-110">Install Node packages</span></span>

<span data-ttu-id="d4c53-111">Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :</span><span class="sxs-lookup"><span data-stu-id="d4c53-111">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="d4c53-112">[dotenv](https://github.com/motdotla/dotenv) pour le chargement de valeurs à partir d’un fichier. env.</span><span class="sxs-lookup"><span data-stu-id="d4c53-112">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="d4c53-113">[moment](https://github.com/moment/moment/) de mise en forme des valeurs de date/heure.</span><span class="sxs-lookup"><span data-stu-id="d4c53-113">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="d4c53-114">[Connect-Flash](https://github.com/jaredhanson/connect-flash) to Flash error messages in the App.</span><span class="sxs-lookup"><span data-stu-id="d4c53-114">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="d4c53-115">[Express-session](https://github.com/expressjs/session) pour stocker des valeurs dans une session côté serveur en mémoire.</span><span class="sxs-lookup"><span data-stu-id="d4c53-115">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="d4c53-116">[Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) pour l’authentification et l’obtention de jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="d4c53-116">[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="d4c53-117">[simple-oauth2](https://github.com/lelylan/simple-oauth2) pour la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="d4c53-117">[simple-oauth2](https://github.com/lelylan/simple-oauth2) for token management.</span></span>
- <span data-ttu-id="d4c53-118">[Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="d4c53-118">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="d4c53-119">[isomorphic-FETCH](https://github.com/matthew-andrews/isomorphic-fetch) pour polyfill pour le nœud FETCH for.</span><span class="sxs-lookup"><span data-stu-id="d4c53-119">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span></span> <span data-ttu-id="d4c53-120">Un Polyfill de Fetch est requis pour `microsoft-graph-client` la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="d4c53-120">A fetch polyfill is required for the `microsoft-graph-client` library.</span></span> <span data-ttu-id="d4c53-121">Pour plus d’informations, consultez le site wiki de la [bibliothèque cliente JavaScript Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) .</span><span class="sxs-lookup"><span data-stu-id="d4c53-121">See the [Microsoft Graph JavaScript client library wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) for more information.</span></span>

1. <span data-ttu-id="d4c53-122">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="d4c53-122">Run the following command in your CLI.</span></span>

    ```Shell
    npm install dotenv@8.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.17.0 isomorphic-fetch@2.2.1
    npm install passport-azure-ad@4.2.1 simple-oauth2@3.3.0 @microsoft/microsoft-graph-client@2.0.0
    ```

    > [!TIP]
    > <span data-ttu-id="d4c53-123">Les utilisateurs de Windows peuvent obtenir le message d’erreur suivant lors de la tentative d’installation de ces packages sur Windows.</span><span class="sxs-lookup"><span data-stu-id="d4c53-123">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > <span data-ttu-id="d4c53-124">Pour résoudre l’erreur, exécutez la commande suivante pour installer les outils de génération Windows à l’aide d’une fenêtre de terminal avec élévation de privilèges (administrateur) qui installe les outils de génération VS et Python.</span><span class="sxs-lookup"><span data-stu-id="d4c53-124">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. <span data-ttu-id="d4c53-125">Mettez à jour l’application de `connect-flash` façon `express-session` à utiliser le et le middleware.</span><span class="sxs-lookup"><span data-stu-id="d4c53-125">Update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="d4c53-126">Ouvrez le `./app.js` fichier et ajoutez l’instruction `require` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="d4c53-126">Open the `./app.js` file and add the following `require` statement to the top of the file.</span></span>

    ```javascript
    var session = require('express-session');
    var flash = require('connect-flash');
    ```

1. <span data-ttu-id="d4c53-127">Ajoutez le code suivant immédiatement après la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="d4c53-127">Add the following code immediately after the `var app = express();` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="d4c53-128">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="d4c53-128">Design the app</span></span>

<span data-ttu-id="d4c53-129">Dans cette section, vous allez implémenter l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="d4c53-129">In this section you will implement the UI for the app.</span></span>

1. <span data-ttu-id="d4c53-130">Ouvrez le `./views/layout.hbs` fichier et remplacez l’intégralité du contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="d4c53-130">Open the `./views/layout.hbs` file and replace the entire contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    <span data-ttu-id="d4c53-131">Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples.</span><span class="sxs-lookup"><span data-stu-id="d4c53-131">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="d4c53-132">Il définit également une disposition globale avec une barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="d4c53-132">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="d4c53-133">Ouvrez `./public/stylesheets/style.css` et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d4c53-133">Open `./public/stylesheets/style.css` and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. <span data-ttu-id="d4c53-134">Ouvrez le `./views/index.hbs` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d4c53-134">Open the `./views/index.hbs` file and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. <span data-ttu-id="d4c53-135">Ouvrez le `./routes/index.js` fichier et remplacez le code existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d4c53-135">Open the `./routes/index.js` file and replace the existing code with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. <span data-ttu-id="d4c53-136">Enregistrez toutes vos modifications et redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="d4c53-136">Save all of your changes and restart the server.</span></span> <span data-ttu-id="d4c53-137">À présent, l’application doit être très différente.</span><span class="sxs-lookup"><span data-stu-id="d4c53-137">Now, the app should look very different.</span></span>

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
