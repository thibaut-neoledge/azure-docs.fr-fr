<properties 
	pageTitle="Déplacer des données à l’aide de l’activité de copie | azure.microsoft.com/ Azure" 
	description="Apprenez-en plus sur le déplacement des données dans les pipelines Data Factory : migration de données entre des magasins de cloud, entre des emplacements locaux et le cloud. Utilisez l’activité de copie." 
	keywords="copier des données, déplacement des données, migration des données, transférer des données"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/08/2016" 
	ms.author="spelluru"/>

# Déplacer des données à l’aide de l’activité de copie

## Vue d'ensemble
Azure Data Factory vous permet d’utiliser l’activité de copie pour copier vers Azure des données de différentes formes provenant de plusieurs sources de données locales et cloud afin de pouvoir les transformer davantage et les analyser. L’activité de copie permet également de publier les résultats de transformation et d’analyse pour l’aide à la décision (BI) et l’utilisation d’application.

![Rôle d’activité de copie](media/data-factory-data-movement-activities/copy-activity.png)

L’activité de copie est effectuée par un [service globalement disponible](#global) sécurisé, fiable et évolutif. Cet article fournit des détails sur le déplacement des données dans Data Factory et dans l’activité de copie. Voyons d’abord comment la migration de données se produit entre deux banques de données cloud, et entre une banque de données locale et une banque de données cloud.

> [AZURE.NOTE] Pour en savoir plus sur les activités en général, consultez l’article [Présentation des pipelines et des activités](data-factory-create-pipelines.md).

### Copie de données entre deux magasins de données cloud
Lorsque les magasins de données source et récepteur (destination) se trouvent dans le cloud, l'activité de copie passe par les étapes suivantes pour copier/déplacer données de la source vers le récepteur. Le service qui alimente l’activité de copie effectue les opérations suivantes :

1. Lit les données du magasin de données source
2. Effectue la sérialisation/désérialisation, la compression/décompression, le mappage des colonnes et la conversion de type en fonction de la configuration du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie
3.	Écrit les données dans le magasin de données de destination

Le service choisit automatiquement la région optimale pour effectuer le déplacement des données, généralement la région la plus proche du magasin de données du récepteur.

![copie cloud-cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### Copie de données entre un magasin de données local et un magasin de données cloud
Pour déplacer en toute sécurité des données entre des banques de données locales derrière votre pare-feu d’entreprise et une banque de données cloud, vous devez installer la passerelle de gestion des données, un agent permettant le traitement et le déplacement des données hybrides sur votre ordinateur local. La passerelle de gestion des données peut être installée sur la même machine que le magasin de données lui-même ou sur une machine distincte ayant accès au magasin de données. Dans ce scénario, la sérialisation/désérialisation, la compression/décompression, le mappage de colonnes et la conversion de type sont effectués par la passerelle de gestion des données. Les données ne transitent pas via le service Azure Data Factory dans un tel cas. La passerelle de gestion des données les écrit directement dans le magasin de destination.

![copie local-cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour découvrir une introduction et une procédure pas à pas, et l’article [Passerelle de gestion de données](data-factory-data-management-gateway.md) pour obtenir des informations détaillées sur la passerelle de gestion des données.

Vous pouvez également déplacer des données depuis/vers des magasins de données pris en charge hébergés sur des machines virtuelles IaaS Azure (machines virtuelles d'infrastructure-as-a-service) avec la passerelle de gestion des données. Dans ce cas, la passerelle de gestion des données peut être installée sur la même machine virtuelle Azure que le magasin de données lui-même ou sur une machine virtuelle distincte ayant accès au magasin de données.

## Banques de données et formats pris en charge
L'activité de copie copie les données d'un magasin de données **source** vers un magasin de données **récepteur**. Data Factory prend en charge les banques de données suivantes, et les **données provenant de toute source peuvent être écrites vers tout récepteur**. Cliquez sur une banque de données pour découvrir comment copier des données depuis/vers cette banque.

Catégorie | Banque de données | Prise en charge en tant que source | Prise en charge en tant que récepteur
:------- | :--------- | :------------------ | :-----------------
Les tables Azure | [Objet blob Azure](data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](data-factory-azure-datalake-connector.md) <br/> [Base de données SQL Azure](data-factory-azure-sql-connector.md) <br/> [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) <br/> [Table Azure](data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](data-factory-azure-documentdb-connector.md) <br/> | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ 
Bases de données | [SQL Server](data-factory-sqlserver-connector.md)* <br/> [Oracle](data-factory-onprem-oracle-connector.md)* <br/> [MySQL](data-factory-onprem-mysql-connector.md)* <br/> [DB2](data-factory-onprem-db2-connector.md)* <br/> [Teradata](data-factory-onprem-teradata-connector.md)* <br/> [PostgreSQL](data-factory-onprem-postgresql-connector.md)* <br/> [Sybase](data-factory-onprem-sybase-connector.md)* <br/>[Cassandra](data-factory-onprem-cassandra-connector.md)* <br/>[MongoDB](data-factory-on-premises-mongodb-connector.md)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; 
Fichier | [Système de fichiers](data-factory-onprem-file-system-connector.md)* <br/> [Système de fichiers DFS Hadoop](data-factory-hdfs-connector.md)* | ✓ <br/> ✓ <br/> | ✓ <br/> &nbsp; 
Autres | [Salesforce](data-factory-salesforce-connector.md)<br/> [ODBC générique](data-factory-odbc-connector.md)* <br/> [OData générique](data-factory-odata-connector.md) <br/> [Table web (table au format HTML)](data-factory-web-table-connector.md) <br/> [GE Historian](data-factory-odbc-connector.md#ge-historian-store)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;

> [AZURE.NOTE] Les banques de données signalées par un astérisque (*) peuvent être locales ou résider sur une instance Azure IaaS. Elles nécessitent que vous installiez une [passerelle de gestion des données](data-factory-data-management-gateway.md) sur un ordinateur local ou Azure IaaS.

Si vous devez déplacer des données vers ou depuis une banque de données qui n’est pas prise en charge par **l’activité de copie**, vous pouvez utiliser **l’activité personnalisée** dans Data Factory avec votre propre logique de copie/déplacement des données. Pour plus d’informations sur la création et l’utilisation d’une activité personnalisée, consultez l’article [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md).

### Formats de fichiers pris en charge
L’activité de copie peut copier des fichiers tels quels entre deux banques de données basées sur des fichiers comme un objet blob Azure, un système de fichiers et le système de fichiers DFS Hadoop. Pour ce faire, vous pouvez ignorer la [section format](data-factory-create-datasets.md) dans les définitions des jeux de données d’entrée et de sortie, et les données sont copiées efficacement sans sérialisation/désérialisation.

L’activité de copie permet également de lire et d’écrire dans les fichiers aux formats spécifiés : texte, Avro, ORC et JSON. Voici quelques exemples d’activités de copie que vous pouvez effectuer :

-	copier des données au format texte (CSV) provenant d’objets blob Azure et les écrire dans SQL Azure ;
-	copier des fichiers au format texte (CSV) provenant d’un système de fichiers local et les écrire dans des objets blob Azure au format Avro ;
-	copier des données dans la base de données SQL Azure et les écrire dans le système de fichiers HDFS local au format ORC.



## <a name="global"></a>Déplacement des données disponible globalement
Même si Azure Data Factory est en soi disponible uniquement dans les régions États-Unis de l’Ouest, États-Unis de l’Est et Europe du Nord, le service alimentant l’activité de copie est disponible dans le monde entier, dans les régions et zones géographiques suivantes. La topologie globalement disponible garantit le déplacement efficace des données en évitant généralement les sauts entre régions. Consultez la section [Services par région](https://azure.azure.microsoft.com/.com/regions/#services) pour connaître la disponibilité du service Data Factory et du déplacement des données dans une région.

### Copier des données entre des banques de données cloud
Lorsque les banques de données source et récepteur résident toutes les deux dans le cloud, Azure Data Factory utilise un déploiement de service dans la région la plus proche de l’emplacement du récepteur dans la même zone géographique afin d’effectuer le déplacement des données. Pour connaître le mappage, reportez-vous au tableau suivant :

Région de la banque de données de destination | Région utilisée pour le déplacement des données
:----------------------------------- | :----------------------------
Est des États-Unis | Est des États-Unis
Est des États-Unis 2 | Est des États-Unis 2
Centre des États-Unis | Centre des États-Unis
Ouest des États-Unis | Ouest des États-Unis
États-Unis - partie centrale septentrionale | États-Unis - partie centrale septentrionale
Centre-Sud des États-Unis | Centre-Sud des États-Unis
Europe du Nord | Europe du Nord
Europe de l'Ouest | Europe de l'Ouest
Asie du Sud-Est | Asie du Sud-Est
Est de l'Asie | Asie du Sud-Est
Est du Japon | Est du Japon
Ouest du Japon | Est du Japon
Sud du Brésil | Sud du Brésil
Est de l’Australie | Est de l’Australie
Sud-est de l’Australie | Sud-est de l’Australie
Inde centrale | Inde centrale
Inde du Sud | Inde centrale
Inde occidentale | Inde centrale


> [AZURE.NOTE] Si la région de la banque de données de destination ne figure pas dans la liste ci-dessus, l’activité de copie échoue au lieu de passer par une autre région.

### Copie de données entre un magasin de données local et un magasin de données cloud
Lorsque les données sont copiées entre l’instance locale (ou une machine virtuelle IaaS Azure) et l’instance cloud, le déplacement des données est effectué par la [passerelle de gestion des données](data-factory-data-management-gateway.md) sur un ordinateur local ou une machine virtuelle IaaS Azure. Les données ne sont pas transmises via le service dans le nuage, sauf si vous utilisez la fonctionnalité de [copie intermédiaire](data-factory-copy-activity-performance.md#staged-copy), au quel cas les données sont transmises via le stockage d’objets blob Azure intermédiaire avant d’être écrites dans la banque de données récepteur.


## Créer un pipeline avec une activité de copie 
Vous pouvez créer un pipeline avec une activité de copie de deux façons :

### Utilisation de l’Assistant de copie
**L’Assistant Data Factory Copy** vous permet de créer un pipeline avec une activité de copie pour copier des données de sources prises en charge vers des destinations **sans avoir à écrire de définitions JSON** pour les services liés, les jeux de données et les pipelines. Reportez-vous au didacticiel sur [l’Assistant Data Factory Copy](data-factory-copy-wizard.md) pour connaître les détails de cet Assistant.

### Utilisation de scripts JSON
Vous pouvez utiliser Data Factory Editor dans le portail Azure, Visual Studio ou Azure PowerShell pour créer une définition JSON pour un pipeline avec une activité de copie et la déployer afin de créer le pipeline dans Data Factory. Consultez le [Didacticiel : Utilisation de l’activité de copie dans un pipeline Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour connaître les instructions des procédures pas à pas.

Les propriétés JSON telles que le nom, la description, les tables d’entrée et de sortie, les stratégies, etc. sont disponibles pour tous les types d’activité. En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité.

Dans le cas d’une activité de copie, la section **typeProperties** varie selon les types de source et de récepteur. Cliquez sur une source/un récepteur dans la section sur les [sources/récepteurs pris en charge](#supported-data-stores) pour en savoir plus sur les propriétés de type prises en charge par l’activité de copie de cette banque de données.

Voici un exemple de définition JSON :

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from Azure blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "InputBlobTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OutputSQLTable"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	} 

La planification définie dans le jeu de données de sortie détermine quand l’activité s’exécute (par exemple **quotidiennement** : fréquence : jour et intervalle : 1). L’activité permet de copier les données d’un jeu de données d’entrée (**source**) vers un jeu de données de sortie (**récepteur**). Vous pouvez spécifier plusieurs jeux de données d’entrée pour l’activité de copie. Ils sont utilisés pour vérifier les dépendances avant l’exécution de l’activité, mais seules les données du premier jeu de données sont copiées dans le jeu de données de destination. Pour plus d’informations, consultez l’article [Planification et exécution](data-factory-scheduling-and-execution.md).

## Performances et réglage 
Consultez l’article [Guide des performances et de l’optimisation de l’activité de copie](data-factory-copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.

## Planification et copie séquentielle
Consultez l’article [Planification et exécution](data-factory-scheduling-and-execution.md) pour plus d’informations sur le fonctionnement de la planification et de l’exécution dans Data Factory.

Il est possible d’exécuter plusieurs opérations de copie l’une après l’autre, de manière séquentielle/ordonnée. Consultez la section [Copie ordonnée](data-factory-scheduling-and-execution.md#ordered-copy) de cet article.

## Conversions des types 
Les magasins de données ont différents types de systèmes natifs. L’activité de copie convertit automatiquement des types source en types récepteur selon l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Vous pouvez trouver le mappage d’un système de type natif donné en .NET pour la banque de données dans les articles propres aux banques de données respectives (cliquez sur le lien respectif dans le tableau [Banques de données prises en charge](#supported-data-stores)). Vous pouvez utiliser ces mappages pour déterminer les types appropriés lors de la création de vos tables afin d'exécuter les conversions adaptées lors de l'activité de copie.

 
## Étapes suivantes
- Consultez l’article [Copie de données d’un objet blob vers Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour en savoir plus sur l’utilisation de l’activité de copie pour déplacer des données d’une banque de données source vers une banque de données récepteur.
- Consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur le déplacement des données depuis une banque de données locale vers une banque de données cloud.
 

<!---HONumber=AcomDC_0817_2016-->