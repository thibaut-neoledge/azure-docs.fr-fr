<properties 
	pageTitle="Activité de procédure stockée SQL Server" 
	description="Découvrez comment utiliser l'activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou un entrepôt Azure SQL Data Warehouse à partir d'un pipeline Data Factory." 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Activité de procédure stockée SQL Server

Vous pouvez utiliser l’activité de procédure stockée SQL Server dans un [pipeline](data-factory-create-pipelines.md) Data Factory pour appeler une procédure stockée dans l’un des magasins de données suivants.


- Base de données SQL Azure 
- Azure SQL Data Warehouse  
- Base de données SQL Server dans votre machine virtuelle d’entreprise ou Azure. Vous devez installer la passerelle de gestion de données sur l’ordinateur qui héberge la base de données ou sur un autre ordinateur afin d’éviter toute mise en concurrence avec la base de données pour les ressources. La passerelle de gestion de données est un logiciel qui connecte des sources de données locales ou des sources de données hébergées dans des machines virtuelles Azure à des services cloud de manière gérée et sécurisée. Pour plus d’informations sur la passerelle de gestion de données, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md). 

Cet article s'appuie sur l'article [Activités de transformation des données](data-factory-data-transformation-activities.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

## Syntaxe
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Détails de la syntaxe

Propriété | Description | Requis
-------- | ----------- | --------
name | Nom de l’activité | Oui
description | Texte décrivant la raison motivant l’activité. | Non
type | SqlServerStoredProcedure | Oui
inputs | Jeux de données d’entrée qui doivent être disponibles (à l’état Prêt) pour l’activité de procédure stockée à exécuter. Les entrées pour l’activité de procédure stockée sont utilisées uniquement pour la gestion des dépendances lors du chaînage de cette activité avec d’autres activités. Les jeux de données d’entrée ne peuvent pas être utilisés dans la procédure stockée en tant que paramètres. | Non
outputs | Jeux de données de sortie produits par l’activité de procédure stockée. Vérifiez que la table de sortie utilise un service lié qui lie une base de données SQL Azure, un entrepôt Azure SQL Data Warehouse ou une base de données SQL Server à la fabrique de données. Les sorties de l’activité de la procédure stockée peuvent servir à valider le résultat de l’activité de la procédure stockée à des fins de traitement ultérieur ou pour la gestion des dépendances lors du chaînage de cette activité avec d’autres activités | Oui
storedProcedureName | Spécifiez le nom de la procédure stockée dans la base de données SQL Azure ou l'entrepôt Azure SQL Data Warehouse qui est représenté(e) par le service lié utilisé par la table de sortie. | Oui
storedProcedureParameters | Spécifiez les valeurs des paramètres de procédure stockée. | Non

## Exemple de procédure pas à pas

### Exemple de table et de procédure stockée
> [AZURE.NOTE] Cet exemple utilise la base de données SQL Azure mais fonctionne de la même manière pour un entrepôt Azure SQL Data Warehouse et une base de données SQL Server.

1. Créez la **table** suivante dans votre base de données SQL Azure à l’aide de SQL Server Management Studio ou d’un autre outil que vous maîtrisez. La colonne datetimestamp affiche la date et l’heure auxquelles l’ID correspondant est généré. 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	La colonne ID affiche l’identifiant unique et la colonne datetimestamp affiche la date et l’heure auxquelles l’ID correspondant est généré. ![Exemples de données](./media/data-factory-stored-proc-activity/sample-data.png)

2. Créez la **procédure stockée** suivante qui insère des données dans la table **sampletable**.

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT] Le **nom** et la **casse** du paramètre (DateTime dans cet exemple) doivent correspondre à ceux du paramètre spécifié dans le script JSON de l’activité/du pipeline. Dans la définition de procédure stockée, vérifiez que **@** est utilisé en tant que préfixe pour le paramètre.
	
