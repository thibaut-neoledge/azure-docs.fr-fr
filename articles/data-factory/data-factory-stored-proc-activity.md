<properties 
	pageTitle="Activité de procédure stockée SQL Server"
	description="Découvrez comment utiliser l’activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure à partir d’un pipeline Data Factory."
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
	ms.date="08/04/2015"
	ms.author="spelluru"/>

# Activité de procédure stockée SQL Server

Vous pouvez utiliser l’activité de procédure stockée SQL Server dans un [pipeline](data-factory-create-pipelines.md) Data Factory pour appeler une procédure stockée dans une base de données **SQL Azure**. Cet article s’appuie sur l’article [Activités de transformation des données](data-factory-data-transformation-activities.md), qui présente une vue d’ensemble de la transformation des données et des activités de transformation prises en charge.

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
outputs | Jeux de données de sortie produits par l’activité de procédure stockée. Vérifiez que la table de sortie utilise un service lié qui lie une base de données SQL Azure à la fabrique de données. Les sorties de l’activité de la procédure stockée peuvent servir à valider le résultat de l’activité de la procédure stockée à des fins de traitement ultérieur ou pour la gestion des dépendances lors du chaînage de cette activité avec d’autres activités | Oui
storedProcedureName | Spécifiez le nom de la procédure stockée dans la base de données SQL Azure qui est représentée par le service lié utilisé par la table de sortie. | Oui
storedProcedureParameters | Spécifiez les valeurs des paramètres de procédure stockée. | Non

## Exemple

Prenons un exemple dans lequel vous souhaitez créer une table dans une base de données SQL Azure qui contient deux colonnes :

des colonnes | Type
------ | ----
ID | Données uniqueidentifier
Datetime | Date et heure auxquelles l’ID correspondant a été généré.

![Exemples de données](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

> [AZURE.NOTE]Le **nom** et la **casse** du paramètre (DateTime dans cet exemple) doivent correspondre à ceux du paramètre spécifié dans le script JSON de l’activité ci-dessous. Dans la définition de procédure stockée, vérifiez que **@** est utilisé en tant que préfixe pour le paramètre.

Pour exécuter cette procédure stockée dans un pipeline Data Factory, vous devez effectuer les opérations suivantes :

1.	Créez un [service lié](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) pour inscrire la chaîne de connexion de la base de données SQL Azure dans laquelle la procédure stockée doit être exécutée.
2.	Créez un [jeu de données](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties) pointant vers la table de sortie dans la base de données SQL Azure. Appelons ce jeu de données sprocsampleout. Ce jeu de données doit référencer le service lié de l’étape n°1. 
3.	Créez la procédure stockée dans la base de données SQL Azure.
4.	Créez le [pipeline](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) ci-dessous avec l’activité SqlServerStoredProcedure pour appeler la procédure stockée dans la base de données SQL Azure.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		            	"name": "SprocActivitySample",
		             	"type": " SqlServerStoredProcedure",
		             	"outputs": [ {"name": "sprocsampleout"} ],
		             	"typeProperties":
		              	{
		                	"storedProcedureName": "sp_sample",
			        		"storedProcedureParameters": 
		        			{
		            			"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        			}
						}
	            	}
		        ]
		     }
		}
5.	Déployez le [pipeline](data-factory-create-pipelines.md).
6.	[Surveillez le pipeline](data-factory-monitor-manage-pipelines.md) à l’aide des vues de gestion et de surveillance Data Factory.

> [AZURE.NOTE]Dans l’exemple ci-dessus, l’activité SprocActivitySample est dépourvue d’entrées. Si vous souhaitez chaîner cette activité avec une activité en amont (par exemple, un traitement antérieur), les sorties de cette dernière peuvent servir d’entrées dans cette activité. Dans ce cas, cette activité n’est pas exécutée tant que l’activité en amont n’est pas terminée, et les sorties des activités en amont sont disponibles (à l’état Prêt). Les entrées ne peuvent pas servir directement de paramètres pour l’activité de procédure stockée.
> 
> Les noms et la casse (majuscule/minuscule) des paramètres de procédure stockée dans le fichier JSON doivent correspondre aux noms des paramètres de procédure stockée dans la base de données cible.

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

<!---HONumber=August15_HO9-->