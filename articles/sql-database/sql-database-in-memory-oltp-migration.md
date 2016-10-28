<properties
	pageTitle="OLTP In-Memory améliore les performances SQL txn | Microsoft Azure"
	description="Adopter In-Memory OLTP pour améliorer les performances transactionnelles dans une base de données SQL existante."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor="MightyPen"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="jodebrui"/>


# Utiliser OLTP en mémoire (version préliminaire) pour améliorer les performances de votre base de données SQL

[OLTP en mémoire](sql-database-in-memory.md) permet d’améliorer les performances des charges de travail OLTP dans les bases de données SQL Azure [Premium](sql-database-service-tiers.md) sans augmenter le niveau de performances.

Suivez ces étapes pour adopter OLTP en mémoire dans votre base de données existante.

## Étape 1 : vérifiez que votre base de données Premium prend en charge OLTP en mémoire

Les bases de données créées en novembre 2015 ou version ultérieure prennent en charge la fonctionnalité In-Memory. Vous pouvez vous assurer si votre base de données prend la fonction In-Memory en exécutant l’instruction Transact-SQL. In-Memory est pris en charge si le résultat retourné est 1 (et nom 0) :

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

L’acronyme *XTP* remplace *Extreme Transaction Processing (Traitement de transactions extrêmes)*

Si la base de données existante doit être déplacée vers une nouvelle base de données V12 Premium, vous pouvez utiliser les techniques suivantes effectuer l’exportation, puis importer vos données.

#### Opérations d’exportation

Exportez votre base de données de production vers un fichier bacpac à l’aide de l’un des outils suivants :

