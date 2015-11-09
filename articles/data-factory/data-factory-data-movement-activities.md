<properties 
	pageTitle="Activités de déplacement des données" 
	description="Découvrez les entités Data Factory que vous pouvez utiliser dans les pipelines Data Factory pour déplacer des données." 
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
	ms.date="10/27/2015" 
	ms.author="spelluru"/>

# Activités de déplacement des données
Data Factory dispose d’un [service globalement disponible](#global) pour prendre en charge le déplacement des données avec une [activité de copie](#copyactivity) qui s’applique à toute une variété de magasins de données répertoriés ci-dessous. Data Factory permet également de [déplacer de façon sécurisée des données entre des sites locaux et le cloud](#moveonpremtocloud) à l'aide de la passerelle de gestion des données.

Pour un didacticiel rapide sur l’activité de copie, reportez-vous à [Didacticiel : Utilisation de l'activité de copie dans un pipeline Azure Data Factory](data-factory-get-started.md). Dans ce didacticiel, vous utiliserez l’activité de copie pour copier des données entre un stockage d'objets blob Azure et une base de données SQL Azure. La section suivante répertorie tous les sources et récepteurs pris en charge par l'activité de copie.


## Magasins de données pris en charge pour l’activité de copie
L’activité copie les données d’un magasin de données **source** vers un magasin de données **récepteur**. Data Factory prend en charge les combinaisons suivantes de magasins de données, source et de réception. Cliquez sur une banque de données pour savoir comment copier des données à partir de/vers ce magasin.

| **Source** | **Section sink** |
| ------ | ---- |
| [Objet blob Azure](data-factory-azure-blob-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure DocumentDB, système de fichiers local, Azure Data Lake Store |
| [Table Azure](data-factory-azure-table-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure DocumentDB, Azure Data Lake Store |
| [Azure SQL Database](data-factory-azure-sql-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure DocumentDB, Azure Data Lake Store |
| [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure DocumentDB, Azure Data Lake Store |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store |
| [Azure Data Lake Store](data-factory-azure-datalake-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure DocumentDB, système de fichiers local, Azure Data Lake Store | 
| [Serveur SQL sur IaaS](data-factory-sqlserver-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Système de fichiers local](data-factory-onprem-file-system-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, système de fichiers local, Azure Data Lake Store |
| [Serveur SQL local](data-factory-sqlserver-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Base de données Oracle locale](data-factory-onprem-oracle-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Base de données MySQL locale](data-factory-onprem-mysql-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Base de données DB2 locale](data-factory-onprem-db2-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Base de données Teradata locale](data-factory-onprem-teradata-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Base de données Sybase locale](data-factory-onprem-sybase-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |
| [Base de données PostgreSQL locale](data-factory-onprem-postgresql-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, serveur SQL local, serveur SQL sur IaaS, Azure Data Lake Store |

## <a name="copyactivity"></a>Activité de copie
L’activité de copie sélectionne un jeu de données d’entrée (**source**) et copie les données selon la configuration de l’activité vers un jeu de données de sortie (**récepteur**). La copie des données est effectuée par lot conformément à la planification définie sur l'activité.

> [AZURE.NOTE]Pour en savoir plus sur la définition des activités en général à un niveau élevé comme les différentes sections JSON et les propriétés disponibles pour toutes les activités, consultez l’article [Présentation des pipelines et des activités](data-factory-create-pipelines.md).

L’activité de copie fournit les fonctionnalités suivantes :

### <a name="global"></a>Déplacement des données globalement disponibles
Le service de déplacement des données de l’activité de copie est disponible globalement dans les régions et zones géographiques suivantes. La topologie globalement disponible garantit le déplacement efficace des données en évitant les sauts entre régions dans la plupart des cas.

| Région | Geography |
| ------ | --------- | 
| Centre des États-Unis | FR |
| Est des États-Unis | FR |
| Est des États-Unis 2 | FR |
| Nord du centre des États-Unis | FR |
| Sud du centre des États-Unis | FR |
| Ouest des États-Unis | FR |
| Sud du Brésil | Amérique latine |
| Europe du Nord | Europe/Moyen-Orient/Afrique |
| Europe de l'Ouest | Europe/Moyen-Orient/Afrique |
| Asie du Sud-Est | Asie-Pacifique |
| Est du Japon | Asie-Pacifique |

### <a name="moveonpremtocloud"></a>Déplacement en toute sécurité de données entre un site local et le cloud
Un des défis de l’intégration de données modernes consiste à déplacer en toute transparence des données vers et depuis un site local et le cloud. La passerelle de gestion des données est un agent que vous pouvez installer en local pour créer des pipelines de données hybrides.

La passerelle de données offre les fonctionnalités suivantes :

1.	Gestion sécurisée de l’accès aux magasins de données locaux.
2.	Modélisation des magasins de données locaux et des magasins de données sur le cloud au sein de la même fabrique de données, et déplacement des données.
3.	Un point unique de surveillance et de gestion avec une visibilité du statut de la passerelle et un tableau de bord basé sur un cloud de fabrique de données.


Pour plus d’informations, consultez [Déplacement des données entre un site local et le cloud](data-factory-move-data-between-onprem-and-cloud.md).

### Déplacement fiable et économique des données
L’activité de copie sert à déplacer de grands volumes de données de manière fiable et en évitant les erreurs temporaires entre une grande variété de sources de données. Les données peuvent être copiées selon une méthode économique avec une option permettant d'activer la compression sur le réseau.

### Conversions des types entre différents types de systèmes
Les magasins de données ont différents types de systèmes natifs. L’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Vous pouvez trouver le mappage d'un système de type natif donné en .NET pour le magasin de données dans les articles sur les connecteurs de magasin de données respectifs. Vous pouvez utiliser ces mappages pour déterminer les types appropriés lors de la création de vos tables afin d’exécuter les conversions appropriées lors de l'activité de copie.

### Utilisation de différents formats de fichiers
L’activité de copie prend en charge différents formats de fichiers, y compris les formats binaire, texte et Avro pour les magasins basés sur des fichiers. Il est possible d’utiliser l’activité de copie pour convertir des données d’un format vers un autre. Exemple : du format texte (CSV) au format Avro.

### Propriétés de l’activité de copie
Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités. En revanche, les propriétés disponibles dans la section **typeProperties** de l’activité varient pour chaque type d’activité.

Pour l’activité de copie, la section **typeProperties** varie selon les types de sources et récepteurs. Chaque page spécifique du magasin de données répertoriée ci-dessus décrit les propriétés spécifiques au type de magasin de données.


## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-movement-activities.md).

<!---HONumber=Nov15_HO1-->