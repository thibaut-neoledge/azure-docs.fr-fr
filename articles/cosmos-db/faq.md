---
title: "Forum Aux Questions Azure Cosmos DB | Microsoft Docs"
description: "Obtenez des réponses aux questions fréquemment posées sur Azure Cosmos DB, un service de base de données multimodèle distribué globalement. Découvrez la capacité, les niveaux de performances et la mise à l’échelle."
keywords: "Questions sur la base de données, Forum aux questions, documentdb, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: e9808af4aa875c5199279825325688afc69e6de6
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="azure-cosmos-db-faq"></a>FAQ Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Notions fondamentales concernant Cosmos DB Azure
### <a name="what-is-azure-cosmos-db"></a>Qu’est-ce qu’Azure Cosmos DB ?
Azure Cosmos DB est un service de base de données multimodèle répliqué globalement, qui offre des requêtes enrichies sur des données sans schéma, aide à produire des performances configurables et fiables, et permet un développement rapide. Tout cela s’effectue via une plateforme gérée s’appuyant sur la puissance et l’étendue de Microsoft Azure. 

Azure Cosmos DB est la solution idéale pour les applications web, mobiles, de jeu et IoT lorsqu’un débit prévisible, une haute disponibilité, une faible latence et un modèle de données sans schéma sont primordiaux. Cette solution offre une flexibilité des schémas et une indexation riche. Elle inclut également une prise en charge transactionnelle multidocument avec JavaScript intégré. 

