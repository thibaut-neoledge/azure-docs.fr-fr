<properties 
	pageTitle="Questions sur le service de base de données DocumentDB - Forum Aux Questions (FAQ) | Microsoft Azure" 
	description="Obtenez des réponses aux questions les plus fréquentes à propos du service de base de données de documents NoSQL DocumentDB Azure. Répondez aux questions sur la capacité, les niveaux de performance et l’évolutivité de la base de données." 
	keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="mimig"/>


#Forum Aux Questions (FAQ) sur DocumentDB

## Questions à propos des concepts de base de Microsoft Azure DocumentDB

### Qu'est-ce que Microsoft Azure DocumentDB ? 
Microsoft Azure DocumentDB est un service de base de données NoSQL orienté documents très évolutif qui propose des requêtes enrichies plutôt que des données sans schéma. Il offre des options de configuration et des performances fiables, tout en permettant un développement rapide. Tout cela est rendu possible grâce à une plateforme gérée, soutenue par la puissance et la portée de Microsoft Azure. DocumentDB est la solution idéale pour les applications web et mobiles lorsque le débit, la faible latence et le modèle de données sans schéma sont primordiaux. DocumentDB offre une flexibilité des schémas et une indexation riche par le biais d'un modèle de données JSON natif. Il inclut également un support transactionnel multi-documents avec JavaScript intégré.
  
