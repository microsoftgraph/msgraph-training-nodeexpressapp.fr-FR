<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="76867-101">Ce didacticiel vous apprend à créer une application Web node. js Express qui utilise l’API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="76867-101">This tutorial teaches you how to build a Node.js Express web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="76867-102">Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez le télécharger de deux manières.</span><span class="sxs-lookup"><span data-stu-id="76867-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="76867-103">Téléchargez le [démarrage rapide de node. js](https://developer.microsoft.com/graph/quick-start?platform=option-node) pour obtenir du code de travail en quelques minutes.</span><span class="sxs-lookup"><span data-stu-id="76867-103">Download the [Node.js quick start](https://developer.microsoft.com/graph/quick-start?platform=option-node) to get working code in minutes.</span></span>
> - <span data-ttu-id="76867-104">Téléchargez ou clonez le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span><span class="sxs-lookup"><span data-stu-id="76867-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="76867-105">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="76867-105">Prerequisites</span></span>

<span data-ttu-id="76867-106">Avant de commencer cette démonstration, [node. js](https://nodejs.org) doit être installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="76867-106">Before you start this demo, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="76867-107">Si vous n’avez pas node. js, consultez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="76867-107">If you do not have Node.js, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="76867-108">Ce didacticiel a été écrit avec le nœud version 10.15.3.</span><span class="sxs-lookup"><span data-stu-id="76867-108">This tutorial was written with Node version 10.15.3.</span></span> <span data-ttu-id="76867-109">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="76867-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="watch-the-tutorial"></a><span data-ttu-id="76867-110">Regarder le didacticiel</span><span class="sxs-lookup"><span data-stu-id="76867-110">Watch the tutorial</span></span>

<span data-ttu-id="76867-111">Ce module a été enregistré et est disponible dans le canal YouTube de développement Office.</span><span class="sxs-lookup"><span data-stu-id="76867-111">This module has been recorded and is available in the Office Development YouTube channel.</span></span>

<!-- markdownlint-disable MD033 MD034 -->
<br/>

> [!VIDEO https://www.youtube-nocookie.com/embed/n6q8Cm-pTYY]
<!-- markdownlint-enable MD033 MD034 -->

## <a name="feedback"></a><span data-ttu-id="76867-112">Commentaires</span><span class="sxs-lookup"><span data-stu-id="76867-112">Feedback</span></span>

<span data-ttu-id="76867-113">Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span><span class="sxs-lookup"><span data-stu-id="76867-113">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>
