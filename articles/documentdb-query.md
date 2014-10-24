<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Requêtes à l'aide d'Azure DocumentDB

## Motivation

Le langage de requête SQL de DocumentDB prend en charge un sous-ensemble de syntaxe ANSI SQL et ajoute une prise en charge orientée documents sous la forme de structures de données imbriquées, de tableaux et de construction d'objets. Les requêtes sont exécutées efficacement par le biais des index de DocumentDB, qui sont automatiquement mis à jour pour toutes les propriétés des documents sans surcharger la gestion des index. Les développeurs peuvent également exploiter toute la puissance du JavaScript dans les requêtes en utilisant des fonctions définies par l'utilisateur.

## Pourquoi des requêtes SQL ?

DocumentDB prend en charge SQL afin d'offrir une interface simple et accessible pour l'interrogation des données. SQL est un langage à la structure simple et puissante, largement utilisé par les développeurs d'application. DocumentDB est dédié aux plateformes et normes ouvertes telles que HTTP, JSON, JavaScript et maintenant SQL.
Les requêtes SQL sont prises en charge via l'API REST, ainsi que par le biais des Kits de développement logiciel (SDK) clients .NET, Node.js et JavaScript. Les requêtes SQL sont également prises en charge dans des procédures stockées et des déclencheurs dans l'API JavaScript côté serveur. En plus des requêtes SQL, DocumentDB prend également en charge les requêtes via LINQ dans le Kit de développement logiciel (SDK) .NET.

# Types de données

Dans DocumentDB, les données des documents et métadonnées sont au format JSON (JavaScript Object Notation). JSON est largement utilisé du fait du nombre croissant d'applications JavaScript et de son évolution en format d'échange de données indépendant de toute plateforme. Du fait de son caractère autodescriptif, il est également adapté aux bases de données sans schéma.

Les types de données primitifs pris en charge dans les requêtes DocumentDB sont identiques à ceux de JSON. JSON est doté d'un système de type simple composé des éléments suivants :

-   Chaînes
-   Chiffres (double précision IEEE754)
-   Valeurs booléennes : true et false
-   Valeur null

Des types de données plus complexes peuvent être représentés dans JSON et DocumentDB en créant des objets imbriqués à l'aide de l'opérateur { } et des tableaux à l'aide de l'opérateur [ ].

# Syntaxe de requête

En utilisant le langage SQL de DocumentDB, les utilisateurs peuvent récupérer des données dans des documents JSON à l'aide des instructions **SELECT**. Voici un exemple d'instruction SQL pour une collection de documents contenant des messages sur un réseau social.

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

Le résultat d'une requête est un tableau de fragments de document JSON, un pour chaque document correspondant aux contraintes spécifiées.

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

La syntaxe formelle complète du langage de requête SQL est incluse à la fin de ce document.

# Clause FROM

La clause **FROM** est généralement le nom de la collection en cours dans laquelle la requête est exécutée. Elle permet par exemple de sélectionner tous les documents de la collection intitulés « posts ». Si le nom est incorrect, une erreur est renvoyée à l'utilisateur.

    SELECT * 
    FROM posts

Le mot clé **ROOT** peut être utilisé au lieu de faire référence au nom de la collection. En effet, par le biais du Kit de développement logiciel (SDK) ou de l'API REST, les requêtes peuvent être ciblées en fonction de l'étendue d'une collection spécifique.

    SELECT * 
    FROM ROOT

Les propriétés de sortie de FROM peuvent avoir des alias. Ceci est utile si vous faites appel à plusieurs sources FROM utilisant **JOIN**. Ce sujet est évoqué plus loin.

    SELECT P.* 
    FROM posts P

Il est également possible de faire une requête depuis une propriété imbriquée. Si les documents publiés contiennent par exemple un sous-document pour l'utilisateur auteur de la publication, il est possible de faire une requête à l'aide de la syntaxe suivante.

    SELECT U.* 
    FROM posts.user U

La source de l'instruction FROM peut également être un tableau scalaire (ensemble ordonné).

    SELECT E
    FROM ["documentdb", "json", "sql"] E

Il est également possible d'omettre l'instruction FROM tout en évaluant les expressions scalaires.

`SELECT "documentdb"`

# Clause WHERE (filtrage)

