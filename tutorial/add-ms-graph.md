<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenir des événements de calendrier à partir d’Outlook

Commencez par ajouter une nouvelle méthode au `./graph.js` fichier pour obtenir les événements à partir du calendrier. Ajoutez la fonction suivante à l' `module.exports` intérieur `./graph.js`du dans.

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

Examinez ce que fait ce code.

- L’URL qui sera appelée est `/me/events`.
- La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.
- La `orderby` méthode trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.

Créez un fichier dans le `./routes` répertoire nommé `calendar.js`et ajoutez le code suivant.

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

Mettre `./app.js` à jour pour utiliser ce nouvel itinéraire. Ajoutez la ligne suivante **avant** la `var app = express();` ligne.

```js
var calendarRouter = require('./routes/calendar');
```

Ajoutez ensuite la ligne suivante **après** la `app.use('/auth', authRouter);` ligne.

```js
app.use('/calendar', calendarRouter);
```

À présent, vous pouvez le tester. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devez voir un vidage JSON des événements sur le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez ajouter une vue pour afficher les résultats de manière plus conviviale. Tout d’abord, ajoutez le code `./app.js` suivant dans `app.set('view engine', 'hbs');` **après** la ligne.

```js
var hbs = require('hbs');
var moment = require('moment');
// Helper to format date/time sent by Graph
hbs.registerHelper('eventDateTime', function(dateTime){
  return moment(dateTime).format('M/D/YY h:mm A');
});
```

Cela implémente une [assistance](http://handlebarsjs.com/#helpers) de guidon pour mettre en forme la date ISO 8601 renvoyée par Microsoft Graph en une solution plus conviviale.

Créez un fichier dans le `./views` répertoire nommé `calendar.hbs` et ajoutez le code suivant.

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

Cela permet d’exécuter une boucle dans une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux. À présent, mettez à `./routes/calendar.js` jour l’itinéraire dans pour utiliser cet affichage. Remplacez l’itinéraire existant par le code suivant.

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

Enregistrez vos modifications, redémarrez le serveur et connectez-vous à l’application. Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
