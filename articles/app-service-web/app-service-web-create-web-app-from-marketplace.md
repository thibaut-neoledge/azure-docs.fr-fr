---
title: "Créer une application web à partir d’Azure Marketplace | Microsoft Docs"
description: "Apprenez à créer une nouvelle application web WordPress à partir d’Azure Marketplace à l’aide du Portail Azure."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: a04c7129cd2e16c129f3e4b8e8e40f76ff37114d


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Créer une application web à partir d’Azure Marketplace
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace met à votre disposition une large gamme d’applications web populaires, développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Par exemple, WordPress, Umbraco CMS, Drupal, etc. Ces applications web sont basées sur un large éventail d’infrastructures répandues, notamment [PHP] dans cet exemple WordPress, [.NET], [Node.js], [Java] et [Python], pour en citer quelques-unes. Pour créer une application web à partir d’Azure Marketplace, le seul logiciel nécessaire est le navigateur que vous utilisez pour le [portail Azure].

Ce didacticiel vous montre comment effectuer les opérations suivantes :

* Rechercher et créer l’application web dans Azure App Service, basée sur un modèle Azure Marketplace.
* Configurer les paramètres d’Azure App Service pour la nouvelle application web.
* Lancer et gérer votre application web.

Pour les besoins de ce didacticiel, vous allez déployer un blog WordPress à partir d’Azure Marketplace. Lorsque vous aurez terminé les étapes de ce didacticiel, vous disposerez de votre propre site WordPress installé et configuré dans le cloud.

![Example de tableau de bord d’application web WordPress][WordPressDashboard1]

Le site WordPress que vous allez déployer dans le cadre de ce didacticiel utilise MySQL pour la base de données. Si vous souhaitez utiliser à la place Base de données SQL pour la base de données, consultez [Project Nami], qui est également disponible via Azure Marketplace.

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez [activer les avantages de votre abonnement Visual Studio][activate] ou [obtenir un essai gratuit][free trial].
> 
> Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service]. Là, vous pouvez créer immédiatement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ni aucun engagement.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Rechercher et créer une application web dans Azure App Service
1. Connectez-vous au [portail Azure].
2. Cliquez sur **Nouveau**.
   
    ![Créer une nouvelle ressource Azure][MarketplaceStart]
3. Recherchez **WordPress**, puis cliquez sur **WordPress**. Si vous souhaitez utiliser Base de données SQL au lieu de MySQL, recherchez **Project Nami**.
   
    ![Rechercher WordPress dans le Marketplace][MarketplaceSearch]
4. Après avoir lu la description de l’application WordPress, cliquez sur **Créer**.
   
    ![Créer une application web WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configurer les paramètres d’Azure App Service pour votre nouvelle application web
1. Une fois l’application web créée, le panneau des paramètres WordPress s’affiche et vous permet d’effectuer les opérations suivantes :
   
    ![Configurer les paramètres d’une application web WordPress][ConfigStart]
2. Entrez un nom pour l’application web dans la zone **Application web** .
   
    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera *{nom}*.azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.
   
    ![Configurer le nom d’une application web WordPress][ConfigAppName]
3. Si vous avez plusieurs abonnements, choisissez celui que vous souhaitez utiliser.
   
    ![Configurer l’abonnement de l’application web][ConfigSubscription]
4. Sélectionnez un **Groupe de ressources** ou créez-en un.
   
    Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager][ResourceGroups].
   
    ![Configurer le groupe de ressources de l’application web][ConfigResourceGroup]
5. Sélectionnez un **plan App Service/emplacement** ou créez-en un.
   
    Pour plus d’informations sur les plans App Service, consultez [Présentation des plans d’Azure App Service][AzureAppServicePlans].
   
    ![Configurer le plan de service de l’application web][ConfigServicePlan]
6. Cliquez sur **Base de données** puis, dans le panneau **Nouvelle base de données MySQL**, indiquez les valeurs requises pour configurer votre base de données MySQL.
   
    a. Entrez un nouveau nom ou conservez le nom par défaut.
   
    b. Laissez le **Type de base de données** défini sur la valeur **Partagé**.
   
    c. Choisissez le même emplacement que celui choisi pour l’application web.
   
    d. Sélectionnez un niveau tarifaire. **Mercure** (gratuit avec connexions et espace disque minimum) convient parfaitement pour ce didacticiel.
   
    e. Dans le panneau **Nouvelle base de données MySQL**, acceptez les mentions légales, puis cliquez sur **OK**.
   
    ![Configurer les paramètres de base de données de l’application web][ConfigDatabase]
7. Dans le panneau **WordPress**, acceptez les mentions légales, puis cliquez sur **Créer**.
   
    ![Terminer les paramètres de l’application web et cliquer sur OK][ConfigFinished]
   
    Généralement, Azure App Service crée l’application web en moins d’une minute. Vous pouvez surveiller la progression en cliquant sur l’icône en forme de cloche en haut de la page du portail.
   
    ![Indicateur de progression][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Lancer et gérer l’application web WordPress
1. Une fois la création de l’application web terminée, dans le portail Azure, accédez au groupe de ressources dans lequel vous avez créé l’application ; vous pourrez voir l’application web et la base de données.
   
    La ressource supplémentaire dotée de l’icône en forme d’ampoule est [Application Insights][ApplicationInsights], qui fournit des services de surveillance pour votre application web.
2. Dans le panneau **Groupe de ressources** , cliquez sur la ligne de l’application web.
   
    ![Sélectionner votre application web WordPress][WordPressSelect]
3. Dans le panneau Application Web, cliquez sur **Parcourir**.
   
    ![Accéder à votre application web WordPress][WordPressBrowse]
4. Si vous êtes invité à sélectionner la langue de votre blog WordPress, sélectionnez-la, puis cliquez sur **Continuer**.
   
    ![Configurer la langue de votre application web WordPress][WordPressLanguage]
5. Sur la page de **bienvenue** WordPress, entrez les informations de configuration requises par WordPress, puis cliquez sur **Install WordPress**.
   
    ![Configurer les paramètres de votre application web WordPress][WordPressConfigure]
6. Connectez-vous en utilisant les informations d’identification créées sur la page de **bienvenue** .  
7. La page Tableau de bord de votre site s’ouvre et affiche les informations que vous avez fournies.    
   
    ![Afficher votre tableau de bord WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer et déployer un exemple d’application web à partir d’Azure Marketplace.

Pour plus d’informations sur l’utilisation d’App Service Web Apps, consultez les liens sur le côté gauche de la page (pour les grandes fenêtres de navigateur) ou en haut de la page (pour les fenêtres de navigateur étroites).

Pour plus d’informations sur le développement d’applications web WordPress sur Azure, consultez [Développement de WordPress sur Azure App Service][WordPressOnAzure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Essayer App Service]: https://azure.microsoft.com/try/app-service/
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[portail Azure]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Feb17_HO3-->


