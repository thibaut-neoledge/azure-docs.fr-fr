---
title: 'Didacticiel : Prise en main du langage U-SQL Azure Data Lake Analytics | Microsoft Docs'
description: "Ce didacticiel permet d’en savoir plus sur le langage U-SQL Azure Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: dc8c7beaf5b8e8d4f5467ffe22390c41f446d787
ms.lasthandoff: 03/04/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Didacticiel : Prise en main du langage U-SQL Azure Data Lake Analytics
U-SQL est un langage qui combine les avantages de SQL à la puissance d’expression de votre propre code pour traiter les données quelle que soit l’échelle. La fonctionnalité évolutive de requête distribuée d’U-SQL vous permet d’analyser efficacement les données entre magasins relationnels comme Azure SQL Database. Avec U-SQL, vous pouvez traiter des données non structurées en appliquant des schémas de lecture et en insérant une logique personnalisée et des fonctions définies par l'utilisateur. En outre, U-SQL comprend l’extensibilité qui vous donne un contrôle précis sur l’exécution à l’échelle. Pour en savoir plus sur la philosophie de conception sur laquelle repose U-SQL, consultez la publication Visual Studio [Présentation de U-SQL – Un langage qui facilite le traitement des Big Data](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

U-SQL diffère en certains points d'ANSI SQL ou de T-SQL. Par exemple, les mots clés tels que SELECT doivent être en lettres majuscules.

 Son système de type et son langage d'expression au sein des clauses SELECT et des prédicats WHERE sont en C#. En d'autres termes, les types de données sont les types C#. Ils utilisent la sémantique C# NULL, et les opérations de comparaison au sein d'un prédicat suivent la syntaxe C# (par ex., a == "foo"). Cela signifie également que les valeurs sont des objets .NET à part entière, ce qui vous permet d’utiliser facilement n’importe quelle méthode sur l’objet (par ex., "f o o o".Split(’ ’)).

