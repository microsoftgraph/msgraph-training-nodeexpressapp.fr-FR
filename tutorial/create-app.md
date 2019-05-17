<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez utiliser [Express](http://expressjs.com/) pour créer une application Web. Si le générateur Express n’est pas déjà installé, vous pouvez l’installer à partir de votre interface de ligne de commande (CLI) à l’aide de la commande suivante.

```Shell
npm install express-generator -g
```

Ouvrez votre interface CLI, accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez la commande suivante pour créer une nouvelle application [](http://handlebarsjs.com/) expresse qui utilise des guidés comme moteur de rendu.

```Shell
express --hbs graph-tutorial
```

Le générateur Express crée un nouveau répertoire appelé `graph-tutorial` et génère un squelette d’application Express. Accédez à ce nouveau répertoire et entrez la commande suivante pour installer les dépendances.

```Shell
npm install
```

Une fois que cette commande est terminée, utilisez la commande suivante pour démarrer un serveur Web local.

```Shell
npm start
```

Ouvrez votre navigateur et accédez à `http://localhost:3000`. Si tout fonctionne, le message «Bienvenue dans Express» s’affiche. Si vous ne voyez pas ce message, consultez le [Guide de prise](http://expressjs.com/starter/generator.html)en main de Express.

Avant de poursuivre, installez des gemmes supplémentaires que vous utiliserez plus tard:

- [dotenv](https://github.com/motdotla/dotenv) pour le chargement de valeurs à partir d’un fichier. env.
- [moment](https://github.com/moment/moment/) de mise en forme des valeurs de date/heure.
- [Connect-Flash](https://github.com/jaredhanson/connect-flash) to Flash error messages in the App.
- [Express-session](https://github.com/expressjs/session) pour stocker des valeurs dans une session côté serveur en mémoire.
- [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad) pour l’authentification et l’obtention de jetons d’accès.
- [simple-oauth2](https://github.com/lelylan/simple-oauth2) pour la gestion des jetons.
- [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.

Exécutez la commande suivante dans votre interface CLI.

```Shell
npm install dotenv@6.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.15.6
npm install passport-azure-ad@4.0.0 simple-oauth2@2.2.1 @microsoft/microsoft-graph-client@1.5.2
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

Maintenant, mettez à jour l’application `connect-flash` pour `express-session` utiliser le et le middleware. Ouvrez le `./app.js` fichier et ajoutez l’instruction `require` suivante en haut du fichier.

```js
var session = require('express-session');
var flash = require('connect-flash');
```

Ajoutez le code suivant immédiatement après la `var app = express();` ligne.

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

## <a name="design-the-app"></a>Concevoir l’application

Commencez par créer la disposition globale de l’application. Ouvrez le `./views/layout.hbs` fichier et remplacez l’intégralité du contenu par le code suivant.

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

Ce code ajoute [bootstrap](http://getbootstrap.com/) pour les styles simples et [font Isard](https://fontawesome.com/) pour certaines icônes simples. Il définit également une disposition globale avec une barre de navigation.

Maintenant, `./public/stylesheets/style.css` ouvrez et remplacez l’intégralité de son contenu par ce qui suit.

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

À présent, mettez à jour la page par défaut. Ouvrez le `./views/index.hbs` fichier et remplacez son contenu par ce qui suit.

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

Ouvrez le `./routes/index.js` fichier et remplacez le code existant par ce qui suit.

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

Enregistrez toutes vos modifications et redémarrez le serveur. À présent, l’application doit être très différente.

![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)