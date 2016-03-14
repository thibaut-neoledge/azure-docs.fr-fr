<properties
	pageTitle="Guide sur les performances et le réglage de l’activité de copie"
	description="En savoir plus sur les facteurs clés ayant une incidence sur les performances du déplacement de données dans Azure Data Factory via l’activité de copie."
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
	ms.date="12/17/2015"
	ms.author="spelluru"/>


# Guide sur les performances et le réglage de l’activité de copie
Cet article décrit les facteurs clés qui ont une incidence sur les performances de déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.

## Vue d’ensemble du déplacement de données dans Azure Data Factory
L’Activité de copie déplace des données dans Azure Data Factory. Elle est alimentée par un [service de déplacement des données disponible à l’échelle mondiale](data-factory-data-movement-activities.md#global), qui peut copier des données entre [différents magasins de données](data-factory-data-movement-activities.md#supported-data-stores-for-copy-activity) de façon sécurisée, fiable, évolutive et performante. Le service de déplacement de données choisit automatiquement la région optimale pour effectuer l’opération de déplacement des données en fonction de l’emplacement des magasins de données source et récepteur. Actuellement, la région la plus proche du magasin de données récepteur est utilisée.

Essayons de comprendre comment se produit ce déplacement de données dans différents scénarios.

### Copie de données entre deux magasins de données cloud
Lorsque les magasins de données source et récepteur (destination) se trouvent dans le cloud, l’activité de copie passe par les étapes suivantes pour copier/déplacer des données de la source vers le récepteur.

1.	Lit les données du magasin de données source
2.	Effectue la sérialisation/désérialisation, la compression/décompression, le mappage des colonnes et la conversion de type en fonction de la configuration du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie
3.	Écrit les données dans le magasin de données de destination

![Copie de données entre deux magasins de données cloud](./media/data-factory-copy-activity-performance/copy-data-between-two-cloud-stores.png)

**Remarque :** les formes représentée en trait en pointillé (compression, mappage de colonnes, etc.) sont des fonctionnalités qui peuvent ou non être exploitées dans votre cas d’utilisation.


### Copie de données entre un magasin de données local et un magasin de données cloud
Pour [déplacer des données entre un magasin de données local et un magasin de données cloud](data-factory-move-data-between-onprem-and-cloud.md), vous devez installer la passerelle de gestion des données, un agent permettant le traitement et le déplacement de données hybrides sur votre machine locale. Dans le cadre de ce scénario, passerelle de gestion des données effectue la sérialisation/désérialisation, la compression/décompression, le mappage des colonnes et la conversion de type en fonction des configuration du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie

![Copie de données entre un magasin de données local et un magasin de données cloud](./media/data-factory-copy-activity-performance/copy-data-between-onprem-and-cloud.png)

## Procédure de réglage des performances
Les étapes classiques que nous vous suggérons pour régler les performances de votre solution Azure Data Factory avec une activité de copie sont répertoriées ci-dessous.

1.	**Établir une ligne de base.** Pendant la phase de développement, testez votre pipeline avec l’activité de copie sur un échantillon de données représentatif. Pour limiter la quantité de données que vous utilisez, vous pouvez tirer parti du [modèle de découpage](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) d’Azure Data Factory.

	Collectez les caractéristiques de temps d’exécution et de performances, en consultant les panneaux « Tranche de données » et « Détails de l’exécution de l’activité » du jeu de données de sortie dans le portail Azure en version préliminaire, qui indiquent la durée de l’activité de copie et la taille des données copiées.

	![Détails de l’exécution d’activité](./media/data-factory-copy-activity-performance/activity-run-details.png)

	Vous pouvez comparer les performances et les configurations de votre scénario aux [Performances de référence](#performance-reference) de l’activité de copie publiées sur les observations internes.
2. **Diagnostic et optimisation des performances** Si les performances que vous observez sont inférieures à vos attentes, vous devez identifier les goulots d’étranglement et opérer des optimisations pour supprimer ou réduire l’impact des goulots d’étranglement. Une description complète du diagnostic des performances dépasserait la portée de cet article, mais vous trouverez quelques informations générales ci-après.
	- [Source](#considerations-on-source)
	- [Section sink](#considerations-on-sink)
	- [Sérialisation/désérialisation.](#considerations-on-serializationdeserialization)
	- [Compression](#considerations-on-compression)
	- [Mappage de colonnes](#considerations-on-column-mapping)
	- [Passerelle de gestion de données](#considerations-on-data-management-gateway)
	- [Autres considérations](#other-considerations)
3. **Étendez la configuration à l’ensemble de vos données** Lorsque vous êtes satisfait des résultats et des performances de l’exécution, vous pouvez étendre la définition du jeu de données et de la période active du pipeline pour couvrir l’ensemble des données.

## Performances de référence
> [AZURE.IMPORTANT] **Exclusion :** les données ci-dessous ont été publiées dans le seul but de fournir des conseils et de proposer une planification de haut niveau. La bande passante, le matériel, la configuration et autres aspects sont supposés figurer parmi les meilleurs de leur catégorie. Utilisez ces données uniquement en guise de référence. Le débit de déplacement des données que vous observez dépend d’une série de variables. Reportez-vous aux sections ultérieurement pour découvrir comment vous pouvez éventuellement régler et obtenir de meilleures performances pour vos besoins en matière de déplacement des données. Ces données seront mises à jour au fur et à mesure des ajouts de fonctionnalités et améliorations des performances.

![Matrice des performances](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] **Bientôt disponible :** nous sommes en train d’améliorer les performances de base, et le tableau ci-dessous présentera bientôt des valeurs de débit meilleures et en plus grand nombre.

Points à noter :

- Le débit est calculé à l’aide de la formule suivante : [taille des données lues à partir de la source]/[durée d’exécution de l’activité de copie]
- Le jeu de données [TPC-H](http://www.tpc.org/tpch/) a été utilisé pour calculer les nombres ci-dessus.
- En ce qui concerne les magasins de données Microsoft Azure, la source et le récepteur sont dans la même région Azure.
- Dans le cas du déplacement de données hybrides (de locales vers le cloud, ou de cloud vers locales), la passerelle de gestion des données (instance unique) était hébergée sur un ordinateur différent de celui du magasin de données local, à l’aide de la configuration suivante. Notez qu’avec une seule exécution d’activité sur la passerelle, l’opération de copie ne consommait qu’une petite partie de la bande passante réseau et des ressources de processeur et de mémoire de cet ordinateur.
	<table>
	<tr>
		<td>UC</td>
		<td>32 cœurs 2,20 GHz Intel Xeon® E5-2660 v2</td>
	</tr>
	<tr>
		<td>Mémoire</td>
		<td>128 GO</td>
	</tr>
	<tr>
		<td>Réseau</td>
		<td>Interface Internet : 10 Gbits/s&#160;; interface intranet : 40 Gbits/s</td>
	</tr>
	</table>

## Considérations sur la source
### Généralités
Vérifiez que le magasin de données sous-jacent n’est pas submergé par d’autres charges de travail s’exécutant dessus ou s’y rapportant, notamment une activité de copie.

Pour les magasins de données Microsoft, reportez-vous aux [rubriques relatives à la surveillance et au réglage](#appendix-data-store-performance-tuning-reference) spécifiques du magasin de données, qui peuvent vous aider à comprendre les caractéristiques de performances de celui-ci, à réduire les temps de réponse et à maximiser le débit.

### Magasins de données basés sur un fichier
*(Incluant les objets Blob Azure, Azure Data Lake et le système de fichiers local)*

- **Taille moyenne de fichier et nombre de fichiers** : l’activité de copie transfère des données fichier par fichier. Pour une même quantité de données à déplacer, le débit global sera plus lent si les données se composent d’un grand nombre de petits fichiers plutôt que d’un petit nombre de fichiers plus volumineux, en raison de la phase d’amorçage nécessaire pour chaque fichier. Par conséquent, vous devez autant que possible combiner plusieurs petits fichiers en fichiers plus volumineux pour augmenter le débit.
- **Format de fichier et compression** : pour d’autres méthodes permettant d’améliorer les performances, voir les sections [Considérations sur la sérialisation/désérialisation](#considerations-on-serializationdeserialization) et [Considérations sur la compression](#considerations-on-compression).
- En outre, pour le scénario de **système de fichiers local** où l’utilisation d’une **passerelle de gestion des données** est requise, voir la section [Considérations sur la passerelle](#considerations-on-data-management-gateway).

### Bases de données relationnelles
*(Incluant Base de données SQL Azure, Azure SQL Data Warehouse, Base de données SQL Server, Base de données Oracle, Base de données MySQL, Base de données DB2, Base de données Teradata, Base de données Sybase et Base de données PostgreSQL)*

- **Modèle de données** : le schéma de table a une incidence sur le débit de copie. Pour copier une même quantité de données, une taille de ligne importante produit de meilleures performances qu’une petite taille, car la base de données peut extraire plus efficacement moins de lots de données contenant moins de lignes.
- **Requête ou procédure stockée** : optimisez la logique de la requête ou de la procédure stockée que vous spécifiez dans la source d’activité de copie, afin d’extraire les données plus efficacement.
- En outre, pour des **bases de données relationnelles locales**, telles que SQL Server et Oracle, où l’utilisation d’une **passerelle de gestion des données** est requise, consultez la section [Considérations sur la passerelle](#considerations-on-data-management-gateway).

## Considérations sur le récepteur

### Généralités
Vérifiez que le magasin de données sous-jacent n’est pas submergé par d’autres charges de travail s’exécutant dessus ou s’y rapportant, notamment une activité de copie.

Pour les magasins de données Microsoft, reportez-vous aux [rubriques relatives à la surveillance et au réglage](#appendix-data-store-performance-tuning-reference) spécifiques du magasin de données, qui peuvent vous aider à comprendre les caractéristiques de performances de celui-ci, à réduire les temps de réponse et à maximiser le débit.

### Magasins de données basés sur un fichier
*(Incluant les objets Blob Azure, Azure Data Lake et le système de fichiers local)*

- **Comportement de copie** : si vous copiez des données d’un autre magasin de données basé sur un fichier, l’activité de copie fournit trois types de comportements via la propriété « copyBehavior » : conserver la hiérarchie, aplatir la hiérarchie et fusionner les fichiers. La conservation ou l’aplanissement de la hiérarchie entraîne peu ou pas de surcharge de performances, tandis que la fusion de fichiers entraîne une surcharge supplémentaire.
- **Format de fichier et compression** : pour d’autres méthodes permettant d’améliorer les performances, voir les sections [Considérations sur la sérialisation/désérialisation](#considerations-on-serializationdeserialization) et [Considérations sur la compression](#considerations-on-compression).
- Pour les **objets blob Azure**, nous ne prenons en charge actuellement que les objets blob de blocs pour l’optimisation du transfert et du débit de données.
- En outre, pour les scénarios de **système de fichiers local** où l’utilisation d’une **passerelle de gestion des données** est requise, voir la section [Considérations sur la passerelle](#considerations-on-data-management-gateway).

### Bases de données relationnelles
*(Incluant Base de données SQL Azure, Azure SQL Data Warehouse et Base de données SQL Server)*

- **Comportement de copie** : selon les propriétés configurées pour « sqlSink », l’activité de copie écrit des données dans la base de données de destination de différentes façons :
	- Par défaut, le service de déplacement de données utilise une API de copie en bloc pour insérer des données en mode Append, ce qui optimise les performances.
	- Si vous configurez une procédure stockée dans le récepteur, la base de données applique les données ligne par ligne, au lieu de les charger en bloc, ce qui entraîne une baisse sensible des performances. Si la taille des données est importante, autant que possible, songez à utiliser plutôt la propriété « sqlWriterCleanupScript » (voir ci-dessous).
	- Si vous configurez la propriété « sqlWriterCleanupScript », pour chaque exécution d’une activité de copie, le service déclenche d’abord le script, puis utilise l’API de copie en bloc pour insérer les données. Par exemple, pour remplacer les données de la table entière par les dernières données, vous pouvez spécifier un script qui supprime tous les enregistrements avant de charger en bloc les nouvelles données à partir de la source.
- **Modèle de données et taille de lot** :
	- Le schéma de table a une incidence sur le débit de copie. Pour copier une même quantité de données, une taille de ligne importante produit de meilleures performances qu’une petite taille, car la base de données peut valider plus efficacement moins de lots de données.
	- L’activité de copie insère les données dans une série de lots dont le nombre de lignes peut être défini à l’aide de la propriété de « writeBatchSize ». Si vos données comportent des lignes de petite taille, vous pouvez définir la propriété « writeBatchSize » sur une valeur plus élevée pour réduire la surcharge de traitement par lots et augmenter le débit. Si la taille de ligne de vos données est importante, soyez prudent en augmentant la valeur de writeBatchSize, car une valeur élevée peut faire échouer la copie en raison d’une surcharge de la base de données.
- En outre, pour des **bases de données relationnelles locales**, telles que SQL Server et Oracle, où l’utilisation d’une **passerelle de gestion des données** est requise, consultez la section [Considérations sur la passerelle](#considerations-on-data-management-gateway).


### Magasins NoSQL
*(Incluant table Azure et Azure DocumentDB)*

- Pour **Table Azure** :
	- **Partition** : l’écriture de données en partitions entrelacées réduit considérablement les performances. Vous pouvez classer vos données sources par clé de partition afin qu’elles soient insérées efficacement partition après partition, ou ajuster la logique pour écrire les données dans une seule partition.
- Pour **Azure DocumentDB** :
	- **Taille de lot** : la propriété de « writeBatchSize » indique le nombre de demandes parallèles adressées au service DocumentDB pour la création de documents. Vous pouvez vous attendre à de meilleures performances lorsque vous augmentez la valeur « writeBatchSize », car davantage de demandes parallèles sont envoyées à DocumentDB. Toutefois, prenez garde à la limitation lors de l’écriture dans DocumentDB (message d’erreur « Le taux de demandes est élevé »). Une limitation peut se produire en raison de divers facteurs, dont la taille des documents, le nombre de termes qu’ils contiennent, et la stratégie d’indexation de la collection cible. Pour obtenir un débit de copie plus élevé, songez à utiliser une meilleure collection (par exemple, S3).

## Considérations sur la sérialisation/désérialisation.
Une sérialisation et une désérialisation peuvent se produire quand le jeu de données d’entrée ou sortie est un fichier. L’activité de copie prend actuellement en charge les formats de données Avro et texte (par exemple, CSV et TSV).

**Comportements de copie :**

- Lors de la copie de fichiers entre magasins de données basés sur des fichiers :
	- Lorsque les jeux de données d’entrée et de sortie ont les mêmes paramètres de format de fichier ou n’en ont aucun, le service de déplacement de données exécute une copie binaire sans effectuer de sérialisation/désérialisation. Par conséquent, vous pouvez constater un meilleur débit par rapport au scénario où les paramètres de format de fichier source/récepteur diffèrent.
	- Lorsque les jeux de fichiers d’entrée et de sortie sont au format texte, et que seul le type d’encodage diffère, le service de déplacement de données opère uniquement une conversion de codage sans effectuer de sérialisation/désérialisation, ce qui entraîne une surcharge de performances par rapport à une copie binaire.
	- Lorsque les jeux de données d’entrée et de sortie diffèrent par leurs format de fichier ou leur configuration, par exemple au niveau des séparateurs, le service de déplacement de données désérialise les données sources en flux, les transforme, puis les sérialise au format de sortie souhaité. Cela entraîne une surcharge des performances beaucoup plus importante par rapport aux scénarios précédents.
- Lors de la copie de fichiers vers un magasin de données non basé sur un fichier ou à partir de celui-ci (par exemple, d’un magasin basé sur un fichier vers une base relationnelle), une étape de sérialisation ou de désérialisation est requise, ce qui entraîne une surcharge significative des performances.

**Format de fichier :** le choix du format de fichier peut avoir une incidence sur les performances de copie. Par exemple, Avro est un format de fichier binaire compact qui stocke des métadonnées avec les données, et est largement pris en charge dans l’écosystème Hadoop pour le traitement et les requêtes. En revanche, le format Avro étant plus coûteux en sérialisation/désérialisation, le débit de copie est inférieur par rapport au format texte. Le choix du format de fichier à utiliser ainsi que du flux de traitement doit être effectué de façon holistique, en considérant la forme sous laquelle les données sont stockées dans les magasins de données sources ou doivent être extraites de systèmes externes, le meilleur format pour le stockage, le traitement analytique et les requêtes, et le format dans lequel les données doivent être exportées dans des mini-data Warehouses pour les outils de rapport et de visualisation. Parfois, un format de fichier non optimal pour les performances en lecture et écriture peut s’avérer bien adapté pour le processus analytique général.

## Considérations relatives à la compression
Lorsque votre jeu de données d’entrée ou de sortie est un fichier, vous pouvez configurer l’activité de copie pour qu’elle effectue une compression ou une décompression lors de l’écriture de données dans la destination. L’activation de la compression constitue compromis entre les entrées/sorties (E/S) et l’utilisation du processeur : la compression des données nécessite des ressources de calcul supplémentaires mais réduit les E/S et le stockage réseau, ce qui, selon vos données, peut améliorer le débit global de copie.

**Codec :** les types de compressions GZIP, BZIP2 et Deflate sont pris en charge. Les trois types peuvent être utilisés par Azure HDInsight pour le traitement. Chaque codec de compression a sa spécificité. Par exemple, le codec BZIP2 présente le débit le plus bas, mais offre les meilleures performances de requête Hive, car il peut être fractionné pour le traitement. Le codec GZIP constitue l’option la plus équilibrée et la plus souvent utilisée. Vous devez choisir le codec le plus approprié pour votre scénario de bout en bout.

**Niveau :** pour chaque codec de compression, vous avec le choix entre deux options, la compression la plus rapide et la compression optimale. L’option de compression la plus rapide compresse les données aussi rapidement que possible, même si le fichier résultant de l’opération n’est pas compressé de façon optimale. L’option de compression optimale nécessite plus de temps, mais produit une quantité de données minimale. Vous pouvez tester les deux options pour voir celle qui offre les meilleures performances globales dans votre cas.

**Considération :** pour la copie d’un volume important de données entre un magasin local et le cloud, où la bande passante du réseau d’entreprise et Azure sont souvent le facteur de limitation, et où vous souhaitez que les jeux de données d’entrée et de sortie soient sous forme non compressée, vous pouvez envisager d’utiliser un **objet blob Azure temporaire** avec compression. Plus précisément, vous pouvez scinder une activité de copie en deux activités : la première copie à partir de la source dans un objet blob temporaire ou intermédiaire sous forme compressée, et la deuxième copie les données compressées de l’objet intermédiaire et les décompresse lors de l’écriture dans le récepteur.

## Considérations sur le mappage de colonnes
La propriété « columnMappings » dans l’activité de copie permet de mapper la totalité ou un sous-ensemble des colonnes d’entrée aux colonnes de sortie. Après avoir lu les données de la source, le service de déplacement de données doit effectuer un mappage de colonnes aux données avant d’écrire celles-ci sur le récepteur. Ce traitement supplémentaire réduit le débit de copie.

Si votre magasin de données source est interrogeable, qu’il s’agisse d’une base relationnelle de type SQL Azure/SQL Server ou d’un magasin NoSQL de type Table Azure/Azure DocumentDB, vous pouvez envisager d’envoyer la logique de filtrage/réorganisation de colonne à la propriété de requête au lieu d’utiliser un mappage de colonnes, ce qui a pour effet d’opérer la projection pendant la lecture des données à partir du magasin de données source, et est beaucoup plus efficace.

## Considérations sur la passerelle de gestion des données
Pour des recommandations relatives à la configuration de la passerelle, voir [Considérations relatives à l’utilisation de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Environnement de l’ordinateur de la passerelle :** nous vous recommandons d’utiliser un ordinateur dédié pour héberger la passerelle de gestion des données. Utilisez des outils tels que PerfMon pour examiner l’utilisation du processeur, de la mémoire et de la bande passante pendant une opération de copie sur l’ordinateur de la passerelle. Basculez vers un ordinateur plus puissant si la bande passante du processeur, de la mémoire ou du réseau forme un goulot d’étranglement.

**Exécutions d’activités de copie simultanées :** une seule instance de la passerelle de gestion des données peut traiter plusieurs exécutions d’activités de copie en même temps. Ainsi, une passerelle peut exécuter simultanément plusieurs travaux de copie, dont le nombre est calculé en fonction de la configuration matérielle de l’ordinateur de la passerelle. Les travaux de copie excédentaires sont placés en file d’attente jusqu’à ce que la passerelle les récupère ou jusqu’à ce qu’ils expirent, selon l’événement qui se produit en premier. Pour éviter tout conflit de ressources sur la passerelle, vous pouvez planifier vos activités par phases afin de réduire la quantité de travaux de copie mis en file d’attente en même temps, ou envisager de fractionner la charge sur plusieurs passerelles.


## Autres considérations
Si les données à copier sont très volumineuses, vous pouvez ajuster votre logique métier afin de partitionner davantage les données à l’aide du mécanisme de découpage d’Azure Data Factory, et planifier l’activité de copie plus fréquemment pour réduire la taille des données pour chaque exécution d’activité de copie.

Soyez prudent en ce qui concerne le nombre de jeux de données et d’activités de copie qui parviennent à une même base de données à un moment donné. Un grand nombre de travaux de copie simultanés peut limiter un magasin de données, et entraîner une dégradation des performances, une multiplication des tentatives internes de travail de copie et, dans certains cas, des échecs d’exécution.

## Étude de cas – copie d’un serveur SQL Server local vers un objet blob Azure
**Scénario :** un pipeline est conçu pour copier des données d’un serveur SQL Server local vers un objet blob Azure au format CSV. Pour accélérer la copie, il est spécifié que les fichiers CSV doivent être compressés au format BZIP2.

**Test et analyse :** il est constaté que le débit de l’activité de copie est inférieur à 2 Mo/s, ce qui est beaucoup plus lent que le test d’évaluation des performances.

**Analyse des performances et réglage :** pour résoudre le problème de performances, nous allons tout d’abord examiner pas à pas la manière dont les données sont traitées et déplacées :

1.	**Lecture des données :** la passerelle ouvre la connexion à SQL Server et envoie la requête. SQL Server répond en envoyant le flux de données à la passerelle via un intranet.
2.	La passerelle **sérialise** le flux de données au format CSV, et **compresse** les données dans un flux BZIP2.
3.	**Écriture des données :** la passerelle charge le flux BZIP2 vers l’objet blob Azure via Internet.

Comme vous pouvez le voir, les données sont traitées et déplacées de manière séquentielle par diffusion en continu : SQL Server -> Réseau local (LAN) -> Passerelle -> Réseau étendu (WAN) -> objet Blob Azure. **Les performances globales sont déterminées par le débit minimal du pipeline**.

![flux de données](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Un ou plusieurs des facteurs suivants peuvent occasionner un goulot d’étranglement des performances :

1.	**Source :** SQL Server offre lui-même un faible débit en raison de lourdes charges.
2.	**Passerelle de gestion de données :**
	1.	**LAN :** la passerelle est éloignée du serveur SQL Server auquel elle est reliée par une connexion à faible bande passante
	2.	La **charge sur l’ordinateur de la passerelle** a atteint ses limites pour l’exécution des opérations suivantes :
		1.	**Sérialisation :** la sérialisation du flux de données au format CSV présente un débit lent
		2.	**Compression :** le codec de compression choisi est lent (par exemple, BZIP2, avec un débit de 2,8 Mo/s avec Core i7)
	3.	**WAN :** faible bande passante entre le réseau d’entreprise et Azure (par exemple, T1 = 1 544 Kbits/s, T2 = 6 312 Kbits/s)
4.	**Récepteur :** l’objet blob Azure présente un faible débit (bien que ce soit très improbable étant donné que son contrat SLA garantit un minimum de 60 Mo/s).

Dans ce cas, la compression de données BZIP2 pourrait ralentir l’ensemble du pipeline. Un basculement vers le codec de compression GZIP peut résoudre ce goulot d’étranglement.

## Annexe : référence sur le réglage des performances du magasin de données
Voici quelques références relatives à la surveillance et au réglage des performances pour quelques magasins de données pris en charge :

- Azure Storage (y compris les objets blob Azure et la table Azure) : [Objectifs d’évolutivité d’Azure Storage](../storage/storage-scalability-targets.md) et [Liste de contrôle des performances et de l’évolutivité d’Azure Storage](../storage//storage-performance-checklist.md)
- Base de données SQL Azure : vous pouvez [surveiller les performances](../sql-database/sql-database-service-tiers.md#monitoring-performance) et vérifier le pourcentage de l’unité de transaction de base de données (DTU).
- Azure SQL Data Warehouse : sa capacité est mesurée en Data Warehouse Units (DWU). Voir [Performances et mise à l’échelle élastiques avec SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-performance-scale.md).
- Azure DocumentDB : [Niveaux de performances dans DocumentDB](../documentdb/documentdb-performance-levels.md).
- SQL Server local : [Surveillance et réglage des performances](https://msdn.microsoft.com/library/ms189081.aspx).
- Serveur de fichiers local : [Réglage des performances des serveurs de fichiers](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0302_2016-->