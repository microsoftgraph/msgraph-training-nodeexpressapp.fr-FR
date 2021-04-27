# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="7d956-101">Comment exécuter le projet terminé</span><span class="sxs-lookup"><span data-stu-id="7d956-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d956-102">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="7d956-102">Prerequisites</span></span>

<span data-ttu-id="7d956-103">Pour exécuter le projet terminé dans ce dossier, vous devez :</span><span class="sxs-lookup"><span data-stu-id="7d956-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="7d956-104">[Node.js](https://nodejs.org) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="7d956-104">[Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="7d956-105">Si vous n'avez pas Node.js, consultez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="7d956-105">If you do not have Node.js, visit the previous link for download options.</span></span> <span data-ttu-id="7d956-106">(**Remarque : ce** didacticiel a été écrit avec Node version 14.15.0.</span><span class="sxs-lookup"><span data-stu-id="7d956-106">(**Note:** This tutorial was written with Node version 14.15.0.</span></span> <span data-ttu-id="7d956-107">Les étapes de ce guide peuvent fonctionner avec d'autres versions, mais elles n'ont pas été testées.)</span><span class="sxs-lookup"><span data-stu-id="7d956-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="7d956-108">Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte scolaire ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7d956-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="7d956-109">Si vous n'avez pas de compte Microsoft, deux options s'offrent à vous pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="7d956-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="7d956-110">Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="7d956-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="7d956-111">Vous pouvez [vous inscrire au programme Office 365 développeur](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.</span><span class="sxs-lookup"><span data-stu-id="7d956-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="7d956-112">Inscrire une application web auprès du Centre d Azure Active Directory'administration</span><span class="sxs-lookup"><span data-stu-id="7d956-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="7d956-113">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="7d956-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="7d956-114">Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="7d956-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="7d956-115">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="7d956-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="7d956-116">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="7d956-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="7d956-117">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="7d956-117">Select **New registration**.</span></span> <span data-ttu-id="7d956-118">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="7d956-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="7d956-119">Définissez le **Nom** sur `Node.js Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="7d956-119">Set **Name** to `Node.js Graph Tutorial`.</span></span>
    - <span data-ttu-id="7d956-120">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="7d956-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="7d956-121">Sous **URI de redirection**, définissez la première flèche déroulante sur `Web`, et la valeur sur `http://localhost:3000/auth/callback`.</span><span class="sxs-lookup"><span data-stu-id="7d956-121">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:3000/auth/callback`.</span></span>

    ![Capture d’écran de la page Inscrire une application](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="7d956-123">Choisissez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="7d956-123">Choose **Register**.</span></span> <span data-ttu-id="7d956-124">Dans la pageNode.js Graph didacticiel, copiez la valeur de l'ID **d'application (client)** et enregistrez-la. Vous en aurez besoin à l'étape suivante. </span><span class="sxs-lookup"><span data-stu-id="7d956-124">On the **Node.js Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="7d956-126">Sélectionnez **Certificats et secrets** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="7d956-126">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="7d956-127">Sélectionnez le bouton **Nouveau secret client**.</span><span class="sxs-lookup"><span data-stu-id="7d956-127">Select the **New client secret** button.</span></span> <span data-ttu-id="7d956-128">Entrez une valeur dans **Description**, sélectionnez une des options pour **Expire le**, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="7d956-128">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Une capture d’écran de la boîte de dialogue Ajouter une clé secrète client](/tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="7d956-130">Copiez la valeur due la clé secrète client avant de quitter cette page.</span><span class="sxs-lookup"><span data-stu-id="7d956-130">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="7d956-131">Vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="7d956-131">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="7d956-132">Ce secret client n’apparaîtra plus jamais, aussi veillez à le copier maintenant.</span><span class="sxs-lookup"><span data-stu-id="7d956-132">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Une capture d’écran de la clé secrète client nouvellement ajoutée](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="7d956-134">Configurer l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d956-134">Configure the sample</span></span>

1. <span data-ttu-id="7d956-135">Renommons `example.env` le fichier `.env` .</span><span class="sxs-lookup"><span data-stu-id="7d956-135">Rename the `example.env` file to `.env`.</span></span>
1. <span data-ttu-id="7d956-136">Modifiez `.env` le fichier et a apporter les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="7d956-136">Edit the `.env` file and make the following changes.</span></span>
    1. <span data-ttu-id="7d956-137">Remplacez `YOUR_CLIENT_SECRET_HERE` par **l'ID d'application** que vous avez obtenu à partir du portail d'inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="7d956-137">Replace `YOUR_CLIENT_SECRET_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="7d956-138">Remplacez `YOUR_APP_PASSWORD_HERE` par le mot de passe que vous avez obtenu à partir du portail d'inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="7d956-138">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="7d956-139">Dans votre interface de ligne de commande, accédez à ce répertoire et exécutez la commande suivante pour installer les conditions requises.</span><span class="sxs-lookup"><span data-stu-id="7d956-139">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    npm install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="7d956-140">Exécution de l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d956-140">Run the sample</span></span>

1. <span data-ttu-id="7d956-141">Exécutez la commande suivante dans votre CLI pour démarrer l'application.</span><span class="sxs-lookup"><span data-stu-id="7d956-141">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="7d956-142">Ouvrez un navigateur et accédez à `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="7d956-142">Open a browser and browse to `http://localhost:3000`.</span></span>
