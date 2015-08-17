<properties
   pageTitle="Conception de tables dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la conception de tables dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Conception de tables dans SQL Data Warehouse #
SQL Data Warehouse est un système de base de données distribuée à traitement massivement parallèle. Par conséquent, il stocke les données sur divers emplacements, appelés **distributions**. Chaque **distribution** est similaire à un compartiment, qui stocke un sous-ensemble unique de données dans l’entrepôt de données. En répartissant les données et les fonctions de traitement sur plusieurs nœuds, SQL Data Warehouse est en mesure de proposer une évolutivité immense, bien supérieure à ce qu’offre n’importe quel système unique.

Lorsqu’une table est créée dans SQL Data Warehouse, elle est répartie sur l’ensemble des distributions.

Cet article aborde les rubriques suivantes :

- Types de données pris en charge
- Principes de la distribution de données
- Distribution par tourniquet (round robin)
- Distribution par hachage
- Partitionnement de tables
- Statistiques
- Fonctionnalités non prises en charge

## Types de données pris en charge
SQL Data Warehouse prend en charge les types de données métiers les plus courants :

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

Dans votre entrepôt de données, vous pouvez identifier les colonnes qui contiennent des types incompatibles, via la requête suivante :

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

La requête inclut tout type de données défini par l’utilisateur qui n’est pris en charge.

Si votre base de données inclut des types non pris en charge, ne vous inquiétez pas. Voici d’autres solutions possibles :

Au lieu du paramètre...

- **geometry** : utilisez un type « varbinary » ;
- **geography** : utilisez un type « varbinary » ;
- **hierarchyid** : utilisez un type CLR non natif ;
- **image**, **text**, **ntext** : en cas de paramètre basé sur du texte, utilisez une valeur «  varchar/nvarchar » (la plus petite possible) ;
- **nvarchar (max)** : utilisez le paramètre « varchar (4000) » ou une valeur inférieure pour optimiser les performances ;
- **numeric** : utilisez une valeur décimale ;
- **sql\_variant** : fractionnez la colonne en plusieurs colonnes fortement typées ;
- **sysname** : utilisez le paramètre « nvarchar (128) » ;
- **table** : appliquez une conversion vers des tables temporaires ;
- **timestamp** : modifiez le code afin d’utiliser le paramètre « datetime2 » et la fonction `CURRENT_TIMESTAMP`. Remarque : vous ne pouvez pas utiliser le paramètre « current\_timestamp » en tant que contrainte par défaut ; la valeur ne sera pas automatiquement mise à jour. Si vous devez migrer les valeurs « rowversion » à partir d’une colonne de type horodatage, utilisez le paramètre BINARY(8) ou VARBINARY(8) pour les valeurs de version de ligne NOT NULL ou NULL ;
- **varchar(max)** : utilisez la valeur « varchar(8000) » ou une valeur inférieure pour optimiser les performances ;
- **uniqueidentifier** : utilisez le paramètre « varbinary (8) » ;
- **types définis par l’utilisateur** : appliquez à nouveau les types natifs, le cas échéant ;
- **xml** : utilisez la valeur « varchar(8000) » ou une valeur inférieure pour optimiser les performances ; répartissez les valeurs sur plusieurs colonnes, le cas échéant.

Prise en charge partielle :

- Les contraintes par défaut prennent uniquement en charge des littéraux et des constantes. Les expressions ou fonctions non déterministes comme `GETDATE()` ou `CURRENT_TIMESTAMP` ne sont pas prises en charge.

> [AZURE.NOTE]Définissez les tables de façon à ce que la taille de ligne maximale (y compris la longueur totale des colonnes à longueur variable) ne dépasse pas 32 767 octets. Vous pouvez définir des lignes incluant des données d’une longueur variable, susceptibles de dépasser cette valeur maximale. Toutefois, vous ne pourrez pas insérer ces données dans la table. Essayez également de limiter la taille de vos colonnes à longueur variable, afin d’optimiser le débit lors de l’exécution des requêtes.

## Principes de la distribution de données

Il existe deux moyens de distribuer les données dans SQL Data Warehouse :

1. Vous pouvez distribuer les données en fonction des valeurs de hachage d’une colonne unique.
2. Vous pouvez les distribuer de manière équitable, mais aléatoire.  

