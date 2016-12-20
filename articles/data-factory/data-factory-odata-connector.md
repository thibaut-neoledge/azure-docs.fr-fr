---
title: "Déplacer des données à partir de sources OData | Microsoft Docs"
description: "Apprenez à déplacer des données à partir de sources OData à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 5e6a61011e201dad2220d2851212ac04bed1342c


---
# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Déplacer des données depuis une source OData à l’aide d’Azure Data Factory
Cet article explique comment utiliser l'activité de copie d’une fabrique de données Azure pour déplacer des données d’OData vers un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

## <a name="supported-versions-and-authentication-types"></a>Versions et types d’authentification pris en charge
Ce connecteur OData prend en charge OData versions 3.0 et 4.0, et vous pouvez copier des données à partir de sources OData cloud et locales. Dans le second cas, vous devez installer la passerelle de gestion des données. Pour plus d’informations sur la passerelle de gestion de données, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) .

Les types d’authentification suivants sont pris en charge :

* Pour accéder au flux OData **cloud**, vous pouvez utiliser l’authentification OAuth basée sur Azure Active Directory, anonyme ou de base (nom d’utilisateur et mot de passe).
* Pour accéder au flux OData **local**, vous pouvez utiliser l’authentification anonyme, de base (nom d’utilisateur et mot de passe) ou Windows.

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données depuis une source OData consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

Les exemples suivants présentent des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données depuis une source OData vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.

## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Exemple : copie de données d’une source OData vers Azure Blob
Cet exemple indique comment copier des données depuis une source OData vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie de Microsoft Azure Data Factory.  

L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [OData](#odata-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [ODataResource](#odata-dataset-type-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#odata-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'exemple copie toutes les heures les données provenant de l’interrogation d'une source OData vers un objet blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié OData** : cet exemple utilise l’authentification de base. Consultez la section [Service lié OData](#odata-linked-service-properties) pour connaître les différents types d’authentification que vous pouvez utiliser.

    {
        "name": "ODataLinkedService",
           "properties":
        {
            "type": "OData",
               "typeProperties":
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
               }
           }
    }


**Service lié Azure Storage**

    {
          "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
          }
    }

**Jeu de données d’entrée OData**

La définition de « external » : « true» informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory.

    {
        "name": "ODataDataset",
        "properties":
        {
            "type": "ODataResource",
            "typeProperties":
            {
                 "path": "Products"
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3                
            }
        }
    }

La spécification d’un **chemin d’accès** dans la définition du jeu de données est facultative.

**Jeu de données de sortie Azure Blob**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline avec activité de copie**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les dernières (nouvelles) données de la source OData.

    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


La spécification de la **requête** dans la définition du pipeline est facultative. L’ **URL** que le service Data Factory utilise pour récupérer les données est : URL spécifiée dans le service lié (obligatoire) + chemin d'accès spécifié dans le jeu de données (facultatif) + requête dans le pipeline (facultatif).

## <a name="odata-linked-service-properties"></a>Propriétés du service OData
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié OData.

| Propriété | Description | Requis |
| --- | --- | --- |
| type |La propriété de type doit être définie sur **OData** |Oui |
| URL |URL du service OData. |Oui |
| authenticationType |Type d’authentification utilisé pour se connecter à la source OData. <br/><br/> Pour OData dans le cloud, les valeurs possibles sont Anonyme, De base et OAuth (notez qu’à l’heure actuelle, Azure Data Factory prend en charge uniquement l’authentification OAuth basée sur Azure Active Directory). <br/><br/> Pour OData en local, les valeurs possibles sont Anonyme, De base et Windows. |Oui |
| username |Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. |Oui (uniquement si vous utilisez l’authentification de base) |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. |Oui (uniquement si vous utilisez l’authentification de base) |
| authorizedCredential |Si vous utilisez OAuth, cliquez sur le bouton **Autoriser** de l’Assistant de copie Data Factory ou de l’éditeur et entrez vos informations d’identification. La valeur de cette propriété sera alors générée automatiquement. |Oui (uniquement si vous utilisez l’authentification OAuth) |
| gatewayName |Nom de la passerelle que le service Data Factory doit utiliser pour se connecter au service OData local. Indiquez uniquement si vous copiez des données à partir de la source OData locale. |Non |

### <a name="using-basic-authentication"></a>Utilisation de l’authentification de base
    {
        "name": "inputLinkedService",
        "properties":
        {
            "type": "OData",
               "typeProperties":
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
               "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Utilisation de l’authentification anonyme
    {
        "name": "ODataLinkedService",
           "properties":
        {
            "type": "OData",
            "typeProperties":
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Utilisation de l’authentification Windows pour accéder à la source OData locale
    {
        "name": "inputLinkedService",
        "properties":
        {
            "type": "OData",
               "typeProperties":
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
               "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Utilisation de l’authentification OAuth pour accéder à la source OData dans le cloud
    {
        "name": "inputLinkedService",
        "properties":
        {
            "type": "OData",
               "typeProperties":
            {
               "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc">",
               "authenticationType": "OAuth",
               "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
           }
       }
    }

## <a name="odata-dataset-type-properties"></a>Propriétés de type du jeu de données OData
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties du jeu de données de type **ODataResource** (qui inclut le jeu de données OData) présente les propriétés suivantes

| Propriété | Description | Requis |
| --- | --- | --- |
| chemin d’accès |Chemin d'accès à la ressource OData |Non |

## <a name="odata-copy-activity-type-properties"></a>Propriétés du type de l’activité de copie OData
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et la stratégie sont disponibles pour tous les types d’activités.

En revanche, les propriétés disponibles dans la section typeProperties de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Lorsque la source est de type **RelationalSource** (qui inclut OData), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Exemple | Requis |
| --- | --- | --- | --- |
| query |Utilise la requête personnalisée pour lire des données. |"?$select=Name, Description&$top=5" |Non |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Mappage de type pour OData
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement les types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Lorsque que déplacez des données à partir de magasins de données OData, les types de données OData sont mappés aux types .NET.

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.



<!--HONumber=Nov16_HO3-->


