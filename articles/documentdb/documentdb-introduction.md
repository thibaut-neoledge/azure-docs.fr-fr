---
title: "Présentation de DocumentDB : une base de données JSON | Microsoft Docs"
description: "Découvrez Azure DocumentDB, une base de données NoSQL JSON. Cette base de données de document est conçue pour le Big Data, une évolutivité élastique et une haute disponibilité."
keywords: "base de données JSON, base de données Document"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4f6ae0c3c40a10f75b46c6e44b0aa9e8ce440b4d
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Présentation de DocumentDB : une base de données NoSQL JSON
## <a name="what-is-documentdb"></a>Qu’est-ce que DocumentDB ?
DocumentDB est un service de base de données NoSQL entièrement géré, conçu pour garantir des performances rapides et prévisibles, tout en offrant une haute disponibilité, une mise à l’échelle élastique, une distribution globale et une grande facilité de développement. DocumentDB est une base de données NoSQL sans schéma qui fournit des capacités de requête SQL complètes et familières avec des latences faibles cohérentes sur les données JSON. Elle garantit que 99 % des lectures sont traitées dans un délai de 10 millisecondes et 99 % des écritures dans un délai de 15 millisecondes. En raison de ces avantages uniques, DocumentDB est parfait pour les applications web, mobiles, de jeu et IoT, ainsi que de nombreuses autres applications nécessitant une mise à l’échelle transparente et une réplication globale.

## <a name="how-can-i-learn-about-documentdb"></a>Comment puis-je obtenir des informations sur DocumentDB ?
Les trois étapes suivantes permettent de découvrir DocumentDB et de le voir à l’œuvre rapidement : 

