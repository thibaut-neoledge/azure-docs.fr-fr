<properties 
	pageTitle="Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Visual Studio" 
	description="Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l'aide de Visual Studio." 
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
	ms.topic="get-started-article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Visual Studio
> [AZURE.SELECTOR]
- [Vue d’ensemble du didacticiel](data-factory-get-started.md)
- [Utilisation de Data Factory Editor](data-factory-get-started-using-editor.md)
- [Utilisation de Visual Studio](data-factory-get-started-using-vs.md)
- [Utiliser PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Utilisation de l’Assistant de copie](data-factory-copy-data-wizard-tutorial.md)

Dans ce didacticiel, vous effectuerez les étapes suivantes à l'aide de Visual Studio 2013 :

1. Créez deux services liés : **AzureStorageLinkedService1** et **AzureSqlinkedService1**. Le service AzureStorageLinkedService1 lie un espace de stockage Azure, et le service AzureSqlLinkedService1 lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactoryVS**. Les données d'entrée pour le pipeline se trouvent dans un conteneur d'objets blob dans le stockage d'objets blob Azure et les données de sortie sont stockées dans une table dans la base de données SQL Azure. Par conséquent, vous ajoutez ces deux magasins de données en tant que services liés à la fabrique de données.
2. Créez les deux tables de fabrique de données, **EmpTableFromBlob** et **EmpSQLTable**, qui représentent les données d'entrée/sortie qui sont stockées dans les magasins de données. Pour la table EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données ; pour la table EmpSQLTable, vous spécifiez la table SQL qui stocke les données de sortie. Vous devez également spécifier d'autres propriétés telles que la structure des données, la disponibilité des données, etc.
3. Créez un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données ADFTutorialDataFactoryVS. Le pipeline effectue une **activité de copie** qui copie les données d'entrée de l'objet blob Azure vers la table SQL Azure de sortie. L’activité de copie effectue le déplacement des données dans Azure Data Factory, et l’activité est alimentée par un service disponible à l’échelle mondiale qui peut copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie [Activités de déplacement des données](data-factory-data-movement-activities.md). 
4. Créez une fabrique de données et déployer des services liés, un groupe de données et le pipeline.    

## Configuration requise
Passez en revue l'article [Vue d'ensemble du didacticiel](data-factory-get-started.md) et effectuez les étapes préalables avant de suivre ce didacticiel.

