<properties 
	pageTitle="Activités de déplacement des données | Microsoft Azure" 
	description="Apprenez-en plus sur le déplacement des données dans les pipelines Data Factory : migration de données entre des magasins de cloud, entre des emplacements locaux et le cloud. Utilisez l’activité de copie." 
	keywords="déplacement des données, migration des données, copier des données, transférer des données"
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
	ms.date="07/11/2016" 
	ms.author="spelluru"/>

# Déplacement des données et activité de copie : migration des données vers le cloud et entre les magasins cloud
Le déplacement des données à partir d’une source vers un récepteur (destination) est effectué par [l’activité de copie](#copyactivity) dans Azure Data Factory. L’activité de copie est effectuée par un [service globalement disponible](#global) sécurisé, fiable et évolutif. Le service choisit automatiquement la région optimale pour effectuer le déplacement des données, généralement la région la plus proche du magasin de données du récepteur.

Voici comment la migration de données se produit entre deux magasins de données cloud, entre un magasin de données local et un magasin de données cloud et vers ou à partir d’un magasin de données sur une machine virtuelle IaaS Azure.

## Copie de données entre deux magasins de données cloud
Lorsque les magasins de données source et récepteur (destination) se trouvent dans le cloud, l'activité de copie passe par les étapes suivantes pour copier/déplacer données de la source vers le récepteur. Le service qui alimente l’activité de copie effectue les opérations suivantes :

1. Lit les données du magasin de données source
2.	Effectue la sérialisation/désérialisation, la compression/décompression, le mappage des colonnes et la conversion de type en fonction de la configuration du jeu de données d'entrée, du jeu de données de sortie et de l'activité de copie
3.	Écrit les données dans le magasin de données de destination

![copie cloud-cloud](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copie de données entre un magasin de données local et un magasin de données cloud
Pour déplacer en toute sécurité des données entre des magasins de données locaux derrière votre pare-feu d’entreprise et un magasin de données cloud, vous devez installer la passerelle de gestion des données, un agent permettant le traitement et le déplacement des données hybrides sur votre ordinateur local. La passerelle de gestion des données peut être installée sur la même machine que le magasin de données lui-même ou sur une machine distincte ayant accès au magasin de données. Dans ce scénario, la sérialisation/désérialisation, la compression/décompression, le mappage de colonnes et la conversion de type sont effectués par la passerelle de gestion des données. Les données ne transitent pas via le service Azure Data Factory dans un tel cas. La passerelle de gestion des données les écrit directement dans le magasin de destination.

![copie local-cloud](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

Consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour découvrir une introduction et une procédure pas à pas et l’article [Data Management Gateway](data-factory-data-management-gateway.md) (Passerelle de gestion des données) pour obtenir des informations détaillées sur la passerelle de gestion des données.

## Copier des données depuis/vers un magasin de données sur une machine virtuelle IaaS Azure 
Vous pouvez également déplacer des données depuis/vers des magasins de données pris en charge hébergés sur des machines virtuelles IaaS Azure (machines virtuelles d'infrastructure-as-a-service) avec la passerelle de gestion des données. Dans ce cas, la passerelle de gestion des données peut être installée sur la même machine virtuelle Azure que le magasin de données lui-même ou sur une machine virtuelle distincte ayant accès au magasin de données.

## Magasins de données pris en charge
L'activité de copie copie les données d'un magasin de données **source** vers un magasin de données **récepteur**. Data Factory prend en charge les magasins de données suivants et des **données provenant de n'importe quelle source peuvent être écrites vers n'importe quel récepteur**. Cliquez sur une banque de données pour savoir comment copier des données à partir de/vers ce magasin.

| sources| Récepteurs |
|:------- | :---- |
| <ul><li>[Objet blob Azure](data-factory-azure-blob-connector.md)</li><li>[Table Azure](data-factory-azure-table-connector.md)</li><li>[Base de données SQL Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (voir la remarque ci-dessous)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[Serveur SQL local/IaaS Azure](data-factory-sqlserver-connector.md)</li><li>[Système de fichiers local/IaaS Azure](data-factory-onprem-file-system-connector.md)</li><li>[Base de données Oracle locale/IaaS Azure](data-factory-onprem-oracle-connector.md)</li><li>[Base de données MySQL locale/IaaS Azure](data-factory-onprem-mysql-connector.md)</li><li>[Base de données DB2 locale/IaaS Azure](data-factory-onprem-db2-connector.md)</li><li>[Base de données Teradata locale/IaaS Azure](data-factory-onprem-teradata-connector.md)</li><li>[Base de données Sybase locale/IaaS Azure](data-factory-onprem-sybase-connector.md)</li><li>[Base de données PostgreSQL locale/IaaS Azure](data-factory-onprem-postgresql-connector.md)</li><li>[Sources de données ODBC locales/IaaS Azure](data-factory-odbc-connector.md)</li><li>[Hadoop Distributed File System (HDFS) local/IaaS Azure](data-factory-hdfs-connector.md)</li><li>[Sources OData](data-factory-odata-connector.md)</li><li>[Table web (table HTML)](data-factory-web-table-connector.md)</li><li>[GE Historian local/IaaS Azure](data-factory-odbc-connector.md#ge-historian-store)</li></ul> | <ul><li>[Objet blob Azure](data-factory-azure-blob-connector.md)</li><li>[Table Azure](data-factory-azure-table-connector.md)</li><li>[Base de données SQL Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (voir la remarque ci-dessous)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[Serveur SQL local/IaaS Azure](data-factory-sqlserver-connector.md)</li><li>[Système de fichiers local/IaaS Azure](data-factory-onprem-file-system-connector.md)</li><li>[Base de données Oracle locale/IaaS Azure](data-factory-onprem-oracle-connector.md)</li></ul> |


> [AZURE.NOTE] La copie de données entre Azure DocumentDB et des magasins de données locaux IaaS Azure n’est pas prise en charge actuellement. La matrice complète d’Azure DocumentDB sera également activée sous peu.

Si vous devez déplacer des données vers ou à partir d’un magasin de données qui n’est pas pris en charge par **l’activité de copie**, utilisez **l’activité personnalisée** dans Data Factory avec votre propre logique de copie/déplacement des données. Consultez l’article [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md) pour plus d’informations sur la création et l’utilisation d’une activité personnalisée.

## Didacticiel
Pour suivre un didacticiel rapide sur l'activité de copie, reportez-vous au [Didacticiel : Utilisation de l'activité de copie dans un pipeline Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Dans ce didacticiel, vous utiliserez l’activité de copie pour copier des données entre un stockage d'objets blob Azure et une base de données SQL Azure.

## <a name="copyactivity"></a>Activité de copie
L’activité de copie permet de copier des données d’un jeu de données d’entrée (**source**) dans un jeu de données de sortie (**récepteur**). La copie des données est effectuée par lot conformément à la planification définie sur l'activité. Pour en savoir plus sur la définition des activités en général, consultez l’article [Pipelines et activités dans Azure Data Factory : créer et planifier des pipelines et des activités à la chaîne](data-factory-create-pipelines.md).

L'activité de copie fournit les fonctionnalités suivantes :

### <a name="global"></a>Déplacement des données disponible globalement
Même si Azure Data Factory est en soi disponible uniquement dans les régions Ouest des États-Unis, Est des États-Unis et Europe du Nord, le service alimentant l’activité de copie est disponible dans le monde entier, dans les régions et zones géographiques suivantes. La topologie globalement disponible garantit le déplacement efficace des données en évitant les sauts entre régions dans la plupart des cas.

La **passerelle de gestion des données** ou **Azure Data Factory** effectue le déplacement des données en fonction de l’emplacement des magasins de données source et de destination dans une opération de copie. Pour plus d’informations, consultez le tableau suivant :

Emplacement du magasin de données source | Emplacement du magasin de données de destination | Déplacement des données effectué par  
-------------------------- | ------------------------------- | ----------------------------- 
Local/Machine virtuelle Azure (IaaS) | Cloud | La **passerelle de gestion des données** sur un ordinateur local/une machine virtuelle Azure. Les données ne transitent pas via le service dans le cloud. <br/><br/>Remarque : la passerelle de gestion des données peut être sur le même ordinateur local ou la même machine virtuelle Azure que le magasin de données, ou sur un autre ordinateur local ou une autre machine virtuelle Azure, à condition qu’elle puisse se connecter aux deux magasins de données.
Cloud | Local/Machine virtuelle Azure (IaaS) | Identique à ce qui précède. 
Local/Machine virtuelle Azure (IaaS) | Local/Machine virtuelle Azure | **La passerelle de gestion des données associée à la source**. Les données ne transitent pas via le service dans le cloud. Voir la remarque ci-dessus.   
Cloud | Cloud | **Le service cloud qui alimente l’activité de copie**. Azure Data Factory utilise le déploiement de ce service dans la région la plus proche de l’emplacement du récepteur dans la même zone géographique. Reportez-vous au tableau suivant pour la mise en correspondance : <br/><br/><table><tr><th>Région du magasin de données de destination</th> <th>Région utilisée pour le déplacement des données</th></tr><tr><td>Est des États-Unis</td><td>Est des États-Unis</td></tr><tr><td>Est des États-Unis 2</td><td>Est des États-Unis 2</td><tr/><tr><td>Centre des États-Unis</td><td>Centre des États-Unis</td><tr/><tr><td>Ouest des États-Unis</td><td>Ouest des États-Unis</td></tr><tr><td>Nord du centre des États-Unis</td><td>Nord du centre des États-Unis</td></tr><tr><td>Sud du centre des États-Unis</td><td>Sud du centre des États-Unis</td></tr><tr><td>Europe du Nord</td><td>Europe du Nord</td></tr><tr><td>Europe de l’Ouest</td><td>Europe de l’Ouest</td></tr><tr><td>Asie du Sud-Est</td><td>Asie du Sud-Est</td></tr><tr><td>Asie de l’Est</td><td>Asie du Sud-Est</td></tr><tr><td>Est du Japon</td><td>Est du Japon</td></tr><tr><td>Ouest du Japon</td><td>Est du Japon</td></tr><tr><td>Sud du Brésil</td><td>Sud du Brésil</td></tr><tr><td>Est de l’Australie</td><td>Est de l’Australie</td></tr><tr><td>Sud-Est de l’Australie</td><td>Sud-Est de l’Australie</td></tr></table>


> [AZURE.NOTE] Si la région du magasin de données de destination ne figure pas dans la liste ci-dessus, l’activité de copie échoue au lieu de passer par une autre région.


### Déplacement fiable et économique des données
L'activité de copie est conçue pour déplacer de grands volumes de données de manière fiable et en évitant les erreurs temporaires sur une grande variété de sources de données. Les données peuvent être copiées selon une méthode économique avec une option permettant d'activer la compression sur le réseau.

### Conversions des types entre différents types de systèmes
Les magasins de données ont différents types de systèmes natifs. L'activité de copie convertit automatiquement des types sources en types récepteurs selon l'approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Vous pouvez trouver le mappage d'un système de type natif donné en .NET pour le magasin de données dans les articles sur les connecteurs de magasin de données respectifs. Vous pouvez utiliser ces mappages pour déterminer les types appropriés lors de la création de vos tables afin d'exécuter les conversions adaptées lors de l'activité de copie.

### Utilisation de différents formats de fichiers
L’activité de copie prend en charge différents formats de fichiers, notamment les formats binaire, texte, Avro. ORC et JSON pour les magasins basés sur des fichiers. Il est possible d'utiliser l'activité de copie pour convertir des données d'un format vers un autre. Exemple : du format texte (CSV) au format Avro. Si les données ne sont pas structurées, vous pouvez omettre la propriété **Structure** dans la définition JSON du [jeu de données](data-factory-create-datasets.md).

### Propriétés de l'activité de copie
Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités. En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité.

Dans le cas d’une activité de copie, la section **typeProperties** varie selon les types de sources et de récepteurs. Cliquez sur une source/un récepteur dans la section [Magasins de données pris en charge](#supported-data-stores) pour en savoir plus sur les propriétés de type prises en charge par l’activité de copie de ce magasin de données.

Chaque page spécifique du magasin de données répertoriée ci-dessus décrit les propriétés spécifiques au type de magasin de données.

### Copie ordonnée
Il est possible d’exécuter plusieurs opérations de copie l’une après l’autre, de manière séquentielle/ordonnée. Si, par exemple, vous avez deux activités de copie dans un pipeline : ActivitédeCopie1 et ActivitédeCopie2 avec les jeux de données de sortie de données d’entrée suivants.

ActivitédeCopie1 : Entrée : JeudeDonnées1 Sortie : JeudeDonnées2

ActivitédeCopie2 : Entrées : JeudeDonnées2 Sortie : JeudeDonnées4

ActivitédeCopie2 s’exécute uniquement si ActivitédeCopie1 s’est exécutée avec succès et que JeudeDonnées2 est disponible.

Dans l’exemple ci-dessus, ActivitédeCopie2 peut avoir une entrée différente, par exemple JeudeDonnées3, mais vous devez alors également spécifier JeudeDonnées2 en tant qu’entrée pour ActivitédeCopie2, afin que l’activité ne s’exécute pas avant la fin d’ActivitédeCopie1. Par exemple :

ActivitédeCopie1 : Entrée : JeudeDonnées1 Sortie : JeudeDonnées2

ActivitédeCopie2 : Entrées : JeudeDonnées3 Sortie JeudeDonnées2 : JeudeDonnées4

Quand plusieurs entrées sont spécifiées, seul le premier jeu de données d’entrée est utilisé pour copier des données, mais les autres jeux de données sont utilisés en tant que dépendances. L’exécution d’ActivitédeCopie2 démarre uniquement quand les conditions suivantes sont remplies :

- ActivitédeCopie2 s’est terminée avec succès et JeudeDonnées2 est disponible. Ce jeu de données n’est pas utilisé lors de la copie des données vers JeudeDonnées4. Il sert uniquement de dépendance de planification pour ActivitédeCopie2.
- JeudeDonnées3 est disponible. Ce jeu de données représente les données qui sont copiées vers la destination.


### Performances et optimisation de l’activité de copie 
Consultez l’article [Guide des performances et de l’optimisation de l’activité de copie](data-factory-copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.


## Assistant Data Factory Copy
**L’Assistant Data Factory Copy** vous permet de créer un pipeline pour copier des données des sources prises en charge vers les destinations sans avoir à écrire des définitions JSON pour les services liés, les jeux de données et les pipelines. Pour lancer l’Assistant Data Factory Copy, cliquez sur la mosaïque **Copier les données** sur la page d’accueil de votre fabrique de données.

![Assistant Copier des données](./media/data-factory-data-movement-activities/copy-data-wizard.png)

### Caractéristiques

#### Un Assistant intuitif et rapide pour copier des données 
Cet Assistant vous permet de déplacer facilement et rapidement des données d’une source vers une destination en procédant comme suit :

1.	Sélectionnez la **source**.
2.	Sélectionnez la **destination**.
3.	Configurez les **paramètres**.

![Sélectionnez la source de données](./media/data-factory-data-movement-activities/select-data-source-page.png)

#### Des options complètes d’exploration des données et de mappage de schéma
Utilisez l’Assistant pour parcourir des tables et des dossiers, afficher un aperçu des données, mapper un schéma, valider des expressions et effectuer des transformations de données simples.

**Parcourir des tables et des dossiers** ![Parcourir des tables et des dossiers](./media/data-factory-data-movement-activities/browse-tables-folders.png)

#### Une expérience évolutive pour divers types d’objets et de données
Dès le début, l’expérience est conçue pour traiter des données volumineuses (Big Data). Vous disposez d’un outil simple et performant pour créer des pipelines Data Factory capables de déplacer des centaines de dossiers, fichiers ou tables.

**Afficher un aperçu des données, mapper un schéma et effectuer des transformations simples** ![Paramètres de format de fichier](./media/data-factory-data-movement-activities/file-format-settings.png) ![Mappage de schéma](./media/data-factory-data-movement-activities/schema-mapping.png) ![Valider des expressions](./media/data-factory-data-movement-activities/validate-expressions.png)

#### Une expérience évolutive pour divers types d’objets et de données
Dès le début, l’expérience est conçue pour traiter des données volumineuses (Big Data). L’Assistant Copy est un moyen simple et efficace de déplacer des centaines de dossiers, fichiers ou tables.

![Sélectionner des tables pour la copie de données](./media/data-factory-data-movement-activities/select-tables-to-copy-data.png)

#### Des options de planification enrichies
Vous pouvez effectuer l’opération de copie une seule fois ou la répéter selon une planification établie (horaire, quotidienne, etc.). Ces deux options peuvent être définies pour l’ensemble des connecteurs utilisés pour la copie locale, dans le cloud ou sur un Bureau local. La copie unique vous permet de déplacer une seule fois des données d’une source vers une destination. Elle peut être utilisée pour les données de toute taille et de n’importe quel format pris en charge. La copie planifiée vous permet de copier des données selon une périodicité définie. Vous pouvez configurer la copie planifiée à l’aide de paramètres avancés (nouvelle tentative, délai d’attente, alertes, etc.).

![Propriétés de planification](./media/data-factory-data-movement-activities/scheduling-properties.png)


### Faites un essai 
Pour découvrir une procédure pas à pas rapide sur l’utilisation de **l’Assistant Data Factory Copy** afin de créer un pipeline avec une activité de copie, consultez [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md).


### Variables dans le chemin du dossier d’objets blob Azure
Vous pouvez utiliser des variables dans le chemin du dossier pour copier des données à partir d’un dossier qui est déterminé au moment de l’exécution par la [variable système WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Les variables prises en charge sont les suivantes : **year**, **month**, **day**, **hour**, **minute** et **{custom}**. Exemple : inputfolder/{year}/{month}/{day}.

Supposons que vos dossiers d’entrée présentent le format suivant :
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Cliquez sur le bouton **Parcourir** à côté de **Fichier ou dossier**, accédez à l’un de ces dossiers (par exemple, 2016->03->01->02), puis cliquez sur **Choisir**. Vous devez maintenant voir **2016/03/01/02** dans la zone de texte. À présent, remplacez **2016** par **{year}**, **03** par **{month}**, **01** par **{day}** et **02** par **{hour}**, puis appuyez sur **la touche de tabulation**. Vous devez maintenant voir des listes déroulantes pour sélectionner le **format** de ces quatre variables, comme illustré ci-dessous :

![Utilisation de variables système](./media/data-factory-data-movement-activities/blob-standard-variables-in-folder-path.png)

Vous pouvez également configurer une variable **custom**, comme indiqué ci-dessous, pour utiliser l’une des [chaînes de format prises en charge](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Pour afficher la zone de texte où entrer la chaîne de format, vous devez d’abord sélectionner un dossier avec cette structure à l’aide du bouton Parcourir, remplacer une valeur par **{custom}** et appuyer sur ** la touche de tabulation**.

![Utilisation de la variable custom](./media/data-factory-data-movement-activities/blob-custom-variables-in-folder-path.png)

## Étapes suivantes
- Consultez la page [Copie de données Blob Storage vers une base de données SQL à l’aide de Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour en savoir plus sur l’utilisation de l’activité de copie pour déplacer des données d’un magasin de données source vers un magasin de données récepteur.
- Consultez la page [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur le déplacement des données depuis un magasin de données local vers un magasin de données de cloud.
 

<!---HONumber=AcomDC_0713_2016-->