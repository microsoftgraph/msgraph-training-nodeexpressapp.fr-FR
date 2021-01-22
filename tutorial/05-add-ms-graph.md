<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [cliente microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Ouvrez **./graph.js** et ajoutez la fonction suivante à l’intérieur. `module.exports`

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetCalendarViewSnippet":::

    Que fait ce code ?

    - L’URL qui sera appelée est `/me/calendarview`.
    - La `header` méthode ajoute l’en-tête à la demande, ce qui entraîne le retour des heures de début et de fin dans le `Prefer: outlook.timezone` fuseau horaire de l’utilisateur.
    - La `query` méthode définit les `startDateTime` `endDateTime` paramètres et les paramètres de l’affichage Calendrier.
    - La méthode limite les champs renvoyés pour chaque événement à ceux que `select` l’affichage utilisera réellement.
    - La `orderby` méthode trie les résultats par heure de début.
    - La `top` méthode limite les résultats à 50 événements.

1. Créez un fichier dans le répertoire **./routes** nommé **calendar.js** et ajoutez le code suivant.

    ```javascript
    const router = require('express-promise-router')();
    const graph = require('../graph.js');
    const moment = require('moment-timezone');
    const iana = require('windows-iana');
    const { body, validationResult } = require('express-validator');
    const validator = require('validator');

    /* GET /calendar */
    router.get('/',
      async function(req, res) {
        if (!req.session.userId) {
          // Redirect unauthenticated requests to home page
          res.redirect('/')
        } else {
          const params = {
            active: { calendar: true }
          };

          // Get the user
          const user = req.app.locals.users[req.session.userId];
          // Convert user's Windows time zone ("Pacific Standard Time")
          // to IANA format ("America/Los_Angeles")
          // Moment needs IANA format
          const timeZoneId = iana.findOneIana(user.timeZone);
          console.log(`Time zone: ${timeZoneId.valueOf()}`);

          // Calculate the start and end of the current week
          // Get midnight on the start of the current week in the user's timezone,
          // but in UTC. For example, for Pacific Standard Time, the time value would be
          // 07:00:00Z
          var startOfWeek = moment.tz(timeZoneId.valueOf()).startOf('week').utc();
          var endOfWeek = moment(startOfWeek).add(7, 'day');
          console.log(`Start: ${startOfWeek.format()}`);

          // Get the access token
          var accessToken;
          try {
            accessToken = await getAccessToken(req.session.userId, req.app.locals.msalClient);
          } catch (err) {
            res.send(JSON.stringify(err, Object.getOwnPropertyNames(err)));
            return;
          }

          if (accessToken && accessToken.length > 0) {
            try {
              // Get the events
              const events = await graph.getCalendarView(
                accessToken,
                startOfWeek.format(),
                endOfWeek.format(),
                user.timeZone);

              res.json(events.value);
            } catch (err) {
              res.send(JSON.stringify(err, Object.getOwnPropertyNames(err)));
            }
          }
          else {
            req.flash('error_msg', 'Could not get an access token');
          }
        }
      }
    );

    async function getAccessToken(userId, msalClient) {
      // Look up the user's account in the cache
      try {
        const accounts = await msalClient
          .getTokenCache()
          .getAllAccounts();

        const userAccount = accounts.find(a => a.homeAccountId === userId);

        // Get the token silently
        const response = await msalClient.acquireTokenSilent({
          scopes: process.env.OAUTH_SCOPES.split(','),
          redirectUri: process.env.OAUTH_REDIRECT_URI,
          account: userAccount
        });

        return response.accessToken;
      } catch (err) {
        console.log(JSON.stringify(err, Object.getOwnPropertyNames(err)));
      }
    }

    module.exports = router;
    ```

1. Mettez **à jour ./app.js** pour utiliser ce nouvel itinéraire. Ajoutez la ligne suivante **avant** la `var app = express();` ligne.

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. Ajoutez la ligne suivante **après** la `app.use('/auth', authRouter);` ligne.

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. Redémarrez le serveur. Connectez-vous et cliquez **sur le lien** Calendrier dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais ajouter une vue pour afficher les résultats de façon plus parlante.

1. Ajoutez le code suivant dans **./app.js après** la `app.set('view engine', 'hbs');` ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    Cela implémente un outil d’aide [Handlebars](http://handlebarsjs.com/#helpers) pour mettre en forme la date ISO 8601 renvoyée par Microsoft Graph en quelque chose de plus convivial.

1. Créez un fichier dans le répertoire **./views** nommé **calendar.hbs** et ajoutez le code suivant.

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    Cela permet de parcourir une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.

1. Maintenant, mettez à jour **l’itinéraire dans ./routes/calendar.js** pour utiliser cet affichage. Remplacez l’itinéraire existant par le code suivant.

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="33-36,49,51-54,61":::

1. Enregistrez vos modifications, redémarrez le serveur et connectez-vous à l’application. Cliquez sur le **lien Calendrier** et l’application doit maintenant restituer une table des événements.

    ![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