1. Regardez la vidéo [What is DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) de deux minutes, qui présente les avantages liés à l'utilisation de DocumentDB.
2. Regardez la vidéo [Create DocumentDB on Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) de trois minutes, qui présente la prise en main de DocumentDB à l'aide du portail Azure.
3. Visitez le [Query Playground](http://www.documentdb.com/sql/demo)où vous pouvez utiliser différentes activités pour découvrir les riches fonctionnalités de requêtes disponibles dans DocumentDB. Ensuite, cliquez sur l'onglet Sandbox, exécutez vos propres requêtes SQL personnalisées et essayez DocumentDB.

Puis, revenez à cet article, où nous approfondissons le sujet.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Quelles fonctionnalités et caractéristiques clés sont offertes par DocumentDB ?
Azure DocumentDB offre les avantages et fonctionnalités suivants :

* **Débit et stockage évolutifs avec flexibilité :** augmentez ou réduisez facilement la taille de votre base de données JSON DocumentDB pour répondre aux besoins de votre application. Vos données sont stockées sur des disques SSD (SSD) pour garantir une faible latence de manière prévisible. DocumentDB prend en charge les conteneurs pour le stockage des données JSON (appelées collections) qui peuvent atteindre des tailles de stockage quasi-illimitée associées à un débit approvisionné. Vous pouvez mettre à l’échelle DocumentDB de manière flexible et transparente avec des performances prévisibles à mesure que votre application se développe. 
* **Réplication dans plusieurs régions :** DocumentDB réplique en toute transparence vos données dans toutes les régions associées à votre compte DocumentDB, ce qui vous permet de développer des applications qui requièrent l’accès global aux données tout en offrant un compromis entre cohérence, disponibilité et performances, tout en offrant les garanties correspondantes. DocumentDB fournit un basculement régional transparent avec les API multihébergement et la possibilité de mettre à l’échelle de manière élastique le débit et le stockage dans le monde entier. Pour en savoir plus, consultez [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md).
* **Requêtes ad hoc à l’aide de la syntaxe SQL familière :** stockez des documents JSON hétérogènes dans DocumentDB et interrogez-les via la syntaxe SQL. DocumentDB utilise une technologie d'indexation parallèle, structurée par des journaux et sans verrouillage qui permet d'indexer automatiquement tout le contenu des documents. Celle-ci autorise les requêtes enrichies en temps réel sans qu’il soit nécessaire de spécifier des indicateurs de schéma, des index secondaires ou des vues. Pour en savoir plus, consultez [Requête DocumentDB](documentdb-sql-query.md). 
* **Exécution de JavaScript dans la base de données :** exprimez la logique d’application en tant que procédures stockées, déclencheurs et fonctions définies par l’utilisateur à l’aide du code JavaScript standard. Cela permet à la logique d’application d’opérer via les données sans se soucier des incompatibilités entre l’application et le schéma de base de données. DocumentDB fournit l'exécution transactionnelle complète de la logique d'application JavaScript directement dans le moteur de base de données. Cette intégration approfondie de JavaScript permet l’exécution des opérations INSERT, REPLACE, DELETE et SELECT à partir d’un programme JavaScript en tant que transaction isolée. Pour plus d’informations, consultez la rubrique [Programmation côté serveur dans DocumentDB](documentdb-programming.md).
* **Niveaux de cohérence ajustables :** Opérez un choix parmi quatre niveaux de cohérence bien définis pour obtenir un équilibre optimal entre cohérence et performances. Pour les requêtes et les opérations de lecture, DocumentDB propose quatre niveaux de cohérence distincts : Fort, En fonction de l’obsolescence, Par session et Éventuel. Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez [Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB](documentdb-consistency-levels.md).
* **Gestion globale :** Ne vous souciez plus de gérer les ressources de base de données et d’ordinateur. En tant que service Microsoft Azure entièrement géré, vous n’avez pas à gérer de machines virtuelles, à déployer et configurer des logiciels, à gérer la mise à l’échelle ni à vous préoccuper des mises à niveau de la couche données. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionales. Vous pouvez facilement ajouter un compte DocumentDB et configurer la capacité dont vous avez besoin, et ainsi vous concentrer pleinement sur votre application plutôt que sur le fonctionnement et la gestion de votre base de données. 
* **Conception ouverte :** Démarrez rapidement à l’aide de compétences et d’outils existants. La programmation dans DocumentDB est simple, accessible et ne nécessite pas d'adopter de nouveaux outils ou d'adhérer à des extensions personnalisées JSON ou JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données, y compris CRUD, les requêtes et le traitement JavaScript via une interface HTTP RESTful. DocumentDB englobe les formats, normes et langages existants, tout en offrant des fonctionnalités de base de données de valeur élevée.
* **Indexation automatique :** par défaut, DocumentDB indexe automatiquement tous les documents de la base de données et n’attend pas et ne nécessite aucun schéma ou création d’index secondaires. Vous ne voulez pas tout indexer ? Ne vous inquiétez pas, vous pouvez aussi [refuser des chemins d’accès dans vos fichiers JSON](documentdb-indexing-policies.md) .
* **Compatibilité avec les applications MongoDB :** Avec DocumentDB : API pour MongoDB, vous pouvez utiliser des bases de données DocumentDB en tant que magasin de données pour les applications écrites pour MongoDB. Cela signifie qu’en utilisant les pilotes existants pour les bases de données MongoDB, votre application écrite pour MongoDB peut désormais communiquer avec DocumentDB et utiliser des bases de données DocumentDB au lieu de bases de données MongoDB. Dans de nombreux cas, vous pouvez passer de MongoDB à DocumentDB en modifiant simplement une chaîne de connexion. Pour en savoir plus, consultez [Qu’est-ce que DocumentDB : API pour MongoDB ?](documentdb-protocol-mongodb.md)

## <a name="data-management"></a>Comment les données sont-elles gérées par DocumentDB ?
Azure DocumentDB gère les données JSON via des ressources de base de données bien définies. Ces ressources sont répliquées à des fins de haute disponibilité et adressables de manière unique via leur URI logique. DocumentDB offre un modèle de programmation RESTful simple basé sur HTTP pour toutes les ressources. 

Un compte de base de données DocumentDB est un espace de noms unique qui vous donne accès à Azure DocumentDB. Pour pouvoir créer un compte de base de données, vous devez posséder un abonnement Azure, lequel vous donne accès à une grande variété de services Azure. 

Toutes les ressources au sein de DocumentDB sont modélisées et stockées en tant que documents JSON. Les ressources sont gérées en tant qu'éléments (documents JSON contenant des métadonnées) et en tant que flux (collections d'éléments). Les ensembles d'éléments sont contenus dans leurs flux respectifs.

L'image ci-dessous affiche les relations entre les ressources DocumentDB :

![Relation hiérarchique entre les ressources dans DocumentDB, une base de données NoSQL JSON][1] 

Un compte de base de données est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, lesquelles comportent des procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents et les pièces jointes associées. Une base de données est aussi associée à des utilisateurs, chacun possédant un ensemble d'autorisations permettant d'accéder à d'autres collections, procédures stockées, déclencheurs, fonctions définies par l'utilisateur, documents ou pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents, les procédures stockées, les fonctions définies par l'utilisateur et les pièces jointes comportent un contenu JSON arbitraire défini par l'utilisateur.  

## <a name="develop"></a> Comment puis-je développer des applications avec DocumentDB ?
Azure DocumentDB expose les ressources via une API REST qui peut être appelée par n'importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, DocumentDB offre des bibliothèques de programmation pour plusieurs langages populaires et est compatible avec les API MongoDB. Les bibliothèques clientes simplifient de nombreuses tâches associées à Azure DocumentDB, car elles gèrent les détails tels que la mise en cache d’adresses, la gestion des exceptions, les nouvelles tentatives automatiques, etc. Elles sont actuellement disponibles pour les langages et les plateformes suivants :  

