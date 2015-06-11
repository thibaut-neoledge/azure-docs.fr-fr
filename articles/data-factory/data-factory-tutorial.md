<properties 
	pageTitle="Déplacement et traitement des fichiers journaux à l&#39;aide d'Azure Data Factory" 
	description="Ce didacticiel avancé décrit un scénario réel proche et implémente le scénario à l'aide de service Factory de données Azure et éditeur de fabrique de données." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Didacticiel : Mesure de l'efficacité d'une campagne marketing  
Contoso est une société de jeu qui crée des jeux pour plusieurs plateformes : consoles de jeux, périphériques de poche et les ordinateurs personnels (PC). Ces jeux générer beaucoup de journaux et objectif de Contoso est pour collecter et analyser ces journaux pour accéder aux préférences des clients, données démographiques, etc. pour identifier les opportunités de vente incitative et de vente croisée, développer de nouvelles fonctionnalités intéressantes à dynamiser sa croissance et fournir une meilleure expérience aux clients comportement d'utilisation.

Dans ce didacticiel, vous allez créer des pipelines de données fabrique pour évaluer l'efficacité d'une campagne marketing Contoso a récemment lancé par collecter les journaux de l'exemple, le traitement et les enrichir des données de référence et transformer les données. Il comporte les trois pipelines suivants :

1.	Le **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis le stockage blob et crée des partitions basées sur l'année, mois et jour.
2.	Le **EnrichGameLogsPipeline** joint des événements de jeu partitionnés avec les données de référence de code géographique et enrichit les données en mappant les adresses IP pour les emplacements géographiques correspondantes.
3.	Le **AnalyzeMarketingCampaignPipeline** pipeline s'appuie sur les données enrichies et traite les données de la publicité pour créer la sortie finale qui contient l'efficacité de la campagne marketing.

## Préparation pour le didacticiel
1.	Lecture [Introduction à la fabrique de données Azure][adfintroduction] pour obtenir une vue d'ensemble de la fabrique de données Azure et les concepts de niveau supérieur.
2.	Pour suivre ce didacticiel, vous devez disposer d'un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d'abonnement](http://azure.microsoft.com/pricing/purchase-options/), des [offres spéciales membres](http://azure.microsoft.com/pricing/member-offers/) ou de la [version d'évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).
3.	Vous devez télécharger et installer [Azure PowerShell][download-azure-powershell] sur votre ordinateur. Vous allez exécuter les applets de commande Factory de données pour télécharger des exemples de données et les scripts pig/hive votre stockage d'objets blob. 
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
Le flux de travail de bout en bout est représenté ci-dessous :

![Didacticiel flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]

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

1.	Après la connexion à la [portail Azure Preview][azure-preview-portal], cliquez sur **nouveau** dans le coin inférieur gauche, cliquez sur **l'analyse de données** dans les **créer** lame, puis cliquez sur **Data Factory** sur la **l'analyse de données** lame. 

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]

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

> [AZURE.NOTE]Cet article utilise le portail Azure, en particulier l'éditeur de fabrique de données, pour créer des pipelines, des tables et des services liés. Voir [didacticiel à l'aide d'Azure PowerShell][adftutorial-using-powershell] si vous souhaitez suivre ce didacticiel à l'aide d'Azure PowerShell.

Dans cette étape, vous allez créer les services liés suivants :

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Créer StorageLinkedService et HDInsightStorageLinkedService

1.	Dans le **DATA FACTORY** lame, cliquez sur **auteur et déployer** vignette pour lancer le **éditeur** pour la fabrique de données.

	![Vignette Créer et déployer][image-author-deploy-tile]

	> [AZURE.NOTE]Voir [données fabrique éditeur][data-factory-editor] rubrique pour une présentation détaillée de l'éditeur de la fabrique de données.

2.  Dans le **éditeur**, cliquez sur **nouveau magasin de données** bouton sur la barre d'outils et sélectionnez **stockage Azure** dans le menu déroulant. Vous devez voir le modèle JSON pour la création d'un service de stockage Azure lié dans le volet droit.
	
	![Éditeur nouveau bouton de magasin de données][image-editor-newdatastore-button]

