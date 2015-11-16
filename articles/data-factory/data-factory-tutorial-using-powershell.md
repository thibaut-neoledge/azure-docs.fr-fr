<properties 
	pageTitle="Déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory (Azure PowerShell)" 
	description="Ce didacticiel avancé décrit un scénario proche de la réalité et l’implémente à l’aide du service Azure Data Factory et d'Azure PowerShell." 
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# Didacticiel : déplacer et traiter des fichiers journaux à l’aide de Data Factory [PowerShell]
Cet article fournit une procédure pas à pas pour un scénario de traitement de journaux à l’aide d’Azure Data Factory pour transformer les données des fichiers journaux en informations.

> [AZURE.IMPORTANT]cet article ne couvre pas toutes les applets de commande Data Factory. Consultez la [référence des applets de commande Data Factory][cmdlet-reference] pour obtenir une documentation complète sur les applets de commande Data Factory.
>    
> Si vous utilisez la version préliminaire d'Azure PowerShell 1.0, vous devez utiliser les applets de commande documentées [ici](https://msdn.microsoft.com/library/dn820234.aspx). Par exemple, utilisez New-AzureRMDataFactory au lieu de New-AzureDataFactory.

## Scénario
Contoso est une société qui crée des jeux pour plusieurs plateformes : des consoles de jeux, des appareils portatifs et des ordinateurs personnels (PC). Chacun de ces jeux produit une très grande quantité de journaux. L’objectif de Contoso est de collecter et d’analyser les journaux générés par ces jeux pour obtenir des informations sur l’utilisation, identifier les opportunités de vente incitative et de vente croisée, développer de nouvelles fonctionnalités intéressantes, etc. pour optimiser vos affaires et fournir une meilleure expérience aux clients.
 
Dans cette procédure pas à pas, nous allons collecter des exemples de journaux, nous allons les traiter et les enrichir avec des données de référence et transformer les données pour évaluer l’efficacité d’une campagne marketing lancée dernièrement par Contoso.

## Préparation pour le didacticiel
1.	Lisez la page [Présentation d’Azure Data Factory][adfintroduction] pour obtenir une vue d’ensemble de Microsoft Azure Data Factory et comprendre les concepts principaux.
2.	Pour suivre ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d’abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d’évaluation gratuite][azure-free-trial].
3.	Vous devez télécharger et installer [Azure PowerShell][download-azure-powershell] sur votre ordinateur. 
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
Le flux de travail de bout en bout est représenté ci-dessous : ![Didacticiel Flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]

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
Dans cette étape, vous allez créer une fabrique de données Microsoft Azure nommée **LogProcessingFactory**.

1.	Une fois connecté au [portail Azure en version préliminaire][azure-preview-portal], cliquez dans le coin inférieur gauche sur **NOUVEAU**, puis sur **Fabrique de données** dans le panneau **Nouveau**. 

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Si l’élément **Fabrique de données** ne s’affiche pas dans le panneau **Nouveau**, faites défiler la fenêtre vers le bas.
	
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

> [AZURE.NOTE]Cet article utilise Microsoft Azure PowerShell pour créer des pipelines, des tables et des services liés. Consultez le didacticiel sur l’[utilisation de l’éditeur Data Factory][adftutorial-using-editor] si vous souhaitez effectuer ce didacticiel à l’aide du portail Microsoft Azure, et plus spécifiquement de l’éditeur Data Factory.

Dans cette étape, vous allez créer les services liés suivants : StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService et HDInsightLinkedService.


1.	Dans le panneau **LogProcessingFactory**, cliquez sur la vignette **Services liés**.

	![Vignette Services liés][image-data-factory-tutorial-linkedservice-tile]

2. Dans le panneau **Services liés**, cliquez sur **+ Magasin de données** dans la barre de commandes.

	![Services liés - Ajouter au magasin][image-data-factory-tutorial-linkedservices-add-datstore]

