<properties 
	pageTitle="Questions sur le service de base de données DocumentDB - Forum Aux Questions (FAQ) | Microsoft Azure" 
	description="Obtenez des réponses aux questions les plus fréquentes à propos du service de base de données de documents NoSQL DocumentDB Azure pour JSON. Répondez aux questions sur la capacité, les niveaux de performance et l’évolutivité de la base de données." 
	keywords="Questions sur la base de données, Forum aux questions, documentdb, azure, Microsoft azure"
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
	ms.date="07/01/2016" 
	ms.author="mimig"/>


#Forum Aux Questions (FAQ) sur DocumentDB

## Questions à propos des concepts de base de Microsoft Azure DocumentDB

### Qu'est-ce que Microsoft Azure DocumentDB ? 
Microsoft Azure DocumentDB est un service de base de données NoSQL orienté documents, à l’échelle mondiale, très rapide, qui propose des requêtes enrichies plutôt que des données sans schéma. Il offre des options de configuration et des performances fiables, tout en permettant un développement rapide. Tout cela est rendu possible grâce à une plateforme gérée, soutenue par la puissance et la portée de Microsoft Azure. DocumentDB est la solution idéale pour les applications web, mobiles, de jeu et IoT lorsque le débit, la haute disponibilité, la faible latence et le modèle de données sans schéma sont primordiaux. DocumentDB offre une flexibilité des schémas et une indexation riche par le biais d'un modèle de données JSON natif. Il inclut également un support transactionnel multi-documents avec JavaScript intégré.
  
