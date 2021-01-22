<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c7685-101">Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application web.</span><span class="sxs-lookup"><span data-stu-id="c7685-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span>

1. <span data-ttu-id="c7685-102">Ouvrez votre CLI, accédez à un répertoire dans lequel vous avez le droit de créer des fichiers et exécutez la commande suivante pour créer une application Express qui utilise [handlebars](http://handlebarsjs.com/) comme moteur de rendu.</span><span class="sxs-lookup"><span data-stu-id="c7685-102">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    <span data-ttu-id="c7685-103">Le générateur Express crée un répertoire appelé et génère la création de `graph-tutorial` modèles pour une application Express.</span><span class="sxs-lookup"><span data-stu-id="c7685-103">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span>

1. <span data-ttu-id="c7685-104">Accédez au `graph-tutorial` répertoire et entrez la commande suivante pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="c7685-104">Navigate to the `graph-tutorial` directory and enter the following command to install dependencies.</span></span>

    ```Shell
    npm install
    ```

1. <span data-ttu-id="c7685-105">Exécutez la commande suivante pour mettre à jour les packages node avec les vulnérabilités signalées.</span><span class="sxs-lookup"><span data-stu-id="c7685-105">Run the following command to update Node packages with reported vulnerabilities.</span></span>

    ```Shell
    npm audit fix
    ```

1. <span data-ttu-id="c7685-106">Exécutez la commande suivante pour mettre à jour la version d’Express et d’autres dépendances.</span><span class="sxs-lookup"><span data-stu-id="c7685-106">Run the following command to update the version of Express and other dependencies.</span></span>

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. <span data-ttu-id="c7685-107">Utilisez la commande suivante pour démarrer un serveur web local.</span><span class="sxs-lookup"><span data-stu-id="c7685-107">Use the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="c7685-108">Ouvrez votre navigateur et accédez à `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="c7685-108">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="c7685-109">Si tout fonctionne, vous verrez un message « Bienvenue dans Express ».</span><span class="sxs-lookup"><span data-stu-id="c7685-109">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="c7685-110">Si vous ne voyez pas ce message, consultez le guide de mise en [ligne Express.](http://expressjs.com/starter/generator.html)</span><span class="sxs-lookup"><span data-stu-id="c7685-110">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

## <a name="install-node-packages"></a><span data-ttu-id="c7685-111">Installer des packages node</span><span class="sxs-lookup"><span data-stu-id="c7685-111">Install Node packages</span></span>

<span data-ttu-id="c7685-112">Avant de passer à la suite, installez des packages supplémentaires que vous utiliserez ultérieurement :</span><span class="sxs-lookup"><span data-stu-id="c7685-112">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="c7685-113">[pour charger des](https://github.com/motdotla/dotenv) valeurs à partir d’un fichier .env.</span><span class="sxs-lookup"><span data-stu-id="c7685-113">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="c7685-114">[moment de](https://github.com/moment/moment/) mise en forme des valeurs de date/heure.</span><span class="sxs-lookup"><span data-stu-id="c7685-114">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="c7685-115">[windows-iana pour](https://github.com/rubenillodo/windows-iana) la traduction des noms de fuseau horaire Windows en ID de fuseau horaire IANA.</span><span class="sxs-lookup"><span data-stu-id="c7685-115">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zone names to IANA time zone IDs.</span></span>
- <span data-ttu-id="c7685-116">[connect-flash](https://github.com/jaredhanson/connect-flash) pour flasher les messages d’erreur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c7685-116">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="c7685-117">[pour stocker des](https://github.com/expressjs/session) valeurs dans une session côté serveur en mémoire.</span><span class="sxs-lookup"><span data-stu-id="c7685-117">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="c7685-118">[express-promise-routeur](https://github.com/express-promise-router/express-promise-router) pour permettre aux routeurs de renvoyer une promesse.</span><span class="sxs-lookup"><span data-stu-id="c7685-118">[express-promise-router](https://github.com/express-promise-router/express-promise-router) to allow route handlers to return a Promise.</span></span>
- <span data-ttu-id="c7685-119">[validateur express pour](https://github.com/express-validator/express-validator) l’analyse et la validation des données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="c7685-119">[express-validator](https://github.com/express-validator/express-validator) for parsing and validating form data.</span></span>
- <span data-ttu-id="c7685-120">[msal-node pour l’authentification](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) et l’obtention de jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="c7685-120">[msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="c7685-121">[uuid utilisé](https://github.com/uuidjs/uuid) par msal-node pour générer des GUID.</span><span class="sxs-lookup"><span data-stu-id="c7685-121">[uuid](https://github.com/uuidjs/uuid) used by msal-node to generate GUIDs.</span></span>
- <span data-ttu-id="c7685-122">[microsoft-graph-client pour](https://github.com/microsoftgraph/msgraph-sdk-javascript) effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c7685-122">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="c7685-123">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span><span class="sxs-lookup"><span data-stu-id="c7685-123">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span></span> <span data-ttu-id="c7685-124">Un polyfill de récupération est requis pour la `microsoft-graph-client` bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="c7685-124">A fetch polyfill is required for the `microsoft-graph-client` library.</span></span> <span data-ttu-id="c7685-125">Pour plus [d’informations,](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) voir le Wiki de la bibliothèque cliente JavaScript Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c7685-125">See the [Microsoft Graph JavaScript client library wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) for more information.</span></span>

1. <span data-ttu-id="c7685-126">Exécutez la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="c7685-126">Run the following command in your CLI.</span></span>

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 express-promise-router@4.0.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
    ```

    > [!TIP]
    > <span data-ttu-id="c7685-127">Les utilisateurs Windows peuvent obtenir le message d’erreur suivant lors de la tentative d’installation de ces packages sur Windows.</span><span class="sxs-lookup"><span data-stu-id="c7685-127">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > <span data-ttu-id="c7685-128">Pour résoudre l’erreur, exécutez la commande suivante pour installer les outils de build Windows à l’aide d’une fenêtre terminal avec élévation de niveaux (administrateur) qui installe les outils de build VS et Python.</span><span class="sxs-lookup"><span data-stu-id="c7685-128">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. <span data-ttu-id="c7685-129">Mettez à jour l’application pour utiliser `connect-flash` `express-session` l’intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="c7685-129">Update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="c7685-130">Ouvrez **./app.js** et ajoutez l’instruction `require` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="c7685-130">Open **./app.js** and add the following `require` statement to the top of the file.</span></span>

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. <span data-ttu-id="c7685-131">Ajoutez le code suivant immédiatement après la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="c7685-131">Add the following code immediately after the `var app = express();` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="c7685-132">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="c7685-132">Design the app</span></span>

<span data-ttu-id="c7685-133">Dans cette section, vous allez implémenter l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="c7685-133">In this section you will implement the UI for the app.</span></span>

1. <span data-ttu-id="c7685-134">Ouvrez **./views/layout.hbs et** remplacez tout le contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c7685-134">Open **./views/layout.hbs** and replace the entire contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    <span data-ttu-id="c7685-135">Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples.</span><span class="sxs-lookup"><span data-stu-id="c7685-135">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="c7685-136">Il définit également une disposition globale avec une barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="c7685-136">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="c7685-137">Ouvrez **./public/stylesheets/style.css** et remplacez tout son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c7685-137">Open **./public/stylesheets/style.css** and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. <span data-ttu-id="c7685-138">Ouvrez **./views/index.hbs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c7685-138">Open **./views/index.hbs** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. <span data-ttu-id="c7685-139">Ouvrez **./routes/index.js** et remplacez le code existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c7685-139">Open **./routes/index.js** and replace the existing code with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. <span data-ttu-id="c7685-140">Enregistrez toutes vos modifications et redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="c7685-140">Save all of your changes and restart the server.</span></span> <span data-ttu-id="c7685-141">L’application doit maintenant avoir une apparence très différente.</span><span class="sxs-lookup"><span data-stu-id="c7685-141">Now, the app should look very different.</span></span>

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
