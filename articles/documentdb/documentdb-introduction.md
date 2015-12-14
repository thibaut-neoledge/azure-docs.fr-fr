<properties 
	pageTitle="Présentation de DocumentDB : une base de données JSON | Microsoft Azure" 
	description="Découvrez Azure DocumentDB, une base de données NoSQL JSON. Cette base de données de document est conçue pour le Big Data, une évolutivité élastique et une haute disponibilité." 
	keywords="base de données JSON, base de données Document"
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
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# Présentation de DocumentDB : une base de données NoSQL JSON

DocumentDB est une base de données de documents NoSQL pour les solutions Big Data avec des données JSON qui requièrent une simple mise à l'échelle et une haute disponibilité.

Les trois étapes suivantes permettent de découvrir JSON et de le voir à l’œuvre rapidement :

1. Regardez la vidéo [What is DocumentDB?](http://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) de deux minutes, qui présente les avantages liés à l'utilisation de DocumentDB.
2. Regardez la vidéo [Create DocumentDB on Azure](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) de trois minutes, qui présente la prise en main de DocumentDB à l'aide du portail Azure.
3. Visitez le [Query Playground](http://www.documentdb.com/sql/demo) où vous pouvez utiliser différentes activités pour découvrir les riches fonctionnalités de requêtes disponibles dans DocumentDB. Ensuite, cliquez sur l'onglet Sandbox, exécutez vos propres requêtes SQL personnalisées et essayez DocumentDB.

Ensuite, revenez à cet article, où nous approfondissons la question pour vous fournir les réponses aux questions suivantes :

-	[Qu’est-ce que DocumentDB et quelle valeur fournit-il au cloud et aux applications mobiles ?](#what-is-azure-documentdb)
-	[Comment mes données sont-elles gérées dans DocumentDB et comment puis-je y accéder ?](#data-management)
-	[Comment développer des applications à l’aide de DocumentDB ?](#develop)
-	[Quelles sont les étapes suivantes pour créer une application DocumentDB ?](#next-steps)  

## Présentation d'Azure DocumentDB  

Les applications modernes produisent et consomment rapidement de très larges volumes de données, et y répondent. Elles évoluent très rapidement, ainsi que le schéma de données sous-jacent. En réponse, de plus en plus de développeurs choisissent des bases de données documentaires NoSQL sans schéma comme solutions simples, rapides et évolutives pour stocker et traiter les données tout en préservant la capacité à itérer rapidement les modèles de données d’application et les flux de données non structurées. Toutefois, de nombreuses bases de données sans schéma n’autorisent pas les requêtes complexes et le traitement transactionnel, ce qui rend difficile la gestion avancée des données. C’est là qu’intervient DocumentDB. Microsoft a développé DocumentDB pour répondre à ces besoins lors de la gestion des données pour les applications d’aujourd’hui.

Microsoft Azure DocumentDB est un service de base de données NoSQL orienté documents sans schéma conçu pour les applications Web et mobiles modernes. DocumentDB fournit de manière cohérente des lectures et des écritures rapides, la flexibilité des schémas et la possibilité de mettre à l'échelle facilement une base de données à la demande. Il ne part pas du principe que vous utilisez des schémas et n’en réclame pas pour les documents JSON qu’il indexe. Par défaut, il indexe automatiquement tous les documents de la base de données et n’attend pas et ne nécessite aucun schéma ou création d'index secondaires. DocumentDB autorise les requêtes ad hoc complexes à l’aide du langage SQL, prend en charge des niveaux de cohérence bien définis et offre un traitement des transactions multi-documents intégré en JavaScript utilisant le modèle de programmation familier des procédures stockées, déclencheurs et fonctions définies par l’utilisateur.

En tant que base de données JSON, DocumentDB prend en charge de manière native les documents JSON, facilitant ainsi l'itération du schéma d'application. Le service adopte l’ubiquité des langages JSON et JavaScript, éliminant ainsi les incompatibilités entre les objets définis par l’application et le schéma de base de données. Une intégration approfondie de JavaScript permet aussi aux développeurs d'exécuter la logique d'application de manière efficace et directement dans le moteur de base de données d'une transaction de base de données.

Azure DocumentDB offre les avantages et fonctionnalités suivants :

-	**Requêtes ad hoc à l’aide de la syntaxe SQL familière :** stockez des documents JSON hétérogènes dans DocumentDB et interrogez-les via la syntaxe SQL. DocumentDB utilise une technologie d'indexation parallèle, structurée par des journaux et sans verrouillage qui permet d'indexer automatiquement tout le contenu des documents. Celle-ci autorise les requêtes enrichies en temps réel sans qu’il soit nécessaire de spécifier des indicateurs de schéma, des index secondaires ou des vues. Pour en savoir plus, consultez [Requête DocumentDB](documentdb-sql-query.md). 

-	**Exécution de JavaScript dans la base de données :** exprimez la logique d’application en tant que procédures stockées, déclencheurs et fonctions définies par l’utilisateur à l’aide du code JavaScript standard. Cela permet à la logique d’application d’opérer via les données sans se soucier des incompatibilités entre l’application et le schéma de base de données. DocumentDB fournit l'exécution transactionnelle complète de la logique d'application JavaScript directement dans le moteur de base de données. Cette intégration approfondie de JavaScript permet l’exécution des opérations INSERT, REPLACE, DELETE et SELECT à partir d’un programme JavaScript en tant que transaction isolée. Pour plus d’informations, consultez la rubrique [Programmation côté serveur dans DocumentDB](documentdb-programming.md).

-	**Niveaux de cohérence ajustables :** Opérez un choix parmi quatre niveaux de cohérence bien définis pour obtenir une équilibre optimal entre cohérence et performances. Pour les requêtes et les opérations de lecture, DocumentDB propose quatre niveaux de cohérence distincts : Fort, En fonction de l’obsolescence, Par session et Éventuel. Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence
-	, disponibilité et latence. Pour en savoir plus, consultez [Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB](documentdb-consistency-levels.md).

-	**Gestion globale :** Ne vous souciez plus de gérer les ressources de base de données et d’ordinateur. En tant que service Microsoft Azure entièrement géré, vous n'avez pas à gérer des machines virtuelles, déployer et configurer des logiciels, ni à vous préoccuper de mises à niveau du niveau de données. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionales. Vous pouvez facilement ajouter un compte DocumentDB et configurer la capacité dont vous avez besoin, et ainsi vous concentrer pleinement sur votre application plutôt que sur le fonctionnement et la gestion de votre base de données.

-	**Débit et stockage extrêmement évolutifs en toute flexibilité :** augmentez ou réduisez facilement la taille de votre base de données JSON DocumentDB pour répondre aux besoins de votre application. La mise à l’échelle est effectuée via des unités détaillées (collections) de stockage SSD réservé et de débit. Vous pouvez mettre à l’échelle DocumentDB de manière flexible avec des performances prévisibles en créant d’autres unités à mesure que votre application grandit.

-	**Conception ouverte :** Démarrez rapidement à l’aide de compétences et d’outils existants. La programmation dans DocumentDB est simple, accessible et ne nécessite pas d'adopter de nouveaux outils ou d'adhérer à des extensions personnalisées JSON ou JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données, y compris CRUD, les requêtes et le traitement JavaScript via une interface HTTP RESTful. DocumentDB englobe les formats, normes et langages existants, tout en offrant des fonctionnalités de base de données de valeur élevée.

Vous pouvez utiliser DocumentDB pour stocker des jeux de données flexibles nécessitant l'extraction de requêtes et un traitement transactionnel. Les scénarios d'application peuvent inclure les données utilisateur des applications web et mobiles interactives, ainsi que le stockage, la récupération et le traitement des données JSON de l'application. Une base de données peut stocker un nombre illimité de documents JSON, car DocumentDB est parfaitement adapté aux applications exécutées sur Internet.

##<a name="data-management"></a>Ressources Azure DocumentDB
Azure DocumentDB gère les données via des ressources de base de données bien définies. Ces ressources sont répliquées à des fins de haute disponibilité et adressables de manière unique via leur URI logique. DocumentDB offre un modèle de programmation RESTful simple basé sur HTTP pour toutes les ressources.

Un compte de base de données DocumentDB est un espace de noms unique qui vous donne accès à Azure DocumentDB. Pour pouvoir créer un compte de base de données, vous devez posséder un abonnement Azure, lequel vous donne accès à une grande variété de services Azure.

Toutes les ressources au sein de DocumentDB sont modélisées et stockées en tant que documents JSON. Les ressources sont gérées en tant qu'éléments (documents JSON contenant des métadonnées) et en tant que flux (collections d'éléments). Les ensembles d'éléments sont contenus dans leurs flux respectifs.

L'image ci-dessous affiche les relations entre les ressources DocumentDB :

![Relation hiérarchique entre les ressources dans DocumentDB, une base de données NoSQL JSON][1]

Un compte de base de données est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, lesquelles comportent des procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents et les pièces jointes associées. Une base de données est aussi associée à des utilisateurs, chacun possédant un ensemble d'autorisations permettant d'accéder à d'autres collections, procédures stockées, déclencheurs, fonctions définies par l'utilisateur, documents ou pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents, les procédures stockées, les fonctions définies par l'utilisateur et les pièces jointes comportent un contenu JSON arbitraire défini par l'utilisateur.

##<a name="develop"></a> Développement à l'aide d'Azure DocumentDB
Azure DocumentDB expose les ressources via une API REST qui peut être appelée par n'importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, DocumentDB offre des bibliothèques de programmation pour plusieurs langages populaires. Ces bibliothèques simplifient de nombreuses tâches associées à Azure DocumentDB, car elles gèrent les détails tels que la mise en cache d'adresses, la gestion des exceptions, les nouvelles tentatives automatiques, etc. Elles sont actuellement disponibles pour les langages et les plateformes suivants :

Télécharger | Documentation
--- | ---
[KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](http://go.microsoft.com/fwlink/?LinkID=402989) | [Bibliothèque .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Kit de développement logiciel (SDK) Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) | [Bibliothèque Node.js](http://dl.windowsazure.com/documentDB/nodedocs/)
[Kit de développement logiciel (SDK) Java](http://go.microsoft.com/fwlink/?LinkID=402380) | [Bibliothèque Java](http://dl.windowsazure.com/documentdb/javadoc/)
[Kit de développement logiciel (SDK) JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Bibliothèque JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/)
n/a | [Kit de développement logiciel (SDK) JavaScript côté serveur](http://dl.windowsazure.com/documentDB/jsserverdocs/)
[Kit de développement logiciel (SDK) Python](https://pypi.python.org/pypi/pydocumentdb) | [Bibliothèque Python](http://dl.windowsazure.com/documentDB/pythondocs/)

En plus des opérations Create, Read, Update et Delete de base, DocumentDB fournit une interface de requêtes SQL enrichies pour la récupération de documents JSON et une prise en charge côté serveur de l'exécution transactionnelle de la logique d'application JavaScript. Les interfaces d'exécution des requêtes et des scripts sont disponibles dans toutes les bibliothèques de plateforme, ainsi que les API REST.

### Requête SQL
DocumentDB accepte l'interrogation de documents à l'aide d'un langage SQL prenant sa racine dans le système de type JavaScript et les expressions avec prise en charge des requêtes hiérarchiques enrichies. Le langage de requête DocumentDB est une interface simple et performante qui permet d'interroger des documents JSON. Le langage prend en charge un sous-ensemble de syntaxe ANSI SQL et ajoute une intégration approfondie des objets JavaScript, des tableaux, de la construction d'objets et de l'appel de fonctions. DocumentDB fournit son modèle de requête sans que le développeur n'ait à fournir un schéma explicite ou des indicateurs d'indexation.

Les fonctions définies par l'utilisateur peuvent être inscrites auprès de DocumentDB et référencées dans le cadre d'une requête SQL, étendant ainsi la syntaxe pour prendre en charge une logique d'application personnalisée. Elles sont écrites en tant que programmes JavaScript et exécutées dans la base de données.

Pour les développeurs .NET, DocumentDB propose également un fournisseur de requête LINQ dans le [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx).

### Transactions et exécution JavaScript
DocumentDB vous permet d'écrire la logique d'application sous forme de programmes nommés écrits entièrement en JavaScript. Ces programmes sont inscrits pour une collection et prennent en charge l'émission d'opérations de base de données sur les documents d'une collection donnée. JavaScript peut être inscrit à des fins d'exécution en tant que déclencheur, procédure stockée ou fonction définie par l'utilisateur. Les déclencheurs et les procédures stockées permettent de créer, lire, mettre à jour et supprimer des documents, tandis que les fonctions définies par l'utilisateur sont exécutées dans le cadre de la logique d'exécution de requêtes sans accès en écriture à la collection.

L'exécution JavaScript dans DocumentDB est modélisée selon les concepts pris en charge par les systèmes de bases de données relationnelles, avec JavaScript en remplacement de Transact-SQL. Toute la logique JavaScript est exécutée dans une transaction ACID ambiante avec isolement de capture instantanée. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée.

## Étapes suivantes
Si vous avez déjà un compte Azure, vous pouvez commencer avec DocumentDB dans le [portail Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) par la [création d'un compte de base de données DocumentDB](documentdb-create-account.md).

Si vous n'avez pas de compte Azure, vous pouvez :

- vous inscrire pour bénéficier d’un [essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/), qui vous offre 30 jours et 200 USD pour essayer tous les services Azure. 
- Si vous possédez un abonnement MSDN, vous pouvez bénéficier de [150 USD de crédits Azure gratuits par mois](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), à utiliser sur n'importe quel service Azure. 

Ensuite, lorsque vous êtes prêt à en savoir plus, visitez notre [cursus](http://azure.microsoft.com/documentation/learning-paths/documentdb/) pour explorer toutes les ressources de formation à votre disposition.


[1]: ./media/documentdb-introduction/json-database-resources1.png
 

<!---HONumber=AcomDC_1203_2015-->