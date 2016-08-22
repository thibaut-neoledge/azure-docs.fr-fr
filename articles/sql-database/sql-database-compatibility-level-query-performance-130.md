<properties
	pageTitle="Niveau de compatibilité, comment l’évaluer | Microsoft Azure"
	description="Procédure et outils pour déterminer le niveau de compatibilité adapté à votre base de données sur Base de données SQL Azure ou Microsoft SQL Server"
	services="sql-database"
	documentationCenter=""
	authors="alainlissoir"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.devlang="NA"
	ms.tgt_pltfrm="NA"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="alainl"/>


# Amélioration des performances des requêtes avec le niveau de compatibilité 130 dans Base de données SQL Azure


Base de données SQL Azure exécute en toute transparence des centaines de milliers de bases de données à différents niveaux de compatibilité, en conservant et en garantissant la compatibilité ascendante avec la version correspondante de Microsoft SQL Server pour tous ses clients.

Par conséquent, rien n’empêche les utilisateurs qui mettent les bases de données au dernier niveau de compatibilité, de tirer parti des nouvelles fonctionnalités d’optimiseur de requête et de processeur de requête. À titre de rappel historique, la correspondance entre les versions de SQL et les niveaux de compatibilité par défaut est la suivante :

- 100 : dans SQL Server 2008 et Base de données SQL Azure V11.
- 110 : dans SQL Server 2012 et Base de données SQL Azure V11.
- 120 : dans SQL Server 2014 et Base de données SQL Azure V12.
- 130 : dans SQL Server 2016 et Base de données SQL Azure V12.


> [AZURE.IMPORTANT] Dès **mi-juin 2016**, dans Base de données SQL Azure, le niveau de compatibilité par défaut sera 130 au lieu de 120 pour les bases de données **créées**.
> 
> Les bases de données créées avant mi-juin 2016 ne sont *pas* concernées et conservent leur niveau de compatibilité actuel (100, 110 ou 120). Les bases de données qui sont migrées de Base de données SQL Azure V11 vers Base de données SQL Azure V12 ne changent pas non plus de niveau de compatibilité.


Dans cet article, nous détaillons les avantages du niveau de compatibilité 130 et expliquons comment tirer parti de ces avantages. Nous abordons les éventuels effets secondaires sur les performances des requêtes pour les applications SQL existantes.


## À propos du niveau de compatibilité 130


Tout d’abord, pour connaître le niveau de compatibilité actuel de votre base de données, exécutez l’instruction Transact-SQL suivante.


```
SELECT compatibility_level
	FROM sys.databases
	WHERE name = '<YOUR DATABASE_NAME>’;
```


Avant ce passage au niveau 130 pour les **nouvelles** bases de données, examinons les raisons d’un tel changement à l’aide de quelques requêtes très simples et voyons ce que chacun peut en retirer.

Le traitement des requêtes dans les bases de données relationnelles peut être très complexe et requérir de grandes compétences en informatique et en mathématiques pour comprendre les choix de conception et les comportements. Dans ce document, le contenu a été volontairement simplifié pour s’assurer que toute personne disposant de connaissances techniques de base puisse comprendre l’impact du nouveau niveau de compatibilité et déterminer comment bénéficier des applications.

