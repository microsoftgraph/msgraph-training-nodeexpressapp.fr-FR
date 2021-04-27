<!-- markdownlint-disable MD002 MD041 -->

Ce didacticiel vous apprend à créer une application web Node.js Express qui utilise l'API Microsoft Graph pour récupérer les informations de calendrier d'un utilisateur.

> [!TIP]
> Si vous préférez simplement télécharger le didacticiel terminé, vous pouvez le télécharger de deux manières.
>
> - Téléchargez le [Node.js démarrage rapide pour](https://developer.microsoft.com/graph/quick-start?platform=option-node) obtenir du code de travail en quelques minutes.
> - Téléchargez ou clonez [le GitHub de stockage.](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)

## <a name="prerequisites"></a>Configuration requise

Avant de commencer cette démonstration, vous devez avoir [ installéNode.js](https://nodejs.org) sur votre ordinateur de développement. Si vous n'avez pas Node.js, consultez le lien précédent pour obtenir les options de téléchargement.

> [!NOTE]
> Windows utilisateurs devront peut-être installer Python et Visual Studio Build Tools pour prendre en charge les modules NPM qui doivent être compilés à partir de C/C++. Le programme Node.js installer sur Windows offre la possibilité d'installer automatiquement ces outils. Vous pouvez également suivre les instructions sur [https://github.com/nodejs/node-gyp#on-windows](https://github.com/nodejs/node-gyp#on-windows) .

Vous devez également avoir un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, ou un compte scolaire ou scolaire Microsoft. Si vous n'avez pas de compte Microsoft, deux options s'offrent à vous pour obtenir un compte gratuit :

- Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Vous pouvez [vous inscrire au programme Office 365 développeur](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.

> [!NOTE]
> Ce didacticiel a été écrit avec Node version 14.15.0. Les étapes de ce guide peuvent fonctionner avec d'autres versions, mais elles n'ont pas été testées.

## <a name="feedback"></a>Commentaires

N'hésitez pas à nous faire part de vos commentaires sur ce didacticiel [dans GitHub référentiel.](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)
