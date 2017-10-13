---
title: "Stocker des informations d’identification dans Azure Key Vault | Microsoft Docs"
description: "Découvrez comment stocker les informations d’identification des magasins de données utilisées par un coffre de clés Azure, qui peuvent être récupérées automatiquement par Azure Data Factory au moment de l’exécution."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: cb4545dcd41fe9a2c73bc86096843b717d61a785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Stocker des informations d’identification dans Azure Key Vault

Vous pouvez stocker les informations d’identification des magasins de données dans un coffre de clés [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory récupère les informations d’identification lors de l’exécution d’une activité qui utilise le magasin de données.

> [!NOTE]
> Pour le moment, seul le [connecteur Dynamics](connector-dynamics-crm-office-365.md) prend en charge cette fonctionnalité.

## <a name="steps"></a>Étapes

Lors de la création d’une fabrique de données, une identité du service est créée en même temps que la fabrique. L’identité du service est une application managée qui est inscrite auprès d’Azure Active Directory et qui représente la fabrique de données en question.

- Lors de la création d’une fabrique de données grâce au **Portail Azure ou à PowerShell**, l’identité du service est toujours créée automatiquement depuis la version préliminaire publique.
- Lors de la création d’une fabrique de données grâce au **kit de développement logiciel (SDK)**, l’identité du service n’est créée que si vous spécifiez « Identity = new FactoryIdentity() » durant la création de l’objet usine. Consultez l’exemple [.NET quickstart - create data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Lors de la création d’une fabrique de données grâce à l’**API REST**, l’identité du service n’est créée que si vous le spécifiez la section « identity » dans le corps de la requête. Consultez l’exemple [REST quickstart - create data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory) (Guide de démarrage rapide REST : créer une fabrique de données).

Pour référencer des informations d’identification stockées dans Azure Key Vault, vous devez :

1. Copier l’ID d’application de l’identité du service généré avec la fabrique de données. Consultez [Récupérer l’identité du service](#retrieve-service-identity).
2. Autoriser l’identité du service à accéder à votre coffre de clés Azure Key Vault. Dans votre coffre de clés -> Contrôle d’accès -> Ajouter -> recherchez l’ID d’application de l’identité du service pour lui attribuer une autorisation de lecture. Cela permet à la fabrique désignée d’accéder au secret du coffre de clés.
3. Créer un service lié pointant vers votre coffre de clés Azure Key Vault. Reportez-vous à la section [Service lié Azure Key Vault](#azure-key-vault-linked-service).
4. Créer un service lié de magasin de données, dans lequel référencer le secret correspondant qui est stocké dans le coffre de clés. Reportez-vous à la section [Référencer les informations d’identification stockées dans le coffre de clés](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Récupérer l’identité du service

Vous pouvez récupérer l’identité du service à partir du portail Azure ou par programmation. Les sections suivantes vous montrent quelques exemples.

>[!TIP]
> Si vous ne voyez pas l’identité du service, [générez l’identité de service](#generate-service-identity) en mettant à jour votre fabrique.

### <a name="using-azure-portal"></a>En passant par le portail Azure

Les informations sur l’identité du service se trouvent sur le portail Azure sous : votre fabrique de données -> Paramètres -> Propriétés :

- ID de l’identité du service
- Locataire de l’identité du service
- **ID D’APPLICATION DE L’IDENTITÉ DU SERVICE** : copiez cette valeur pour donner accès à Key Vault

![Récupérer l’identité du service](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>Utiliser PowerShell

L’ID principal et l’ID locataire de l’identité du service seront retournés quand vous aurez obtenu une fabrique de données spécifique comme suit :

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiez l’ID principal, puis exécutez dessous la commande Azure Active Directory avec l’ID principal comme paramètre pour obtenir l’**ID d’application**, que vous utiliserez pour obtenir l’accès à Key Vault :

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Service lié Azure Key Vault

Les propriétés suivantes sont prises en charge pour le service lié Azure Key Vault :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureKeyVault**. | Oui |
| baseUrl | Spécifiez l’URL d’Azure Key Vault. | Oui |

**Exemple :**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Référencer les informations d’identification stockées dans le coffre de clés

Les propriétés suivantes sont prises en charge lorsque vous configurez un champ dans le service lié qui référence un secret de coffre de clés :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type du champ doit être définie sur **AzureKeyVaultSecret**. | Oui |
| secretName | Nom du secret dans le coffre de clés Azure. | Oui |
| secretVersion | Version du secret dans le coffre de clés Azure.<br/>Si elle n’est pas spécifiée, la version la plus récente du secret est utilisée.<br/>Si elle est spécifiée, elle utilise la version spécifiée.| Non |
| store | Fait référence au service lié Azure Key Vault que vous utilisez pour stocker les informations d’identification. | Oui |

**Exemple : (voir la section « password »)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
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

## <a name="generate-service-identity"></a>Générer l’identité du service

Si vous constatez que votre fabrique de données ne possède pas d’identité du service associée à l’instruction [retrieve service identity](#retrieve-service-identity) suivante, vous pouvez en récupérer une en mettant à jour la fabrique de données avec l’initiateur d’identité par programmation.

> [!NOTE]
> - **L’identité du service ne peut pas être modifiée**. La mise à jour d’une fabrique de données pour laquelle vous disposez déjà d’une identité du service n’aura aucun effet ; l’identité du service restera inchangée.
> - **L’identité du service ne peut pas être supprimée**. Si vous mettez à jour une fabrique de données qui dispose déjà d’une identité du service, sans spécifier le paramètre « identity » dans l’objet usine, ou sans spécifier la section « identity » dans le corps de la requête REST, vous recevrez un message d’erreur.

Les sections suivantes montrent différents exemples sur la manière de générer une identité du service pour votre fabrique si elle n’existe pas.

### <a name="using-powershell"></a>Utiliser PowerShell

Appelez la commande **Set-AzureRmDataFactoryV2** à nouveau, vous verrez alors les champs « identity » qui viennent d’être générés :

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>Utilisation de l'API REST

Appelez ensuite l’API avec la section« identity » dans le corps de la requête :

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Corps de la requête** : ajoutez "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Réponse** : l’identité du service est créée automatiquement, et la section « identity » est remplie en conséquence.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>Utiliser le kit de développement logiciel (SDK)

Appelez la fabrique de données pour créer ou mettre à jour la fonction avec Identity=new FactoryIdentity(). Exemple de code utilisant NET :

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).