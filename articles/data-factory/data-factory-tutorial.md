<properties 
	pageTitle="Déplacement et traitement des fichiers journaux à l’aide d'Azure Data Factory (portail Azure)" 
	description="Ce didacticiel avancé décrit un scénario proche de la réalité et l'implémente à l'aide du service Microsoft Azure Data Factory et de Data Factory Editor dans le portail Azure." 
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
	ms.date="08/25/2015" 
	ms.author="spelluru"/>

# Didacticiel : Mesure de l’efficacité d’une campagne marketing  
Contoso est une société qui crée des jeux pour plusieurs plateformes : des consoles de jeux, des appareils portatifs et des ordinateurs personnels (PC). Ces jeux génèrent beaucoup de journaux. L’objectif de Contoso est de collecter et d’analyser ces journaux pour connaître les préférences des clients, des données démographiques, des comportements d’utilisation, etc., afin d’identifier des opportunités de vente incitative et de vente croisée et de développer de nouvelles fonctionnalités intéressantes visant à optimiser la croissance et à fournir une meilleure expérience aux clients.

Dans ce didacticiel, vous allez créer des pipelines Data Factory afin d’évaluer l’efficacité d’une campagne marketing lancée récemment par Contoso, en collectant des exemples de journaux, en les traitant et en les enrichissant avec des données de référence, puis en transformant ces données. Les trois pipelines suivants sont inclus :

1.	Le pipeline **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis un stockage d’objets blob et crée des partitions basées sur l’année, le mois et le jour.
2.	Le pipeline **EnrichGameLogsPipeline** associe des événements de jeu partitionnés à des données de référence de code géographique et les enrichit en mappant les adresses IP avec les emplacements géographiques correspondants.
3.	Le pipeline **AnalyzeMarketingCampaignPipeline** s’appuie sur les données enrichies et les traite avec les données de publicité pour créer la sortie finale contenant l’efficacité de la campagne marketing.

