<properties title="Introduction to Microsoft Azure DocumentDB" pageTitle="Introduction to Microsoft Azure DocumentDB | Azure" description="Learn about Azure DocumentDB and its value to cloud and mobile applications. Also, learn about how it manages data, and how you can use it in application development." metaKeywords="" services="documentdb" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Présentation de Microsoft Azure DocumentDB
Cet article propose une introduction à Microsoft Azure DocumentDB destinée aux développeurs, aux professionnels de l'informatique et aux décideurs économiques. Il vous permettra de découvrir :
-	Le service Azure DocumentDB et la valeur qu'il apporte à vos applications cloud et mobiles
-	Comment vos données sont gérées dans Azure DocumentDB
-	Comment accéder aux données et développer des applications à l'aide d'Azure DocumentDB
-	Commet générer par la suite votre première application Azure DocumentDB

## Qu'est-ce qu'Azure DocumentDB ?
Les applications modernes produisent et consomment rapidement de très larges volumes de données, et y répondent. Elles évoluent très rapidement, ainsi que le schéma de données sous-jacent. En réponse, de plus en plus de développeurs choisissent des bases de données NoSQL sans schéma comme solutions simples, rapides et élastiques pour stocker et traiter les données tout en préservant la capacité à itérer rapidement les modèles de données d'application et les flux de données non structurées. Toutefois, de nombreuses bases de données sans schéma n'autorisent pas les requêtes non triviales et le traitement transactionnel, rendant difficile la gestion avancée des données. Microsoft a développé Azure DocumentDB afin de fournir ces fonctionnalités lors de la gestion de données sans schéma.

Microsoft Azure DocumentDB est un service de base de données NoSQL orienté documents conçu pour les applications web et mobiles modernes. DocumentDB fournit de manière cohérente des lectures et des écritures rapides, la flexibilité des schémas et la possibilité de mettre à l'échelle facilement une base de données à la demande. DocumentDB autorise les requêtes ad hoc complexes à l'aide du langage SQL, prend en charge des niveaux de cohérence bien définis et offre un traitement de transactions multi-documents intégré en JavaScript utilisant le modèle de programmation familier des procédures stockées, déclencheurs et fonctions définies par l'utilisateur.

Azure DocumentDB prend en charge de manière native les documents JSON, facilitant ainsi l'itération du schéma d'application. Le service adopte l'ubiquité des langages JSON et JavaScript, éliminant ainsi les non-correspondances entre le système de type d'application et le schéma de base de données. Une intégration approfondie de JavaScript permet aussi aux développeurs d'exécuter la logique d'application de manière efficace directement dans le moteur de base de données au sein de la transaction de base de données.

Azure DocumentDB offre les avantages et fonctionnalités suivants :

-	**Requêtes ad hoc avec la syntaxe SQL bien connue :** stockez des documents JSON hétérogènes dans DocumentDB et interrogez-les via la syntaxe SQL. DocumentDB utilise une technologie d'indexation parallèle, structurée par des journaux et sans verrouillage qui permet d'indexer automatiquement tout le contenu des documents. Celle-ci autorise les requêtes enrichies en temps réel sans qu'il soit nécessaire de spécifier des indicateurs de schéma, ni des index ou vues secondaires.

-	**Exécution JavaScript au sein de la base de données :** exprimez la logique d'application en tant que procédures stockées, déclencheurs et fonctions définies par l'utilisateur à l'aide du code JavaScript standard. Cela permet à la logique d'application d'opérer via les données JSON sans se soucier des non-correspondances d'impédance entre l'application et le schéma de base de données. DocumentDB fournit l'exécution transactionnelle complète de la logique d'application JavaScript directement dans le moteur de base de données. Cette intégration approfondie de JavaScript permet l'exécution des opérations INSERT, REPLACE, DELETE et SELECT à partir d'un programme JavaScript en tant que transaction isolée.

-	**Niveaux de cohérence ajustables :** opérez un choix parmi quatre niveaux de cohérence bien définis pour obtenir une équilibre optimal entre cohérence et performances. Pour les requêtes et les opérations de lecture, DocumentDB propose quatre niveaux de cohérence distincts : Strong (Fort), Bounded-Staleness (En fonction de l'obsolescence), Session (Par session) et Eventual (Éventuel). Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence.

-	**Gestion globale :** ne vous souciez plus de gérer les ressources de bases de données et d'ordinateurs. En tant que service Microsoft Azure entièrement géré, vous n'avez pas à gérer des machines virtuelles, déployer et configurer des logiciels, ni à vous préoccuper de mises à niveau du niveau de données. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionales. Vous pouvez facilement ajouter un compte DocumentDB et approvisionner la capacité dont vous avez besoin, et ainsi vous concentrer pleinement sur votre application plutôt que sur le fonctionnement et la gestion de votre base de données.

-	**Débit et stockage évolutifs en toute flexibilité :** mettez facilement à l'échelle DocumentDB en fonction des besoins de votre application. La mise à l'échelle a lieu via des unités détaillées de stockage SSD réservé et de débit. Vous pouvez mettre à l'échelle de manière flexible DocumentDB avec des performances prévisibles en achetant des unités de capacité supplémentaires à mesure que votre application grandit.

-	**Conception ouverte :** démarrez rapidement à l'aide de compétences et d'outils existants. La programmation dans DocumentDB est simple, accessible, et ne nécessite pas d'adopter de nouveaux outils ou d'adhérer à des extensions personnalisées des formats JSON et JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données, y compris CRUD, les requêtes et le traitement JavaScript via une interface HTTP RESTful. DocumentDB englobe des formats, normes et langages existants, tout en offrant des fonctionnalités de base de données de valeur.

Vous pouvez utiliser Azure DocumentDB pour stocker des jeux de données flexibles nécessitant l'extraction de requêtes et un traitement transactionnel. Les scénarios d'application peuvent inclure des données utilisateur pour des applications web et mobiles interactives, ainsi que le stockage, la récupération et le traitement des données JSON de l'application. Une base de données peut stocker un nombre illimité de documents JSON, faisant de DocumentDB un service idéal pour les applications exécutées sur Internet.

## Ressources Azure DocumentDB
Azure DocumentDB gère les données via des ressources de base de données bien définies. Ces ressources sont répliquées à des fins de haute disponibilité et adressables de manière unique via leur URI logique. DocumentDB offre un modèle de programmation RESTful simple basé sur HTTP pour toutes les ressources.

Un compte de base de données DocumentDB est un espace de noms unique qui vous donne accès à Azure DocumentDB. Pour pouvoir créer un compte de base de données, vous devez posséder un abonnement Azure, c'est-à-dire un plan qui vous donne accès à divers services Azure. 

Toutes les ressources Azure DocumentDB sont modélisées et stockées en tant que documents JSON. Les ressources sont gérées en tant qu'éléments (documents JSON contenant des métadonnées) et en tant que flux (collections d'éléments). Les ensembles d'éléments sont contenus dans leurs flux respectifs.

