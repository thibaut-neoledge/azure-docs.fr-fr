<properties 
   pageTitle="Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio | Azure" 
   description="Découvrez comment installer Data Lake Tools Visual Studio et développer et tester des scripts U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="jgao"/>

# Didacticiel : Prise en main du langage U-SQL Azure Data Lake Analytics

U-SQL est un langage qui allie les avantages de SQL à la puissance d'expression de votre propre code de façon à traiter toutes les données, quelle que soit l'échelle. La fonctionnalité évolutive de requête distribuée d'U-SQL vous permet d'analyser efficacement les données du magasin et entre magasins relationnels comme les bases de données SQL Azure. Elle vous permet de traiter des données non structurées en appliquant des schémas lors de la lecture et d'intégrer une logique personnalisée et des UDF, et a l'extensibilité nécessaire pour permettre un contrôle précis de l'exécution à grande échelle. Pour en savoir plus sur la philosophie de conception sous-jacente à U-SQL, reportez-vous à ce [Billet de blog Visual Studio](http://blogs.msdn.com/b/visualstudio/archive/2015/09/28/introducing-u-sql.aspx).

Il existe des différences par rapport à ANSI SQL ou à T-SQL. Par exemple, ses mots-clés, comme SELECT, doivent être en MAJUSCULES.

Son système de types et son langage d'expression au sein des clauses SELECT, des prédicats WHERE, etc. sont en C#. En d'autres termes, les types de données sont les types C# et utilisent la sémantique NULL C#. Les opérations de comparaison au sein d'un prédicat suivent la syntaxe C# (ex. a == "foo"). Cela signifie également que les valeurs sont des objets .NET à part entière, ce qui vous permet d'utiliser facilement n'importe quelle méthode sur l'objet (ex. "f o o o".Split(' ')).

Pour plus d'informations, consultez [Référence U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###Configuration requise