Pour plus de questions, de réponses et d’instructions sur le déploiement et l’utilisation de ce service, consultez la [page de documentation de DocumentDB](http://azure.microsoft.com/documentation/services/documentdb/).

### Quel type de base de données est DocumentDB ?
DocumentDB est une base de données NoSQL orientée documents qui stocke les données au format JSON. DocumentDB prend en charge les structures imbriquées et autonomes qui peuvent être interrogées grâce à une [syntaxe de requête SQL](documentdb-sql-query.md) DocumentDB enrichie. DocumentDB offre un traitement transactionnel haute performance JavaScript côté serveur par le biais de [procédures stockées, de déclencheurs et de fonctions définies par l'utilisateur](documentdb-programming.md). La base de données prend également en charge des niveaux de cohérence ajustables avec les [niveaux de performances](documentdb-performance-levels.md) associés.
 
### Les bases de données DocumentDB ont-elles des tables comme une base de données relationnelle (SGBDR) ?
Non, DocumentDB stocke les données dans des collections sous forme de documents JSON. Pour plus d'informations sur les ressources de DocumentDB, consultez la rubrique [Modèle de ressource et concepts de DocumentDB](documentdb-resources.md).

### Les bases de données DocumentDB prennent-elles en charge les données sans schéma ?
Oui, DocumentDB permet aux applications de stocker des documents JSON arbitrairement sans définition de schéma ni indice. Les données peuvent être interrogées immédiatement via l'interface de requête SQL de DocumentDB.

### DocumentDB prend-il en charge les transactions ACID ?
Oui, DocumentDB prend en charge les transactions entre documents exprimées en procédures stockées et déclencheurs JavaScript. Les transactions sont étendues à une seule collection et exécutées intégralement avec des sémantiques ACID, isolées du code et des requêtes utilisateur en cours d'exécution. Si des exceptions surviennent lors de l'exécution du code d'application JavaScript côté serveur, la transaction entière est annulée.

### Dans quels cas utilise-t-on généralement DocumentDB ?  
DocumentDB est le choix idéal pour les nouvelles applications web et mobiles lorsque l'évolutivité, les performances et la possibilité d'interroger des données sans schéma sont primordiales. DocumentDB permet un développement rapide et prend en charge l'itération continue des modèles de données d'application. Les applications qui gèrent du contenu généré par l'utilisateur et des données sont [communément utilisées dans DocumentDB](documentdb-use-cases.md).

### Quelles sont les limites de mise à l’échelle de DocumentDB ?
Les comptes DocumentDB peuvent être mis à l’échelle en termes de stockage et de débit en ajoutant des collections. Consultez la rubrique [Limites de DocumentDB](documentdb-limits.md) pour connaître les quotas de service du nombre de collections. S’il vous faut des collections supplémentaires, [contactez le support technique](documentdb-increase-limits.md) pour augmenter le quota de votre compte.

### Combien coûte Microsoft Azure DocumentDB ?
Pour plus d’informations, consultez la page [Tarification de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317). Les frais d'utilisation de DocumentDB sont déterminés par le nombre de collections utilisées, le nombre d'heures durant lequel les collections sont en ligne, et le [niveau de performances](documentdb-performance-levels.md) de chaque collection.

### Existe-t-il une version d'évaluation gratuite ?
Si vous débutez avec Azure, vous pouvez vous inscrire pour bénéficier d’une [version d'évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/), qui vous offre 30 jours et 200 USD pour essayer tous les services Azure. Ou, si vous possédez un abonnement MSDN, vous pouvez bénéficier de [150 USD de crédits Azure gratuits par mois](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), à utiliser sur n'importe quel service Azure.

### Comment puis-je obtenir une aide supplémentaire avec DocumentDB ?
Si vous avez besoin d'aide, contactez-nous sur [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), les [Forums de développeurs Azure DNS DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) ou planifiez une [conversation en tête-à-tête avec l'équipe d'ingénierie de DocumentDB](http://www.askdocdb.com/). Pour vous tenir informé des dernières nouveautés et fonctionnalités de DocumentDB, suivez-nous sur [Twitter](https://twitter.com/DocumentDB).

## Configuration de Microsoft Azure DocumentDB

### Comment m'inscrire à Microsoft Azure DocumentDB ?
Microsoft Azure DocumentDB est disponible dans le [portail Azure en version préliminaire][azure-portal]. Vous devez d'abord disposer d'un abonnement à Microsoft Azure. Une fois inscrit à Microsoft Azure, vous pouvez ajouter un compte DocumentDB à votre abonnement Azure. Pour obtenir des instructions sur l'ajout d'un compte DocumentDB, consultez la page [Création d'un compte de base de données DocumentDB](documentdb-create-account.md).

### Qu'est-ce que la clé principale ?
Une clé principale est un jeton de sécurité permettant d'accéder à toutes les ressources d'un compte. Les personnes disposant de cette clé ont un accès en lecture et en écriture à toutes les ressources du compte de la base de données. Faites preuve de précaution lorsque vous distribuez des clés principales. La clé principale primaire et la clé principale secondaire sont disponibles dans le panneau **Keys **du [portail Azure en version préliminaire][azure-portal]. Pour plus d'informations sur les clés, consultez la rubrique [Affichage, copie et régénération de clés d'accès](documentdb-manage-account.md#keys).

### Comment créer une base de données ?
Vous pouvez créer des bases de données à l'aide du [portail Azure en version préliminaire](), comme décrit dans la rubrique [Création d'une base de données DocumentDB](documentdb-create-database.md), à l'aide d'un des [Kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) ou via les [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### Qu'est-ce qu'une collection ?
Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Les requêtes et les transactions sont étendues vers les collections. Vous pouvez stocker un ensemble de documents JSON hétérogènes dans une seule collection. Tous seront indexés automatiquement.

Les collections sont également les entités de facturation de DocumentDB. Vos frais d'utilisation mensuels de DocumentDB sont déterminés par le nombre de collections utilisées, le nombre d'heures durant lesquelles les collections sont en ligne et le [niveau de performances](documentdb-performance-levels.md) de chaque collection. Pour plus d’informations, consultez [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).

### Quelles sont les limites des bases de données et des collections ?
Chaque collection est fournie avec une allocation de stockage de base de données et de débit configuré à l'un des [niveaux de performances](documentdb-performance-levels.md) pris en charge. Des quotas existent également pour chaque ressource gérée par le service. Pour obtenir la liste de toutes les limites, consultez la rubrique [Limites de DocumentDB](documentdb-limits.md). Pour demander une modification des limites de votre compte, consultez la rubrique [Demande d'augmentation des limites de compte DocumentDB](documentdb-increase-limits.md).

### Comment configurer des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et des autorisations en utilisant un [Kit de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) ou des [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Questions à propos du développement avec Microsoft Azure DocumentDB

### Comment développer avec DocumentDB ?
Des [Kits de développement logiciel (SDK)](https://msdn.microsoft.com/library/azure/dn781482.aspx) sont disponibles pour .NET, Python, Node.js, JavaScript et Java. Les développeurs peuvent également utiliser les [API RESTful HTTP](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour interagir avec les ressources DocumentDB sur de nombreuses plateformes et dans de nombreux langages.

Des exemples de Kits de développement logiciel (SDK) DocumentDB [.NET](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples) et [Python](https://github.com/Azure/azure-documentdb-python) sont disponibles sur GitHub.

### DocumentDB prend-il en charge SQL ?
Le langage de requête SQL de DocumentDB fournit des opérateurs relationnels et hiérarchiques enrichis, ainsi qu'une extensibilité, via des fonctions JavaScript définies par l'utilisateur. La syntaxe JSON permet la modélisation de documents JSON en tant qu'arborescences avec des étiquettes en tant que nœuds d'arborescence. Cette capacité est exploitée par les techniques d'indexation automatique de DocumentDB et le langage de requête SQL de ce dernier. Pour plus d'informations sur l'utilisation de la syntaxe SQL, consultez l'article [Interrogation de DocumentDB][query].

### Quels sont les types de données pris en charge par DocumentDB ?
Les types de données primitifs pris en charge dans DocumentDB sont identiques à ceux pris en charge dans JSON. JSON dispose d'un système de type simple constitué de chaînes, de chiffres (double précision IEEE754), ainsi que de valeurs booléennes (true, false et null). Des types de données plus complexes, tels que DateTime, Guid, Int64 et Geometry peuvent être représentés dans JSON et DocumentDB en créant des objets imbriqués à l'aide de l'opérateur { } et des tableaux à l'aide de l'opérateur [ ].

### Quels sont les avantages concurrentiels de DocumentDB ?
DocumentDB prend en charge le contrôle d'accès concurrentiel optimiste via les balises d'entité HTTP ou ETags. Chaque ressource DocumentDB dispose d'un ETag et les clients DocumentDB incluent leur dernière version lue dans les requêtes en écriture. Si l'ETag est actuel, la modification est effectuée. Si la valeur a été modifiée en externe, le serveur rejette l'écriture avec un code de réponse « HTTP 412 Échec de la condition préalable ». Les clients doivent lire la dernière version de la ressource et relancer la requête.

### Comment effectuer des transactions dans DocumentDB ?
DocumentDB prend en charge les transactions intégrées au langage via les déclencheurs et procédures stockées JavaScript. Toutes les opérations de la base de données dans les scripts sont exécutées avec un isolement de capture instantanée sur la collection. Une capture instantanée des versions des documents (ETags) est prise au début de la transaction et validée uniquement si le script fonctionne. Si le JavaScript renvoie une erreur, la transaction est annulée. Pour plus d’informations, consultez la rubrique [Programmation côté serveur dans DocumentDB](documentdb-programming.md).

### Comment insérer des documents en bloc dans DocumentDB ? 
Il existe trois façons d’insérer des documents en bloc dans DocumentDB :

- L'outil de migration de données, comme décrit dans la rubrique [Importation de données vers DocumentDB](documentdb-import-data.md).
- L'Explorateur de documents dans le portail Azure en version préliminaire, comme décrit dans la rubrique [Ajouter des documents en bloc à l'aide de l'Explorateur de documents](documentdb-view-json-document-explorer.md#BulkAdd).
- Les procédures stockées, comme décrit dans la rubrique [Programmation DocumentDB côté serveur](documentdb-programming.md).

### DocumentDB prend-il en charge la mise en cache des liens de ressource ?
Oui. DocumentDB étant un service RESTful, les liens de ressource sont immuables et peuvent être mis en cache. Les clients DocumentDB peuvent spécifier un en-tête « If-None-Match » pour des lectures en comparaison avec des ressources telles que des documents ou des collections. Ils peuvent également mettre à jour les copies locales uniquement lorsque la version du serveur a été modifiée.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=Sept15_HO3-->