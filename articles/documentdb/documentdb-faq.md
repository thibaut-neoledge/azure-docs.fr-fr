---
title: "Questions sur le service de base de données Azure Cosmos DB - Forum Aux Questions (FAQ) | Documents Microsoft"
description: "Obtenez des réponses aux questions fréquemment posées sur Azure Cosmos DB, service de base de données multimodèle à distribution mondiale. Répondez aux questions sur la capacité, les niveaux de performance et l’évolutivité de la base de données."
keywords: "Questions sur la base de données, Forum aux questions, documentdb, azure, Microsoft azure"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 687690fc3b99d52d7c822b7b7ff71c282541be6d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="frequently-asked-questions-about-azure-cosmos-db"></a>Forum Aux Questions sur Azure Cosmos DB
## <a name="database-questions-about-microsoft-azure-cosmos-db-fundamentals"></a>Questions à propos des concepts de base de Microsoft Azure Cosmos DB
### <a name="what-is-microsoft-azure-cosmos-db"></a>Qu’est-ce que Microsoft Azure Cosmos DB ?
Microsoft Azure Cosmos DB est un service de base de données multimodèle mondialement répliquée qui propose des requêtes enrichies sur des données sans schéma. Il offre des options de configuration et des performances fiables, tout en permettant un développement rapide. Tout cela est rendu possible grâce à une plateforme gérée, soutenue par la puissance et la portée de Microsoft Azure. Azure Cosmos DB est la solution idéale pour les applications web, mobiles, de jeu et IoT lorsqu’un débit prévisible, une haute disponibilité, une faible latence et un modèle de données sans schéma sont primordiaux. Cosmos DB offre une flexibilité des schémas et une indexation riche. Il inclut également un support transactionnel multidocument avec JavaScript intégré.  

Azure Cosmos DB a été lancé en 2010 pour répondre aux problématiques des développeurs auxquelles font face les applications à grande échelle chez Microsoft. Comme la création d’applications distribuées mondialement n’est pas un problème unique à Microsoft, nous mettons le service à disposition en externe pour tous les développeurs par le biais d’Azure DocumentDB. Azure Cosmos DB est la prochaine grande étape dans l’évolution de DocumentDB et nous le mettons maintenant à disposition. Dans le cadre de cette version d’Azure Cosmos DB, les clients DocumentDB (avec leurs données) sont automatiquement des clients Azure Cosmos DB. La transition est transparente et les clients ont maintenant accès à une large gamme de nouvelles fonctionnalités offertes par Azure Cosmos DB. 

