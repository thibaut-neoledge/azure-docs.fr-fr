---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>Créer un compte Azure Cosmos DB avec l’API MongoDB
Les bases de données Azure Cosmos DB peuvent désormais être utilisées comme magasins de données pour les applications écrites pour MongoDB. Pour utiliser cette fonctionnalité, vous avez besoin d’un compte Azure et d’un compte Azure Cosmos DB. Ce didacticiel vous guide dans le processus de création d’un compte Azure Cosmos DB à utiliser avec les applications de MongoDB. 

Vous pouvez créer un compte Azure Cosmos DB avec prise en charge du compte MongoDB avec le portail Azure ou avec l’interface de ligne de commande Azure avec les modèles Azure Resource Manager. Cet article décrit la procédure de création d’un compte Azure Cosmos DB avec prise en charge du compte MongoDB à l’aide du portail Azure. Pour créer un compte à l’aide de l’interface de ligne de commande Azure avec Azure Resource Manager, consultez l’article [Automatiser la gestion des comptes Azure Cosmos DB à l’aide d’Azure CLI 2.0](documentdb-automation-resource-manager-cli.md).

## <a name="prerequisite"></a>Configuration requise
Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) dès maintenant.
## <a name="create-an-azure-cosmos-db-account"></a>Créer un compte Azure Cosmos DB

1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation gauche, cliquez sur **Azure Cosmos DB**.

    ![Capture d’écran du volet de navigation gauche du portail, avec mise en surbrillance de l'entrée DocumentDB NoSQL](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Sinon, cliquez sur **Plus de services >**, tapez **DocumentDB** dans la barre de recherche supérieure, puis cliquez sur **Azure Cosmos DB**.

    ![Capture d’écran du panneau Plus de services avec recherche de l'entrée NoSQL DocumentDB](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. En haut du panneau **Azure Cosmos DB**, cliquez sur **+ Ajouter** sur la barre d’action supérieure.

    ![Capture d’écran du bouton Ajouter dans le panneau de ressources Cosmos DB](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. Dans le panneau **Azure Cosmos DB account** (Compte Azure Cosmos DB), indiquez la configuration souhaitée pour le compte.

   ![Capture d’écran du panneau du nouveau compte Azure Cosmos DB prenant en charge le protocole MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - Dans la zone **ID**, entrez un nom pour identifier le compte.  Lorsque **l’ID** est validé, une coche verte s’affiche dans la case **ID**. La valeur de **ID** devient le nom d’hôte dans l’URI. Cet **ID** ne peut contenir que des minuscules, des chiffres, le caractère « - » et doit compter entre 3 et 50 caractères. Notez que *documents.azure.com* sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte.

    - Pour **API**, sélectionnez **MongoDB**. Cette opération spécifie l’API de communication que vous souhaitez utiliser pour interagir avec votre base de données Azure Cosmos DB.

    - Dans **Abonnement**, sélectionnez l’abonnement Azure que vous voulez utiliser pour le compte. Si votre compte ne comporte qu’un seul abonnement, ce compte sera sélectionné par défaut.

    - Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour le compte.  Un groupe de ressources déjà attaché à l’abonnement Azure est sélectionné par défaut.  Vous pouvez cependant choisir de sélectionner un nouveau groupe de ressources auquel ajouter le compte. Pour plus d’informations, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md).

    - Utilisez **Emplacement** pour indiquer l’emplacement géographique où héberger le compte.

6. Une fois les options du nouveau compte configurées, cliquez sur **Créer**.  La création du compte peut prendre plusieurs minutes.

   Vous pouvez surveiller l’avancement depuis le hub de notifications.  

   ![Capture d’écran du hub de notifications montrant la création du compte Azure Cosmos DB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Pour accéder à votre nouveau compte, cliquez sur **Azure Cosmos DB** dans le menu de gauche. Dans la liste des comptes habituels de prise en charge des protocoles DocumentDB et DocumentDB avec Mongo, cliquez sur le nom de votre nouveau compte.
8. Votre compte Azure Cosmos DB est maintenant prêt à être utilisé avec votre application MongoDB.

   ![Capture d’écran du panneau de compte par défaut](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment vous [connecter](documentdb-connect-mongodb-account.md) à un compte DocumentDB prenant en charge le protocole MongoDB.

