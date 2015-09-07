<properties 
	pageTitle="Copie des données de sortie du didacticiel vers une base de données SQL Server locale"
	description="La procédure pas à pas de ce didacticiel étend le didacticiel de fabrique des données pour copier des données sur l’efficacité d’une campagne marketing vers une base de données SQL Server locale."
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


# Procédure pas à pas : copier des données sur l’efficacité d’une campagne vers une base de données SQL Server locale. 
Dans cette procédure pas à pas, vous allez apprendre à configurer l’environnement permettant d’activer l’utilisation de vos données locales sur le pipeline.
 
Dans la dernière étape du scénario de traitement de journal, lors de la première procédure pas à pas avec Partition -> Enrichir -> Analyser le flux de travail, la sortie de l’efficacité de la campagne marketing a été copiée dans une base de données SQL Azure. Vous pouvez également déplacer ces données vers une base de données SQL Server locale pour l’analyser au sein de votre organisation.
 
Pour copier les données d’efficacité de la campagne marketing depuis un objet blob Azure vers une base de données SQL Server locale, vous devez créer en supplément un service lié local, une table et un pipeline utilisant le même jeu de cmdlets présenté dans la première procédure pas à pas.

## Configuration requise

Vous **devez** suivre la procédure pas à pas du didacticiel : [Déplacement et traitement des fichiers journaux à l’aide du service Azure Data Factory][datafactorytutorial] avant de suivre la procédure pas à pas de cet article.

**(recommandé)** Consultez et pratiquez la procédure pas à pas de l’article [Activation de vos pipelines pour les utiliser avec des données locales][useonpremisesdatasources] pour manier la création d’un pipeline servant à déplacer les données SQL Server locales vers un magasin d’objets blob Azure.


Dans cette procédure pas à pas, vous allez effectuer les étapes suivantes :

