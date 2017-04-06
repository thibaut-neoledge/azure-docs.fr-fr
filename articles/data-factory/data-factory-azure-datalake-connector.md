---
title: "Déplacer des données vers et depuis Azure Data Lake Store | Microsoft Docs"
description: "Découvrez comment déplacer des données depuis et vers le stockage Azure Data Lake Store à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 1d7ba169675e822ae08a6c679301a0db01e6e338
ms.lasthandoff: 03/24/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Déplacer des données vers et depuis Azure Data Lake Store à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour déplacer des données vers ou à partir d’Azure Data Lake Store. Il s’appuie sur l’article relatif aux [activités de déplacement de données](data-factory-data-movement-activities.md), qui présente une vue d’ensemble du déplacement de données avec l’activité de copie. 

Vous pouvez copier des données à partir de tout magasin de données source pris en charge vers Azure Data Lake Store ou à partir d’Azure Data Lake Store vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez le tableau [Magasins de données pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

> [!NOTE]
> Créez un compte Azure Data Lake Store avant de créer un pipeline avec l’activité de copie pour déplacer des données vers/depuis Azure Data Lake Store. Pour plus d’informations sur Azure Data Lake Store, consultez [Prise en main d’Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec une activité de copie qui déplace les données vers ou à partir d’Azure Data Lake Store à l’aide de différents outils/API.

Le moyen le plus simple de créer un pipeline consiste à utiliser l’**Assistant Copie**. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

Vous pouvez également utiliser les outils suivants pour créer un pipeline : **portail Azure**, **Visual Studio**, **Azure PowerShell**, **modèle Azure Resource Manager**, **API .NET** et **API REST**. Pour connaître la procédure de création d’un pipeline avec une activité de copie, consultez [ce didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Que vous utilisiez les outils ou les API, la création d’un pipeline qui déplace les données d’un magasin de données source vers un magasin de données récepteur implique les étapes suivantes : 

1. Créer des **services liés** pour lier les magasins de données d’entrée et de sorite à votre fabrique de données.
2. Créer des **jeux de données** pour représenter les données d’entrée et de sortie pour l’opération de copie. 
3. Créer un **pipeline** avec une activité de copie qui prend un jeu de données en tant qu’entrée et un jeu de données en tant que sortie. 

Lorsque vous utilisez l’Assistant, les définitions JSON pour ces entités Data Factory (les services liés, les jeux de données et le pipeline) sont créées automatiquement pour vous. Lorsque vous utilisez les outils/API (à l’exception de l’API .NET), vous devez définir ces entités Data Factory à l’aide du format JSON.  Pour obtenir des exemples comportant des définitions JSON pour les entités Data Factory utilisées pour copier les données vers ou à partir d’Azure Data Lake Store, consultez la section [Exemples JSON](#json-examples) de cet article. 

Les sections suivantes offrent des informations détaillées sur les propriétés JSON utilisées pour définir les entités Data Factory propres à Azure Data Lake Store : 

## <a name="linked-service-properties"></a>Propriétés du service lié
Un service lié lie un magasin de données à une fabrique de données. Pour lier votre magasin de données Azure Data Lake Store à votre fabrique de données, vous devez créer un service lié de type **AzureDataLakeStore**. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié à Azure Data Lake Store. Vous pouvez choisir l’authentification d’un **principal du service** et des **informations d’identification utilisateur**.

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **AzureDataLakeStore** | Oui |
| dataLakeStoreUri | Spécifiez des informations à propos du compte Azure Data Lake Store. Il se présente au format suivant : `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Oui |
| subscriptionId | ID d’abonnement Azure auquel appartient le magasin Data Lake Store. | Requis pour le récepteur |
| resourceGroupName | Nom du groupe de ressources Azure auquel appartient le magasin Data Lake Store. | Requis pour le récepteur |

### <a name="using-service-principal-authentication-recommended"></a>Authentification d’un principal du service (recommandée)
Pour utiliser l’authentification d’un principal du service, inscrivez une entité d’application dans Azure Active Directory (AAD) et lui accorder l’accès à Data Lake Store. Consultez la page [Authentification de service à service](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) pour des instructions détaillées. Notez les valeurs suivantes : **ID d’application**, **clé d’application** et **ID de locataire**. Vous utilisez ces informations pour définir le service lié. 

> [!IMPORTANT]
> Si vous utilisez l’Assistant Copie dans le cadre de la création de pipelines de données, veillez à accorder au principal du service au minimum le rôle Lecteur dans Access Control (IAM) pour le compte Data Lake Store au moins l’autorisation Lecture et exécution sur votre racine Data Lake Store (« / »). Sinon, l’erreur « Les informations d’identification fournies ne sont pas valides » peut s’afficher.
>
> Une fois que vous créez/mettez à jour un principal de service dans AAD, il peut falloir quelques minutes avant que les modifications entrent réellement en vigueur. Vérifiez d’abord la configuration des ACL du principal de service et de Data Lake Store. Si vous voyez toujours l’erreur « Les informations d’identification fournies ne sont pas valides », attendez un moment et réessayez.

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| servicePrincipalId | Spécifiez l’ID client de l’application. | Oui |
| servicePrincipalKey | Spécifiez la clé de l’application. | Oui |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du Portail Azure. | Oui |

**Exemple : utilisation de l’authentification d’un principal du service**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>Utilisation de l’authentification des informations d’identification utilisateur
Vous pouvez également utiliser l’authentification des informations d’identification utilisateur pour la copie vers ou à partir de Data Lake Store en spécifiant les propriétés ci-dessous :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| autorisation | Cliquez sur le bouton **Autoriser** dans **Data Factory Editor** et saisissez vos informations d’identification, ce qui affecte l’URL d’autorisation générée automatiquement à cette propriété. | Oui |
| sessionId | ID de session OAuth issu de la session d’autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Ce paramètre est généré automatiquement lorsque vous utilisez Data Factory Editor. | Oui |

**Exemple : utilisation de l’authentification des informations d’identification utilisateur**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiration du jeton
Le code d’autorisation que vous générez à l’aide du bouton **Autoriser** expire au bout d’un certain temps. Consultez le tableau suivant pour connaître les délais d’expiration associés aux différents types de comptes d’utilisateur. Vous pouvez rencontrer le message d’erreur suivant lorsque **le jeton d’authentification expire** :
 
```
"Credential operation error: invalid_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z".
```

| Type d’utilisateur | Expire après |
|:--- |:--- |
| Comptes d’utilisateurs NON gérés par Azure Active Directory (@hotmail.com, @live.com, etc.). |12 heures |
| Comptes d’utilisateurs gérés par Azure Active Directory (AAD) |14 jours après la dernière exécution de tranche de données. <br/><br/>90 jours, si une tranche basée sur un service lié OAuth est exécutée au moins une fois tous les 14 jours. |

Si vous modifiez votre mot de passe avant cette date d’expiration du jeton, le jeton expirera immédiatement et vous verrez l’erreur mentionnée dans cette section.

Pour éviter ou résoudre cette erreur, accordez une nouvelle autorisation à l’aide du bouton **Autoriser** au moment de **l’expiration du jeton**, puis redéployer le service lié. Vous pouvez également générer des valeurs pour les propriétés **sessionId** et **authorization** à l’aide du code fourni dans la section suivante :

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Pour générer les valeurs des propriétés sessionId et authorization au moyen d’un programme

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
Pour plus d’informations sur les classes Data Factory utilisées dans le code, consultez les rubriques [AzureDataLakeStoreLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) et [AuthorizationSessionGetResponse, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Ajoutez une référence à la version **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** pour la classe WindowsFormsWebAuthenticationDialog utilisée dans le code.

## <a name="dataset-properties"></a>Propriétés du jeu de données
Pour spécifier un jeu de données pour représenter les données d’entrée dans un stockage Blob Azure, vous devez définir la propriété type du jeu de données sur **AzureDataLakeStore**. Définissez la propriété **linkedServiceName** du jeu de données sur le nom du service lié Azure Data Lake Store. Pour obtenir une liste complète des sections et propriétés JSON disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.). La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement, le format, etc. des données dans le magasin de données. La section typeProperties correspondant au jeu de données de type **AzureDataLakeStore** a les propriétés suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| folderPath |Chemin d’accès au conteneur et au dossier dans le magasin Azure Data Lake |Oui |
| fileName |Le nom du fichier dans le magasin Azure Data Lake. fileName est facultatif et sensible à la casse. <br/><br/>Si vous spécifiez un nom de fichier, l’activité (y compris la copie) fonctionne sur le fichier spécifique.<br/><br/>Lorsque fileName n’est pas spécifié, la copie inclut tous les fichiers dans le paramètre folderPath du jeu de données d’entrée.<br/><br/>Lorsque fileName n'est pas spécifié pour un jeu de données de sortie, le nom du fichier généré aura ce format dans l'exemple suivant : Data<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Non |
| partitionedBy |partitionedBy est une propriété facultative. Vous pouvez l'utiliser pour spécifier un folderPath dynamique et le nom de fichier pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. Consultez [Utilisation de la propriété partitionedBy](#using-partitionedby-property) pour obtenir plus d’informations et des exemples. |Non |
| format | Les types de formats suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](data-factory-supported-file-and-compression-formats.md#text-format), [format Json](data-factory-supported-file-and-compression-formats.md#json-format), [format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [format Orc](data-factory-supported-file-and-compression-formats.md#orc-format) et [format Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie. |Non |
| compression | Spécifiez le type et le niveau de compression pour les données. Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**. Les niveaux pris en charge sont **Optimal** et **Fastest**. Pour plus d’informations, consultez [Formats de fichiers et de compression pris en charge dans Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Non |

### <a name="using-partitionedby-property"></a>Utilisation de la propriété partitionedBy
Comme mentionné dans la section précédente, vous pouvez spécifier un folderPath et un fileName dynamiques pour les données de série chronologique avec la propriété **partitionedBy**, [les fonctions Data Factory et les variables système](data-factory-functions-variables.md).

Consultez les articles [Création de jeux de données](data-factory-create-datasets.md) et [Planification et exécution](data-factory-scheduling-and-execution.md) pour mieux comprendre les jeux de données de série chronologique, la planification et les segments.

#### <a name="sample-1"></a>Exemple 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Dans cet exemple, {Slice} est remplacé par la valeur de la variable système Data Factory SliceStart au format (AAAAMMJJHH) spécifié. SliceStart fait référence à l'heure de début du segment. folderPath est différent pour chaque segment. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemple 2
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Dans cet exemple, l'année, le mois, le jour et l'heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés folderPath et fileName.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et la stratégie sont disponibles pour tous les types d’activités.

En revanche, les propriétés disponibles dans la section typeProperties de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs

**AzureDataLakeStoreSource** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| recursive |Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. |True (valeur par défaut), False |Non |

**AzureDataLakeStoreSink** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| copyBehavior |Spécifie le comportement de copie. |<b>PreserveHierarchy</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><br/><b>FlattenHierarchy</b> : tous les fichiers du dossier source sont créés dans le premier niveau du dossier cible. Les fichiers cibles sont créés avec le nom généré automatiquement.<br/><br/><b>MergeFiles</b> : fusionne tous les fichiers du dossier source dans un même fichier. Si le nom de fichier/d’objet blob est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement. |Non |

## <a name="json-examples"></a>Exemples JSON
Les exemples suivants présentent des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données vers et depuis Azure Data Lake Store et Azure Blob Storage. Toutefois, les données peuvent être copiées **directement** à partir de n’importe quelle source, vers tout récepteur pris en charge. Pour plus d’informations, consultez la section « Banques de données et formats pris en charge » de l’article [Déplacer des données à l’aide de l’activité de copie](data-factory-data-movement-activities.md).  
## <a name="example-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Exemple : copie de données d’un objet blob Azure vers Azure Data Lake Store
L’exemple suivant montre :

1. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
2. Un service lié de type [AzureDataLakeStore](#linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d'entrée de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureDataLakeStore](#dataset-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) et [AzureDataLakeStoreSink](#copy-activity-properties).

L’exemple copie des données de série horaire depuis un stockage d’objets blob vers Azure Data Lake Store toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure Storage :**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Service lié Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Consultez la section [Propriétés de service lié Azure Data Lake Store](#linked-service-properties) qui comporte les détails de configuration.
>

**Jeu de données d'entrée d'objet Blob Azure :**

Les données sont récupérées à partir d'un nouvel objet Blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois et le jour de début et le nom de fichier utilise l’heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Jeu de données de sortie Azure Data Lake Store :**

L’exemple copie des données dans un magasin Azure Data Lake. De nouvelles données sont copiées dans le magasin Data Lake toutes les heures.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Activité de copie dans un pipeline avec une source Blob et un récepteur Azure Data Lake Store :** 

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **AzureDataLakeStoreSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Exemple : copie de données d’Azure Data Lake Store vers un objet blob Azure
L’exemple suivant montre :

1. Un service lié de type [AzureDataLakeStore](#linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureDataLakeStore](#dataset-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [AzureDataLakeStoreSource](#copy-activity-properties) et [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

L’exemple copie des données appartenant à une série horaire depuis un magasin Azure Data Lake vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure Data Lake Store :**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Consultez la section [Propriétés de service lié Azure Data Lake Store](#linked-service-properties) qui comporte les détails de configuration.
>

**Service lié Azure Storage :**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Jeu de données Azure Data Lake de sortie :**

La définition de **external:true** sur informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à la Data Factory et non produit par une activité dans la Data Factory.

```JSON
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
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
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Jeu de données de sortie Azure Blob :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Activité de copie dans un pipeline avec une source Azure Data Lake Store et un récepteur Blob :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **AzureDataLakeStoreSource** et le type **sink** est défini sur **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

Vous pouvez également mapper les colonnes du jeu de données source sur les colonnes du jeu de données récepteur dans la définition de l’activité de copie. Pour plus d’informations, consultez [Mappage de colonnes de jeux de données dans Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

