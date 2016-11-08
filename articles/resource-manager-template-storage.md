---
title: Modèle Resource Manager pour le stockage | Microsoft Docs
description: Affiche le schéma Resource Manager pour le déploiement de comptes de stockage par le biais d'un modèle.
services: azure-resource-manager,storage
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz

---
# Compte de stockage, schéma de modèle
Crée un compte de stockage.

## Format de schéma
Pour créer un compte de stockage, ajoutez le schéma suivant à la section des ressources de votre modèle.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## Valeurs
Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| --- | --- |
| type |Enum<br />Requis<br />**Microsoft.Storage/storageAccounts**<br /><br />Le type de ressource à créer. |
| apiVersion |Enum<br />Requis<br />**2015-06-15** ou **2015-05-01-preview**<br /><br />La version de l’API à utiliser pour la création de la ressource. |
| name |String<br />Requis<br />Entre 3 et 24 caractères, uniquement des chiffres et des lettres minuscules.<br /><br />Nom du compte de stockage à créer. Le nom doit être unique dans tout Azure. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms, comme illustré dans l’exemple ci-dessous. |
| location |String<br />Requis<br />Une région qui prend en charge les comptes de stockage. Pour déterminer les régions valides, consultez [Régions prises en charge](resource-manager-supported-services.md#supported-regions).<br /><br />Région dans laquelle héberger le compte de stockage. |
| properties |Object<br />Requis<br />[properties object](#properties)<br /><br />Objet qui spécifie le type de compte de stockage à créer. |

<a id="properties" />

### objet propriétés
| Nom | Valeur |
| --- | --- |
| accountType |String<br />Requis<br />**Standard\_LRS**, **Standard\_ZRS**, **Standard\_GRS**, **Standard\_RAGRS** ou **Premium\_LRS**<br /><br />Type de compte de stockage. Les valeurs autorisées correspondent à Standard Locally Redundant, Standard Zone Redundant, Standard Geo-Redundant, Standard Read-Access Geo-Redundant et Premium Locally Redundant. Pour plus d’informations sur ces types de comptes, consultez [Réplication Azure Storage](storage/storage-redundancy.md). |

## Exemples
L’exemple suivant déploie un compte de stockage Standard Locally Redundant avec un nom unique basé sur l’ID du groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## Modèles de démarrage rapide
Il existe de nombreux modèles de démarrage rapide qui incluent un compte de stockage. Les modèles suivants illustrent des scénarios courants :

* [Création d'un compte de stockage standard](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
* [Déploiement simple d'une machine virtuelle Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
* [Déploiement simple d'une machine virtuelle Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
* [Création d'un profil CDN, d'un point de terminaison CDN avec un compte de stockage comme origine](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
* [Création d'une batterie de serveurs SharePoint à haute disponibilité avec 9 machines virtuelles à l'aide de l'extension DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
* [Déploiement simple d'un cluster Service Fabric sécurisé à 5 nœuds avec WAD activé](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
* [Création d'une machine virtuelle à partir d'une image Windows avec 4 disques de données vides](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)

## Étapes suivantes
* Pour obtenir des informations générales sur le stockage, consultez [Introduction à Microsoft Azure Storage](storage/storage-introduction.md).
* Par obtenir des exemples de modèles qui utilisent un nouveau compte de stockage avec une machine virtuelle, consultez [Déployer une machine virtuelle Linux simple](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=AcomDC_0406_2016-->