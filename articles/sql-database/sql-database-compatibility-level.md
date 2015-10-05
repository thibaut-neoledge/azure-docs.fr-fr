<properties 
	pageTitle="Niveaux de compatibilité dans SQL Database | Microsoft Azure" 
	description="Explique comment définir le niveau de compatibilité de votre base de données Azure SQL Database et les fonctionnalités qui sont affectées."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# Niveaux de compatibilité dans SQL Database


Pour Azure SQL Database, cette rubrique décrit les choix de fonctionnalités que vous pouvez effectuer à l'aide de l'instruction **ALTER DATABASE** Transact-SQL. Le concept de niveau de compatibilité est conçu pour réduire les risques de mise à niveau.


La plupart des fonctionnalités qui sont activées ou désactivées par le niveau de compatibilité font partie de l'optimiseur de requête. Avec Microsoft, l'activation d'une nouvelle fonctionnalité d'optimiseur de requête dépend du niveau de compatibilité lorsque :


- Le contexte mis à niveau modifie la sémantique d'une fonction précédente.
- L'optimisation de la requête est fortement susceptible de nuire aux performances de certaines requêtes SQL, rares mais légitimes.


Si votre requête est moins performante après la mise à niveau d'une version de votre base de données ou serveur Azure SQL Database, vous pouvez réduire le niveau de compatibilité. Le paramètre inférieur peut désactiver un ensemble réduit d'améliorations de l'optimiseur, y compris sans doute celle qui est défavorable à votre requête.


## Fonctionnement du niveau de compatibilité


Depuis des années, Microsoft SQL Server dispose de niveaux de compatibilité définissables. Désormais, à compter de la version V12, Azure SQL Database adopte également les niveaux de compatibilité. L'activation de certaines nouvelles fonctionnalités est contrôlée par le paramètre de niveau de compatibilité dans chaque base de données SQL Database. Les paramètres possibles sont les suivants :


- 110 : le paramètre le plus bas possible et, par conséquent, celui qui exclut le plus de nouvelles fonctionnalités.
- 120 : correspond vaguement à SQL Database V11 (ce qui signifie que `SELECT @@version;` retourne **11.**0.0.0).
 - La version V11 était également appelée « SAWA V2 » pour SQL Database.
 - 120 est la valeur la plus élevée dans Microsoft SQL Server 2014.
- 130 : correspond vaguement à V12 (ce qui signifie que `SELECT @@version;` retourne **12.**0.0.0).
 - 130 est la valeur la plus élevée dans Microsoft SQL Server 2016.


Par exemple, le paramètre 120 signifie que votre base de données a accès au sous-ensemble de fonctionnalités qui est activé au niveau 120, *plus* les fonctionnalités qui deviennent actives avec n'importe quel paramètre inférieur, comme 110. Avec la valeur 120, votre base de données n'a pas accès aux fonctionnalités de niveau 130.


## Lecture ou définition du niveau de compatibilité


### Lecture du niveau de compatibilité


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### Définition du niveau de compatibilité


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


Pour plus d'informations, consultez les pages suivantes :


