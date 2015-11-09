<properties
   pageTitle="Modèle Resource Manager pour le stockage | Microsoft Azure"
   description="Affiche le schéma du gestionnaire de ressources pour les comptes de stockage."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# Compte de stockage - schéma de modèle

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

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Oui | **Microsoft.Storage/storageAccounts** | Type de ressource à créer. |
| apiVersion | enum | Oui | **2015-06-15** <br /> **2015-05-01-preview** | Version de l’API à utiliser pour créer la ressource. | 
| name | string | Oui | Entre 3 et 24 caractères, uniquement des chiffres et des lettres minuscules | Nom du compte de stockage à créer. Le nom doit être unique dans tout Azure. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms, comme illustré dans l’exemple ci-dessous. |
| location | string | Oui | Pour déterminer les régions valides, consultez [Régions prises en charge](resource-manager-supported-services.md#supported-regions). | Région dans laquelle héberger le compte de stockage. |
| properties | objet | Oui | (voir ci-dessous) | Objet qui spécifie le type de compte de stockage à créer.

### objet propriétés

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | string | Oui | **Standard\_LRS**<br />**Standard\_ZRS**<br />**Standard\_GRS**<br />**Standard\_RAGRS**<br />**Premium\_LRS** | Type de compte de stockage. Les valeurs autorisées correspondent à Standard Locally Redundant, Standard Zone Redundant, Standard Geo-Redundant, Standard Read-Access Geo-Redundant et Premium Locally Redundant. Pour plus d’informations sur ces types de comptes, consultez [Réplication Azure Storage](./storage/storage-redundancy.md). |

	
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

## Étapes suivantes

- Pour obtenir des informations générales sur le stockage, consultez [Introduction à Microsoft Azure Storage](./storage/storage-introduction.md).
- Par obtenir des exemples de modèles qui utilisent un nouveau compte de stockage avec une machine virtuelle, consultez [Déployer une machine virtuelle Linux simple](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=Nov15_HO1-->