Vous devez suivre le [didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

Dans ce didacticiel, vous avez exécuté une tâche Data Lake Analytics avec le script U-SQL suivant :

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

Ce script n'a aucune étape de transformation. Il lit le fichier source appelé **SearchLog.tsv**, le schématise et génère l'ensemble de lignes dans un fichier nommé **SearchLog-from-adltools.csv**.

Notez le point d'interrogation en regard du type de données du champ Durée. Cela signifie que le champ Durée peut être null.

Quelques concepts et mots-clés du script :

- **Variables de l'ensemble de lignes** : toute expression de requête qui produit un ensemble de lignes peut être affectée à une variable. U-SQL suit le modèle d'affectation de noms variable T-SQL, par exemple **@searchlog** dans le script. Remarque : l'affectation ne force pas l'exécution. Elle nomme simplement l'expression et vous donne la possibilité de construire des expressions plus complexes.
- **EXTRACT** vous donne la possibilité de définir un schéma lors de la lecture. Le schéma est spécifié par une paire nom de colonne et nom de type C# par colonne. Il utilise un **Extracteur**, par exemple **Extractors.Tsv()**, pour extraire les fichiers tsv. Vous pouvez développer des extracteurs personnalisés.
- **OUTPUT** prend un ensemble de lignes et le sérialise. Outputters.Csv() génère un fichier séparé par des virgules dans l'emplacement spécifié. Vous pouvez également développer des générateurs de sortie personnalisés.
- Notez que les deux chemins d'accès sont relatifs. Vous pouvez également utiliser des chemins d'accès absolus. Par exemple : 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser un chemin d'accès absolu pour accéder aux fichiers dans les comptes de stockage liés. La syntaxe des fichiers stockés dans des comptes Azure Storage liés est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Les conteneurs d'objets Blob Azure avec des autorisations d'accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.

## Utiliser des variables scalaires

Vous pouvez également utiliser des variables scalaires pour faciliter la maintenance de votre script. Le script U-SQL précédent peut également s'écrire comme suit :

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
      
## Transformer des ensembles de lignes

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

La clause WHERE utilise une [expression booléenne C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Vous pouvez utiliser le langage d'expressions C# pour faire vos propres expressions et fonctions. Vous pouvez même effectuer un filtrage plus complexe en les combinant avec des conjonctions logiques (and) et des disjonctions (or).

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
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();
        
La deuxième requête fonctionne sur le résultat du premier ensemble de lignes. Par conséquent, le résultat est une composition des deux filtres. Vous pouvez également réutiliser un nom de variable, les noms ont une portée lexicale.

## Ensembles de lignes agrégés

U-SQL vous fournit les classiques **ORDER BY**, **GROUP BY** et les agrégations.

La requête suivante recherche la durée totale par région et renvoie ensuite les 5 premiers durées dans l'ordre.

Les ensembles de lignes U-SQL ne conservent pas leur ordre pour la requête suivante. Par conséquent, pour ordonner un résultat, vous devez ajouter ORDER BY à l'instruction OUTPUT, comme indiqué ci-dessous :

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

La clause U-SQL HAVING peut être utilisée pour restreindre le résultat aux groupes qui remplissent la condition HAVING :

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

## Jointures de données

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


U-SQL prend uniquement en charge la syntaxe de jointure conforme à ANSI : EnsembleLignes1 JOIN EnsembleLignes2 ON prédicat. L'ancienne syntaxe FROM EnsembleLignes1, EnsembleLignes2 WHERE prédicat n'est PAS prise en charge. Le prédicat dans un JOIN doit être une jointure d'égalité et non une expression. Si vous souhaitez utiliser une expression, ajoutez-la à la clause SELECT d'un ensemble de lignes précédent. Si vous souhaitez effectuer une autre comparaison, vous pouvez la déplacer dans la clause WHERE.

        
## Créer des bases de données, des fonctions table, des vues et des tables

U-SQL vous permet d'utiliser des données dans le contexte d'une base de données et du schéma. Par conséquent, vous n'avez pas besoin de toujours lire ou écrire dans des fichiers.

Chaque script U-SQL s'exécute avec une base de données par défaut (master) et le schéma par défaut (dbo) comme contexte par défaut. Vous pouvez créer votre propre base de données et/ou schéma. Pour modifier le contexte, utilisez l'instruction **USE**.


### Créer une fonction table

Dans le script U-SQL précédent, vous avez réutilisé EXTRACT en lisant le même fichier source. La fonction table U-SQL vous permet d'encapsuler les données pour une réutilisation ultérieure.

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
    
Le script suivant montre comment utiliser la fonction table définie dans le script précédent :

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
        
### Créer des vues

Si vous ne disposez que d'une seule expression de requête que vous voulez abstraire sans la paramétrer, vous pouvez créer une vue au lieu d'une fonction table.

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
    
Le script suivant montre l'utilisation de la vue définie :

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

### créez des tables 

De façon similaire à une table de base de données relationnelle, U-SQL vous permet de créer une table avec un schéma prédéfini ou de créer une table et de déduire le schéma à partir de la requête de remplissage de la table (également appelée CREATE TABLE AS SELECT ou CTAS).

Le script suivant crée une base de données et deux tables :

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
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
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### Tables de requête

Vous pouvez interroger les tables (créées dans le script précédent) de la même façon que vous interrogez les fichiers de données. Au lieu de créer un ensemble de lignes avec EXTRACT, vous pouvez maintenant faire simplement référence au nom de table.

Le script de transformation que vous avez utilisé précédemment est modifié pour lire à partir des tables :

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

Remarque : actuellement, vous ne pouvez pas exécuter une instruction SELECT sur une table dans le script où vous créez cette table.


##Conclusion

Ce didacticiel ne couvre qu'une petite partie d'U-SQL. En raison de la portée de ce didacticiel, il ne peut pas tout traiter, par exemple :

- Utilisation de CROSS APPLY pour décompresser des parties des chaînes, de tableaux et de plans en lignes.
- Utilisation de jeux de données partitionnés (ensembles de fichiers et tables partitionnées).
- Développement d'opérateurs définis par l'utilisateur, notamment des extracteurs, des générateurs de sortie, des processeurs et des agrégateurs définis par l'utilisateur en C#.
- Utilisation des fonctions de fenêtrage U-SQL.
- Gestion du code U-SQL avec les procédures stockées, les fonctions table et les vues.
- Exécution d'un code personnalisé arbitraire sur vos nœuds de traitement. 
- Connexion aux bases de données SQL Azure et fédération de requêtes entre celles-ci et vos données U-SQL et Azure Data Lake.

## Voir aussi 

- [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Utilisation des fonctions fenêtre U-SQL dans les tâches Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
- [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## Donnez-nous votre avis

- [Suggérer un nouveau backlog de documentation](data-lake-analytics-documentation-backlog.md)
- [Soumettre une demande de fonctionnalité](http://aka.ms/adlafeedback)
- [Obtenir de l'aide dans les forums](http://aka.ms/adlaforums)
- [Faire des commentaires sur U-SQL](http://aka.ms/usqldiscuss)

<!---HONumber=AcomDC_0107_2016-->