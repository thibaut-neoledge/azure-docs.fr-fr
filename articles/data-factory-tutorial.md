<properties 
	pageTitle="Déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory" 
	description="Ce didacticiel avancé décrit un scénario proche de la réalité et l'implémente à l'aide du service Azure Data Factory." 
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
	ms.date="1/28/2015" 
	ms.author="spelluru"/>

# Didacticiel : Déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory
Cet article fournit une procédure pas à pas pour un scénario de traitement de journaux à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en informations. 

## Scénario
Contoso est une société de jeu qui crée des jeux pour plusieurs plateformes : consoles de jeu, périphériques de poche et ordinateurs personnels (PC). Chacun de ces jeux produit une très grande quantité de journaux. L'objectif de Contoso est de collecter et d'analyser les journaux générés par ces jeux pour obtenir des informations sur l'utilisation, identifier les opportunités de vente incitative et de vente croisée, développer de nouvelles fonctionnalités intéressantes, etc. pour optimiser vos affaires et fournir une meilleure expérience aux clients.
 
Dans cette procédure pas à pas, nous allons collecter des exemples de journaux, nous allons les traiter et les enrichir avec des données de référence et transformer les données pour évaluer l'efficacité d'une campagne marketing lancée dernièrement par Contoso.

## Préparation pour le didacticiel
1.	Lisez la page [Présentation d'Azure Data Factory][adfintroduction] pour obtenir une vue d'ensemble d'Azure Data Factory et comprendre les concepts de haut niveau.
2.	Pour suivre ce didacticiel, vous devez disposer d'un abonnement Azure. Pour plus d'informations sur l'obtention d'un abonnement, consultez les pages traitant des [formules d'abonnement] [azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].
3.	Vous devez télécharger et installer [Azure PowerShell][download-azure-powershell] sur votre ordinateur. 
2.	**(recommandé)** Consultez et pratiquez le didacticiel de l'article [Prise en main d'Azure Data Factory][adfgetstarted], car ce didacticiel simple vous permettra de vous familiariser avec le portail et les cmdlets.
3.	**(recommandé)** Consultez et pratiquez la procédure pas à pas de l'article [Utilisation de Pig et Hive avec Azure Data Factory][usepigandhive] pour manier la création d'un pipeline servant à déplacer les données d'une source de données locale vers un magasin d'objets blob Azure.
4.	Téléchargez les fichiers [ADFWalkthrough][adfwalkthrough-download] dans le dossier **C:\ADFWalkthrough** **sans modifier la structure du dossier** :
	- **Pipelines :** inclut des  fichiers JSON contenant la définition des pipelines.
	- **Tables :** inclut des  fichiers JSON contenant la définition des tables.
	- **LinkedServices :** inclut des fichiers JSON contenant la définition de votre cluster de stockage et de calcul (HDInsight). 
	- **Scripts :** inclut des scripts Hive et Pig qui sont utilisés pour traiter les données et appelés à partir de pipelines.
	- **SampleData :** inclut des exemples de données pour cette procédure pas à pas.
	- **OnPremises :** inclut des fichiers JSON et un script, utilisés pour illustrer l'accès à vos données locales.
	- **AzureEnvironmentSetup.ps1 :** il s'agit du script PowerShell pour configurer l'environnement Azure.
	- **uploadSampleDataAndScripts.ps1 :** ce script télécharge les exemples de données et les scripts dans Azure.
5. Assurez-vous de disposer des ressources Azure suivantes :			
	- Compte Azure Storage.
	- Base de données SQL Azure
	- Azure HDInsight Cluster version 3.1 ou supérieure.	
7. Une fois les ressources Azure créées, assurez-vous de disposer des informations nécessaires pour se connecter à chacune de ces ressources.
 	- **Compte Azure Storage** : nom de compte et clé de compte.  
	- **Base de données SQL Azure** : serveur, base de données, nom d'utilisateur et mot de passe.
	- **Azure HDInsight Cluster** : nom du cluster HDInsight, nom d'utilisateur, mot de passe, nom de compte et clé de compte pour le stockage Azure associé à ce cluster.  
 8. Lancez **Azure PowerShell**, accédez à **C:\ADFWalkthrough** et exécutez le script de préparation**\AzureEnvironmentSetup.ps1**.
 
		Avant d'exécuter le script, vous devez disposer de votre nom d'abonnement et du compte Microsoft associé à l'abonnement.

		Vous devez configurer l'environnement Azure à l'aide de ce script à chaque fois que vous lancez   Azure PowerShell. La configuration n'est pas conservée d'une session à une autre et doit être exécutée séparément, même si vous utilisez plusieurs sessions simultanément.

		Vous pouvez également utiliser la cmdlet **Add-AzureAccount** pour vous connecter à Azure et sélectionner un abonnement (si vous en avez plusieurs) à l'aide de la cmdlet **Select-AzureSubscription**.
	

## Vue d'ensemble
Le flux de travail de bout en bout est représenté ci-dessous :
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. Le pipeline **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis un stockage d'objets blob (RawGameEventsTable) et crée des partitions basées sur l'année, le mois et le jour (PartitionedGameEventsTable).
2. Le pipeline **EnrichGameLogsPipeline** joint les événements de jeu partitionnés (table PartitionedGameEvents, qui est une sortie de PartitionGameLogsPipeline) avec un code géographique (RefGetoCodeDictionaryTable) et enrichit les données en mappant une adresse IP à la géolocalisation correspondante (EnrichedGameEventsTable).
3. Le pipeline **AnalyzeMarketingCampaignPipeline** exploite les données enrichies (EnrichedGameEventTable produite par EnrichGameLogsPipeline) et les traite avec les données de publicité (RefMarketingCampaignnTable) pour créer la sortie finale d'efficacité de la campagne marketing, qui est copiée dans la base de données SQL Azure (MarketingCampainEffectivensessSQLTable) et un stockage d'objets blob Azure (MarketingCampaignEffectivenessBlobTable) pour analyse.
    
## Procédure pas à pas : création, déploiement et surveillance des flux de travail
1. [Étape 1 : téléchargez les exemples de données et les scripts](#MainStep1). Dans cette étape, vous allez télécharger tous les exemples de données (y compris l'ensemble des journaux et des données de référence) et les scripts Hive/Pig qui seront exécutés par les flux de travail. Les scripts que vous exécutez créent également une base de données SQL Azure (nommée MarketingCampaigns), des tables, des types définis par l'utilisateur et des procédures stockées.
2. [Étape 2 : créez une fabrique de données Azure](#MainStep2). Dans cette étape, vous allez créer une fabrique de données Azure nommée LogProcessingFactory.
3. [Étape 3 : créez des services liés](#MainStep3). Dans cette étape, vous allez créer les services liés suivants : 
	
	- 	**StorageLinkedService**. Lie l'emplacement de stockage Azure qui contient des événements de jeu bruts, des événements de jeu partitionnés, des événements de jeu enrichis, des informations d'efficacité de la campagne marketing, des données géocodées de référence et des données de campagne marketing de référence dans LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Lie une base de données SQL Azure qui contient des informations d'efficacité de la campagne marketing. 
	- 	**HDInsightStorageLinkedService**. Lie un stockage d'objets blob Azure qui est associé au cluster HDInsight qui désigne HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Lie un cluster Azure HDInsight à LogProcessingFactory. Ce cluster permet d'effectuer un traitement pig/hive sur les données. 
 		
4. [Étape 4 : créez des tables](#MainStep4). Dans cette étape, vous allez créer les tables suivantes :  	
	
	- **RawGameEventsTable**. Cette table spécifie l'emplacement des données d'événement de jeu brutes dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Cette table spécifie l'emplacement des données d'événement de jeu partitionnées dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Cette table spécifie l'emplacement des données géocodées de référence dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Cette table spécifie l'emplacement des données de campagne marketing de référence dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Cette table spécifie l'emplacement des données d'événement de jeu enrichies dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Cette table spécifie la table SQL (MarketingCampaignEffectiveness) de la base de données SQL Azure définie par AzureSqlLinkedService qui contient les données d'efficacité de la campagne marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Cette table spécifie l'emplacement des données d'efficacité de la campagne marketing dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Étape 5 : créez et planifiez des pipelines](#MainStep5). Dans cette étape, vous allez créer les pipelines suivants :
	- **PartitionGameLogsPipeline**. Ce pipeline lit les événements de jeu bruts depuis un stockage d'objets blob (RawGameEventsTable) et crée des partitions basées sur l'année, le mois et le jour (PartitionedGameEventsTable). 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Ce pipeline joint les événements de jeu partitionnés (table PartitionedGameEvents, qui est une sortie de PartitionGameLogsPipeline) avec un code géographique (RefGetoCodeDictionaryTable) et enrichit les données en mappant une adresse IP à la géolocalisation correspondante (EnrichedGameEventsTable). 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Ce pipeline exploite les données enrichies (EnrichedGameEventTable produite par EnrichGameLogsPipeline) et les traite avec les données de publicité (RefMarketingCampaignnTable) pour créer la sortie finale d'efficacité de la campagne marketing, qui est copiée dans la base de données SQL Azure (MarketingCampainEffectivensessSQLTable) et un stockage d'objets blob Azure (MarketingCampaignEffectivenessBlobTable) pour analyse.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Étape 6 : surveiller les pipelines et les tranches de données](#MainStep6). Dans cette étape, vous allez surveiller les pipelines, les tables et les tranches de données à l'aide du portail Azure.

### <a name="MainStep1"></a>Étape 1 : téléchargez les exemples de données et les scripts
Dans cette étape, vous allez télécharger tous les exemples de données (y compris l'ensemble des journaux et des données de référence) et les scripts Hive/Pig qui seront appelés par les flux de travail. Les scripts que vous exécutez créent également une base de données SQL Azure nommée **MarketingCampaigns**, des tables, des types définis par l'utilisateur et des procédures stockées. 

Les tables, les types définis par l'utilisateur et les procédures stockées sont utilisés lors du déplacement des résultats de l'efficacité de la campagne marketing depuis le stockage d'objets blob Azure vers la base de données SQL Azure.

1. Ouvrez **uploadSampleDataAndScripts.ps1** dans le dossier **C:\ADFWalkthrough** (ou dans celui qui contient les fichiers extraits) dans votre éditeur favori, remplacez l'élément en surbrillance par vos informations de cluster et enregistrez le fichier.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] Pour ce script, l'utilitaire sqlcmd doit être installé sur votre ordinateur. Si SQL Server est installé, l'utilitaire l'est également. Sinon, [téléchargez][sqlcmd-install] et installez l'utilitaire. 
	> Vous pouvez également utiliser les fichiers dans le dossier : C:\ADFWalkthrough\Scripts pour télécharger les scripts pig/hive et des exemples de fichiers dans le conteneur adfwalkthrough dans le stockage d'objets blob, et créer la table MarketingCampaignEffectiveness dans la base de données SQL Azure MarketingCampaigns.   
2. Vérifiez que votre ordinateur local est autorisé à accéder à la base de données SQL Azure. Pour activer l'accès, utilisez le **portail de gestion Azure** ou **sp_set_firewall_rule** sur la base de données master pour créer une règle de pare-feu pour l'adresse IP de votre ordinateur. Cela peut prendre jusqu'à cinq minutes pour que cette modification prenne effet. Consultez la rubrique [Définition des règles de pare-feu pour Azure SQL][azure-sql-firewall].
3. Lancez **Azure PowerShell**. 
4. Naviguez jusqu'à l'emplacement où vous avez extrait les exemples (par exemple : **C:\ADFWalkthrough**)
5. Exécutez **uploadSampleDataAndScripts.ps1** 
6. Une fois que le script s'exécute correctement, vous verrez les éléments suivants :

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>Étape 2 : créez une fabrique de données Azure.
Dans cette étape, vous allez créer la fabrique de données Azure nommée **LogProcessingFactory**.

1.	Après la connexion à la [version préliminaire du portail Azure][azure-preview-portal], cliquez sur **NOUVEAU** dans l'angle inférieur gauche, puis cliquez sur **Fabrique de données** sur le panneau **Nouveau**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Si **Fabrique de données** ne s'affiche pas dans le panneau **Nouveau**, faites défiler vers le bas. 
	
5. Dans le panneau **Nouvelle fabrique de données**, entrez **LogProcessingFactory** comme **Nom**.

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Si vous n'avez pas encore créé de groupe de ressources Azure nommé **ADF**, procédez comme suit :
	1. Cliquez sur **NOM DU GROUPE DE RESSOURCES**, puis cliquez sur **Créer un groupe de ressources**.
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. Dans le panneau **Créer un groupe de ressources**, entrez **ADF** comme nom du groupe de ressources, puis cliquez sur **OK**.
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. Sélectionnez **ADF** comme **NOM DU GROUPE DE RESSOURCES**.  
8.	Dans le panneau **Nouvelle fabrique de données**, notez que l'option **Ajouter au tableau d'accueil** est sélectionnée par défaut. Cela ajoute un lien vers la fabrique de données sur le tableau d'accueil (ce que vous voyez lorsque vous vous connectez à la version préliminaire du portail Azure).

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer** pour créer la fabrique de données.
10.	Lorsque la fabrique de données est créée, vous voyez le panneau **FABRIQUE DE DONNÉES** intitulé **LogProcessingFactory**.

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	Si vous ne le voyez pas, effectuez l'une des opérations suivantes :

	- Cliquez sur **LogProcessingFactory** sur le **tableau d'accueil** (page d'accueil)
	- Cliquez sur **PARCOURIR**, à gauche, puis cliquez sur **Tout**, **Fabriques de données**, puis sur la fabrique de données.
 

 
### <a name="MainStep3"></a>Étape 3 : créez des services liés

Dans cette étape, vous allez créer les services liés suivants : StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService et HDInsightLinkedService.

1.	Dans le panneau **LogProcessingFactory**, cliquez sur la vignette **Services liés**.

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. Dans le panneau **Services liés**, cliquez sur **+ Magasin de données** dans la barre de commandes.	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. Dans le panneau **Nouveau magasin de données**, entrez **StorageLinkedService** comme **Nom**, cliquez sur **TYPE (paramètres requis)**, puis sélectionnez **Compte de stockage Azure**.

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. Dans le panneau **Nouveau magasin de données**, vous voyez deux nouveaux champs : **Nom de compte** et **Clé de compte**. Entrez le nom et la clé de votre **compte de stockage Azure**.

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	Vous pouvez obtenir le nom et la clé de votre compte de stockage Azure à partir du portail, comme indiqué ci-après :

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. Après avoir cliqué sur **OK** dans le panneau Nouveau magasin de données, vous devez voir **StorageLinkedService** dans la liste **MAGASINS DE DONNÉES** dans le panneau **Services liés**. Consultez le concentrateur **NOTIFICATIONS** (sur la gauche) pour voir les messages.

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Répétez les **étapes 2 à 5** pour créer un autre service lié nommé : **HDInsightStorageLinkedService**. Il s'agit du stockage utilisé par votre cluster HDInsight.
7. Confirmez que vous voyez **StorageLinkedService** et **HDInsightStorageLinkedService** dans la liste du panneau Services liés.
8. Dans le panneau **Services liés**, cliquez sur **Ajouter (+) Magasin de données** dans la barre de commandes.
9. Entrez le nom **AzureSqlLinkedService**.
10. Cliquez sur **TYPE (paramètres requis)**, sélectionnez **Base de données SQL Azure**.
11. Maintenant, vous devez voir les champs supplémentaires suivants dans le panneau **Nouveau magasin de données**. Entrez le nom du **serveur**de la base de données SQL Azure, le nom de la **base de données**, le **nom de l'utilisateur** et le **mot de passe**, puis cliquez sur **OK**.
	1. Entrez **MarketingCampaigns** comme nom de **base de données**. Il s'agit de la base de données SQL Azure créée par les scripts que vous avez exécutés à l'étape 1. Vous devez confirmer que cette base de données a été effectivement créée par les scripts (en cas d'erreurs).
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		Pour obtenir ces valeurs à partir du portail de gestion Azure : cliquez sur les chaînes de connexion Afficher la base de données SQL pour la base de données MarketingCampaigns

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. Vérifiez que vous voyez les trois magasins de données que vous avez créés : **StorageLinkedService**, **HDInsightStorageLinkedService** et **AzureSqlLinkedService**.
13. Vous devez créer un autre service lié, mais cette fois il s'agit d'un service de calcul, précisément du **cluster HDInsight Azure**. Le portail ne prend pas encore en charge la création d'un service de calcul lié. Par conséquent, vous devez utiliser Azure PowerShell pour ce faire. 
14. Passez à **Azure PowerShell** s'il est déjà ouvert ; sinon, lancez **Azure PowerShell**.
15. Passez en mode **AzureResourceManager**, car les cmdlets Azure Data Factory sont disponibles uniquement dans ce mode.

		Switch-AzureMode AzureResourceManager

16. Accédez au sous-dossier **LinkedServices** dans **C:\ADFWalkthrough** ou dans le dossier de l'emplacement où vous avez extrait les fichiers.
17. Ouvrez **HDInsightLinkedService.json** dans votre éditeur favori, remplacez l'élément en surbrillance par vos informations de cluster et enregistrez le fichier.

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. Utilisez la commande suivante pour définir la variable $df sur le nom de la fabrique de données.

		$df = "LogProcessingFactory"
19. Utilisez la cmdlet **New-AzureDataFactoryLinkedService** pour créer un service lié comme suit. Commencez par le compte de stockage :

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Si vous utilisez un nom différent pour ResourceGroupName, DataFactoryName ou LinkedService, indiquez-les dans la cmdlet ci-dessus. Vous devez également fournir le chemin d'accès complet du fichier JSON du service lié si le fichier est introuvable.
20. Vous devez voir les quatre services liés dans le panneau **Services liés**, comme illustré ci-après. Si le panneau Services liés n'est pas ouvert, cliquez sur Services liés dans la page **FABRIQUE DE DONNÉES** de **LogProcessingFactory**. L'actualisation du panneau des services liés peut prendre quelques secondes.

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>Étape 4 : créez des tables 
Dans cette étape, vous allez créer les tables suivantes : 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
L'image ci-dessus présente les pipelines sur la ligne du milieu et les tables sur les lignes supérieures et inférieures. 

Le portail Azure n'autorise pas encore la création de jeux de données/tables, vous devez donc utiliser Azure PowerShell pour créer des tables dans cette version.

#### Créer des tables

1.	Dans Azure PowerShell, accédez au dossier **Tables** (**C:\ADFWalkthrough\Tables**) depuis l'emplacement où vous avez extrait les exemples. 
2.	Utilisez la cmdlet **New-AzureDataFactoryTable** pour créer les tables comme suit pour **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	Si vous utilisez un nom différent pour ResourceGroupName et DataFactoryName, indiquez-les dans la cmdlet ci-dessus. En outre, indiquez le chemin d'accès complet du fichier JSON de la table si la cmdlet ne trouve pas le fichier.

3. Répétez l'étape précédente pour créer les tables suivantes :	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. Dans la **version préliminaire du portail Azure**, cliquez sur **Jeux de données** dans le panneau **FABRIQUES DE DONNÉES** pour **LogProcessingFactory** et vérifiez que vous voyez tous les jeux de données (les tables sont des jeux de données rectangulaires). 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	Vous pouvez également utiliser la commande suivante à partir d'Azure PowerShell :
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>Étape 5 : créer et planifier des pipelines
Dans cette étape, vous allez créer les pipelines suivants : PartitionGameLogsPipeline, EnrichGameLogsPipeline et AnalyzeMarketingCampaignPipeline.

1. Dans l'**Explorateur Windows**, accédez au sous-dossier **Pipelines** dans le dossier **C:\ADFWalkthrough** (ou à partir de l'emplacement où vous avez extrait les exemples).
2.	Ouvrez **PartitionGameLogsPipeline.json** dans votre éditeur favori, remplacez l'élément en surbrillance par vos informations de compte de stockage pour le compte de stockage de données et enregistrez le fichier.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Répétez l'étape pour créer les pipelines suivants :
	1. **EnrichGameLogsPipeline**.json (3 occurrences).
	2. **AnalyzeMarketingCampaignPipeline**.json (3 occurrences).

	**IMPORTANT :** Vérifiez que vous avez remplacé toutes les valeurs <storageaccountname> par le nom de votre compte de stockage. 
 
4.  Dans **Azure PowerShell**, accédez au sous-dossier **Pipelines** dans le dossier **C:\ADFWalkthrough** (ou à partir de l'emplacement où vous avez extrait les exemples).
5.  Utilisez la cmdlet **New-AzureDataFactoryPipeline** pour créer les pipelines comme suit pour **PartitionGameLogspeline**.json.	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	Si vous utilisez un nom différent pour ResourceGroupName, DataFactoryName ou le pipeline, indiquez-les dans la cmdlet ci-dessus. Fournissez également le chemin d'accès complet au fichier JSON de pipeline.
6. Répétez l'étape précédente pour créer les pipelines suivants :
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. Utilisez la cmdlet **Get-AzureDataFactoryPipeline** pour obtenir la liste des pipelines.
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. Une fois les pipelines créés, vous pouvez spécifier la durée de traitement des données. En spécifiant la période active pour un pipeline, vous définissez la durée de traitement des tranches de données en fonction des propriétés de disponibilité qui ont été définies pour chaque table ADF.

Pour spécifier la période active pour le pipeline, vous pouvez utiliser la cmdlet Set-AzureDataFactoryPipelineActivePeriod. Dans cette procédure pas à pas, les exemples de données concernent la période du 1er mai au 5 mai. Utilisez 2014-05-01 comme valeur StartDateTime. La valeur EndDateTime est facultative.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. Confirmez pour définir la période active pour le pipeline.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Répétez les deux étapes précédentes pour définir une période active pour les pipelines suivants.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. Dans la **version préliminaire du portail Azure**, cliquez sur la vignette **Pipelines** (pas sur le nom des pipelines) dans le panneau **FABRIQUE DE DONNÉES** pour **LogProcessingFactory**. Vous devez voir les pipelines que vous avez créés.

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. Dans le panneau **FABRIQUE DE DONNÉES** pour **LogProcessingFactory**, cliquez sur **Diagramme**.

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. Vous pouvez réorganiser le diagramme affiché, comme dans le diagramme ci-après qui affiche les entrées directes en haut et les sorties en bas. Vous pouvez voir que la sortie de **PartitionGameLogsPipeline** est transmise comme entrée à EnrichGameLogsPipeline et que la sortie de **EnrichGameLogsPipeline** est transmise à **AnalyzeMarketingCampaignPipeline**. Double-cliquez sur un titre pour afficher des détails sur l'objet représenté par le panneau.

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**Félicitations !** Vous avez créé la fabrique de données Azure, des services liés, des pipelines et des tables, et démarré le flux de travail. 


### <a name="MainStep6"></a>Étape 6 : surveiller les pipelines et les tranches de données 

1.	Si le panneau FABRIQUE DE DONNÉES pour LogProcessingFactory n'est pas ouvert, vous pouvez effectuer l'une des opérations suivantes :
	1.	Cliquez sur **LogProcessingFactory** depuis le **tableau d'accueil**. Lors de la création de la fabrique de données, l'option **Ajouter au tableau d'accueil** a été automatiquement activée.

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. Cliquez sur le concentrateur **PARCOURIR**, puis cliquez sur **Tout**.
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. Dans le panneau **Parcourir**, sélectionnez **Fabriques de données**, puis **LogProcessingFactory** dans le panneau **Fabrique de données**.

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Vous pouvez surveiller votre fabrique de données de plusieurs façons. Vous pouvez démarrer avec les pipelines ou les jeux de données. Commençons avec les pipelines avant d'aller plus loin. 
3.	Cliquez sur **Pipelines** dans le panneau **FABRIQUE DE DONNÉES**. 
4.	Cliquez sur **PartitionGameLogsPipeline** sur le panneau Pipelines. 
5.	Sur le panneau **PIPELINE** de **PartitionGameLogsPipeline**, vous voyez que le pipeline consomme le jeu de données **RawGameEventsTable**.  Cliquez sur **RawGameEventsTable**.

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. Sur le panneau TABLE de **RawGameEventsTable**, vous pouvez voir toutes les tranches. Dans la capture d'écran suivante, toutes les tranches sont à l'état **Prêt**, et il n'y a aucune tranche problématique. Cela signifie que les données sont prêtes à être traitées.	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. Sur le panneau **PIPELINE** de **PartitionGameLogsPipeline**, cliquez sur **Produit**. 
8. Vous devez voir la liste des jeux de données produits par ce pipeline : 
9. Cliquez sur la table **PartitionedGameEvents** dans le panneau **Jeux de données produits**. 
10.	Vérifiez que l'**état** de toutes les tranches est défini sur **Prêt**. 
11.	Cliquez sur l'une des tranches à l'état **Prêt** pour voir le panneau **TRANCHE DE DONNÉES** correspondant.

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	En cas d'erreur, vous voyez l'état **Échec** ici.  Vous pourriez aussi voir les deux tranches à l'état **Prêt**, ou les deux à l'état **PendingValidation**, en fonction de la vitesse de traitement des tranches.
 
	Pour comprendre tous les états possibles des tranches, reportez-vous à la rubrique [Référence du développeur Azure Data Factory][developer-reference].

12.	Sur le panneau **TRANCHE DE DONNÉES**, cliquez sur l'exécution dans la liste **Exécutions d'activité**. Vous devez voir le panneau Exécution d'activité pour cette tranche. Vous devez voir le panneau **DÉTAILS SUR L'EXÉCUTION D'ACTIVITÉ**.

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	Cliquez sur **Télécharger** pour télécharger les fichiers. Cet écran est particulièrement utile lorsque vous dépannez des erreurs de traitement HDInsight. 
	 
	
Lorsque tous les pipelines ont terminé leur exécution, vous pouvez consulter les résultats dans **MarketingCampaignEffectivenessTable** dans la base de données SDL Azure **MarketingCampaigns**. 

**Félicitations !**Vous pouvez désormais surveiller et dépanner les flux de travail. Vous avez appris comment utiliser Azure Data Factory pour traiter les données et obtenir des analyses.

	 

## Procédure pas à pas : utilisation de données locales

**(recommandé)** Consultez et pratiquez la procédure pas à pas de l'article [Activation de vos pipelines pour les utiliser avec des données locales][useonpremisesdatasources] pour manier la création d'un pipeline servant à déplacer les données SQL Server locales vers un magasin d'objets blob Azure.


Dans cette procédure pas à pas, vous allez apprendre à configurer l'environnement permettant d'activer l'utilisation de vos données locales sur le pipeline.
 
Dans la dernière étape du scénario de traitement de journal, lors de la première procédure pas à pas avec Partition -> Enrichir -> Analyser le flux de travail, la sortie de l'efficacité de la campagne marketing a été copiée dans une base de données SQL Azure. Vous pouvez également déplacer ces données vers une base de données SQL Server locale pour l'analyser au sein de votre organisation.
 
Pour copier les données d'efficacité de la campagne marketing depuis un objet blob Azure vers une base de données SQL Server locale, vous devez créer en supplément un service lié local, une table et un pipeline utilisant le même jeu de cmdlets présenté dans la première procédure pas à pas.

Dans ce didacticiel, vous allez effectuer les étapes suivantes : 

1. [Étape 1 : créer une passerelle de gestion des données](#OnPremStep1). La passerelle de gestion des données  est un agent client qui fournit l'accès aux sources de données locales de votre organisation à partir du cloud. La passerelle permet le transfert de données entre un serveur SQL Server local et des magasins de données Azure.	

	Vous devez avoir installé au moins une passerelle dans votre environnement d'entreprise et l'avoir enregistrée avec Azure Data Factory avant d'ajouter une base de données SQL Server locale comme un service lié à une fabrique de données Azure.

2. [Étape 2 : créer un service lié pour le serveur SQL Server local](#OnPremStep2). Dans cette étape, vous créez d'abord une base de données et une table sur votre ordinateur SQL Server local, avant de créer le service lié : **OnPremSqlLinkedService**.  
3. [Étape 3 : créer une table et un pipeline](#OnPremStep3). Dans cette étape, vous allez créer une table **MarketingCampaignEffectivenessOnPremSQLTable** et un pipeline **EgressDataToOnPremPipeline**. 

4. [Étape 4 : surveiller le pipeline et afficher le résultat](#OnPremStep4). Dans cette étape, vous allez surveiller les pipelines, les tables et les tranches de données à l'aide du portail Azure.


### <a name="OnPremStep1"></a>Étape 1 : créer une passerelle de gestion des données

La passerelle de gestion des données est un agent client qui fournit l'accès aux sources de données locales de votre organisation à partir du cloud. La passerelle permet le transfert de données entre un serveur SQL Server local et des magasins de données Azure.
  
Vous devez avoir installé au moins une passerelle dans votre environnement d'entreprise et l'avoir enregistrée avec Azure Data Factory avant d'ajouter une base de données SQL Server locale comme un service lié à une fabrique de données Azure.

Si vous disposez déjà d'une passerelle de données, ignorez cette étape.

1.	Créer une passerelle de données logique. Dans la **version préliminaire du portail Azure**, cliquez sur **Services liés** sur le panneau **FABRIQUE DE DONNÉES**.
2.	Cliquez sur **Ajouter (+) Passerelle de données** sur la barre de commandes.  
3.	Dans le panneau **Nouvelle passerelle de données**, cliquez sur **CRÉER**.
4.	Dans le panneau **Créer**, entrez **MyGateway** comme nom pour la passerelle de données****.
5.	Cliquez sur **SÉLECTIONNER UNE RÉGION** et modifiez-la au besoin. 
6.	Cliquez sur **OK** dans le panneau **Créer**. 
7.	Vous devez voir le panneau **Configurer**. 
8.	Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cela permet de télécharger, d'installer et de configurer la passerelle sur votre ordinateur et d'inscrire la passerelle auprès du service. Si une passerelle est déjà installée sur votre ordinateur et que vous souhaitez créer un lien vers cette passerelle logique sur le portail, vous pouvez utiliser la clé sur ce panneau pour réinscrire votre passerelle à l'aide du Gestionnaire de configuration de la passerelle de gestion des données (version préliminaire).

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Cliquez sur **OK** pour fermer le panneau **Configurer** et sur **OK** pour fermer le panneau **Créer**. Attendez que l'état de **MyGateway** dans le panneau **Services liés** passe sur **CORRECT**. Vous pouvez également lancer l'outil **Gestionnaire de configuration de la passerelle de gestion des données (version préliminaire)** pour vérifier que le nom de la passerelle correspond à celui du portail et que l'**état** est **Enregistré**. Vous devrez peut-être fermer et rouvrir le panneau Services liés pour afficher le dernier état. L'actualisation de l'écran sur l'état le plus récent peut prendre quelques minutes.	

### <a name="OnPremStep2"></a>Étape 2 : créer un service lié pour le serveur SQL Server local

Dans cette étape, vous créez d'abord la base de données et la table requises sur votre ordinateur SQL Server local, avant de créer le service lié.

#### Préparer la base de données et la table locales

Pour commencer, vous devez créer la base de données SQL Server, la table, les types définis par l'utilisateur et les procédures stockées. Cela permettra de passer les résultats **MarketingCampaignEffectiveness** de l'objet blob Azure dans la base de données SQL Server.

1.	Dans l'**Explorateur Windows**, accédez au sous-dossier **OnPremises** dans le dossier **C:\ADFWalkthrough** (ou à l'emplacement où vous avez extrait les exemples).
2.	Ouvrez **prepareOnPremDatabase&Table.ps1** dans votre éditeur favori, remplacez l'élément en surbrillance par vos informations SQL Server et enregistrez le fichier (fournissez les informations d'**authentification SQL**). Dans le cadre du didacticiel, activez l'authentification SQL pour votre base de données. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. Dans **Azure PowerShell**, accédez au dossier **C:\ADFWalkthrough\OnPremises**.
4.	Exécutez **prepareOnPremDatabase&Table.ps1** **(soit & entre guillemets doubles soit comme illustré ci-dessous)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### Créer le service lié

1.	Dans la **version préliminaire du portail Azure**, cliquez sur la vignette **Services liés** sur le panneau **FABRIQUE DE DONNÉES** pour **LogProcessingFactory**.
2.	Dans le panneau **Services liés**, cliquez sur **Ajouter (+) Magasin de données**.
3.	Dans le panneau **Nouveau magasin de données**, entrez **OnPremSqlLinkedService** comme **Nom**. 
4.	Cliquez sur **Type (paramètres requis)** et sélectionnez **SQL Server**. Maintenant, vous devez voir les paramètres **PASSERELLE DE DONNÉES**, **Serveur**, **Base de données** et **INFORMATIONS D'IDENTIFICATION** dans le panneau **Nouveau magasin de données**. 
5.	Cliquez sur **PASSERELLE DE DONNÉES (configurer les paramètres requis)** et sélectionnez la passerelle **MyGateway** que vous avez créée précédemment. 
6.	Entrez le **nom** du serveur de base de données qui héberge la base de données **MarketingCampaigns**. 
7.	Entrez **MarketingCampaigns** comme nom de base de données. 
8.	Cliquez sur **INFORMATIONS D'IDENTIFICATION**. 
9.	Dans le panneau **Informations d'identification**, cliquez sur **Cliquez ici pour définir les informations d'identification en toute sécurité**.
10.	Cela installe une application en un clic pour la première fois et lance la boîte de dialogue **Configuration des informations d'identification**. 
11.	Dans la boîte de dialogue **Configuration des informations d'identification**, entrez **Nom d'utilisateur** et **Mot de passe**, puis cliquez sur **OK**. Attendez la fermeture de la boîte de dialogue. 
12.	Cliquez sur **OK** dans le panneau **Nouveau magasin de données**. 
13.	Dans le panneau **Services liés**, vérifiez que **OnPremSqlLinkedService** est répertorié dans la liste et que l'**état** du service lié est **Correct**.

### <a name="OnPremStep3"></a>Étape 3 : créer une table et un pipeline

#### Créer la table logique locale

1.	Dans **Azure PowerShell**, accédez au dossier **C:\ADFWalkthrough\OnPremises**. 
2.	Utilisez la cmdlet **New-AzureDataFactoryTable** pour créer les tables comme suit pour **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Créer le pipeline pour copier les données d'un objet blob Azure dans SQL Server

1.	Utilisez la cmdlet **New-AzureDataFactoryPipeline** pour créer le pipeline comme suit pour **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. Utilisez la cmdlet **Set-AzureDataFactoryPipelineActivePeriod** pour spécifier la période active pour **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	Appuyez sur **" O "** pour continuer.
	
### <a name="OnPremStep4"></a>Étape 4 : surveiller le pipeline et afficher le résultat

Vous pouvez maintenant utiliser les mêmes étapes que celles présentées à l'[étape 6 : surveiller les tables et les pipelines](#MainStep6)  pour surveiller le nouveau pipeline et les tranches de données pour la nouvelle table ADF locale.
 
Lorsque vous voyez que l'état d'une tranche de la table **MarketingCampaignEffectivenessOnPremSQLTable** passe à Prêt, cela signifie que le pipeline a terminé l'exécution de la tranche. Pour afficher les résultats, interrogez la table **MarketingCampaignEffectiveness** dans la base de données **MarketingCampaigns** de votre serveur SQL Server.
 
Félicitations ! Vous avez terminé la procédure pas à pas pour utiliser vos données locales. 
 
## Voir aussi

Article | Description
------ | ---------------
[Activation de vos pipelines pour les utiliser avec des données locales][useonpremisesdatasources] | Cet article contient une procédure pas à pas permettant de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Utilisation de Pig et Hive avec Data Factory][usepigandhive] | Cet article contient une procédure pas à pas permettant d'exécuter un script Hive/Pig à l'aide de HDInsight Activity pour traiter les données d'entrée afin de produire des données de sortie. 
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline. 
[Surveillance et gestion d'Azure Data Factory à l'aide de PowerShell][monitor-manage-using-powershell] | Cet article décrit comment surveiller une fabrique de données Azure à l'aide de cmdlets Azure PowerShell. Vous pouvez essayer les exemples de l'article portant sur ADFTutorialDataFactory.
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les cmdlets, le script JSON, les fonctions, etc.
[Référence des cmdlets Azure Data Factory][cmdlet-reference] | Ces informations de référence comportent des détails sur toutes les cmdlets Data Factory. 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/fr-fr/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!--HONumber=46--> 