1. [Étape 1 : création d’un portail de gestion des données](#OnPremStep1). La passerelle de gestion des données est un agent client qui fournit l’accès aux sources de données locales de votre organisation à partir du cloud. La passerelle permet le transfert de données entre un serveur SQL Server local et des magasins de données Azure.	

	Vous devez avoir installé au moins une passerelle dans votre environnement d’entreprise et l’avoir enregistrée avec Azure Data Factory avant d’ajouter une base de données SQL Server locale comme un service lié à une fabrique de données Azure.

2. [Étape 2 : création d’un service lié pour le serveur SQL Server local](#OnPremStep2). Dans cette étape, vous créez d’abord une base de données et une table sur votre ordinateur SQL Server local, avant de créer le service lié : **OnPremSqlLinkedService**.
3. [Étape 3 : création d’une table et d’un pipeline](#OnPremStep3). Dans cette étape, vous allez créer une table **MarketingCampaignEffectivenessOnPremSQLTable** et un pipeline **EgressDataToOnPremPipeline**. 

4. [Étape 4 : surveillance du pipeline et affichage du résultat](#OnPremStep4). Dans cette étape, vous allez surveiller les pipelines, les tables et les tranches de données à l’aide du portail Azure.


## <a name="OnPremStep1"></a>Étape 1 : création d’un portail de gestion des données

La passerelle de gestion des données est un agent client qui fournit l’accès aux sources de données locales de votre organisation à partir du cloud. La passerelle permet le transfert de données entre un serveur SQL Server local et des magasins de données Azure.
  
Vous devez avoir installé au moins une passerelle dans votre environnement d’entreprise et l’avoir enregistrée avec Azure Data Factory avant d’ajouter une base de données SQL Server locale comme un service lié à une fabrique de données Azure.

Si vous disposez déjà d’une passerelle de données, ignorez cette étape.

1.	Créer une passerelle de données logique. Dans la **version préliminaire du portail Azure**, cliquez sur **Services liés** sur le volet **DATA FACTORY** de votre fabrique de données.
2.	Cliquez sur **Ajouter (+) Passerelle de données** sur la barre de commandes.  
3.	Dans le panneau **Nouvelle passerelle de données**, cliquez sur **CRÉER**.
4.	Dans le panneau **Créer**, entrez **MyGateway** comme **nom** pour la passerelle de données.
5.	Cliquez sur **SÉLECTIONNER UNE RÉGION** et modifiez-la au besoin. 
6.	Cliquez sur **OK** dans le volet **Créer**. 
7.	Vous devez voir le panneau **Configurer**. 
8.	Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cela permet de télécharger, d’installer et de configurer la passerelle sur votre ordinateur et d’inscrire la passerelle auprès du service. Si une passerelle est déjà installée sur votre ordinateur et que vous souhaitez créer un lien vers cette passerelle logique sur le portail, vous pouvez utiliser la clé sur ce panneau pour réinscrire votre passerelle à l’aide du Gestionnaire de configuration de la passerelle de gestion des données (version préliminaire).

	![Gestionnaire de configuration de la passerelle de gestion des données][image-data-factory-datamanagementgateway-configuration-manager]

9. Cliquez sur **OK** pour fermer le volet **Configurer** et sur **OK** pour fermer le volet **Créer**. Attendez que l’état de **MyGateway** dans le volet **Services liés** passe sur **CORRECT**. Vous pouvez également lancer l’outil **Gestionnaire de configuration de la passerelle de gestion des données (version préliminaire)** pour vérifier que le nom de la passerelle correspond à celui du portail et que l’**état** est **Enregistré**. Vous devrez peut-être fermer et rouvrir le panneau Services liés pour afficher le dernier état. L’actualisation de l’écran sur l’état le plus récent peut prendre quelques minutes.

## <a name="OnPremStep2"></a>Étape 2 : création d’un service lié pour le serveur SQL Server local

Dans cette étape, vous créez d’abord la base de données et la table requises sur votre ordinateur SQL Server local, avant de créer le service lié.

### Préparer la base de données et la table locales

Pour commencer, vous devez créer la base de données SQL Server, la table, les types définis par l’utilisateur et les procédures stockées. Cela permettra de passer les résultats **MarketingCampaignEffectiveness** de l’objet blob Azure dans la base de données SQL Server.

1.	Dans l’**Explorateur Windows**, accédez au sous-dossier **OnPremises** dans le dossier **C:\\ADFWalkthrough** (ou à l’emplacement où vous avez extrait les exemples).
2.	Ouvrez **prepareOnPremDatabase&Table.ps1** dans votre éditeur favori, remplacez l’élément en surbrillance par vos informations SQL Server et enregistrez le fichier (fournissez les informations d’**authentification SQL**). Dans le cadre du didacticiel, activez l’authentification SQL pour votre base de données. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. Dans **Azure PowerShell**, accédez au dossier **C:\\ADFWalkthrough\\OnPremises**.
4.	Exécutez **prepareOnPremDatabase&Table.ps1** **(soit & entre guillemets doubles soit comme illustré ci-dessous)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Une fois que le script s’exécute correctement, vous verrez les éléments suivants :
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### Créer le service lié

1.	Dans la **version préliminaire du portail Azure**, cliquez sur la vignette **Créer et déployer** sur le volet **DATA FACTORY** pour **LogProcessingFactory**.
2.	Dans **Data Factory Editor**, cliquez sur **Nouveau magasin de données** sur la barre d'outils, puis sélectionnez **Base de données SQL Server locale**.
3.	Dans le script JSON, procédez comme suit : 
	1.	Remplacez **<servername>** par le nom du serveur qui héberge votre base de données SQL Server.
	2.	Remplacez **<databasename>** par **MarketingCampaigns**.
	3.	Si vous utilisez l’**authentification SQL** :
		1.	Spécifiez **<username>** et **<password>** dans les **connectionString**.
		2.	Supprimez les deux dernières lignes (les propriétés JSON **username** et **password** sont nécessaires uniquement si vous utilisez l'authentification Windows). 
		3.	Supprimez **, (virgule) **à la fin de la ligne **gatewayName**.

		**Si vous utilisez l’authentification Windows : ** 1. Définissez la valeur de **Integrated Security** sur **True** dans **connectionString**. Supprimez « **User ID=<username>;Password=<password>;** » de connectionString. 2. Spécifiez le nom de l'utilisateur ayant accès à la base de données dans la propriété **username**. 3. Spécifiez le mot de passe du compte d’utilisateur dans la propriété **password**.   
	4. Spécifiez le nom de la passerelle (**MyGateway**) dans la propriété gatewayName. 		  	 
3.	Cliquez sur **Déployer** sur la barre d'outils pour déployer le service lié. 

## <a name="OnPremStep3"></a>Étape 3 : création d’une table et d’un pipeline

### Création de la table logique locale

1.	Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** sur la barre d'outils, puis sélectionnez **SQL local**. 
2. Remplacez le script JSON du volet de droite par le script JSON du fichier **MarketingCampaignEffectivenessOnPremSQLTable.json** à partir du dossier **C:\\ADFWalkthrough\\OnPremises**.
3. Remplacez le nom du service lié (propriété **linkedServiceName**) **OnPremSqlServerLinkedService** par **SqlServerLinkedService**.
4. Cliquez sur **Déployer** sur la barre d'outils pour déployer la table. 
	 
#### Créer le pipeline pour copier les données d'un objet blob Azure dans SQL Server

1.	1. Dans **Data Factory Editor**, cliquez sur **Nouveau pipeline** dans la barre d'outils. Si ce bouton n’est pas affiché dans la barre d’outils, cliquez sur **... (points de suspension)**. Vous pouvez également cliquer sur **Pipelines** dans l'arborescence, puis sur **Nouveau pipeline**.
2. Remplacez le script JSON du volet de droite par le script JSON du fichier **EgressDataToOnPremPipeline.json** à partir du dossier **C:\\ADFWalkthrough\\OnPremises**.
3. Ajoutez une **virgule (« , »)** à la fin du **crochet fermant (« ] »)** dans le script JSON puis ajoutez les trois lignes suivantes après le crochet fermant. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Notez que les heures de **début** et de **fin** sont définies sur 05/01/2014 et 05/05/2014, car les données d’exemple de cette procédure pas à pas vont du 05/01/2014 au 05/05/2014.
 
3. Cliquez sur **Déployer** sur la barre d’outils pour déployer le pipeline. Vérifiez que le message **PIPELINE CRÉÉ AVEC SUCCÈS** s’affiche sur la barre de titre de l'éditeur.
	
## <a name="OnPremStep4"></a>Étape 4 : surveillance du pipeline et affichage du résultat

Vous pouvez maintenant suivre la même procédure que celle de la section **Surveiller les pipelines et les tranches de données** du [didacticiel principal][datafactorytutorial] pour surveiller le nouveau pipeline et les tranches de données de la nouvelle table ADF locale.
 
Lorsque vous voyez que l'état d'une tranche de la table **MarketingCampaignEffectivenessOnPremSQLTable** passe à Prêt, cela signifie que le pipeline a terminé l'exécution de la tranche. Pour afficher les résultats, interrogez la table **MarketingCampaignEffectiveness** dans la base de données **MarketingCampaigns** de votre serveur SQL Server.
 
Félicitations ! Vous avez terminé la procédure pas à pas pour utiliser votre source de données locales.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=August15_HO9-->