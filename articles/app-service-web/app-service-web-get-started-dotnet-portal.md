---
title: "Déployer une application web Umbraco dans le portail Azure en cinq minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant un exemple d’application ASP.NET. Consultez immédiatement les résultats."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.lasthandoff: 04/06/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Déployer une application web Umbraco dans le portail Azure en cinq minutes

Dans ce didacticiel, vous découvrirez comment déployer une application web [Umbraco](https://our.umbraco.org/) dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) en quelques minutes.

![Application Umbraco](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Composants requis
Vous devez avoir un compte Microsoft Azure. Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Vous pouvez [essayer App Service](https://azure.microsoft.com/try/app-service/) sans compte Azure. Créez une application de base et expérimentez-la pendant une heure, sans carte de paiement et sans engagement.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Déployer l’application ASP.NET
1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Ouvrez [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Ce lien est un raccourci permettant de configurer immédiatement une application Umbraco dans le portail Azure.

3. Sous **Nom de l’application**, entrez un nom d’application web. Si le nom est unique dans le domaine `azurewebsites.net`, une coche verte s’affiche dans la case.
   
5. Sous **Groupe de ressources**, cliquez sur **Créer** pour créer un nouveau [groupe de ressources](../azure-resource-manager/resource-group-overview.md), puis donnez-lui un nom.

7. Cliquez sur **Plan App Service/Emplacement** > **Créer**. Configurez le [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) comme indiqué :

    - Sous **Plan App Service**, entrez le nom souhaité.
    - Sous **Emplacement**, choisissez un emplacement pour héberger votre plan.
    - Cliquez sur **Niveau de tarification**, puis sélectionnez **F1 gratuit** ou un autre niveau qui vous convient, puis cliquez sur **Sélectionner**.
    - Cliquez sur **OK**.

    Votre configuration Umbraco CMS doit maintenant ressembler à la capture d’écran suivante :

    ![Configuration en cours - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Cliquez sur **SQL Database** > **Créer une base de données**. Configurez la base de données SQL comme indiqué :

    - Sous **Nom**, entrez un nom, tel que **myDB**.
    - Cliquez sur **Niveau de tarification**, puis sélectionnez **F gratuit** ou un autre niveau qui vous convient, puis cliquez sur **Sélectionner**.
    - Cliquez sur **Serveur cible** > **Créer un serveur**. Configurez le serveur de base de données comme indiqué :

        - Sous **Nom du serveur**, entrez un nom pour le serveur. Si le nom est unique dans le domaine `.database.windows.net`, une coche verte s’affiche dans la case.
        - Dans **Connexion d’administrateur serveur**, entrez le nom d’utilisateur administrateur de votre choix.
        - Sous **Mot de passe** et **Confirmer le mot de passe**, entrez le mot de passe de votre choix.
        - Sous Emplacement, sélectionnez le même emplacement que celui utilisé pour l’application web.
        - Vérifiez que l’option **Autoriser les services Azure à accéder au serveur** est sélectionnée.
        - Cliquez sur **Sélectionner**.
    
    - Cliquez sur **Sélectionner**.

13. Cliquez sur **Paramètres de l’application Web**, spécifiez le nom et le mot de passe utilisateur de la base de données, puis cliquez sur **OK**.

    Votre configuration Umbraco CMS doit maintenant ressembler à la capture d’écran suivante :

    ![Configuration terminée - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Cliquez sur **Create**.
    
    À présent, Azure crée votre application Umbraco en fonction de votre configuration. Vous devriez voir le message **Le déploiement a commencé**.

    ![Déploiement réussi - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Lancer et gérer votre application web Umbraco

Une autre notification s’affiche quand Azure termine le déploiement de l’application.

![Déploiement réussi - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Cliquez sur la notification. Si vous l’avez manqué, vous pouvez toujours y accéder en cliquant sur la cloche de notification (![Cloche de notification - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Vous devriez maintenant voir le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) de gestion de votre application web (*panneau* : page de portail qui s’ouvre horizontalement).

3. Dans la partie supérieure de la page Vue d’ensemble, cliquez sur **Parcourir**.
   
    ![Parcourir - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/browse.png)

    À présent, la page **Bienvenue** d’Umbraco s’affiche. Configurez l’installation d’Umbraco et commencez à l’utiliser !

    ![Configuration Umbraco - première configuration Umbraco dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Étapes suivantes
* [Déployer une application web ASP.NET dans Azure App Service, à l’aide de Visual Studio](app-service-web-get-started-dotnet.md) - Apprenez à créer une application web Azure dans Visual Studio, à l’aide de l’un des modèles d’application inclus.
* [Déployer votre code dans Azure App Service](web-sites-deploy.md) - Apprenez à déployer à partir de FTP ou de référentiels de contrôle source.
* [Ajouter des fonctionnalités à votre première application web](app-service-web-get-started-2.md) - Faites passer votre application Azure à la vitesse supérieure. Authentifiez vos utilisateurs. Faites évoluer sa capacité en fonction de la demande. Configurez des alertes de performance. Tout cela en seulement quelques clics.