Pour plus de questions, de réponses et d’instructions sur le déploiement et l’utilisation de ce service, consultez la [page de documentation de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### Quel type de base de données est DocumentDB ?
DocumentDB est une base de données NoSQL orientée documents qui stocke les données au format JSON. DocumentDB prend en charge les structures imbriquées et autonomes qui peuvent être interrogées grâce à une [syntaxe de requête SQL](documentdb-sql-query.md) DocumentDB enrichie. DocumentDB offre un traitement transactionnel haute performance JavaScript côté serveur par le biais de [procédures stockées, de déclencheurs et de fonctions définies par l’utilisateur](documentdb-programming.md). La base de données prend également en charge des niveaux de cohérence ajustables avec les [niveaux de performances](documentdb-performance-levels.md) associés.
 
### Les bases de données DocumentDB ont-elles des tables comme une base de données relationnelle (SGBDR) ?
Non, DocumentDB stocke les données dans des collections sous forme de documents JSON. Pour plus d’informations sur les ressources de DocumentDB, consultez la rubrique [Modèle de ressource et concepts de DocumentDB](documentdb-resources.md). Pour plus d’informations sur les différences entre les solutions NoSQL comme DocumentDB et les solutions relationnelles, consultez [NoSQL et SQL](documentdb-nosql-vs-sql.md).

### Les bases de données DocumentDB prennent-elles en charge les données sans schéma ?
Oui, DocumentDB permet aux applications de stocker des documents JSON arbitrairement sans définition de schéma ni indice. Les données peuvent être interrogées immédiatement via l'interface de requête SQL de DocumentDB.

### DocumentDB prend-il en charge les transactions ACID ?
Oui, DocumentDB prend en charge les transactions entre documents exprimées en procédures stockées et déclencheurs JavaScript. Les transactions sont étendues à une seule partition au sein de chaque collection et exécutées intégralement avec des sémantiques ACID, isolées du code et des requêtes utilisateur en cours d’exécution. Si des exceptions surviennent lors de l'exécution du code d'application JavaScript côté serveur, la transaction entière est annulée. Pour plus d’informations sur les transactions, consultez [Transactions de programme de base de données](documentdb-programming.md#database-program-transactions).

### Dans quels cas utilise-t-on généralement DocumentDB ?  
DocumentDB est une option conseillée pour les nouvelles applications web, mobiles, de jeu et IoT dans lesquelles la mise à l’échelle automatique, les performances prévisibles, la commande rapide de temps de réponse en millisecondes, et la capacité à interroger sur des données sans schéma sont importants. DocumentDB permet un développement rapide et prend en charge l'itération continue des modèles de données d'application. Les applications qui gèrent du contenu généré par l’utilisateur et des données sont [communément utilisées dans DocumentDB](documentdb-use-cases.md).

### Comment DocumentDB offre-t-il des performances prévisibles ?
Une [unité de requête](documentdb-request-units.md) est la mesure du débit dans DocumentDB. 1 RU correspond au débit de la requête GET d’un document d’1 Ko. Chaque opération dans DocumentDB, y compris les lectures, les écritures, les requêtes SQL et les exécutions de procédures stockées, comporte une valeur d’unité de requête déterministe basée sur le débit nécessaire pour terminer l’opération. Au lieu de penser à l’UC, à l’E/S et à la mémoire, et à la façon dont ils impactent le débit de votre application, vous pouvez penser en termes de mesure d’unité de requête unique.

Chaque collection DocumentDB peut être réservée avec un débit approvisionné en termes d’unités de requête de débit par seconde. Pour les applications quelle que soit leur échelle, vous pouvez évaluer les requêtes individuelles pour mesurer leur valeur d’unités de requête, et approvisionner les collections pour gérer la somme totale des unités de requête sur l’ensemble des requêtes. Vous pouvez également mettre à l’échelle le débit de votre collection à mesure de l’évolution des besoins de votre application. Pour plus d’informations sur les unités de requête et pour obtenir de l’aide afin de déterminer vos besoins en collections, consultez [Gérer les performances et les capacités](documentdb-manage.md) et [Calculatrice de débit](https://www.documentdb.com/capacityplanner).

### DocumentDB est-il conforme à HIPAA ?
Oui, DocumentDB est conforme à HIPAA. HIPAA établit les conditions requises pour l’utilisation, la divulgation et la protection des informations de santé identifiables de façon individuelle. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/fr-FR/TrustCenter/Compliance/HIPAA).

### Quelles sont les limites de stockage de DocumentDB ? 
Il n’existe aucune limite théorique à la quantité totale de données qu’une collection peut stocker dans DocumentDB. Si vous souhaitez stocker plus de 250 Go de données dans une seule collection, [contactez le support technique](documentdb-increase-limits.md) pour augmenter le quota de votre compte.

### Quelles sont les limites de débit de DocumentDB ? 
Il n’existe aucune limite théorique à la quantité totale de débit qu’une collection peut prendre en charge dans DocumentDB, si votre charge de travail peut être distribuée à peu près uniformément entre un nombre suffisant de clés de partition. Si vous devez dépasser 250 000 unités de requêtes/seconde par collection ou compte, [contactez le support technique](documentdb-increase-limits.md) pour augmenter le quota de votre compte.

### Combien coûte Microsoft Azure DocumentDB ?
Pour plus d’informations, consultez la page [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/). Les frais d’utilisation de DocumentDB sont déterminés par le nombre de collections utilisées, le nombre d’heures durant lequel les collections sont en ligne, le stockage utilisé et le débit approvisionné pour chaque collection.

### Un compte gratuit est-il disponible ?
Si vous débutez avec Azure, vous pouvez vous inscrire pour bénéficier d’un [compte Azure gratuit](https://azure.microsoft.com/free/), qui vous offre 30 jours et 200 USD pour essayer tous les services Azure. Ou, si vous possédez un abonnement Visual Studio, vous pouvez bénéficier de [150 USD de crédits Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), à utiliser sur n’importe quel service Azure.

### Comment puis-je obtenir une aide supplémentaire avec DocumentDB ?
Si vous avez besoin d’aide, contactez-nous sur [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), les [Forums de développeurs Azure DNS DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) ou planifiez une [conversation 1:1 avec l’équipe d’ingénierie de DocumentDB](http://www.askdocdb.com/). Pour vous tenir informé des dernières nouveautés et fonctionnalités de DocumentDB, suivez-nous sur [Twitter](https://twitter.com/DocumentDB).

## Configuration de Microsoft Azure DocumentDB

### Comment m'inscrire à Microsoft Azure DocumentDB ?
Microsoft Azure DocumentDB est disponible dans le [portail Azure][azure-portal]. Vous devez d'abord disposer d'un abonnement à Microsoft Azure. Une fois inscrit à Microsoft Azure, vous pouvez ajouter un compte DocumentDB à votre abonnement Azure. Pour obtenir des instructions sur l’ajout d’un compte DocumentDB, consultez la page [Création d’un compte de base de données DocumentDB](documentdb-create-account.md).

### Qu'est-ce que la clé principale ?
Une clé principale est un jeton de sécurité permettant d'accéder à toutes les ressources d'un compte. Les personnes disposant de cette clé ont un accès en lecture et en écriture à toutes les ressources du compte de la base de données. Faites preuve de précaution lorsque vous distribuez des clés principales. La clé principale primaire et la clé principale secondaire sont disponibles dans le panneau **Clés** du [portail Azure][azure-portal]. Pour plus d’informations sur les clés, consultez la rubrique [Affichage, copie et régénération de clés d’accès](documentdb-manage-account.md#keys).

### Comment créer une base de données ?
Vous pouvez créer des bases de données à l’aide du [portail Azure](), comme décrit dans la rubrique [Création d’une base de données DocumentDB](documentdb-create-database.md), à l’aide d’un des [Kits de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md) ou au moyen des [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### Qu'est-ce qu'une collection ?
Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le débit et le stockage utilisés. Les collections peuvent couvrir une ou plusieurs partitions/serveurs et peuvent être mises à l’échelle pour gérer des volumes de stockage ou de débit quasi-illimités.

Les collections sont également les entités de facturation de DocumentDB. Chaque collection est facturée sur la base du débit approvisionné et de l’espace de stockage utilisé. Pour plus d’informations, consultez [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).

### Comment configurer des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et des autorisations en utilisant un [Kit de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md) ou des [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Questions à propos du développement avec Microsoft Azure DocumentDB

### Comment développer avec DocumentDB ?
Des [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) sont disponibles pour .NET, Python, Node.js, JavaScript et Java. Les développeurs peuvent également utiliser les [API RESTful HTTP](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour interagir avec les ressources DocumentDB sur de nombreuses plateformes et dans de nombreux langages.

Des exemples de kits de développement logiciel (SDK) DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) et [Python](documentdb-python-samples.md) sont disponibles sur GitHub.

### DocumentDB prend-il en charge SQL ?
Le langage de requête SQL DocumentDB est un sous-ensemble amélioré des fonctionnalités de requête prises en charge par SQL. Le langage de requête SQL de DocumentDB fournit des opérateurs relationnels et hiérarchiques enrichis, ainsi qu'une extensibilité, via des fonctions JavaScript définies par l'utilisateur. La syntaxe JSON permet la modélisation de documents JSON en tant qu'arborescences avec des étiquettes en tant que nœuds d'arborescence. Cette capacité est exploitée par les techniques d'indexation automatique de DocumentDB et le langage de requête SQL de ce dernier. Pour plus d’informations sur l’utilisation de la syntaxe SQL, consultez l’article [Interrogation de DocumentDB][query].

### Quels sont les types de données pris en charge par DocumentDB ?
Les types de données primitifs pris en charge dans DocumentDB sont identiques à ceux pris en charge dans JSON. JSON dispose d'un système de type simple constitué de chaînes, de chiffres (double précision IEEE754), ainsi que de valeurs booléennes (true, false et null). Des types de données plus complexes, tels que DateTime, Guid, Int64 et Geometry peuvent être représentés dans JSON et DocumentDB en créant des objets imbriqués à l'aide de l'opérateur { } et des tableaux à l'aide de l'opérateur [ ].

### Quels sont les avantages concurrentiels de DocumentDB ?
DocumentDB prend en charge le contrôle d’accès concurrentiel optimiste via les balises d’entité HTTP ou etags. Chaque ressource DocumentDB dispose d’une balise etag et l’etag est définie sur le serveur à chaque fois qu’un document est mis à jour. L’en-tête de l’etag et la valeur actuelle sont inclus dans tous les messages de réponse. Les balises etags peuvent être utilisées avec l’en-tête If-Match pour permettre au serveur de déterminer si une ressource doit être mise à jour. La valeur If-Match est la valeur etag utilisée pour la vérification. Si la valeur etag correspond à la valeur etag du serveur, la ressource sera mise à jour. Si la balise etag n’est plus active, le serveur rejette l’opération avec un code de réponse « HTTP 412 Échec de la condition préalable ». Le client doit alors récupérer à nouveau la ressource pour obtenir la valeur etag actuelle pour la ressource. En outre, les balises etags peuvent être utilisées avec un en-tête If-None-Match pour déterminer si la nouvelle extraction d’une ressource est nécessaire.

Pour utiliser l’accès concurrentiel optimiste dans .NET, utilisez la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx). Pour voir un exemple .NET, consultez [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans l’exemple DocumentManagement sur github.

### Comment effectuer des transactions dans DocumentDB ?
DocumentDB prend en charge les transactions intégrées au langage via les déclencheurs et procédures stockées JavaScript. Toutes les opérations de base de données dans les scripts sont exécutées avec l’isolement de capture instantanée étendu à la collection s’il s’agit d’une collection à partition unique, ou à des documents ayant la même valeur de clé de partition au sein d’une collection, si la collection est partitionnée. Une capture instantanée des versions des documents (ETags) est prise au début de la transaction et validée uniquement si le script fonctionne. Si le JavaScript renvoie une erreur, la transaction est annulée. Pour plus d’informations, consultez la rubrique [Programmation côté serveur dans DocumentDB](documentdb-programming.md).

### Comment insérer des documents en bloc dans DocumentDB ? 
Il existe trois façons d’insérer des documents en bloc dans DocumentDB :

- L’outil de migration de données, décrit dans la rubrique [Importation de données vers DocumentDB](documentdb-import-data.md).
- L’Explorateur de documents dans le portail Azure, décrit dans la rubrique [Ajouter des documents en bloc à l’aide de l’Explorateur de documents](documentdb-view-json-document-explorer.md#BulkAdd).
- Les procédures stockées, décrites dans la rubrique [Programmation DocumentDB côté serveur](documentdb-programming.md).

### DocumentDB prend-il en charge la mise en cache des liens de ressource ?
Oui. DocumentDB étant un service RESTful, les liens de ressource sont immuables et peuvent être mis en cache. Les clients DocumentDB peuvent spécifier un en-tête « If-None-Match » pour des lectures en comparaison avec des ressources telles que des documents ou des collections. Ils peuvent également mettre à jour les copies locales uniquement lorsque la version du serveur a été modifiée.

### Une instance locale de DocumentDB est-elle disponible ?
À ce stade, une instance locale de DocumentDB n’est pas disponible. Vous pouvez suivre l’état d’un émulateur local et voter pour celui-ci sur le [forum de commentaires](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0706_2016-->