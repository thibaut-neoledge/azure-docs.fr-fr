---
title: "Création et connexion à une base de données MySQL dans Azure"
description: "Apprenez à utiliser le portail Azure pour créer une base de données MySQL et vous y connecter à partir d’une application web PHP dans Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Création et connexion à une base de données MySQL dans Azure
Ce tutoriel vous montre comment créer une base de données MySQL sur le [portail Azure](https://portal.azure.com) (fournisseur [ClearDB](http://www.cleardb.com/)) et s’y connecter à partir d’une application web en PHP s’exécutant dans [Azure App Service](app-service/app-service-web-overview.md).

> [!NOTE]
> Vous pouvez également créer une base de données MySQL dans le cadre d’un <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">modèle d’application Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Création d’une base de données MySQL dans le portail Azure
Pour créer une base de données MySQL dans le portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Nouveau** > **Données + Stockage** > **Base de données MySQL**.

    ![Création d’une base de données MySQL dans Azure - démarrer](./media/store-php-create-mysql-database/create-db-1-start.png)
3. Dans le [panneau](azure-portal-overview.md)Nouvelle base de données MySQL, configurez votre nouvelle base de données MySQL comme suit (*panneau*: une page du portail s’ouvre à l’horizontale) :

   * **Nom de base de données**: entrez un nom d’identification unique
   * **Abonnement**: choisissez l’abonnement à utiliser
   * **Type de base de données** : sélectionnez **Partagé** pour les niveaux économiques ou gratuits, ou **Dédié** pour obtenir des ressources dédiées.
   * **Groupe de ressources**: ajoutez la base de données MySQL à un [groupe de ressources](azure-resource-manager/resource-group-overview.md) existant ou placez-la dans un nouveau groupe. Les ressources du même groupe peuvent être facilement gérées ensemble.
   * **Emplacement**: choisissez un emplacement proche. Lorsque vous ajoutez la base de données à un groupe de ressources existant, vous êtes limité à l’emplacement du groupe de ressources.
   * **Niveau tarifaire** : cliquez sur **Niveau tarifaire**, puis sélectionnez une option de tarification (le niveau **Mercure** est gratuit), puis cliquez sur **Sélectionner**.
   * **Conditions juridiques** : cliquez sur **Conditions juridiques**, passez en revue les détails de l’achat, puis cliquez sur **Acheter**.
   * **Épingler au tableau de bord**: sélectionnez cette option si vous souhaitez bénéficier d’un accès direct depuis le tableau de bord. Cette fonctionnalité est particulièrement utile si vous n’êtes pas encore tout à fait familiarisé avec la navigation dans le portail.

     La capture d’écran suivante illustre une manière de configurer votre base de données MySQL.  
     ![Création d’une base de données MySQL dans Azure - configurer](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Une fois la configuration terminée, cliquez sur **Créer**.

    ![Création d’une base de données MySQL dans Azure - créer](./media/store-php-create-mysql-database/create-db-3-create.png)

    Une notification contextuelle vous informe que le déploiement a démarré.

    ![Création d’une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Une autre fenêtre contextuelle s’affiche une fois le déploiement réussi. Le portail ouvre aussi automatiquement le panneau de votre base de données MySQL.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Connexion à la base de données MySQL
Pour afficher les informations de connexion de votre nouvelle base de données MySQL, cliquez simplement sur **Propriétés** sur le panneau de votre application web.

![Création d'une base de données MySQL dans Azure - panneau Base de données MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Vous pouvez désormais utiliser ces informations de connexion dans n’importe quelle application web. Cliquez [ici](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)pour obtenir un exemple d’utilisation des informations de connexion à partir d’une simple application PHP.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