L'image ci-dessous affiche les relations entre les ressources Azure DocumentDB :

![][1]

Un compte de base de données est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, qui contiennent quant à elles des procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents et les pièces jointes associées. Une base de données est aussi associée à des utilisateurs, chacun possédant un ensemble d'autorisations permettant d'accéder à d'autres collections, procédures stockées, déclencheurs, fonctions définies par l'utilisateur, documents ou pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents, les procédures stockées, les fonctions définies par l'utilisateur et les pièces jointes contiennent du contenu JSON arbitraire défini par l'utilisateur.

## Développement avec Azure DocumentDB
Azure DocumentDB expose les ressources via une API REST pouvant être appelée par n'importe quel langage capable d'envoyer des demandes HTTP/HTTPS. Par ailleurs, Azure DocumentDB offre des bibliothèques de programmation pour plusieurs langages populaires. Ces bibliothèques simplifient de nombreuses tâches associées à Azure DocumentDB, car elles gèrent les détails tels que la mise en cache d'adresses, la gestion des exceptions, les nouvelles tentatives automatiques, etc. Elles sont actuellement disponibles pour les langages et les plateformes suivants (d'autres sont à l'étude) :

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
-	[Node.js](http://go.microsoft.com/fwlink/?LinkID=402990)
-	[JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
-	[Python](http://go.microsoft.com/fwlink/?LinkID=402992)

En plus des opérations Create, Read, Update et Delete de base, Azure DocumentDB fournit une interface de requêtes SQL enrichies pour la récupération de documents JSON et une prise en charge côté serveur de l'exécution transactionnelle de la logique d'application JavaScript. Les interfaces d'exécution des requêtes et des scripts sont disponibles dans toutes les bibliothèques de plateforme, ainsi que les API REST.

### Requête SQL
Azure DocumentDB prend en charge l'interrogation de documents à l'aide d'un langage SQL prenant sa racine dans le système de type JavaScript avec la prise en charge des requêtes hiérarchiques enrichies. Le langage de requête DocumentDB est une interface simple et performante qui permet d'interroger des documents JSON. Le langage prend en charge un sous-ensemble de syntaxe ANSI SQL et ajoute une intégration approfondie des objets JavaScript, des tableaux, de la construction d'objets et de l'appel de fonctions. DocumentDB fournit son modèle de requête sans que le développeur n'ait à fournir un schéma explicite ou des indicateurs d'indexation.

Les fonctions définies par l'utilisateur peuvent être inscrites auprès d'Azure DocumentDB et référencées dans le cadre d'une requête SQL, étendant ainsi la syntaxe pour prendre en charge une logique d'application personnalisée. Elles sont écrites en tant que programmes JavaScript et exécutées dans la base de données.

Pour les développeurs .NET, Azure DocumentDB propose également un fournisseur de requête LINQ dans le Kit de développement logiciel (SDK) .NET.

## Transactions et exécution JavaScript
Azure DocumentDB vous permet d'écrire la logique d'application sous forme de programmes nommés écrits entièrement en JavaScript. Ces programmes sont inscrits pour une collection et prennent en charge l'émission d'opérations de base de données sur les documents d'une collection donnée. Le JavaScript de l'application peut être inscrit à des fins d'exécution en tant que déclencheur, procédure stockée ou fonction définie par l'utilisateur. Les déclencheurs et les procédures stockées permettent de créer, de lire, de mettre à jour et de supprimer les documents alors que les fonctions définies par l'utilisateur sont exécutées dans le cadre de l'exécution de requêtes sans accès en écriture à la collection.

L'exécution JavaScript dans DocumentDB est modélisée après les concepts pris en charge par les systèmes de bases de données relationnelles, avec JavaScript en remplacement de T-SQL. Toute la logique JavaScript est exécutée dans une transaction ACID ambiante avec isolement de capture instantanée. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée.

## Étapes suivantes
Pour prendre en main Azure DocumentDB, explorez les ressources suivantes :

-	[Présentation des concepts DocumentDB](/documentation/articles/documentdb-resources/)
-	[Interaction avec DocumentDB](/documentation/articles/documentdb-interactions-with-resources/)
-	[Création d'un compte de base de données](/documentation/articles/documentdb-create-account/)
-	[Prise en main de DocumentDB](/documentation/articles/documentdb-get-started/)

[1]: ./media/documentdb-introduction/intro.png
