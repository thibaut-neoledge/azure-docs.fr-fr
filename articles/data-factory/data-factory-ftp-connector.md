---
title: "Déplacement de données à partir d’un serveur FTP | Microsoft Docs"
description: "Découvrez comment déplacer des données depuis un serveur FTP à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Déplacer des données depuis un serveur FTP à l’aide d’Azure Data Factory
Cet article explique comment utiliser l'activité de copie d’une fabrique de données Azure pour déplacer des données d’un serveur FTP vers un magasin de données récepteur pris en charge. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et la liste de magasins de données pris en charge comme sources/récepteurs.

Actuellement, Data Factory prend uniquement en charge le déplacement de données d’un serveur FTP vers d’autres magasins de données, mais pas l’inverse. Il prend en charge à la fois les serveurs FTP locaux et cloud.

Si vous déplacez des données d’un serveur FTP **local** vers un magasin de données cloud (exemple : stockage Blob Azure), installez et utilisez la passerelle de gestion des données. La passerelle de gestion des données est un agent client qui est installé sur votre ordinateur local et permet aux services cloud de se connecter à une ressource locale. Consultez [Passerelle de gestion des données](data-factory-data-management-gateway.md) pour plus d’informations sur la passerelle. Consultez l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) pour obtenir des instructions détaillées sur la configuration de la passerelle et son utilisation. Vous utilisez la passerelle pour vous connecter à un serveur FTP, même si le serveur est sur une machine virtuelle Azure IaaS (VM).

Vous pouvez installer la passerelle sur le même ordinateur local ou la machine virtuelle Azure IaaS comme serveur FTP. Toutefois, nous vous recommandons d’installer la passerelle sur un ordinateur/une machine virtuelle IaaS Azure distinct(e) afin d’éviter les conflits de ressources, ainsi que pour obtenir de meilleures performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure au serveur FTP.

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données depuis un serveur FTP consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

Les exemples suivants présentent des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Exemple : copie de données depuis un serveur FTP à un objet Blob Azure
Cet exemple indique comment copier des données à partir d’un serveur FTP vers un stockage des objets blob Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.  

L’exemple contient les entités de fabrique de données suivantes :

* Un service lié de type [FtpServer](#ftp-linked-service-properties).
* Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
* Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [FileShare](#fileshare-dataset-type-properties).
* Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Un [pipeline](data-factory-create-pipelines.md) avec activité de copie qui utilise [FileSystemSource](#ftp-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie des données d’un serveur FTP vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service FTP lié** Cet exemple utilise l’authentification de base avec le nom d’utilisateur et le mot de passe en texte brut. Vous pouvez également utiliser une des méthodes suivantes :

* Authentification anonyme
* Authentification de base avec des informations d’identification chiffrées
* FTP sur SSL/TLS (FTPS)

Consultez la section [Service lié FTP](#ftp-linked-service-properties) pour connaître les différents types d’authentification que vous pouvez utiliser.

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",           
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
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

**Jeu de données d’entrée FTP** Ce jeu de données fait référence au dossier FTP `mysharedfolder` et au fichier `test.csv`. Le pipeline copie le fichier vers la destination.

La définition de « external » : « true» informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory.

    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Jeu de données de sortie d’objet Blob Azure**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **FileSystemSource** et le type **sink** est défini sur **BlobSink**.

    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
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
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Propriétés du service lié FTP
Le tableau suivant fournit la description des éléments JSON spécifiques du service lié FTP.

| Propriété | Description | Requis | Default |
| --- | --- | --- | --- |
| type |La propriété de type doit être définie sur FtpServer |Oui |&nbsp; |
| host |Nom ou adresse IP du serveur FTP |Oui |&nbsp; |
| authenticationType |Spécification du type d’authentification |Oui |Basic, anonyme |
| username |L’utilisateur ayant accès au serveur FTP |Non |&nbsp; |
| password |Mot de passe de l’utilisateur (nom d’utilisateur) |Non |&nbsp; |
| Encryptedcredential |Informations d’identification chiffrées pour accéder au serveur FTP |Non |&nbsp; |
| gatewayName |Nom de la passerelle de gestion des données pour se connecter à un serveur FTP local |Non |&nbsp; |
| port |Port sur lequel le serveur FTP écoute |Non |21 |
| enableSsl |Indiquez si vous souhaitez utiliser FTP sur un canal SSL/TLS |Non |true |
| enableServerCertificateValidation |Spécifiez si vous souhaitez activer validation des certificats SSL lors de l’utilisation de FTP sur un canal SSL/TLS |Non |true |

### <a name="using-anonymous-authentication"></a>Utilisation de l’authentification anonyme
    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {        
                "authenticationType": "Anonymous",
                  "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Utilisation d’un nom d'utilisateur et d’un mot de passe en texte brut pour l’authentification de base
    {
        "name": "FTPLinkedService",
          "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
          }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Utilisation de port, enableSsl, enableServerCertificateValidation
    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",    
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Utilisation d’encryptedCredential pour l’authentification et la passerelle
    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
          }
    }

Consultez [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour plus d’informations sur la définition des informations d’identification pour une source de données FTP locale.

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propriétés de type de l’activité de copie FTP
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d'activités.

En revanche, les propriétés disponibles dans la section typeProperties de l'activité varient pour chaque type d'activité. Pour l’activité de copie, les propriétés de type dépendent des types de sources et récepteurs.

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

* [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec Activité de copie.



<!--HONumber=Nov16_HO3-->