Le type de distribution des données est déterminé au niveau de la table. Toutes les tables sont distribuées de sorte que vous puissiez prendre cette décision pour chaque table de la base de données SQL Data Warehouse.

La première option porte le nom de **distribution par tourniquet (round robin)**, ou hachage aléatoire, dans certains cas. Il s’agit de l’option par défaut, ou sans échec.

La deuxième option est qualifiée de **distribution par hachage**. On peut la considérer comme une forme de distribution des données optimisée. Il s’agit de l’option par défaut lorsque des clusters de tables partagent des critères d’agrégation et/ou d’association communs.

## Distribution par tourniquet (round robin)

Ce type de distribution permet de répartir les données de manière aussi équitable que possible, pour toutes les distributions. Des mémoires tampons contenant des lignes de données sont allouées tour à tour (d’où l’idée de tourniquet) à chaque distribution. Ce processus est répété jusqu’à ce que toutes les mémoires tampons de données aient été allouées. À aucun moment les données ne sont triées ni classées dans une table avec distribution par tourniquet (round robin). C’est pour cette raison que ce type de distribution est parfois appelé « hachage aléatoire ». Les données sont simplement réparties sur les distributions, de manière aussi uniforme que possible.

Voici un exemple de table avec distribution par tourniquet (round robin) :

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