Pour plus d’informations, voir le guide de [référence sur le langage U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

### <a name="prerequisites"></a>Composants requis
Si vous ne l'avez pas déjà fait, vous devez suivre le [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Une fois que vous avez terminé le didacticiel, revenez au présent article.

Vous avez exécuté dans le didacticiel une tâche Azure Data Lake Analytics avec le script U-SQL suivant :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Ce script n'a aucune étape de transformation. Il lit le fichier source appelé SearchLog.tsv, le schématise et écrit l’ensemble de lignes dans un fichier nommé SearchLog-first-u-sql.csv.

Notez le point d'interrogation en regard du type de données du champ **Durée**. Cela signifie que le champ **Durée** pourrait avoir la valeur null.

Dans le script, vous trouverez les concepts et les mots clés suivants :

* Variables de l'ensemble de lignes: toute expression de requête qui produit un ensemble de lignes peut être affectée à une variable. U-SQL suit le modèle d’affectation de noms variable T-SQL (@searchlog, par exemple) dans le script.

 >[!NOTE]
 >L'affectation ne force pas l'exécution. Elle nomme simplement l’expression pour vous permettre de construire des expressions plus complexes.
* EXTRACT : à l’aide de ce mot clé, vous pouvez définir un schéma de lecture. Le schéma est spécifié par une paire nom de colonne et nom de type C# par colonne. Il utilise un Extracteur (par exemple, Extractors.Tsv()) pour extraire les fichiers tsv. Vous pouvez développer des extracteurs personnalisés.
* OUTPUT : ce mot-clé prend un ensemble de lignes et le sérialise. Outputters.Csv() génère un fichier séparé par des virgules à l'emplacement spécifié. Vous pouvez également développer des générateurs de sortie personnalisés.

 >[!NOTE]
 >Les deux chemins d'accès sont relatifs. Vous pouvez également utiliser des chemins d'accès absolus. Par exemple :    
 >     adl://\<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >Vous devez utiliser un chemin d'accès absolu pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe des fichiers stockés dans le compte de stockage Azure lié est : wasb://\<nom-du-conteneur-blob>@\<nom-du-compte-de-stockage>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >Les conteneurs de stockage d’objets Blob Azure avec des autorisations d’accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.

## <a name="use-scalar-variables"></a>Utiliser des variables scalaires
Vous pouvez également utiliser des variables scalaires pour faciliter la maintenance de votre script. Le script U-SQL précédent peut également s'écrire comme suit :

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformer des ensembles de lignes
Utilisez **SELECT** pour transformer des ensembles de lignes :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La clause WHERE utilise une [expression booléenne C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Vous pouvez utiliser le langage d'expressions C# pour faire vos propres expressions et fonctions. Vous pouvez même effectuer un filtrage plus complexe en les combinant avec des conjonctions logiques (AND) et des disjonctions (OR).

Le script suivant utilise la méthode DateTime.Parse() et une conjonction.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >La deuxième requête fonctionne sur le résultat du premier ensemble de lignes, ce qui crée une combinaison des deux filtres. Vous pouvez également réutiliser un nom de variable, et les noms ont une portée lexicale.

## <a name="aggregate-rowsets"></a>Ensembles de lignes agrégés
U-SQL vous fournit les ORDER BY, GROUP BY et les agrégations que vous connaissez déjà.

La requête suivante recherche la durée totale par région, puis affiche les cinq premières durées dans l’ordre.

Les ensembles de lignes U-SQL ne conservent pas leur ordre pour la requête suivante. Par conséquent, pour ordonner un résultat, vous devez ajouter ORDER BY à l'instruction OUTPUT :

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

La clause U-SQL ORDER BY doit être combinée avec la clause FETCH dans une expression SELECT.

La clause U-SQL HAVING peut être utilisée pour restreindre le résultat aux groupes qui remplissent la condition HAVING :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Jointures de données
U-SQL fournit les opérateurs de jointure courants comme INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN et SEMI JOIN pour réaliser des jointures non seulement entre tables, mais aussi entre ensembles de lignes (même produites à partir de fichiers).

Le script suivant associe le journal de recherche avec un journal d'impression de publicités et nous renvoie les publicités associées à la chaîne de requête pour une date donnée.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL prend uniquement en charge la syntaxe de jointure conforme à ANSI : Rowset1 JOIN Rowset2 ON prédicat. L'ancienne syntaxe FROM EnsembleLignes1, EnsembleLignes2 WHERE prédicat n'est _pas_ prise en charge.
Le prédicat dans un JOIN doit être une jointure d'égalité et non une expression. Si vous souhaitez utiliser une expression, ajoutez-la à la clause SELECT d'un ensemble de lignes précédent. Si vous souhaitez effectuer une autre comparaison, vous pouvez la déplacer dans la clause WHERE.

## <a name="create-databases-table-valued-functions-views-and-tables"></a>Créer des bases de données, des fonctions table, des vues et des tables
Dans U-SQL, vous pouvez utiliser des données dans le contexte d’une base de données et d'un schéma, et vous ne devez pas toujours accéder en lecture ou en écriture aux fichiers.

Chaque script U-SQL s'exécute avec une base de données par défaut (master) et le schéma par défaut (DBO) comme contexte par défaut. Vous pouvez créer votre propre base de données ou schéma. Pour modifier le contexte, utilisez l'instruction USE.

### <a name="create-a-tvf"></a>Création d'une TVF
Dans le script U-SQL précédent, vous avez réutilisé EXTRACT pour lire depuis le même fichier source. Avec la fonction table U-SQL, vous pouvez encapsuler les données pour une réutilisation ultérieure.  

Le script suivant crée une fonction table appelée *Searchlog()* dans la base de données et le schéma par défaut :

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

Le script suivant montre comment utiliser la fonction table définie dans le script précédent :

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Créer des vues
Si vous ne disposez que d'une seule expression de requête et ne souhaitez pas créer un paramètre à partir de cette expression, vous pouvez créer une vue au lieu d'une fonction table.

Le script suivant crée une vue appelée *SearchlogView* dans la base de données et le schéma par défaut :

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Le script suivant montre l'utilisation de la vue définie :

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>créez des tables
De façon similaire aux tables de base de données relationnelle, U-SQL vous permet de créer une table avec un schéma prédéfini ou de créer une table et de déduire le schéma à partir de la requête de remplissage de la table (également appelée CREATE TABLE AS SELECT ou CTAS).

Créez une base de données et deux tables avec le script suivant :

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tables de requête
Vous pouvez interroger des tables, par exemple celles que vous avez créées dans le script précédent, de la même manière que vous interrogez les fichiers de données. Au lieu de créer un ensemble de lignes avec EXTRACT, vous pouvez maintenant faire référence au nom de table.

Pour lire à partir des tables, modifiez le script de transformation que vous avez utilisé précédemment :

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >Actuellement, vous ne pouvez pas exécuter une instruction SELECT sur une table dans le script dans lequel vous avez créé cette table.

## <a name="conclusion"></a>Conclusion
Ce didacticiel couvre uniquement une petite partie du langage U-SQL. En raison de sa portée limitée, il n’aborde pas les nombreux autres avantages du langage U-SQL. Vous pouvez par exemple afficher :

* Utilisation de CROSS APPLY pour décompresser des parties des chaînes, de tableaux et de plans en lignes.
* Utilisation de jeux de données partitionnés (ensembles de fichiers et tables partitionnées).
* Développement d’opérateurs définis par l’utilisateur, notamment des extracteurs, des générateurs de sortie, des processeurs et des agrégateurs définis par l’utilisateur en C#.
* Utilisation des fonctions de fenêtrage U-SQL.
* Gestion du code U-SQL avec les vues, les fonctions table et les procédures stockées.
* Exécution d'un code personnalisé arbitraire sur vos nœuds de traitement.
* Connexion aux bases de données SQL et fédération de requêtes entre celles-ci et vos données U-SQL et Azure Data Lake.

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilisation des fonctions U-SQL dans les travaux Analytique Data Lake Azure](data-lake-analytics-use-window-functions.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Donnez-nous votre avis
* [Soumettre une demande de fonctionnalité](http://aka.ms/adlafeedback)
* [Obtenir de l'aide dans les forums](http://aka.ms/adlaforums)
* [Faire des commentaires sur U-SQL](http://aka.ms/usqldiscuss)

