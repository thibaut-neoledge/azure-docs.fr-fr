---
title: Transfert de données à partir de magasins de données ODBC | Microsoft Docs
description: Apprenez à transférer des données à partir de magasins de données ODBC à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: jingwang

---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Transfert de données à partir de magasins de données ODBC à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie d’une fabrique de données Azure pour déplacer des données entre un magasin de données ODBC local et un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Actuellement, Data Factory prend uniquement en charge le déplacement de données d’un magasin de données ODBC local vers d’autres magasins de données. Il ne prend pas en charge le déplacement de données à partir d’autres magasins de données vers un magasin de données ODBC local.

## <a name="enabling-connectivity"></a>Activation de la connectivité
Le service Data Factory prend en charge la connexion à des sources ODBC locales à l’aide de la passerelle de gestion des données. Consultez l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle. Utilisez la passerelle pour vous connecter à un magasin de données ODBC même si elle est hébergée sur une machine virtuelle IaaS Azure. 

Vous pouvez installer la passerelle sur le même ordinateur local ou la machine virtuelle Azure comme magasin de données ODBC. Toutefois, nous vous recommandons d’installer la passerelle sur un ordinateur/une machine virtuelle IaaS Azure distinct(e) afin d’éviter les conflits de ressources, ainsi que pour obtenir de meilleures performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure d’accéder à l’ordinateur sur lequel réside le magasin de données ODBC. 

En dehors de la passerelle de gestion des données, vous devez également installer le pilote ODBC pour le magasin de données de l’ordinateur de passerelle. 

> [!NOTE]
> Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle. 
> 
> 

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données depuis une source ODBC consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données. 

Les exemples suivants présentent des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données depuis une source ODBC vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores) , via l’activité de copie d’Azure Data Factory.

## <a name="sample:-copy-data-from-odbc-data-store-to-azure-blob"></a>Exemple : copie des données depuis un magasin de données ODBC vers un objet Blob Azure
Cet exemple indique comment copier des données depuis un magasin de données ODBC vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores) , via l’activité de copie d’Azure Data Factory.  

L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [OnPremisesOdbc](#odbc-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#odbc-dataset-type-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#odbc-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie toutes les heures les données de résultat d’une requête d’un magasin de données ODBC en local vers un objet blob. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples. 

Dans un premier temps, configurez la passerelle de gestion des données. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) . 

