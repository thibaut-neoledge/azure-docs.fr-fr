---
title: "Créer des connecteurs personnalisés à partir d’API Web - Azure Logic Apps | Microsoft Docs"
description: "Créer des connecteurs personnalisés à partir d’API Web"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Créer des connecteurs personnalisés à partir d’API Web

Pour créer un connecteur personnalisé utilisable dans Azure Logic Apps, Microsoft Flow ou Microsoft PowerApps, commencez par créer une API web que vous hébergerez avec Azure Web Apps, authentifierez avec Azure Active Directory et inscrirez en tant que connecteur avec Logic Apps, Flow ou PowerApps. Ce didacticiel explique comment effectuer ces tâches en créant une application API Web ASP.NET. Vous trouverez des modèles qui montrent comment structurer le code pour les déclencheurs et les actions de votre connecteur sur la page [Créer des API personnalisées qui seront appelées par des workflows d’applications logiques](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Composants requis

* [Visual Studio 2013 ou une version ultérieure](https://www.visualstudio.com/vs/). Ce didacticiel utilise Visual Studio 2015.

* Le code de votre API Web. Si vous n’en avez pas, essayez ce didacticiel : [Bien démarrer avec l’API Web ASP.NET 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez commencer avec un [compte Azure gratuit](https://azure.microsoft.com/free/). Sinon, souscrivez à un [abonnement avec paiement au fur et à mesure](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Créer et déployer une application Web ASP.NET sur Azure

Pour les besoins de ce didacticiel, créez une application Web ASP.NET Visual C#. 

1. Ouvrez Visual Studio 2015, puis sélectionnez **Fichier** > **Nouveau Projet**.

   1. Développez **Installé**, accédez à **Modèles** > **Visual C#** > **Web**, puis sélectionnez **Application Web ASP.NET**.

   2. Indiquez le nom du projet, l’emplacement et le nom de la solution associée à votre application, puis sélectionnez **OK**.

   Par exemple :

   ![Créer une application Web ASP.NET Visual C#](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. Dans la zone **Nouvelle application Web ASP.NET**, sélectionnez le modèle **API Web**. Si la case n’est pas cochée, sélectionnez **Héberger dans le cloud**. Cliquez sur **Modifier l’authentification**.

   ![Sélectionnez le modèle « API Web », « Héberger dans le cloud », « Modifier l’authentification »](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Sélectionnez **Aucune authentification**, puis cliquez sur **OK**. Vous pourrez configurer l’authentification plus tard.

   ![Sélectionnez « Aucune authentification »](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. Lorsque la zone **Nouvelle application Web ASP.NET** s’affiche de nouveau, sélectionnez **OK**. 

5. Dans la zone **Créer un service App Service**, passez en revue les paramètres d’hébergement décrits dans le tableau, apportez les modifications que vous souhaitez et cliquez sur **Créer**. 

   Un [Plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) représente une collection de ressources physiques utilisées pour héberger des applications dans un abonnement Azure. Découvrez [App Service](../app-service/app-service-value-prop-what-is.md).

   ![Créer un App Service](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | Votre compte professionnel ou scolaire Azure, ou votre compte Microsoft personnel | *votre-compte-utilisateur* | Sélectionnez votre compte d’utilisateur. | 
   | **Nom de l’application web** | *nom-application-api-web-personnalisée* ou le nom par défaut | Entrez le nom de votre application API Web, qui est utilisé dans l’URL de votre application, par exemple : http://*nom-application-api-web*. | 
   | **Abonnement** | *nom-abonnement-Azure* | Sélectionnez l’abonnement Azure que vous souhaitez utiliser. | 
   | **Groupe de ressources** | *nom-groupe-de-ressources-Azure* | Sélectionnez un groupe de ressources Azure existant ou, si vous n’en avez pas, créez-en-un. <p>**Remarque** : Un groupe de ressources Azure organise les ressources Azure dans un abonnement Azure. | 
   | **Plan App Service** | *nom-plan-App-Service* | Sélectionnez un plan App Service existant, ou si vous n’en avez pas, créez-en-un. | 
   |||| 

   Si vous créez un plan App Service, spécifiez ces paramètres :

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Emplacement** | *région-déploiement* | Sélectionnez la région de déploiement de votre application. | 
   | **Taille** | *taille-plan-App-Service* | Sélectionnez la taille de votre plan, qui détermine le coût et la capacité en ressources de calcul de votre plan de service. | 
   |||| 

   Pour configurer les autres ressources requises par votre application, cliquez sur **Explorer d’autres services Azure**.

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Type de ressource** | *type-ressource-Azure* | Le cas échéant, sélectionnez et configurez les ressources supplémentaires requises par votre application. | 
   |||| 

6. Une fois que Visual Studio a déployé votre projet, générez le code de votre application.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Créer un fichier OpenAPI (Swagger) qui décrit votre API Web

Pour connecter votre application API Web à Logic Apps, vous avez besoin d’un [fichier OpenAPI (anciennement Swagger)](http://swagger.io/) qui décrit les opérations de votre API. Vous pouvez écrire votre propre définition OpenAPI pour votre API avec [l’éditeur en ligne de Swagger](http://editor.swagger.io/), mais ce didacticiel utilise un outil open source nommé [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Si ce n’est pas encore fait, installez le package NuGet Swashbuckle dans votre projet Visual Studio.

   1. Dans Visual Studio, cliquez sur **Outils ** > **Gestionnaire de package NuGet** > 
    **Console du gestionnaire de package**.

   2. Dans la **Console du gestionnaire de package**, accédez au répertoire de projet de votre application si vous y n'êtes pas déjà (exécutez `Set-Location "project-path"`), et exécuter cette cmdlet PowerShell : 
   
      `Install-Package Swashbuckle`

      Par exemple :

      ![Console du gestionnaire de package, installez Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Si vous exécutez votre application après avoir installé Swashbuckle, celui-ci génère un fichier OpenAPI à cette URL : 
   >
   > http://*{URL-racine-application-API-web}*/swagger/docs/v1
   > 
   > Swashbuckle génère également une interface utilisateur à cette URL : 
   > 
   > http://*{URL-racine-application-API-web}*/swagger

3. Dès que possible, publiez votre application API Web sur Azure. Pour effectuer la publication à partir de Visual Studio, cliquez avec le bouton droit sur votre projet Web dans l’Explorateur de solutions, sélectionnez **Publier...** et suivez les invites.

   > [!IMPORTANT]
   > Les doublons d’ID d’opérations rendent un document OpenAPI non valide. Si vous avez utilisé l’exemple de modèle C#, le modèle *répète deux fois cet ID d’opération* : `Values_Get`. 
   > 
   > Pour résoudre ce problème, transformez une instance en `Value_Get` et relancez la publication.

4. Récupérez le document OpenAPI en accédant à cet emplacement : 

   http://*{URL-racine-application-API-web}*/swagger/docs/v1

   Vous pouvez également télécharger un [exemple de document OpenAPI](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) dans ce didacticiel. 
   Veillez à supprimer les commentaires, qui commencent par « // », avant d’utiliser le document.

5. Enregistrez le contenu sous forme de fichier JSON. En fonction de votre navigateur, vous devrez peut-être copier et coller le texte dans un fichier texte vide.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer l’authentification pour les connecteurs personnalisés](../logic-apps/custom-connector-azure-active-directory-authentication.md)











