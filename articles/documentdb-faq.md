<properties title="Frequently asked questions about DocumentDB" pageTitle="Forum Aux Questions (FAQ) sur DocumentDB | Azure" description="Réponses aux questions du forum sur le service de base de données de documents NoSQL DocumentDB Azure. Découvrez ce que sont la capacité et les unités de requête et comment mettre à l'échelle en fonction des besoins de votre application." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, faq"   services="documentdb" solutions="data-management"   authors="spelluru" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />


#Forum Aux Questions (FAQ) sur DocumentDB

- [Concepts de base d'Azure DocumentDB](#fundamentals)
- [Configuration d'Azure DocumentDB](#setup)
- [Développement avec Azure DocumentDB](#develop)

## <a id="fundamentals"></a> Concepts de base de Microsoft Azure DocumentDB

###Qu'est-ce que Microsoft Azure DocumentDB ? 
Microsoft Azure DocumentDB est un service de base de données NoSQL orienté documents très évolutif qui propose des requêtes enrichies plutôt que des données sans schéma. Il offre des options de configuration et des performances fiables, tout en permettant un développement rapide. Tout cela est rendu possible grâce à une plateforme gérée, soutenue par la puissance et la portée de Microsoft Azure. DocumentDB est la solution idéale pour les applications web et mobiles lorsque le débit, la faible latence et le modèle de données sans schéma sont primordiaux. DocumentDB offre une flexibilité des schémas et une indexation riche par le biais d'un modèle de données JSON natif. Il inclut également un support transactionnel multi-documents avec JavaScript intégré.  
  
Pour avoir des informations sur le déploiement et l'utilisation de ce service, consultez la [page de documentation de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###Quel type de base de données est DocumentDB ?
DocumentDB est une base de données NoSQL orientée documents qui stocke les données au format JSON.  DocumentDB prend en charge les structures imbriquées et autonomes qui peuvent être interrogées grâce à une syntaxe de requête SQL DocumentDB enrichie. DocumentDB offre un traitement transactionnel haute performance JavaScript côté serveur par le biais de procédures stockées, de déclencheurs et de fonctions définies par l'utilisateur. La base de données prend également en charge des niveaux de cohérence ajustables avec les niveaux de performances associés.
 
###Les bases de données DocumentDB ont-elles des tables comme un SGBDR ?
Non, DocumentDB stocke les données dans des collections sous forme de documents JSON.  Pour plus d'informations sur les ressources de DocumentDB, consultez l'article Modèle de ressource et concepts DocumentDB. 

###Les bases de données DocumentDB prennent-elles en charge les données sans schéma ?
Oui, DocumentDB permet aux applications de stocker des documents JSON arbitrairement sans définition de schéma ni indice. Les données peuvent être interrogées immédiatement via l'interface de requête SQL de DocumentDB.   

###DocumentDB prend-il en charge les transactions ACID ?
Oui, DocumentDB prend en charge les transactions entre documents exprimées en procédures stockées et déclencheurs JavaScript. Les transactions sont étendues à une seule collection et exécutées intégralement avec des sémantiques ACID, isolées du code et des requêtes utilisateur en cours d'exécution.  Si des exceptions surviennent lors de l'exécution du code d'application JavaScript côté serveur, la transaction entière est annulée. 

###Dans quels cas utilise-t-on généralement DocumentDB ?  
DocumentDB est le choix idéal pour les nouvelles applications web et mobiles lorsque l'évolutivité, les performances et les requêtes de données sans schéma sont primordiales. DocumentDB permet un développement rapide et prend en charge l'itération continue de modèles de données d'application. Les applications qui gèrent du contenu généré par l'utilisateur et des données sont communément utilisées dans DocumentDB.  

###Quelles sont les limites en termes d'évolutivité et de capacité ?
Chaque compte Azure DocumentDB prend en charge un nombre maximal d'unités de capacité, qui peut être configuré par le biais du portail Azure. S'il vous faut des unités de capacité supplémentaires, veuillez contacter le support technique afin d'augmenter le quota de votre compte.  Pour en savoir plus sur les quotas de ressources, veuillez consulter [Limites dans la version préliminaire de DocumentDB.](http://azure.microsoft.com/en-us/documentation/articles/documentdb-limits/) 


###Combien coûte Microsoft Azure DocumentDB ?
Consultez les [informations de tarification de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317) pour plus de détails.

## <a id="setup"></a> Configuration de Microsoft Azure DocumentDB

###Comment m'inscrire à Microsoft Azure DocumentDB ?
Microsoft Azure DocumentDB (version préliminaire) est disponible dans la nouvelle version préliminaire du portail Azure.  Vous devez d'abord disposer d'un abonnement à Microsoft Azure.  Une fois inscrit à Microsoft Azure, vous pouvez ajouter un compte DocumentDB à votre abonnement Azure par le biais de la galerie.   

###Qu'est-ce que la clé principale ?
Une clé principale est un jeton de sécurité permettant d'accéder à toutes les ressources d'un compte. Les personnes disposant de cette clé ont un accès en lecture et en écriture à toutes les ressources du compte de la base de données. Faites preuve de précaution lorsque vous distribuez des clés principales. 

###Comment créer une base de données ?
Vous pouvez créer des bases de données en utilisant un Kit de développement logiciel (SDK) DocumentDB ou des API REST.  Consultez la section Développement de la [page de documentation DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319) pour plus d'informations sur les méthodes de développement des applications. 

###Qu'est-ce qu'une collection ?
Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Les requêtes et les transactions sont étendues vers les collections. Vous pouvez stocker un ensemble de documents JSON hétérogènes dans une seule collection. Tous seront indexés automatiquement.   

###Quelles sont les limites des bases de données et des collections ?
Chaque unité de capacité achetée est dotée d'un stockage de base de données alloué et d'un débit approvisionné. Des quotas existent également pour chaque ressource gérée par le service. Consultez l'article sur les limites du service DocumentDB pour plus d'informations.  

###Comment configurer des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et des autorisations en utilisant un Kit de développement logiciel (SDK) DocumentDB ou des API REST. Consultez la section Développement de la [page de documentation DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319)   pour plus d'informations sur les méthodes de développement des applications.     


## <a id="develop"></a>Développement avec Microsoft Azure DocumentDB

###Comment développer avec DocumentDB ?
Dans le cadre de la version préliminaire, les Kits de développement logiciel (SDK) sont disponibles pour .NET, Python, Node.js et JavaScript.  Les développeurs peuvent également utiliser les API RESTful HTTP pour interagir avec les ressources DocumentDB sur de nombreuses plateformes et dans de nombreuses langages. Pour plus d'informations sur l'utilisation des Kits de développement logiciel (SDK), consultez la section Développement de la [page de documentation DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###DocumentDB prend-il en charge SQL ?
Le langage de requête SQL de DocumentDB fournit des opérateurs relationnels et hiérarchiques enrichis, ainsi qu'une extensibilité via des fonctions JavaScript définies par l'utilisateur. La syntaxe JSON permet la modélisation de documents JSON en tant qu'arborescences avec des étiquettes en tant que nœuds d'arborescence. Cette capacité est exploitée par les techniques d'indexation automatique de DocumentDB et le langage de requête SQL de ce dernier.  Pour plus d'informations sur la façon d'utiliser notre syntaxe SQL, consultez l'article [Requêtes à l'aide du SQL de DocumentDB][query].

###Quels sont les types de données pris en charge par DocumentDB ?
Les types de données primitifs pris en charge dans DocumentDB sont identiques à ceux pris en charge dans JSON. JSON dispose d'un système de type simple constitué de chaînes, de chiffres (double précision IEEE754), ainsi que de valeurs booléennes (true, false et null).  Des types de données plus complexes, tels que DateTime, Guid, Int64 et Geometry peuvent être représentés dans JSON et DocumentDB en créant des objets imbriqués à l'aide de l'opérateur { } et des tableaux à l'aide de l'opérateur []. 

###Quels sont les avantages concurrentiels de DocumentDB ?
DocumentDB prend en charge le contrôle d'accès concurrentiel optimiste via les balises d'entité HTTP ou ETags. Chaque ressource DocumentDB dispose d'un ETag et les clients DocumentDB incluent leur dernière version lue dans les requêtes en écriture. Si l'ETag est actuel, la modification est effectuée. Si la valeur a été modifiée en externe, le serveur rejette l'écriture avec un code de réponse " HTTP 412 Échec de la condition préalable ". Les clients doivent lire la dernière version de la ressource et relancer la requête. 

###Comment effectuer des transactions dans DocumentDB ?
DocumentDB prend en charge les transactions intégrées au langage via les déclencheurs et procédures stockées JavaScript. Toutes les opérations de la base de données dans les scripts sont exécutées avec un isolement de capture instantanée sur la collection. Une capture instantanée des versions des documents (ETags) est prise au début de la transaction et validée uniquement si le script fonctionne. Si le JavaScript renvoie une erreur, la transaction est annulée.

###Comment insérer des documents en bloc dans DocumentDB ? 
La prise en charge des procédures stockées par DocumentDB est un moyen efficace d'insérer en bloc. En développant une simple procédure stockée JavaScript qui accepte et insère des documents, vous pouvez exécuter des insertions en bloc. Ainsi, l'insertion en bloc sera exécutée sous forme de transaction, laissant la collection dans un état cohérent. Pour plus d'informations sur le modèle de programmation, consultez la section Développement de la [page de documentation DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###DocumentDB prend-il en charge la mise en cache des liens de ressource ?
Oui. DocumentDB étant un service RESTful, les liens de ressource sont immuables et peuvent être mis en cache. Les clients DocumentDB peuvent spécifier un en-tête " If-None-Match " pour des lectures en comparaison avec des ressources telles que des documents ou des collections. Ils peuvent également mettre à jour les copies locales uniquement lorsque la version du serveur a été modifiée. 




[preview-portal]: https://portal.azure.com
[query]: ../documentdb-sql-query/