En voici un autre :

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
)
;
```

> [AZURE.NOTE]Comme vous pouvez le constater, le deuxième exemple ne fait mention d’aucune clé de distribution. Le type de distribution par tourniquet (round robin) est le type par défaut ; par conséquent, il n’est pas absolument nécessaire. Toutefois, comme il est explicite, il constitue une bonne pratique, car il permet de garantir que vos pairs sont avertis de vos intentions lorsque vous révisez la conception de la table.

Ce type de table est couramment utilisé lorsqu’il n’existe aucune colonne clé évidente selon laquelle hacher les données. Il peut également être utilisé par des tables plus petites ou moins importantes lorsque le coût du mouvement n’est pas très intéressant.

Le processus de chargement des données dans une table avec distribution par tourniquet (round robin) peut être plus rapide qu’une opération de chargement dans une table avec distribution par hachage. Dans le cas d’une table avec distribution par tourniquet (round robin), il n’est pas nécessaire de comprendre la nature des données ou d’exécuter le hachage avant le chargement. Pour cette raison, ce type de table représente souvent une cible de chargement adéquate.

> [AZURE.NOTE]Lorsqu’elles font l’objet d’une distribution par tourniquet (round robin), les données sont allouées à la distribution au niveau de la *mémoire tampon*.

### Recommandations

Vous pouvez envisager une distribution par tourniquet (round robin) des données de votre table dans les cas suivants :

- lorsqu’il n’existe aucune clé de jointure évidente ;
- si vous ne connaissez pas la clé de distribution du candidat au hachage ;
- si la table ne partage aucune clé de jointure commune avec d’autres tables ;
- si la jointure est moins importante que d’autres dans la requête ;
- si la table correspond à une table de chargement initial.

## Distribution par hachage

Ce type de distribution exploite une fonction interne pour répartir un jeu de données sur les distributions, via le hachage d’une colonne unique. Lorsque les données sont hachées, aucun ordre explicite n’est attribué aux données allouées à une distribution. Toutefois, le hachage est un processus déterministe. De ce fait, les résultats du hachage sont prévisibles. Ainsi, le hachage d’une colonne de nombres entiers contenant la valeur 10 aura pour résultat la même valeur de hachage. Cela signifie que ***n’importe quelle*** colonne de nombres entiers hachée contenant la valeur 10 sera allouée à la même distribution. Il s’agit d’un état de fait également valable d’une table à l’autre.

Le côté prévisible du hachage joue un rôle critique. Il signifie que la distribution par hachage des données peut permettre d’améliorer les performances lors de la lecture des données et de la jointure des tables les unes avec les autres.

Comme vous le verrez ci-dessous, la distribution par hachage peut être très efficace pour l’optimisation des requêtes. C’est pour cette raison qu’on la considère comme une forme de distribution des données optimisée.

> [AZURE.NOTE]N’oubliez pas que le hachage n’est pas basé sur la valeur des données, mais plutôt sur le type des données hachées.

Voici une table dont les données ont été distribuées par hachage, via le paramètre ProductKey.

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE]Lorsqu’elles font l’objet d’une distribution par hachage, les données sont allouées à la distribution au niveau de la ligne.

## Partitions de table
Les partitions de table sont prises en charge ; elles sont simples à définir.

Exemple de commande `CREATE TABLE` partitionnée par SQL Data Warehouse :

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Vous pouvez constater qu’aucun schéma ou fonction de partitionnement ne figure dans la définition. En effet, ces paramètres sont pris en charge lorsque la table est créée. Il vous suffit d’identifier les points limites de la colonne qui servira de clé de partitionnement.

## Statistiques

SQL Data Warehouse utilise un optimiseur de requête distribuée pour créer le plan de requête approprié lorsque des utilisateurs interrogent des tables. Une fois créé, ce plan de requête fournit la stratégie et la méthode utilisées par la base de données pour accéder aux données et répondre à la demande de l’utilisateur. L’optimiseur de requête de SQL Data Warehouse est basé sur le coût. En d’autres termes, il compare diverses options (plans) en fonction de leur coût relatif et choisit le plan le plus efficace pour la situation. Par conséquent, il lui faut un grand nombre d’informations, car il doit être à même de prendre des décisions éclairées et basées sur le coût. Ce logiciel contient des informations sur les statistiques portant sur la table (concernant la taille de cette dernière) et incluses dans les objets de base de données appelés `STATISTICS`.

Les statistiques sont conservées dans une ou plusieurs colonnes d’index ou de tables. Elles apportent à l’optimiseur basé sur le coût des informations importantes sur la cardinalité et la sélectivité des valeurs. Cela peut s’avérer particulièrement utile lorsqu’il a besoin d’évaluer des classes WHERE, JOIN, GROUP BY et HAVING dans une requête. Pour cette raison, il est très important que les informations contenues dans ces objets de statistiques reflètent *précisément* l’état actuel de la table. Vous devez impérativement comprendre que la précision du coût importe avant tout. Si les statistiques reflètent précisément l’état de la table, il est alors possible de comparer les différents plans afin de trouver celui qui s’avérera le moins coûteux. Si elles ne sont pas précises, SQL Data Warehouse peut choisir un plan incorrect.

Dans SQL Data Warehouse, les statistiques figurant dans les colonnes sont définies par l’utilisateur.

Nous devons donc les créer nous-mêmes. Comme nous l’avons vu, ce processus de création ne doit pas être négligé. Les logiciels SQL Server et SQL Data Warehouse sont très différents à ce niveau : SQL Server crée automatiquement des statistiques en cas de requête portant sur des colonnes. Par défaut, SQL Server met automatiquement ces statistiques à jour. Toutefois, dans SQL Data Warehouse, les statistiques doivent être créées manuellement, et gérées à la main.

### Recommandations

Lorsque vous générez des statistiques, tenez compte des recommandations suivantes :

1. Créez des statistiques à une colonne sur les colonnes utilisées dans les clauses `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY` et `DISTINCT`.
2. Générez des statistiques à plusieurs colonnes sur les clauses composites.
3. Mettez les statistiques à jour régulièrement. Rappelez-vous : ce processus n’est pas effectué automatiquement !

>[AZURE.NOTE]Bien souvent, SQL Data Warehouse s’appuie uniquement sur le paramètre `AUTOSTATS` pour vérifier que les statistiques des colonnes sont à jour. Ce n’est pas une meilleure pratique, même dans le cas d’entrepôts de données SQL Server. Les `AUTOSTATS` sont déclenchés par un taux de changement de 20 %, ce qui peut être insuffisant dans les tables de faits volumineuses qui contiennent des millions, voire des milliards de lignes. Ainsi, il est toujours judicieux de s’assurer de la mise à jour des statistiques, afin de garantir que ces dernières reflètent de manière précise la cardinalité de la table.

## Fonctionnalités non prises en charge
SQL Data Warehouse n’utilise ou ne prend pas en charge les fonctionnalités suivantes :

- Clés primaires
- Clés étrangères
- Contraintes CHECK
- Contraintes uniques
- Index uniques
- Colonnes calculées
- Colonnes éparses
- Types définis par l’utilisateur
- Vues indexées
- Identités
- Séquences
- Déclencheurs
- Synonymes


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->