Les composants suivants doivent être installés sur votre ordinateur :
- Visual Studio 2013
- Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013. Accédez à la [page de téléchargement d'Azure](https://azure.microsoft.com/downloads/), puis cliquez sur **Installation de Visual Studio 2013** dans la section **.NET**.

## Création d’un projet Visual Studio 
1. Lancez **Visual Studio 2013**. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. La boîte de dialogue **Nouveau projet** doit s’afficher.  
2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez le modèle **DataFactory**, puis cliquez sur **Projet Data Factory vide**. Si le modèle DataFactory n’est pas affiché, fermez Visual Studio, installez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, puis rouvrez Visual Studio.  

	![Boîte de dialogue Nouveau projet](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Entrez le **nom** du projet, son **emplacement** et le nom de la **solution**, puis cliquez sur **OK**.

	![Explorateur de solutions](./media/data-factory-get-started-using-vs/solution-explorer.png)

## créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un compte de stockage Azure, une base de données SQL Azure ou une base de données SQL Server locale.

Dans cette étape, vous allez créer deux services liés : **AzureStorageLinkedService1** et **AzureSqlLinkedService1**. Le service AzureStorageLinkedService1 lie un compte de stockage Azure, et le service AzureSqlLinkedService lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**.

### Créer le service lié Azure Storage

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.      
5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Service lié Azure Storage** dans la liste, puis cliquez sur **Ajouter**. 

	![Nouveau service lié](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Remplacez **accountname** et **accountkey** par le nom de votre compte de stockage Azure et par sa clé.

	![Service lié Azure Storage](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Enregistrez le fichier **AzureStorageLinkedService1.json**.

### Créer le service lié SQL Azure

5. Cliquez de nouveau avec le bouton droit sur le nœud **Services liés** dans l'**Explorateur de solutions**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
6. Cette fois, sélectionnez **Service lié SQL Azure**, puis cliquez sur **Ajouter**. 
7. Dans le **fichier AzureSqlLinkedService1.json**, remplacez **servername**, **databasename**, ****username@servername** et **password** par les noms du serveur SQL Azure, de la base de données et du compte d'utilisateur, et par le mot de passe.
8.  Enregistrez le fichier **AzureSqlLinkedService1.json**. 


## Créer des jeux de données
À l’étape précédente, vous avez créé les services liés **AzureStorageLinkedService1** et **AzureSqlLinkedService1** pour lier un compte de stockage Azure et une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**. Dans cette étape, vous allez définir les deux tables de fabrique de données, **EmpTableFromBlob** et **EmpSQLTable**, qui représentent les données d’entrée/sortie qui sont stockées dans les magasins de données référencés par AzureStorageLinkedService1 et AzureSqlLinkedService1, respectivement. Pour la table EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données ; pour la table EmpSQLTable, vous devez spécifier la table SQL qui stocke les données de sortie.

### Créer le jeu de données d’entrée

9. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
10. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Objet blob Azure**, puis cliquez sur **Ajouter**.   
10. Remplacez le texte JSON par le texte suivant, puis enregistrez le fichier **AzureBlobLocation1.json**. 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

### Créer un jeu de données de sortie

11. À nouveau dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
12. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **SQL Azure**, puis cliquez sur **Ajouter**. 
13. Remplacez le texte JSON par le texte JSON suivant, puis enregistrez le fichier **AzureSqlTableLocation1.json**.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## Création d’un pipeline 
Jusqu’à présent, vous avez créé des services liés et des tables d’entrée/sortie. À présent, vous allez créer un pipeline pourvu d’une **activité de copie** pour copier des données de la base de données d’objets blob Azure vers la base de données SQL Azure.


1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Pipelines**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.  
15. Sélectionnez **Pipeline de copie de données** dans la boîte de dialogue **Ajouter un nouvel élément**, puis cliquez sur**Ajouter**. 
16. Remplacez le texte JSON par le texte JSON suivant, puis enregistrez le fichier **CopyActivity1.json**.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
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

## Publier/Déployer des entités Data Factory
  
18. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**. 
19. Si la boîte de dialogue **Connectez-vous à votre compte Microsoft** s'affiche, entrez vos informations d'identification pour le compte qui dispose de l'abonnement Azure, puis cliquez sur **Se connecter**.
20. La boîte de dialogue suivante doit s’afficher :

	![Boîte de dialogue Publier](./media/data-factory-get-started-using-vs/publish.png)

21. Dans la page Configurer une fabrique de données, procédez comme suit :
	1. Sélectionnez l'option **Créer une fabrique de données**.
	2. Entrez **VSTutorialFactory** comme **Nom**.  
	
		> [AZURE.NOTE]  
		Le nom de la fabrique de données Azure doit être un nom global unique. Si vous obtenez une erreur au sujet du nom de la fabrique de données pendant la publication, modifiez le nom de la fabrique de données (par exemple, votrenomVSTutorialFactory) et renouvelez la publication. Consultez la rubrique [Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
		> 
		> Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.
	3. Sélectionnez l’abonnement approprié pour le champ **Abonnement**. 
	4. Sélectionnez le **groupe de ressources** pour la fabrique de données à créer. 
	5. Sélectionnez la **région** pour la fabrique de données. 
	6. Cliquez sur **Suivant** pour basculer vers la page **Publier des éléments**. 
23. Dans la page **Publier des éléments**, vérifiez que toutes les entités de fabriques de données sont sélectionnées, puis cliquez sur **Suivant** pour basculer vers la page **Résumé**.     
24. Passez en revue le résumé, puis cliquez sur **Suivant** pour démarrer le processus de déploiement et afficher l’**état du déploiement**.
25. Dans la page **État du déploiement**, vous devez voir l’état du processus de déploiement. Une fois le déploiement terminé, cliquez sur Terminer. 


## Utiliser l’Explorateur de serveurs pour passer en revue la fabrique des données

1. Dans **Visual Studio**, cliquez sur **Affichage** dans le menu, puis sur **Explorateur de serveurs**.
2. Dans la fenêtre Explorateur de serveurs, développez **Azure**, puis **Data Factory**. Si la boîte de dialogue **Se connecter à Visual Studio** est affichée, entrez le **compte** associé à votre abonnement Azure, puis cliquez sur **Continuer**. Entrez le **mot de passe**, puis cliquez sur **Se connecter**. Visual Studio essaie d’obtenir des informations sur toutes les fabriques de données Azure contenues dans votre abonnement. L'état de cette opération s'affiche dans la fenêtre **Liste des tâches de Data Factory**. ![Explorateur de serveurs](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Vous pouvez cliquer avec le bouton droit sur une fabrique de données et sélectionner Exporter la fabrique de données vers le nouveau projet pour créer un projet Visual Studio basé sur une fabrique de données existante. ![Exporter la fabrique de données vers un projet Visual Studio](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Mettre à jour des outils Data Factory pour Visual Studio
Pour mettre à jour des outils Azure Data Factory pour Visual Studio, procédez comme suit :

1. Dans le menu, cliquez sur **Outils**, puis sélectionnez **Extensions et mises à jour**. 
2. Dans le volet gauche, sélectionnez **Mises à jour**, puis **Galerie Visual Studio**.
4. Sélectionnez **Outils Azure Data Factory pour Visual Studio**, puis cliquez sur **Mettre à jour**. Si cette entrée n’est pas affichée, c’est que vous possédez déjà la dernière version de ces outils. 

Consultez [Surveiller les jeux de données et le pipeline](data-factory-get-started-using-editor.md#monitor-pipeline) pour obtenir des instructions sur l’utilisation du portail Azure pour surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.

## Voir aussi
Pour plus d’informations sur l’**activité de copie** dans Azure Data Factory, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).

<!---HONumber=AcomDC_0323_2016-->