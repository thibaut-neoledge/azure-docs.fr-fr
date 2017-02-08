---
title: "Gestion d’un compte DocumentDB via le portail Azure | Microsoft Docs"
description: "Apprenez à gérer un compte DocumentDB via le portail Azure. Trouvez un guide vous expliquant comment utiliser le portail Azure pour afficher, copier, supprimer et accéder aux comptes."
keywords: Portail Azure, documentdb, azure, Microsoft azure
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f38b5d6127897ba2b083e0f3bb41562650214ae9


---
# <a name="how-to-manage-a-documentdb-account"></a>Gestion d'un compte DocumentDB
Découvrez comment définir la cohérence globale, utiliser les clés et supprimer un compte DocumentDB dans le portail Azure.

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>Gestion des paramètres de cohérence DocumentDB
La sélection du niveau de cohérence adéquat dépend de la sémantique de votre application. Vous devez vous familiariser avec les niveaux de cohérence disponibles dans DocumentDB en lisant [Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB][consistency]. DocumentDB fournit les garanties de cohérence, de disponibilité et de performance, à tous les niveaux de cohérence disponibles pour votre compte de base de données. La configuration de votre compte de base de données avec un niveau de cohérence Fort nécessite que vos données se limitent à une seule région Azure et ne soient pas disponibles mondialement. En revanche, les niveaux de cohérence souples (Obsolescence limitée, Session ou Éventuel) vous permettent d’associer autant de régions Azure que vous le souhaitez à votre compte de base de données. Les étapes simples suivantes vous montrent comment sélectionner le niveau de cohérence par défaut pour votre compte de base de données. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Pour spécifier le niveau de cohérence par défaut d’un compte DocumentDB
1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB.
2. Dans le panneau du compte, cliquez sur **Cohérence par défaut**.
3. Dans le panneau **Cohérence par défaut**, sélectionnez le nouveau niveau de cohérence et cliquez sur **Enregistrer**.
    ![Cohérence par défaut Session][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>Affichage, copie et régénération des clés d’accès
Lorsque vous créez un compte DocumentDB, le service génère deux clés d'accès maître qui peuvent être utilisées pour l'authentification lors de l'accès au compte DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet de régénérer les clés sans interruption de votre compte DocumentDB. 

Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Clés** à partir du menu de ressources dans le panneau **Compte DocumentDB** pour afficher, copier et régénérer les clés d’accès à votre compte DocumentDB.

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
Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB pour garantir la sécurité des connexions. Deux clés d'accès vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

> [!WARNING]
> La régénération de vos clés d'accès affecte toutes les applications qui dépendent de la clé actuelle. Tous les clients qui utilisent la clé d'accès pour accéder au compte DocumentDB doivent être mis à jour pour utiliser la nouvelle clé.
> 
> 

Si certains de vos services cloud ou applications utilisent le compte DocumentDB, vous perdrez les connexions en régénérant les clés, sauf si vous remplacez vos clés. Les étapes suivantes décrivent le processus de remplacement de vos clés.

1. Mettez à jour la clé d'accès dans le code de votre application afin de référencer la clé d'accès secondaire du compte DocumentDB.
2. Régénérez la clé d’accès principale de votre compte de stockage. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB.
3. Dans le panneau **Compte DocumentDB**, cliquez sur **Clés**.
4. Dans le panneau **Clés**, cliquez sur le bouton Régénérer, puis sur **OK** pour confirmer que vous souhaitez générer une nouvelle clé.
    ![Régénération des clés d’accès](./media/documentdb-manage-account/regenerate-keys.png)
5. Une fois que vous avez vérifié que la nouvelle clé peut être utilisée(environ 5 minutes après la régénération), mettez à jour la clé d'accès dans le code de votre application afin de référencer la nouvelle clé d'accès primaire.
6. Régénérez la clé d’accès secondaire.
   
    ![Régénération de clés d'accès](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Il faut parfois attendre plusieurs minutes avant de pouvoir utiliser une clé qui vient d’être générée pour accéder à votre compte DocumentDB.
> 
> 

## <a name="get-the--connection-string"></a>Obtention de la chaîne de connexion
Procédez comme suit pour récupérer votre chaîne de connexion : 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte DocumentDB.
2. Dans le menu de ressources, cliquez sur **Clés**.
3. Cliquez sur le bouton **Copier** situé en regard de la case **Chaîne de connexion principale** ou de la case **Chaîne de connexion secondaire**. 

Si vous utilisez la chaîne de connexion dans [l’outil de migration de base de données DocumentDB](documentdb-import-data.md), ajoutez le nom de la base de données à la fin de la chaîne de connexion. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> Supprimer un compte DocumentDB
Pour supprimer du portail Azure un compte DocumentDB dont vous ne vous servez plus, utilisez la commande **Supprimer le compte** du panneau **Compte DocumentDB**.

![Comment supprimer un compte DocumentDB dans le portail Azure](./media/documentdb-manage-account/deleteaccount.png)

1. Dans le [portail Azure](https://portal.azure.com/), accédez au compte DocumentDB à supprimer.
2. Dans le panneau **Compte DocumentDB**, cliquez sur **Plus**, puis sur **Supprimer le compte**. Ou, cliquez avec le bouton droit sur le nom de la base de données, puis cliquez sur **Supprimer le compte**.
3. Dans le volet de confirmation qui s'affiche, entrez le nom du compte DocumentDB afin pour confirmer que vous souhaitez le supprimer.
4. Cliquez sur le bouton **Supprimer** .

![Comment supprimer un compte DocumentDB dans le portail Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>Étapes suivantes
Découvrez comment [prendre en main votre compte DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Pour en savoir plus sur DocumentDB, consultez la documentation Azure DocumentDB sur [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

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
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO3-->


