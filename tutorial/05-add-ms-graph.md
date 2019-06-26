<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2a7c6-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="2a7c6-102">Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="2a7c6-103">Obtenir des événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="2a7c6-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="2a7c6-104">Commencez par ajouter une nouvelle méthode au `./graph.js` fichier pour obtenir les événements à partir du calendrier.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-104">Start by adding a new method to the `./graph.js` file to get the events from the calendar.</span></span> <span data-ttu-id="2a7c6-105">Ajoutez la fonction suivante à l' `module.exports` intérieur `./graph.js`du dans.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-105">Add the following function inside the `module.exports` in `./graph.js`.</span></span>

```js
getEvents: async function(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const events = await client
    .api('/me/events')
    .select('subject,organizer,start,end')
    .orderby('createdDateTime DESC')
    .get();

  return events;
}
```

<span data-ttu-id="2a7c6-106">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="2a7c6-107">L’URL qui sera appelée est `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-107">The URL that will be called is `/me/events`.</span></span>
- <span data-ttu-id="2a7c6-108">La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-108">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="2a7c6-109">La `orderby` méthode trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-109">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="2a7c6-110">Créez un fichier dans le `./routes` répertoire nommé `calendar.js`et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-110">Create a new file in the `./routes` directory named `calendar.js`, and add the following code.</span></span>

```js
var express = require('express');
var router = express.Router();
var tokens = require('../tokens.js');
var graph = require('../graph.js');

/* GET /calendar */
router.get('/',
  async function(req, res) {
    if (!req.isAuthenticated()) {
      // Redirect unauthenticated requests to home page
      res.redirect('/')
    } else {
      let params = {
        active: { calendar: true }
      };

      // Get the access token
      var accessToken;
      try {
        accessToken = await tokens.getAccessToken(req);
      } catch (err) {
        res.json(err);
      }

      if (accessToken && accessToken.length > 0) {
        try {
          // Get the events
          var events = await graph.getEvents(accessToken);

          res.json(events.value);
        } catch (err) {
          res.json(err);
        }
      }
    }
  }
);

module.exports = router;
```

<span data-ttu-id="2a7c6-111">Mettre `./app.js` à jour pour utiliser ce nouvel itinéraire.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-111">Update `./app.js` to use this new route.</span></span> <span data-ttu-id="2a7c6-112">Ajoutez la ligne suivante **avant** la `var app = express();` ligne.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-112">Add the following line **before** the `var app = express();` line.</span></span>

```js
var calendarRouter = require('./routes/calendar');
```

<span data-ttu-id="2a7c6-113">Ajoutez ensuite la ligne suivante **après** la `app.use('/auth', authRouter);` ligne.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-113">Then add the following line **after** the `app.use('/auth', authRouter);` line.</span></span>

```js
app.use('/calendar', calendarRouter);
```

<span data-ttu-id="2a7c6-114">À présent, vous pouvez le tester.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-114">Now you can test this.</span></span> <span data-ttu-id="2a7c6-115">Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-115">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="2a7c6-116">Si tout fonctionne, vous devez voir un vidage JSON des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-116">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="2a7c6-117">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="2a7c6-117">Display the results</span></span>

<span data-ttu-id="2a7c6-118">À présent, vous pouvez ajouter une vue pour afficher les résultats de manière plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-118">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="2a7c6-119">Tout d’abord, ajoutez le code `./app.js` suivant dans `app.set('view engine', 'hbs');` **après** la ligne.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-119">First, add the following code in `./app.js` **after** the `app.set('view engine', 'hbs');` line.</span></span>

```js
var hbs = require('hbs');
var moment = require('moment');
// Helper to format date/time sent by Graph
hbs.registerHelper('eventDateTime', function(dateTime){
  return moment(dateTime).format('M/D/YY h:mm A');
});
```

<span data-ttu-id="2a7c6-120">Cela implémente une [assistance](http://handlebarsjs.com/#helpers) de guidon pour mettre en forme la date ISO 8601 renvoyée par Microsoft Graph en une solution plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-120">This implements a [Handlebars helper](http://handlebarsjs.com/#helpers) to format the ISO 8601 date returned by Microsoft Graph into something more human-friendly.</span></span>

<span data-ttu-id="2a7c6-121">Créez un fichier dans le `./views` répertoire nommé `calendar.hbs` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-121">Create a new file in the `./views` directory named `calendar.hbs` and add the following code.</span></span>

```html
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    {{#each events}}
      <tr>
        <td>{{this.organizer.emailAddress.name}}</td>
        <td>{{this.subject}}</td>
        <td>{{eventDateTime this.start.dateTime}}</td>
        <td>{{eventDateTime this.end.dateTime}}</td>
      </tr>
    {{/each}}
  </tbody>
</table>
```

<span data-ttu-id="2a7c6-122">Cela permet d’exécuter une boucle dans une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-122">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="2a7c6-123">À présent, mettez à `./routes/calendar.js` jour l’itinéraire dans pour utiliser cet affichage.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-123">Now update the route in `./routes/calendar.js` to use this view.</span></span> <span data-ttu-id="2a7c6-124">Remplacez l’itinéraire existant par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-124">Replace the existing route with the following code.</span></span>

```js
router.get('/',
  async function(req, res) {
    if (!req.isAuthenticated()) {
      // Redirect unauthenticated requests to home page
      res.redirect('/')
    } else {
      let params = {
        active: { calendar: true }
      };

      // Get the access token
      var accessToken;
      try {
        accessToken = await tokens.getAccessToken(req);
      } catch (err) {
        req.flash('error_msg', {
          message: 'Could not get access token. Try signing out and signing in again.',
          debug: JSON.stringify(err)
        });
      }

      if (accessToken && accessToken.length > 0) {
        try {
          // Get the events
          var events = await graph.getEvents(accessToken);
          params.events = events.value;
        } catch (err) {
          req.flash('error_msg', {
            message: 'Could not fetch events',
            debug: JSON.stringify(err)
          });
        }
      }

      res.render('calendar', params);
    }
  }
);
```

<span data-ttu-id="2a7c6-125">Enregistrez vos modifications, redémarrez le serveur et connectez-vous à l’application.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-125">Save your changes, restart the server, and sign in to the app.</span></span> <span data-ttu-id="2a7c6-126">Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.</span><span class="sxs-lookup"><span data-stu-id="2a7c6-126">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)