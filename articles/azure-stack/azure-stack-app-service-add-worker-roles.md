---
title: "Augmenter la taille des instances des rôles de travail dans App Services - Azure Stack  | Microsoft Docs"
description: "Instructions détaillées pour la mise à l’échelle d’App Services Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: dbb6d956a615482e42745296c94b2c07c0086653
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="app-service-on-azure-stack-add-more-worker-roles"></a>App Service sur Azure Stack : ajouter davantage de rôles de travail

Ce document fournit des instructions sur la mise à l’échelle d’App Service sur les rôles de travail Azure Stack. Il contient les étapes de création de rôles de travail supplémentaires pour prendre en charge des applications de n’importe quelle taille.

> [!NOTE]
> Si votre environnement POC Azure Stack n’a pas plus de 96 Go de RAM, l’ajout de capacité supplémentaire pourrait se révéler difficile.

App Service sur Azure Stack, par défaut, prend en charge les niveaux Worker gratuits et partagés. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

Si vous n’êtes pas sûr de ce qui a été déployé avec l’installation App Service sur Azure Stack par défaut, vous pouvez consulter des informations supplémentaires dans la [vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).

Azure App Service sur Azure Stack déploie tous les rôles à l’aide de groupes de machines virtuelles identiques et en tant que tel tire parti des fonctionnalités de mise à l’échelle de cette charge de travail. Par conséquent, toute la mise à l’échelle des niveaux Worker s’effectue via l’administrateur App Service.

Ajouter des Workers supplémentaires directement à partir de l’administrateur du fournisseur de ressources App Service.

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur du service.
2. Parcourir **App Services**.

  ![](media/azure-stack-app-service-add-worker-roles/image01.png)
3. Cliquez sur **Rôles**. La répartition de tous les rôles App Service déployés s’affiche ici.
4. Cliquez sur **Mettre à l’échelle les instances**.

  ![](media/azure-stack-app-service-add-worker-roles/image02.png)
5. Sous **Mettre à l’échelle les instances** :
    1. Sélectionnez le **Type de rôle**. Dans cette préversion, cette option est limitée à Web Worker.
    2. Sélectionnez le **niveau Worker** dans lequel vous voulez déployer ce Worker. Les choix par défaut sont Petit, Moyen, Grand ou Partagé. Si vous avez créé vos propres niveaux Worker, ceux-ci sont également disponibles pour la sélection.
    3. Choisissez le nombre **d’instances de rôle** supplémentaires à ajouter.
    4. Cliquez sur **OK** pour déployer les Workers supplémentaires.
    ![](media/azure-stack-app-service-add-worker-roles/image03.png)
6. App Service sur Azure Stack ajoute ensuite les machines virtuelles supplémentaires, les configure, installe tous les logiciels requis et les marque comme Prêt lorsque ce processus est terminé. Ce processus peut prendre environ 80 minutes.
7. Vous pouvez surveiller la progression de la préparation des nouveaux Workers en les affichant dans le panneau des **rôles**.

Une fois qu’ils sont entièrement déployés et prêts, les utilisateurs peuvent déployer leur charge de travail sur les Workers. Voici un exemple des différents niveaux tarifaires disponibles par défaut. S’il n’existe aucun Worker disponible pour un niveau Worker spécifique, l’option permettant de choisir le niveau tarifaire correspondant est indisponible.
![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pour monter en charge la gestion, les rôles de serveur frontal ou serveur de publication vous devez monter en charge le groupe de machines virtuelles identiques correspondant. Nous allons ajouter la possibilité de monter en charge ces rôles via la gestion App Service dans une future version.

### <a name="next-steps"></a>Étapes suivantes

[Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)