Le langage SQL de DocumentDB prend en charge une clause **WHERE** facultative qui filtre l'ensemble des résultats en fonction de conditions spécifiées que le document doit respecter. L'expression de filtre peut contenir des comparaisons logiques avec n'importe quelle propriété ou n'importe quel chemin imbriqué dans les documents.

Par exemple, une propriété telle qu'un message peut être utilisée dans les requêtes. Le filtrage est efficace à l'aide de l'index automatique de DocumentDB.

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

Les propriétés imbriquées peuvent être utilisées pour filtrer à l'aide de la notation en points. La longueur des chemins peut varier en fonction du schéma des documents.

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

Les valeurs individuelles des tableaux peuvent être référencées à l'aide de l'opérateur de déréférence du tableau. Le déroulement du tableau est pris en charge à l'aide du mot clé **IN** (évoqué plus tard).

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

Les comparaisons de plage (\>, \<. \>=, \<=, !=) sont prises en charge pour les valeurs numériques. Les chiffres peuvent également être comparés au résultat d'une expression arithmétique, comme présenté ci-dessous.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

Les opérateurs logiques peuvent être combinés à l'aide des opérateurs AND, OR et NOT, comme dans le langage SQL classique. Ces données sont traitées efficacement par l'intersection des index pour les propriétés spécifiées.

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## Gestion des propriétés manquantes et Null

Les données NULL sont prises en charge sous forme de mots clés et les propriétés peuvent leur être comparées, comme les autres données scalaires.

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

Le schéma des documents pouvant être inconnu ou rapidement modifié, le langage prend également en charge les opérateurs spéciaux pour vérifier les propriétés manquantes, notamment via l'opérateur ISUNDEFINED, similaire à **undefined** dans JavaScript.

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> Remarque : similaire à JavaScript, c'est-à-dire que undefined renvoie toujours la valeur false.

# Clause SELECT (projection)

La clause **SELECT** peut être utilisée pour extraire certaines propriétés de la requête. L'opérateur étoile (\*) renvoie les documents complets correspondant à la requête.

    SELECT * 
    FROM posts

Les champs spécifiés peuvent être extraits dans la clause SELECT. Les champs peuvent être atomiques (chaînes, chiffres, valeurs booléennes) ou complexes (tableaux, objets JSON). Les types complexes dans la clause SELECT, tels que « user » dans l'exemple ci-dessous, renvoient l'objet JSON entier sous la propriété. Les propriétés de la clause SELECT doivent avoir des noms ou des alias uniques.

    SELECT posts.id, posts.user
    FROM posts

> Remarque : JSON n'ayant pas d'ordre, les propriétés individuelles du document ne sont pas renvoyées dans un ordre spécifique.

> Remarque : les noms de champ doivent être qualifiés de manière explicite. Les documents n'ayant pas de schéma fixe, ceci est nécessaire pour l'exécution de la requête afin d'utiliser la liaison adaptée.

Vous pouvez également accéder aux propriétés en utilisant la syntaxe de recherche du dictionnaire, comme ci-dessous :

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> Remarque : la syntaxe de recherche du dictionnaire doit être utilisée pour contourner les noms de propriétés utilisant un mot clé réservé tel que FROM dans l'exemple ci-dessus.

## Évaluation des expressions

Les expressions scalaires peuvent également être utilisées avec des expressions. Quand aucun nom n'est spécifié, un nom généré automatiquement est utilisé comme espace réservé ($1, $2, $3, etc.).

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

Les expressions suivantes peuvent être utilisées dans les expressions de requête SQL. Les opérateurs sont fortement typés comme en SQL. Par exemple, equals (=) est une égalité forte, ce qui signifie que 5 != “5”.

| Opérateurs arithmétiques    | +, -, \*, /, % (Modulo)                          |
|-----------------------------|--------------------------------------------------|
| Opérateurs logiques         | AND, OR, NOT                                     |
| Opérateurs au niveau du bit | & (Bitwise AND), | (Bitwise OR), ^ (Bitwise XOR) |
| Opérateurs chaîne           | || (concaténer)                                  |
| Opérateurs de comparaison   | =, !=, \>, \<, \>=, \<=                          |

## Transformation JSON

