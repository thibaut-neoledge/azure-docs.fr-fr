---
title: "Copier des données de Dynamics CRM et 365 à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de Dynamics CRM et 365 vers des banques de données réceptrices prises en charge."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copier des données de Dynamics 365/Dynamics CRM à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de Dynamics 365/Dynamics CRM. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de Dynamics 365/Dynamics CRM vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Dynamics prend en charge les versions et les types d’authentification de Dynamics ci-dessous :

| Versions de Dynamics | Types d’authentification | Exemples de services liés |
|:--- |:--- |:--- |
| Dynamics 365 (en ligne) <br> Dynamics CRM (en ligne) | office365 | [Dynamics en ligne + authentification Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local avec IFD <br> Dynamics CRM 2016 local avec IFD <br> Dynamics CRM 2015 local avec IFD | IFD | [Dynamics local avec IFD + authentification IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD sont les initiales de « Internet Facing Deployment » (déploiement avec accès via Internet).*

> [!NOTE]
> Pour utiliser le connecteur Dynamics, stockez votre mot de passe dans Azure Key Vault et laissez l’activité de copie d’Azure Data Factory le récupérer lors de l’exécution d’une copie de données. Découvrez comment configurer dans la section relative aux [propriétés de service lié](#linked-service-properties).

## <a name="getting-started"></a>Prise en main

Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de Dynamics.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Dynamics sont les suivantes :

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 et Dynamics CRM en ligne

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **Dynamics**. | Oui |
| deploymentType | Type de déploiement de l’instance Dynamics. Doit être **« Online »** pour Dynamics en ligne. | Oui |
| organizationName | Nom d’organisation de l’instance Dynamics. | Non, à spécifier quand il y a plusieurs instances Dynamics associées à l’utilisateur. |
| authenticationType | Type d’authentification pour se connecter au serveur Dynamics. Spécifiez **« Office365 »** pour Dynamics en ligne. | Oui |
| username | Spécifiez le nom d’utilisateur pour la connexion à Dynamics. | Oui |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Vous devez placer le mot de passe dans Azure Key Vault et le configurer en tant que « AzureKeyVaultSecret ». Pour plus d’informations, voir l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |

**Exemple : Dynamics en ligne utilisant l’authentification Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 et Dynamics CRM locaux avec IFD

*Les propriétés supplémentaires en comparaison de Dynamics en ligne sont « hostName » et « port ».*

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **Dynamics**. | Oui |
| deploymentType | Type de déploiement de l’instance Dynamics. Doit être **« OnPremisesWithIfd »** pour Dynamics local avec IFD.| Oui |
| **hostName** | Nom d’hôte du serveur Dynamics local. | Oui |
| **port** | Port du serveur de Dynamics local. | Non, la valeur par défaut est 443 |
| organizationName | Nom d’organisation de l’instance Dynamics. | Oui |
| authenticationType | Type d’authentification pour se connecter au serveur Dynamics. Spécifiez **« Ifd »** pour Dynamics local avec IFD. | Oui |
| username | Spécifiez le nom d’utilisateur pour la connexion à Dynamics. | Oui |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Notez que vous devez placer le mot de passe dans Azure Key Vault et le configurer en tant que « AzureKeyVaultSecret ». Pour plus d’informations, voir l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |

**Exemple : Dynamics local avec IFD utilisant l’authentification IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Dynamics.

Pour copier des données de Dynamics, affectez la valeur **DynamicsEntity** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **DynamicsEntity** |Oui |
| entityName | Nom logique de l’entité à récupérer. | Non (si « query » est spécifié dans la source de l’activité) |

> [!IMPORTANT]
> **La section « structure » dans le jeu de données est requise pour Dynamics**, qui définit un nom de colonne et un type de données pour les données de Dynamics que vous souhaitez copier. Pour en savoir plus, découvrez la [structure du jeu de données](concepts-datasets-linked-services.md#dataset-structure) et le [mappage de type de données pour Dynamics](#data-type-mapping-for-dynamics).

**Exemple :**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Dynamics.

### <a name="dynamics-as-source"></a>Dynamics en tant que source

Pour copier des données de Dynamics, définissez le type de source dans l’activité de copie sur **DynamicsSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **DynamicsSource**  | Oui |
| query  | FetchXML est un langage de requête propriétaire qui est utilisé dans Microsoft Dynamics (en ligne et local). Voyez l’exemple ci-dessous et apprenez-en davantage en lisant [Générer des requêtes avec FetchXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Non (si « entityName » est spécifié dans le jeu de données)  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exemple de requête FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

## <a name="data-type-mapping-for-dynamics"></a>Mappage de type de données pour Dynamics

Lors de la copie de données de Dynamics, les mappages suivants sont utilisés entre les types de données Dynamics et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

Configurez le type de données Azure Data Factory correspondant dans la structure du jeu de données en fonction du type de données Dynamics de votre source à l’aide de la table de mappage ci-dessous :

| Type de données Dynamics | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| AttributeTypeCode.BigInt | long |
| AttributeTypeCode.Boolean | Boolean |
| AttributeType.Customer | Guid |
| AttributeType.DateTime | Datetime |
| AttributeType.Decimal | Décimal |
| AttributeType.Double | Double |
| AttributeType.EntityName | String |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | Guid |
| AttributeType.ManagedProperty | Boolean |
| AttributeType.Memo | String |
| AttributeType.Money | Décimal |
| AttributeType.Owner | Guid |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | Guid |
| AttributeType.String | String |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Les types de données Dynamics AttributeType.CalendarRules et AttributeType.PartyList ne sont pas pris en charge.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).