Pour plus de questions, de réponses et d’instructions sur le déploiement et l’utilisation de ce service, consultez la [page de documentation d’Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-happened-to-documentdb"></a>Qu’est devenu DocumentDB ?
L’API DocumentDB fait partie des API et des modèles de données pris en charge pour Azure Cosmos DB. De plus, Azure Cosmos DB met à votre disposition l’API Graph (préversion), l’API Table (préversion) et l’API MongoDB. 

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Comment accéder à mon compte DocumentDB dans le portail Azure ?
Il vous suffit de cliquer sur l’icône Azure Cosmos DB dans le menu de gauche du portail Azure. Si vous possédiez auparavant un compte DocumentDB, vous disposez maintenant d’un compte Azure Cosmos DB, sans répercussion sur votre facturation.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quels sont les cas d’utilisation courants d’Azure Cosmos DB ?
Azure Cosmos DB est un choix judicieux pour les nouvelles applications web, mobiles, de jeu et IoT où une mise à l’échelle automatique, des performances prévisibles, des temps de réponse de l’ordre d’une milliseconde et la capacité à exécuter des requêtes sur des données sans schéma sont importants. Azure Cosmos DB permet un développement rapide et prend en charge l’itération continue des modèles de données d’application.  Les applications qui gèrent du contenu généré par l’utilisateur et des données sont [des cas d’utilisation courants pour Azure Cosmos DB](documentdb-use-cases.md).  

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Comment Azure Cosmos DB offre-t-il des performances prévisibles ?
Dans Azure Cosmos DB, la mesure du débit est exprimée en [unités de requête (RU)](documentdb-request-units.md). 1 RU correspond au débit de la requête GET d’un document d’1 Ko. Chaque opération dans Azure Cosmos DB, y compris les lectures, les écritures, les requêtes SQL et les exécutions de procédures stockées, comporte une valeur de RU déterministe basée sur le débit nécessaire à l’exécution de l’opération. Au lieu de penser à l’UC, à l’E/S et à la mémoire, et à la façon dont ils impactent le débit de votre application, vous pouvez penser en termes de mesure d’unité de requête unique.

Chaque conteneur Azure Cosmos DB peut être réservé avec un débit approvisionné en termes de RU de débit par seconde. Pour les applications quelle que soit leur échelle, vous pouvez évaluer les requêtes individuelles pour mesurer leur valeur de RU, et approvisionner un conteneur pour gérer la somme totale des unités de requête sur l’ensemble des requêtes. Vous pouvez également mettre à l’échelle le débit de votre conteneur à mesure de l’évolution des besoins de votre application. Pour plus d’informations sur les unités de requête et pour obtenir de l’aide afin de déterminer vos besoins en matière de conteneurs, consultez [Estimation des besoins de débit](documentdb-request-units.md#estimating-throughput-needs) et essayez la [calculatrice de débit](https://www.documentdb.com/capacityplanner). Les conteneurs ici font référence à la collection de l’API DocumentDB, aux graphiques de l’API Graph, à la collection de l’API MongoDB et aux tables de l’API Table.  


### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB est-il conforme à la loi HIPAA ?
Oui, Azure Cosmos DB est conforme à la loi HIPAA. HIPAA établit les conditions requises pour l’utilisation, la divulgation et la protection des informations de santé identifiables de façon individuelle. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quelles sont les limites de stockage d’Azure Cosmos DB ?
Il n’existe aucune limite à la quantité totale de données qu’un conteneur peut stocker dans Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quelles sont les limites de débit d’Azure Cosmos DB ?
Il n’existe aucune limite à la quantité totale de débit qu’un conteneur peut prendre en charge dans Azure Cosmos DB, à condition que votre charge de travail soit distribuée à peu près uniformément entre un nombre suffisant de clés de partition.

### <a name="how-much-does-microsoft-azure-cosmos-db-cost"></a>Combien coûte Microsoft Azure Cosmos DB ?
Pour plus d’informations, consultez la page [Tarification d’Azure Cosmos DB](https://azure.microsoft.com/pricing/details/documentdb/). Les frais d’utilisation d’Azure Cosmos DB sont déterminés par le nombre de conteneurs configurés, le nombre d’heures durant lequel les conteneurs ont été en ligne et le débit configuré pour chaque conteneur. Les conteneurs ici font référence à la collection de l’API DocumentDB, aux graphiques de l’API Graph, à la collection de l’API MongoDB et aux tables de l’API Table. 

### <a name="is-there-a-free-account-available"></a>Un compte gratuit est-il disponible ?
Si vous débutez avec Azure, vous pouvez vous inscrire pour bénéficier d’un [compte Azure gratuit](https://azure.microsoft.com/free/), qui vous offre 30 jours et 200 USD pour essayer tous les services Azure. Ou, si vous possédez un abonnement Visual Studio, vous pouvez bénéficier de [150 USD de crédits Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), à utiliser sur n’importe quel service Azure.  

Vous pouvez également utiliser l’[émulateur Azure Cosmos DB](documentdb-nosql-local-emulator.md) pour développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur Azure Cosmos DB, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Comment puis-je obtenir une aide supplémentaire avec Azure Cosmos DB ?
Si vous avez besoin d’aide, contactez-nous sur [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), le [forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB) ou planifiez une conversation individuelle avec l’équipe d’ingénierie d’Azure Cosmos DB en envoyant un message à [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com). 

## <a name="set-up-microsoft-azure-cosmos-db"></a>Configurer Microsoft Azure Cosmos DB
### <a name="how-do-i-sign-up-for-microsoft-azure-cosmos-db"></a>Comment s’inscrire à Microsoft Azure Cosmos DB ?
Microsoft Azure Cosmos DB est disponible dans le portail Azure. Vous devez d'abord disposer d'un abonnement à Microsoft Azure. Une fois inscrit à Microsoft Azure, vous pouvez ajouter un compte d’API DocumentDB, Graph (préversion), Table (préversion) ou MongoDB à votre abonnement Azure.

### <a name="what-is-a-master-key"></a>Qu'est-ce que la clé principale ?
Une clé principale est un jeton de sécurité permettant d'accéder à toutes les ressources d'un compte. Les personnes disposant de cette clé ont un accès en lecture et en écriture à toutes les ressources du compte de la base de données. Faites preuve de précaution lorsque vous distribuez des clés principales. La clé principale primaire et la clé principale secondaire sont disponibles dans le panneau **Clés** du [portail Azure][azure-portal]. Pour plus d’informations sur les clés, consultez la rubrique [Affichage, copie et régénération de clés d’accès](documentdb-manage-account.md#keys).

### <a name="what-are-the-regions-to-which-preferredlocations-be-set-to"></a>Quelles sont les régions configurables pour PreferredLocations ? 
À l’heure actuelle, les régions valides pour PrefferredLocations sont les suivantes : États-Unis de l’Ouest, États-Unis Centre-Ouest, Ouest des États-Unis 2, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis du Centre, Sud du centre des États-Unis, Nord du centre des États-Unis, Europe de l’Ouest, Europe du Nord, Asie de l’Est, Sud-Est asiatique, Japon de l’Ouest, Japon de l’Est, Sud-Est de l’Australie, Est de l’Australie, Centre de l’Inde, Inde du Sud, Inde de l’Ouest, Canada Est, Canada Centre, Centre de l’Allemagne, Nord-Est de l’Allemagne, Nord de la Chine, Est de la Chine, Corée du Sud, Royaume-Uni Ouest, Royaume-Uni Sud, Sud du Brésil, USGov Arizona et USGov Texas.

### <a name="is-there-something-i-should-be-aware-of-when-distributing-data-across-the-world-via-azures-data-centers"></a>Y a-t-il quelque chose d’important à savoir concernant la distribution de données à travers le monde via les centres de données Azure ? 
Cosmos DB est présent dans toutes les régions. Comme il s’agit du service principal, chaque nouveau centre de données aura une présence Cosmos DB. Les centres de données actuels sont répertoriés ci-dessus. Lorsque vous les définissez, vous devez garder à l’esprit que Cosmos DB respecte les clouds souverains et les clouds pour le secteur public. Par conséquent, si vous créez un compte dans un de ces emplacements, vous n’êtes pas autorisé à effectuer de réplication en dehors de celui-ci. De même, vous ne pouvez pas activer la réplication à partir d’un compte extérieur dans ces emplacements. 



### <a name="do-you-plan-to-provide-more-price-options-in-the-future"></a>Prévoyez-vous de proposer d’autres options de tarification ?
Oui. À l’heure actuelle, Cosmos DB offre un modèle optimisé pour le débit. Nous prévoyons de proposer des modèles de tarification optimale dans un avenir proche. 

 
# <a name="documentdb-api"></a>API DocumentDB 
## <a name="database-questions-about-developing-against-documentdb-api"></a>Questions à propos du développement avec l’API DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb-api"></a>Comment développer avec l’API DocumentDB ?
L’API Microsoft DocumentDB est disponible dans le [portail Azure][azure-portal].  Vous devez d'abord disposer d'un abonnement à Microsoft Azure.  Une fois inscrit à Microsoft Azure, vous pouvez ajouter un conteneur d’API DocumentDB à votre abonnement Azure. Pour savoir comment ajouter un compte Azure Cosmos DB, consultez [Créer un compte de base de données Azure Cosmos DB](documentdb-create-account.md). Si vous possédiez auparavant un compte DocumentDB, vous disposez maintenant d’un compte Azure Cosmos DB.  

[Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) sont disponibles pour .NET, Python, Node.js, JavaScript et Java.  Les développeurs peuvent également utiliser les [API HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour interagir avec les ressources Azure Cosmos DB sur différentes plateformes et dans de nombreux langages.

### <a name="can-i-access-some-ready-made-samples-for-getting-headstart"></a>Puis-je accéder à des exemples prêts à l’emploi pour gagner du temps ?
Des exemples pour les Kits de développement logiciel (SDK) [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) et [Python](documentdb-python-samples.md) de l’API DocumentDB sont disponibles sur GitHub.


### <a name="does-documentdb-api--database-support-schema-free-data"></a>Les bases de données de l’API DocumentDB prennent-elles en charge les données sans schéma ?
Oui, l’API DocumentDB permet aux applications de stocker des documents JSON arbitraires sans définition de schéma ni indicateurs. Les données peuvent être interrogées immédiatement via l’interface de requête SQL d’Azure Cosmos DB.   

### <a name="does-documentdb-api-support-acid-transactions"></a>L’API DocumentDB prend-elle en charge les transactions ACID ?
Oui, l’API DocumentDB prend en charge les transactions entre documents exprimées sous forme de procédures stockées et de déclencheurs JavaScript. Les transactions sont étendues à une seule partition au sein de chaque collection et exécutées intégralement avec des sémantiques ACID, isolées du code et des requêtes utilisateur en cours d’exécution.  Si des exceptions surviennent lors de l’exécution du code d’application JavaScript côté serveur, la transaction entière est annulée. Pour plus d’informations sur les transactions, consultez [Transactions de programme de base de données](documentdb-programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Qu'est-ce qu'une collection ?
Une collection consiste en un groupe de documents et la logique d’application JavaScript associée. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le débit et le stockage utilisés. Les collections peuvent couvrir une ou plusieurs partitions/serveurs et peuvent être mises à l’échelle pour gérer des volumes de stockage ou de débit quasi-illimités.

Les collections constituent également les entités de facturation d’Azure Cosmos DB. Chaque collection est facturée sur la base du débit approvisionné et de l’espace de stockage utilisé. Pour plus d’informations, consultez [Tarification de l’API DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-create-a-database"></a>Comment créer une base de données ?
Vous pouvez créer des bases de données à l’aide du [portail Azure]() en suivant la procédure décrite dans l’article [Création d’une collection et d’une base de données Azure Cosmos DB](documentdb-create-collection.md), à l’aide de l’un des [Kits de développement logiciel (SDK) Azure Cosmos DB](documentdb-sdk-dotnet.md) ou au moyen des [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Comment configurer des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et des autorisations en utilisant l’un des [Kits de développement logiciel (SDK) de l’API DocumentDB](documentdb-sdk-dotnet.md) ou les [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

### <a name="does-documentdb-api-support-sql"></a>L’API DocumentDB prend-elle en charge SQL ?
Le langage de requête SQL est un sous-ensemble amélioré des fonctionnalités de requête prises en charge par SQL. Le langage de requête SQL d’Azure Cosmos DB fournit des opérateurs relationnels et hiérarchiques enrichis ainsi qu’une extensibilité par le biais de fonctions JavaScript définies par l’utilisateur. La syntaxe JSON permet la modélisation de documents JSON sous forme d’arborescences avec des étiquettes en tant que nœuds d’arborescence. Cette capacité est exploitée par les techniques d’indexation automatique d’Azure Cosmos DB et le langage de requête SQL de ce dernier.  Pour plus d’informations sur l’utilisation de la syntaxe SQL, consultez l’[article relatif aux requêtes dans DocumentDB][query].

### <a name="does-documentdb-api--support-sql-aggregation-functions"></a>L’API DocumentDB prend-elle en charge les fonctions d’agrégation SQL ?
L’API DocumentDB prend en charge l’agrégation à faible latence à n’importe quelle échelle par l’intermédiaire des fonctions d’agrégation `COUNT`, `MIN`, `MAX`, `AVG` et `SUM` via la syntaxe SQL. Pour plus d’informations, consultez [Fonctions d’agrégation](documentdb-sql-query.md#Aggregates).

### <a name="how-does-documentdb-api--provide-concurrency"></a>Quel type d’accès concurrentiel offre l’API DocumentDB ?
L’API DocumentDB prend en charge le contrôle d’accès concurrentiel optimiste via les balises d’entité HTTP ou ETags. Chaque ressource de l’API DocumentDB présente une balise ETag qui est définie sur le serveur à chaque fois qu’un document est mis à jour. L’en-tête de l’etag et la valeur actuelle sont inclus dans tous les messages de réponse. Les balises etags peuvent être utilisées avec l’en-tête If-Match pour permettre au serveur de déterminer si une ressource doit être mise à jour. La valeur If-Match est la valeur etag utilisée pour la vérification. Si la valeur etag correspond à la valeur etag du serveur, la ressource sera mise à jour. Si la balise etag n’est plus active, le serveur rejette l’opération avec un code de réponse « HTTP 412 Échec de la condition préalable ». Le client doit alors récupérer à nouveau la ressource pour obtenir la valeur etag actuelle pour la ressource. En outre, les balises etags peuvent être utilisées avec un en-tête If-None-Match pour déterminer si la nouvelle extraction d’une ressource est nécessaire.

Pour utiliser l’accès concurrentiel optimiste dans .NET, utilisez la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Pour voir un exemple .NET, consultez [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans l’exemple DocumentManagement sur github.

### <a name="how-do-i-perform-transactions-in-documentdb-api"></a>Comment effectuer des transactions dans l’API DocumentDB ?
L’API DocumentDB prend en charge les transactions intégrées au langage via les déclencheurs et les procédures stockées JavaScript. Toutes les opérations de base de données dans les scripts sont exécutées avec l’isolement de capture instantanée étendu à la collection s’il s’agit d’une collection à partition unique, ou à des documents ayant la même valeur de clé de partition au sein d’une collection, si la collection est partitionnée. Une capture instantanée des versions des documents (ETags) est prise au début de la transaction et validée uniquement si le script fonctionne. Si le JavaScript renvoie une erreur, la transaction est annulée. Pour plus d’informations, consultez l’article [Programmation côté serveur dans l’API DocumentDB](documentdb-programming.md).

### <a name="how-can-i-bulk-insert-documents-into-documentdb-api"></a>Comment insérer des documents en bloc dans l’API DocumentDB ?
Il existe trois façons d’insérer des documents en bloc dans Azure Cosmos DB :

* L’outil de migration de données, décrit dans l’article [Importation de données vers l’API DocumentDB](documentdb-import-data.md).
* L’Explorateur de documents dans le portail Azure, décrit dans la rubrique [Ajouter des documents en bloc à l’aide de l’Explorateur de documents](documentdb-view-json-document-explorer.md#bulk-add-documents).
* Les procédures stockées, décrites dans l’article [Programmation côté serveur dans l’API DocumentDB](documentdb-programming.md).

### <a name="does-documentdb-api-support-resource-link-caching"></a>L’API DocumentDB prend-elle en charge la mise en cache des liens de ressource ?
Oui. Cosmos DB étant un service RESTful, les liens de ressource sont immuables et peuvent être mis en cache. Les clients de l’API DocumentDB peuvent spécifier un en-tête « If-None-Match » pour des lectures en comparaison avec des ressources telles que des documents ou des collections. Ils peuvent également mettre à jour les copies locales uniquement lorsque la version du serveur a été modifiée.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Une instance locale de l’API DocumentDB est-elle disponible ?
Oui. L’[émulateur Azure Cosmos DB](documentdb-nosql-local-emulator.md) fournit une émulation haute fidélité du service de l’API DocumentDB. Il prend en charge les mêmes fonctionnalités qu’Azure Cosmos DB, notamment la création et l’interrogation des documents JSON, l’approvisionnement et la mise à l’échelle des collections, et l’exécution des procédures stockées et des déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur Azure Cosmos DB et les déployer vers Azure à l’échelle mondiale en apportant une seule modification à la configuration du point de terminaison de connexion pour Azure Cosmos DB.

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>Questions à propos du développement avec l’API pour MongoDB
### <a name="what-is-azure-cosmos-dbs-api-for-mongodb"></a>Qu’est-ce que l’API d’Azure Cosmos DB pour MongoDB ?
L’API de Microsoft Azure Cosmos DB pour MongoDB est une couche de compatibilité qui permet aux applications de communiquer facilement et en toute transparence avec le moteur de base de données Azure Cosmos DB natif en utilisant des API et des pilotes Apache MongoDB communautaires existants. Les développeurs peuvent maintenant utiliser les chaînes et compétences de l’outil MongoDB pour créer des applications qui tirent parti des fonctionnalités uniques d’Azure Cosmos DB, qui incluent l’indexation automatique, la maintenance de sauvegarde et des contrats de niveau de service (SLA) avec compensation financière.

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>Comment me connecter à mon API de base de données MongoDB ?
Pour se connecter à l’API d’Azure Cosmos DB pour MongoDB, la solution la plus rapide consiste à passer par le [portail Azure](https://portal.azure.com). Naviguez jusqu’à votre compte. Dans la barre de *navigation gauche*, cliquez sur *Démarrage rapide*. *Démarrage rapide* est le meilleur moyen d’obtenir des extraits de code pour vous connecter à votre base de données. 

Azure Cosmos DB applique des normes et des exigences strictes en matière de sécurité. Les comptes Azure Cosmos DB requièrent une authentification et une communication sécurisée via *SSL*. Assurez-vous donc d’utiliser TLSv1.2.

Pour plus d’informations, consultez [Connexion à votre API de base de données MongoDB](documentdb-connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Existe-t-il des codes d’erreur supplémentaires pour une API de base de données MongoDB ?
L’API pour MongoDB a ses propres codes d’erreur spécifiques en plus des codes d’erreur MongoDB habituels.


| Error               | Code  | Description  | Solution  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Le nombre total d’unités de requête consommées a dépassé le taux d’unités de requête configuré pour la collection et a été limité. | Envisagez de mettre à l’échelle le débit de la collection à partir du portail Azure ou faites une nouvelle tentative. |
| ExceededMemoryLimit | 16501 | En tant que service mutualisé, l’opération a dépassé les unités de mémoire du client. | Réduire l’étendue de l’opération via un critère de requête plus restrictif ou contactez le support à partir du [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>*Ex :  &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="database-questions-about-developing-with-azure-cosmos-db-table-api-preview"></a>Questions à propos du développement avec Azure Cosmos DB : API Table (préversion)

### <a name="terms"></a>Termes 
L’API Table (préversion) d’Azure Cosmos DB fait référence à l’offre Premium d’Azure Cosmos DB pour la prise en charge des tables annoncée à l’occasion de la conférence Build 2017. 

Le Kit de développement logiciel (SDK) Table Standard est le SDK Stockage Table Azure existant. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Comment puis-je utiliser la nouvelle offre d’API Table (préversion) ? 
L’API Microsoft Table est disponible dans le [portail Azure][azure-portal].  Vous devez d'abord disposer d'un abonnement à Microsoft Azure.  Une fois inscrit à Microsoft Azure, vous pouvez ajouter un conteneur d’API Table à votre abonnement Azure.  
Pendant la durée de la préversion, des [Kits de développement logiciel (SDK)](../cosmos-db/table-sdk-dotnet.md) sont disponibles pour .NET. Pour vous lancer, vous pouvez suivre le [guide de démarrage rapide de l’API Table](../cosmos-db/create-table-dotnet.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Ai-je besoin d’un nouveau Kit de développement logiciel (SDK) pour utiliser l’API Table (préversion) ? 
Oui : un nouveau SDK prenant la forme d’un package Nuget est disponible [ici](../cosmos-db/table-sdk-dotnet.md). Il porte le nom de Kit de développement logiciel (SDK) pour Stockage Microsoft Azure 8.1.2 avec API Table Premium (préversion).  

### <a name="how-do-i-provide-the-feedback-about-the-sdk-bugs"></a>Comment faire pour signaler les bogues du Kit de développement logiciel (SDK) ?
Pour nous faire part de vos commentaires, vous pouvez utiliser l’une des méthodes suivantes :

* [UserVoice](https://feedback.azure.com/forums/263030-documentdb)
* [Forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Quelle chaîne de connexion faut-il utiliser pour se connecter à l’API Table (préversion) ?
La chaîne de connexion est `DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com`. Vous pouvez obtenir la chaîne de connexion dans la page Clés du portail Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-apipreview"></a>Comment faire pour remplacer les paramètres de configuration pour les options de requête dans la nouvelle API Table (préversion) ?
Ces paramètres sont documentés dans [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Vous pouvez modifier les paramètres en les ajoutant à la section appsettings du fichier app.config de l’application cliente.
<appSettings>
    <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
    <add key="TableThroughput" value="<PositiveIntegerValue"/>
    <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
    <add key="TableUseGatewayMode" value="True|False"/>
    <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
</appSettings>


### <a name="is-there-any-change-to-existing-customers-that-use-the-existing-standard-table-sdk"></a>Y a-t-il des changements pour les clients existants qui utilisent le Kit de développement logiciel (SDK) Table Standard existant ?
Aucune. Il n’y a aucun changement pour les clients existants ou nouveaux utilisant le SDK Table Standard existant. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Comment afficher les données de table qui sont stockées dans Azure Cosmos DB pour les utiliser avec l’API Table (préversion) ? 
Vous pouvez utiliser le portail Azure pour parcourir les données. Vous pouvez également utiliser le code de l’API Table (préversion) ou les outils mentionnés ci-dessous. 

### <a name="which-tools-will-work-with-table-api-preview"></a>Quels outils fonctionnent avec l’API Table (préversion) ? 
La version antérieure d’Azure Explorer (0.8.9).

Les outils qui acceptent une chaîne de connexion au format spécifié précédemment peuvent prendre en charge la nouvelle API Table (préversion). Vous trouverez une liste des outils de table dans la page [Outils clients d’Azure Storage](../storage/storage-explorers.md). 

### <a name="does-powershellcli-work-with-the-new-table-api-preview-"></a>Est-ce que PowerShell/CLI fonctionne avec la nouvelle API Table (préversion) ?
Nous prévoyons d’ajouter la prise en charge de PowerShell/CLI pour l’API Table (préversion). 

### <a name="is-the-concurrency-on-operations-controlled"></a>L’accès concurrentiel sur les opérations est-il contrôlé ?
Oui, un accès concurrentiel optimiste est fourni via l’utilisation du mécanisme d’ETag comme dans l’API Table Standard. 

### <a name="is-odata-query-model-supported-for-entities"></a>Le modèle de requête OData est-il pris en charge pour les entités ? 
Oui, l’API Table (préversion) prend en charge les requêtes Odata et les requêtes Linq. 

### <a name="can-i-connect-to-standard-azure-table-and-the-new-premium-table-api-preview-at-side-by-side-in-same-application-"></a>Puis-je me connecter à un stockage Table Azure Standard et à la nouvelle API Table Premium (préversion) simultanément dans la même application ? 
Oui, vous pouvez le faire en créant deux instances différentes de l’élément CloudTableClient pointant vers leur propre URI via la chaîne de connexion.

### <a name="how-do-i-migrate-existing-table-storage-application-to-this-new-offering"></a>Comment migrer une application de stockage Table existante vers cette nouvelle offre ?
Si vous souhaitez tirer parti de la nouvelle offre d’API Table avec vos données de stockage Table existantes, contactez askdocdb@microsoft.com. 

### <a name="what-is-the-roadmap-for-this-service-when-will-other-functionality-of-standard-table-api-be-offered"></a>Quelle est la feuille de route pour ce service ? Quand les autres fonctionnalités de l’API Table Standard seront-elles proposées ?
Nous prévoyons d’ajouter la prise en charge des jetons SAP, du contexte de service, des statistiques, du chiffrement, de l’analyse et d’autres fonctionnalités à mesure que nous progressons vers la disponibilité générale.  N’hésitez pas à nous faire part de vos commentaires via [UserVoice](https://feedback.azure.com/forums/263030-documentdb). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-say-i-start-with-n-gbs-of-data-and-my-data-will-grow-to-1-tb-overtime"></a>Comment l’extension de la taille de stockage est-elle gérée pour ce service, par exemple si le volume de mes données passe de N Go à 1 To avec le temps ?  
Cosmos DB est conçu pour offrir une capacité de stockage illimitée via l’utilisation de la mise à l’échelle horizontale. Notre service surveillera votre stockage et augmentera concrètement la capacité. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Comment faire pour surveiller l’offre d’API Table (préversion) ?
Vous pouvez utiliser le volet Mesures de l’API Table (préversion) pour surveiller les requêtes et l’utilisation du stockage. 

### <a name="how-do-i-calculate-throughput-i-require"></a>Comment calculer le débit dont j’ai besoin ?
Vous pouvez utiliser l’estimateur de capacité pour calculer le débit de table nécessaire pour les opérations, comme expliqué [ici](https://www.documentdb.com/capacityplanner). En général, vous pouvez représenter votre entité sous forme de document JSON et fournir les valeurs requises pour vos opérations. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Puis-je utiliser le nouveau Kit de développement logiciel (SDK) de l’API Table (préversion) localement avec l’émulateur ?
Oui, vous pouvez utiliser l’API Table (préversion) sur l’émulateur local avec le nouveau SDK. Téléchargez le nouvel émulateur [ici](documentdb-nosql-local-emulator.md). La valeur de StorageConnectionString dans le fichier app.config doit être « DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081 ». 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>Mon application existante peut-elle fonctionner avec l’API Table (préversion) ? 
La surface d’exposition de la nouvelle API Table (préversion) est compatible avec le Kit de développement logiciel (SDK) Table Standard Azure existant pour les opérations de création, de suppression, de mise à jour et de requête avec l’API .NET. Assurez-vous que vous disposez d’une clé de ligne, car l’API Table (préversion) nécessite à la fois une clé de partition et une clé de ligne. Nous prévoyons également d’ajouter la prise en charge d’autres SDK à mesure que nous progressons vers la disponibilité générale de cette offre de service.

### <a name="do-i-need-to-migrate-existing-azure-table-based-application-to-new-sdk-if-i-do-not-want-to-use-table-api-preview-features"></a>Ai-je besoin de migrer une application basée sur le stockage Table Azure vers le nouveau Kit de développement logiciel (SDK) si je ne souhaite pas utiliser les fonctionnalités de la nouvelle API Table (préversion) ?
Non, les clients existants peuvent créer et utiliser des ressources de Table Standard actuelles sans la moindre interruption. Toutefois, si vous n’utilisez pas la nouvelle API Table (préversion), vous ne pouvez pas bénéficier de l’indexation automatique, de l’option de cohérence supplémentaire ou de la distribution globale. 

### <a name="how-do-i-add-replication-for-the-data-in-premium-table-api-preview-across-multiple-regions-of-azure"></a>Comment ajouter la réplication pour les données de l’API Table Premium (préversion) dans plusieurs régions Azure ?
Vous pouvez utiliser les [paramètres de réplication mondiale](documentdb-portal-global-replication.md) du portail Cosmos DB afin d’ajouter des régions appropriées pour votre application. Pour développer une application mondialement distribuée, vous devez également ajouter votre application avec les informations PreferredLocation définies sur la région locale afin de bénéficier d’une faible latence de lecture. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in--premium-table-apipreview"></a>Comment modifier la région d’écriture principale du compte dans l’API Table Premium (préversion) ?
Vous pouvez utiliser le volet du portail de réplication mondiale de Cosmos DB pour ajouter une région, puis basculer vers la région requise. Pour connaître la marche à suivre, consultez l’article [Developing with multi-region Azure Cosmos DB accounts](documentdb-regional-failovers.md) (Développement avec des comptes Azure Cosmos DB multirégions). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Comment configurer mes régions de lecture préférées pour une faible latence lors de la distribution de mes données ? 
Vous devez tirer parti de la clé PreferredLocation dans le fichier app.config pour effectuer les lectures à partir de l’emplacement local. Pour une application existante, l’API Table (préversion) lèvera une erreur si LocationMode est défini. Supprimez ce code, sinon l’API Table Premium (préversion) récupèrera cette information dans le fichier app.config.  Ces paramètres sont documentés dans [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).


### <a name="how-do-i-think-of-consistency-in-table-api-preview"></a>Comment la cohérence est-elle gérée dans l’API Table (préversion) ? 
Cosmos DB offre un bon compromis entre cohérence, disponibilité et latence. À ce jour, ce service permet de choisir parmi 5 niveaux de cohérence. Ces niveaux de cohérence sont disponibles pour les développeurs qui utilisent l’API Table (préversion). Ils peuvent ainsi choisir le modèle de cohérence approprié au niveau de la table et d’une requête individuelle lors de l’interrogation des données.  Lorsqu’un client se connecte, il peut spécifier un niveau de cohérence. Cela peut être modifié via le paramètre du fichier app.config pour la valeur de la clé TableConsistencyLevel. L’API Table (préversion) offre des lectures à faible latence avec lecture de vos propres écritures et cohérence de session par défaut. Pour plus d’informations, consultez [Niveaux de cohérence](documentdb-consistency-levels.md). Par défaut, l’API Table Standard offre aujourd’hui une cohérence forte au sein d’une région et une cohérence éventuelle dans les emplacements secondaires.  

### <a name="does-this-mean-compared-to-eventual--strong-consistency-that-is-possible-with-standard-table---we-now-have-more-choices"></a>Cela signifie-t-il que nous disposons maintenant de plus d’options pour la cohérence ?
Oui, ces options sont documentées dans l’article [Niveaux de cohérence](documentdb-consistency-levels.md) pour aider les développeurs d’application à exploiter la nature distribuée de Cosmos DB. Dans la mesure où des garanties sont également fournies pour la cohérence, vous pouvez tirer parti de ces différents niveaux en toute confiance dès aujourd’hui. Ces paramètres sont documentés dans [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled--how-long-does-it-take-to-replicate-the-data"></a>Une fois la distribution mondiale activée, combien de temps faut-il pour répliquer les données ?
Nous validons les données durablement dans la région locale et transmettons les données vers les autres régions en l’espace de quelques millisecondes. Cette réplication dépend uniquement de la durée des boucles du centre de données. Pour plus d’informations sur les capacités de distribution mondiale de Cosmos DB, consultez [Azure Cosmos DB: A globally distributed database service on Azure](documentdb-distribute-data-globally.md) (Azure Cosmos DB : un service de base de données mondialement distribué sur Azure).

### <a name="can-the-read-request-consistency-be-changed"></a>Est-il possible de modifier la cohérence des requêtes de lecture ?
Cosmos DB permet de définir la cohérence au niveau du conteneur, c’est-à-dire de la table. Le Kit de développement logiciel (SDK) permet également de modifier le niveau en fournissant la valeur de la clé TableConsistencyLevel dans le fichier app.config. Voici les valeurs possibles : Strong (Forte)|Bounded Staleness (Obsolescence limitée)|Session|ConsistentPrefix (Préfixe cohérent)|Eventual (Éventuelle). Pour plus d’informations, consultez l’article [Niveaux de cohérence](documentdb-consistency-levels.md). L’important ici est de se rappeler que le niveau de cohérence des requêtes ne peut pas être supérieur à celui de la table. Par exemple, si vous avez configuré une cohérence éventuelle pour la table, vous ne pouvez pas définir une cohérence forte pour les requêtes. 

### <a name="how-does-premium-table-api-preview-account-take-care-of-failover-in-case-a-region-goes-down"></a>Comment l’API Table Premium (préversion) gère-t-elle le basculement dans le cas où une région rencontre une défaillance ? 
L’API Table Premium (préversion) tire parti de la plate-forme mondialement distribuée de Cosmos DB. Pour vous assurer que votre application peut tolérer des temps d’arrêt de centre de données, vous devez activer au moins une région supplémentaire pour le compte dans le portail Cosmos DB (voir [Developing with multi-region Azure Cosmos DB accounts](documentdb-regional-failovers.md) (Développement avec des comptes Azure Cosmos DB multirégions)). Vous pouvez définir la priorité de la région à l’aide du portail (voir [Developing with multi-region Azure Cosmos DB accounts](documentdb-regional-failovers.md) (Développement avec des comptes Azure Cosmos DB multirégions)). Vous pouvez ajouter autant de régions que nécessaire pour le compte et contrôler l’emplacement de basculement en indiquant la priorité. Bien entendu, vous devez également fournir une application à cet emplacement pour tirer parti de la base de données. De cette façon, vos clients ne subiront pas de temps d’arrêt. Le Kit de développement logiciel (SDK) client prend en charge l’hébergement automatique : il peut détecter la région qui connaît une défaillance et effectuer automatiquement le basculement vers la nouvelle région.

### <a name="is-premium-table-api-preview-enabled-for-backups"></a>L’API Table Premium (préversion) prend-elle en charge les sauvegardes ?
Oui, l’API Table Premium (préversion) tire parti de la plate-forme de Cosmos DB pour les sauvegardes. Les sauvegardes sont effectuées automatiquement. La sauvegarde avec Cosmos DB est documentée dans l’article [Sauvegarde et restauration en ligne automatiques avec Azure Cosmos DB](C:\Users\govindk\azure-docs-pr\articles\documentdb\documentdb-online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-entities-by-default"></a>L’API Table (préversion) indexe-t-elle par défaut tous les attributs des entités ?
Oui, tous les attributs des entités sont indexés par défaut. Les détails de l’indexation sont documentés dans l’article [Stratégies d’indexation d’Azure Cosmos DB](documentdb-indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-different-indexes-to-satisfy-the-queries"></a>Cela signifie-t-il que je n’ai pas besoin de créer des index différents pour satisfaire les requêtes ? 
Oui, Cosmos DB assure l’indexation automatique de tous les attributs sans définition de schéma. Les développeurs n’ont donc pas à se préoccuper de la création et la gestion d’index, et peuvent se concentrer sur l’application à la place. Les détails de l’indexation sont documentés dans l’article [Stratégies d’indexation d’Azure Cosmos DB](documentdb-indexing-policies.md).

### <a name="can-the-indexing-policy-be-changed"></a>La stratégie d’indexation peut-elle être modifiée ?
Oui, vous pouvez modifier l’index en fournissant la définition d’index. La signification de ces paramètres est documentée dans l’article [Fonctionnalités d’Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Vous devez correctement encoder et placer dans une séquence d’échappement ces paramètres.  

Au format JSON de chaîne dans le fichier app.config :
{ "indexingMode": "consistent", "automatic": true, "includedPaths": [ { "path": "/somepath", "indexes": [ { "kind": "Range", "dataType": "Number", "precision": -1 }, { "kind": "Range", "dataType": "String", "precision": -1 } ] } ], "excludedPaths": [ { "path": "/anotherpath" } ] }

### <a name="cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-like-sorting-aggregates-hierarchy-and-other-functionality---is-this-planned-to-be-added-to-the-table-api-"></a>La plateforme de Cosmos DB semble offrir de nombreuses fonctionnalités, telles que le tri, les agrégats et les hiérarchies. Prévoyez-vous de les ajouter à l’API Table ? 
En préversion, Azure Cosmos DB prend en charge les mêmes fonctionnalités de requête que le stockage Table Azure pour l’API Table. Azure Cosmos DB prend également en charge le tri, les agrégats, les requêtes géospatiales, les hiérarchies et un large éventail de fonctions intégrées. Les fonctionnalités supplémentaires seront ajoutées à l’API Table dans une prochaine mise à jour de service. Pour une vue d’ensemble de ces fonctionnalités, consultez [Azure Cosmos DB query](../documentdb/documentdb-sql-query.md) (Requête dans Azure Cosmos DB).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Quand dois-je modifier le débit de table pour l’API Table (préversion) ?
Vous devez modifier le débit de table lorsque vous effectuez l’ETL de données ou souhaitez charger rapidement un grand volume de données. 

OU

Vous avez besoin d’augmenter le débit pour le conteneur au niveau du backend, car vous constatez que le débit utilisé est supérieur au débit approvisionné dans les métriques et vous rencontrez des limitations. Pour plus d’informations, consultez l’article [Définir le débit](documentdb-set-throughput.md).

### <a name="can-i-scale-up-or-down-the-throughput-of-my-table-api-preview-table"></a>Puis-je augmenter ou réduire le débit de mon API Table (préversion) ? 
Oui, vous pouvez utiliser le volet de mise à l’échelle du portail Cosmos DB pour faire de même. Pour plus d’informations, consultez l’article [Définir le débit](documentdb-set-throughput.md).

### <a name="can-premium-table-api-preview-leverage-ru-per-minute-offering"></a>L’API Table Premium (préversion) peut-elle tirer parti de l’offre de RU par minute ? 
Oui, l’API Table Premium (préversion) exploite les fonctionnalités de Cosmos DB afin de fournir des contrats de niveau de service (SLA) pour les performances, la latence, la disponibilité et la cohérence. Elle peut ainsi tirer parti de l’offre de RU par minute, comme décrit dans l’article [Request Units](documentdb-request-units.md) (Unités de requête). Cette fonctionnalité permet au client de ne pas prévoir d’approvisionnement pour les périodes d’activité maximale et de lisser les pics de la charge de travail.

### <a name="is-there-a-default-tablethroughput-which-is-set-for-newly-provisioned-tables"></a>Un débit de table par défaut est-il défini pour les nouvelles tables approvisionnées ?
Oui, si vous ne remplacez pas la valeur de TableThroughput dans le fichier app.config et n’utilisez pas un conteneur créé au préalable dans Cosmos DB, le service crée une table avec un débit de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-standard-table-api"></a>La tarification change-t-elle pour les clients existants de l’API Table Standard ?
Aucune. Le tarif reste inchangé pour les clients de l’API Table Standard existants. 

### <a name="how-is-the-price-calculated-for-the-table-apipreview"></a>Comment le tarif est-il calculé pour l’API Table (préversion) ? 
Cela dépend du débit de table alloué. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Comment gérer les limitations qui affectent les tables de l’offre d’API Table (préversion) ? 
Si le taux de requête dépasse la capacité du débit approvisionné pour le conteneur sous-jacent, vous recevez une erreur et le Kit de développement logiciel (SDK) essaie de renouveler l’appel en suivant la stratégie de nouvelle tentative.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-leverage-premium-table-preview-offering-of-cosmos-db"></a>Pourquoi dois-je choisir un débit en dehors de la clé de partition et de la clé de ligne pour tirer parti de l’offre d’API Table Premium (préversion) de Cosmos DB ?
Si vous ne fournissez pas de débit par défaut pour votre conteneur dans le fichier app.config, Cosmos DB en définit un. 

Cosmos DB fournit des garanties de performances et de latence avec des limites supérieures pour les opérations. Cela est possible lorsque le moteur peut appliquer la gouvernance sur les opérations du client. La définition du débit de table vous assure essentiellement un débit et une latence garantis, car la plateforme réserve désormais cette capacité et garantit la réussite des opérations.  
La spécification du débit vous permet également de le modifier en toute flexibilité pour tirer parti de la saisonnalité de votre application, répondre aux besoins en matière de débit et réduire les coûts.

### <a name="azure-storage-sdk-has-been-very-cheap-for-me-as-i-only-pay-to-store-the-data-and-i-rarely-query-cosmos-dbs-new-offering-seems-to-be-charging-me-even-though-i-have-not-performed-single-transaction-or-stored-anything-whats-going-on-here"></a>Le Kit de développement logiciel (SDK) Stockage Azure est très économique pour moi, car je paie uniquement pour le stockage des données et j’exécute rarement des requêtes. La nouvelle offre de Cosmos DB semble me facturer des frais alors que je n’ai pas effectué la moindre transaction ou stocké quoi que ce soit. Comment cela se fait-il ?

Cosmos DB a été conçu pour offrir un système mondialement distribué basé sur des contrats de niveau de service (SLA) avec des garanties de disponibilité, de latence et de débit. Lorsque vous réservez un débit dans Cosmos DB, celui-ci est garanti, à la différence des autres systèmes. Cosmos DB fournit également des fonctionnalités supplémentaires réclamées depuis longtemps par les clients, telles que les index secondaires et la distribution mondiale. Pendant la durée de la préversion, nous fournissons un modèle optimisé pour le débit, mais à terme, nous prévoyons de fournir un modèle optimisé pour le stockage afin de répondre aux besoins de nos clients.  

### <a name="i-never-get-quota-full-indicating-a-partition-is-full-when-i-keep-ingesting-data-into-azure-table-storage-with-the-table-apipreview--i-can-get-this-error-is-this-offering-limiting-me-and-forcing-me-to-change-my-present-application"></a>Je n’ai jamais de « quota atteint » (indiquant qu’une partition est pleine) lorsque j’ingère continuellement des données dans le stockage Table Azure. Avec l’API Table (préversion), il m’arrive d’obtenir cette erreur. Est-ce que cette offre me limite et m’oblige à modifier mon application actuelle ?

Cosmos DB est un système basé sur des contrats de niveau de service (SLA) qui offre une mise à l’échelle illimitée avec des garanties de latence, de débit, de disponibilité et de cohérence. Pour bénéficier de performances premium garanties, vous devez vous assurer que la taille des données et l’index sont gérables et évolutifs. La limite de 10 Go sur le nombre d’entités/éléments par clé de partition nous permet de garantir des performances de recherche et de requête exceptionnelles. Afin que votre application offre une bonne évolutivité, nous vous demandons ne pas de créer de partition sensible en stockant toutes les informations dans une seule partition et en interrogeant cette dernière.  

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>Si je comprends bien, la clé de partition et la clé de ligne sont toujours requises avec la nouvelle API Table (préversion) ? 
Oui. Comme la surface d’exposition de l’API Table (préversion) est semblable à celle du Kit de développement logiciel (SDK) Stockage Table Azure, la clé de partition offre un excellent moyen de distribuer les données. La clé de ligne est unique au sein de cette partition. La clé de ligne doit être présente et ne peut pas avoir la valeur null comme dans le SDK Standard. Dans la préversion de la build, la longueur de la clé de ligne est de 255 octets et celle de la clé de partition de 100 octets (bientôt 1 Ko). 

### <a name="what-will-be-the-error-messages-of-table-api-preview-"></a>Quels seront les messages d’erreur de l’API Table (préversion) ?
Étant donné que cette préversion est compatible avec l’API Table Standard, la majorité des erreurs correspondront aux erreurs de cette dernière. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another--in-tables-api-preview"></a>Pourquoi suis-je limité lorsque j’essaie de créer de nombreuses tables successivement dans l’API Table (préversion) ?
Cosmos DB est un système basé sur des contrats de niveau de service (SLA) qui offre des garanties de latence, de débit, de disponibilité et de cohérence. Comme il s’agit d’un système approvisionné, il réserve des ressources afin de respecter ces exigences. Le taux rapide de création de tables est détecté et limité. Nous vous conseillons d’examiner le taux de création de tables et de lui donner une valeur inférieure à 5 par minute. Gardez à l’esprit que l’API Table (préversion) est un système approvisionné, ce qui signifie qu’à partir du moment où vous l’approvisionnez, vous devez payer pour en profiter.  

# <a name="graph-apipreview"></a>API Graph (préversion)
## <a name="database-questions-about-developing-against-graph-apipreview"></a>Questions à propos du développement avec l’API Graph (préversion)
### <a name="how-can-i-leverage-graph-api-preview-with-cosmos-db"></a>Comment puis-je tirer parti de l’API Graph (préversion) avec Cosmos DB ?
Vous pouvez tirer parti des fonctionnalités de l’API Graph (préversion) à partir d’une bibliothèque d’extension. Celle-ci porte le nom de Microsoft Azure Graphs et est disponible dans NuGet. 

### <a name="it-looks-like-you-support-gremlin-as-traversal-language-do-you-plan-to-add-some-more-forms-of-query-"></a>Apparemment, vous prenez en charge Gremlin en tant que langage de traversée. Prévoyez-vous d’ajouter d’autres formes de requête ?
Oui, nous prévoyons d’ajouter d’autres mécanismes de requête ultérieurement. 

### <a name="how-can-i-use-the-new-graph-apipreview-offering"></a>Comment puis-je utiliser la nouvelle offre d’API Graph (préversion) ? 
Pour vous lancer, suivez le [guide de démarrage rapide de l’API Graph](../cosmos-db/create-graph-dotnet.md).




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