3. Remplacez **accountname** et **accountkey** avec le nom de compte et les valeurs de clé de compte pour votre compte de stockage Azure.

	![Stockage d'objets Blob éditeur JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Voir [référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) pour plus d'informations sur les propriétés JSON.

4. Cliquez sur **déployer** sur la barre d'outils pour déployer le StorageLinkedService. Vérifiez que le message **lié SERVICE créé avec succès** sur la barre de titre.

	![Stockage d'objets Blob éditeur déployer][image-editor-blob-storage-deploy]

5. Répétez les étapes pour créer un autre stockage Azure lié service nommé : **HDInsightStorageLinkedService** pour le stockage associé à votre cluster HDInsight. Dans le script JSON pour le service lié, modifiez la valeur de la **nom** propriété **HDInsightStorageLinkedService**.

### Créer AzureSqlLinkedService
1. Dans le **données fabrique éditeur** , cliquez sur **nouveau magasin de données** bouton sur la barre d'outils et sélectionnez **base de données SQL Azure** dans le menu déroulant. Vous devez voir le modèle JSON pour créer le service SQL Azure lié dans le volet droit.
2. Remplacez **nom_serveur**, **username@servername**, et **mot de passe** avec des noms de votre serveur SQL Azure, le compte d'utilisateur et le mot de passe. 3. Remplacez **databasename** avec **MarketingCampaigns**. Il s'agit de la base de données SQL Azure créé par les scripts que vous avez exécuté à l'étape 1. Vous devez confirmer que cette base de données a été effectivement créé par les scripts (en cas d'erreurs). 
3. Cliquez sur **déployer** sur la barre d'outils pour créer et déployer le AzureSqlLinkedService.

### Créer HDInsightLinkedService
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, lorsque vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l'exemple, nous allons utiliser un cluster à la demande.

#### Pour utiliser un cluster HDInsight à la demande
1. Cliquez sur **Nouveau calcul** à partir de la barre de commandes et sélectionnez **cluster HDInsight sur demande** dans le menu.
2. Procédez comme suit dans le script JSON : 
	1. Pour le **clusterSize** propriété, spécifiez la taille du cluster HDInsight.
	2. Pour le **jobsContainer** propriété, spécifiez le nom du conteneur par défaut où les journaux de cluster seront stockés. Pour les besoins de ce didacticiel, spécifiez **adfjobscontainer**.
	3. Pour le **timeToLive** propriété, spécifiez la durée pendant laquelle le client peut être inactif avant d'être supprimé. 
	4. Pour le **version** propriété, spécifiez la version de HDInsight que vous souhaitez utiliser. Si vous excluez cette propriété, la dernière version est utilisée.  
	5. Pour le **linkedServiceName**, spécifiez **HDInsightStorageLinkedService** que vous avez créé à la méthode Get de démarrer le didacticiel. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		Notez que le **type** du service lié est défini sur **HDInsightOnDemandLinkedService**.

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le service lié.
   
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Cliquez sur **Nouveau calcul** à partir de la barre de commandes et sélectionnez **cluster HDInsight** à partir du menu.
2. Procédez comme suit dans le script JSON : 
	1. Pour le **clusterUri** propriété, entrez l'URL de votre HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/     
	2. Pour le **nom d'utilisateur** propriété, entrez le nom d'utilisateur qui a accès au cluster HDInsight.
	3. Pour le **mot de passe** propriété, entrez le mot de passe de l'utilisateur. 
	4. Pour le **LinkedServiceName** propriété, entrez **StorageLinkedService**. Désigne le service lié que vous avez créé dans le didacticiel démarré Get. 

	Ignorer que les **type** du service lié est défini sur **HDInsightBYOCLinkedService** (BYOC signifie mettre de votre propre Cluster).

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le service lié.


## <a name="MainStep4"></a> Étape 4: Créer des tables
 
Dans cette étape, vous allez créer les tables de fabrique de données suivantes :

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Didacticiel flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]
 
