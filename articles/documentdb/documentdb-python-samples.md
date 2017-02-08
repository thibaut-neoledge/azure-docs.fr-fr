---
title: Exemples Python NoSQL pour DocumentDB | Microsoft Docs
description: "Accédez à des exemples Python NoSQL sur github couvrant l’exécution de tâches courantes dans DocumentDB, notamment les opérations CRUD pour les documents JSON dans les bases de données NoSQL."
keywords: Exemples Python
services: documentdb
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: moderakh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7b555732bad08c86c33ee78344ae9c4e6af7c7b8


---
# <a name="documentdb-python-examples"></a>Exemples Python DocumentDB
> [!div class="op_single_selector"]
> * [Exemples .NET](documentdb-dotnet-samples.md)
> * [Exemples Node.js](documentdb-nodejs-samples.md)
> * [Exemples Python](documentdb-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)
> 
> 

Le référentiel GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) contient des exemples de solutions qui exécutent des opérations CRUD ainsi que d’autres opérations courantes sur les ressources Azure DocumentDB. Cet article fournit :

* Liens vers les tâches dans chacun des exemples de fichiers de projet Python. 
* Des liens vers le contenu de référence d’API connexe.

**Configuration requise**

1. Pour utiliser ces exemples Python, vous avez besoin d’un compte Azure :
   * Vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/): vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu’ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web. Votre carte de crédit ne sera pas débitée tant que vous n'aurez pas explicitement modifié vos paramètres pour demander à l'être.
     * Vous pouvez [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): ce dernier vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.
2. Vous devez également disposer du [Kit de développement logiciel (SDK) Python](documentdb-sdk-python.md). 
   
   > [!NOTE]
   > Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples transmettent donc plusieurs appels à [document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). À chaque appel, votre abonnement sera facturé pour 1 heure d’utilisation selon le niveau de performances de la collection en cours de création. 
   > 
   > 

## <a name="database-examples"></a>Exemples de base de données
Le fichier [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) explique comment effectuer les tâches suivantes.

| Task | Informations de référence sur l'API |
| --- | --- |
| [Créer une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Demander un compte pour une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Lire une base de données par identifiant](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Répertorier les bases de données pour un compte](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Supprimer une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Exemples de collection
Le fichier [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) du projet [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) explique comment effectuer les tâches suivantes.

| Task | Informations de référence sur l'API |
| --- | --- |
| [Création d'une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Lire une liste de toutes les collections d’une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) |[document_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Obtenir une collection par identifiant](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Obtenir le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) |[DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Modifier le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) |[document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Supprimer une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |




<!--HONumber=Nov16_HO3-->


