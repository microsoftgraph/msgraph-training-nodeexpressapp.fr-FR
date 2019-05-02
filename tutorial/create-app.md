<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="22f31-101">Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application Web.</span><span class="sxs-lookup"><span data-stu-id="22f31-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span> <span data-ttu-id="22f31-102">Si le générateur Express n’est pas déjà installé, vous pouvez l’installer à partir de votre interface de ligne de commande (CLI) à l’aide de la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="22f31-102">If you don't already have the Express generator installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

```Shell
npm install express-generator -g
```

<span data-ttu-id="22f31-103">Ouvrez votre interface CLI, accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez la commande suivante pour créer une nouvelle application [](http://handlebarsjs.com/) expresse qui utilise des guidés comme moteur de rendu.</span><span class="sxs-lookup"><span data-stu-id="22f31-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

```Shell
express --hbs graph-tutorial
```

<span data-ttu-id="22f31-104">Le générateur Express crée un nouveau répertoire appelé `graph-tutorial` et génère un squelette d’application Express.</span><span class="sxs-lookup"><span data-stu-id="22f31-104">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span> <span data-ttu-id="22f31-105">Accédez à ce nouveau répertoire et entrez la commande suivante pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="22f31-105">Navigate to this new directory and enter the following command to install dependencies.</span></span>

```Shell
npm install
```

<span data-ttu-id="22f31-106">Une fois que cette commande est terminée, utilisez la commande suivante pour démarrer un serveur Web local.</span><span class="sxs-lookup"><span data-stu-id="22f31-106">Once that command completes, use the following command to start a local web server.</span></span>

```Shell
npm start
```

<span data-ttu-id="22f31-107">Ouvrez votre navigateur et accédez à `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="22f31-107">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="22f31-108">Si tout fonctionne, le message «Bienvenue dans Express» s’affiche.</span><span class="sxs-lookup"><span data-stu-id="22f31-108">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="22f31-109">Si vous ne voyez pas ce message, consultez le [Guide de prise](http://expressjs.com/starter/generator.html)en main de Express.</span><span class="sxs-lookup"><span data-stu-id="22f31-109">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

<span data-ttu-id="22f31-110">Avant de poursuivre, installez des gemmes supplémentaires que vous utiliserez plus tard:</span><span class="sxs-lookup"><span data-stu-id="22f31-110">Before moving on, install some additional gems that you will use later:</span></span>

- <span data-ttu-id="22f31-111">[dotenv](https://github.com/motdotla/dotenv) pour le chargement de valeurs à partir d’un fichier. env.</span><span class="sxs-lookup"><span data-stu-id="22f31-111">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="22f31-112">[moment](https://github.com/moment/moment/) de mise en forme des valeurs de date/heure.</span><span class="sxs-lookup"><span data-stu-id="22f31-112">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="22f31-113">[Connect-Flash](https://github.com/jaredhanson/connect-flash) to Flash error messages in the App.</span><span class="sxs-lookup"><span data-stu-id="22f31-113">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="22f31-114">[Express-session](https://github.com/expressjs/session) pour stocker des valeurs dans une session côté serveur en mémoire.</span><span class="sxs-lookup"><span data-stu-id="22f31-114">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="22f31-115">[Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) pour l’authentification et l’obtention de jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="22f31-115">[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="22f31-116">[simple-oauth2](https://github.com/lelylan/simple-oauth2) pour la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="22f31-116">[simple-oauth2](https://github.com/lelylan/simple-oauth2) for token management.</span></span>
- <span data-ttu-id="22f31-117">[Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="22f31-117">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="22f31-118">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="22f31-118">Run the following command in your CLI.</span></span>

```Shell
npm install dotenv@6.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.15.6
npm install passport-azure-ad@4.0.0 simple-oauth2@2.2.1 @microsoft/microsoft-graph-client@1.5.2
```

><span data-ttu-id="22f31-119">__UTILISATEURS WINDOWS__</span><span class="sxs-lookup"><span data-stu-id="22f31-119">__WINDOWS USERS__</span></span>
>
><span data-ttu-id="22f31-120">Si vous obtenez le message d’erreur suivant lorsque vous essayez d’installer ces packages:</span><span class="sxs-lookup"><span data-stu-id="22f31-120">If you get the following error message when trying to install these packages:</span></span>
>
> `gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.`
>
><span data-ttu-id="22f31-121">Exécutez la commande suivante pour installer les outils de génération Windows à l’aide d’une fenêtre de terminal avec élévation de privilèges (administrateur) qui installe les outils de génération VS, ainsi que Python</span><span class="sxs-lookup"><span data-stu-id="22f31-121">Run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and also Python</span></span>
>
> `npm install --global --production windows-build-tools`

<span data-ttu-id="22f31-122">Maintenant, mettez à jour l’application `connect-flash` pour `express-session` utiliser le et le middleware.</span><span class="sxs-lookup"><span data-stu-id="22f31-122">Now update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="22f31-123">Ouvrez le `./app.js` fichier et ajoutez l’instruction `require` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="22f31-123">Open the `./app.js` file and add the following `require` statement to the top of the file.</span></span>

```js
var session = require('express-session');
var flash = require('connect-flash');
```

<span data-ttu-id="22f31-124">Ajoutez le code suivant immédiatement après la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="22f31-124">Add the following code immediately after the `var app = express();` line.</span></span>

```js
// Session middleware
// NOTE: Uses default in-memory session store, which is not
// suitable for production
app.use(session({
  secret: 'your_secret_value_here',
  resave: false,
  saveUninitialized: false,
  unset: 'destroy'
}));

// Flash middleware
app.use(flash());

// Set up local vars for template layout
app.use(function(req, res, next) {
  // Read any flashed errors and save
  // in the response locals
  res.locals.error = req.flash('error_msg');

  // Check for simple error string and
  // convert to layout's expected format
  var errs = req.flash('error');
  for (var i in errs){
    res.locals.error.push({message: 'An error occurred', debug: errs[i]});
  }

  next();
});
```

## <a name="design-the-app"></a><span data-ttu-id="22f31-125">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="22f31-125">Design the app</span></span>

<span data-ttu-id="22f31-126">Commencez par créer la disposition globale de l’application.</span><span class="sxs-lookup"><span data-stu-id="22f31-126">Start by creating the global layout for the app.</span></span> <span data-ttu-id="22f31-127">Ouvrez le `./views/layout.hbs` fichier et remplacez l’intégralité du contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="22f31-127">Open the `./views/layout.hbs` file and replace the entire contents with the following code.</span></span>

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Node.js Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    <link rel='stylesheet' href='/stylesheets/style.css' />
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="/" class="navbar-brand">Node.js Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="/" class="nav-link{{#if active.home}} active{{/if}}">Home</a>
            </li>
            {{#if user}}
              <li class="nav-item" data-turbolinks="false">
                <a href="/calendar" class="nav-link{{#if active.calendar}} active{{/if}}">Calendar</a>
              </li>
            {{/if}}
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            {{#if user}}
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true"
                  aria-expanded="false">
                  {{#if user.avatar}}
                    <img src="{{ user.avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  {{else}}
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  {{/if}}
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ user.displayName }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ user.email }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="/auth/signout" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            {{else}}
              <li class="nav-item">
                <a href="/auth/signin" class="nav-link">Sign In</a>
              </li>
            {{/if}}
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      {{#each error}}
        <div class="alert alert-danger" role="alert">
          <p class="mb-3">{{ this.message }}</p>
          {{#if this.debug }}
            <pre class="alert-pre border bg-light p-2"><code>{{ this.debug }}</code></pre>
          {{/if}}
        </div>
      {{/each}}

      {{{body}}}
    </main>
  </body>
</html>
```

<span data-ttu-id="22f31-128">Ce code ajoute [bootstrap](http://getbootstrap.com/) pour les styles simples et [font Isard](https://fontawesome.com/) pour certaines icônes simples.</span><span class="sxs-lookup"><span data-stu-id="22f31-128">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="22f31-129">Il définit également une disposition globale avec une barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="22f31-129">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="22f31-130">Maintenant, `./public/stylesheets/style.css` ouvrez et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="22f31-130">Now open `./public/stylesheets/style.css` and replace its entire contents with the following.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="22f31-131">À présent, mettez à jour la page par défaut.</span><span class="sxs-lookup"><span data-stu-id="22f31-131">Now update the default page.</span></span> <span data-ttu-id="22f31-132">Ouvrez le `./views/index.hbs` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="22f31-132">Open the `./views/index.hbs` file and replace its contents with the following.</span></span>

```html
<div class="jumbotron">
  <h1>Node.js Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Node.js</p>
  {{#if user}}
    <h4>Welcome {{ user.displayName }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  {{else}}
    <a href="/auth/signin" class="btn btn-primary btn-large">Click here to sign in</a>
  {{/if}}
</div>
```

<span data-ttu-id="22f31-133">Ouvrez le `./routes/index.js` fichier et remplacez le code existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="22f31-133">Open the `./routes/index.js` file and replace the existing code with the following.</span></span>

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  let params = {
    active: { home: true }
  };

  res.render('index', params);
});

module.exports = router;
```

<span data-ttu-id="22f31-134">Enregistrez toutes vos modifications et redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="22f31-134">Save all of your changes and restart the server.</span></span> <span data-ttu-id="22f31-135">À présent, l’application doit être très différente.</span><span class="sxs-lookup"><span data-stu-id="22f31-135">Now, the app should look very different.</span></span>

![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)