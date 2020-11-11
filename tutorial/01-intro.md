<!-- markdownlint-disable MD002 MD041 -->

Ce didacticiel vous apprend à créer une Node.js application Web Express qui utilise l’API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.

> [!TIP]
> Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez le télécharger de deux manières.
>
> - Téléchargez le [Node.js démarrage rapide](https://developer.microsoft.com/graph/quick-start?platform=option-node) pour obtenir du code de travail en quelques minutes.
> - Téléchargez ou clonez le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cette démonstration, [Node.js](https://nodejs.org) doit être installé sur votre ordinateur de développement. Si vous n’avez pas Node.js, reportez-vous au lien précédent pour obtenir les options de téléchargement.

> [!NOTE]
> Les utilisateurs de Windows peuvent avoir besoin d’installer Python et des outils de génération Visual Studio pour prendre en charge les modules NPM qui doivent être compilés à partir de C/C++. Le programme d’installation Node.js sur Windows offre une option permettant d’installer automatiquement ces outils. Vous pouvez également suivre les instructions à l’adresse [https://github.com/nodejs/node-gyp#on-windows](https://github.com/nodejs/node-gyp#on-windows) .

Vous devez également disposer d’un compte Microsoft personnel disposant d’une boîte aux lettres sur Outlook.com ou d’un compte professionnel ou scolaire Microsoft. Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :

- Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.

> [!NOTE]
> Ce didacticiel a été écrit avec le nœud version 12.18.4. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.

## <a name="feedback"></a>Commentaires

Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).
