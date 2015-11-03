<properties
	pageTitle="Utiliser In-Memory OLTP pour améliorer les performances transactionnelles de SQL Azure | Microsoft Azure"
	description="Adopter In-Memory OLTP pour améliorer les performances transactionnelles dans une base de données SQL existante."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Utiliser In-Memory (version préliminaire) pour améliorer les performances de votre application SQL Azure

Procédez comme suit pour optimiser les performances transactionnelles de votre base de données SQL Azure Premium existante à l’aide de [In-Memory](sql-database-in-memory.md).

Pour comparer, choisissez une charge de travail similaire à votre charge de travail de production avec un nombre de connexions simultanées et un ratio lecture/écriture similaires. Pour réduire la latence du réseau, nous vous recommandons d’exécuter votre charge de travail de test dans la même région Azure que la base de données.

## Étape 1 : copier vos données dans une nouvelle base de données Premium
1.	Exportez votre base de données de production vers un fichier bacpac à l’aide de l’un des outils suivants :

	A. la fonctionnalité Exporter du [portail](https://portal.azure.com/) ;

	B. la fonctionnalité Exporter une application de la couche Données de SQL Server Management Studio.

2.	Importez le fichier bacpac dans une nouvelle base de données Premium sur un serveur V12 :

	A. Dans le portail, accédez au serveur, puis sélectionnez l’option Importer la base de données. Veillez à sélectionner un niveau tarifaire Premium.

	B. Dans SQL Server Management Studio (SSMS) : connectez-vous au serveur, cliquez avec le bouton droit sur le nœud Bases de données, puis sélectionnez Importer une application de la couche Données.


## Étape 2 : identifier les objets à migrer vers In-Memory OLTP
SQL Server Management Studio (SSMS) inclut un rapport d’analyse des performances transactionnelles que vous pouvez générer sur une base de données ayant une charge de travail active, afin d’identifier les tables et procédures stockées candidates à la migration vers In-Memory OLTP. Pour plus de détails, voir [Déterminer si un tableau ou une procédure stockée doit être déplacée vers l’OLTP en mémoire](https://msdn.microsoft.com/library/dn205133.aspx).

1.	Connectez-vous à la base de données de production à l’aide de SSMS. Si vous avez une charge de travail en cours d’exécution par rapport à la nouvelle base de données de test, vous pouvez également vous connecter à celle-ci.
2.	Cliquez avec le bouton droit sur la base de données, puis sélectionnez Reports -> Standard Reports -> Transaction Performance Analysis Report. Le rapport permet d’identifier les tables et procédures stockées pouvant bénéficier d’In-Memory OLTP, en fonction de l’utilisation.


## Étape 3 : migrer les tables
1.	Connectez-vous à la nouvelle base de données de test à l’aide de SSMS. Pour éviter d’avoir à utiliser l’option WITH (SNAPSHOT) dans les requêtes, nous vous recommandons de définir l’option de base de données MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT.
2.	Une fois connecté à la nouvelle base de données de test, exécutez la commande suivante :

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	Migrez la table sur disque vers la table à mémoire optimisée en suivant l’une des approches suivantes :

	A. Assistant d’optimisation de la mémoire de SSMS : une fois connecté à la base de données de test, cliquez avec le bouton droit sur la table, puis sélectionnez Memory Optimization Advisor. Ce conseiller vous permet de déterminer si la table inclut des fonctionnalités non prises en charge par la table à mémoire optimisée. Si ce n’est pas le cas, le conseiller peut effectuer la migration de schéma et de données réelle. Pour plus de détails, voir [Conseiller d’optimisation de la mémoire](https://msdn.microsoft.com/library/dn284308.aspx).

	B. Migration manuelle : connectez-vous à la nouvelle base de données de test à l’aide de SSMS.

Procédez comme suit pour migrer une table :

1.	Générez un script de la table en cliquant sur la table, puis en sélectionnant Script Table As -> CREATE To -> New Query Window.
2.	Modifiez l’index CLUSTERED en NONCLUSTERED et ajoutez l’option avec WITH (MEMORY\_OPTIMIZED=ON).
3.	Si la table utilise des fonctionnalités non prises en charge, implémentez des solutions de contournement. MSDN décrit comment gérer les [fonctionnalités communes non prises en charge](https://msdn.microsoft.com/library/dn247639.aspx).
4.	Renommez la table existante à l’aide de sp\_rename.
5.	Créez la nouvelle table à mémoire optimisée en exécutant le script CREATE TABLE.
6.	Copiez les données en exécutant l’instruction suivante : ``INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>

## Étape 4 (facultative) : migrer les procédures stockées

Connectez-vous à la nouvelle base de données de test à l’aide [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx), version préliminaire de septembre 2015 ou version ultérieure.

Identifiez toutes les fonctionnalités non prises en charge dans les procédures stockées compilées en mode natif en exécutant le [Conseiller d’optimisation de la mémoire](https://msdn.microsoft.com/library/dn284308.aspx) sur l’ancienne procédure. Les solutions de contournement pour les fonctionnalités communes non prises en charge sont documentées dans [MSDN](https://msdn.microsoft.com/library/dn296678.aspx).

Lors de la migration d’une procédure stockée vers le mode natif, vous devez tenir compte des deux aspects suivants :

- Les modules natifs requièrent les options suivantes :

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- Les modules natifs utilisent des [blocs Atomic](https://msdn.microsoft.com/library/dn452281.aspx) pour la gestion des transactions. Les instructions BEGIN TRAN/COMMIT/ROLLBACK explicites ne sont pas requises.

Une procédure stockée compilée en mode natif typique ressemble à ceci :


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



Notez que, si SNAPSHOT est le niveau d’isolation le plus couramment utilisé avec une table à mémoire optimisée, les niveaux d’isolation REPEATABLE READ et SERIALIZABLE sont également pris en charge.

##### Procédez comme suit pour migrer une procédure stockée :

1.	Générez un script de l’ancienne procédure stockée en cliquant avec le bouton droit sur celle-ci, puis en sélectionnant Script Procedure As -> CREATE To -> New Query Window.
2.	Réécrivez l’en-tête de procédure en utilisant le modèle ci-dessus, puis supprimez les instructions BEGIN TRAN/ROLLBACK/COMMIT.
3.	Si la procédure stockée utilise des fonctionnalités non prises en charge, implémentez des solutions de contournement. MSDN décrit comment gérer les [fonctionnalités communes non prises en charge](https://msdn.microsoft.com/library/dn296678.aspx).
4.	SUPPRIMEZ la procédure ou renommez l’ancienne procédure à l’aide de sp\_rename.
5.	Créez la procédure stockée compilée en mode natif en exécutant le script CREATE PROCEDURE.

## Étape 5 : exécuter votre charge de travail
Exécutez votre charge de travail de test sur les tables à mémoire optimisée et les procédures stockées compilées en mode natif, en mesurant le gain de performances.

## Étapes suivantes

[Surveiller le stockage en mémoire](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/).

[Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->