Voyons rapidement les apports du niveau de compatibilité 130. Vous trouverez plus de détails dans [Niveau de compatibilité ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mais voici un bref résumé :

- L’opération Insert d’une instruction Insert-select peut être multithread ou avoir un plan parallèle. Auparavant, elle était monothread.
- La table optimisée en mémoire et les requêtes de variables de table peuvent désormais avoir des plans parallèles. Auparavant, cette opération était également monothread.
- Les statistiques de la table optimisée en mémoire peuvent maintenant être échantillonnées et sont mises à jour automatiquement. Pour plus d’informations, consultez [Nouveautés : OLTP en mémoire (optimisation en mémoire)](https://msdn.microsoft.com/library/bb510411.aspx#InMemory).
- Traitement en mode batch ou en mode ligne avec index columnstore
  - Les tris sur une table avec un index columnstore s’effectuent maintenant en mode batch.
  - Les agrégats de fenêtres fonctionnent maintenant en mode batch, comme les instructions TSQL LAG/LEAD.
  - Les requêtes exécutées sur les tables columnstore avec plusieurs clauses distinctes sont traitées en mode batch.
  - Les requêtes exécutées sous DOP=1 ou avec un plan série sont également traitées en mode batch.
- Enfin, les améliorations apportées à l’estimation de la cardinalité sont issues du niveau de compatibilité 120. Pour ceux d’entre vous qui utilisent un niveau de compatibilité antérieur (comme 100 ou 110), le passage au niveau 130 apporte également ces améliorations qui influencent les performances des requêtes de vos applications.


## Mise en pratique du niveau de compatibilité 130


Tout d’abord, procurons-nous des tables, des index et des données aléatoires créées pour tester certaines de ces nouvelles fonctionnalités. Les exemples de script TSQL peuvent s’exécuter sous SQL Server 2016 ou Base de données SQL Azure. Toutefois, lorsque vous créez une base de données SQL Azure, veillez à choisir au moins une base de données P2, car il vous faut deux cœurs au minimum pour utiliser plusieurs threads et tirer parti de ces fonctionnalités.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
	(EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
	(Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
	(c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
	(‘Blue’, RAND() * 100000, RAND() * 100000),
	(‘Yellow’, RAND() * 100000, RAND() * 100000),
	(‘Red’, RAND() * 100000, RAND() * 100000),
	(‘Green’, RAND() * 100000, RAND() * 100000),
	(‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Maintenant, intéressons-nous à certaines fonctionnalités de traitement des requêtes, qui sont fournies avec un niveau de compatibilité de 130.


## Opération INSERT parallèle


Les instructions TSQL ci-dessous exécutent l’opération INSERT sous les niveaux de compatibilité 120 et 130, respectivement dans un modèle monothread (120) et multithread (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
	SELECT C1, COUNT(C2) * 10 * RAND()
		FROM T_source
		GROUP BY C1
	OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
	SELECT C1, COUNT(C2) * 10 * RAND()
		FROM T_source
		GROUP BY C1
	OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


En demandant le plan de requête actuel et en examinant sa représentation sous forme de graphique ou son contenu XML, vous pouvez déterminer la fonction d’estimation de la cardinalité à l’œuvre. L’examen des plans côte à côte sur la figure 1 montre clairement que l’opération INSERT avec index columnstore passe du mode série dans le niveau 120 au mode parallèle dans le niveau 130. Notez également que l’icône de l’itérateur dans le niveau 130 montre deux flèches parallèles, indiquant une exécution en parallèle. Si vous avez des opérations INSERT volumineuses à traiter, l’exécution en parallèle liée au nombre de cœurs à disposition pour la base de données est plus performante : jusqu’à 100 fois plus rapide selon le cas.


*Figure 1 : passage de l’opération INSERT du mode série au mode parallèle dans le niveau de compatibilité 130.*


![La figure 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## Mode Batch SÉRIE


Dans le niveau de compatibilité 130, le traitement des lignes de données s’effectue en mode batch. Tout d’abord, les opérations en mode batch ne sont disponibles que si vous avez un index columnstore. Ensuite, un batch qui représente 900 lignes environ utilise une logique de code optimisée pour un processeur multicœur et un débit de mémoire supérieur. Dans la mesure du possible, il exploite directement les données compressées de l’index columnstore. C’est ainsi que SQL Server 2016 peut traiter environ 900 lignes à la fois, au lieu de 1 ligne à la fois. Par conséquent, le temps système de l’opération est maintenant partagé par l’ensemble du batch, ce qui réduit le coût global par ligne. Cette quantité partagée d’opérations, combinée avec la compression des index columnstore, réduit la latence inhérente d’une opération SELECT en mode batch. Pour en savoir plus sur l’index columnstore et le mode batch, consultez [Description des index columnstore](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
	FROM T_target
	GROUP BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


L’examen des plans de requête côte à côte sur la figure 2 montre que le mode de traitement a changé avec le niveau de compatibilité. Par conséquent, l’exécution des requêtes dans les deux niveaux de compatibilité révèle que le temps de traitement est majoritairement en mode ligne (86 %) avec 14 % seulement en mode batch, où 2 lots ont été traités. Plus le jeu de données est volumineux, moins cet avantage est marqué.


*Figure 2 : passage de l’opération SELECT du mode série au mode parallèle dans le niveau de compatibilité 130.*


![Figure 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## Opération SORT en mode batch


Cas similaire au précédent mais appliqué à une opération de tri, la transition de mode ligne (niveau de compatibilité 120) au mode batch (niveau de compatibilité 130) améliore les performances de l’opération SORT pour les mêmes raisons.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	ORDER BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	ORDER BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


La figure 3 montre que l’opération SORT en mode ligne représente 81 % du coût, tandis que le mode batch ne représente que 19 % du coût (respectivement 81 et 56 % du tri).


*Figure 3 : passage de l’opération SORT du mode série au mode parallèle avec le niveau de compatibilité 130.*


![Figure 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Bien sûr, ces exemples ne contiennent que des dizaines de milliers de lignes, ce qui n’est rien par rapport aux données disponibles dans la plupart des serveurs SQL aujourd’hui. Mais, projetez ces résultats sur plusieurs millions de lignes, et le gain peut se chiffrer en quelques minutes d’exécution par jour, selon la nature de votre charge de travail.


## Améliorations de l’estimation de la cardinalité


Depuis SQL Server 2014, une base de données exécutée au moins au niveau de compatibilité 120 utilise la nouvelle fonctionnalité d’estimation de la cardinalité. En fait, l’estimation de la cardinalité désigne la logique utilisée pour déterminer comment SQL Server exécute une requête en fonction de son coût estimé. Cette estimation est calculée à l’aide de données statistiques associées aux objets impliqués dans cette requête. En pratique, globalement, les fonctions d’estimation de la cardinalité estiment le nombre de lignes et indiquent la distribution des valeurs, le nombre de valeurs distinctes et les nombres en double dans les tables et objets référencés dans la requête. Toute erreur dans ces estimations peut, entre autres, générer des E/S superflues en raison d’allocations de mémoire insuffisantes (comme des saturations de TempDB) ou faire opter pour le mode d’exécution en série plutôt qu’en parallèle. En conclusion, des estimations fausses peuvent avoir un impact négatif sur les performances d’exécution des requêtes. Mais, des estimations exactes, plus précises, améliorent l’exécution des requêtes !

Comme nous l’avons déjà dit, l’optimisation des requêtes et l’estimation sont un sujet complexe. Mais si vous souhaitez en savoir plus sur les plans de requête et l’estimation de la cardinalité, consultez [Optimiser vos plans de requête avec l’estimation de la cardinalité dans SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx).


## Quelle estimation de la cardinalité utilisez-vous actuellement ?


Pour déterminer l’estimation de la cardinalité sous laquelle vos requêtes sont exécutées, nous allons utiliser les exemples de requête ci-dessous. Notez que ce premier exemple s’exécutera sous le niveau de compatibilité 110, ce qui implique l’utilisation des anciennes fonctions d’estimation de la cardinalité.


```
-- Old CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Une fois l’exécution terminée, cliquez sur le lien XML et examinez les propriétés du premier itérateur indiqué ci-dessous. Notez que la propriété appelée CardinalityEstimationModelVersion a pour valeur 70. Cela ne signifie pas que le niveau de compatibilité de la base de données est défini pour SQL Server 7.0 (soit 110 comme le montrent les instructions TSQL ci-dessus). La valeur 70 représente simplement la fonctionnalité d’estimation de la cardinalité disponible depuis SQL Server 7.0, qui n’a fait l’objet d’aucune révision majeure jusqu’à SQL Server 2014 (fourni avec le niveau de compatibilité 120).


*Figure 4 : la propriété CardinalityEstimationModelVersion est égale à 70 si vous utilisez le niveau de compatibilité 110 ou un niveau antérieur.*


![Figure 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Vous pouvez également opter pour le niveau de compatibilité 130 et désactiver l’utilisation de la nouvelle fonction d’estimation de la cardinalité en réglant LEGACY\_CARDINALITY\_ESTIMATION sur ON avec [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx). La situation sera rigoureusement identique au niveau 110 du point de vue de la fonction d’estimation de la cardinalité, si vous utilisez le niveau de compatibilité le plus récent. Ainsi, vous pouvez tirer parti des nouvelles fonctionnalités de traitement des requêtes offertes par le dernier niveau de compatibilité (c’est-à-dire en mode batch), tout en utilisant l’ancienne fonctionnalité d’estimation de la cardinalité au besoin.


```
-- Old CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Le passage au niveau de compatibilité 120 ou 130 active la nouvelle fonctionnalité d’estimation de la cardinalité. Dans ce cas, la propriété CardinalityEstimationModelVersion par défaut prend la valeur 120 ou 130, comme indiqué ci-dessous.


```
-- New CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figure 5 : propriété CardinalityEstimationModelVersion égale à 130 en cas d’utilisation du niveau de compatibilité 130.*


![Figure 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## Constatation de différences dans l’estimation de la cardinalité


À présent, nous allons exécuter une requête légèrement plus complexe, comprenant une instruction INNER JOIN avec une clause WHERE et des prédicats, et voir le nombre de lignes estimées par l’ancienne fonction d’estimation de la cardinalité.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
	FROM
		           [dbo].[T_source] S
		INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
	WHERE
		S.[Color] = ‘Red’  AND
		S.[c2] > 2000  AND
		T.[c2] > 2000
	OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


L’exécution de cette requête renvoie 200 704 lignes, alors que la fonctionnalité d’estimation de la cardinalité en avait compté 194 284. Bien sûr, comme nous l’avons déjà vu, ces nombres de lignes dépendent également de la fréquence d’exécution des requêtes précédentes, qui augmente la taille des tables à chaque exécution. Évidemment, les prédicats de votre requête ont également une influence sur l’estimation, tout comme la forme de la table, les données contenues et leurs corrélations avec les autres types de données.


*Figure 6 : le nombre de lignes estimé est de 194 284, soit 6 000 de moins que les 200 704 attendues.*


![Figure 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Nous allons maintenant exécuter la même requête avec la nouvelle fonctionnalité d’estimation de la cardinalité.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
	FROM
		           [dbo].[T_source] S
		INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
	WHERE
		S.[Color] = ‘Red’  AND
		S.[c2] > 2000  AND
		T.[c2] > 2000
	OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Comme vous le voyez, le nombre de lignes estimé est de 202 877, une valeur plus proche de la précédente estimation et légèrement supérieure à celle-ci.

*Figure 7 : le nombre de lignes estimé est désormais de 202 877, au lieu de 194 284.*


![Figure 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


En réalité, le jeu de résultats contient 200 704 lignes. Mais tout dépend de la fréquence selon laquelle vous avez exécuté les requêtes des exemples précédents. Surtout, comme le TSQL utilise l’instruction RAND(), les valeurs réelles renvoyées peuvent varier d’une exécution à l’autre. Par conséquent, dans cet exemple, la nouvelle estimation de la cardinalité est plus précise dans le nombre de lignes calculé, car 202 877 est beaucoup plus proche de 200 704 que 194 284. Enfin, si vous modifiez des prédicats de la clause WHERE en incluant un signe d’égalité (au lieu de « > » par exemple), les estimations entre l’ancienne et nouvelle fonction de cardinalité peuvent diverger encore davantage, selon le nombre de correspondances obtenues.

En l’occurrence, une estimation inférieure de 6 000 lignes par rapport au nombre réel ne représente pas une grande quantité de données dans certaines situations. Maintenant, transposez ce cas de figure à des millions de lignes entre plusieurs tables, avec des requêtes plus complexes. L’estimation peut parfois être inférieure de plusieurs millions de lignes, entraînant le choix d’un mauvais plan d’exécution ou exigeant des allocations de mémoire insuffisantes à l’origine d’une saturation de TempDB et d’une augmentation importante des E/S.

Si vous en avez la possibilité, testez cette comparaison sur vos requêtes et vos jeux de données, et voyez dans quelle mesure les estimations (anciennes et nouvelles) sont affectées. Parfois, le nombre obtenu peut être très inférieur à la réalité ou beaucoup plus proche du nombre renvoyé dans le résultat. Tout cela dépend de la forme de vos requêtes, des caractéristiques de la base de données SQL Azure, de la nature et de la taille de vos jeux de données, ainsi que des statistiques disponibles sur ces derniers. Si vous venez de créer votre instance de Base de données SQL Azure, l’optimiseur de requête doit partir de zéro et ne peut pas réutiliser les statistiques des exécutions précédentes. Les estimations sont donc très contextuelles et souvent propres à chaque serveur et chaque application. C’est un aspect important à prendre en compte.


## Quelques éléments à prendre en compte


La plupart des charges de travail auraient intérêt à utiliser le niveau de compatibilité 130. Mais, avant d’adopter le niveau de compatibilité approprié à votre environnement de production, vous avez 3 possibilités :

1. Vous passez au niveau de compatibilité 130 et vous évaluez la situation. Si vous remarquez des régressions revenez au niveau de compatibilité d’origine ou conservez le niveau 130 et ne restaurez que l’ancien mode de la fonction d’estimation de la cardinalité (comme expliqué ci-avant, cette opération peut, à elle seule, résoudre le problème).
2. Vous testez complètement vos applications avec une charge de production similaire, puis vous affinez et validez les performances avant de passer en production. En cas de problèmes, procédez comme indiqué plus haut : vous pouvez revenir au niveau de compatibilité d’origine ou simplement rétablir l’ancien mode de la fonction d’estimation de la cardinalité.
3. La dernière possibilité et la plus récente pour résoudre ces problèmes, consiste à utiliser le magasin des requêtes. Et c’est l’option recommandée aujourd’hui ! Pour faciliter l’analyse de vos requêtes avec le niveau de compatibilité 120 ou antérieur et le niveau 130, nous vous encourageons vivement à utiliser le magasin des requêtes. Disponible dans la dernière version de Base de données SQL Azure V12, le magasin des requêtes est conçu pour vous aider à résoudre les problèmes de performance des requêtes. Considérez-le comme un enregistreur qui collecte et met en forme des informations historiques détaillées sur toutes les requêtes. Il simplifie considérablement l’analyse des performances en accélérant le diagnostic et la résolution des problèmes. Pour en savoir plus, consultez [Magasin des requêtes : un enregistreur de données pour votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


Globalement, si vous avez plusieurs bases de données au niveau de compatibilité 120 ou à un niveau antérieur, et que vous prévoyez d’en faire passer certaines au niveau 130, ou si votre charge de travail approvisionne automatiquement les nouvelles bases de données bientôt associées par défaut au niveau 130, prenez en compte les points suivants :

- Avant de passer au nouveau niveau de compatibilité en production, activez le magasin des requêtes. Pour en savoir plus, consultez [Migrer des plans de requête](https://msdn.microsoft.com/library/bb895281.aspx).
- Ensuite, testez toutes les charges de travail critiques à l’aide de requêtes et de données représentatives d’un environnement de production et comparez les performances constatées à celles relevées par le magasin des requêtes. Si vous rencontrez des régressions, vous pouvez identifier les requêtes concernées à l’aide du magasin des requêtes et utiliser l’option de forçage du plan (également appelée épinglage du plan). Dans ce cas, vous conservez le niveau de compatibilité 130 et utilisez le plan de requête précédent, comme ce que suggère le magasin des requêtes.
- Si vous souhaitez tirer parti des nouvelles fonctionnalités de Base de données SQL Azure (qui exécute SQL Server 2016), mais que vous êtes sensible aux modifications apportées par le niveau de compatibilité 130, vous pouvez en dernier recours envisager de forcer le retour au niveau de compatibilité qui convient à votre charge de travail à l’aide d’une instruction ALTER DATABASE. Mais sachez que l’option de forçage de plan du Magasin des requêtes est votre meilleure option, car ne pas utiliser le niveau 130 revient à rester au niveau fonctionnel d’une ancienne version de SQL Server.
- Si vous avez des applications mutualisées dans plusieurs bases de données, il peut être nécessaire de mettre à jour la logique d’approvisionnement de vos bases de données afin d’assurer un niveau de compatibilité cohérent à toutes les bases de données. Les performances de la charge de travail de votre application peuvent varier lorsque certaines bases de données fonctionnent à des niveaux de compatibilité différents. Par conséquent, l’utilisation d’un niveau de compatibilité cohérent sur toutes les bases de données peut être requise pour offrir la même expérience à l’ensemble de vos clients. Ceci n’est pas une obligation. Tout dépend de la façon dont votre application est affectée par le niveau de compatibilité.
- Enfin, concernant l’estimation de la cardinalité et comme pour la modification du niveau de compatibilité avant le passage en production, il est recommandé de tester votre charge de travail de production dans les nouvelles conditions pour déterminer si votre application tire parti des améliorations de l’estimation de la cardinalité.


## Conclusion


L’utilisation de Base de données SQL Azure pour tirer parti de toutes les améliorations de SQL Server 2016 améliore nettement l’exécution des requêtes. C’est vrai ! Bien sûr, comme pour toute nouvelle fonctionnalité, il convient d’effectuer une évaluation appropriée afin de déterminer les conditions optimales de la charge de travail de votre base de données. L’expérience montre que la plupart des charges de travail s’exécutent de manière transparente avec le niveau de compatibilité 130, tout en tirant parti des nouvelles fonctions de traitement des requêtes et de la nouvelle fonctionnalité d’estimation de la cardinalité. Ceci dit, en pratique, il existe toujours des exceptions. Une évaluation est donc indispensable pour déterminer dans quelle mesure vous pouvez bénéficier de ces améliorations. Là encore, le magasin des requêtes peut être très utile !

Compte tenu de l’évolution de SQL Azure, vous pouvez espérer un niveau de compatibilité 140 très bientôt. Le temps venu, nous discuterons des apports de ce niveau de compatibilité 140, comme nous venons d’expliquer brièvement les avantages du niveau 130 aujourd’hui.

Pour l’instant, n’oublions pas qu’à partir de juin 2016, Base de données SQL Azure passera au niveau de compatibilité 130 par défaut pour les bases de données créées. Prenez date !


## Références


- [Nouveautés (moteur de base de données)](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog : Le magasin des requêtes : un enregistreur de données pour votre base de données par Borko Novakovic, 8 juin 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Niveau de compatibilité ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)

- [Niveau de compatibilité 130 pour Base de données SQL Azure V12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- Document [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator (Optimiser vos plans de requêtes avec l’Estimateur de la cardinalité de SQL Server 2014)](https://msdn.microsoft.com/library/dn673537.aspx)

- [Description des index columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog : Amélioration des performances des requêtes avec le niveau de compatibilité 130 dans Base de données SQL Azure, par Alain Lissoir, 6 mai 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->

<!---HONumber=AcomDC_0810_2016-->