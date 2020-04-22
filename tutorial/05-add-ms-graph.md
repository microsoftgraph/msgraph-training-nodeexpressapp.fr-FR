<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Ouvrez `./graph.js` et ajoutez la fonction suivante dans `module.exports`.

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetEventsSnippet":::

    Que fait ce code ?

    - L’URL qui sera appelée est `/me/events`.
    - La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.
    - La `orderby` méthode trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.

1. Créez un fichier dans le `./routes` répertoire nommé `calendar.js`et ajoutez le code suivant.

    ```javascript
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
          else {
            req.flash('error_msg', 'Could not get an access token');
          }
        }
      }
    );

    module.exports = router;
    ```

1. Mettre `./app.js` à jour pour utiliser ce nouvel itinéraire. Ajoutez la ligne suivante **avant** la `var app = express();` ligne.

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. Ajoutez la ligne suivante **après** la `app.use('/auth', authRouter);` ligne.

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. Redémarrez le serveur. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais ajouter une vue pour afficher les résultats de façon plus parlante.

1. Ajoutez le code `./app.js` suivant **après** la `app.set('view engine', 'hbs');` ligne.

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    Cela implémente une [assistance de guidon](http://handlebarsjs.com/#helpers) pour mettre en forme la date ISO 8601 renvoyée par Microsoft Graph en une solution plus conviviale.

1. Créez un fichier dans le `./views` répertoire nommé `calendar.hbs` et ajoutez le code suivant.

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    Cela permet de parcourir une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.

1. À présent, mettez à `./routes/calendar.js` jour l’itinéraire dans pour utiliser cet affichage. Remplacez l’itinéraire existant par le code suivant.

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="16-19,26,28-31,37":::

1. Enregistrez vos modifications, redémarrez le serveur et connectez-vous à l’application. Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.

    ![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
