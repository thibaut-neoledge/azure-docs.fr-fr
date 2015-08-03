<properties 
	pageTitle="Prise en main d'Azure Data Factory" 
	description="Ce didacticiel vous montre comment créer un exemple de pipeline de données qui copie les données d'un objet blob vers une instance de base de données SQL Azure." 
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
	ms.date="07/17/2015" 
	ms.author="spelluru"/>

# Didacticiel : Créer et surveiller une fabrique de données à l’aide de Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##Dans ce didacticiel
Dans ce didacticiel, vous allez tout d’abord créer une fabrique de données Azure nommée **ADFTutorialDataFactoryVS** à l’aide du portail Azure en version préliminaire, puis procéder comme suit à l’aide de Visual Studio 2013 :

1. Créez deux services liés : **AzureStorageLinkedService1** et **AzureSqlinkedService1**. Le service AzureStorageLinkedService1 lie un espace de stockage Azure, et le service AzureSqlLinkedService1 lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactoryVS**. Les données d'entrée pour le pipeline se trouvent dans un conteneur d'objets blob dans le stockage d'objets blob Azure et les données de sortie sont stockées dans une table dans la base de données SQL Azure. Par conséquent, vous ajoutez ces deux magasins de données en tant que services liés à la fabrique de données.
2. Créez les deux tables de fabrique de données, **EmpTableFromBlob** et **EmpSQLTable**, qui représentent les données d’entrée/sortie qui sont stockées dans les magasins de données. Pour la table EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données ; pour la table EmpSQLTable, vous spécifiez la table SQL qui stocke les données de sortie. Vous devez également spécifier d'autres propriétés telles que la structure des données, la disponibilité des données, etc.
3. Créez un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données ADFTutorialDataFactoryVS. Le pipeline dispose d’une **activité de copie** qui copie les données d’entrée de l’objet blob Azure dans la table SQL Azure de sortie. 


## <a name="CreateDataFactory"></a>Créer une fabrique Azure Data Factory
Dans cette étape, vous utilisez le portail Azure en version préliminaire pour créer une fabrique de données Azure nommée **ADFTutorialDataFactoryVS**.

1.	Une fois connecté au [portail Azure en version préliminaire](http://portal.azure.com), cliquez dans le coin inférieur gauche sur **NOUVEAU**, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Fabrique de données** dans le panneau **Analyse de données**. 

	![Nouveau -> DataFactory](./media/data-factory-get-started-using-vs/NewDataFactoryMenu.png)

6. Dans le panneau **Nouvelle fabrique de données** :
	1. Entrez **ADFTutorialDataFactoryVS** comme **nom**. 
	
  		![Panneau Nouvelle fabrique de données](./media/data-factory-get-started-using-vs/getstarted-new-data-factory.png)
	2. Cliquez sur **NOM DU GROUPE DE RESSOURCES** et procédez comme suit :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, entrez **ADFTutorialResourceGroup** comme **nom** du groupe de ressources, puis cliquez sur **OK**. 

			![Créer un groupe de ressources](./media/data-factory-get-started-using-vs/CreateNewResourceGroup.png)

		Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](resource-group-overview.md).  
7. Dans le panneau **Nouvelle fabrique de données**, notez que l'option **Ajouter au tableau d'accueil** est sélectionnée.
8. Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.

	Le nom de la fabrique de données Azure doit être un nom global unique. Si le message d’erreur **Le nom de la fabrique de données « ADFTutorialDataFactoryVS » n’est pas disponible** s’affiche, modifiez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactoryVS), puis réessayez de la créer. Utilisez ce nom à la place d'ADFTutorialFactory quand vous effectuez les étapes restantes de ce didacticiel. Consultez la rubrique [Data Factory - Règles d’affectation de noms][règles-affectation de noms-fabrique-données] pour savoir comment nommer les artefacts Data Factory.
	 
	![Nom de la fabrique de données indisponible](./media/data-factory-get-started-using-vs/getstarted-data-factory-not-available.png)

