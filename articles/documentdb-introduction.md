<properties title="Introduction to Microsoft Azure DocumentDB" pageTitle="Présentation de Microsoft Azure DocumentDB | Azure" description="Découvrez Azure DocumentDB, une base de données de documents NoSQL, et son intérêt pour les applications cloud et mobiles. Découvrez la façon dont il gère les données et dont vous pouvez l'utiliser lors du développement d'applications." metaKeywords="" services="documentdb" solutions="data-management"  authors="spelluru" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="mimig" />

#Présentation de Microsoft Azure DocumentDB

Cet article propose une introduction à Microsoft Azure DocumentDB, service de base de données NoSQL orienté documents, destiné aux développeurs, aux professionnels de l'informatique et aux décideurs économiques. En le lisant, vous serez en mesure de répondre aux questions suivantes :  

-	[Qu'est-ce que DocumentDB et quelle valeur fournit-il au cloud et aux applications mobiles ?](#what-is-docdb)
-	[Comment mes données sont-elles gérées dans DocumentDB et comment puis-je y accéder ?](#data-management)
-	[Comment développer des applications à l'aide de DocumentDB ?](#develop)
-	[Quelles sont les étapes suivantes pour créer une application DocumentDB ?](#next-steps)  

##<a name="what-is-docdb"></a>Présentation d'Azure DocumentDB  

Les applications modernes produisent et consomment rapidement de très larges volumes de données, et y répondent. Elles évoluent très rapidement, ainsi que le schéma de données sous-jacent. En réponse, de plus en plus de développeurs choisissent des bases de données NoSQL sans schéma comme solutions simples, rapides et élastiques pour stocker et traiter les données tout en préservant la capacité à itérer rapidement les modèles de données d'application et les flux de données non structurées. Toutefois, de nombreuses bases de données sans schéma n'autorisent pas les requêtes non triviales et le traitement transactionnel, ce qui rend difficile la gestion avancée des données. C'est là qu'intervient DocumentDB. Microsoft a développé DocumentDB pour répondre à ces besoins lors de la gestion de données sans schéma.

Microsoft Azure DocumentDB est un service de base de données NoSQL orienté documents conçu pour les applications web et mobiles modernes.  DocumentDB fournit de manière cohérente des lectures et des écritures rapides, la flexibilité des schémas et la possibilité de mettre à l'échelle facilement une base de données à la demande. DocumentDB autorise les requêtes ad hoc complexes à l'aide du langage SQL, prend en charge des niveaux de cohérence bien définis et offre un traitement de transactions multi-documents intégré en JavaScript utilisant le modèle de programmation familier des procédures stockées, déclencheurs et fonctions définies par l'utilisateur. 

DocumentDB prend en charge de manière native les documents JSON, facilitant ainsi l'itération du schéma d'application. Le service adopte l'ubiquité des langages JSON et JavaScript, éliminant ainsi les non-correspondances entre le type d'application et le schéma de base de données. Une intégration approfondie de JavaScript permet aussi aux développeurs d'exécuter la logique d'application de manière efficace et directement dans le moteur de base de données d'une transaction de base de données. 

Azure DocumentDB offre les avantages et fonctionnalités suivants :

-	**Requêtes ad hoc avec une syntaxe SQL courante :** stocker des documents JSON hétérogènes dans DocumentDB et interroger ces documents via une syntaxe SQL classique. DocumentDB utilise une technologie d'indexation structurée par des journaux à simultanéité élevée et sans verrouillage pour indexer automatiquement tout le contenu des documents. Cela permet des requêtes riches en temps réel sans avoir besoin de spécifier des indicateurs de schéma, des index secondaires ou des vues.

-	**Exécution du code JavaScript au sein de la base de données :** exprimer la logique de l'application en tant que procédures stockées, déclencheurs et fonctions définies par l'utilisateur (UDF) à l'aide d'un code JavaScript standard. Votre logique d'application peut ainsi opérer sur les données JSON sans se soucier de l'incompatibilité entre l'application et le schéma de base de données. DocumentDB fournit l'exécution transactionnelle complète de la logique d'application JavaScript directement dans le moteur de base de données. L'intégration approfondie de JavaScript permet l'exécution d'opérations INSERT, REPLACE, DELETE et SELECT à partir d'un programme JavaScript sous forme de transaction isolée.

-	**Niveaux de cohérence réglables :** sélectionner parmi quatre niveaux de cohérence bien définis pour atteindre un compromis optimal entre la cohérence et les performances. Pour les requêtes et les opérations de lecture, DocumentDB offre quatre niveaux de cohérence distincts : forte, péremption délimitée, session et final. Ces niveaux de cohérence granulaires et précis permettent d'obtenir un bon compromis entre la cohérence, la disponibilité et la latence.

-	**Gestion complète :** éliminer le besoin de gérer les ressources des base de données et des machines. En tant que service entièrement géré de Microsoft Azure, il est inutile de gérer les machines virtuelles, de déployer et de configurer les logiciels ou de gérer les mises à niveau de la couche de données complexes. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionales. Vous pouvez facilement ajouter un compte DocumentDB et provisionner les capacités au fur et à mesure que vous en avez besoin, ce qui vous permet de vous concentrer sur votre application au lieu d'exploiter et de gérer votre base de données.

-	**Souplesse d'évolution du débit et du stockage :** mise à l'échelle simple de DocumentDB pour répondre aux besoins de vos applications. La mise à l'échelle s'effectue via les unités spécifiques de débit et de stockage SSD réservées. Vous pouvez faire évoluer DocumentDB de façon élastique avec des performances prévisibles en achetant plusieurs unités de capacité lorsque votre application croît.

-	**Conception ouverte :** 
-	 rapidement en utilisant les outils et compétences existants. La programmation dans DocumentDB est simple, accessible et ne nécessite pas d'adopter de nouveaux outils ou d'adhérer à des extensions personnalisées JSON ou JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données, y compris CRUD, les requêtes et le traitement JavaScript via une interface HTTP RESTful. DocumentDB englobe les formats, normes et langages existants, tout en offrant des fonctionnalités de base de données de valeur élevée.

Vous pouvez utiliser DocumentDB pour stocker des jeux de données flexibles nécessitant l'extraction de requêtes et un traitement transactionnel. Les scénarios d'application peuvent inclure les données utilisateur des applications web et mobiles interactives, ainsi que le stockage, la récupération et le traitement des données JSON de l'application. Une base de données peut stocker un nombre illimité de documents JSON, car DocumentDB est parfaitement adapté aux applications exécutées sur Internet.

##<a name="data-management"></a>Ressources Azure DocumentDB
Azure DocumentDB gère les données via des ressources de base de données bien définies. Ces ressources sont répliquées à des fins de haute disponibilité et adressables de manière unique via leur URI logique. DocumentDB offre un modèle de programmation RESTful simple basé sur HTTP pour toutes les ressources. 

Un compte de base de données DocumentDB est un espace de noms unique qui vous donne accès à Azure DocumentDB. Pour pouvoir créer un compte de base de données, vous devez posséder un abonnement Azure, lequel vous donne accès à une grande variété de services Azure. 

Toutes les ressources au sein de DocumentDB sont modélisées et stockées en tant que documents JSON. Les ressources sont gérées en tant qu'éléments (documents JSON contenant des métadonnées) et en tant que flux (collections d'éléments). Les ensembles d'éléments sont contenus dans leurs flux respectifs.

L'image ci-dessous affiche les relations entre les ressources DocumentDB :

![][1] 

Un compte de base de données est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, lesquelles comportent des procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents et les pièces jointes associées. Une base de données est aussi associée à des utilisateurs, chacun possédant un ensemble d'autorisations permettant d'accéder à d'autres collections, procédures stockées, déclencheurs, fonctions définies par l'utilisateur, documents ou pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents, les procédures stockées, les fonctions définies par l'utilisateur et les pièces jointes comportent un contenu JSON arbitraire défini par l'utilisateur.  

##<a name="develop"></a>Développement avec Azure DocumentDB
Azure DocumentDB expose les ressources via une API REST qui peut être appelée par n'importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, DocumentDB offre des bibliothèques de programmation pour plusieurs langages populaires. Ces bibliothèques simplifient de nombreuses tâches associées à Azure DocumentDB, car elles gèrent les détails tels que la mise en cache d'adresses, la gestion des exceptions, les nouvelles tentatives automatiques, etc. Elles sont actuellement disponibles pour les langages et les plateformes suivants (d'autres sont à l'étude) :  

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
-	[Node.js](http://go.microsoft.com/fwlink/?LinkID=402990)
-	[JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
-	[Python](http://go.microsoft.com/fwlink/?LinkID=402992)

En plus des opérations Create, Read, Update et Delete de base, Azure DocumentDB fournit une interface de requêtes SQL enrichies pour la récupération de documents JSON et une prise en charge côté serveur de l'exécution transactionnelle de la logique d'application JavaScript. Les interfaces d'exécution des requêtes et des scripts sont disponibles dans toutes les bibliothèques de plateforme, ainsi que les API REST. 

###Requête SQL
DocumentDB accepte l'interrogation de documents à l'aide d'un langage SQL prenant sa racine dans le système de type JavaScript et les expressions avec prise en charge des requêtes hiérarchiques enrichies. Le langage de requête DocumentDB est une interface simple et performante qui permet d'interroger des documents JSON. Le langage prend en charge un sous-ensemble de syntaxe ANSI SQL et ajoute une intégration approfondie des objets JavaScript, des tableaux, de la construction d'objets et de l'appel de fonctions. DocumentDB fournit son modèle de requête sans que le développeur n'ait à fournir un schéma explicite ou des indicateurs d'indexation.

Les fonctions définies par l'utilisateur peuvent être inscrites auprès de DocumentDB et référencées dans le cadre d'une requête SQL, étendant ainsi la syntaxe pour prendre en charge une logique d'application personnalisée. Elles sont écrites en tant que programmes JavaScript et exécutées dans la base de données. 

Pour les développeurs .NET, DocumentDB propose également un fournisseur de requête LINQ dans le Kit de développement logiciel (SDK) .NET. 

###Transactions et exécution JavaScript
DocumentDB vous permet d'écrire la logique d'application sous forme de programmes nommés écrits entièrement en JavaScript. Ces programmes sont inscrits pour une collection et prennent en charge l'émission d'opérations de base de données sur les documents d'une collection donnée. JavaScript peut être inscrit à des fins d'exécution en tant que déclencheur, procédure stockée ou fonction définie par l'utilisateur. Les déclencheurs et les procédures stockées permettent de créer, lire, mettre à jour et supprimer des documents, tandis que les fonctions définies par l'utilisateur sont exécutées dans le cadre de la logique d'exécution de requêtes sans accès en écriture à la collection.

L'exécution JavaScript dans DocumentDB est modélisée après les concepts pris en charge par les systèmes de bases de données relationnelles, avec JavaScript en remplacement de T-SQL. Toute la logique JavaScript est exécutée dans une transaction ACID ambiante avec isolement de capture instantanée. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée.

##<a name="next-steps"></a>Étapes suivantes
Pour la prise en main d'Azure DocumentDB, explorez les ressources suivantes :

- [Essayer DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB)
-	[Modèle de ressource et concepts de DocumentDB](/documentation/articles/documentdb-resources/)
-	[Interagir avec les ressources DocumentDB](/documentation/articles/documentdb-interactions-with-resources/)
-	[Créer un compte de base de données DocumentDB](/documentation/articles/documentdb-create-account/)
-	[Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB](/documentation/articles/documentdb-get-started/)

[1]: ./media/documentdb-introduction/intro.png

<!--HONumber=35.2-->