L'image ci-dessus présente les pipelines sur la ligne du milieu et les tables sur les lignes supérieures et inférieures.

### Créer des tables
	
1. Dans le **éditeur** pour la fabrique de données, cliquez sur **nouveau dataset** bouton dans la barre d'outils et cliquez sur **stockage d'objets Blob Azure** dans le menu déroulant. 
2. Remplacez le script JSON à partir de JSON dans le volet droit le **RawGameEventsTable.json** à partir de la **C:\ADFWalkthrough\\Tables** dossier.
3. Cliquez sur **déployer** sur la barre d'outils pour créer et déployer le tableau. Vérifiez que le **TABLE créée avec succès** message sur la barre de titre de l'éditeur.
4. Répétez les étapes 1 à 3 avec le contenu provenant des fichiers suivants : 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Répétez les étapes 1 à 3 avec le contenu du fichier suivant. MAIS sélectionnez **Sql Azure** après avoir cliqué sur **nouveau dataset**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Étape 5: Créer et planifier des pipelines
Dans cette étape, vous allez créer les pipelines suivants :

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Pour créer des pipelines

1. Dans **Data Factory Editor**, cliquez sur **Nouveau pipeline** dans la barre d'outils. Si ce bouton n’est pas affiché dans la barre d’outils, cliquez sur **... (points de suspension)**. Vous pouvez également cliquer sur **Pipelines** dans l'arborescence, puis sur **Nouveau pipeline**.
2. Remplacez le script JSON à partir de JSON dans le volet droit le **PartitionGameLogsPipeline.json** à partir de la **C:\ADFWalkthrough\\Pipelines** dossier.
3. Ajouter une **virgule (« , »)** à la fin du **crochet fermant (« ] »)** dans le script JSON puis ajoutez les trois lignes suivantes après le crochet fermant. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]Notez que les heures de début et de fin sont définies sur 05/01/2014 et 05/05/2014, car les données d'exemple de cette procédure pas à pas vont du 05/01/2014 au 05/05/2014.
 
3. Cliquez sur **Déployer** sur la barre d'outils pour déployer le pipeline. Vérifiez que le message **PIPELINE CRÉÉ AVEC SUCCÈS** s’affiche sur la barre de titre de l'éditeur.
4. Répétez les étapes 1 à 3 avec le contenu provenant des fichiers suivants : 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Fermez les lames fabrique de données en appuyant sur **X** (en haut à droite) pour afficher la page d'accueil (** DATA FACTORY **lame) pour votre fabrique de données. 
### Vue de diagramme

1. Dans le **DATA FACTORY** lame pour le **LogProcessingFactory**, cliquez sur **diagramme**. 

	![Lien de diagramme][image-data-factory-tutorial-diagram-link]

2. Vous pouvez réorganiser le diagramme affiché, comme dans le diagramme ci-après qui affiche les entrées directes en haut et les sorties en bas. Vous pouvez voir que la sortie de la **PartitionGameLogsPipeline** est transmis comme entrée pour l'EnrichGameLogsPipeline et la sortie de la **EnrichGameLogsPipeline** est transmis à la **AnalyzeMarketingCampaignPipeline**. Double-cliquez sur un titre pour afficher des détails sur l'objet représenté par le panneau.

	![Vue de diagramme][image-data-factory-tutorial-diagram-view]