- [Niveau de compatibilité ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## Tables en mémoire et index Columnstore


La majorité des fonctionnalités de niveau de compatibilité qui deviennent actives aux niveaux 120 et 130 est liée aux tables et index *en mémoire*. Par conséquent, les éléments importants sont les suivants :


- Tables à mémoire optimisée
- Index Columnstore


Avec des éléments en mémoire, le niveau de compatibilité 130 offre les avantages suivants :


- La capacité de l'optimiseur de requête à traiter plus de requêtes en mode par *lots* est activée.
 - Le mode de traitement par lots est plus rapide que le mode *ligne* lorsque le nombre de lignes est important.
- L'ajout de l'opérateur **SORT**.
- L'ajout d'agrégats Windows.


Pour plus d'informations sur les tables en mémoire et les index Columnstore, consultez :


- [Tables à mémoire optimisée](http://msdn.microsoft.com/library/dn133165.aspx)
- [CRÉATION D'UN INDEX COLUMNSTORE CLUSTERISÉ (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [Index Columnstore décrits](http://msdn.microsoft.com/library/gg492088.aspx)


## Fonctionnalités générales nécessitant un niveau minimal de 130


L'estimateur de cardinalité (CE) génère et stocke des statistiques sur le nombre approximatif de lignes dans une table. Plusieurs parties de l'optimiseur de requête utilisent les informations de cardinalité.


Les algorithmes utilisés par le CE ont été améliorés. Les améliorations entraînent un changement et le changement peut provoquer la régression dans de rares cas.


| 130 est le niveau<br/>nécessaire<br/>minimal | Zone de requête,<br/>Général | Détails de l'amélioration du<br/>plan de requête<br/> |
| :-- | :-- | :-- |
| 130 | Estimateur de cardinalité (CE) | Perfectionnements de l'estimateur de cardinalité (CE), par rapport au CE précédent de niveau 120.<br/><br/>Dans le plan de requête, vous pouvez voir : **CardinalityEstimationModelVersion="130"**<br/><br/>**Indicateur de suivi** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) peut être activé pour utiliser le CE du niveau 120 lorsque votre base de données est au niveau 130.<br/><br/>**Indicateur de suivi** [**4199**](http://support.microsoft.com/kb/974006), lorsque votre base de données est au niveau de compatibilité 130, peut être défini sur "off" pour désactiver les correctifs de l'optimiseur de requête. L'indicateur s'applique uniquement aux correctifs qui sont mis en œuvre une fois que le niveau 130 a entièrement quitté la version préliminaire et qu'il est mis à la disponibilité générale. Pour plus d'informations, consultez :<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | Plans de requête parallèles pour les tables en mémoire | Les requêtes peuvent utiliser plusieurs threads et s'exécuter en parallèle, lorsqu'elles concernent une table en mémoire, c'est-à-dire une table qui a été créée avec la clause **MEMORY\_OPTIMIZED = YES**. Le parallélisme peut accélérer l'exécution des requêtes.<br/><br/>Cette amélioration est prise en charge pour les procédures Transact-SQL normales et les procédures stockées par l'utilisateur. Mais elle n'est pas prise en charge pour les procédures stockées natives compilées dans une DLL. |


## Fonctionnalités d'index Columnstore nécessitant un niveau minimal de 130


Au niveau de compatibilité 130, les améliorations de l'optimiseur de requête peuvent résulter en un nouveau plan de requête. Pour les plans modifiés impliquant un index Columnstore, la modification implique en général l'un des éléments suivants :


- Une réduction des circonstances où les données doivent être copiées pour une opération secondaire ultérieure.
- Une modification du traitement par *ligne* en traitement par ***lots*** pour les opérations de tri.


Dans la plupart des cas, la modification du plan améliore les performances de la requête en impliquant :


- Des insertions parallèles dans un index Columnstore.
 - Le niveau 130 ne fournit pas d'analyse parallèle des index non clusterisés.
- Une utilisation accrue de la base de données **tempdb**.


| 130 est le niveau<br/>nécessaire<br/>minimal | Zone de requête,<br/>index Columnstore | Détails de l'amélioration du<br/>plan de requête<br/> |
| :-- | :-- | :-- |
| 130 | Requêtes de fonction | Les performances sont améliorées par le passage au mode de traitement par lots, dans les cas suivants :<br/><br/>• Le tri est impliqué.<br/><br/>• Agrégation avec *plusieurs* fonctions distinctes<br/>(une fonction de chacune de deux puces dans la liste suivante) :<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *ou* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *ou* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *ou* **STDEVP**<br/><br/>• Fenêtre fonctions d’agrégation<br/>(décrites [ici sur MSDN](http://msdn.microsoft.com/library/ms189461.aspx), et [ici par Kathi Kellenberger](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• Fenêtre agrégats [définis par l’utilisateur](http://msdn.microsoft.com/library/ms131057.aspx) :<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• Fenêtre fonctions agrégat d’analyse :<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | Plan de requête en série monothread | Une requête exécutée sur un seul thread peut s'exécuter en mode par lots. Cela peut accélérer les performances de la requête.<br/><br/>Un plan de requête peut être conçu en tant que thread unique ou une requête peut s'exécuter sous **MAXDOP 1**. |
| 130 | Insertion parallèle | Votre plan de requête peut effectuer des insertions en parallèle.<br/<br/>L'[exemple](#ExampleQueryParallelCciByCompatLevel) plus loin dans cette rubrique illustre ce parallélisme. |
| 130 | Anti-semi join | Cet opérateur peut désormais s'exécuter en mode par lots. |


## Fonctionnalités générales nécessitant un niveau minimal de 120


| 120 est le niveau<br/>nécessaire<br/>minimal | Zone de requête,<br/>Général | Détails de l'amélioration du<br/>plan de requête<br/> |
| :-- | :-- | :-- |
| 120 | [Modification des tables MEMORY\_OPTIMIZED](http://msdn.microsoft.com/library/dn269114.aspx) | Vous permet d'exécuter des opérations **ALTER TABLE** Transact-SQL sur les tables avec **MEMORY\_OPTIMIZED = YES**.<br/><br/>L'application de base de données peut continuer à s'exécuter, mais les opérations qui accèdent à la table sont bloquées jusqu'à ce que l'opération **ALTER TABLE** soit terminée. |
| 120 | [Création et gestion du stockage des objets à mémoire optimisée](http://msdn.microsoft.com/library/dn133174.aspx) | Le moteur OLTP en mémoire est intégré dans SQL Server. Cela vous permet d'avoir à la fois des tables **MEMORY\_OPTIMIZED** et des tables traditionnelles sur disque dans la même base de données. |
| 120 | [Prise en charge de Transact-SQL pour OLTP en mémoire](http://msdn.microsoft.com/library/dn133180.aspx) | Une poignée de commandes Transact-SQL a été améliorée pour prendre en charge le traitement de transactions en ligne en mémoire (OLTP).<br/><br/>Par exemple, le nouveau mot clé **NATIVE\_COMPILATION** de la commande [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx). |
| 120 | Estimateur de cardinalité (CE) | Perfectionnements de l'estimateur de cardinalité (CE), par rapport au CE antérieur de niveau 110.<br/><br/>Dans le plan de requête, vous pouvez voir : **CardinalityEstimationModelVersion="120"**<br/><br/>Pour plus d'informations sur la façon dont l'**indicateur de suivi 4199** interagit avec la valeur de niveau de compatibilité, consultez [KB 974006](http://support.microsoft.com/kb/974006).|


## Fonctionnalités d'index Columnstore nécessitant un niveau minimal de 120


Cette section décrit les [fonctionnalités d'indexation columnstore](http://msdn.microsoft.com/library/dn934994.aspx) qui sont activées au niveau de compatibilité 120 ou supérieur.


| 120 est le niveau de<br/>compatibilité<br/>minimal | Index ColumnStore<br/>Nom de la fonctionnalité | Index ColumnStore<br/>Description de la fonctionnalité |
| :-- | :-- | :-- |
| 120 | Niveau d'isolement de capture instantanée (SI) et<br/><br/>niveau d'isolement de capture instantanée à lecture validée (RCSI). | Lorsque le plan de requête implique un index Columnstore, les niveaux SI et RCSI évitent que les données de transactions partiellement terminées soient incluses dans les résultats de la requête, sans nécessiter de verrous excessifs. |
| 120 | Défragmentation d'index | Les lignes supprimées sont effacées sans reconstruction d'index explicite.<br/><br/>**ALTER INDEX... REORGANIZE** efface les lignes supprimées de l'index Columnstore tandis que la table et l'index restent opérationnels en ligne. |
| 120 | Accessible sur un [réplica secondaire lisible](http://msdn.microsoft.com/library/ff878253.aspx) AlwaysOn | Vous pouvez améliorer les performances d'analyse opérationnelle en transférant les requêtes d'analyse vers un réplica secondaire AlwaysOn. |
| 120 | Poussée d'agrégat vers le bas,<br/>pendant la phase d'analyse de la table des fonctions d'agrégation | Améliore les performances en effectuant des calculs intermédiaires plus tôt dans le plan de requête, de sorte que moins de données doivent être copiées lors des phases ultérieures.<br/><br/>S'applique à **MIN**, **MAX**, **SUM**, **COUNT**, **AVG**.<br/><br/>S'applique uniquement lorsque le type de données est de huit octets ou moins, sauf pour les chaînes. |
| 120 | Poussée vers le bas de clauses **WHERE** basées sur chaîne | L'optimisation du prédicat de poussée vers le bas peut accélérer les requêtes qui comparent les données de chaîne de type &#x5b;var&#x5d;char ou n&#x5b;var&#x5d;char. Cette optimisation :<br/><br/>• s'applique aux opérateurs de comparaison courants, notamment **LIKE** qui utilisent des filtres bitmap.<br/><br/>• fonctionne uniquement lorsqu'il existe un prédicat de chaîne.<br/><br/>• fonctionne avec tous les classements que le produit prend en charge.<br/><br/>Pour plus d'informations sur les filtres bitmap, consultez ce billet de blog : [Présentation des filtres bitmap d'exécution de requête](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx). |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## Exemple de modification du plan de requête par niveau de compatibilité


Pour une instruction **INSERT...SELECT** Transact-SQL, cette section affiche la modification de son plan de requête entre les niveaux de compatibilité 120 et 130.


#### Schéma de table source


La table suivante contient au moins 300 000 lignes. Le plan de requête avancé peut ne pas se préoccuper du parallélisme s'il existe trop peu de données pour le justifier.


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### Script pour générer le plan de requête


Appliquez ces étapes au script Transact-SQL qui suit :


1. Dans **Ssms.exe** connectez-vous à votre base de données.
2. Collez le script Transact-SQL dans une fenêtre de requête **Ssms.exe**.
3. Modifiez le script pour vous assurer que **120** est la valeur de l'instruction **ALTER DATABASE**.
4. Exécutez uniquement la partie du script qui est *avant* l'instruction **INSERT INTO**.
5. Activez l'option de menu : **Requête** > **Inclure le plan d'exécution réel (Ctrl+M)**.
6. Exécutez uniquement l'instruction **INSERT INTO**.<br/><br/>
7. Désactivez l'option de menu : **Requête** > **Inclure le plan d'exécution réel (Ctrl+M)**.
8. Enfin, exécutez uniquement la partie du script qui est *après* l'instruction **INSERT INTO**.
9. Notez le plan de requête pour **120** dans l'onglet **Plan d'exécution**, qui se trouve près de l'onglet **Résultats**.<br/>-- -- -- -- -- --
10. Modifiez le script pour définir **130** comme valeur de l'instruction **ALTER DATABASE**.
11. Réexécutez le script comme décrit dans les étapes précédentes.
12. Notez que le plan de requête pour **130** est différent et inclut le parallélisme.


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### Affichage de deux plans



<br/>**120 :** Voici le plan de requête lorsque le niveau de compatibilité est **120**.


![Plan au niveau 120][1-Plan-at-level-120]


<br/>**130 :** Voici le plan de requête lorsque le niveau de compatibilité est **130**.


Le plan **130** inclut le *parallélisme* dont ne dispose pas le plan **120**.


L'affichage de ce plan est plutôt large dans **Ssms.exe**. Pour un meilleur affichage ici, la capture d'écran est divisée en deux parties. La deuxième partie suit la première partie.


![Plan au niveau 130][2-Plan-at-level-130]


## Niveau de compatibilité par défaut dans votre base de données


Lorsque vous mettez à niveau votre serveur Azure SQL Database, de la version V11 à V12 par exemple, le niveau de compatibilité dans chaque base de données reste inchangé. Après la mise à niveau, vous pouvez augmenter le niveau de compatibilité dans chaque base de données à l'aide de l'instruction **ALTER DATABASE**.


Toute nouvelle base de données créée aura le niveau de compatibilité maximal autorisé par la version SQL Database.



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Sept15_HO4-->