9. Cliquez sur le hub **NOTIFICATIONS** situé à gauche et recherchez les notifications relatives au processus de création. Cliquez sur **X** pour fermer le panneau **NOTIFICATIONS** si celui-ci est ouvert.
10. Une fois la création terminée, le panneau **FABRIQUE DE DONNÉES** apparaît de la manière suivante :

    ![Page d'accueil Data Factory](./media/data-factory-get-started-using-vs/getstarted-data-factory-home-page.png)

## Créer et déployer des entités Data Factory à l’aide de Visual Studio 

### Conditions préalables
Vous devez avoir installé le logiciel suivant sur votre ordinateur : - Visual Studio 2013 - Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013. Accédez à la [page de téléchargement d’Azure](http://azure.microsoft.com/downloads/), puis cliquez sur **Installation de Visual Studio 2013** dans la section **.NET**.

### Procédure pas à pas

#### Créer le projet Visual Studio 
1. Lancez **Visual Studio 2013**. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. La boîte de dialogue **Nouveau projet** doit s’afficher.  
2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez le modèle **DataFactory**, puis cliquez sur **Projet Data Factory vide**. Si le modèle DataFactory n’est pas affiché, fermez Visual Studio, installez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, puis rouvrez Visual Studio.  

	![Boîte de dialogue Nouveau projet](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Entrez le **nom** du projet, son **emplacement** et le nom de la **solution**, puis cliquez sur **OK**.

	![Explorateur de solutions](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un compte de stockage Azure, une base de données SQL Azure ou une base de données SQL Server locale.

Dans cette étape, vous allez créer deux services liés : **AzureStorageLinkedService1** et **AzureSqlLinkedService1**. Le service AzureStorageLinkedService1 lie un compte de stockage Azure, et le service AzureSqlLinkedService lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**.

##### Créer le service lié Azure Storage

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.      
5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Service lié Azure Storage** dans la liste, puis cliquez sur **Ajouter**. 

	![Nouveau service lié](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Remplacez **accountname** et **accountkey** par le nom de votre compte de stockage Azure et par sa clé.

	![Service lié Azure Storage](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Enregistrez le fichier **AzureStorageLinkedService1.json**.

#### Créer le service lié SQL Azure

5. Cliquez avec le bouton droit sur le nœud **Services liés** de nouveau dans l’**Explorateur de solutions**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
6. Cette fois, sélectionnez **Service lié SQL Azure**, puis cliquez sur **Ajouter**. 
7. Dans le **fichier AzureSqlLinkedService1.json**, remplacez **servername**, **databasename**, **username@servername** et **password** par les noms du serveur SQL Azure, de la base de données et du compte d’utilisateur, et par le mot de passe. 8.  Enregistrez le fichier **AzureSqlLinkedService1.json**. 


### créer des tables d'entrée et de sortie
À l’étape précédente, vous avez créé les services liés **AzureStorageLinkedService1** et **AzureSqlLinkedService1** pour lier un compte de stockage Azure et une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**. Dans cette étape, vous allez définir les deux tables de fabrique de données, **EmpTableFromBlob** et **EmpSQLTable**, qui représentent les données d’entrée/sortie qui sont stockées dans les magasins de données référencés par AzureStorageLinkedService1 et AzureSqlLinkedService1, respectivement. Pour la table EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données ; pour la table EmpSQLTable, vous devez spécifier la table SQL qui stocke les données de sortie.

#### Création de la table d’entrée

9. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
10. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Objet blob Azure**, puis cliquez sur **Ajouter**.   
10. Remplacez le texte JSON par le texte suivant et enregistrez le fichier **AzureBlobLocation1.json**. 

		{
    		"name": "EmpTableFromBlob",
        	"properties":
        	{
            	"structure":  
                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "AzureStorageLinkedService1"
	            },
	            "availability": 
	            {
	                "frequency": "Hour",
	                "interval": 1,
	                "waitOnExternal": {}
                }
	        }
		}

#### Création de la table de sortie

11. À nouveau dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
12. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **SQL Azure**, puis cliquez sur **Ajouter**. 
13. Remplacez le texte JSON par le texte JSON suivant et enregistrez le fichier **AzureSqlTableLocation1.json**.

		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSqlTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService1"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

#### Création du pipeline 
Jusqu’à présent, vous avez créé des services liés et des tables d’entrée/sortie. À présent, vous allez créer un pipeline pourvu d’une **activité de copie** pour copier des données de la base de données d’objets blob Azure vers la base de données SQL Azure.


1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Pipelines**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.  
15. Sélectionnez **Pipeline de copie de données** dans la boîte de dialogue **Ajouter un nouvel élément**, puis cliquez sur**Ajouter**. 
16. Remplacez le texte JSON par le texte JSON suivant, puis enregistrez le fichier **CopyActivity1.json**.
			
		 {
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from a blob to Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink",
		                        "writeBatchSize": 10000,
		                        "writeBatchTimeout": "60:00:00"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		
		        "start": "2015-07-12T00:00:00Z",
		        "end": "2015-07-13T00:00:00Z",
		        "isPaused": false
		    }
		} 

#### Publier/Déployer des entités Data Factory
  
18. Dans la zone des barres d’outils, cliquez avec le bouton droit sur **Data Factory** et sélectionnez cet élément pour activer la barre d’outils Data Factory si ce n’est déjà fait. 
19. Dans la **barre d’outils Data Factory**, cliquez sur la **zone de liste déroulante** pour voir toutes les fabriques de données de votre abonnement Azure. Si la boîte de dialogue **Se connecter à Visual Studio** s’affiche : 
	20. Entrez le **compte de messagerie** associé à l’abonnement Azure dans lequel vous souhaitez créer la fabrique de données, entrez le **mot de passe**, puis cliquez sur **Connexion**.
	21. Une fois la connexion établie, vous devez voir toutes les fabriques de données dans l’abonnement Azure. Dans ce didacticiel, vous allez créer une fabrique de données.       
22. Dans la liste déroulante, sélectionnez **ADFTutorialFactoryVS**, puis cliquez sur le bouton **Publier** pour déployer/publier les services liés, les jeux de données et le pipeline.    

	![Bouton Publier](./media/data-factory-get-started-using-vs/publish.png)

23. L’état de la publication doit s’afficher dans la fenêtre Liste des tâches de Data Factory qui est illustrée dans l’image ci-dessus. Vérifiez que la publication a abouti.

## Utiliser l’Explorateur de serveurs pour passer en revue les entités Data Factory

1. Dans **Visual Studio**, cliquez sur **Affichage** dans le menu, puis sur **Explorateur de serveurs**.
2. Dans la fenêtre Explorateur de serveurs, développez **Azure** et **Data Factory**. Si la boîte de dialogue **Se connecter à Visual Studio** est affichée, entrez le **compte** associé à votre abonnement Azure, puis cliquez sur **Continuer**. Entrez le **mot de passe**, puis cliquez sur **Se connecter**. Visual Studio essaie d’obtenir des informations sur toutes les fabriques de données Azure contenues dans votre abonnement. L’état de cette opération s’affiche dans la fenêtre **Liste des tâches de Data Factory**. ![Explorateur de serveurs](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Vous pouvez cliquer avec le bouton droit sur une fabrique de données et sélectionner Exporter la fabrique de données vers le nouveau projet pour créer un projet Visual Studio basé sur une fabrique de données existante. ![Exporter la fabrique de données vers un projet Visual Studio](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Mettre à jour des outils Data Factory pour Visual Studio
Pour mettre à jour des outils Azure Data Factory pour Visual Studio, procédez comme suit :

1. Dans le menu, cliquez sur **Outils**, puis sélectionnez **Extensions et mises à jour**. 
2. Dans le volet gauche, sélectionnez **Mises à jour**, puis **Galerie Visual Studio**.
4. Sélectionnez **Outils Azure Data Factory pour Visual Studio** et cliquez sur **Mettre à jour**. Si cette entrée n’est pas affichée, c’est que vous possédez déjà la dernière version de ces outils. 

Consultez [Surveiller les jeux de données et le pipeline](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline) pour obtenir des instructions sur l’utilisation du portail Azure en version préliminaire afin de surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.

<!---HONumber=July15_HO4-->