3. Droit **AnalyzeMarketingCampaignPipeline**, puis cliquez sur **ouverte**. Vous devez voir toutes les activités dans le pipeline, ainsi que des groupes de données d'entrée et de sortie pour les activités.
 
	![Ouverte](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Cliquez sur **Data factory** dans l'arborescence de navigation dans le coin supérieur gauche pour revenir à l'affichage des diagrammes avec tous les pipelines.


**Félicitations !** Vous avez créé la fabrique de données Azure, des services liés, des pipelines et des tables, et démarré le flux de travail.


## <a name="MainStep6"></a> Étape 6: Surveiller les tranches de données et de pipelines 

1.	Si vous n'avez pas le **DATA FACTORY** lame pour le **LogProcessingFactory** ouvert, vous pouvez effectuer l'une des opérations suivantes :
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

	Les deux **récemment mis à jour les tranches** et **Échec récemment tranches** listes sont triés par le **heure de dernière mise à jour**. L'heure de mise à jour d'une tranche de données est modifié dans les situations suivantes.

	-  Vous mettre à jour l'état de la coupe manuellement, par exemple, à l'aide de la **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **exécuter** sur la **tranche** lame pour la tranche.
	-  La tranche modifie l'état en raison d'une exécution (par exemple, une exécution démarré, une exécution s'est terminée et a échoué, une exécution s'est terminée et réussie, etc.).
 
	Cliquez sur le titre des listes ou des **... (points de suspension)** Pour consulter la liste supérieure des tranches. Cliquez sur **filtre** sur la barre d'outils pour filtrer les secteurs.
	
	Pour afficher les tranches de données triés par les heures de début et de fin de tranche au lieu de cela, cliquez sur **tranches de données (par heure de tranche)** vignette.

	![Tranches de données par heure de secteur][DataSlicesBySliceTime]
 
7. À présent, sur le **PIPELINE** lame pour **PartiionGameLogsPipeline**, cliquez sur **produit**.
8. Vous devez voir la liste des jeux de données produits par ce pipeline : 
9. Cliquez sur **PartitionedGameEvents** table dans le **produit datasets** lame. 
10.	Vérifiez que le **état** de toutes les tranches est défini sur **prêt**. 
11.	Cliquez sur l'un des secteurs qui est **prêt** pour voir les **tranche de données** lame pour ce secteur.

	![Lame de tranche de données RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si une erreur s'est produite, vous voyez un **Failed **état ici. Vous pouvez également voir soit les deux tranches avec l'état **prêt**, ou les deux avec l'état **en attente de validation**, selon la vitesse de traitement des secteurs.

	Si la section n'est pas dans le **prêt** état, vous pouvez voir les tranches en amont qui ne sont pas prêts et bloquent la tranche actuelle à partir de l'exécution dans le **tranches en amont qui ne sont pas prêts** liste.
 
	Reportez-vous à la [référence du développeur Azure données fabrique][developer-reference] pour obtenir une présentation de tous les états possibles de tranche.

12.	Dans le **tranche de données** lame, cliquez sur l'exécution de la **activité** liste. Vous devez voir le panneau Exécution d'activité pour cette tranche. Vous devez voir les éléments suivants **Détails de l'exécution activité** lame.

	![Volet de détails de l'exécution d'activité][image-data-factory-monitoring-activity-run-details]

13.	Cliquez sur **télécharger** pour télécharger les fichiers. Cet écran est particulièrement utile lorsque vous dépannez des erreurs de traitement HDInsight.
	 
	
Lorsque le pipeline de tous les aient terminé leur exécution, vous pouvez consulter dans le **MarketingCampaignEffectivenessTable** dans les **MarketingCampaigns** base de données SQL Azure pour afficher les résultats.

**Félicitations !** Vous pouvez désormais surveiller et dépanner les flux de travail. Vous avez appris comment utiliser Azure Data Factory pour traiter les données et obtenir des analyses.

## Étendre le didacticiel pour utiliser des données locales
Dans la dernière étape du scénario de la procédure pas à pas dans cet article de traitement du journal, la sortie de l'efficacité de campagne marketing a été copiée dans une base de données SQL Azure. Vous pouvez également déplacer ces données vers une base de données SQL Server locale pour l'analyser au sein de votre organisation.
 
Pour copier les données de l'efficacité de campagne marketing à partir d'objets Blob Azure à SQL serveur local, vous devez créer des services liés sur site supplémentaires, Table et le Pipeline à la procédure pas à pas dans cet article.

Pratique du [procédure pas à pas : utilisation sur site source de données][tutorial-onpremises] pour apprendre à créer un pipeline pour copier les données de l'efficacité des campagnes marketing à une base de données SQL Server sur site.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

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

<!---HONumber=GIT-SubDir--> 