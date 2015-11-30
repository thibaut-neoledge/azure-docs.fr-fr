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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Activités de déplacement des données
L'[Activité de copie](#copyactivity) effectue le déplacement des données dans Azure Data Factory et l'activité est alimentée par un [Service de déplacement des données disponible à l'échelle mondiale](#global) qui peut copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Le service choisit automatiquement la région la plus optimale pour effectuer l'opération de déplacement des données en fonction de l'emplacement des magasins de données source et récepteur. Actuellement, la région la plus proche du magasin de données récepteur est utilisée.
 
Essayons de comprendre comment se produit ce déplacement de données dans différents scénarios.

## Copie de données entre deux magasins de données cloud
Lorsque les magasins de données source et récepteur (destination) se trouvent dans le cloud, l'activité de copie passe par les étapes suivantes pour copier/déplacer données de la source vers le récepteur. Le service de déplacement des données

1. Lit les données du magasin de données source
2.	Effectue la sérialisation/désérialisation, la compression/décompression, le mappage des colonnes et la conversion de type en fonction de la configuration du jeu de données d'entrée, du jeu de données de sortie et de l'activité de copie 
3.	Écrit les données dans le magasin de données de destination

![copie cloud-cloud](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copie de données entre un magasin de données local et un magasin de données cloud
Pour [déplacer en toute sécurité des données entre des magasins de données locaux derrière votre pare-feu d'entreprise et un magasin de données cloud](#moveonpremtocloud), vous devez installer la passerelle de gestion des données, un agent permettant le traitement et le déplacement des données hybrides sur votre machine locale. La passerelle de gestion des données peut être installée sur la même machine que le magasin de données lui-même ou sur une machine distincte ayant accès au magasin de données. Dans ce scénario, la sérialisation/désérialisation, la compression/décompression, le mappage de colonnes et la conversion de type sont effectués par la passerelle de gestion des données. Le service de déplacement de données n'est pas impliqué dans ce scénario.

![copie local-cloud](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copier des données depuis/vers un magasin de données sur une machine virtuelle IaaS Azure 
Vous pouvez également déplacer des données depuis/vers des magasins de données pris en charge hébergés sur des machines virtuelles IaaS Azure (machines virtuelles d'infrastructure-as-a-service) avec la passerelle de gestion des données. Dans ce cas, la passerelle de gestion des données peut être installée sur la même machine virtuelle Azure que le magasin de données lui-même ou sur une machine virtuelle distincte ayant accès au magasin de données.

## Magasins de données pris en charge
L'activité de copie copie les données d'un magasin de données **source** vers un magasin de données **récepteur**. Data Factory prend en charge les combinaisons suivantes de magasins de données, source et de réception. Cliquez sur une banque de données pour savoir comment copier des données à partir de/vers ce magasin.

| **Source** | **Section sink** |
| ------ | ---- |
| [Objet blob Azure](data-factory-azure-blob-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure DocumentDB, système de fichiers local/IaaS Azure, Azure Data Lake Store |
| [Table Azure](data-factory-azure-table-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure DocumentDB, Azure Data Lake Store |
| [Base de données SQL Azure](data-factory-azure-sql-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure DocumentDB, Azure Data Lake Store |
| [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure DocumentDB, Azure Data Lake Store |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | Objet blob Azure, table Azure, Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store |
| [Azure Data Lake Store](data-factory-azure-datalake-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure DocumentDB, système de fichiers local/IaaS Azure, Azure Data Lake Store | 
| [Serveur SQL sur IaaS](data-factory-sqlserver-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Système de fichiers local/IaaS Azure](data-factory-onprem-file-system-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, système de fichiers local/IaaS Azure, Azure Data Lake Store |
| [Serveur SQL local/IaaS Azure](data-factory-sqlserver-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Base de données Oracle locale/IaaS Azure](data-factory-onprem-oracle-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Base de données MySQL locale/IaaS Azure](data-factory-onprem-mysql-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Base de données DB2 locale/IaaS Azure](data-factory-onprem-db2-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Base de données Teradata locale/IaaS Azure](data-factory-onprem-teradata-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Base de données Sybase locale/IaaS Azure](data-factory-onprem-sybase-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |
| [Base de données PostgreSQL locale/IaaS Azure](data-factory-onprem-postgresql-connector.md) | Objet blob Azure, table Azure, base de données SQL Azure, Azure SQL Data Warehouse, serveur SQL local/IaaS Azure, Azure Data Lake Store |

## Didacticiel
Pour suivre un didacticiel rapide sur l'activité de copie, reportez-vous à [Didacticiel : Utiliser l'activité de copie dans un pipeline Azure Data Factory](data-factory-get-started.md). Dans ce didacticiel, vous utiliserez l’activité de copie pour copier des données entre un stockage d'objets blob Azure et une base de données SQL Azure. La section suivante répertorie tous les sources et récepteurs pris en charge par l'activité de copie.

## <a name="copyactivity"></a>Activité de copie
L'activité de copie prend un jeu de données d'entrée (**source**) et un jeu de données de sortie (**récepteur**). La copie des données est effectuée par lot conformément à la planification définie sur l'activité. Pour en savoir plus sur la définition des activités de haut niveau en général, notamment différentes sections JSON et les propriétés disponibles pour toutes les activités, consultez l'article [Présentation des pipelines et des activités](data-factory-create-pipelines.md).

L'activité de copie fournit les fonctionnalités suivantes :

### <a name="global"></a>Déplacement des données disponible à l'échelle mondiale
Même si Azure Data Factory est en soi disponible uniquement dans la région Ouest des États-Unis et dans le Nord de l'Europe, le service de déplacement des données avec l'activité de copie est disponible dans le monde entier, dans les régions et zones géographiques suivantes. La topologie globalement disponible garantit le déplacement efficace des données en évitant les sauts entre régions dans la plupart des cas.

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

Notez les points suivants :

- Si vous copiez des données à partir d'une **source de données locale** vers le **cloud** ou vice versa (par exemple, serveur SQL local -> objet blob Azure), le déplacement des données est en fait effectué par la **passerelle de gestion des données** dans votre environnement local sans intervention du service de déplacement des données.
- Si vous copiez des données entre un **cloud source** et un **cloud de destination** (par exemple, objet blob Azure -> SQL Azure), le **service de déplacement des données** sélectionne le déploiement **le plus proche de l'emplacement récepteur dans la même zone géographique** pour effectuer le transfert. Par exemple, si vous copiez des données à partir de l'Asie du Sud-Est vers l'Ouest du Japon, le déploiement du service de déplacement des données dans l'Est du Japon est utilisé pour effectuer la copie. Lorsque la source et la destination se trouvent dans la même zone géographique et qu'aucun service de déplacement des données n'est disponible dans cette zone géographique (par exemple, l'Australie actuellement), l'activité de copie échouera au lieu de passer par une autre zone géographique. Remarque : le service de déplacement de données serait étendu à l'Australie également. 

### <a name="moveonpremtocloud"></a>Déplacer en toute sécurité des données entre un emplacement local et le cloud
Un des défis de l’intégration de données modernes consiste à déplacer en toute transparence des données vers et depuis un site local et le cloud. La passerelle de gestion des données est un agent que vous pouvez installer en local pour créer des pipelines de données hybrides.

La passerelle de données offre les fonctionnalités suivantes :

1.	Gestion sécurisée de l’accès aux magasins de données locaux.
2.	Modélisation des magasins de données locaux et des magasins de données sur le cloud au sein de la même fabrique de données, et déplacement des données.
3.	Un point unique de surveillance et de gestion avec une visibilité du statut de la passerelle et un tableau de bord basé sur un cloud de fabrique de données.

Vous devez considérer votre source de données comme une source de données locale (qui se trouve derrière un pare-feu) même lorsque vous utilisez **ExpressRoute** et **utiliser la passerelle** pour établir la connectivité entre le service et la source de données.

Pour plus d'informations, consultez [Déplacer des données entre un emplacement local et le cloud](data-factory-move-data-between-onprem-and-cloud.md).

### Déplacement fiable et économique des données
L'activité de copie est conçue pour déplacer de grands volumes de données de manière fiable et en évitant les erreurs temporaires sur une grande variété de sources de données. Les données peuvent être copiées selon une méthode économique avec une option permettant d'activer la compression sur le réseau.

### Conversions des types entre différents types de systèmes
Les magasins de données ont différents types de systèmes natifs. L'activité de copie convertit automatiquement des types sources en types récepteurs selon l'approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Vous pouvez trouver le mappage d'un système de type natif donné en .NET pour le magasin de données dans les articles sur les connecteurs de magasin de données respectifs. Vous pouvez utiliser ces mappages pour déterminer les types appropriés lors de la création de vos tables afin d'exécuter les conversions adaptées lors de l'activité de copie.

### Utilisation de différents formats de fichiers
L'activité de copie prend en charge différents formats de fichiers, notamment les formats binaire, texte et Avro pour les magasins basés sur des fichiers. Il est possible d'utiliser l'activité de copie pour convertir des données d'un format vers un autre. Exemple : du format texte (CSV) au format Avro. Si les données ne sont pas structurées, vous pouvez omettre la propriété **Structure** dans la définition JSON du [jeu de données](data-factory-create-datasets.md).

### Propriétés de l'activité de copie
Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités. En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité.

Pour l'activité de copie, la section **typeProperties** varie selon le type de source et de récepteur. Chaque page spécifique du magasin de données répertoriée ci-dessus décrit les propriétés spécifiques au type de magasin de données.

<!---HONumber=Nov15_HO4-->