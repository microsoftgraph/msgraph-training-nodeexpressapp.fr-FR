<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d785a-101">Dans cette section, vous allez ajouter la possibilité de créer des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d785a-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-a-new-event-form"></a><span data-ttu-id="d785a-102">Créer un formulaire d’événement</span><span class="sxs-lookup"><span data-stu-id="d785a-102">Create a new event form</span></span>

1. <span data-ttu-id="d785a-103">Créez un fichier dans le répertoire **./views** nommé **NewEvent. BH** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="d785a-103">Create a new file in the **./views** directory named **newevent.hbs** and add the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/newevent.hbs" id="NewEventFormSnippet":::

1. <span data-ttu-id="d785a-104">Ajoutez le code suivant au fichier **calendar.js./routes/** avant la `module.exports = router;` ligne.</span><span class="sxs-lookup"><span data-stu-id="d785a-104">Add the following code to the **./routes/calendar.js** file before the `module.exports = router;` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetEventFormSnippet":::

<span data-ttu-id="d785a-105">Cela implémente un formulaire pour l’entrée utilisateur et l’affiche.</span><span class="sxs-lookup"><span data-stu-id="d785a-105">This implements a form for user input and renders it.</span></span>

## <a name="create-the-event"></a><span data-ttu-id="d785a-106">Créer l’événement</span><span class="sxs-lookup"><span data-stu-id="d785a-106">Create the event</span></span>

1. <span data-ttu-id="d785a-107">Ouvrez **./graph.js** et ajoutez la fonction suivante dans `module.exports` .</span><span class="sxs-lookup"><span data-stu-id="d785a-107">Open **./graph.js** and add the following function inside `module.exports`.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="CreateEventSnippet":::

    <span data-ttu-id="d785a-108">Ce code utilise les champs de formulaire pour créer un objet d’événement Graph, puis envoie une requête POST au `/me/events` point de terminaison pour créer l’événement dans le calendrier par défaut de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d785a-108">This code uses the form fields to create a Graph event object, then sends a POST request to the `/me/events` endpoint to create the event on the user's default calendar.</span></span>

1. <span data-ttu-id="d785a-109">Ajoutez le code suivant au fichier **calendar.js./routes/** avant la `module.exports = router;` ligne.</span><span class="sxs-lookup"><span data-stu-id="d785a-109">Add the following code to the **./routes/calendar.js** file before the `module.exports = router;` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="PostEventFormSnippet":::

    <span data-ttu-id="d785a-110">Ce code valide et nettoie l’entrée du formulaire, puis appelle `graph.createEvent` la création de l’événement.</span><span class="sxs-lookup"><span data-stu-id="d785a-110">This code validates and sanitized the form input, then calls `graph.createEvent` to create the event.</span></span> <span data-ttu-id="d785a-111">Il redirige vers l’affichage Calendrier une fois l’appel terminé.</span><span class="sxs-lookup"><span data-stu-id="d785a-111">It redirects back to the calendar view after the call completes.</span></span>

1. <span data-ttu-id="d785a-112">Enregistrez vos modifications, puis redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="d785a-112">Save your changes and restart the app.</span></span> <span data-ttu-id="d785a-113">Cliquez sur l’élément de navigation **calendrier** , puis cliquez sur le bouton **créer un événement** .</span><span class="sxs-lookup"><span data-stu-id="d785a-113">Click the **Calendar** nav item, then click the **Create event** button.</span></span> <span data-ttu-id="d785a-114">Renseignez les valeurs et cliquez sur **créer**.</span><span class="sxs-lookup"><span data-stu-id="d785a-114">Fill in the values and click **Create**.</span></span> <span data-ttu-id="d785a-115">L’application revient à l’affichage Calendrier une fois que le nouvel événement est créé.</span><span class="sxs-lookup"><span data-stu-id="d785a-115">The app returns to the calendar view once the new event is created.</span></span>

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event-01.png)
