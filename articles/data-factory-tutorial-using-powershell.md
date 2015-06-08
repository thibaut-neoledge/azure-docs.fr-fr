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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Didacticiel : Déplacer et traiter des fichiers journaux à l'aide de la fabrique de données [PowerShell]
Cet article fournit une procédure pas à pas pour un scénario de traitement de journaux à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en informations.

## Scénario
Contoso est une société de jeu qui crée des jeux pour plusieurs plateformes : consoles de jeux, périphériques de poche et les ordinateurs personnels (PC). Chacun de ces jeux produit une très grande quantité de journaux. L'objectif de Contoso est de collecter et d'analyser les journaux générés par ces jeux pour obtenir des informations sur l'utilisation, identifier les opportunités de vente incitative et de vente croisée, développer de nouvelles fonctionnalités intéressantes, etc. pour optimiser vos affaires et fournir une meilleure expérience aux clients.
 
Dans cette procédure pas à pas, nous allons collecter des exemples de journaux, nous allons les traiter et les enrichir avec des données de référence et transformer les données pour évaluer l'efficacité d'une campagne marketing lancée dernièrement par Contoso.

## Préparation pour le didacticiel
1.	Lecture [Introduction à la fabrique de données Azure][adfintroduction] pour obtenir une vue d'ensemble de la fabrique de données Azure et les concepts de niveau supérieur.
2.	Pour suivre ce didacticiel, vous devez disposer d'un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d'abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].
3.	Vous devez télécharger et installer [Azure PowerShell][download-azure-powershell] sur votre ordinateur. 
2.	**(recommandé)** révision et pratique du didacticiel dans le [prise en main Azure Data Factory][adfgetstarted] article pour obtenir un didacticiel simple pour vous familiariser avec le portail et les applets de commande.
3.	**(recommandé)** révision et pratiques de la procédure pas à pas dans le [Utilisez Pig et Hive avec Azure Data Factory][usepigandhive] article pour une procédure pas à pas sur la création d'un pipeline pour déplacer des données à partir de la source de données local vers un magasin d'objets blob Azure.
4.	Télécharger [ADFWalkthrough][adfwalkthrough-download] fichiers **C:\ADFWalkthrough** dossier **en conservant la structure de dossier**:
	- **Pipelines :** inclut les fichiers JSON qui contient la définition des pipelines.
	- **Tables :** inclut les fichiers JSON qui contient la définition des Tables.
	- **LinkedServices :** inclut les fichiers JSON qui contient la définition de votre stockage et de calcul (HDInsight) cluster 
	- **Scripts :** inclut des scripts Hive et Pig qui sont utilisées pour traiter les données et appelés à partir de pipelines
	- **SampleData :** inclut des exemples de données pour cette procédure pas à pas
	- **OnPremises :** inclut les fichiers JSON et script qui sont utilisés pour illustrer l'accès à vos données locales
	- **uploadSampleDataAndScripts.ps1 :** ce script télécharge les exemples de données et les scripts dans Azure.
5. Assurez-vous de disposer des ressources Azure suivantes :			
	- Compte Azure Storage.
	- Base de données SQL Azure
	- Azure de HDInsight Cluster version 3.1 ou au-dessus (ou utiliser une demande HDInsight cluster que le service de données fabrique créera automatiquement)	
7. Une fois les ressources Azure créées, assurez-vous de disposer des informations nécessaires pour se connecter à chacune de ces ressources.
 	- **Compte de stockage azure** - nom de compte et clé de compte.  
	- **Base de données SQL azure** -serveur, base de données, nom d'utilisateur et mot de passe.
	- **Azure HDInsight Cluster**.-nom du cluster HDInsight, nom d'utilisateur, mot de passe et nom de compte et clé de compte pour le stockage Azure associé à ce cluster. Si vous souhaitez utiliser un cluster de HDInsight à la demande au lieu de votre cluster HDInsight, vous pouvez ignorer cette étape.  
8. Lancez **Azure PowerShell** et exécutez les commandes suivantes. Ne fermez pas Azure PowerShell. Si vous fermez et rouvrez, vous devez exécuter ces commandes à nouveau.
	- Exécutez **Add-AzureAccount** et entrez le nom d'utilisateur et un mot de passe que vous utilisez pour vous connecter au portail Azure Preview.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements pour ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l'abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire.
	

