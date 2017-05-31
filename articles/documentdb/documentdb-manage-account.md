---
title: "Gestion d’un compte Azure Cosmos DB via le portail Azure | Microsoft Docs"
description: "Apprenez à gérer votre compte Azure Cosmos DB via le portail Azure. Trouvez un guide vous expliquant comment utiliser le portail Azure pour afficher, copier, supprimer et accéder aux comptes."
keywords: Portail Azure, documentdb, azure, Microsoft azure
services: cosmosdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 59c2a0fb05809233cf789feb81189a24a18484b9
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Comment gérer un compte Azure Cosmos DB
Découvrez comment définir la cohérence globale, utiliser les clés et supprimer un compte Azure Cosmos DB dans le portail Azure.

## <a id="consistency"></a>Gérer les paramètres de cohérence Azure Cosmos DB
La sélection du niveau de cohérence adéquat dépend de la sémantique de votre application. Vous devez vous familiariser avec les niveaux de cohérence disponibles dans Azure Cosmos DB en lisant l’article relatif à [l’utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans Azure Cosmos DB][consistency]. Azure Cosmos DB fournit les garanties de cohérence, de disponibilité et de performance, à tous les niveaux de cohérence disponibles pour votre compte de base de données. La configuration de votre compte de base de données avec un niveau de cohérence Fort nécessite que vos données se limitent à une seule région Azure et ne soient pas disponibles mondialement. En revanche, les niveaux de cohérence souples (Obsolescence limitée, Session ou Éventuel) vous permettent d’associer autant de régions Azure que vous le souhaitez à votre compte de base de données. Les étapes simples suivantes vous montrent comment sélectionner le niveau de cohérence par défaut pour votre compte de base de données. 

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Pour spécifier le niveau de cohérence par défaut d’un compte Azure Cosmos DB
1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte Azure Cosmos DB.
2. Dans le panneau du compte, cliquez sur **Cohérence par défaut**.
3. Dans le panneau **Cohérence par défaut**, sélectionnez le nouveau niveau de cohérence et cliquez sur **Enregistrer**.
    ![Cohérence par défaut Session][5]

## <a id="keys"></a>Affichage, copie et régénération des clés d’accès
Lorsque vous créez un compte Azure Cosmos DB, le service génère deux clés d’accès maître qui peuvent être utilisées pour l’authentification lors de l’accès au compte Azure Cosmos DB. En fournissant deux clés d'accès, Azure Cosmos DB vous permet de régénérer les clés sans interruption de votre compte Azure Cosmos DB. 

Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Clés** à partir du menu de ressources dans le panneau **Compte Azure Cosmos DB** pour afficher, copier et régénérer les clés d’accès à votre compte Azure Cosmos DB.

![Capture d’écran du portail Azure, panneau Clés](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> Le panneau **Clés** inclut également des chaînes de connexion primaire et secondaire qui peuvent être utilisées pour la connexion à votre compte à partir de [l’outil de migration de données](documentdb-import-data.md).
> 
> 

Des clés en lecture seule sont également disponibles sur ce panneau. Les lectures et les demandes sont des opérations en lecture seule, contrairement aux opérations de création, de suppression et de remplacement.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copie d’une touche d’accès rapide dans le portail Azure
Dans le panneau **Clés**, cliquez sur le bouton **Copier** à droite de la clé que vous souhaitez copier.

![Affichage et copie d’une touche d’accès rapide dans le portail Azure, panneau Clés](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Régénération de clés d'accès
Vous devez modifier périodiquement les clés d'accès à votre compte Azure Cosmos DB pour garantir la sécurité des connexions. Deux clés d’accès vous sont affectées afin de vous permettre de conserver des connexions au compte Azure Cosmos DB à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

> [!WARNING]
> La régénération de vos clés d'accès affecte toutes les applications qui dépendent de la clé actuelle. Tous les clients qui utilisent la clé d’accès pour accéder au compte Azure Cosmos DB doivent être mis à jour pour utiliser la nouvelle clé.
> 
> 

Si certains de vos services cloud ou applications utilisent le compte Azure Cosmos DB, vous perdrez les connexions en régénérant les clés, sauf si vous remplacez vos clés. Les étapes suivantes décrivent le processus de remplacement de vos clés.

1. Mettez à jour la clé d’accès dans le code de votre application afin de référencer la clé d’accès secondaire du compte Azure Cosmos DB.
2. Régénérez la clé d’accès principale de votre compte Azure Cosmos DB. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte Azure Cosmos DB.
3. Dans le panneau **Compte Azure Cosmos DB**, cliquez sur **Clés**.
4. Dans le panneau **Clés**, cliquez sur le bouton Régénérer, puis sur **OK** pour confirmer que vous souhaitez générer une nouvelle clé.
    ![Régénération des clés d’accès](./media/documentdb-manage-account/regenerate-keys.png)
5. Une fois que vous avez vérifié que la nouvelle clé peut être utilisée(environ 5 minutes après la régénération), mettez à jour la clé d'accès dans le code de votre application afin de référencer la nouvelle clé d'accès primaire.
6. Régénérez la clé d’accès secondaire.
   
    ![Régénération de clés d'accès](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Il faut parfois attendre plusieurs minutes avant de pouvoir utiliser une clé qui vient d’être générée pour accéder à votre compte Azure Cosmos DB.
> 
> 

## <a name="get-the--connection-string"></a>Obtention de la chaîne de connexion
Procédez comme suit pour récupérer votre chaîne de connexion : 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte Azure Cosmos DB.
2. Dans le menu de ressources, cliquez sur **Clés**.
3. Cliquez sur le bouton **Copier** situé en regard de la case **Chaîne de connexion principale** ou de la case **Chaîne de connexion secondaire**. 

Si vous utilisez la chaîne de connexion dans [l’outil de migration de base de données Azure Cosmos DB](documentdb-import-data.md), ajoutez le nom de la base de données à la fin de la chaîne de connexion. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Supprimer un compte Azure Cosmos DB
Pour supprimer du portail Azure un compte Azure Cosmos DB dont vous ne vous servez plus, cliquez avec le bouton droit sur le nom du compte, puis cliquez sur **Supprimer le compte**.

![Comment supprimer un compte Azure Cosmos DB dans le portail Azure](./media/documentdb-manage-account/deleteaccount.png)

1. Dans le [portail Azure](https://portal.azure.com/), accédez au compte Azure Cosmos DB à supprimer.
2. Dans le panneau **Compte Azure Cosmos DB**, cliquez avec le bouton droit sur le compte, puis cliquez sur **Supprimer le compte**. 
3. Dans le volet de confirmation qui s'affiche, entrez le nom du compte Azure Cosmos DB pour confirmer que vous souhaitez le supprimer.
4. Cliquez sur le bouton **Supprimer** .

![Comment supprimer un compte Azure Cosmos DB dans le portail Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a id="next"></a>Étapes suivantes
Découvrez comment [prendre en main votre compte Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