Pour plus de questions, réponses et instructions concernant les bases de données pour le déploiement et l’utilisation de ce service, voir la [Documentation Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>Qu’est devenu DocumentDB ?
L’API DocumentDB fait partie des API et des modèles de données pris en charge pour Azure Cosmos DB. De plus, Azure Cosmos DB met à votre disposition l’API Graph (préversion), l’API Table (préversion) et l’API MongoDB. Pour plus d’informations, voir [Questions des clients de DocumentDB](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Comment accéder à mon compte DocumentDB dans le portail Azure ?
Dans le volet de gauche du portail Azure, cliquez sur l’icône Azure Cosmos DB. Si vous possédiez auparavant un compte DocumentDB, vous disposez maintenant d’un compte Azure Cosmos DB, sans répercussion sur votre facturation.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quels sont les cas d’utilisation courants d’Azure Cosmos DB ?
Azure Cosmos DB est un choix judicieux pour les nouvelles applications web, mobiles, de jeu et IoT où une mise à l’échelle automatique, des performances prévisibles, des temps de réponse rapides de l’ordre d’une milliseconde et la capacité à exécuter des requêtes sur des données sans schéma sont importants. Azure Cosmos DB permet un développement rapide et prend en charge l’itération continue des modèles de données d’application. Les applications qui gèrent du contenu et des données générés par l’utilisateur sont [des cas d’utilisation courants pour Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Comment Azure Cosmos DB offre-t-il des performances prévisibles ?
Dans Azure Cosmos DB, la mesure du débit est exprimée en [unités de requête](request-units.md) (RU). Un débit de 1 RU correspond au débit de la requête GET d’un document de 1 Ko. Chaque opération dans Azure Cosmos DB, y compris les lectures, les écritures, les requêtes SQL et les exécutions de procédures stockées, comporte une valeur de RU déterministe basée sur le débit nécessaire à l’exécution de l’opération. Au lieu de penser à l’UC, à l’E/S et à la mémoire, ainsi qu’à la façon dont ces éléments affectent le débit de votre application, vous pouvez penser en termes de mesure de RU unique.

Vous pouvez réserver chaque conteneur Azure Cosmos DB avec un débit approvisionné en termes de RU de débit par seconde. Pour les applications de toute échelle, vous pouvez évaluer les requêtes individuelles pour mesurer leur valeur de RU, et approvisionner un conteneur pour gérer la totalité des unités de requête sur l’ensemble des requêtes. Vous pouvez également mettre à l’échelle le débit de votre conteneur à mesure de l’évolution des besoins de votre application. Pour plus d’informations sur les unités de requête et pour obtenir de l’aide afin de déterminer vos besoins en matière de conteneurs, voir [Estimation des besoins de débit](request-units.md#estimating-throughput-needs) et essayez la [calculatrice de débit](https://www.documentdb.com/capacityplanner). Le terme *conteneur* fait ici référence à une collection de l’API DocumentDB, à un graphique de l’API Graph, à une collection de l’API MongoDB API et à une table de l’API Table. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Comment Azure Cosmos DB prend-il en charge différents modèles de données tels que clé/valeur, en colonnes, document et graphique ?

Les modèles de données clé/valeur (table), en colonnes, document et graphique sont tous pris en charge de manière native en raison de la conception ARS (atomes, enregistrements et séquences) sur laquelle repose Azure Cosmos DB. Les atomes, enregistrements et séquences peuvent facilement être mappés et projetés vers différents modèles de données. Les API pour un sous-ensemble de modèles sont disponibles dès à présent (API DocumentDB, MongoDB, Table et Graph), et d’autres API propres à des modèles de données supplémentaires seront disponibles ultérieurement.

Azure Cosmos DB a un moteur d’indexation indépendant du point de vue du schéma capable d’indexer automatiquement toutes les données qu’il reçoit sans nécessiter de schéma ou d’index secondaire de la part du développeur. Le moteur s’appuie sur un ensemble de dispositions d’index logiques (inversée, en colonnes, arborescence) qui découplent la disposition de stockage des sous-systèmes de traitement de requêtes et d’index. Cosmos DB peut également prendre en charge un ensemble de protocoles de transmission et d’API de manière extensible, et les traduire efficacement vers le modèle de données principal (1) et les dispositions d’index logiques (2), ce qui en fait une solution unique capable de prendre en charge plusieurs modèles de données en mode natif.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB est-il conforme à la loi HIPAA ?
Oui, Azure Cosmos DB est conforme à la loi HIPAA. HIPAA établit les conditions requises pour l’utilisation, la divulgation et la protection des informations de santé identifiables de façon individuelle. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quelles sont les limites de stockage d’Azure Cosmos DB ?
Il n’existe aucune limite à la quantité totale de données qu’un conteneur peut stocker dans Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quelles sont les limites de débit d’Azure Cosmos DB ?
Il n’existe aucune limite au débit total qu’un conteneur peut prendre en charge dans Azure Cosmos DB. L’idée principale est de distribuer votre charge de travail à peu près uniformément entre un nombre suffisant de clés de partition.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Combien coûte Azure Cosmos DB ?
Pour plus d’informations, voir [Tarification d’Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Les frais d’utilisation d’Azure Cosmos DB sont déterminés par le nombre de conteneurs approvisionnés, le nombre d’heures durant lequel les conteneurs ont été en ligne et le débit approvisionné pour chaque conteneur. Le terme *conteneur* fait ici référence à une collection de l’API DocumentDB, à un graphique de l’API Graph, à une collection de l’API MongoDB API et à des tables de l’API Table. 

### <a name="is-a-free-account-available"></a>Un compte gratuit est-il disponible ?
Si vous débutez avec Azure, vous pouvez vous inscrire pour bénéficier d’un [compte Azure gratuit](https://azure.microsoft.com/free/), qui vous donne 30 jours et un crédit pour essayer tous les services Azure. Si vous avez un abonnement Visual Studio, vous pouvez aussi bénéficier de [crédits Azure gratuits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) à utiliser sur n’importe quel service Azure. 

Vous pouvez également utiliser l’[émulateur Azure Cosmos DB](local-emulator.md) pour développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur Azure Cosmos DB, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Comment puis-je obtenir une aide supplémentaire avec Azure Cosmos DB ?
Si vous avez besoin d’aide, contactez-nous sur [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) ou le [forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB), ou planifiez une conversation individuelle avec l’équipe d’ingénierie d’Azure Cosmos DB en envoyant un message à [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

## <a name="set-up-azure-cosmos-db"></a>Configurer Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Comment s’inscrire pour Azure Cosmos DB ?
Azure Cosmos DB est disponible dans le portail Azure. Tout d’abord, souscrivez un abonnement Azure. Une fois inscrit, vous pouvez ajouter un compte d’API DocumentDB, d’API Graph (préversion), d’API Table (préversion) ou d’API MongoDB à votre abonnement Azure.

### <a name="what-is-a-master-key"></a>Qu'est-ce qu’une clé principale ?
Une clé principale est un jeton de sécurité permettant d'accéder à toutes les ressources d'un compte. Les personnes disposant de cette clé ont un accès en lecture et en écriture à toutes les ressources du compte de la base de données. Soyez prudent lorsque vous distribuez des clés principales. La clé principale primaire et la clé principale secondaire sont disponibles dans le panneau **Clés** du [portail Azure][azure-portal]. Pour plus d’informations sur les clés, consultez la rubrique [Affichage, copie et régénération de clés d’accès](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quelles sont les régions configurables pour PreferredLocations ? 
La valeur de PreferredLocations peut être définie sur toute région Azure dans laquelle Cosmos DB est disponible. Pour obtenir la liste des régions disponibles, voir [Régions Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Y a-t-il quelque chose que je dois savoir concernant la distribution de données à travers le monde via les centres de données Azure ? 
Azure Cosmos DB est présent dans toutes les régions Azure, comme l’indique la page [Régions Azure](https://azure.microsoft.com/regions/). Comme il s’agit du service principal, chaque nouveau centre de données a une présence Azure Cosmos DB. 

Lorsque vous définissez une région, n’oubliez pas qu’Azure Cosmos DB respecte les clouds souverains et du secteur public. Autrement dit, si vous créez un compte dans une région souveraine, vous ne pouvez pas répliquer en dehors de cette région souveraine. De même, vous ne pouvez pas activer la réplication dans d’autres emplacements souverains à partir d’un compte externe. 

## <a name="develop-against-the-documentdb-api"></a>Développer par rapport à l’API DocumentDB

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>Comment commencer à développer par rapport l’API DocumentDB ?
L’API Microsoft DocumentDB est disponible dans le [portail Azure][azure-portal]. Vous devez d’abord souscrire un abonnement Microsoft Azure. Après avoir souscrit un abonnement Microsoft Azure, vous pouvez ajouter un conteneur d’API DocumentDB à votre abonnement Azure. Pour savoir comment ajouter un compte Azure Cosmos DB, consultez [Créer un compte de base de données Azure Cosmos DB](create-documentdb-dotnet.md#create-account). Si vous possédiez auparavant un compte DocumentDB, vous disposez maintenant d’un compte Azure Cosmos DB. 

[Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) sont disponibles pour .NET, Python, Node.js, JavaScript et Java. Les développeurs peuvent également utiliser les [API HTTP RESTful](/rest/api/documentdb/) pour interagir avec les ressources Azure Cosmos DB sur différentes plateformes et dans de nombreux langages.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Puis-je accéder à certains exemples prêts à l’emploi pour gagner du temps ?
Des exemples pour les Kits de développement logiciel (SDK) [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) et [Python](documentdb-python-samples.md) de l’API DocumentDB sont disponibles sur GitHub.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>La base de données de l’API DocumentDB prend-elle en charge les données sans schéma ?
Oui, l’API DocumentDB permet aux applications de stocker des documents JSON arbitraires sans définition ou conseil de schéma. Les données peuvent être interrogées immédiatement via l’interface de requête SQL d’Azure Cosmos DB.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>L’API DocumentDB prend-elle en charge les transactions ACID ?
Oui, l’API DocumentDB prend en charge les transactions entre documents exprimées sous forme de procédures stockées et de déclencheurs JavaScript. Les transactions sont étendues à une seule partition au sein de chaque collection, et exécutées en mode « Tout ou rien » avec des sémantiques ACID, isolément d’autres codes et requêtes utilisateur s’exécutant simultanément. Si des exceptions surviennent lors de l’exécution du code d’application JavaScript côté serveur, la transaction entière est annulée. Pour plus d’informations sur les transactions, consultez [Transactions de programme de base de données](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Qu'est-ce qu'une collection ?
Une collection consiste en un groupe de documents et la logique d’application JavaScript associée. Une collection est une entité facturable, où le [coût](performance-levels.md) est déterminé par le débit et le stockage utilisé. Des collections peuvent s’étendre à un ou plusieurs serveurs ou partitions, et peuvent être mises à l’échelle pour gérer des volumes de stockage ou de débit pratiquement illimités.

Les collections constituent également les entités de facturation d’Azure Cosmos DB. Chaque collection est facturée à l’heure, sur la base du débit approvisionné et de l’espace de stockage utilisé. Pour plus d’informations, consultez la [Tarification Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Comment créer une base de données ?
Vous pouvez créer des bases de données à l’aide du [portail Azure](https://portal.azure.com) comme décrit dans [Ajouter une collection](create-documentdb-dotnet.md#create-collection), de l’un des [Kits de développement logiciel (SDK) Azure Cosmos DB](documentdb-sdk-dotnet.md) ou d’[API REST](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Comment configurer des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et des autorisations à l’aide de l’un des [SDK d’API Cosmos DB](documentdb-sdk-dotnet.md) ou des [API REST](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>L’API DocumentDB prend-elle en charge SQL ?
Le langage de requête SQL est un sous-ensemble amélioré des fonctionnalités de requête prises en charge par SQL. Le langage de requête SQL d’Azure Cosmos DB fournit des opérateurs hiérarchiques et relationnels enrichis ainsi qu’une extensibilité par le biais de fonctions JavaScript définies par l’utilisateur. La grammaire JSON permet de modéliser des documents JSON en tant qu’arborescences comprenant des nœuds étiquetés qui sont utilisés par les techniques d’indexation automatique et le langage de requête SQL d’Azure Cosmos DB. Pour plus d’informations sur l’utilisation de la grammaire SQL, voir [QueryDocumentDB][query].

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>L’API DocumentDB prend-elle en charge les fonctions d’agrégation SQL ?
L’API DocumentDB prend en charge l’agrégation à faible latence à n’importe quelle échelle par l’intermédiaire des fonctions d’agrégation `COUNT`, `MIN`, `MAX`, `AVG` et `SUM` via la grammaire SQL. Pour plus d’informations, consultez [Fonctions d’agrégation](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>Comment l’API DocumentDB assure-t-elle l’accès concurrentiel ?
L’API DocumentDB prend en charge le contrôle d’accès concurrentiel optimiste via des étiquettes d’entité (ETag) HTTP. Chaque ressource de l’API DocumentDB est dotée d’une ETag définie sur le serveur à chaque mise à jour d’un document. L’en-tête et la valeur actuelle ETag sont inclus dans tous les messages de réponse. Les ETag peuvent être utilisées avec l’en-tête If-Match pour permettre au serveur de déterminer si une ressource nécessite une mise à jour. La valeur If-Match est la valeur ETag utilisée pour la vérification. Si la valeur ETag correspond à la valeur ETag du serveur, la ressource est mise à jour. Si l’ETag n’est plus actuelle, le serveur rejette l’opération en retournant un code de réponse « HTTP 412 Échec de la condition préalable ». Dans ce cas, le client extrait à nouveau la ressource afin d’obtenir la valeur ETag actuelle pour la ressource. En outre, les ETag peuvent être utilisées avec l’en-tête If-None-Match pour déterminer si une nouvelle extraction d’une ressource est nécessaire.

Pour utiliser l’accès concurrentiel optimiste dans .NET, utilisez la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Pour un exemple .NET, voir [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans l’exemple DocumentManagement sur github.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>Comment effectuer des transactions dans l’API DocumentDB ?
L’API DocumentDB prend en charge les transactions intégrées au langage via des procédures stockées et déclencheurs JavaScript. Toutes les opérations de base de données à l’intérieur des scripts sont exécutées en isolement de capture instantanée. S’il s’agit d’une collection à partition unique, l’exécution est étendue à la collection. Si la collection est partitionnée, l’exécution est étendue aux documents ayant la même valeur de clé de partition au sein de la collection. Une capture instantanée des versions des documents (ETags) est prise au début de la transaction et validée uniquement si le script fonctionne. Si le JavaScript renvoie une erreur, la transaction est annulée. Pour plus d’informations, consultez [Programmation en JavaScript côté serveur pour Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Comment insérer des documents en bloc dans Cosmos DB ?
Vous pouvez insérer en bloc des documents dans Azure Cosmos DB de deux manières :

* Avec l’outil de migration de données, comme décrit dans [Outil de migration de base de données pour Azure Cosmos DB](import-data.md).
* Avec des procédures stockées, comme décrit dans [Programmation en JavaScript côté serveur pour Azure Cosmos DB](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>L’API DocumentDB prend-elle en charge la mise en cache de lien de ressource ?
Oui. Azure Cosmos DB étant un service RESTful, les liens de ressource sont immuables et peuvent être mis en cache. Les clients de l’API DocumentDB peuvent spécifier un en-tête « If-None-Match » pour des lectures effectuées en comparaison avec des documents ou collections de type ressource, puis mettre à jour leurs copies locales après modification de la version du serveur.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Une instance locale de l’API DocumentDB est-elle disponible ?
Oui. L’[émulateur Azure Cosmos DB](local-emulator.md) fournit une émulation haute fidélité du service Cosmos DB. Il prend en charge les mêmes fonctionnalités qu’Azure Cosmos DB, dont la création et l’interrogation de documents JSON, l’approvisionnement et la mise à l’échelle des collections, et l’exécution des procédures stockées et déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur Azure Cosmos DB, puis les déployer vers Azure à une échelle globale en apportant une simple modification à la configuration du point de terminaison de connexion pour Azure Cosmos DB.

## <a name="develop-against-the-api-for-mongodb"></a>Développer par rapport à l’API pour MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Qu’est-ce que l’API d’Azure Cosmos DB pour MongoDB ?
L’API Azure Cosmos DB pour MongoDB est une couche de compatibilité qui permet aux applications de communiquer facilement et en toute transparence avec le moteur de base de données Azure Cosmos DB natif en utilisant des API et pilotes MongoDB Apache existants pris en charge par la communauté. Les développeurs peuvent désormais utiliser des chaînes d’outils et compétences MongoDB existantes pour créer des applications qui tirent parti d’Azure Cosmos DB. Les développeurs bénéficient des fonctionnalités uniques d’Azure Cosmos DB, dont l’indexation d’automatique, la maintenance de sauvegarde, les contrats de niveau de service (SLA) avec garantie financière, etc.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Comment me connecter à mon API pour la base de données MongoDB ?
Pour se connecter à l’API Azure Cosmos DB pour MongoDB, la solution la plus rapide consiste à se diriger vers le [portail Azure](https://portal.azure.com). Accédez à votre compte, puis, dans le menu de navigation à gauche, cliquez sur **Démarrage rapide**. Un Démarrage rapide est le meilleur moyen d’obtenir des extraits de code pour vous connecter à votre base de données. 

Azure Cosmos DB applique des normes et des exigences strictes en matière de sécurité. Les comptes Azure Cosmos DB nécessitant une authentification et une communication sécurisée via SSL, veillez à utiliser TLSv1.2.

Pour plus d’informations, voir [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Existe-t-il des codes d’erreur supplémentaires pour une API de base de données MongoDB ?
En plus des codes d’erreur MongoDB habituels, l’API MongoDB a ses propres codes d’erreur :


| Error               | Code  | Description  | Solution  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Le nombre total d’unités de requête consommées ayant dépassé le taux d’unités de requête approvisionné pour la collection, il a été limité. | Envisagez de mettre à l’échelle le débit de la collection via le portail Azure ou de faire une nouvelle tentative. |
| ExceededMemoryLimit | 16501 | En tant que service mutualisé, l’opération a dépassé les unités de mémoire du client. | Réduisez l’étendue de l’opération en fixant un critère de requête plus restrictif, ou contactez le support technique via le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>*Exemple : &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection(’users’).aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>Développer avec l’API Table (préversion)

### <a name="terms"></a>Termes 
L’API Table (préversion) d’Azure Cosmos DB fait référence à l’offre Premium d’Azure Cosmos DB pour la prise en charge des tables annoncée dans le cadre de Microsoft Build 2017. 

Le Kit de développement logiciel (SDK) de table standard est le Kit de développement logiciel (SDK) Table de stockage Azure existant. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Comment utiliser la nouvelle offre d’API Table (préversion) ? 
L’API Table d’Azure Cosmos DB est disponible dans le [portail Azure][azure-portal]. Vous devez d’abord souscrire un abonnement Microsoft Azure. Une fois inscrit, vous pouvez ajouter un compte d’API Table d’Azure Cosmos DB à votre abonnement Azure, puis ajouter des tables à votre compte. 

Pendant la période de la préversion, lorsque des [Kits de développement logiciel (SDK)](../cosmos-db/table-sdk-dotnet.md) sont disponibles pour .NET, vous pouvez commencer en suivant les instructions de l’article relatif au démarrage rapide de l’[API Table](../cosmos-db/create-table-dotnet.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Ai-je besoin d’un nouveau Kit de développement logiciel (SDK) pour utiliser l’API Table (préversion) ? 
Oui, le [SDK Microsoft Azure Storage Premium Table (préversion)](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) est disponible sur NuGet. Pour plus d’informations, voir [API .NET Table Azure Cosmos DB : Téléchargement et notes de publication](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md). 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Comment formuler des commentaires sur le Kit de développement logiciel (SDK) ou des bogues ?
Vous pouvez partager vos commentaires par les biais suivants :

* [UserVoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Quelle chaîne de connexion utiliser pour se connecter à l’API Table (préversion) ?
La chaîne de connexion est :
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
Vous pouvez obtenir la chaîne de connexion dans la page Clés du portail Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>Comment remplacer les paramètres de configuration pour les options de requête dans la nouvelle API Table (préversion) ?
Pour plus d’informations sur les paramètres de configuration, voir [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Vous pouvez modifier les paramètres en les ajoutant à la section appSettings du fichier app.config dans l’application cliente.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>Y a-t-il des changements pour les clients qui utilisent le Kit de développement logiciel (SDK) de table standard existant ?
Aucun. Il n’y a aucun changement pour les clients, existants ou nouveaux, utilisant le Kit de développement logiciel (SDK) de table standard existant. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Comment afficher les données de table qui sont stockées dans Azure Cosmos DB pour les utiliser avec l’API Table (préversion) ? 
Vous pouvez utiliser le portail Azure pour parcourir les données. Vous pouvez également utiliser le code ou les outils de l’API Table (préversion) mentionnés dans la réponse suivante. 

### <a name="which-tools-work-with-the-table-api-preview"></a>Quels outils fonctionnent avec l’API Table (préversion) ? 
Vous pouvez utiliser l’ancienne version de l’Explorateur Azure (0.8.9).

Les outils offrant la flexibilité nécessaire pour prendre une chaîne de connexion au format spécifié précédemment peuvent prendre en charge la nouvelle API Table (préversion). Vous trouverez une liste des outils de table dans la page [Outils clients d’Azure Storage](../storage/storage-explorers.md). 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell ou Azure CLI fonctionnent-ils avec la nouvelle API Table (préversion) ?
Nous prévoyons d’ajouter à PowerShell et à Azure CLI la prise en charge de l’API Table (préversion). 

### <a name="is-the-concurrency-on-operations-controlled"></a>L’accès concurrentiel sur les opérations est-il contrôlé ?
Oui, l’accès concurrentiel optimiste est fourni via l’utilisation du mécanisme ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Le modèle de requête OData est-il pris en charge pour les entités ? 
Oui, l’API Table (préversion) prend en charge les requêtes OData et LINQ. 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>Puis-je me connecter à la table Azure standard et à la nouvelle API Table (préversion) premium simultanément dans la même application ? 
Oui, vous pouvez vous connecter en créant deux instances distinctes de CloudTableClient, chacune pointant vers son propre URI via la chaîne de connexion.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>Comment migrer une application de Stockage de table Azure existante vers cette nouvelle offre ?
Si vous souhaitez tirer parti de la nouvelle offre d’API Table sur vos données de stockage Table existantes, contactez [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>Quelle est la feuille de route pour ce service, et quand proposerez-vous d’autres fonctionnalités de l’API Table standard ?
Nous prévoyons d’ajouter la prise en charge des jetons SAP, de ServiceContext, des Statistiques, du Chiffrement côté client, de l’Analytique et d’autres fonctionnalités à mesure que nous progressons vers la mise à disposition générale. Vous pouvez formuler des commentaires sur [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Comment l’extension de la taille de stockage est-elle gérée pour ce service, par exemple, si je commence avec *n* Go de données et que le volume de celles-ci passe à 1 To au fil du temps ? 
Azure Cosmos DB est conçu pour offrir une capacité de stockage illimitée via l’utilisation de la mise à l’échelle horizontale. Le service peut surveiller votre stockage et en augmenter efficacement le volume. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Comment surveiller l’offre de l’API Table (préversion) ?
Le volet **Mesures** de l’API Table (préversion) permet de surveiller les demandes et l’utilisation du stockage. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Comment calculer le débit dont j’ai besoin ?
Vous pouvez utiliser l’estimateur de capacité pour calculer le débit de table requis pour les opérations. Pour plus d’informations, voir [Estimer les unités de requête et le stockage de données](https://www.documentdb.com/capacityplanner). En général, vous pouvez représenter votre entité sous forme de JSON, et fournir les valeurs pour vos opérations. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Puis-je utiliser le nouveau Kit de développement logiciel (SDK) de l’API Table (préversion) localement avec l’émulateur ?
Oui, vous pouvez utiliser l’API Table (préversion) sur l’émulateur local avec le nouveau Kit de développement logiciel (SDK). Pour télécharger le nouvel émulateur, accédez à [Utiliser l’émulateur Azure Cosmos DB pour développer et tester localement](local-emulator.md). La valeur StorageConnectionString dans app.config doit être :

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>Mon application existante peut-elle fonctionner avec l’API Table (préversion) ? 
La surface d’exposition de la nouvelle API Table (préversion) est compatible avec le Kit de développement logiciel (SDK) de table standard Azure existant pour les opérations de création, de suppression, de mise à jour et de requête dans l’API .NET. Assurez-vous que vous disposez d’une clé de ligne, car l’API Table (préversion) nécessite une clé de partition et une clé de ligne. Nous prévoyons également d’élargir la prise en charge du Kit de développement logiciel (SDK) à mesure que nous progressons vers la mise à disposition générale de cette offre de service.

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>Dois-je migrer mes applications basées sur une table Azure existantes vers le nouveau Kit de développement logiciel (SDK) si je ne souhaite pas utiliser les fonctionnalités de la nouvelle API Table (préversion) ?
Non, vous pouvez créer et utiliser des ressources de table standard existantes sans la moindre interruption. Toutefois, si vous n’utilisez pas la nouvelle API Table (préversion), vous ne pouvez pas bénéficier de l’indexation automatique, de l’option de cohérence supplémentaire ou de la distribution globale. 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>Comment ajouter la réplication des données de l’API Table (préversion) premium dans plusieurs régions Azure ?
Vous pouvez utiliser les [paramètres de réplication globale](tutorial-global-distribution-documentdb.md#portal) du portail Azure Cosmos DB pour ajouter des régions appropriées pour votre application. Pour développer une application distribuée globalement, vous devez également ajouter votre application avec les informations PreferredLocation définies sur la région locale afin de bénéficier d’une faible latence de lecture. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>Comment modifier la région d’écriture principale du compte dans l’API Table (préversion) premium ?
Vous pouvez utiliser le volet du portail de réplication globale d’Azure Cosmos DB pour ajouter une région, puis basculer vers la région requise. Pour des instructions, voir [Développement avec des comptes Azure Cosmos DB multirégions](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Comment configurer mes régions de lecture préférées pour une faible latence lors de la distribution de mes données ? 
Pour faciliter la lecture à partir de l’emplacement local, utilisez la clé PreferredLocation dans le fichier app.config. Pour les applications existantes, l’API Table (préversion) génère une erreur si LocationMode est défini. Supprimez ce code, car l’API Table (préversion) premium extrait ces informations du fichier app.config. Pour plus d’informations, voir [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Que sont les niveaux de cohérence dans l’API Table (préversion) ? 
Azure Cosmos DB adopte des compromis bien pensés entre cohérence, disponibilité et latence. Azure Cosmos DB offre cinq niveaux de cohérence aux développeurs de l’API Table (préversion). Vous pouvez donc choisir le modèle de cohérence approprié au niveau table, et effectuer des demandes individuelles lors de l’interrogation des données. Quand un client se connecte, il peut spécifier un niveau de cohérence. Vous pouvez modifier le niveau via le paramètre app.config pour la valeur de la clé TableConsistencyLevel. 

L’API Table (préversion) permet d’effectuer des lectures à faible latence avec les options Lire vos écritures et Cohérence de session définies par défaut. Pour plus d’informations, consultez [Niveaux de cohérence](consistency-levels.md). 

Par défaut, Stockage de table Azure offre une Cohérence forte au sein d’une région et une Cohérence éventuelle dans les sites secondaires. 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Azure Cosmos DB offre-t-il davantage de niveaux de cohérence que des tables standard ?
Oui, pour plus d’informations sur la façon de tirer parti de la nature distribuée d’Azure Cosmos DB, voir [Niveaux de cohérence](consistency-levels.md). Des garanties étant fournies pour les niveaux de cohérence, vous pouvez utiliser ceux-ci en toute confiance. Pour plus d’informations, voir [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Une fois la distribution globale activée, combien de temps faut-il pour répliquer les données ?
Nous validons les données durablement dans la région locale, et envoyons les données vers les autres régions immédiatement en quelques millisecondes. Cette réplication dépend uniquement de la durée des boucles (RTT) du centre de données. Pour en savoir plus sur la fonctionnalité de distribution globale d’Azure Cosmos DB, voir [Azure Cosmos DB : un service de base de données distribué globalement sur Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Est-il possible de modifier le niveau cohérence des demandes de lecture ?
Azure Cosmos DB permet de définir le niveau de cohérence au niveau du conteneur (sur la table). Le Kit de développement logiciel (SDK) permet de modifier le niveau en fournissant la valeur de la clé TableConsistencyLevel dans le fichier app.config. Les valeurs possibles sont : Forte, Obsolescence limitée, Session, Préfixe cohérent et Éventuelle. Pour plus d’informations, voir [Niveaux de cohérence ajustables dans Azure Cosmos DB](consistency-levels.md). L’idée clé est que vous ne pouvez pas définir un niveau de cohérence de demande supérieur à celui défini pour la table. Par exemple, vous ne pouvez pas définir le niveau de cohérence pour la table sur Éventuelle et le niveau de cohérence de demande sur Forte. 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>Comment le compte d’API Table (préversion) premium gère-t-il le basculement en cas de défaillance d’une région ? 
L’API Table (préversion) premium emprunte à la plateforme distribuée globalement d’Azure Cosmos DB. Pour vous assurer que votre application peut tolérer des temps d’arrêt de centre de données, activez au moins une région supplémentaire pour le compte dans le portail Azure Cosmos DB (voir [Développement avec des comptes Azure Cosmos DB multirégions](regional-failover.md)). Vous pouvez définir la priorité de la région à l’aide du portail (voir [Developing with multi-region Azure Cosmos DB accounts](regional-failover.md) (Développement avec des comptes Azure Cosmos DB multirégions)). 

Vous pouvez ajouter autant de régions que vous le souhaitez pour le compte, et contrôler l’emplacement de basculement en indiquant une priorité. Bien entendu, pour utiliser la base de données, vous devez également fournir une application à cet emplacement. Si vous procédez de la sorte, vos clients ne subissent aucun temps d’arrêt. Le Kit de développement logiciel (SDK) client détermine automatiquement l’hébergement. Autrement dit, il peut détecter la région défaillante et basculer automatiquement vers la nouvelle région.

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>L’API Table (préversion) premium prend-elle en charge les sauvegardes ?
Oui, l’API Table (préversion) premium emprunte à la plateforme d’Azure Cosmos DB pour les sauvegardes. Les sauvegardes sont effectuées automatiquement. Pour plus d’informations, voir [Sauvegarde et restauration en ligne automatiques avec Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>L’API Table (préversion) indexe-t-elle par défaut tous les attributs d’une entité ?
Oui, tous les attributs d’une entité sont indexés par défaut. Pour plus d’informations, voir [Comment Azure Cosmos DB indexe-t-il les données ?](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Cela signifie-t-il que je n’ai pas besoin de créer plusieurs index pour satisfaire les requêtes ? 
Oui, Azure Cosmos DB assure l’indexation automatique de tous les attributs sans aucune définition de schéma. Cette automatisation permet aux développeurs de se concentrer sur l’application plutôt que sur la création et la gestion d’index. Pour plus d’informations, voir [Comment Azure Cosmos DB indexe-t-il les données ?](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Puis-je modifier la stratégie d’indexation ?
Oui, vous pouvez modifier la stratégie d’indexation en fournissant la définition d’index. Pour plus d’informations, voir [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Vous devez correctement encoder et placer dans une séquence d’échappement les paramètres. 

Au format de chaîne JSON dans le fichier app.config :
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB en tant que plateforme semble avoir de nombreuses fonctionnalités, telles que le tri, l’agrégation, la hiérarchisation et autres. Allez-vous ajouter ces fonctionnalités à l’API Table ? 
En préversion, l’API Table offre les mêmes fonctionnalités de requête que Stockage de table Azure. Azure Cosmos DB prend également en charge le tri, les agrégats, les requêtes géospatiales, les hiérarchies et un large éventail de fonctions intégrées. Nous ajouterons des fonctionnalités à l’API Table lors d’une prochaine mise à jour du service. Pour plus d’informations, consultez [Requêtes SQL pour l’API DocumentDB Azure Cosmos DB](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Quand dois-je modifier le débit de table (TableThroughput) pour l’API Table (préversion) ?
Vous devez modifier débit de table (TableThroughput) dans les situations suivantes :
* Vous effectuez une opération d’extraction, transformation et chargement (ETL) de données, ou souhaitez charger une grande quantité de données dans un laps de temps court. 
* Vous avez besoin d’un débit supérieur du conteneur sur le serveur principal. Par exemple, vous voyez que le débit utilisé est supérieur au débit approvisionné, et vous êtes limité. Pour plus d’informations, consultez [Définir le débit des conteneurs Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>Puis-je augmenter ou réduire le débit ma table d’API Table (préversion) ? 
Oui, vous pouvez utiliser le volet de mise à l’échelle du portail Azure Cosmos DB pour régler le débit. Pour plus d’informations, voir [Définir le débit](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Un débit de table (TableThroughput) par défaut est-il défini pour les nouvelles tables approvisionnées ?
Oui, si vous ne remplacez pas le débit de table (TableThroughput) dans le fichier app.config et n’utilisez pas de conteneur créé préalablement dans Azure Cosmos DB, le service crée une table dont le débit est défini sur 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>La tarification change-t-elle pour les clients existants de l’API Table standard ?
Aucune. Le tarif reste inchangé pour les clients existants de l’API Table standard. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Comment le prix est-il calculé pour l’API Table (préversion) ? 
Cela dépend du débit de table (TableThroughput) alloué. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Comment gérer les limitations qui affectent les tables dans l’offre d’API Table (préversion) ? 
Si le taux de demandes dépasse la capacité du débit approvisionné pour le conteneur sous-jacent, vous recevez une erreur et le Kit de développement logiciel (SDK) essaie de renouveler l’appel en appliquant la stratégie de nouvelle tentative.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Pourquoi dois-je choisir un débit en dehors de la clé de partition et de la clé de ligne pour tirer parti de l’offre d’API Table (préversion) premium d’Azure Cosmos DB ?
Si vous ne spécifiez pas de débit par défaut pour votre conteneur dans le fichier app.config, Azure Cosmos DB en définit un. 

Azure Cosmos DB fournit des garanties de performances et de latence avec des limites supérieures pour les opérations. Cette garantie est possible lorsque le moteur peut assurer la gouvernance des opérations du client. La définition du débit de table (TableThroughput) est pour vous l’assurance de bénéficier du débit et de la latence garantis, car la plateforme réserve cette capacité et garantit le succès des opérations. 

La spécification du débit vous permet de modifier celui-ci en souplesse pour tirer parti du caractère saisonnier de votre application, répondre aux besoins de débit et réduire les coûts.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Le Kit de développement logiciel (SDK) Stockage Azure était très peu onéreux pour moi, car je payais uniquement pour le stockage des données et n’effectuais que rarement des requêtes. La nouvelle offre Azure Cosmos DB semble me facturer des frais malgré que je n’ai pas effectué la moindre transaction ou stocké quoi que ce soit. Pouvez-vous m’en expliquer la raison ?

Azure Cosmos DB est conçu pour être un système distribué globalement basé sur un contrat de niveau de service (SLA) offrant des garanties en matière de disponibilité, de latence et de débit. Le débit que vous réservez dans Azure Cosmos DB est garanti, à la différence du débit d’autres systèmes. Azure Cosmos DB offre des fonctionnalités supplémentaires qui ont été demandées par les clients, telles que les index secondaires et la distribution globale. Pendant la période de la préversion, nous fournissons un modèle optimisé pour le débit mais, à terme, nous prévoyons de fournir un modèle optimisé pour le stockage afin de répondre aux besoins de nos clients. 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Je ne reçois jamais de notification de « quota complet » (indiquant qu’une partition est pleine) lorsque j’ingère des données dans un stockage Table. Avec l’API Table (préversion), je reçois ce message. Cette offre me limite-t-elle et m’oblige-t-elle à modifier mon application existante ?

Azure Cosmos DB est un système basé sur un contrat de niveau de service (SLA) permettant une mise à l’échelle illimitée, avec des garanties en matière de latence, de débit, de disponibilité et de cohérence. Pour être certain de bénéficier des performances premium garanties, assurez-vous que la taille de vos données et votre index sont gérables et extensibles. La limite de 10 Go appliquée au nombre d’entités ou d’éléments par clé de partition nous permet de garantir des performances de recherche et de requête exceptionnelles. Pour vous assurer que la mise à l’échelle de votre application fonctionne correctement, nous vous recommandons de ne *pas* créer de partition sensible en stockant toutes les informations dans une seule partition et en interrogeant celle-ci. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>Si je comprends bien, une clé de partition et une clé de ligne sont toujours requises avec la nouvelle API Table (préversion) ? 
Oui. Comme la surface d’exposition de l’API Table (préversion) est semblable à celle du Kit de développement logiciel (SDK) Stockage Table, la clé de partition constitue un moyen efficace de distribuer les données. La clé de ligne est unique au sein de cette partition. La clé de ligne doit être présente et ne peut pas avoir la valeur null comme dans le Kit de développement logiciel (SDK) standard. La longueur de la clé de ligne est de 255 octets, et celle de la clé de partition de 100 octets (bientôt portée à 1 Ko). 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Quels sont les messages d’erreur relatifs à l’API Table (préversion) ?
Étant donné que cette préversion est compatible avec la table standard, la plupart des erreurs correspondent à celles de la table standard. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Pourquoi suis-je limité lorsque je tente de créer successivement de nombreuses tables dans l’API Table (préversion) ?
Azure Cosmos DB est un système basé sur un contrat de niveau de service (SLA) qui offre des garanties en matière de latence, de débit, de disponibilité et de cohérence. Comme il s’agit d’un système approvisionné, il réserve des ressources afin de garantir le respect de ces exigences. La vitesse de création des tables est détectée et limitée. Nous vous recommandons de consulter le taux de création de tables et de le réduire à moins de 5 par minute. N’oubliez pas que l’API Table (préversion) est un système approvisionné. Dès que vous l’approvisionnez, vous commencez à payer. 

## <a name="develop-against-the-graph-api-preview"></a>Développer par rapport à l’API Graph (préversion)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Comment puis-je appliquer les fonctionnalités de l’API Graph (préversion) à Azure Cosmos DB ?
Pour appliquer les fonctionnalités de l’API Graph (préversion), vous pouvez utiliser une bibliothèque d’extension. Cette bibliothèque nommée Microsoft Azure AD Graph est disponible sur NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Il semble que vous prenez en charge le langage de traversée de graphique Gremlin. Prévoyez-vous d’ajouter d’autres formes de requête ?
Oui, nous prévoyons d’ajouter d’autres mécanismes de requête à l’avenir. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Comment utiliser la nouvelle offre d’API Graph (préversion) ? 
Pour commencer, lisez l’article relatif au démarrage rapide de l’[API Graph](../cosmos-db/create-graph-dotnet.md).

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Questions des clients de DocumentDB
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Pourquoi migrez-vous vers Azure Cosmos DB ? 

Azure Cosmos DB est le prochain grand saut en matière de bases de données cloud extensibles distribuées globalement. En tant que client DocumentDB, vous avez désormais accès au système et aux capacités révolutionnaires qu’offre Azure Cosmos DB.

Azure Cosmos DB a été lancé en 2010 sous le nom de « Project Florence » pour répondre aux problématiques que rencontraient les développeurs lors de la création d’applications à grande échelle au sein de Microsoft. Les difficultés liées à la création d’applications distribuées globalement n’étant pas un problème propre à Microsoft, en 2015, nous avons mis la première génération de cette technologie à la disposition des développeurs Azure sous la forme d’Azure DocumentDB. 

Depuis, nous avons ajouté de nouvelles fonctionnalités et introduit de nouvelles possibilités significatives. Azure Cosmos DB en est le résultat. Dans le cadre de cette publication, les clients DocumentDB (et leurs données) sont devenus, automatiquement et en toute fluidité, des clients Azure Cosmos DB. Ces fonctionnalités touchent les domaines du moteur principal de base de données, ainsi que de la distribution globale, de l’extensibilité élastique et des contrats SLA performants et complets. Plus précisément, nous avons amélioré le moteur de base de données d’Azure Cosmos DB pour lui permettre de mapper efficacement l’ensemble des modèles de données, systèmes de types et API courants au modèle de données sous-jacent d’Azure Cosmos DB. 

L’expression actuelle de ce travail pour les développeurs est la prise en charge de [Gremlin](../cosmos-db/graph-introduction.md) et des [API Stockage Table](../cosmos-db/table-introduction.md). Et ce n’est qu’un début. Nous prévoyons d’ajouter d’autres API populaires et de nouveaux modèles de données au fil du temps, ainsi que d’accomplir des progrès supplémentaires en matière de performances et de stockage à l’échelle mondiale. 

Il est important de souligner que le [dialecte SQL](../documentdb/documentdb-sql-query.md) de DocumentDB n’a jamais été que l’une des nombreuses API que l’Azure Cosmos DB sous-javent pouvait prendre en charge. Pour les développeurs utilisant un service entièrement géré tel qu’Azure Cosmos DB, les seules interfaces avec le service sont les API exposées par celui-ci. Rien ne change réellement pour les clients DocumentDB existants. Dans Azure Cosmos DB, vous obtenez exactement la même API SQL que celle offerte par DocumentDB. Désormais (et à l’avenir), vous pouvez accéder à d’autres fonctionnalités précédemment inaccessibles. 

Un autre témoin de notre travail continu est la base étendue pour une extensibilité globale et flexible du débit et du stockage. Nous avons apporté plusieurs améliorations fondamentales au sous-système de distribution globale. Une de ces nombreuses fonctionnalités côté développeur est le modèle de cohérence Préfixe cohérent, qui regroupe en fait cinq modèles de cohérence bien définis. Nous publierons beaucoup plus de fonctionnalités intéressantes à mesure qu’elles arriveront à maturité. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>Que dois-je faire pour que mes ressources DocumentDB continuent à opérer sur Azure Cosmos DB ?

Vous n’avez rien à faire du tout. Vos ressources DocumentDB sont désormais des ressources Azure Cosmos DB, et aucune interruption de service ne s’est produite lors de la migration.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Quelles modifications dois-je effectuer pour que mon application fonctionne avec Azure Cosmos DB ?

Vous n’avez aucune modification à effectuer. Les classes, les espaces de noms et les noms de package NuGet n’ont pas changé. Comme toujours, nous vous recommandons de conserver vos kits de développement logiciel à jour pour tirer parti des dernières fonctionnalités et améliorations. 

### <a name="whats-changed-in-the-azure-portal"></a>Quelles sont les nouveautés dans le portail Azure ?

Document DB n’apparaît plus en tant que service Azure dans le portail. À la place figure une nouvelle icône Azure Cosmos DB illustrée dans l’image suivante. Toutes vos collections sont disponibles comme auparavant, et vous pouvez toujours mettre à l’échelle le débit, modifier les niveaux de cohérence et analyser les contrats SLA. Les fonctionnalités de l’Explorateur de données (préversion) ont été améliorées. Vous pouvez désormais afficher et modifier des documents, créer et exécuter des requêtes, ainsi qu’utiliser des procédures stockées, des déclencheurs et des UDF à partir d’une page, comme illustré dans l’image suivante : 

![Panneau Collections Azure Cosmos DB](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>La tarification change-t-elle ?

Non, le coût d’exécution de votre application sur Azure Cosmos DB est le même qu’auparavant.

### <a name="are-there-changes-to-the-slas"></a>Les contrats SLA ont-ils changé ?

Non, les contrats SLA concernant la disponibilité, la cohérence, la latence et le débit restent inchangés et sont toujours affichés dans le portail. Pour plus d’informations, voir [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Application To-Do avec exemples de données](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

