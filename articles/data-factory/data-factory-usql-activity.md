---
title: "Transformer des données à l’aide d’un script U-SQL - Azure | Microsoft Docs"
description: "Découvrez comment traiter ou transformer les données en exécutant des scripts U-SQL sur le service de calcul Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 7c9f14503a7cf5c0808e26884a73cd2918ff1c74
ms.openlocfilehash: 7a26b44f1c2c97174fb98ffdf0cb74a71d27710a


---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformer des données en exécutant des scripts U-SQL sur Azure Data Lake Analytics 
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procédure stockée](data-factory-stored-proc-activity.md)
> * [Langage U-SQL du service Analytique Data Lake](data-factory-usql-activity.md)
> * [.NET personnalisé](data-factory-use-custom-activities.md)

Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l’**activité U-SQL de Data Lake Analytics** qui exécute un script **U-SQL** sur un service lié de calcul **Azure Data Lake Analytics**. 

> [!NOTE]
> Créez un compte Azure Data Lake Analytics avant de créer un pipeline avec une activité U-SQL Data Lake Analytics. Pour plus d’informations sur Azure Data Lake Analytics, consultez [Prise en main d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
> 
> Consultez le [didacticiel Concevez votre premier pipeline](data-factory-build-your-first-pipeline.md) pour connaître les étapes détaillées de création d'une fabrique de données, de services liés, de jeux de données et d'un pipeline. Utilisez les extraits de code JSON avec Data Factory Editor ou Visual Studio ou Azure PowerShell pour créer les entités Data Factory.
> 
> 

## <a name="azure-data-lake-analytics-linked-service"></a>Service lié Analytique Azure Data Lake
Vous créez un service lié **Analytique Azure Data Lake** pour lier un service de calcul Analytique Azure Data Lake Analytics à une fabrique de données Azure. L’activité U-SQL Analytique Data Lake dans le pipeline fait référence à ce service lié. 

L’exemple suivant présente la définition JSON pour le service lié Analytique Azure Data Lake. 

```JSON
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

Le tableau suivant décrit les propriétés utilisées dans la définition JSON. 

| Propriété | Description | Requis |
| --- | --- | --- |
| Type |La propriété de type doit être définie sur **AzureDataLakeAnalytics**. |Oui |
| accountName |Nom du compte du service Analytique Azure Data Lake. |Oui |
| dataLakeAnalyticsUri |URI du service Analytique Azure Data Lake. |Non |
| autorisation |Le code d’autorisation est automatiquement récupéré après un clic sur le bouton **Autoriser** dans l’éditeur de la fabrique de données et une fois la connexion OAuth effectuée. |Oui |
| subscriptionId |ID d’abonnement Azure |Non (si non spécifié, l’abonnement de la fabrique de données est utilisé). |
| resourceGroupName |Nom du groupe de ressources Azure |Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé). |
| sessionId |ID de session issu de la session d'autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. L’ID de session est généré automatiquement dans l’éditeur de la fabrique de données. |Oui |

Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire au bout d’un certain temps. Consultez le tableau suivant pour connaître les délais d’expiration associés aux différents types de comptes d’utilisateur. Le message d’erreur suivant peut s’afficher à **l’expiration du jeton** d’authentification : Erreur de l’opération d’informations d’identification : invalid_grant - AADSTS70002: Erreur de validation des informations d’identification. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z ».

| Type d’utilisateur | Expire après |
|:--- |:--- |
| Comptes d’utilisateurs NON gérés par Azure Active Directory (@hotmail.com, @live.com,, etc.). |12 heures |
| Comptes d’utilisateurs gérés par Azure Active Directory (AAD) |14 jours après la dernière exécution de tranche de données. <br/><br/>90 jours, si une tranche basée sur un service lié OAuth est exécutée au moins une fois tous les 14 jours. |

Pour éviter ou résoudre cette erreur, accordez une nouvelle autorisation à l’aide du bouton **Autoriser** au moment de **l’expiration du jeton**, puis redéployer le service lié. Vous pouvez également générer des valeurs pour les propriétés **sessionId** et **authorization** à l’aide du code fourni dans la section suivante. 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Pour générer les valeurs des propriétés sessionId et authorization au moyen d’un programme

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Pour plus d’informations sur les classes Data Factory utilisées dans le code, consultez les rubriques [AzureDataLakeStoreLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) et [AuthorizationSessionGetResponse, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Ajoutez une référence à Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Activité U-SQL Data Lake Analytics
L'extrait de code JSON suivant définit un pipeline avec une activité U-SQL Data Lake Analytics. La définition d'activité comporte une référence au service lié Azure Data Lake Analytics créé précédemment.   

```JSON
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

Le tableau suivant indique les noms et les descriptions des propriétés qui sont spécifiques à cette activité. 

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| Type |La propriété de type doit être définie sur **DataLakeAnalyticsU-SQL**. |Oui |
| scriptPath |Chemin d'accès au dossier qui contient le script SQL-U. Le nom de fichier respecte la casse. |Non (si vous utilisez le script) |
| scriptLinkedService |Service lié qui lie le stockage qui contient le script à la fabrique de données |Non (si vous utilisez le script) |
| script |Spécifiez un script en ligne au lieu de spécifier scriptPath et scriptLinkedService. Par exemple : « script » : « Test CRÉER BASE DE DONNÉES ». |Non (si vous utilisez scriptPath et scriptLinkedService) |
| degreeOfParallelism |Le nombre maximal de nœuds utilisés simultanément pour exécuter le travail. |Non |
| priority |Détermine les travaux parmi tous ceux qui sont en file d'attente qui doivent être sélectionnés pour s'exécuter en premier. Plus le numéro est faible, plus la priorité est élevée. |Non |
| parameters |Paramètres du script U-SQL |Non |

Vous trouverez la définition du script dans la section [Définition du script SearchLogProcessing.txt](#script-definition) . 

## <a name="sample-input-and-output-datasets"></a>Exemples de jeux de données d'entrée et de sortie
### <a name="input-dataset"></a>Jeu de données d'entrée
Dans cet exemple, les données d'entrée se trouvent dans un magasin Azure Data Lake (fichier SearchLog.tsv dans le dossier datalake/input). 

```JSON
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Jeu de données de sortie
Dans cet exemple, les données de sortie générées par le script U-SQL sont stockées dans un magasin Azure Data Lake (dossier datalake/output). 

```JSON
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Exemple de service lié Data Lake Store
Voici la définition de l’exemple de service lié Azure Data Lake Store utilisé par les jeux de données d’entrée/de sortie. 

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>"
        }
    }
}
```

Consultez l’article [Déplacer des données vers et depuis Azure Data Lake Store](data-factory-azure-datalake-connector.md) pour obtenir une description des propriétés JSON. 

## <a name="sample-u-sql-script"></a>Exemple de script SQL-U

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Les valeurs des paramètres **@in** et **@out** dans le script U-SQL sont passées dynamiquement par ADF en utilisant la section « parameters ». Consultez la section « parameters » dans la définition du pipeline.

Vous pouvez aussi spécifier d’autres propriétés comme degreeOfParallelism et priority dans votre définition de pipeline pour les travaux qui s’exécutent au niveau du service Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Paramètres dynamiques
Dans l’exemple de définition de pipeline, des valeurs codées en dur sont affectées aux paramètres de sortie. 

```JSON
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Il est possible d’utiliser des paramètres dynamiques à la place. Par exemple : 

```JSON
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Dans ce cas, les fichiers d’entrée sont toujours récupérés à partir du dossier /datalake/input et les fichiers de sortie sont générés dans le dossier /datalake/output. Les noms de fichiers sont dynamiques et basés sur l’heure de début de la tranche horaire.  




<!--HONumber=Jan17_HO4-->


