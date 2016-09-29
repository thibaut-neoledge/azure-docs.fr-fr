<properties
	pageTitle="Toutes les rubriques pour le service Data Factory | Microsoft Azure"
	description="Tableau de toutes les rubriques du service Azure nommé data-factory qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>


# Toutes les rubriques pour le service Azure Data Factory

Cette rubrique répertorie toutes les rubriques qui s'appliquent directement au service Azure **Data Factory**. Pour trouver les rubriques qui vous intéressent, vous pouvez rechercher des mots clés sur cette page web à l'aide de la combinaison de touches **Ctrl+F**.




## Nouveau

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 1 | [Créer votre première fabrique de données Azure en utilisant l’API REST Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l’aide de l’API REST Data Factory. |
| 2 | [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API REST. |
| 3 | [Assistant Data Factory Copy](data-factory-copy-wizard.md) | Découvrez comment utiliser l’Assistant Data Factory Copy pour copier des données à partir de sources de données prises en charge dans des récepteurs. |
| 4 | [Passerelle de gestion de données](data-factory-data-management-gateway.md) | Mettez en place une passerelle de données pour déplacer vos données entre un emplacement local et le cloud. Utilisez la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 5 | [Déplacer des données depuis une base de données Cassandra locale à l’aide d’Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Découvrez comment déplacer des données depuis une base de données Cassandra locale à l’aide d’Azure Data Factory. |
| 6 | [Déplacer des données depuis MongoDB à l’aide d’Azure Data Factory](data-factory-on-premises-mongodb-connector.md) | Découvrez comment déplacer des données depuis une base de données MongoDB à l’aide d’Azure Data Factory. |
| 7 | [Déplacer des données depuis Salesforce à l’aide d’Azure Data Factory](data-factory-salesforce-connector.md) | Découvrez comment déplacer des données depuis Salesforce à l’aide d’Azure Data Factory. |


## Articles mis à jour

Cette section répertorie les articles qui ont été mis à jour récemment et dont la mise à jour a été volumineuse ou importante. Pour chaque article mis à jour, un extrait de code approximatif du texte markdown ajouté s’affiche. Les articles ont été mis à jour dans la plage de dates allant du **2016-07-26** au **2016-08-21**.

| &nbsp; | Article | Texte mis à jour, extrait de code |
| --: | :-- | :-- |
| 8 | [Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically.md) | **Connexion sans boîte de dialogue contextuelle** L’exemple de code ci-dessus lance une boîte de dialogue dans laquelle vous pouvez entrer des informations d’identification Azure. Si vous avez besoin de vous connecter par programme sans l’aide d’une boîte de dialogue, consultez Authentification d'un principal du service à l'aide d'Azure Resource Manager (ressource-groupe-authentification-service-principal.md authenticate-service-principal-with-certificate---powershell). **Exemple** Créez la méthode GetAuthorizationHeaderNoPopup comme illustré ci-dessous : public static string GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri(«https://login.windows.net»), ConfigurationManager.AppSettings «ActiveDirectoryTenantId» ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings «AdfClientId» , ConfigurationManager.AppSettings «AdfClientSecret» ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings «WindowsManagementUri» , credential).Result; |
| 9 | [Déplacer des données à l’aide de l’activité de copie](data-factory-data-movement-activities.md) | **Formats de fichier pris en charge** L’activité de copie peut copier des fichiers tels quels entre deux banques de données basées sur des fichiers comme un objet blob Azure, un système de fichiers et le système de fichiers DFS Hadoop. Pour ce faire, vous pouvez ignorer la section format (data-factory-create-datasets.md) dans les définitions des jeux de données d’entrée et de sortie, et les données sont copiées efficacement sans sérialisation/désérialisation. L’activité de copie permet également de lire et d’écrire dans les fichiers aux formats spécifiés : texte, Avro, ORC et JSON. Voici des exemples d’activités de copie que vous pouvez effectuer : / copier des données au format texte (CSV) depuis Azure Blob et les écrire dans Azure SQL / copier des fichiers au format texte (CSV) provenant d’un système de fichiers local et les écrire dans des objets blob Azure au format Avro ; / copier des données dans une base de données Azure SQL et les écrire sur un système de fichiers HDFS local au format ORC** .a name="global"../a.Déplacement de données disponible globalement** Le service qui propulse l’activité de copie est disponible globalement dans les régions et zones suivantes, même si Azure Data Factory n’est disponible que dans l’Ouest et l’Est des États-Unis ainsi que dans les régions d’Europe du Nord.|
| 10 | [Déplacer des données depuis une source OData à l’aide d’Azure Data Factory](data-factory-odata-connector.md) | **Avec l’authentification Windows accédant à la source OData locale** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## Didacticiels

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 11 | [Didacticiel : Générer votre premier pipeline pour traiter les données à l’aide du cluster Hadoop](data-factory-build-your-first-pipeline.md) | Ce didacticiel Azure Data Factory vous montre comment créer et planifier une fabrique de données qui traite les données à l’aide du script Hive sur un cluster Hadoop. |
| 12 | [Didacticiel : concevoir votre première fabrique de données Azure à l’aide du modèle Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory en utilisant un modèle Azure Resource Manager. |
| 13\. | [Créer votre première fabrique de données Azure à l’aide du portail Azure et de Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md) | Dans ce didacticiel, vous créez un exemple de pipeline Azure Data Factory à l’aide de Data Factory Editor dans le portail Azure. |
| 14 | [Créer votre première fabrique de données Azure à l’aide d’Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l’aide d’Azure PowerShell. |
| 15 | [Créer votre première fabrique de données Azure à l’aide de Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory avec Visual Studio. |
| 16 | [Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Data Factory Editor](data-factory-copy-activity-tutorial-using-azure-portal.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de Data Factory Editor dans le portail Azure. |
| 17 | [Didacticiel : Créer un pipeline avec l'activité de copie à l'aide d'Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide d’Azure PowerShell. |
| 18 | [Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de Visual Studio. |
| 19 | [Copie de données Blob Storage vers une base de données SQL à l’aide de Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Ce didacticiel vous montre comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données depuis Blob Storage vers une base de données SQL Azure. |
| 20 | [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) | Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie, à l’aide de l’Assistant de copie et de Data Factory. |



## Déplacement des données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 21 | [Déplacer des données vers et depuis un objet Blob Azure à l’aide d’Azure Data Factory](data-factory-azure-blob-connector.md) | Découvrez comment copier des données d’objets blob dans Azure Data Factory. Les exemples suivants indiquent comment copier des données vers et depuis Azure Blob Storage et Base de données SQL Azure. |
| 22 | [Déplacer des données vers et depuis Azure Data Lake Store à l’aide d’Azure Data Factory](data-factory-azure-datalake-connector.md) | Découvrez comment déplacer des données depuis et vers le stockage Azure Data Lake Store à l’aide d’Azure Data Factory. |
| 23 | [Déplacer des données vers et depuis DocumentDB à l’aide d’Azure Data Factory](data-factory-azure-documentdb-connector.md) | Découvrez comment déplacer des données depuis et vers la collection Azure DocumentDB à l’aide d’Azure Data Factory. |
| 24 | [Déplacer des données vers et depuis Base de données SQL Azure à l’aide d’Azure Data Factory](data-factory-azure-sql-connector.md) | Découvrez comment déplacer des données depuis et vers Azure SQL Database à l’aide d’Azure Data Factory. |
| 25 | [Déplacer des données vers et depuis Azure SQL Data Warehouse à l’aide d’Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) | Découvrez comment déplacer des données depuis et vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory. |
| 26 | [Déplacer des données vers et depuis Azure Table à l’aide d’Azure Data Factory](data-factory-azure-table-connector.md) | Découvrez comment déplacer des données depuis et vers le stockage Azure Table à l’aide d’Azure Data Factory. |
| 27 | [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) | En savoir plus sur les facteurs clés ayant une incidence sur les performances du déplacement de données dans Azure Data Factory via l’activité de copie. |
| 28 | [Déplacer des données à l’aide de l’activité de copie](data-factory-data-movement-activities.md) | Apprenez-en plus sur le déplacement des données dans les pipelines Data Factory : migration de données entre des magasins de cloud, entre des emplacements locaux et le cloud. Utilisez l’activité de copie. |
| 29 | [Notes de version pour la passerelle de gestion des données](data-factory-gateway-release-notes.md) | Notes de version pour la passerelle de gestion des données |
| 30 | [Transfert de données à partir d’un HDFS en local à l’aide d’Azure Data Factory](data-factory-hdfs-connector.md) | Découvrez comment transférer des données à partir d’un HDFS en local à l’aide d’Azure Data Factory. |
| 31 | [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) | Découvrez comment utiliser la nouvelle application de surveillance et gestion pour surveiller et gérer les fabriques de données et les pipelines Azure. |
| 32 | [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) | Mettez en place une passerelle de données pour déplacer vos données entre un emplacement local et le cloud. Utilisez la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 33 | [Déplacer des données depuis une source OData à l’aide d’Azure Data Factory](data-factory-odata-connector.md) | Apprenez à déplacer des données à partir de sources OData à l’aide d’Azure Data Factory. |
| 34 | [Transfert de données à partir de magasins de données ODBC à l’aide d’Azure Data Factory](data-factory-odbc-connector.md) | Apprenez à transférer des données à partir de magasins de données ODBC à l’aide d’Azure Data Factory. |
| 35 | [Déplacer des données depuis DB2 à l’aide d’Azure Data Factory](data-factory-onprem-db2-connector.md) | Découvrez comment déplacer des données depuis une base de données DB2 à l’aide d’Azure Data Factory. |
| 36 | [Déplacement de données vers et depuis le système de fichiers local à l’aide d’Azure Data Factory](data-factory-onprem-file-system-connector.md) | Apprenez à déplacer des données vers et depuis le système de fichiers local à l’aide d’Azure Data Factory. |
| 37 | [Déplacer des données depuis MySQL à l’aide d’Azure Data Factory](data-factory-onprem-mysql-connector.md) | Découvrez comment déplacer des données depuis une base de données MySQL à l’aide d’Azure Data Factory. |
| 38 | [Déplacer des données vers/depuis Oracle en local à l’aide d’Azure Data Factory](data-factory-onprem-oracle-connector.md) | Découvrez comment déplacer des données vers et depuis une base de données Oracle locale à l’aide d’Azure Data Factory. |
| 39 | [Déplacer des données depuis PostgreSQL à l’aide d’Azure Data Factory](data-factory-onprem-postgresql-connector.md) | Découvrez comment déplacer des données depuis une base de données PostgreSQL à l’aide d’Azure Data Factory. |
| 40 | [Déplacer des données depuis Sybase à l’aide d’Azure Data Factory](data-factory-onprem-sybase-connector.md) | Découvrez comment déplacer des données depuis une base de données Sybase à l’aide d’Azure Data Factory. |
| 41 | [Déplacer des données depuis Teradata à l’aide d’Azure Data Factory](data-factory-onprem-teradata-connector.md) | En savoir plus sur le connecteur Teradata pour le service Data Factory qui vous permet de déplacer des données depuis une base de données Teradata |
| 42 | [Déplacement des données vers et depuis SQL Server local ou sur IaaS (Machine virtuelle Azure) à l’aide d’Azure Data Factory](data-factory-sqlserver-connector.md) | Apprendre à déplacer des données vers/depuis une base de données SQL Server locale ou une machine virtuelle Azure à l’aide d’Azure Data Factory. |
| 43 | [Déplacer des données depuis une source de table web à l’aide d’Azure Data Factory](data-factory-web-table-connector.md) | Découvrez comment transférer des données à partir d’une table locale dans une page web à l’aide d’Azure Data Factory. |



## Transformation des données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 44 | [Créer des pipelines prédictifs à l’aide des activités Azure Machine Learning](data-factory-azure-ml-batch-execution-activity.md) | Décrit comment créer des pipelines prédictifs à l’aide d’Azure Data Factory et d’Azure Machine Learning. |
| 45 | [Services liés de calcul](data-factory-compute-linked-services.md) | En savoir plus sur environnements de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory pour transformer/traiter les données. |
| 46 | [Traiter des jeux de données volumineux à l’aide de Data Factory et Batch](data-factory-data-processing-using-batch.md) | Décrit comment traiter de grandes quantités de données dans un pipeline Azure Data Factory en utilisant une capacité de traitement parallèle d’Azure Batch. |
| 47 | [En savoir plus sur la transformation et l’analyse des données dans Azure Data Factory](data-factory-data-transformation-activities.md) | Apprenez-en plus sur la transformation de données dans Azure Data Factory. Transformez et traitez des données dans un cluster Azure HDInsight ou un batch Azure. |
| 48 | [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md) | Découvrez comment vous pouvez utiliser l’activité de diffusion en continu Hadoop dans une fabrique de données Azure pour exécuter des programmes de diffusion en continu Hadoop sur votre cluster HDInsight propre/à la demande. |
| 49 | [Activité Hive](data-factory-hive-activity.md) | Découvrez comment vous pouvez utiliser l'activité Hive d’une fabrique de données Azure pour exécuter des requêtes Hive sur un cluster HDInsight à la demande/ou votre propre cluster. |
| 50 | [Appeler des programmes MapReduce à partir de Data Factory](data-factory-map-reduce.md) | Learn how to process data by running MapReduce programs on an Azure HDInsight cluster from an Azure data factory. |
| 51 | [Activité pig](data-factory-pig-activity.md) | Découvrez comment utiliser l'activité pig d’une fabrique de données Azure pour exécuter des requêtes pig sur un cluster HDInsight à la demande/ou votre propre cluster. |
| 52 | [Appeler des programmes Spark à partir de Data Factory](data-factory-spark.md) | Apprenez à appeler des programmes Spark à partir d'une fabrique de données Azure avec l'activité MapReduce. |
| 53 | [Activité de procédure stockée SQL Server](data-factory-stored-proc-activity.md) | Découvrez comment utiliser l'activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou un entrepôt Azure SQL Data Warehouse à partir d'un pipeline Data Factory. |
| 54 | [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md) | Découvrez comment créer des activités personnalisées et les utiliser dans un pipeline Azure Data Factory. |
| 55 | [Exécution d'un script U-SQL sur Azure Data Lake Analytics à partir de la fabrique d'Azure Data Factory](data-factory-usql-activity.md) | Découvrez comment traiter les données en exécutant des scripts U-SQL sur le service de calcul Azure Data Lake Analytics. |



## Exemples

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 56 | [Azure Data Factory - Exemples](data-factory-samples.md) | Fournit des détails sur les exemples fournis avec le service Azure Data Factory. |



## Cas d'utilisation

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 57 | [Études de cas clients](data-factory-customer-case-studies.md) | Découvrez comment certains de nos clients utilisent Azure Data Factory. |
| 58 | [Cas d’utilisation - Profilage des utilisateurs](data-factory-customer-profiling-usecase.md) | Découvrez comment Azure Data Factory permet de créer un workflow piloté par les données (pipeline) pour définir le profil des clients de leurs jeux. |
| 59 | [Cas d’utilisation - Recommandations de produits](data-factory-product-reco-usecase.md) | Découvrez un cas d'utilisation implémenté à l'aide d’Azure Data Factory et d'autres services. |



## Surveillance et gestion

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 60 | [Surveillance et gestion des pipelines d’Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Découvrez comment utiliser le portail Azure et Azure PowerShell pour analyser et gérer les fabriques de données et les pipelines Azure que vous avez créés. |



## Foundation

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 61 | [Azure Data Factory : Journal des modifications de l’API .NET](data-factory-api-change-log.md) | Décrit les dernières modifications, les ajouts de fonctionnalités, les correctifs de bogues, etc., d’une version spécifique de l'API .NET de Azure Data Factory. |
| 62 | [Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically.md) | Découvrez comment créer, analyser et gérer par programmation des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory. |
| 63 | [Référence du développeur Azure Data Factory](data-factory-sdks.md) | Découvrez les différentes façons de créer, surveiller et gérer des fabriques de données Azure. |



## Divers

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 64 | [Azure Data Factory - Forum aux Questions](data-factory-faq.md) | Forum aux Questions sur Azure Data Factory. |
| 65 | [Azure Data Factory - Variables système et fonctions](data-factory-functions-variables.md) | Fournit la liste des variables système et fonctions Azure Data Factory |
| 66 | [Azure Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) | Décrit les règles d'affectation de noms pour les entités Data Factory. |
| 67 | [Résolution des problèmes liés à Data Factory](data-factory-troubleshoot.md) | Découvrez comment résoudre les problèmes liés à l'utilisation de Azure Data Factory. |

<!---HONumber=AcomDC_0914_2016-->