| Télécharger | Documentation |
| --- | --- |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](http://go.microsoft.com/fwlink/?LinkID=402989) |[Bibliothèque .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) |
| [Kit de développement logiciel (SDK) Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Bibliothèque Node.js](http://azure.github.io/azure-documentdb-node/) |
| [Kit de développement logiciel (SDK) Java](http://go.microsoft.com/fwlink/?LinkID=402380) |[Bibliothèque Java](http://azure.github.io/azure-documentdb-java/) |
| [Kit de développement logiciel (SDK) JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) |[Bibliothèque JavaScript](http://azure.github.io/azure-documentdb-js/) |
| n/a |[Kit de développement logiciel (SDK) JavaScript côté serveur](http://azure.github.io/azure-documentdb-js-server/) |
| [Kit de développement logiciel (SDK) Python](https://pypi.python.org/pypi/pydocumentdb) |[Bibliothèque Python](http://azure.github.io/azure-documentdb-python/) |
| n/a | [API pour MongoDB](documentdb-protocol-mongodb.md)

[L’émulateur Azure DocumentDB](documentdb-nosql-local-emulator.md) vous permet de développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur DocumentDB, vous pouvez commencer à utiliser un compte Azure DocumentDB dans le cloud.

En plus des opérations Create, Read, Update et Delete de base, DocumentDB fournit une interface de requêtes SQL enrichies pour la récupération de documents JSON et une prise en charge côté serveur de l'exécution transactionnelle de la logique d'application JavaScript. Les interfaces d'exécution des requêtes et des scripts sont disponibles dans toutes les bibliothèques de plateforme, ainsi que les API REST. 

### <a name="sql-query"></a>Requête SQL
DocumentDB accepte l’interrogation de documents à l’aide d’un langage SQL prenant sa racine dans le système de type JavaScript, ainsi que les expressions prenant en charge les requêtes relationnelles, hiérarchiques et spatiales. Le langage de requête DocumentDB est une interface simple et performante qui permet d'interroger des documents JSON. Le langage prend en charge un sous-ensemble de syntaxe ANSI SQL et ajoute une intégration approfondie des objets JavaScript, des tableaux, de la construction d'objets et de l'appel de fonctions. DocumentDB fournit son modèle de requête sans que le développeur n'ait à fournir un schéma explicite ou des indicateurs d'indexation.

Les fonctions définies par l'utilisateur peuvent être inscrites auprès de DocumentDB et référencées dans le cadre d'une requête SQL, étendant ainsi la syntaxe pour prendre en charge une logique d'application personnalisée. Elles sont écrites en tant que programmes JavaScript et exécutées dans la base de données. 

Pour les développeurs .NET, DocumentDB propose également un fournisseur de requête LINQ dans le [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Transactions et exécution JavaScript
DocumentDB vous permet d'écrire la logique d'application sous forme de programmes nommés écrits entièrement en JavaScript. Ces programmes sont inscrits pour une collection et prennent en charge l'émission d'opérations de base de données sur les documents d'une collection donnée. JavaScript peut être inscrit à des fins d'exécution en tant que déclencheur, procédure stockée ou fonction définie par l'utilisateur. Les déclencheurs et les procédures stockées permettent de créer, lire, mettre à jour et supprimer des documents, tandis que les fonctions définies par l'utilisateur sont exécutées dans le cadre de la logique d'exécution de requêtes sans accès en écriture à la collection.

L'exécution JavaScript dans DocumentDB est modélisée selon les concepts pris en charge par les systèmes de bases de données relationnelles, avec JavaScript en remplacement de Transact-SQL. Toute la logique JavaScript est exécutée dans une transaction ACID ambiante avec isolement de capture instantanée. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée.

## <a name="are-there-any-online-courses-on-documentdb"></a>Y-a-t-il des formations en ligne sur DocumentDB ?

Oui, il y a une formation [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) cours sur Azure DocumentDB. 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>Étapes suivantes
Vous disposez déjà d’un compte Azure ? Dans ce cas, vous pouvez commencer avec DocumentDB dans le [portail Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) en [créant un compte de base de données DocumentDB](documentdb-create-account.md).

Vous n’avez pas de compte Azure ? Vous pouvez :

* vous inscrire pour bénéficier d’un [essai gratuit d’Azure](https://azure.microsoft.com/free/), qui vous offre 30 jours et l’équivalent de 200 USD pour essayer tous les services Azure. 
* Si vous possédez un abonnement MSDN, vous pouvez bénéficier de [150 USD de crédits Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , à utiliser sur n'importe quel service Azure. 
* Téléchargez [l’émulateur Azure DocumentDB](documentdb-nosql-local-emulator.md) pour développer votre application localement.

Ensuite, quand vous êtes prêt à aller plus loin, visitez notre [cursus](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour explorer toutes les ressources de formation à votre disposition. 

[1]: ./media/documentdb-introduction/json-database-resources1.png


