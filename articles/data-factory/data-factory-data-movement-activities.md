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
	ms.date="02/03/2016" 
	ms.author="spelluru"/>

# Activités de déplacement des données
L’[Activité de copie](#copyactivity) effectue le déplacement des données dans Azure Data Factory, et l’activité est alimentée par un [service disponible à l’échelle mondiale](#global) qui peut copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Le service choisit automatiquement la région la plus optimale pour effectuer le déplacement des données. La région la plus proche du magasin de données récepteur est utilisée.
 
Essayons de comprendre comment se produit ce déplacement de données dans différents scénarios.

## Copie de données entre deux magasins de données cloud
Lorsque les magasins de données source et récepteur (destination) se trouvent dans le cloud, l'activité de copie passe par les étapes suivantes pour copier/déplacer données de la source vers le récepteur. Le service qui alimente l’activité de copie effectue les opérations suivantes :

1. Lit les données du magasin de données source
2.	Effectue la sérialisation/désérialisation, la compression/décompression, le mappage des colonnes et la conversion de type en fonction de la configuration du jeu de données d'entrée, du jeu de données de sortie et de l'activité de copie 
3.	Écrit les données dans le magasin de données de destination

![copie cloud-cloud](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copie de données entre un magasin de données local et un magasin de données cloud
Pour [déplacer en toute sécurité des données entre des magasins de données locaux derrière votre pare-feu d'entreprise et un magasin de données cloud](#moveonpremtocloud), vous devez installer la passerelle de gestion des données, un agent permettant le traitement et le déplacement des données hybrides sur votre machine locale. La passerelle de gestion des données peut être installée sur la même machine que le magasin de données lui-même ou sur une machine distincte ayant accès au magasin de données. Dans ce scénario, la sérialisation/désérialisation, la compression/décompression, le mappage de colonnes et la conversion de type sont effectués par la passerelle de gestion des données. Les données ne transitent pas via le service Azure Data Factory dans un tel cas. La passerelle de gestion des données les écrit directement dans le magasin de destination.

![copie local-cloud](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copier des données depuis/vers un magasin de données sur une machine virtuelle IaaS Azure 
Vous pouvez également déplacer des données depuis/vers des magasins de données pris en charge hébergés sur des machines virtuelles IaaS Azure (machines virtuelles d'infrastructure-as-a-service) avec la passerelle de gestion des données. Dans ce cas, la passerelle de gestion des données peut être installée sur la même machine virtuelle Azure que le magasin de données lui-même ou sur une machine virtuelle distincte ayant accès au magasin de données.

## Magasins de données pris en charge
L'activité de copie copie les données d'un magasin de données **source** vers un magasin de données **récepteur**. Data Factory prend en charge les magasins de données suivants et des **données provenant de n'importe quelle source peuvent être écrites vers n'importe quel récepteur**. Cliquez sur une banque de données pour savoir comment copier des données à partir de/vers ce magasin.

| sources| Récepteurs |
|:------- | :---- |
| <ul><li>[Objet blob Azure](data-factory-azure-blob-connector.md)</li><li>[Table Azure](data-factory-azure-table-connector.md)</li><li>[Base de données SQL Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (voir remarque ci-après)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Système de fichiers local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Base de données Oracle locale/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[Base de données MySQL locale/Azure IaaS](data-factory-onprem-mysql-connector.md)</li><li>[Base de données DB2 locale/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Base de données Teradata locale/Azure IaaS](data-factory-onprem-teradata-connector.md)</li><li>[Base de données Sybase locale/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[Base de données PostgreSQL locale/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[Sources de données ODBC locales/Azure IaaS](data-factory-odbc-connector.md)</li><li>[Hadoop Distributed File System (HDFS) local/Azure IaaS](data-factory-hdfs-connector.md)</li><li>[Sources OData](data-factory-odata-connector.md)</li><li>[Table web](data-factory-web-table-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Table Azure](data-factory-azure-table-connector.md)</li><li>[Base de données Azure SQL](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (voir remarque ci-après)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Système de fichiers local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] Vous ne pouvez opérer des déplacement qu’à destination/en provenance d’Azure DocumentDB à partir de/vers d’autres services Azure comme Objet blob Azure, Table Azure, Base de données SQL Azure, Azure SQL Data Warehouse, Azure DocumentDB et Azure Data Lake Store. La matrice complète d’Azure DocumentDB sera aussi prise en charge sous peu.

## Didacticiel
Pour suivre un didacticiel rapide sur l'activité de copie, reportez-vous au [Didacticiel : Utilisation de l'activité de copie dans un pipeline Azure Data Factory](data-factory-get-started.md). Dans ce didacticiel, vous utiliserez l’activité de copie pour copier des données entre un stockage d'objets blob Azure et une base de données SQL Azure.

## <a name="copyactivity"></a>Activité de copie
L'activité de copie prend un jeu de données d'entrée (**source**) et un jeu de données de sortie (**récepteur**). La copie des données est effectuée par lot conformément à la planification définie sur l'activité. Pour en savoir plus sur la définition des activités en général, consultez l’article [Présentation des pipelines et des activités](data-factory-create-pipelines.md).

L'activité de copie fournit les fonctionnalités suivantes :

### <a name="global"></a>Déplacement des données disponible globalement
Même si Azure Data Factory est en soi disponible uniquement dans la région Ouest des États-Unis et dans le Nord de l’Europe, le service alimentant l’activité de copie est disponible dans le monde entier, dans les régions et zones géographiques suivantes. La topologie globalement disponible garantit le déplacement efficace des données en évitant les sauts entre régions dans la plupart des cas.

La **passerelle de gestion des données** ou **Azure Data Factory** effectue le déplacement des données en fonction de l’emplacement des magasins de données source et de destination dans une opération de copie. Pour plus d’informations, consultez le tableau suivant :

Emplacement du magasin de données source | Emplacement du magasin de données de destination | Déplacement des données effectué par  
-------------------------- | ------------------------------- | ----------------------------- 
Local/Machine virtuelle Azure (IaaS) | Cloud | La **passerelle de gestion des données** sur un ordinateur local/une machine virtuelle Azure. Les données ne transitent pas via le service dans le cloud. <p> Remarque : la passerelle de gestion des données peut être sur le même ordinateur local/la même machine virtuelle Azure que le magasin de données ou sur un autre ordinateur local/une machine virtuelle Azure différente tant qu’elle peut se connecter aux deux magasins de données.</p>
Cloud | Local/Machine virtuelle Azure (IaaS) | Identique à ce qui précède. 
Local/Machine virtuelle Azure (IaaS) | Local/Machine virtuelle Azure | La **passerelle de gestion des données associée à la source**. Les données ne transitent pas via le service dans le cloud. Voir la remarque ci-dessus.   
Cloud | Cloud | <p>**Le service cloud qui alimente l’activité de copie**. Azure Data Factory utilise le déploiement de ce service dans la région la plus proche de l’emplacement du récepteur dans la même zone géographique. Reportez-vous au tableau suivant pour connaître le mappage: </p><table><tr><th>Région du magasin de données de destination</th> <th>Région utilisée pour le déplacement des données</th></tr><tr><td>États-Unis de l’Est</td><td>États-Unis de l’Est</td></tr><tr><td>États-Unis de l’Est 2</td><td>États-Unis de l’Est 2</td></tr><tr><td>États-Unis du Centre</td><td>États-Unis du Centre</td></tr><tr><td>États-Unis de l’Ouest</td><td>États-Unis de l’Ouest</td></tr><tr><td>Nord du centre des États-Unis</td><td>Nord du centre des États-Unis</td></tr><tr><td>Sud du centre des États-Unis</td><td>Sud du centre des États-Unis</td></tr><tr><td>Europe du Nord</td><td>Europe du Nord</td></tr><tr><td>Europe de l’Ouest</td><td>Europe de l’Ouest</td></tr><tr><td>Sud-Est asiatique</td><td>Asie du Sud-Est</td></tr><tr><td>Asie de l’Est</td><td>Asie du Sud-Est</td></tr><tr><td>Japon de l’Est</td><td>Japon de l’Est </td></tr><tr><td>Japon de l’Ouest</td><td>Japon de l’Est</td></tr><tr><td>Sud du Brésil</td><td>Sud du Brésil</td></tr><tr><td>Est de l’Australie</td><td>Est de l’Australie</td></tr><tr><td>Sud-Est de l’Australie</td><td>Sud-Est de l’Australie</td></tr></table>


> [AZURE.NOTE] Si la région du magasin de données de destination ne figure pas dans la liste ci-dessus, l’activité de copie échoue au lieu de passer par une autre région.



### <a name="moveonpremtocloud"></a>Déplacer des données en toute sécurité entre un emplacement local et le cloud
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
Les magasins de données ont différents types de systèmes natifs. L'activité de copie convertit automatiquement des types sources en types récepteurs selon l'approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Vous pouvez trouver le mappage d'un système de type natif donné en .NET pour le magasin de données dans les articles sur les connecteurs de magasin de données respectifs. Vous pouvez utiliser ces mappages pour déterminer les types appropriés lors de la création de vos tables afin d'exécuter les conversions adaptées lors de l'activité de copie.

### Utilisation de différents formats de fichiers
L'activité de copie prend en charge différents formats de fichiers, notamment les formats binaire, texte et Avro pour les magasins basés sur des fichiers. Il est possible d'utiliser l'activité de copie pour convertir des données d'un format vers un autre. Exemple : du format texte (CSV) au format Avro. Si les données ne sont pas structurées, vous pouvez omettre la propriété **Structure** dans la définition JSON du [jeu de données](data-factory-create-datasets.md).

### Propriétés de l'activité de copie
Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités. En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité.

Pour l'activité de copie, la section **typeProperties** varie selon le type de source et de récepteur. Chaque page spécifique du magasin de données répertoriée ci-dessus décrit les propriétés spécifiques au type de magasin de données.


### Performances et optimisation de l’activité de copie 
Consultez l’article [Guide des performances et de l’optimisation de l’activité de copie](data-factory-copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes façons d’optimiser les performances de l’activité de copie.

<!---HONumber=AcomDC_0302_2016-->