## Vue d'ensemble
Le flux de travail de bout en bout est représentée ci-dessous : ![Didacticiel flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]

1. Le **PartitionGameLogsPipeline** lit les événements bruts de jeu à partir d'un stockage d'objets blob (RawGameEventsTable) et crée des partitions basées sur l'année, mois et jour (PartitionedGameEventsTable).
2. Le **EnrichGameLogsPipeline** joint des événements de jeu partitionnées (PartitionedGameEvents table, qui est une sortie de la PartitionGameLogsPipeline) avec le code géographique (RefGetoCodeDictionaryTable) et enrichit les données en mappant une adresse IP à correspondante emplacement géographique (EnrichedGameEventsTable).
3. Le **AnalyzeMarketingCampaignPipeline** pipeline s'appuie sur les données enrichies (EnrichedGameEventTable produit par le EnrichGameLogsPipeline) et le traite avec les données de publicité (RefMarketingCampaignnTable) pour créer la sortie finale de l'efficacité de la campagne de commercialisation qui sont copiées dans la base de données SQL Azure (MarketingCampainEffectivensessSQLTable) et un stockage d'objets blob Azure (MarketingCampaignEffectivenessBlobTable) pour l'analyse.
    
## Procédure pas à pas : Création, déploiement et analyse des flux de travail
1. [Étape 1: télécharger des exemples de données et les scripts](#MainStep1). Dans cette étape, vous allez télécharger tous les exemples de données (y compris l'ensemble des journaux et des données de référence) et les scripts Hive/Pig qui seront exécutés par les flux de travail. Les scripts que vous exécutez créent également une base de données SQL Azure (nommée MarketingCampaigns), des tables, des types définis par l'utilisateur et des procédures stockées.
2. [Étape 2: créer une fabrique de données Azure](#MainStep2). Dans cette étape, vous allez créer une fabrique de données Azure nommée LogProcessingFactory.
3. [Étape 3: créer des services liés](#MainStep3). Dans cette étape, vous allez créer les services liés suivants : 
	
	- 	**StorageLinkedService**. Lie l'emplacement de stockage Azure qui contient des événements de jeu bruts, des événements de jeu partitionnés, des événements de jeu enrichis, des informations d'efficacité de la campagne marketing, des données géocodées de référence et des données de campagne marketing de référence dans LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Lie une base de données SQL Azure qui contient des informations d'efficacité de la campagne marketing. 
	- 	**HDInsightStorageLinkedService**. Lie un stockage d'objets blob Azure qui est associé au cluster HDInsight qui désigne HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Lie un cluster Azure HDInsight à LogProcessingFactory. Ce cluster permet d'effectuer un traitement pig/hive sur les données. 
 		
4. [Étape 4: créer des tables](#MainStep4). Dans cette étape, vous allez créer les tables suivantes :
	
	- **RawGameEventsTable**. Cette table spécifie l'emplacement des données d'événement de jeu brutes dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Cette table spécifie l'emplacement des données d'événement de jeu partitionnées dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Cette table spécifie l'emplacement des données géocodées de référence dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Cette table spécifie l'emplacement des données de campagne marketing de référence dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Cette table spécifie l'emplacement des données d'événement de jeu enrichies dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Cette table spécifie la table SQL (MarketingCampaignEffectiveness) dans la base de données SQL Azure défini par AzureSqlLinkedService qui contient les données de l'efficacité de campagne marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Cette table spécifie l'emplacement des données d'efficacité de la campagne marketing dans le stockage d'objets blob Azure défini par StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Étape 5: créer et planifier les pipelines](#MainStep5). Dans cette étape, vous allez créer les pipelines suivants :
	- **PartitionGameLogsPipeline**. Ce pipeline lit les événements de jeu bruts depuis un stockage d'objets blob (RawGameEventsTable) et crée des partitions basées sur l'année, le mois et le jour (PartitionedGameEventsTable). 


		![Pipeline PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Ce pipeline joint les événements de jeu partitionnés (table PartitionedGameEvents, qui est une sortie de PartitionGameLogsPipeline) avec un code géographique (RefGetoCodeDictionaryTable) et enrichit les données en mappant une adresse IP à la géolocalisation correspondante (EnrichedGameEventsTable).

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Ce pipeline exploite les données enrichies (EnrichedGameEventTable produite par EnrichGameLogsPipeline) et les traite avec les données de publicité (RefMarketingCampaignnTable) pour créer la sortie finale d'efficacité de la campagne marketing, qui est copiée dans la base de données SQL Azure (MarketingCampainEffectivensessSQLTable) et un stockage d'objets blob Azure (MarketingCampaignEffectivenessBlobTable) pour analyse.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Étape 6: surveiller les tranches de données et pipelines](#MainStep6). Dans cette étape, vous allez surveiller les pipelines, les tables et les tranches de données à l'aide du portail Azure.

## <a name="MainStep1"></a> Étape 1: Télécharger des exemples de données et de scripts
Dans cette étape, vous allez télécharger tous les exemples de données (y compris l'ensemble des journaux et des données de référence) et les scripts Hive/Pig qui seront appelés par les flux de travail. Les scripts que vous exécutez également créent une base de données SQL Azure appelé **MarketingCampaigns**, tables, les types définis par l'utilisateur et les procédures stockées.

Les tables, les types définis par l'utilisateur et les procédures stockées sont utilisés lors du déplacement des résultats de l'efficacité de la campagne marketing depuis le stockage d'objets blob Azure vers la base de données SQL Azure.

1. Ouvrez **uploadSampleDataAndScripts.ps1** de **C:\ADFWalkthrough** dossier (ou le dossier qui contient les fichiers extraits) dans votre éditeur favori, remplacez l'en surbrillance avec vos informations de cluster et enregistrez le fichier.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][télécharger][sqlcmd-install]   
2. Vérifiez que votre ordinateur local est autorisé à accéder à la base de données SQL Azure. Pour activer l'accès, utilisez la **portail de gestion Azure** ou **sp_set_firewall_rule** sur la base de données master pour créer une règle de pare-feu pour l'adresse IP de votre ordinateur. Cela peut prendre jusqu'à cinq minutes pour que cette modification prenne effet. Voir [définition des règles de pare-feu pour SQL Azure][azure-sql-firewall].
4. Dans PowerShell Azure, accédez à l'emplacement où vous avez extrait les exemples (par exemple : **C:\ADFWalkthrough**)
5. Exécutez **uploadSampleDataAndScripts.ps1** 
6. Une fois que le script s'exécute correctement, vous verrez les éléments suivants :

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
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


## <a name="MainStep2"></a> Étape 2: Créer une fabrique de données Azure
Dans cette étape, vous créez une fabrique de données Azure nommée **LogProcessingFactory**.

1.	Après la connexion à la [portail Azure Preview][azure-preview-portal], cliquez sur **nouveau** dans le coin inférieur gauche, cliquez sur **Data Factory** sur la **nouveau** lame. 

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Si vous ne voyez pas **Data Factory** sur la **nouveau** lame, faites défiler vers le bas.
	
5. Dans le **nouvelle fabrique de données** lame, entrez **LogProcessingFactory** pour le **nom**.

	![Fabrique lame][image-data-factory-tutorial-new-datafactory-blade]

6. Si vous n'avez pas créé un groupe de ressources Azure nommé **ADF** déjà, procédez comme suit :
	1. Cliquez sur **nom de groupe de ressources**, puis cliquez sur **créer un groupe de ressources**.
	
		![Volet du groupe de ressources][image-data-factory-tutorial-resourcegroup-blade]
	2. Dans le **créer un groupe de ressources** lame, entrez **ADF** pour le nom du groupe de ressources, cliquez sur **OK**.
	
		![Créer le groupe de ressources][image-data-factory-tutorial-create-resourcegroup]
7. Sélectionnez **ADF** pour le **nom de groupe de ressources**.  
8.	Dans le **nouvelle fabrique de données** lame, notez que **Ajouter au tableau d'accueil** est activée par défaut. Cela ajoute un lien vers la fabrique de données sur le tableau d'accueil (ce que vous voyez lorsque vous vous connectez à la version préliminaire du portail Azure).

	![Créer la fabrique lame][image-data-factory-tutorial-create-datafactory]

9.	Dans le **nouvelle fabrique de données** lame, cliquez sur **créer** pour créer la fabrique de données.
10.	Après la création de la fabrique de données, vous devez voir les **DATA FACTORY** lame intitulée **LogProcessingFactory**.

	![Page d'accueil de fabrique de données][image-data-factory-tutorial-datafactory-homepage]

	
	Si vous ne le voyez pas, effectuez l'une des opérations suivantes :

	- Cliquez sur **LogProcessingFactory** sur la **tableau d'accueil** (page d'accueil)
	- Cliquez sur **Parcourir** dans le volet gauche, cliquez sur **tout**, cliquez sur **fabriques données**, puis cliquez sur la fabrique de données.
 
	> [AZURE.NOTE]Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez une erreur : **nom d'usine de données « LogProcessingFactory » n'est pas disponible**, modifiez le nom (par exemple, yournameLogProcessingFactory). Utilisez ce nom LogProcessingFactory lors de l'exécution des étapes de ce didacticiel.
 
## <a name="MainStep3"></a> Étape 3: Créer des services liés

> [AZURE.NOTE]Cet article utilise Azure PowerShell pour créer des pipelines, des tables et des services liés. Voir [didacticiel à l'aide de données fabrique éditeur][adftutorial-using-editor] si vous souhaitez effectuer ce didacticiel à l'aide du portail Azure, éditeur de fabrique de données en particulier.

Dans cette étape, vous allez créer les services liés suivants : StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService et HDInsightLinkedService.


1.	Dans le **LogProcessingFactory** lame, cliquez sur **Services liés** vignette.

	![Vignette Services liés][image-data-factory-tutorial-linkedservice-tile]

2. Dans le **Services liés** lame, cliquez sur **+ magasin de données** à partir de la barre de commandes.

	![Ajouter des Services liés - magasin][image-data-factory-tutorial-linkedservices-add-datstore]

3. Dans le **nouveau magasin de données** lame, entrez **StorageLinkedService** pour le **nom**, cliquez sur **TYPE (paramètres requis)**, puis sélectionnez **compte de stockage Azure**.

	![Type de magasin de données - stockage Azure][image-data-factory-tutorial-datastoretype-azurestorage]

4. Dans le **nouveau magasin de données** lame, vous verrez deux nouveaux champs : **nom de compte** et **clé de compte**. Entrez le nom de compte et clé de compte pour votre **compte de stockage Azure**.

	![Paramètres de stockage Azure][image-data-factory-tutorial-azurestorage-settings]

	Vous pouvez obtenir le nom et la clé de votre compte de stockage Azure à partir du portail, comme indiqué ci-après :

	![Clé de stockage][image-data-factory-tutorial-storage-key]
  
5. Après avoir cliqué sur **OK** sur les nouvelles données stocker lame, vous devriez voir **StorageLinkedService** dans la liste des **les banques de données** sur la **Services liés** lame. Vérifiez **NOTIFICATIONS** Hub (à gauche) pour voir tous les messages.

	![Lame de Services liés avec le stockage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Répétez les **les étapes 2 à 5** créer un autre service nommé lié : **HDInsightStorageLinkedService**. Il s'agit du stockage utilisé par votre cluster HDInsight.
7. Vérifiez que les deux **StorageLinkedService** et **HDInsightStorageLinkedService** dans la liste dans le volet Services liés.
8. Dans le **Services liés** lame, cliquez sur **Ajouter (+) le magasin de données** à partir de la barre de commandes.
9. Entrez **AzureSqlLinkedService** pour le nom.
10. Cliquez sur **TYPE (paramètres requis)**, sélectionnez **base de données SQL Azure**.
11. À présent, vous devez les champs supplémentaires suivants sur le **nouveau magasin de données** lame. Entrez le nom de la base de données SQL Azure **server**, **base de données** nom, **nom d'utilisateur**, et **mot de passe**, puis cliquez sur **OK**.
	1. Entrez **MarketingCampaigns** pour le **base de données**. Il s'agit de la base de données SQL Azure créé par les scripts que vous avez exécuté à l'étape 1. Vous devez confirmer que cette base de données a été effectivement créé par les scripts (en cas d'erreurs).
		
 		![Paramètres de SQL Azure][image-data-factory-tutorial-azuresql-settings]

		Pour obtenir ces valeurs à partir du portail de gestion Azure : cliquez sur vue SQL chaînes de connexion de base de données MarketingCampaigns

		![Chaîne de connexion de base de données SQL Azure][image-data-factory-tutorial-azuresql-database-connection-string]

12. Vérifiez que tous les magasins de trois données que vous avez créé : **StorageLinkedService**, **HDInsightStorageLinkedService**, et **AzureSqlLinkedService**.
13. Vous devez créer un autre service lié, mais il s'agit d'un service de calcul, spécifiquement **cluster Azure HDInsight**. Le portail ne prend pas encore en charge la création d'un service de calcul lié. Par conséquent, vous devez utiliser Azure PowerShell pour ce faire. 
14. Basculez vers **Azure PowerShell** si vous avez déjà ouvert (ou) lancement **Azure PowerShell**. Si vous avez fermé et rouvert Azure PowerShell, vous devez exécuter les commandes suivantes : 
	- Exécutez **Add-AzureAccount** et entrez le nom d'utilisateur et un mot de passe que vous utilisez pour vous connecter au portail Azure Preview.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements pour ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l'abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire. 
15. Basculez vers **AzureResourceManager** mode comme les applets de commande Azure Data Factory sont disponibles dans ce mode.

		Switch-AzureMode AzureResourceManager

16. Accédez à la **LinkedServices** sous-dossier **C:\ADFWalkthrough** (ou) à partir du dossier à partir de l'emplacement où vous avez extrait les fichiers.
17. Ouvrez **HDInsightLinkedService.json** dans votre éditeur favori et notez que le type est défini sur **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, lorsque vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l'exemple, nous allons utiliser un cluster à la demande. Le HDInsightLinkedService lie un cluster de HDInsight à la demande à la fabrique de données. Pour utiliser votre propre cluster HDInsight, mettre à jour la section Propriétés du fichier HDInsightLinkedService.json, comme illustré ci-dessous (clustername replace, nom d'utilisateur et mot de passe avec les valeurs appropriées) :
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. Utilisez la commande suivante pour définir la variable $df sur le nom de la fabrique de données.

		$df = “LogProcessingFactory”
19. Utilisez l'applet de commande **New-AzureDataFactoryLinkedService** pour créer un Service lié comme suit. Commencez par le compte de stockage :

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Si vous utilisez un nom différent pour ResourceGroupName, DataFactoryName ou LinkedService, indiquez-les dans la cmdlet ci-dessus. Vous devez également fournir le chemin d'accès complet du fichier JSON du service lié si le fichier est introuvable.
20. Vous devez voir tous les services liés quatre dans le **Services liés** lame comme indiqué ci-dessous. Si la lame de services lié n'est pas ouverte, cliquez sur Services liés dans le **DATA FACTORY** page **LogProcessingFactory**. L'actualisation du panneau des services liés peut prendre quelques secondes.

	![Lié Services toutes les][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Étape 4: Créer des tables 
Dans cette étape, vous allez créer les tables suivantes :

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Didacticiel flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]
 
L'image ci-dessus présente les pipelines sur la ligne du milieu et les tables sur les lignes supérieures et inférieures.

Le portail Azure n'autorise pas encore la création de jeux de données/tables, vous devez donc utiliser Azure PowerShell pour créer des tables dans cette version.

### Créer des tables

1.	Dans Azure PowerShell, accédez à la **tableaux** dossier (** C:\ADFWalkthrough\\Tables**) à partir de l'emplacement où vous avez extrait les exemples. 
2.	Utilisez l'applet de commande **New-AzureDataFactoryTable** pour créer les Tables comme suit pour **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Si vous utilisez un nom différent pour ResourceGroupName et DataFactoryName, indiquez-les dans la cmdlet ci-dessus. En outre, indiquez le chemin d'accès complet du fichier JSON de la table si la cmdlet ne trouve pas le fichier.

3. Répétez l'étape précédente pour créer les tables suivantes :
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. Dans le **portail Azure Preview**, cliquez sur **Datasets** dans le **DATA FACTORY** lame pour **LogProcessingFactory** et vérifiez que tous les groupes de données (les tables sont des groupes de données rectangulaire).

	![Jeux de données de toutes les][image-data-factory-tutorial-datasets-all]

	Vous pouvez également utiliser la commande suivante à partir d'Azure PowerShell :
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Étape 5: Créer et planifier des pipelines
Dans cette étape, vous allez créer les pipelines suivants : PartitionGameLogsPipeline, EnrichGameLogsPipeline et AnalyzeMarketingCampaignPipeline.

1. Dans **l'Explorateur Windows**, accédez à la **Pipelines** sous-dossier dans **C:\ADFWalkthrough** dossier (ou à partir de l'emplacement où vous avez extrait les exemples).
2.	Ouvrez **PartitionGameLogsPipeline.json** dans votre éditeur favori, remplacez l'en surbrillance avec votre compte de stockage pour les informations de compte de stockage de données et enregistrez le fichier.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Répétez l'étape pour créer les pipelines suivants :
	1. **EnrichGameLogsPipeline**.json (3 occurrences)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 occurrences)

	**IMPORTANT :** confirmer que vous avez remplacé toutes les <storageaccountname> avec votre nom de compte de stockage.
 
4.  Dans **Azure PowerShell**, accédez à la **Pipelines** sous-dossier dans **C:\ADFWalkthrough** dossier (ou à partir de l'emplacement où vous avez extrait les exemples).
5.  Utilisez l'applet de commande **New-AzureDataFactoryPipeline** pour créer les Pipelines comme suit pour **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Si vous utilisez un nom différent pour ResourceGroupName, DataFactoryName ou le pipeline, indiquez-les dans la cmdlet ci-dessus. Fournissez également le chemin d'accès complet au fichier JSON de pipeline.
6. Répétez l'étape précédente pour créer les pipelines suivants :
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Utilisez l'applet de commande **Get-AzureDataFactoryPipeline** pour obtenir la liste des Pipelines.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Une fois les pipelines créés, vous pouvez spécifier la durée de traitement des données. En spécifiant la période active pour un pipeline, vous définissez la durée de traitement des tranches de données en fonction des propriétés de disponibilité qui ont été définies pour chaque table ADF.

Pour spécifier la période active pour le pipeline, vous pouvez utiliser la cmdlet Set-AzureDataFactoryPipelineActivePeriod. Dans cette procédure pas à pas, les exemples de données provient 05/01 à 05/05. Utilisez 2014-05-01 en tant que le StartDateTime. La valeur EndDateTime est facultative.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Confirmez pour définir la période active pour le pipeline.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Répétez les deux étapes précédentes pour définir une période active pour les pipelines suivants.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. Dans le **portail Azure Preview**, cliquez sur **Pipelines** vignette (et non sur les noms des pipelines) dans le **DATA FACTORY** lame pour le **LogProcessingFactory**, vous devez voir les pipelines que vous avez créé.

	![Tous les Pipelines][image-data-factory-tutorial-pipelines-all]

12. Dans le **DATA FACTORY** lame pour le **LogProcessingFactory**, cliquez sur **diagramme**.

	![Lien de diagramme][image-data-factory-tutorial-diagram-link]

13. Vous pouvez réorganiser le diagramme affiché, comme dans le diagramme ci-après qui affiche les entrées directes en haut et les sorties en bas. Vous pouvez voir que la sortie de la **PartitionGameLogsPipeline** est transmis comme entrée pour l'EnrichGameLogsPipeline et la sortie de la **EnrichGameLogsPipeline** est transmis à la **AnalyzeMarketingCampaignPipeline**. Double-cliquez sur un titre pour afficher des détails sur l'objet représenté par le panneau.

	![Vue de diagramme][image-data-factory-tutorial-diagram-view]

	**Félicitations !** Vous avez créé la fabrique de données Azure, des services liés, des pipelines et des tables, et démarré le flux de travail.


## <a name="MainStep6"></a> Étape 6: Surveiller les tranches de données et de pipelines 

1.	Si le panneau FABRIQUE DE DONNÉES pour LogProcessingFactory n'est pas ouvert, vous pouvez effectuer l'une des opérations suivantes :
	1.	Cliquez sur **LogProcessingFactory** sur la **tableau d'accueil**. Lors de la création de la fabrique de données, le **Ajouter au tableau d'accueil** option a été activée automatiquement.

		![Tableau d'accueil d'analyse][image-data-factory-monitoring-startboard]

	2. Cliquez sur **Parcourir** concentrateur, puis cliquez sur **tout**.
	 	
		![Analyse de concentrateur tout][image-data-factory-monitoring-hub-everything]

		Dans le **Parcourir** lame, sélectionnez **fabriques données** et sélectionnez **LogProcessingFactory** dans les **fabriques de données** lame.

		![Analyse Datafactories de navigation][image-data-factory-monitoring-browse-datafactories]
2. Vous pouvez surveiller votre fabrique de données de plusieurs façons. Vous pouvez démarrer avec les pipelines ou les jeux de données. Commençons avec les pipelines avant d'aller plus loin. 
3.	Cliquez sur **Pipelines** sur la **DATA FACTORY** lame. 
4.	Cliquez sur **PartitionGameLogsPipeline** dans le volet de Pipelines. 
5.	Dans le **PIPELINE** lame pour **PartitionGameLogsPipeline**, vous voyez que le pipeline consomme **RawGameEventsTable** dataset. Cliquez sur **RawGameEventsTable**.

	![Pipeline consommées et produites][image-data-factory-monitoring-pipeline-consumed-produced]

6. Dans le volet de la TABLE de **RawGameEventsTable**, vous voyez tous les secteurs. Dans la capture d'écran suivante, tous les secteurs sont **prêt** état et il n'y aucune coupe du problème. Cela signifie que les données sont prêtes à être traitées.

	![Volet RawGameEventsTable TABLE][image-data-factory-monitoring-raw-game-events-table]
 
7. À présent, sur le **PIPELINE** lame pour **PartiionGameLogsPipeline**, cliquez sur **produit**.
8. Vous devez voir la liste des jeux de données produits par ce pipeline : 
9. Cliquez sur **PartitionedGameEvents** table dans le **produit datasets** lame. 
10.	Vérifiez que le **état** de toutes les tranches est défini sur **prêt**. 
11.	Cliquez sur l'un des secteurs qui est **prêt** pour voir les **tranche de données** lame pour ce secteur.

	![Lame de tranche de données RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si une erreur s'est produite, vous voyez un **Failed **état ici. Vous pouvez également voir soit les deux tranches avec l'état **prêt**, ou les deux avec l'état **en attente de validation**, selon la vitesse de traitement des secteurs.
 
	Reportez-vous à la [référence du développeur Azure données fabrique][developer-reference] pour obtenir une présentation de tous les états possibles de tranche.

12.	Dans le **tranche de données** lame, cliquez sur l'exécution de la **activité** liste. Vous devez voir le panneau Exécution d'activité pour cette tranche. Vous devez voir les éléments suivants **Détails de l'exécution activité** lame.

	![Volet de détails de l'exécution d'activité][image-data-factory-monitoring-activity-run-details]

13.	Cliquez sur **télécharger** pour télécharger les fichiers. Cet écran est particulièrement utile lorsque vous dépannez des erreurs de traitement HDInsight.
	 
	
Lorsque le pipeline de tous les aient terminé leur exécution, vous pouvez consulter dans le **MarketingCampaignEffectivenessTable** dans les **MarketingCampaigns** base de données SQL Azure pour afficher les résultats.

**Félicitations !** Vous pouvez désormais surveiller et dépanner les flux de travail. Vous avez appris comment utiliser Azure Data Factory pour traiter les données et obtenir des analyses.

## Étendre le didacticiel pour utiliser des données locales
Dans la dernière étape du scénario de la procédure pas à pas dans cet article de traitement du journal, la sortie de l'efficacité de campagne marketing a été copiée dans une base de données SQL Azure. Vous pouvez également déplacer ces données vers une base de données SQL Server locale pour l'analyser au sein de votre organisation.
 
Pour copier les données de l'efficacité de campagne marketing à partir d'objets Blob Azure à SQL serveur local, vous devez créer des services liés sur site supplémentaires, Table et le Pipeline à la procédure pas à pas dans cet article.

Pratique du [procédure pas à pas : utilisation sur site source de données][tutorial-onpremises-using-powershell] pour apprendre à créer un pipeline pour copier les données de l'efficacité des campagnes marketing à une base de données SQL Server sur site.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->