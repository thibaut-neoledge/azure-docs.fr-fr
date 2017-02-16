---
title: "Créer un compte DocumentDB à utiliser avec les applications MongoDB | Microsoft Docs"
description: "Utilisez ce didacticiel pour apprendre à créer un compte DocumentDB prenant en charge le protocole MongoDB (actuellement disponible en version préliminaire)."
keywords: mongodb apps, mongodb app
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 413f3ae1-a68e-43be-b0d7-fa2987644f3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 9b83a0878f45bf3a79d00ad77965308d95f33d28
ms.openlocfilehash: 797ee3b1209e5abf545f0614c408d94391a29c8b


---

# <a name="create-a-documentdb-account-for-use-with-mongodb-apps"></a>Créer un compte DocumentDB à utiliser avec les applications MongoDB
Les bases de données DocumentDB peuvent désormais être utilisées comme magasins de données pour les applications écrites pour MongoDB. Pour utiliser cette fonctionnalité, vous avez besoin d’un compte Azure et d'un compte DocumentDB. Ce didacticiel vous guide dans le processus de création d’un compte DocumentDB à utiliser avec les applications de MongoDB. 

Vous pouvez créer un compte DocumentDB avec prise en charge pour compte MongoDB avec le portail Azure ou avec l’interface de ligne de commande Azure (CLI) avec les modèles Azure Resource Manager. Cet article décrit la procédure de création d’une base de données DocumentDB avec prise en charge des comptes MongoDB avec le portail Azure. Pour créer un compte à l’aide d’Azure Resource Manager et de la CLI Azure, consultez l’article [Automate DocumentDB with support for MongoDB account creation (Automatisation de DocumentDB avec prise en charge de la création de comptes MongoDB)](documentdb-automation-resource-manager-cli.md#quick-create-documentdb-with-mongodb-api-account).

## <a name="prerequisite"></a>Configuration requise
Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) dès maintenant.
## <a name="create-a-documentdb-account"></a>Création d'un compte DocumentDB

1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation gauche, cliquez sur **NoSQL (DocumentDB)**.

    ![Capture d’écran du volet de navigation gauche du portail, avec mise en surbrillance de l'entrée DocumentDB NoSQL](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Sinon, cliquez sur **Plus de services >**, tapez **DocumentDB** dans la barre de recherche supérieure, puis cliquez sur **NoSQL (DocumentDB)**.

    ![Capture d’écran du panneau Plus de services avec recherche de l'entrée NoSQL DocumentDB](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. En haut du panneau**NoSQL (DocumentDB)**, cliquez sur **+ Ajouter** sur la barre d’action supérieure.

    ![Capture d’écran du bouton Ajouter dans le panneau de ressources DocumentDB NoSQL](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. Dans le panneau **Compte DocumentDB** , indiquez la configuration souhaitée pour le compte.

   ![Capture d’écran du panneau du nouveau compte DocumentDB prenant en charge le protocole MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - Dans la zone **ID**, entrez un nom pour identifier le compte.  Lorsque **l’ID** est validé, une coche verte s’affiche dans la case **ID**. La valeur de **ID** devient le nom d’hôte dans l’URI. Cet **ID** ne peut contenir que des minuscules, des chiffres, le caractère « - » et doit compter entre 3 et 50 caractères. Notez que *documents.azure.com* sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte.

    - Pour **API NoSQL**, sélectionnez **MongoDB**. Cette opération spécifie l'API de communication que vous souhaitez utiliser pour interagir avec votre base de données DocumentDB.

    - Dans **Abonnement**, sélectionnez l’abonnement Azure que vous voulez utiliser pour le compte. Si votre compte ne comporte qu’un seul abonnement, ce compte sera sélectionné par défaut.

    - Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour le compte.  Un groupe de ressources déjà attaché à l’abonnement Azure est sélectionné par défaut.  Vous pouvez cependant choisir de sélectionner un nouveau groupe de ressources auquel ajouter le compte. Pour plus d’informations, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md).

    - Utilisez **Emplacement** pour indiquer l’emplacement géographique où héberger le compte.

6. Une fois les options du nouveau compte configurées, cliquez sur **Créer**.  La création du compte peut prendre plusieurs minutes.

   Vous pouvez surveiller l’avancement depuis le hub de notifications.  

   ![Capture d’écran du hub de notifications montrant la création du compte DocumentDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Pour accéder à votre nouveau compte, cliquez sur **DocumentDB (NoSQL)** dans le menu de gauche. Dans la liste des comptes habituels de prise en charge des protocoles DocumentDB et DocumentDB avec Mongo, cliquez sur le nom de votre nouveau compte.
8. Votre compte DocumentDB est maintenant prêt à être utilisé avec votre application MongoDB.

   ![Capture d’écran du panneau de compte par défaut](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment vous [connecter](documentdb-connect-mongodb-account.md) à un compte DocumentDB prenant en charge le protocole MongoDB.



<!--HONumber=Jan17_HO2-->