3. Dans le panneau **Nouveau magasin de données**, saisissez **StorageLinkedService** dans le champ **Nom**, cliquez sur **TYPE (paramètres requis)**, puis sélectionnez **Compte de stockage Azure**.

	![Type de magasin de données - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. Dans le panneau **Nouveau magasin de données**, vous voyez apparaître deux nouveaux champs : **Nom de compte** et **Clé de compte**. Saisissez le nom et la clé de votre **compte Microsoft Azure Storage**.

	![Paramètres Azure Storage][image-data-factory-tutorial-azurestorage-settings]

	Vous pouvez obtenir le nom et la clé de votre compte de stockage Azure à partir du portail, comme indiqué ci-après :

	![Clé de stockage][image-data-factory-tutorial-storage-key]
  
5. Après avoir cliqué sur **OK** dans le panneau Nouveau magasin de données, vous devez voir apparaître **StorageLinkedService** dans la liste **MAGASINS DE DONNÉES** du panneau **Services liés**. Consultez le concentrateur **NOTIFICATIONS** (sur la gauche) pour voir les messages.

	![Panneau Services liés avec Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Répétez les **étapes 2 à 5** pour créer un autre service lié nommé **HDInsightStorageLinkedService**. Il s’agit du stockage utilisé par votre cluster HDInsight.
7. Confirmez que vous voyez s’afficher **StorageLinkedService** et **HDInsightStorageLinkedService** dans la liste du panneau Services liés.
8. Dans le panneau **Services liés**, cliquez sur **Ajouter (+) Magasin de données** dans la barre de commandes.
9. Entrez le nom **AzureSqlLinkedService**.
10. Cliquez sur **TYPE (paramètres requis)** et sélectionnez **Base de données SQL Azure**.
11. Maintenant, vous devez voir apparaître les champs supplémentaires suivants dans le panneau **Nouveau magasin de données**. Saisissez le nom du **serveur** de la base de données SQL Azure, le nom de la **base de données**, le **nom de l’utilisateur** et le **mot de passe**, puis cliquez sur **OK**.
	1. Saisissez **MarketingCampaigns** dans le champ **Base de données**. Il s’agit de la base de données SQL Azure créée par les scripts que vous avez exécutés à l’étape 1. Vous devez confirmer que cette base de données a bien été créée par les scripts (en cas d’erreurs).
		
 		![Paramètres SQL Azure][image-data-factory-tutorial-azuresql-settings]

		Pour obtenir ces valeurs à partir du portail de gestion Azure, cliquez sur Afficher les chaînes de connexion de la base de données SQL pour la base de données MarketingCampaigns.

		![Chaîne de connexion Base de données SQL Azure][image-data-factory-tutorial-azuresql-database-connection-string]

12. Vérifiez que les trois magasins de données que vous avez créés s’affichent : **StorageLinkedService**, **HDInsightStorageLinkedService** et **AzureSqlLinkedService**.
13. Vous devez créer un autre service lié, mais cette fois il s’agit d’un service de calcul, précisément du **cluster HDInsight Azure**. Le portail ne prend pas encore en charge la création d’un service de calcul lié. Par conséquent, vous devez utiliser Azure PowerShell pour ce faire. 
14. Accédez à **Azure PowerShell** s’il est déjà ouvert ; sinon, démarrez **Azure PowerShell**. Si vous avez fermé et rouvert Azure PowerShell, vous devez exécuter les commandes suivantes : 
	- Exécutez **Add-AzureAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Microsoft Azure en version préliminaire.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire. 
15. Passez en mode **AzureResourceManager**, car les applets de commande Azure Data Factory sont disponibles uniquement dans ce mode.

		Switch-AzureMode AzureResourceManager

16. Accédez au sous-dossier **LinkedServices** dans **C:\\ADFWalkthrough** ou dans le dossier de l’emplacement auquel vous avez extrait les fichiers.
17. Ouvrez l’élément **HDInsightLinkedService.json** dans votre éditeur favori et notez que le type est défini sur **HDInsightOnDemandLinkedService**.


	Le service Azure Data Factory prend en charge la création d’un cluster à la demande et l’utilise pour traiter des données d’entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, lorsque vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l’exemple, nous allons utiliser un cluster à la demande.
	
	L’élément HDInsightLinkedService lie un cluster HDInsight à la demande à la fabrique de données. Pour utiliser votre propre cluster HDInsight, mettez à jour la section Propriétés du fichier HDInsightLinkedService.json, comme illustré ci-dessous (remplacez clustername, username et password par les valeurs appropriées) :
	
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
19. Utilisez l’applet de commande **New-AzureDataFactoryLinkedService** pour créer un service lié, comme suit. Commencez par le compte de stockage :

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Si vous utilisez un nom différent pour ResourceGroupName, DataFactoryName ou LinkedService, indiquez-les dans la cmdlet ci-dessus. Vous devez également fournir le chemin d’accès complet du fichier JSON du service lié si le fichier est introuvable.
20. Vous devez voir apparaître les quatre services liés dans le panneau **Services liés**, comme illustré ci-après. Si le panneau Services liés n’est pas ouvert, cliquez sur Services liés dans la page **DATA FACTORY** de **LogProcessingFactory**. L’actualisation du panneau des services liés peut prendre quelques secondes.

	![Services liés - Tout][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Étape 4 : Créer des services liés 
Dans cette étape, vous allez créer les tables suivantes :

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Didacticiel Flux de bout en bout][image-data-factory-tutorial-end-to-end-flow]
 
L’image ci-dessus présente les pipelines sur la ligne du milieu et les tables sur les lignes supérieures et inférieures.

Le portail Azure n’autorise pas encore la création de jeux de données/tables, vous devez donc utiliser Azure PowerShell pour créer des tables dans cette version.

### Créer des tables

1.	Dans Azure PowerShell, accédez au dossier **Tables** (**C:\\ADFWalkthrough\\Tables**) depuis l’emplacement auquel vous avez extrait les exemples.
2.	Utilisez l'applet de commande **New-AzureDataFactoryDataset** pour créer les jeux de données pour **RawGameEventsTable.json** comme suit.	


		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Si vous utilisez un nom différent pour ResourceGroupName et DataFactoryName, indiquez-les dans la cmdlet ci-dessus. En outre, indiquez le chemin d’accès complet du fichier JSON de la table si la cmdlet ne trouve pas le fichier.

3. Répétez l’étape précédente pour créer les tables suivantes :
		
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. Dans le **portail Azure en version préliminaire**, cliquez sur **Jeux de données** dans le panneau **FABRIQUES DE DONNÉES** pour **LogProcessingFactory** et vérifiez que vous voyez apparaître tous les jeux de données (les tables sont des jeux de données rectangulaires).

	![Jeux de données - Tout][image-data-factory-tutorial-datasets-all]

	Vous pouvez également utiliser la commande suivante à partir d’Azure PowerShell :
			
		Get-AzureDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Étape 5 : Créer et planifier des pipelines
Dans cette étape, vous allez créer les pipelines suivants : PartitionGameLogsPipeline, EnrichGameLogsPipeline et AnalyzeMarketingCampaignPipeline.

1. Dans l’**Explorateur Windows**, accédez au sous-dossier **Pipelines** dans le dossier **C:\\ADFWalkthrough** (ou à partir de l’emplacement auquel vous avez extrait les exemples).
2.	Ouvrez l’élément **PartitionGameLogsPipeline.json** dans votre éditeur favori, remplacez l’élément en surbrillance par vos informations de compte de stockage pour le compte de stockage de données et enregistrez le fichier.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Répétez l’étape pour créer les pipelines suivants :
	1. **EnrichGameLogsPipeline.json** (3 occurrences)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 occurrences)

	**IMPORTANT : ** vérifiez que vous avez remplacé toutes les valeurs <storageaccountname> par le nom de votre compte de stockage.
 
4.  Dans **Azure PowerShell**, accédez au sous-dossier **Pipelines** dans le dossier **C:\\ADFWalkthrough** (ou à partir de l’emplacement auquel vous avez extrait les exemples).
5.  Utilisez l’applet de commande **New-AzureDataFactoryPipeline** pour créer les pipelines pour **PartitionGameLogspeline.json**, comme suit.	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Si vous utilisez un nom différent pour ResourceGroupName, DataFactoryName ou le pipeline, indiquez-les dans la cmdlet ci-dessus. Fournissez également le chemin d’accès complet au fichier JSON de pipeline.
6. Répétez l’étape précédente pour créer les pipelines suivants :
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Utilisez l’applet de commande **Get-AzureDataFactoryPipeline** pour obtenir la liste des pipelines.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Une fois les pipelines créés, vous pouvez spécifier la durée de traitement des données. En spécifiant la période active pour un pipeline, vous définissez la durée de traitement des tranches de données en fonction des propriétés de disponibilité qui ont été définies pour chaque table ADF.

Pour spécifier la période active pour le pipeline, vous pouvez utiliser la cmdlet Set-AzureDataFactoryPipelineActivePeriod. Dans cette procédure pas à pas, les exemples de données concernent la période du 1er mai au 5 mai. Utilisez la valeur 2014-05-01 pour le paramètre StartDateTime. La valeur EndDateTime est facultative.
			
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

11. Dans le **portail Azure en version préliminaire**, cliquez sur la vignette **Pipelines** (et non sur le nom des pipelines) dans le panneau **DATA FACTORY** de **LogProcessingFactory**. Vous devez voir apparaître les pipelines que vous avez créés.

	![Tous les pipelines][image-data-factory-tutorial-pipelines-all]

12. Dans le panneau **DATA FACTORY** de **LogProcessingFactory**, cliquez sur **Diagramme**.

	![Lien Diagramme][image-data-factory-tutorial-diagram-link]

13. Vous pouvez réorganiser le diagramme affiché, comme dans le diagramme ci-après qui affiche les entrées directes en haut et les sorties en bas. Vous pouvez voir que la sortie de **PartitionGameLogsPipeline** est transmise en tant qu’entrée à l’élément EnrichGameLogsPipeline et que la sortie de **EnrichGameLogsPipeline** est transmise à **AnalyzeMarketingCampaignPipeline**. Double-cliquez sur un titre pour afficher des détails sur l’objet représenté par le panneau.

	![Vue schématique][image-data-factory-tutorial-diagram-view]

	**Félicitations !** Vous avez créé la fabrique de données Azure, des services liés, des pipelines et des tables, et démarré le flux de travail.


## <a name="MainStep6"></a> Étape 6 : Surveiller des pipelines et des tranches de données 

1.	Si le panneau FABRIQUE DE DONNÉES pour LogProcessingFactory n’est pas ouvert, vous pouvez effectuer l’une des opérations suivantes :
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
 
7. Dans le panneau **PIPELINE** de **PartitionGameLogsPipeline**, cliquez sur **Produit**.
8. Vous devez voir la liste des jeux de données produits par ce pipeline : 
9. Cliquez sur la table **PartitionedGameEvents** du panneau **Jeux de données produits**. 
10.	Vérifiez que l’**état** de toutes les tranches est défini sur **Prêt**. 
11.	Cliquez sur l’une des tranches à l’état **Prêt** pour voir le panneau **TRANCHE DE DONNÉES** correspondant.

	![Panneau TRANCHE DE DONNÉES RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	En cas d’erreur, vous voyez l’état **Échec **ici. Vous pouvez aussi voir les deux tranches à l'état **Prêt** ou à l'état **PendingValidation**, en fonction de la vitesse de traitement des tranches.
 
	Pour comprendre tous les états possibles des tranches, reportez-vous à la rubrique [Référence du développeur Azure Data Factory][developer-reference].

12.	Dans le panneau **TRANCHE DE DONNÉES**, cliquez sur l’exécution dans la liste **Exécutions d’activité**. Vous devez voir le panneau Exécution d’activité pour cette tranche. Vous devez voir apparaître le panneau **DÉTAILS SUR L’EXÉCUTION D’ACTIVITÉ**.

	![Panneau Détails sur l’exécution d’activité][image-data-factory-monitoring-activity-run-details]

13.	Cliquez sur **Télécharger** pour télécharger les fichiers. Cet écran est particulièrement utile lorsque vous dépannez des erreurs de traitement HDInsight.
	 
	
Lorsque tous les pipelines ont terminé leur exécution, vous pouvez consulter les résultats dans l’élément **MarketingCampaignEffectivenessTable** de la base de données SQL Azure **MarketingCampaigns**.

**Félicitations !** Vous pouvez désormais surveiller et dépanner les flux de travail. Vous avez appris comment utiliser Azure Data Factory pour traiter les données et obtenir des analyses.

## Étendre le didacticiel pour utiliser des données locales
Dans la dernière étape du scénario de traitement de journal de la procédure de cet article, la sortie de l’efficacité de la campagne marketing a été copiée dans une base de données SQL Azure. Vous pouvez également déplacer ces données vers une base de données SQL Server locale pour l’analyser au sein de votre organisation.
 
Pour copier les données d’efficacité de la campagne marketing depuis un objet blob Microsoft Azure vers une base de données SQL Server locale, vous devez créer en plus les service lié, table et pipeline locaux présentés dans la procédure pas à pas de cet article.

Suivez la [procédure pas à pas sur l’utilisation d’une source de données locale][tutorial-onpremises-using-powershell] pour apprendre à créer un pipeline afin de copier les données de l’efficacité des campagnes marketing dans une base de données SQL Server locale.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

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

<!---HONumber=Nov15_HO2-->