Les requêtes SQL peuvent renvoyer des fragments JSON et non uniquement des documents complets : le mot clé **VALUE** peut être utilisé pour cela. Par exemple, le document suivant renvoie « 2 » au lieu de {“id”: “2”}.

    SELECT VALUE posts.id
    FROM posts

Des expressions JSON plus complexes peuvent être construites comme dans JavaScript à l'aide des opérateurs {} et []. Cela permet aux requêtes de bénéficier d'une flexibilité pour transformer la forme des ensembles de résultats.

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> Remarque : les valeurs peuvent être n'importe quelle expression valide, mais les étiquettes doivent être des littéraux (chaînes).

# Jonctions et itération

Dans les bases de données de documents, les données référencées sont intégrées en tant que sous-documents ou tableaux intégrés. De ce fait, le langage SQL prend en charge les documents internes, c'est-à-dire les jointures réflexives, à l'aide du mot clé **JOIN**.

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> Remarque : les jonctions sont à l'origine des jonctions croisées, mais puisque les données sont intégrées, elles agissent en tant que jonctions internes.

Dans l'exemple, l'opérateur **IN** est utilisé pour créer une itération de tous les éléments d'une source. La source d'une clause IN peut être soit un tableau, soit un objet. Lorsqu'il s'agit d'un objet, l'itérateur passe par chaque propriété. IN peut également être utilisé directement comme illustré ci-dessous. IN peut également être utilisé avec les littéraux d'un tableau.

    SELECT tag 
    FROM tag IN posts.tags

Les jonctions multiples peuvent être utilisées dans une instruction SQL, comme indiqué ci-dessous :

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# Fonctions définies par l'utilisateur

Les requêtes DocumentDB prennent en charge les extensions programmables sous la forme de fonctions définies par l'utilisateur JavaScript. Chaque collection peut avoir une ou plusieurs **fonctions définies par l'utilisateur** enregistrées qui transforment les fragments JSON et les référencent par nom directement dans les scripts.

Par exemple, l'utilisation de la création d'une fonction définie par l'utilisateur, « regex\_match », peut être définie comme suit :

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

Ensuite, elle peut être référencée dans les requêtes comme dans cet exemple :

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> Remarque : les fonctions définies par l'utilisateur ont accès uniquement aux paramètres d'entrée. Aucune opération de stockage DocumentDB (lecture, écriture, requête, etc.) n'est prise en charge dans les fonctions définies par l'utilisateur.

> Remarque : actuellement, une seule fonction définie par l'utilisateur peut être utilisée dans une requête.

Les fonctions définies par l'utilisateur peuvent traiter et renvoyer des fragments JSON, et non uniquement des littéraux. Par exemple, une fonction « array\_count » peut être définie comme suit :

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

Ensuite, elle peut être utilisée dans des requêtes pour trouver la taille d'un tableau au sein d'un document :

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

Les fonctions définies par l'utilisateur peuvent également être spécifiées dans la clause SELECT ou dans la clause FROM d'une requête. Par exemple :

    SELECT array_count(p.likes) AS count
    FROM posts p

Pour plus de détails sur la création et la gestion de fonctions définies par l'utilisateur, reportez-vous à la documentation sur la programmation avec JavaScript.

# Pagination

Chaque exécution de requête renvoie un lot de résultats en fonction de la taille de page configurée par le client. Pour lire tous les résultats d'une requête, les applications doivent paginer chaque ensemble de résultats jusqu'à ce qu'il n'y ait plus de données à lire. Pour lire un maximum de 10 documents par exemple, les clients peuvent modifier la taille de page à 10 afin de limiter le nombre maximum de documents renvoyés. Notez que les requêtes peuvent renvoyer moins de résultats que la taille de page ou même aucun résultat pour certaines requêtes. Pour lire tous les résultats d'une requête, les clients doivent récupérer le lot suivant à l'aide du jeton de liaison de la réponse jusqu'à ce qu'il soit vide.

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

Ceci n'est pas implémenté dans la syntaxe SQL sous forme de mot clé TOP ou LIMIT, car la fonctionnalité est déjà disponible via les jetons de liaison dans le Kit de développement logiciel (SDK) client/l'API REST sous-jacents. Pour plus d'informations et d'exemples, reportez-vous à la documentation de l'API REST pour les documents GET ou les méthodes ReadFeed dans les Kits de développement logiciel (SDK).