### Créer une fabrique de données  
4. Après la connexion au [portail Azure](https://portal.azure.com/), procédez comme suit :
	1.	Cliquez sur **NOUVEAU** dans le menu de gauche. 
	2.	Cliquez sur **Analyse des données** dans le panneau **Créer**.
	3.	Cliquez sur **Data Factory** dans le panneau **Données + analyse**.
4.	Dans le panneau **Nouvelle fabrique de données**, entrez **SProcDF** dans le champ Nom. Les noms Azure Data Factory sont globalement uniques. Vous devez faire précéder le nom de la fabrique de données par votre nom, pour activer la création de la fabrique. 
3.	Si vous n’avez pas créé de groupe de ressources, vous devez en créer un. Pour ce faire :
	1.	Cliquez sur **NOM DU GROUPE DE RESSOURCES**.
	2.	Sélectionnez **Créer un groupe de ressources** dans le panneau **Groupe de ressources**.
	3.	Entrez **ADFTutorialResourceGroup** dans le champ **Nom** du panneau **Créer un groupe de ressources**.
	4.	Cliquez sur **OK**.
4.	Après avoir sélectionné le groupe de ressources, vérifiez que vous utilisez l’abonnement correspondant à celui dans lequel vous souhaitez créer la fabrique de données.
5.	Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.
6.	La fabrique de données apparaît comme étant en cours de création dans le **Tableau d’accueil** du portail Azure. Une fois la fabrique de données créée, vous verrez la page correspondante indiquant son contenu.

### Créer un service lié Azure SQL  
Après avoir créé la fabrique de données, vous créez un service lié Azure SQL qui relie votre base de données SQL Azure à la fabrique de données. Il s’agit de la base de données qui contient la table sampletable et la procédure stockée sp\_sample.

7.	Cliquez sur **Créer et déployer** dans le panneau **DATA FACTORY** pour **SProcDF**. Cette action lance l'éditeur Data Factory Editor. 
2.	Cliquez sur **Nouvelle banque de données** dans la barre de commandes et choisissez **SQL Azure**. Le script JSON de création d’un service lié Azure SQL doit apparaître dans l’éditeur. 
4. Remplacez **servername** par le nom de votre serveur de base de données SQL Azure, **databasename** par la base de données dans laquelle vous avez créé la table et la procédure stockée, **username@servername** par le compte d’utilisateur qui a accès à la base de données et **password** par le mot de passe du compte d’utilisateur.
5. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

### Créer un jeu de données de sortie
6. Cliquez sur **Nouveau jeu de données** dans la barre de commandes et sélectionnez **SQL Azure**.
7. Copiez-collez le script JSON suivant dans l’éditeur JSON.

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. Cliquez sur **Déployer** dans la barre de commandes pour déployer le jeu de données. 

### Créer un pipeline avec une activité SqlServerStoredProcedure
Nous allons maintenant créer un pipeline avec une activité SqlServerStoredProcedure.
 
9. Cliquez sur **... (points de suspension)** dans la barre de commandes et sur **Nouveau pipeline**. 
9. Copiez-collez l’extrait de code JSON suivant. **storedProcedureName** a la valeur **sp\_sample**. Le nom et la casse du paramètre **DateTime** doivent correspondre à ceux du paramètre dans la définition de procédure stockée.  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. Cliquez sur **Déployer** dans la barre d’outils pour déployer le pipeline.  

### Surveiller le pipeline

6. Cliquez sur **X** pour fermer les panneaux de l’éditeur Data Factory Editor et pour revenir au panneau Data Factory, puis cliquez sur**Schéma**.
7. Dans la vue schématique, une vue d'ensemble des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche. 
8. Dans la vue schématique, double-cliquez sur le jeu de données **sprocsampleout**. Les tranches s’affichent avec l’état Prêt. Il doit y avoir 24 tranches, car il se produit une tranche par heure entre le 02/01/2015 et le 03/01/2015. 
10. Quand une tranche est en état **Prêt**, exécutez une requête **select * from sampledata** sur la base de données SQL Azure pour vérifier que les données ont été insérées dans la table par la procédure stockée.

	![Données de sortie](./media/data-factory-stored-proc-activity/output.png)

	Consultez [Surveiller le pipeline](data-factory-monitor-manage-pipelines.md) pour plus d’informations sur la surveillance des pipelines Azure Data Factory.

> [AZURE.NOTE] Dans l’exemple ci-dessus, l’activité SprocActivitySample est dépourvue d’entrées. Si vous souhaitez chaîner cette activité avec une activité en amont (par exemple, un traitement antérieur), les sorties de cette dernière peuvent servir d’entrées dans cette activité. Dans ce cas, cette activité n’est pas exécutée tant que l’activité en amont n’est pas terminée, et les sorties des activités en amont sont disponibles (à l’état Prêt). Les entrées ne peuvent pas servir directement de paramètres pour l’activité de procédure stockée.

## Transmission d’une valeur statique 
À présent, ajoutons une autre colonne nommée « Scénario » dans la table contenant une valeur statique appelée « Exemple de document ».

![Exemple de données 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Pour ce faire, transmettez le paramètre Scénario et la valeur de l’activité de procédure stockée. La section typeProperties de l’exemple ci-dessus ressemble à ceci :

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_0427_2016-->