**Service lié de HDFS** : cet exemple utilise l’authentification de base. Consultez la section [Service lié ODBC](#odbc-linked-service-properties) pour connaître les différents types d’authentification que vous pouvez utiliser. 

    {
        "name": "OnPremOdbcLinkedService",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
                "userName": "username",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }

**Service lié Azure Storage**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Jeu de données d’entrée ODBC**

L’exemple suppose que vous avez créé une table « MyTable » dans une base de données ODBC et qu’elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.

La définition de « external » : « true» informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory.

    {
        "name": "ODBCDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremOdbcLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }



**Jeu de données de sortie Azure Blob**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

    {
        "name": "AzureBlobOdbcDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configurée pour utiliser ces jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.

    {
        "name": "CopyODBCToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "OdbcDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOdbcDataSet"
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
                    "name": "OdbcToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="odbc-linked-service-properties"></a>Propriétés du service lié ODBC
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié ODBC.

| Propriété | Description | Requis |
| --- | --- | --- |
| type |Le type de propriété doit être défini sur : **OnPremisesOdbc** |Oui |
| connectionString |Partie de la chaîne de connexion ne contenant pas les informations d’accès, avec des informations d’identification chiffrées facultatives. Consultez les exemples dans les sections suivantes. |Oui |
| credential |Partie de la chaîne de connexion contenant les informations d’accès, spécifiée dans un format de valeurs de propriété spécifique au pilote. Exemple : « Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>; ». |Non |
| authenticationType |Type d’authentification utilisé pour se connecter au magasin de données ODBC. Les valeurs possibles sont : Anonyme et De base. |Oui |
| username |Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. |Non |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. |Non |
| gatewayName |Nom de la passerelle que le service Data Factory doit utiliser pour se connecter au magasin de données ODBC. |Oui |

Pour en savoir plus sur la définition des informations d’identification pour un magasin de données ODBC local, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="using-basic-authentication"></a>Utilisation de l’authentification de base
    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
                "userName": "username",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }

### <a name="using-basic-authentication-with-encrypted-credentials"></a>Utilisation de l’authentification de base avec des informations d’identification chiffrées
Vous pouvez chiffrer les informations d’identification à l’aide de l’applet de commande [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (version 1.0 d’Azure PowerShell) ou [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (version 0.9 ou antérieure d’Azure PowerShell).  

    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-anonymous-authentication"></a>Utilisation de l’authentification anonyme
    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
                "credential": "UID={uid};PWD={pwd}",
                "gatewayName": "mygateway"
            }
        }
    }



## <a name="odbc-dataset-type-properties"></a>Propriétés de type du jeu de données ODBC
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties du jeu de données de type **RelationalTable** (qui inclut le jeu de données ODBC) présente les propriétés suivantes

| Propriété | Description | Requis |
| --- | --- | --- |
| TableName |Nom de la table dans le magasin de données ODBC. |Oui |

## <a name="odbc-copy-activity-type-properties"></a>Propriétés de type de l’activité de copie ODBC
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d'activités. 

En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Dans l’activité de copie, quand la source est de type **RelationalSource** (ce qui inclut ODBC), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| query |Utilise la requête personnalisée pour lire des données. |Chaîne de requête SQL. Par exemple : select * from MyTable. |Oui |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odbc"></a>Mappage de type pour ODBC
Comme mentionné dans l’article consacré aux [activités de déplacement de données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en deux étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Lors du déplacement de données à partir de magasins de données ODBC, les types de données ODBC sont mappés aux types .NET, comme indiqué dans la rubrique [Mappages de types de données ODBC](https://msdn.microsoft.com/library/cc668763.aspx) .

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="ge-historian-store"></a>Magasin GE Historian
Vous créez un service lié ODBC pour lier un magasin de données [GE Proficy Historian (désormais GE Historian)](http://www.geautomation.com/products/proficy-historian) à une fabrique de données Azure comme l’indique l’exemple suivant : 

    {
        "name": "HistorianLinkedService",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "connectionString": "DSN=<name of the GE Historian store>",
                "gatewayName": "<gateway name>",
                "authenticationType": "Basic",
                "userName": "<user name>",
                "password": "<password>"
            }
        }
    }

Installez la passerelle de gestion des données sur un ordinateur local et enregistrez la passerelle auprès du portail. La passerelle installée sur votre ordinateur local utilise le pilote ODBC pour GE Historian afin de se connecter au magasin de données GE Historian. Par conséquent, installez le pilote s’il n’est pas déjà installé sur l’ordinateur passerelle. Consultez la section [Activation de la connectivité](#enabling-connectivity) pour plus d’informations.

Avant d'utiliser le magasin GE Historian dans une solution Data Factory, vérifiez que la passerelle peut se connecter au magasin de données en suivant les instructions de la section suivante. 

Lisez l'article depuis le début pour une présentation détaillée de l'utilisation de magasins de données ODBC en tant que magasins de données sources dans une opération de copie.  

## <a name="troubleshoot-connectivity-issues"></a>Résoudre les problèmes de connectivité
Pour résoudre les problèmes de connexion, utilisez l’onglet **Diagnostics** du **Gestionnaire de configuration de la passerelle de gestion des données**. 

1. Lancez le **Gestionnaire de configuration de la passerelle de gestion des données**. Vous pouvez exécuter directement « C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe » (ou) rechercher **Passerelle** pour trouver un lien vers l’application **Passerelle de gestion des données de Microsoft**, comme l’illustre l’image suivante. 
   
    ![Rechercher la passerelle](./media/data-factory-odbc-connector/search-gateway.png)
2. Basculez vers l’onglet **Diagnostics** .
   
    ![Diagnostics de la passerelle](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png) 
3. Sélectionnez le **type** de magasin de données (service lié). 
4. Spécifiez **l’authentification** et entrez les **informations d’identification** (ou) entrez la **chaîne de connexion** utilisée pour vous connecter au magasin de données. 
5. Cliquez sur **Tester la connexion** pour tester la connexion au magasin de données. 

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!--HONumber=Oct16_HO2-->