# Comportement de la cohérence des requêtes

DocumentDB prend en charge quatre niveaux de cohérence ajustables : Strong (Fort), Bounded-Staleness (En fonction de l'obsolescence), Session (Par session) et Eventual (Éventuel). Les requêtes offrent la même garantie que les niveaux de cohérence. Par défaut, les résultats d'une requête doivent correspondre au niveau de cohérence requis par le client. L'index de DocumentDB est structuré par des journaux et conçu pour être à jour et cohérent avec les données, quel que soit leur volume. Lorsque des documents sont insérés ou mis à jour, ils sont immédiatement disponibles dans les résultats de requête.

Pour des applications ayant besoin de cohérence éventuelle, la règle d'indexation peut être configurée pour utiliser l'indexation en différé. Dans ce cas, l'index est mis à jour d'une manière cohérente lorsque la collection est inactive. Pour récapituler, voici le tableau du comportement de cohérence des requêtes avec les différentes configurations de compte de base de données.

| Cohérence des données                             | Stratégie d'indexation | Comportement de requête                           |
|---------------------------------------------------|------------------------|---------------------------------------------------|
| Strong (Fort)                                     | Cohérent               | Strong (Fort)                                     |
| Bounded Staleness (En fonction de l'obsolescence) | Cohérent               | Bounded Staleness (En fonction de l'obsolescence) |
| Session                                           | Cohérent               | Session                                           |
| Eventual (Éventuel)                               | Cohérent               | Eventual (Éventuel)                               |
| Strong (Fort)                                     | Différé                | Eventual (Éventuel)                               |
| Bounded Staleness (En fonction de l'obsolescence) | Différé                | Eventual (Éventuel)                               |
| Session                                           | Différé                | Eventual (Éventuel)                               |
| Eventual (Éventuel)                               | Différé                | Eventual (Éventuel)                               |

Pour plus d'informations, consultez la documentation sur la stratégie d'indexation et la configuration.

# API et Kits de développement logiciel (SDK)

Les requêtes peuvent être exécutées à l'aide de l'API REST, des Kits de développement logiciel (SDK) clients et de l'API JavaScript côté serveur à l'aide de la syntaxe SQL.

## Requêtes à l'aide de l'API REST

Les applications peuvent poster des requêtes sur des collections à l'aide de l'API REST. Les éléments suivants doivent être inclus dans les demandes de requête :

-   Header x-ms-documentdb-isquery : true pour indiquer qu'il s'agit d'une requête.
-   En-tête Content-Type : application/sql pour indiquer que le langage SQL est utilisé.
-   Corps contenant l'instruction SELECT
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization: ...
    x-ms-continuation:
    x-ms-activity-id: 82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date: Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id: 9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery: True
    Cache-Control: no-cache
    x-ms-version: 2014-02-25
    User-Agent: Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type: application/sql
    Host: ...
    Content-Length: 59
    Expect: 100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> Remarque : la requête n'est pas prise en charge à l'aide de GET ou de chaînes de requête.

## Requêtes à l'aide du Kit de développement logiciel (SDK) .NET

Le Kit de développement logiciel (SDK) .NET prend en charge les requêtes à l'aide de la méthode CreateDocumentQuery qui accepte les requêtes SQL sous forme de chaînes. Le résultat de la requête est une interface IQueryable qui peut être traitée à l'aide de LINQ côté client.

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

Pour plus d'informations, consultez la documentation sur le Kit de développement logiciel (SDK) .NET.

## Requêtes dans des procédures stockées et des déclencheurs

L'API JavaScript côté serveur pour les procédures stockées et les déclencheurs prend également en charge les requêtes faites à l'aide de SQL.

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

Pour plus d'informations, consultez la documentation sur le Kit de développement logiciel (SDK) JavaScript côté serveur.

# Annexe A - Syntaxe SQL

Les schémas suivants montrent la syntaxe SQL formelle pour le langage de requête DocumentDB.

![][]

![][1]

![][2]

![][3]

![][4]

![][5]

![][6]

![][7]

![][8]

![][9]

![][10]

![][11]

![][12]

![][13]

![][14]

![][15]

![][16]

  []: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
