---
title: "Déployer une application web WordPress dans le portail Azure en cinq minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant une application WordPress. Consultez immédiatement les résultats."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/13/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>Déployer une application web WordPress dans le portail Azure en cinq minutes

Ce didacticiel vous explique comment déployer votre première application web [WordPress](https://wordpress.org/) dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) en quelques minutes.

![Site WordPress](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Composants requis
Vous devez avoir un compte Microsoft Azure. Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Vous pouvez [essayer App Service](https://azure.microsoft.com/try/app-service/) sans compte Azure. Créez une application de base et expérimentez-la pendant une heure, sans carte de paiement et sans engagement.
> 
> 

## <a name="deploy-the-wordpress-app"></a>Déployer l’application WordPress
1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Ouvrez [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress).

    Ce lien est un raccourci permettant de configurer immédiatement une application WordPress dans le portail Azure.

3. Sous **Nom de l’application**, entrez un nom d’application web. Si le nom est unique dans le domaine `azurewebsites.net`, une coche verte s’affiche dans la case.
   
5. Sous **Groupe de ressources**, cliquez sur **Créer** pour créer un nouveau [groupe de ressources](../azure-resource-manager/resource-group-overview.md), puis donnez-lui un nom.

6. Sous **Fournisseur de base de données**, sélectionnez **CleaDB**.

7. Cliquez sur **Plan App Service/Emplacement** > **Créer**. Configurez le [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) comme indiqué :

    - Sous **Plan App Service**, entrez le nom souhaité.
    - Sous **Emplacement**, choisissez un emplacement pour héberger votre plan.
    - Cliquez sur **Niveau de tarification**, puis sélectionnez **F1 gratuit** ou un autre niveau qui vous convient, puis cliquez sur **Sélectionner**.
    - Cliquez sur **OK**.

8. Cliquez sur **Base de données** > **Créer**. Configurez la base de données SQL comme indiqué :

    - Sous **Nom de la base de données**, entrez un nom pour la base de données. 
    - Sous **Emplacement**, choisissez le même emplacement que celui du plan App Service.
    - Cliquez sur **Niveau de tarification**, puis sélectionnez **Mercury** ou un autre niveau qui vous convient, puis cliquez sur **Sélectionner**.
    - Cliquez sur **Mentions légales** et sur **Acheter**.
    - Cliquez sur **OK**.

9. Cliquez sur **Create**.

    À présent, Azure crée votre application WordPress en fonction de votre configuration. Vous devriez voir le message **Le déploiement a commencé**.

    ![Déploiement commencé - première configuration WordPress dans Azure App Service](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>Lancer et gérer l’application web WordPress

Une autre notification s’affiche quand Azure termine le déploiement de l’application.

![Déploiement réussi - première configuration WordPress dans Azure App Service](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Cliquez sur la notification. Si vous l’avez manqué, vous pouvez toujours y accéder en cliquant sur la cloche de notification (![Cloche de notification - première configuration WordPress dans Azure App Service](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Vous devriez maintenant voir le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) de gestion de votre application web (*panneau* : page de portail qui s’ouvre horizontalement).

3. Dans la partie supérieure de la page Vue d’ensemble, cliquez sur **Parcourir**.
   
    ![Parcourir - première configuration WordPress dans Azure App Service](./media/app-service-web-get-started-php-portal/browse.png)

    À présent, la page **Bienvenue** de WordPress s’affiche. Configurez l’installation de WordPress et commencez à l’utiliser !

    ![Configuration de WordPress - première configuration WordPress dans Azure App Service](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Étapes suivantes
* [Créer, configurer et déployer une application web Laravel dans Azure](app-service-web-php-get-started.md) - Découvrez les compétences de base nécessaires pour exécuter n’importe quelle application web PHP dans Azure, par exemple :

    * Créer et configurer des applications dans Azure depuis PowerShell/Bash
    * Définir la version PHP
    * Utiliser un fichier de démarrage qui ne se trouve pas dans le répertoire de l’application racine
    * Activer l’automatisation de Composer
    * Accéder à des variables propres à l’environnement
    * Résoudre les problèmes courants

* [Déployer votre code dans Azure App Service](web-sites-deploy.md) - Apprenez à déployer à partir de FTP ou de référentiels de contrôle source.
* [Ajouter des fonctionnalités à votre première application web](app-service-web-get-started-2.md) - Faites passer votre application Azure à la vitesse supérieure. Authentifiez vos utilisateurs. Faites évoluer sa capacité en fonction de la demande. Configurez des alertes de performance. Tout cela en seulement quelques clics.