## Préparation pour le didacticiel
1.	Lisez la page [Présentation d’Azure Data Factory][adfintroduction] pour obtenir une vue d’ensemble de Microsoft Azure Data Factory et comprendre les concepts principaux.
2.	Pour suivre ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d’abonnement](http://azure.microsoft.com/pricing/purchase-options/), des [offres spéciales membres](http://azure.microsoft.com/pricing/member-offers/) ou de la [version d’évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).
3.	Vous devez télécharger et installer [Azure PowerShell][download-azure-powershell] sur votre ordinateur. Vous allez exécuter les applets de commande Data Factory pour charger des exemples de données et des scripts pig/hive sur votre stockage d’objets blob. 
2.	**(recommandé)** Consultez et effectuez le didacticiel de l’article [Prise en main d’Azure Data Factory][adfgetstarted], car ce didacticiel simple vous permettra de vous familiariser avec le portail et les applets de commande.
3.	**(recommandé)** Consultez et effectuez la procédure pas à pas de l’article [Utilisation de Pig et Hive avec Azure Data Factory][usepigandhive] pour en savoir plus sur la création d’un pipeline servant à déplacer les données d’une source de données locale vers un magasin d’objets blob Microsoft Azure.
4.	Téléchargez les fichiers [ADFWalkthrough][adfwalkthrough-download] dans le dossier **C:\\ADFWalkthrough** **sans modifier la structure du dossier** :
	- **Pipelines :** inclut des fichiers JSON contenant la définition des pipelines.
	- **Tables :** inclut des fichiers JSON contenant la définition des tables.
	- **LinkedServices :** inclut des fichiers JSON contenant la définition de votre cluster de stockage et de calcul (HDInsight). 
	- **Scripts :** inclut des scripts Hive et Pig qui sont utilisés pour traiter les données et appelés à partir de pipelines.
	- **SampleData :** inclut des exemples de données pour cette procédure pas à pas.
	- **OnPremises :** inclut des fichiers JSON et un script permettant d’illustrer l’accès à vos données locales.
	- **uploadSampleDataAndScripts.ps1 :** ce script charge les exemples de données et les scripts dans Microsoft Azure.
5. Assurez-vous de disposer des ressources Azure suivantes :			
	- Compte Azure Storage.
	- Base de données SQL Azure.
	- Un cluster Microsoft Azure HDInsight version 3.1 ou plus (ou un cluster HDInsight à la demande créé automatiquement par le service Data Factory).	
7. Une fois les ressources Azure créées, assurez-vous de disposer des informations nécessaires pour vous connecter à chacune de ces ressources.
 	- **Compte Azure Storage** : nom de compte et clé de compte.  
	- **Base de données SQL Azure** : serveur, base de données, nom d’utilisateur et mot de passe.
	- **Cluster Azure HDInsight** : nom du cluster HDInsight, nom d’utilisateur, mot de passe, nom de compte et clé de compte pour le système Microsoft Azure Storage associé à ce cluster. Si vous souhaitez utiliser un cluster HDInsight à la demande au lieu de votre propre cluster HDInsight, vous pouvez ignorer cette étape.  
8. Lancez **Azure PowerShell** et exécutez les commandes suivantes. Laissez la fenêtre Microsoft Azure PowerShell ouverte. Si vous la fermez, puis la rouvrez, vous devez exécuter ces commandes à nouveau.
	- Exécutez **Add-AzureAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Microsoft Azure en version préliminaire.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire.	

## Vue d’ensemble
Le flux de travail de bout en bout est représenté ci-dessous :

![Didacticiel Flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]

1. Le pipeline **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis un stockage d’objets blob (RawGameEventsTable) et crée des partitions basées sur l’année, le mois et le jour (PartitionedGameEventsTable).
2. Le pipeline **EnrichGameLogsPipeline** joint les événements de jeu partitionnés (table PartitionedGameEvents, qui est une sortie de PartitionGameLogsPipeline) avec un code géographique (RefGetoCodeDictionaryTable) et enrichit les données en mappant une adresse IP à la géolocalisation correspondante (EnrichedGameEventsTable).
3. Le pipeline **AnalyzeMarketingCampaignPipeline** exploite les données enrichies (table EnrichedGameEventTable produite par EnrichGameLogsPipeline) et les traite avec les données de publicité (RefMarketingCampaignTable) pour créer la sortie finale d’efficacité de la campagne marketing, qui est copiée dans la base de données SQL Azure (MarketingCampainEffectivenessSQLTable) et un stockage d’objets blob Microsoft Azure (MarketingCampaignEffectivenessBlobTable) pour analyse.
    
## Procédure pas à pas : création, déploiement et surveillance des flux de travail
1. [Étape 1 : Télécharger des exemples de données et des scripts](#MainStep1). Dans cette étape, vous allez télécharger tous les exemples de données (y compris l’ensemble des journaux et des données de référence) et les scripts Hive/Pig qui seront exécutés par les flux de travail. Les scripts que vous exécutez créent également une base de données SQL Azure (nommée MarketingCampaigns), des tables, des types définis par l’utilisateur et des procédures stockées.
2. [Étape 2 : Créer une fabrique de données Microsoft Azure](#MainStep2). Dans cette étape, vous allez créer une fabrique de données Azure nommée LogProcessingFactory.
3. [Étape 3 : Créer des services liés](#MainStep3). Dans cette étape, vous allez créer les services liés suivants : 
	
	- 	**StorageLinkedService**. Lie l’emplacement de stockage Azure qui contient des événements de jeu bruts, des événements de jeu partitionnés, des événements de jeu enrichis, des informations d’efficacité de la campagne marketing, des données géocodées de référence et des données de campagne marketing de référence dans LogProcessingFactory.   
	- 	**AzureSqlLinkedService**. Lie une base de données SQL Azure qui contient des informations d’efficacité de la campagne marketing. 
	- 	**HDInsightStorageLinkedService**. Lie un stockage d’objets blob Azure qui est associé au cluster HDInsight qui désigne HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Lie un cluster Azure HDInsight à LogProcessingFactory. Ce cluster permet d’effectuer un traitement pig/hive sur les données. 
 		
4. [Étape 4 : Créer des services liés](#MainStep4). Dans cette étape, vous allez créer les tables suivantes :
	
	- **RawGameEventsTable**. Cette table spécifie l’emplacement des données d’événement de jeu brutes dans le stockage d’objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Cette table spécifie l’emplacement des données d’événement de jeu partitionnées dans le stockage d’objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Cette table spécifie l’emplacement des données géocodées de référence dans le stockage d’objets blob Azure défini par StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Cette table spécifie l’emplacement des données de campagne marketing de référence dans le stockage d’objets blob Azure défini par StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Cette table spécifie l’emplacement des données d’événement de jeu enrichies dans le stockage d’objets blob Azure défini par StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Cette table spécifie la table SQL (MarketingCampaignEffectiveness) de la base de données SQL Microsoft Azure définie par AzureSqlLinkedService qui contient les données d’efficacité de la campagne marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Cette table spécifie l’emplacement des données d’efficacité de la campagne marketing dans le stockage d’objets blob Azure défini par StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Étape 5 : Créer et planifier des pipelines](#MainStep5). Dans cette étape, vous allez créer les pipelines suivants :
	- **PartitionGameLogsPipeline**. Ce pipeline lit les événements de jeu bruts depuis un stockage d’objets blob (RawGameEventsTable) et crée des partitions basées sur l’année, le mois et le jour (PartitionedGameEventsTable). 


		![Pipeline PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Ce pipeline joint les événements de jeu partitionnés (table PartitionedGameEvents, qui est une sortie de PartitionGameLogsPipeline) avec un code géographique (RefGetoCodeDictionaryTable) et enrichit les données en mappant une adresse IP à la géolocalisation correspondante (EnrichedGameEventsTable).

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Ce pipeline exploite les données enrichies (EnrichedGameEventTable produite par EnrichGameLogsPipeline) et les traite avec les données de publicité (RefMarketingCampaignnTable) pour créer la sortie finale d’efficacité de la campagne marketing, qui est copiée dans la base de données SQL Azure (MarketingCampainEffectivensessSQLTable) et un stockage d’objets blob Azure (MarketingCampaignEffectivenessBlobTable) pour analyse.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Étape 6 : Surveiller des pipelines et des tranches de données](#MainStep6). Dans cette étape, vous allez surveiller les pipelines, les tables et les tranches de données à l’aide du portail Azure.

## <a name="MainStep1"></a> Étape 1 : Télécharger des exemples de données et des scripts
Dans cette étape, vous allez télécharger tous les exemples de données (y compris l’ensemble des journaux et des données de référence) et les scripts Hive/Pig qui seront appelés par les flux de travail. Les scripts que vous exécutez créent également une base de données SQL Microsoft Azure nommée **MarketingCampaigns**}, des tables, des types définis par l’utilisateur et des procédures stockées.

Les tables, les types définis par l’utilisateur et les procédures stockées sont utilisés lors du déplacement des résultats de l’efficacité de la campagne marketing depuis le stockage d’objets blob Azure vers la base de données SQL Azure.

1. Ouvrez le fichier **uploadSampleDataAndScripts.ps1** figurant dans le dossier **C:\\ADFWalkthrough** (ou dans celui qui contient les fichiers extraits) dans votre éditeur favori, remplacez l’élément en surbrillance par vos informations de cluster et enregistrez le fichier.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	Pour ce script, l'utilitaire sqlcmd doit être installé sur votre ordinateur. Si SQL Server est installé, l'utilitaire l'est également. Sinon, [téléchargez][sqlcmd-install] et installez l'utilitaire.
	
	Vous pouvez également utiliser les fichiers du dossier : C:\\ADFWalkthrough\\Scripts pour télécharger les scripts pig/hive et des exemples de fichiers dans le conteneur adfwalkthrough du stockage d’objets blob, et créer la table MarketingCampaignEffectiveness dans la base de données SQL Azure MarketingCampaigns.
   
2. Vérifiez que votre ordinateur local est autorisé à accéder à la base de données SQL Azure. Pour activer l’accès, utilisez le **portail de gestion Microsoft Azure** ou l’élément **sp\_set\_firewall\_rule** sur la base de données MASTER pour créer une règle de pare-feu pour l’adresse IP de votre ordinateur. Cela peut prendre jusqu’à cinq minutes pour que cette modification prenne effet. Voir [Définition des règles de pare-feu pour Azure SQL][azure-sql-firewall].
4. Dans Azure PowerShell, accédez à l’emplacement auquel vous avez extrait les exemples (par exemple, **C:\\ADFWalkthrough**).
5. Exécutez le fichier **uploadSampleDataAndScripts.ps1**. 
6. Une fois que le script s’exécute correctement, vous verrez les éléments suivants :

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

## <a name="MainStep2"></a> Étape 2 : Créer une fabrique de données Microsoft Azure
Dans cette étape, vous allez créer une fabrique de données Microsoft Azure nommée **LogProcessingFactory**.

1.	Une fois connecté au [portail Azure en version préliminaire][azure-preview-portal], cliquez sur l’option **NOUVEAU** dans l’angle inférieur gauche de la fenêtre, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Data Factory** dans le panneau **Analyse de données**. 

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]

5. Dans le panneau **Nouvelle fabrique de données**, saisissez **LogProcessingFactory** dans le champ **Nom**.

	![Panneau Data Factory][image-data-factory-tutorial-new-datafactory-blade]

6. Si vous n’avez pas encore créé de groupe de ressources Microsoft Azure nommé **ADF**, procédez comme suit :
	1. Cliquez sur **NOM DU GROUPE DE RESSOURCES**, puis sur **Créer un groupe de ressources**.
	
		![Panneau Groupe de ressources][image-data-factory-tutorial-resourcegroup-blade]
	2. Dans le panneau **Créer un groupe de ressources**, saisissez **ADF** dans le champ du nom du groupe de ressources, puis cliquez sur **OK**.
	
		![Créer un groupe de ressources][image-data-factory-tutorial-create-resourcegroup]
7. Sélectionnez **ADF** dans le champ **NOM DU GROUPE DE RESSOURCES**.  
8.	Dans le panneau **Nouvelle fabrique de données**, notez que l’option **Ajouter au tableau d’accueil** est sélectionnée par défaut. Cela ajoute un lien vers la fabrique de données sur le tableau d’accueil (ce que vous voyez lorsque vous vous connectez à la version préliminaire du portail Azure).

	![Panneau Créer une fabrique de données][image-data-factory-tutorial-create-datafactory]

9.	Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer** pour créer la fabrique de données.
10.	Lorsque la fabrique de données est créée, vous voyez apparaître le panneau **DATA FACTORY** intitulé **LogProcessingFactory**.

	![Page d’accueil Data Factory][image-data-factory-tutorial-datafactory-homepage]

	
	Si vous ne le voyez pas, effectuez l’une des opérations suivantes :

	- Cliquez sur **LogProcessingFactory** sur le **tableau d’accueil** (page d’accueil).
	- Cliquez sur **PARCOURIR**, à gauche, sur **Tout**, sur **Fabriques de données**, puis sur la fabrique de données.
 
	Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche : **Le nom de fabrique de données LogProcessingFactory n’est pas disponible**, modifiez le nom (par exemple, votrenomLogProcessingFactory). Utilisez ce nom à la place de « LogProcessingFactory » lorsque vous effectuez les étapes de ce didacticiel.
 
## <a name="MainStep3"></a> Étape 3 : Créer des services liés

> [AZURE.NOTE]Cet article utilise le portail Azure, en particulier Data Factory Editor, pour créer des pipelines, des tables et des services liés. Consultez le [didacticiel utilisant Azure PowerShell][adftutorial-using-powershell] si vous souhaitez suivre ce didacticiel à l’aide de Microsoft Azure PowerShell.

Dans cette étape, vous allez créer les services liés suivants :

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Création des services StorageLinkedService et HDInsightStorageLinkedService

1.	Dans le panneau **DATA FACTORY**, cliquez sur la vignette **Créer et déployer** pour lancer l’**éditeur** de la fabrique de données.

	![Vignette Créer et déployer][image-author-deploy-tile]

	Pour obtenir une présentation détaillée de Data Factory Editor, consultez la rubrique [Data Factory Editor][data-factory-editor].

2.  Dans l’**éditeur**, cliquez sur le bouton **Nouveau magasin de données** de la barre d’outils, puis sélectionnez **Stockage Azure** dans le menu déroulant. Le modèle JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans le volet droit.
	
	![Éditeur - Bouton Nouveau magasin de données][image-editor-newdatastore-button]

3. Remplacez les éléments **nom\_compte** et **clé\_compte** par le nom du compte et les valeurs de clé de compte de votre compte Microsoft Azure Storage.

	![Éditeur - Stockage d’objets blob - JSON][image-editor-blob-storage-json]
	
	Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Cliquez sur l’option **Déployer** de la barre d’outils pour déployer le service lié StorageLinkedService. Vérifiez que le message **SERVICE LIÉ CRÉÉ AVEC SUCCÈS** s’affiche dans la barre de titre.

	![Éditeur - Stockage d’objets blob - Déployer][image-editor-blob-storage-deploy]

5. Répétez ces étapes pour créer un autre service lié Microsoft Azure Storage, nommé **HDInsightStorageLinkedService**, pour le stockage associé à votre cluster HDInsight. Dans le script JSON du service lié, remplacez la valeur de la propriété **name** par **HDInsightStorageLinkedService**.

### Création du service AzureSqlLinkedService
1. Dans **Data Factory Editor**, cliquez sur le bouton **Nouveau magasin de données** de la barre d’outils, puis sélectionnez **Base de données SQL Azure** dans le menu déroulant. Le modèle JSON pour la création du service lié SQL Azure doit apparaître dans le volet droit.
2. Remplacez les éléments **nom\_serveur**, ****username@servername** et **password** par le nom de votre serveur SQL Azure, le nom du compte d’utilisateur et le mot de passe associé.
3. Remplacez l’élément **nom\_BD** par **MarketingCampaigns**. Il s’agit de la base de données SQL Microsoft Azure créée par les scripts que vous avez exécutés à l’étape 1. Vous devez confirmer que cette base de données a bien été créée par les scripts (en cas d’erreurs). 
3. Cliquez sur l’option **Déployer** de la barre d’outils pour créer et déployer le service AzureSqlLinkedService.

### Création du service HDInsightLinkedService
Le service Azure Data Factory prend en charge la création d’un cluster à la demande et l’utilise pour traiter des données d’entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, lorsque vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l’exemple, nous allons utiliser un cluster à la demande.

#### Pour utiliser un cluster HDInsight à la demande
1. Cliquez sur l’option **Nouveau calcul** de la barre de commandes et sélectionnez **Cluster HDInsight à la demande** dans le menu.
2. Dans le script JSON, procédez comme suit : 
	1. Pour la propriété **clusterSize**, spécifiez la taille du cluster HDInsight.
	2. Pour la propriété **jobsContainer**, spécifiez le nom du conteneur par défaut dans lequel les journaux de cluster seront stockés. Pour les besoins de ce didacticiel, spécifiez **adfjobscontainer**.
	3. Pour la propriété **timeToLive**, spécifiez la durée pendant laquelle le client peut être inactif avant d’être supprimé. 
	4. Pour la propriété **version**, spécifiez la version de Microsoft Azure HDInsight que vous souhaitez utiliser. Si vous excluez cette propriété, la version la plus récente est utilisée.  
	5. Pour **linkedServiceName**, spécifiez le service lié **HDInsightStorageLinkedService** que vous avez créé dans le didacticiel de prise en main. 

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

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.
   
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Cliquez sur l’option **Nouveau calcul** de la barre de commandes et sélectionnez **Cluster HDInsight** dans le menu.
2. Dans le script JSON, procédez comme suit : 
	1. Pour la propriété **clusterUri**, saisissez l’URL de votre cluster HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/.     
	2. Pour la propriété **UserName**, saisissez le nom d’utilisateur ayant accès au cluster HDInsight.
	3. Pour la propriété **Password**, spécifiez le mot de passe de l’utilisateur. 
	4. Pour la propriété **LinkedServiceName**, saisissez **StorageLinkedService**. Il s’agit du service lié que vous avez créé lors du didacticiel de prise en main. 

	Notez que le **type** du service lié est défini sur **HDInsightBYOCLinkedService** (« BYOC » signifiant « Bring Your Own Cluster », ou « fournissez votre propre cluster »).

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.


## <a name="MainStep4"></a> Étape 4 : Créer des tables
 
Dans cette étape, vous allez créer les tables Data Factory suivantes :

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Didacticiel Flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]
 
L’image ci-dessus présente les pipelines sur la ligne du milieu et les tables sur les lignes supérieures et inférieures.

### Créer des tables
	
1. Dans l’**éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** de la barre d’outils et sélectionnez **Stockage d’objets blob Azure** dans le menu déroulant. 
2. Remplacez le script JSON du volet de droite par le script JSON du fichier **RawGameEventsTable.json**, dans le dossier **C:\\ADFWalkthrough\\Tables**.
3. Cliquez sur l’option **Déployer** de la barre d’outils pour créer et déployer la table. Vérifiez que le message **TABLE CORRECTEMENT CRÉÉE** s’affiche sur la barre de titre de l’éditeur.
4. Répétez les étapes 1 à 3 pour le contenu provenant des fichiers suivants : 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Répétez les étapes 1 à 3 pour le contenu provenant du fichier suivant : N’OUBLIEZ PAS DE sélectionner **Azure Sql** après avoir cliqué sur **Nouveau jeu de données**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Étape 5 : Créer et planifier des pipelines
Dans cette étape, vous allez créer les pipelines suivants :

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Pour créer des pipelines

1. Dans **Data Factory Editor**, cliquez sur **Nouveau pipeline** dans la barre d’outils. Si ce bouton n’est pas affiché dans la barre d’outils, cliquez sur **... (points de suspension)**. Vous pouvez également cliquer sur **Pipelines** dans l’arborescence, puis sur **Nouveau pipeline**.
2. Remplacez le script JSON du volet de droite par le script JSON du fichier **PartitionGameLogsPipeline.json** dans le dossier **C:\\ADFWalkthrough\\Pipelines**.
3. Ajoutez une **virgule (« , »)** à la fin du **crochet fermant (« ] »)** dans le script JSON puis ajoutez les trois lignes suivantes après le crochet fermant. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Notez que les heures de début et de fin sont définies sur 05/01/2014 et 05/05/2014, car les données d’exemple de cette procédure pas à pas vont du 05/01/2014 au 05/05/2014.
 
3. Cliquez sur **Déployer** sur la barre d’outils pour déployer le pipeline. Vérifiez que le message **PIPELINE CRÉÉ AVEC SUCCÈS** s’affiche sur la barre de titre de l’éditeur.
4. Répétez les étapes 1 à 3 pour le contenu provenant des fichiers suivants : 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Fermez les panneaux Data Factory en appuyant sur **X** (en haut à droite) pour afficher la page d’accueil (panneau **DATA FACTORY **) de votre fabrique de données.

### Vue schématique

1. Dans le panneau **DATA FACTORY** de **LogProcessingFactory**, cliquez sur **Diagramme**. 

	![Lien Diagramme][image-data-factory-tutorial-diagram-link]

2. Vous pouvez réorganiser le diagramme affiché, comme dans le diagramme ci-après qui affiche les entrées directes en haut et les sorties en bas. Vous pouvez voir que la sortie de **PartitionGameLogsPipeline** est transmise en tant qu’entrée à l’élément EnrichGameLogsPipeline et que la sortie de **EnrichGameLogsPipeline** est transmise à **AnalyzeMarketingCampaignPipeline**. Double-cliquez sur un titre pour afficher des détails sur l’objet représenté par le panneau.

	![Vue schématique][image-data-factory-tutorial-diagram-view]

3. Cliquez avec le bouton droit de la souris sur **AnalyzeMarketingCampaignPipeline**, puis sélectionnez **Ouvrir le pipeline**. Vous devez voir apparaître toutes les activités dans le pipeline, ainsi que des jeux de données d’entrée et de sortie pour les activités.
 
	![Ouvrir un pipeline](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Cliquez sur l’option **Data Factory** de la barre de navigation figurant dans l’angle supérieur gauche de la fenêtre pour revenir à la vue schématique incluant tous les pipelines.


**Félicitations !** Vous avez créé la fabrique de données Azure, des services liés, des pipelines et des tables, et démarré le flux de travail.


## <a name="MainStep6"></a> Étape 6 : Surveiller des pipelines et des tranches de données 

1.	Si le panneau **DATA FACTORY** de **LogProcessingFactory** n’est pas ouvert, vous pouvez effectuer l’une des opérations suivantes :
	1.	Cliquez sur **LogProcessingFactory** dans le **tableau d’accueil**. Lors de la création de la fabrique de données, l’option **Ajouter au tableau d’accueil** a été automatiquement activée.

		![Tableau d’accueil Surveillance][image-data-factory-monitoring-startboard]

	2. Cliquez sur le concentrateur **PARCOURIR**, puis sur **Tout**.
	 	
		![Concentrateur Surveillance - Tout][image-data-factory-monitoring-hub-everything]

		Dans le panneau **Parcourir**, sélectionnez **Fabriques de données**, puis **LogProcessingFactory** dans le panneau **Fabriques de données**.

		![Surveillance - Parcourir les fabriques de données][image-data-factory-monitoring-browse-datafactories]
2. Vous pouvez surveiller votre fabrique de données de plusieurs façons. Vous pouvez démarrer avec les pipelines ou les jeux de données. Commençons par les pipelines avant d’aller plus loin. 
3.	Cliquez sur **Pipelines** dans le panneau **DATA FACTORY**. 
4.	Cliquez sur **PartitionGameLogsPipeline** dans le panneau Pipelines. 
5.	Dans le panneau **PIPELINE** de **PartitionGameLogsPipeline**, vous voyez que le pipeline consomme le jeu de données **RawGameEventsTable**. Cliquez sur **RawGameEventsTable**.

	![Pipelines consommés et produits][image-data-factory-monitoring-pipeline-consumed-produced]

6. Dans le panneau TABLE de **RawGameEventsTable**, vous pouvez voir toutes les tranches. Dans la capture d’écran suivante, toutes les tranches sont à l’état **Prêt**, et il n’y a aucune tranche problématique. Cela signifie que les données sont prêtes à être traitées.

	![Panneau TABLE RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** sont triées en fonction de l’**HEURE DE LA DERNIÈRE MISE À JOUR**. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.

	-  Vous mettez à jour l’état de la tranche manuellement, par exemple, à l’aide de **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
	-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).
 
	Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches. Cliquez sur l’option **Filtre** de la barre d’outils pour filtrer les tranches.
	
	Pour afficher les tranches de données triées en fonction des heures de début et de fin de tranche, cliquez sur la vignette **Tranches de données (par heure de tranche)**.

	![Tranches de données par heure de tranche][DataSlicesBySliceTime]
 
7. Dans le panneau **PIPELINE** de **PartitionGameLogsPipeline**, cliquez sur **Produit**.
8. Vous devez voir la liste des jeux de données produits par ce pipeline : 
9. Cliquez sur la table **PartitionedGameEvents** du panneau **Jeux de données produits**. 
10.	Vérifiez que l’**état** de toutes les tranches est défini sur **Prêt**. 
11.	Cliquez sur l’une des tranches à l’état **Prêt** pour voir le panneau **TRANCHE DE DONNÉES** correspondant.

	![Panneau TRANCHE DE DONNÉES RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	En cas d’erreur, vous voyez l’état **Échec **ici. Vous pouvez aussi voir les deux tranches à l’état **Prêt** ou à l’état **PendingValidation**, en fonction de la vitesse de traitement des tranches.

	Si la tranche n’a pas l’état **Prêt**, vous pouvez afficher les tranches en amont qui ne sont pas prêtes et qui empêchent l’exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**.
 
	Pour comprendre tous les états possibles des tranches, reportez-vous à la rubrique [Référence du développeur Azure Data Factory][developer-reference].

12.	Dans le panneau **TRANCHE DE DONNÉES**, cliquez sur l’exécution dans la liste **Exécutions d’activité**. Vous devez voir le panneau Exécution d’activité pour cette tranche. Vous devez voir apparaître le panneau **DÉTAILS SUR L’EXÉCUTION D’ACTIVITÉ**.

	![Panneau Détails sur l’exécution d’activité][image-data-factory-monitoring-activity-run-details]

13.	Cliquez sur **Télécharger** pour télécharger les fichiers. Cet écran est particulièrement utile lorsque vous dépannez des erreurs de traitement HDInsight.
	 
	
Lorsque tous les pipelines ont terminé leur exécution, vous pouvez consulter les résultats dans l’élément **MarketingCampaignEffectivenessTable** de la base de données SQL Azure **MarketingCampaigns**.

**Félicitations !** Vous pouvez désormais surveiller et dépanner les flux de travail. Vous avez appris comment utiliser Azure Data Factory pour traiter les données et obtenir des analyses.

## Étendre le didacticiel pour utiliser des données locales
Dans la dernière étape du scénario de traitement de journal de la procédure de cet article, la sortie de l’efficacité de la campagne marketing a été copiée dans une base de données SQL Azure. Vous pouvez également déplacer ces données vers une base de données SQL Server locale pour l’analyser au sein de votre organisation.
 
Pour copier les données d’efficacité de la campagne marketing depuis un objet blob Microsoft Azure vers une base de données SQL Server locale, vous devez créer en plus les service lié, table et pipeline locaux présentés dans la procédure pas à pas de cet article.

Suivez la [procédure pas à pas sur l’utilisation d’une source de données locale][tutorial-onpremises] pour apprendre à créer un pipeline afin de copier les données de l’efficacité des campagnes marketing dans une base de données SQL Server locale.

## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-tutorial.md).

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


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

<!---HONumber=Sept15_HO2-->