- La fonctionnalité [Exporter](sql-database-export.md) dans le [portail](https://portal.azure.com/).

- La fonctionnalité **Exporter une application de la couche Données** dans un [fichier SSMS.exe à jour](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. Dans l’**Explorateur d’objets**, développez le nœud **Bases de données**.
 2. Cliquez avec le bouton droit sur le nœud de votre base de données.
 3. Cliquez sur **Tâches** > **Exporter l’application de la couche Données**.
 4. Utilisez la fenêtre de l’Assistant s’affiche.


#### Étapes d’importation

Importez le fichier bacpac dans une nouvelle base de données Premium.

1. Dans le [portail](https://portal.azure.com/) Azure,
 - accédez au serveur.
 - Sélectionnez l’option [Base de données d’importation](sql-database-import.md).
 - Sélectionnez un niveau tarifaire Premium.

2. Utilisez SSMS pour importer le fichier bacpac :
 - Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur le nœud **Bases de données**.
 - Cliquez sur **Importer l’application de la couche Données**.
 - Utilisez la fenêtre de l’Assistant s’affiche.


## Étape 2 : identifier les objets à migrer vers In-Memory OLTP

SSMS inclut un rapport de **présentation d’analyse des performances des transactions** que vous pouvez exécuter sur une base de données avec une charge de travail active. Le rapport identifie les tables et procédures stockées candidates pour la migration vers In-Memory OLTP.

Dans SSMS, pour générer le rapport :
- Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur le nœud de votre base de données.
- Cliquez sur **Rapports** > **Rapports Standard** > **Présentation de l’analyse des performances des transactions**.

Pour plus d’informations, voir [Déterminer si une table ou une procédure stockée doit être déplacée vers In-Memory OLTP](http://msdn.microsoft.com/library/dn205133.aspx).


## Étape 3 : Créer une base de données de test comparable

Supposons que le rapport indique que votre base de données est équipée d’une table qui gagnerait à être convertie en table à mémoire optimisée. Nous vous recommandons de commencer par effectuer un test pour confirmer l’indication.

Vous avez besoin d’une copie de test de votre base de données de production. La base de données de test doit être au même niveau de niveau de service que votre base de données de production.

Pour faciliter le test, modifiez votre base de données comme suit :

1. Connectez-vous à la base de données de test à l’aide de SSMS.

2. Pour éviter d’avoir recours à l’option WITH (SNAPSHOT) dans les requêtes, définissez l’option de base de données, comme indiqué dans l’instruction T-SQL suivante :
```
ALTER DATABASE CURRENT
	SET
		MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## Étape 4 : Migrer les tables

Vous devez créer et remplir une copie de mémoire optimisée de la table que vous souhaitez tester. Vous pouvez le créer en utilisant soit :

- L’Assistant Optimisation de mémoire pratique en SSMS.
- T-SQL manuel.


#### Assistant Optimisation de mémoire en SSMS

Pour utiliser cette option de migration :

1. Se connecter à la base de données de test avec SSMS.

2. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur la table, puis cliquez sur **Conseiller d’optimisation de mémoire**.
 - L’assistant **Conseiller d’optimiseur de mémoire de table** s’affiche.

3. Dans l’assistant, cliquez sur **Validation de migration** (ou sur le bouton **Suivant**) pour voir si la table possède des fonctionnalités non prises en charge dans les tables à mémoire optimisée. Pour plus d'informations, consultez les pages suivantes :
 - La *liste de vérification d’optimisation de mémoire* dans [Memory Optimization Advisor](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Constructions Transact-SQL non prises en charge par In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migration vers In-Memory OLTP](http://msdn.microsoft.com/library/dn247639.aspx).

4. Si la table ne possède pas de fonctions non prises en charge, l’Assistant peut exécuter le schéma et la migration des données pour vous.


#### T-SQL manuel

Pour utiliser cette option de migration :

1. Connectez-vous à votre base de données de test à l’aide de SSMS (ou utilitaire similaire).

2. Obtenir le script T-SQL complet pour votre table et ses index.
 - Dans SSMS, cliquez avec le bouton droit sur le nœud de votre table.
 - Cliquez sur **Générer un script de la table en tant que** > **Créer à** > **Nouvelle fenêtre de requête**.

3. Dans la fenêtre de script, ajoutez WITH (MEMORY\_OPTIMIZED = ON) à l’instruction CREATE TABLE.

4. S’il existe un index CLUSTERED (ORDONNÉ) en clusters, le transformer en NONCLUSTERED (NON ORDONNÉ).

5. Renommez la table existante à l’aide de SP\_RENAME.

6. Créez la nouvelle copie de la table de mémoire optimisée si la table en exécutant le script CREATE TABLE modifié.

7. Copiez les données dans votre table mémoire optimisée à l’aide d’INSERT... SELECT * INTO :
	
```
INSERT INTO <new_memory_optimized_table>
		SELECT * FROM <old_disk_based_table>;
```


## Étape 5 (facultative) : migrer les procédures stockées

La fonctionnalité In-Memory peut également modifier une procédure stockée pour améliorer les performances.


### Considérations relatives à des procédures stockées compilées en mode natif

Une procédure stockée compilée en mode natif doit avoir les options suivantes dans sa clause WITH T-SQL :

- NATIVE\_COMPILATION

- SCHEMABINDING : les tables significatives figurant dans la procédure stockée ne peuvent voir leurs définitions modifiées de quelque manière que ce soit susceptible d’affecter la procédure stockée, à moins que vous ne glissiez la procédure stockée.


Un module natif doit utiliser un grand [bloc ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) pour la gestion de transaction. Il n’existe aucun rôle pour une instruction BEGIN TRANSACTION ou ROLLBACK TRANSACTION explicite. Si votre code détecte une violation de règle métier, il peut terminer le bloc atomique avec une instruction [THROW](http://msdn.microsoft.com/library/ee677615.aspx).


### En général, CREATE PROCEDURE compilée en mode natif

En général, le code T-SQL pour créer une procédure stockée compilée en mode natif est similaire au modèle suivant :

```
CREATE PROCEDURE schemaname.procedurename
	@param1 type1, …
	WITH NATIVE_COMPILATION, SCHEMABINDING
	AS
		BEGIN ATOMIC WITH
			(TRANSACTION ISOLATION LEVEL = SNAPSHOT,
			LANGUAGE = N'your_language__see_sys.languages'
			)
		…
		END;
```

- Pour le niveau TRANSACTION\_ISOLATION\_LEVEL, SNAPSHOT est la valeur la plus commune pour la procédure stockée compilée de façon native. Cependant, un sous-ensemble d’autres valeurs est également pris en charge :
 - REPEATABLE READ
 - SERIALIZABLE


- La valeur LANGUAGE doit être présente dans la vue sys.languages.


### Comment migrer une procédure stockée

Les étapes de la migration sont les suivantes :


1. Obtenir le script CREATE PROCEDURE pour la procédure stockée régulière interprétée.

2. Réécrire son en-tête pour qu’il corresponde au modèle précédent.

3. Déterminer si le code TSQL de procédure stocké utilise les fonctions non prises en charge pour les procédures stockées compilées en mode natif. Mettre en œuvre des solutions de contournement si nécessaire.
 - Pour plus d’informations, voir [Problèmes de migration pour les procédures stockées compilées natives](http://msdn.microsoft.com/library/dn296678.aspx).

4. Renommez l’ancienne procédure stockée en utilisant SP\_RENAME. Ou FAITES-LE SIMPLEMENT GLISSER (DROP).

5. Exécutez le script CREATE PROCEDURE T-SQL modifié.


## Étape 6 : exécuter votre charge de travail dans un test

L’exécution d’une charge de travail dans votre base de données de test est similaire à la charge de travail qui s’exécute dans votre base de données de production. Cela doit révéler les gains de performances générés par votre utilisation de la fonction In-Memory pour les tables et les procédures stockées.

Les principaux attributs de la charge de travail sont :

- Nombre de connexions simultanées.

- Rapport Lecture/Écriture


Pour adapter et exécuter la charge de travail de test, envisagez d’utiliser l’outil pratique ostress.exe, figurant [ici](sql-database-in-memory.md).


Pour réduire la latence du réseau, exécutez le test dans la région géographique Azure dans laquelle la base de données existe.


## Étape 7 : surveillance post exécution

Veillez à surveiller les effets des performances de vos implémentations In-Memory en production :

- [Surveiller le stockage en mémoire](sql-database-in-memory-oltp-monitoring.md).

- [Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)


## Liens connexes

- [In-Memory OLTP (optimisation en mémoire)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Présentation des procédures stockées compilées en mode natif](http://msdn.microsoft.com/library/dn133184.aspx)

- [Conseil d’optimisation par mémoire](http://msdn.microsoft.com/library/dn284308.aspx)

<!---HONumber=AcomDC_0720_2016-->