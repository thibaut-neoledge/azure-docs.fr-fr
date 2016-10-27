<properties
    pageTitle="Toutes les rubriques pour le service Data Factory | Microsoft Azure"
    description="Tableau de toutes les rubriques du service Azure nommé data-factory qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>



# <a name="all-topics-for-azure-data-factory-service"></a>Toutes les rubriques pour le service Azure Data Factory

Cette rubrique répertorie toutes les rubriques qui s'appliquent directement au service Azure **Data Factory** . Pour trouver les rubriques qui vous intéressent, vous pouvez rechercher des mots clés sur cette page web à l'aide de la combinaison de touches **Ctrl+F**.




## <a name="new"></a>Nouveau

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 1 | [Déplacer des données depuis Amazon Redshift à l’aide d’Azure Data Factory](data-factory-amazon-redshift-connector.md) | Apprenez à déplacer des données à partir d’Amazon Redshift à l’aide d’Azure Data Factory. |
| 2 | [Déplacement de données à partir d’Amazon Simple Storage Service à l’aide d’Azure Data Factory](data-factory-amazon-simple-storage-service-connector.md) | Découvrez comment déplacer des données à partir d’Amazon Simple Storage Service (S3) à l’aide d’Azure Data Factory. |
| 3 | [Azure Data Factory - Assistant Copy](data-factory-azure-copy-wizard.md) | Découvrez comment utiliser l’Assistant Azure Data Factory Copy pour copier des données à partir de sources de données prises en charge dans des récepteurs. |
| 4 | [Didacticiel : Créer votre première fabrique de données Azure en utilisant l’API REST Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l’aide de l’API REST Data Factory. |
| 5 | [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API .NET. |
| 6 | [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API REST. |
| 7 | [Assistant Data Factory Copy](data-factory-copy-wizard.md) | Découvrez comment utiliser l’Assistant Data Factory Copy pour copier des données à partir de sources de données prises en charge dans des récepteurs. |
| 8 | [Passerelle de gestion de données](data-factory-data-management-gateway.md) | Mettez en place une passerelle de données pour déplacer vos données entre un emplacement local et le cloud. Utilisez la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 9 | [Déplacer des données depuis une base de données Cassandra locale à l’aide d’Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Découvrez comment déplacer des données depuis une base de données Cassandra locale à l’aide d’Azure Data Factory. |
| 10 | [Déplacer des données depuis MongoDB à l’aide d’Azure Data Factory](data-factory-on-premises-mongodb-connector.md) | Découvrez comment déplacer des données depuis une base de données MongoDB à l’aide d’Azure Data Factory. |
| 11 | [Déplacer des données depuis Salesforce à l’aide d’Azure Data Factory](data-factory-salesforce-connector.md) | Découvrez comment déplacer des données depuis Salesforce à l’aide d’Azure Data Factory. |


## <a name="updated-articles,-data-factory"></a>Articles mis à jour, Data Factory

Cette section répertorie les articles qui ont été mis à jour récemment et dont la mise à jour a été volumineuse ou importante. Pour chaque article mis à jour, un extrait de code approximatif du texte markdown ajouté s’affiche. Les articles ont été mis à jour dans la plage de dates allant du **22-08-2016** au **05-10-2016**.

| &nbsp; | Article | Texte mis à jour, extrait de code | Mis à jour quand |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory : Journal des modifications de l’API .NET](data-factory-api-change-log.md) | Cet article fournit des informations sur les modifications apportées au SDK Azure Data Factory dans une version spécifique. Vous trouverez le dernier package NuGet pour Azure Data Factory ici (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) ** Version 4.11.0** Ajouts de fonctionnalités : / Les types de services liés suivants ont été ajoutés :  -  OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx)    -  AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx)   -  AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / Les types de jeux de données suivants ont été ajoutés :  -  MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx)  -  AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / Les types de sources de copie suivants ont été ajoutés :    -  MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) ** Version 4.10.0** / Les propriétés facultatives suivantes ont été ajoutées à TextFormat :    -  Ski | 2016-09-22 |
| 13. | [Déplacer des données vers et depuis un objet Blob Azure à l’aide d’Azure Data Factory](data-factory-azure-blob-connector.md) |  /  copyBehavior  /  Cette propriété définit le comportement de copie lorsque la source est BlobSource ou FileSystem.  /  **PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible..br/..br/.**FlattenHierarchy :** tous les fichiers du dossier source se trouvent dans le premier niveau du dossier cible. Le nom des fichiers cibles est généré automatiquement. .br/..br/.**MergeFiles : (par défaut)** fusionne tous les fichiers du dossier source dans un même fichier. Si le nom de fichier/d’objet blob est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement.  /  No  /  **BlobSource** prend également en charge ces deux propriétés pour la compatibilité descendante. / **treatEmptyAsNull**: spécifie s’il faut traiter une chaîne null ou vide en tant que valeur null. / **skipHeaderLineCount** : spécifie le nombre de lignes à ignorer. Applicable uniquement quand le jeu de données d’entrée utilise TextFormat. De même, **BlobSink** prend en charge | 2016-09-28 |
| 14 | [Créer des pipelines prédictifs à l’aide des activités Azure Machine Learning](data-factory-azure-ml-batch-execution-activity.md) | **Service web nécessitant plusieurs entrées** Si le service web prend plusieurs entrées, utilisez la propriété **webServiceInputs** au lieu d’utiliser **webServiceInput**. Les jeux de données référencés par **webServiceInputs** doivent également être inclus dans les **entrées** de l’activité. Dans votre expérience Azure ML, les ports et paramètres globaux de l’entrée et la sortie du service web ont des noms par défaut (« input1 », « input2 ») que vous pouvez personnaliser. Les noms que vous utilisez pour les paramètres globalParameters, webServiceOutputs et webServiceInputs doivent correspondre exactement aux noms utilisés dans les expériences. Vous pouvez afficher la charge utile de l’exemple de requête sur la page d’aide relative à l’exécution par lots pour votre point de terminaison Azure ML afin de vérifier le mappage attendu.    {       "name": "PredictivePipeline",       "properties": {             "description": "use AzureML model",             "activities":  {                "name": "MLActivity",               "type": "AzureMLBatchExecution",                "description": "prediction analysis on batch input",                "inputs":  {                    "name": "inputDataset1"                 }, {                    "name": "inputDatase | 2016-09-13 |
| 15 | [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) | 1. **Établir une ligne de base**. Pendant la phase de développement, testez votre pipeline en utilisant l’activité de copie sur un échantillon de données représentatif. Vous pouvez utiliser le modèle de découpage de Data Factory (data-factory-planification-et-execution.md time-series-datasets-and-data-slices) pour limiter la quantité de données avec lesquelles vous travaillez.  Collectez les temps d’exécution et les caractéristiques de performances à l’aide de **l’application de surveillance et gestion**. Choisissez **Surveiller et gérer** sur votre page d’accueil Data Factory. Dans l’arborescence, sélectionnez le **jeu de données de sortie**. Dans la liste des **fenêtres d’activité** , sélectionnez l’exécution de l’activité de copie. **fenêtres d’activité** répertorient la durée de l’activité de copie et la taille des données qui sont copiées. Le débit est répertorié dans **l’Explorateur de fenêtres d’activité**. Pour en savoir plus sur l’application, consultez Surveiller et gérer les pipelines Azure Data Factory à l’aide de l’application de surveillance et gestion (data-factory-monitor-manage-app.md).  ! Détails de l’exécution d’activité (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn | 2016-09-27 |
| 16 | [Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Notez les points suivants : - Le **type** de jeu de données est défini sur **AzureBlob**.     - Le paramètre **linkedServiceName** est défini sur la valeur **AzureStorageLinkedService**. Vous avez créé ce service lié à l’étape 2.     - **folderPath** est défini sur le conteneur **adftutorial**. Vous pouvez également spécifier le nom d’un objet blob dans le dossier à l’aide de la propriété **fileName** . Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.    - Le **type** de format a la valeur **TextFormat**  - Le fichier texte contient deux champs, **FirstName** et **LastName**, séparés par une virgule (**columnDelimiter**)     - Le paramètre **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval**, la valeur **1**). Le service Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur d’objets blob (**adftutorial**) que vous avez spécifié.  Si vous ne spécifiez pas de nom (**fileName**) pour un jeu de données **d’entrée**, tous les fichiers/objets blob du dossier d’entrée (**folderPath**) sont considérés | 2016-09-29 |
| 17 | [Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically.md) | Notez l’ID d’application et le mot de passe (clé secrète client) et utilisez-les dans la procédure. **Obtenir les ID d’abonnement et client Azure** Si vous n’avez pas la dernière version d’Azure PowerShell sur votre ordinateur, suivez les instructions de l’article Installation et configuration d’Azure PowerShell (../powershell-install-configure.md). 1. Démarrez Azure PowerShell et exécutez la commande suivante 2. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.         Login-AzureRmAccount   Si vous n’avez qu’un seul abonnement Azure associé à ce compte, il est inutile d’effectuer les deux étapes suivantes. 3. Exécutez la commande suivante pour afficher tous les abonnements de ce compte.       Get-AzureRmSubscription 4. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **NameOfAzureSubscription** par le nom de votre abonnement Azure.       Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription  /  Set-AzureRmCo | 2016-09-14 |
| 18 | [Pipelines et activités dans Azure Data Factory](data-factory-create-pipelines.md) |       ,       "start": "2016-07-12T00:00:00Z",    "end": "2016-07-13T00:00:00Z"       }     } Notez les points suivants : / Dans la section des activités, il existe une seule activité dont le **type** a la valeur **Copy**. / L’entrée de l’activité est définie sur **InputDataset** et sa sortie, sur **OutputDataset**. / Dans la section **typeProperties**, **BlobSource** est spécifié en tant que type de source et **SqlSink**, en tant que type de récepteur. Pour une procédure pas à pas complète de création de ce pipeline, consultez Didacticiel : Copie de données de Stockage Blob vers une SQL Database (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Exemple de pipeline de transformation** Dans l’exemple de pipeline suivant, il existe une activité de type **HDInsightHive** dans la section **activités**. Dans cet exemple, l’activité Hive HDInsight (data-factory-hive-activity.md) transforme les données d’un Stockage Blob Azure en exécutant un fichier de script Hive sur un cluster Hadoop Azure HDInsight.  {     "name": "TransformPipeline",    "p | 2016-09-27 |
| 19 | [Transformer des données dans Azure Data Factory](data-factory-data-transformation-activities.md) | Data Factory prend en charge les activités de transformation des données suivantes, qui peuvent être ajoutées à des pipelines (data-factory-create-pipelines.md), soit individuellement soit de façon chaînée avec une autre activité. .  AZURE.NOTE Pour des instructions détaillées, consultez l’article Créer un pipeline avec la transformation Hive (data-factory-build-your-first-pipeline.md). ** Activité Hive HDInsight** L’activité Hive HDInsight d’un pipeline Data Factory exécute des requêtes Hive sur votre propre cluster ou cluster à la demande HDInsight sous Windows ou Linux. Consultez l’article Activité Hive (data-factory-hive-activity.md) pour plus de détails sur cette activité. ** Activité Pig HDInsight** L’activité Pig HDInsight d’un pipeline Data Factory exécute des requêtes Pig sur votre propre cluster ou cluster à la demande HDInsight sous Windows ou Linux. Consultez l’article Activité Pig (data-factory-pig-activity.md) pour plus de détails sur cette activité. **Activité MapReduce HDInsight** L’activité MapReduce de HDInsight dans un pipeline Data Factory exécute des programmes MapReduce sur votre | 2016-09-26 |
| 20 | [Planification et exécution avec Data Factory](data-factory-scheduling-and-execution.md) | ActivitédeCopie2 s’exécute uniquement si ActivitédeCopie1 s’est exécutée avec succès et que JeudeDonnées2 est disponible. Voici l’exemple de pipeline JSON : {       "name": "ChainActivities",    "properties": {           "description": "Run activities in sequence",      "activities":       {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "copyBehavior": "PreserveHierarchy",    "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "inputs":       {       "name": "Dataset1"      }       ,       "outputs":      {       "name": "Dataset2"      }       ,       "policy": {     "timeout": "01:00:00"       },      "scheduler": {      "frequency": "Hour",    "interval": 1       },      "name": "CopyFromBlob1ToBlob2",     "description": "Copy data from a blob to another"       },      {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "in | 2016-09-28 |





## <a name="tutorials"></a>Didacticiels

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 21 | [Didacticiel : Générer votre premier pipeline pour traiter les données à l’aide du cluster Hadoop](data-factory-build-your-first-pipeline.md) | Ce didacticiel Azure Data Factory vous montre comment créer et planifier une fabrique de données qui traite les données à l’aide du script Hive sur un cluster Hadoop. |
| 22 | [Didacticiel : concevoir votre première fabrique de données Azure à l’aide du modèle Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) | Dans ce didacticiel, vous créez un exemple de pipeline Azure Data Factory en utilisant un modèle Azure Resource Manager. |
| 23 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide du portail Azure](data-factory-build-your-first-pipeline-using-editor.md) | Dans ce didacticiel, vous créez un exemple de pipeline Azure Data Factory à l’aide de Data Factory Editor dans le portail Azure. |
| 24 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide d’Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l’aide d’Azure PowerShell. |
| 25 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide de Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory avec Visual Studio. |
| 26 | [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide du portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de Data Factory Editor dans le portail Azure. |
| 27 | [Didacticiel : Créer un pipeline avec l'activité de copie à l'aide d'Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide d’Azure PowerShell. |
| 28 | [Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de Visual Studio. |
| 29 | [Copie de données Blob Storage vers une base de données SQL à l’aide de Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Ce didacticiel vous montre comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données depuis Blob Storage vers une base de données SQL Azure. |
| 30 | [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie, à l’aide de l’Assistant de copie et de Data Factory. |



## <a name="data-movement"></a>Déplacement des données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 31 | [Déplacer des données vers et depuis un objet Blob Azure à l’aide d’Azure Data Factory](data-factory-azure-blob-connector.md) | Découvrez comment copier des données d’objets blob dans Azure Data Factory. Les exemples suivants indiquent comment copier des données vers et depuis Azure Blob Storage et Base de données SQL Azure. |
| 32 | [Déplacer des données vers et depuis Azure Data Lake Store à l’aide d’Azure Data Factory](data-factory-azure-datalake-connector.md) | Découvrez comment déplacer des données depuis et vers le stockage Azure Data Lake Store à l’aide d’Azure Data Factory. |
| 33 | [Déplacer des données vers et depuis DocumentDB à l’aide d’Azure Data Factory](data-factory-azure-documentdb-connector.md) | Découvrez comment déplacer des données depuis et vers la collection Azure DocumentDB à l’aide d’Azure Data Factory. |
| 34 | [Déplacer des données vers et depuis Base de données SQL Azure à l’aide d’Azure Data Factory](data-factory-azure-sql-connector.md) | Découvrez comment déplacer des données depuis et vers Azure SQL Database à l’aide d’Azure Data Factory. |
| 35 | [Déplacer des données vers et depuis Azure SQL Data Warehouse à l’aide d’Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) | Découvrez comment déplacer des données depuis et vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory. |
| 36 | [Déplacer des données vers et depuis Azure Table à l’aide d’Azure Data Factory](data-factory-azure-table-connector.md) | Découvrez comment déplacer des données depuis et vers le stockage Azure Table à l’aide d’Azure Data Factory. |
| 37 | [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) | En savoir plus sur les facteurs clés ayant des répercussions sur les performances du déplacement de données dans Azure Data Factory lorsque vous utilisez l’activité de copie. |
| 38 | [Déplacer des données à l’aide de l’activité de copie](data-factory-data-movement-activities.md) | Apprenez-en plus sur le déplacement des données dans les pipelines Data Factory : migration de données entre des magasins de cloud, entre des boutiques locales et cloud. Utiliser l’activité de copie. |
| 39 | [Notes de version pour la passerelle de gestion des données](data-factory-gateway-release-notes.md) | Notes de version pour la passerelle de gestion des données |
| 40 | [Transfert de données à partir d’un HDFS en local à l’aide d’Azure Data Factory](data-factory-hdfs-connector.md) | Découvrez comment transférer des données à partir d’un HDFS en local à l’aide d’Azure Data Factory. |
| 41 | [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) | Découvrez comment utiliser la nouvelle application de surveillance et gestion pour surveiller et gérer les fabriques de données et les pipelines Azure. |
| 42 | [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) | Mettez en place une passerelle de données pour déplacer vos données entre un emplacement local et le cloud. Utilisez la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 43 | [Déplacer des données depuis une source OData à l’aide d’Azure Data Factory](data-factory-odata-connector.md) | Apprenez à déplacer des données à partir de sources OData à l’aide d’Azure Data Factory. |
| 44 | [Transfert de données à partir de magasins de données ODBC à l’aide d’Azure Data Factory](data-factory-odbc-connector.md) | Apprenez à transférer des données à partir de magasins de données ODBC à l’aide d’Azure Data Factory. |
| 45 | [Déplacer des données depuis DB2 à l’aide d’Azure Data Factory](data-factory-onprem-db2-connector.md) | Découvrez comment déplacer des données depuis une base de données DB2 à l’aide d’Azure Data Factory. |
| 46 | [Déplacer des données vers et depuis un système de fichiers local à l’aide d’Azure Data Factory](data-factory-onprem-file-system-connector.md) | Apprenez à déplacer des données vers et depuis un système de fichiers local à l’aide d’Azure Data Factory. |
| 47 | [Déplacer des données depuis MySQL à l’aide d’Azure Data Factory](data-factory-onprem-mysql-connector.md) | Découvrez comment déplacer des données depuis une base de données MySQL à l’aide d’Azure Data Factory. |
| 48 | [Déplacer des données vers/depuis Oracle en local à l’aide d’Azure Data Factory](data-factory-onprem-oracle-connector.md) | Découvrez comment déplacer des données vers et depuis une base de données Oracle locale à l’aide d’Azure Data Factory. |
| 49 | [Déplacer des données depuis PostgreSQL à l’aide d’Azure Data Factory](data-factory-onprem-postgresql-connector.md) | Découvrez comment déplacer des données depuis une base de données PostgreSQL à l’aide d’Azure Data Factory. |
| 50 | [Déplacer des données depuis Sybase à l’aide d’Azure Data Factory](data-factory-onprem-sybase-connector.md) | Découvrez comment déplacer des données depuis une base de données Sybase à l’aide d’Azure Data Factory. |
| 51 | [Déplacer des données depuis Teradata à l’aide d’Azure Data Factory](data-factory-onprem-teradata-connector.md) | En savoir plus sur le connecteur Teradata pour le service Data Factory qui vous permet de déplacer des données depuis une base de données Teradata |
| 52 | [Déplacement des données vers et depuis SQL Server local ou sur IaaS (Machine virtuelle Azure) à l’aide d’Azure Data Factory](data-factory-sqlserver-connector.md) | Apprendre à déplacer des données vers/depuis une base de données SQL Server locale ou une machine virtuelle Azure à l’aide d’Azure Data Factory. |
| 53 | [Déplacer des données depuis une source de table web à l’aide d’Azure Data Factory](data-factory-web-table-connector.md) | Découvrez comment transférer des données à partir d’une table locale dans une page web à l’aide d’Azure Data Factory. |



## <a name="data-transformation"></a>Transformation des données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 54 | [Créer des pipelines prédictifs à l’aide des activités Azure Machine Learning](data-factory-azure-ml-batch-execution-activity.md) | Décrit comment créer des pipelines prédictifs à l’aide d’Azure Data Factory et d’Azure Machine Learning. |
| 55 | [Services liés de calcul](data-factory-compute-linked-services.md) | En savoir plus sur environnements de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory pour transformer/traiter les données. |
| 56 | [Traiter des jeux de données volumineux à l’aide de Data Factory et Batch](data-factory-data-processing-using-batch.md) | Décrit comment traiter de grandes quantités de données dans un pipeline Azure Data Factory en utilisant une capacité de traitement parallèle d’Azure Batch. |
| 57 | [Transformer des données dans Azure Data Factory](data-factory-data-transformation-activities.md) | Découvrez comment transformer des données ou traiter les données dans Azure Data Factory à l’aide de Hadoop, Machine Learning ou Azure Data Lake Analytics. |
| 58 | [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md) | Découvrez comment vous pouvez utiliser l’activité de diffusion en continu Hadoop dans une fabrique de données Azure pour exécuter des programmes de diffusion en continu Hadoop sur votre cluster HDInsight propre/à la demande. |
| 59 | [Activité Hive](data-factory-hive-activity.md) | Découvrez comment vous pouvez utiliser l'activité Hive d’une fabrique de données Azure pour exécuter des requêtes Hive sur un cluster HDInsight à la demande/ou votre propre cluster. |
| 60 | [Appeler des programmes MapReduce à partir de Data Factory](data-factory-map-reduce.md) | Learn how to process data by running MapReduce programs on an Azure HDInsight cluster from an Azure data factory. |
| 61 | [Activité pig](data-factory-pig-activity.md) | Découvrez comment utiliser l'activité pig d’une fabrique de données Azure pour exécuter des requêtes pig sur un cluster HDInsight à la demande/ou votre propre cluster. |
| 62 | [Appeler des programmes Spark à partir de Data Factory](data-factory-spark.md) | Apprenez à appeler des programmes Spark à partir d'une fabrique de données Azure avec l'activité MapReduce. |
| 63 | [Activité de procédure stockée SQL Server](data-factory-stored-proc-activity.md) | Découvrez comment utiliser l'activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou un entrepôt Azure SQL Data Warehouse à partir d'un pipeline Data Factory. |
| 64 | [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md) | Découvrez comment créer des activités personnalisées et les utiliser dans un pipeline Azure Data Factory. |
| 65 | [Exécution d'un script U-SQL sur Azure Data Lake Analytics à partir de la fabrique d'Azure Data Factory](data-factory-usql-activity.md) | Découvrez comment traiter les données en exécutant des scripts U-SQL sur le service de calcul Azure Data Lake Analytics. |



## <a name="samples"></a>Exemples

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 66 | [Azure Data Factory - Exemples](data-factory-samples.md) | Fournit des détails sur les exemples fournis avec le service Azure Data Factory. |



## <a name="use-cases"></a>Cas d'utilisation

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 67 | [Études de cas clients](data-factory-customer-case-studies.md) | Découvrez comment certains de nos clients utilisent Azure Data Factory. |
| 68 | [Cas d’utilisation - Profilage des utilisateurs](data-factory-customer-profiling-usecase.md) | Découvrez comment Azure Data Factory permet de créer un workflow piloté par les données (pipeline) pour définir le profil des clients de leurs jeux. |
| 69 | [Cas d’utilisation - Recommandations de produits](data-factory-product-reco-usecase.md) | Découvrez un cas d'utilisation implémenté à l'aide d’Azure Data Factory et d'autres services. |



## <a name="monitor-and-manage"></a>Surveillance et gestion

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 70 | [Surveillance et gestion des pipelines d’Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Découvrez comment utiliser le portail Azure et Azure PowerShell pour analyser et gérer les fabriques de données et les pipelines Azure que vous avez créés. |



## <a name="sdk"></a>Foundation

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 71 | [Azure Data Factory : Journal des modifications de l’API .NET](data-factory-api-change-log.md) | Décrit les dernières modifications, les ajouts de fonctionnalités, les correctifs de bogues, etc., d’une version spécifique de l'API .NET de Azure Data Factory. |
| 72 | [Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically.md) | Découvrez comment créer, analyser et gérer par programmation des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory. |
| 73 | [Référence du développeur Azure Data Factory](data-factory-sdks.md) | Découvrez les différentes façons de créer, surveiller et gérer des fabriques de données Azure. |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 74 | [Azure Data Factory - Forum aux Questions](data-factory-faq.md) | Forum aux Questions sur Azure Data Factory. |
| 75 | [Azure Data Factory - Variables système et fonctions](data-factory-functions-variables.md) | Fournit la liste des variables système et fonctions Azure Data Factory |
| 76 | [Azure Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) | Décrit les règles d'affectation de noms pour les entités Data Factory. |
| 77 | [Résolution des problèmes liés à Data Factory](data-factory-troubleshoot.md) | Découvrez comment résoudre les problèmes liés à l'utilisation de Azure Data Factory. |




<!--HONumber=Oct16_HO2-->


