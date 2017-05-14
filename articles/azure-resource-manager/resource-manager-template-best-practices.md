---
title: "Bonnes pratiques relatives à la création de modèles Azure Resource Manager | Microsoft Docs"
description: "Instructions pour simplifier vos modèles Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 24c2b27948e9373bc3957f706ed802cc36c04148
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Bonnes pratiques relatives à la création de modèles Azure Resource Manager
Ces instructions vous aideront à créer des modèles Azure Resource Manager fiables et faciles à utiliser. Ces instructions sont uniquement des suggestions. Il ne s’agit pas de spécifications requises, sauf indication contraire. Votre scénario peut nécessiter la variation de l’un des exemples ou approches suivantes.

## <a name="resource-names"></a>Noms de ressource
Il existe généralement trois types de noms de ressource avec lesquels vous travaillez dans Resource Manager :

* des noms de ressource qui doivent être uniques ;
* des noms des ressources qui ne doivent pas obligatoirement être uniques, mais pour lesquels vous choisissez un nom qui vous permettra d’identifier la ressource en fonction du contexte ;
* des noms de ressources qui peuvent être génériques.

Pour établir une convention de nommage, voir [Instructions de dénomination d’infrastructure Azure](../virtual-machines/windows/infrastructure-naming-guidelines.md). Pour plus d’informations sur les restrictions de noms de ressource, consultez [Conventions d’affectation de noms recommandées pour les ressources Azure](../guidance/guidance-naming-conventions.md).

### <a name="unique-resource-names"></a>Noms de ressource uniques
Vous devez fournir un nom de ressource unique pour tout type de ressource disposant d’un point de terminaison d’accès aux données. Certains types de ressource courants nécessitent un nom unique, notamment :

* Azure Storage<sup>1</sup> 
* Fonctionnalité Web Apps d’Azure App Service
* SQL Server
* coffre de clés Azure
* Cache Redis Azure
* Azure Batch
* Azure Traffic Manager
* Recherche Azure
* Azure HDInsight

<sup>1</sup> Les noms de compte de stockage doivent être en minuscules, comporter 24 caractères au maximum et ne pas comprendre de traits d’union.

Si vous fournissez un paramètre pour un nom de ressource, vous devez fournir un nom unique lors du déploiement de la ressource. Vous pouvez éventuellement créer une variable qui utilise la fonction [uniqueString()](resource-group-template-functions-string.md#uniquestring) pour générer un nom. 

Vous pouvez également ajouter un préfixe ou un suffixe au résultat.**uniqueString**. La modification du nom unique peut vous aider à identifier plus facilement le type de ressource à partir du nom. Par exemple, vous pouvez générer un nom unique pour un compte de stockage avec la variable suivante :

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Noms de ressource pour l’identification
Pour certains types de ressources, les noms ne doivent pas nécessairement être uniques. Pour ces types de ressources, vous pouvez fournir un nom qui identifie le contexte de la ressource et le type de ressource. Fournissez un nom descriptif qui vous permet d’identifier la ressource dans une liste de ressources. Si vous devez utiliser un nom de ressource différent pour d’autres déploiements, vous pouvez utiliser un paramètre pour le nom :

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

S’il est inutile de transmettre un nom au cours du déploiement, vous pouvez utiliser une variable : 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

Vous pouvez également utiliser une valeur codée en dur :

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>Noms de ressource génériques
Pour les types de ressources qui sont accessibles en grande partie par le biais d’une autre ressource, vous pouvez utiliser un nom générique codé en dur dans le modèle. Par exemple, vous pouvez définir un nom générique standard pour les règles de pare-feu sur un serveur SQL :

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>parameters
Les informations suivantes peuvent être utiles lorsque vous travaillez avec des paramètres :

* Réduisez l’utilisation des paramètres. Si possible, utilisez une variable ou une valeur littérale. Utilisez des paramètres uniquement pour les scénarios suivants :
   
   * les paramètres que vous souhaitez utiliser varient en fonction de l’environnement (référence SKU, taille, capacité) ;
   * les noms de ressources que vous souhaitez spécifier pour faciliter l’identification ;
   * les valeurs que vous utilisez souvent pour effectuer d’autres tâches (par exemple, le nom d’utilisateur administrateur) ;
   * les clés secrètes (notamment les mots de passe) ;
   * le nombre ou le tableau de valeurs à utiliser lors de la création de plusieurs instances d’un type de ressource.
* Utilisez la casse mixte pour les noms de paramètre.
* Fournissez une description dans les métadonnées pour chaque paramètre :

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Définissez des valeurs par défaut pour les paramètres (à l’exception des mots de passe et des clés SSH) :
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Utilisez **SecureString** pour tous les mots de passe et les clés secrètes : 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Si possible, n’utilisez pas de paramètre pour spécifier l’emplacement. Au lieu de cela, utilisez la propriété **d’emplacement** du groupe de ressources. Avec l’expression **resourceGroup ().location** pour toutes vos ressources, les ressources dans le modèle sont déployées dans le même emplacement que le groupe de ressources :
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Si un type de ressource est uniquement pris en charge dans un nombre limité d’emplacements, envisagez de spécifier un emplacement valide directement dans le modèle. Si vous devez utiliser un paramètre **d’emplacement**, partagez cette valeur de paramètre autant que possible avec les ressources qui sont susceptibles de se trouver dans le même emplacement. Cela réduit le nombre de fois où les utilisateurs sont invités à fournir des informations d’emplacement.
* Évitez d’utiliser un paramètre ou une variable pour la version de l’API pour un type de ressource. Les propriétés de ressource et les valeurs peuvent varier selon le numéro de version. IntelliSense dans des éditeurs de code n’est pas en mesure de déterminer le schéma correct lorsque la version de l’API est définie sur un paramètre ou une variable. Au lieu de cela, codez en dur la version de l’API dans le modèle.

## <a name="variables"></a>variables
Les informations suivantes peuvent être utiles lorsque vous travaillez avec des variables :

* Utilisez des variables pour les valeurs que vous devez utiliser plusieurs fois dans un modèle. Si une valeur est utilisée une seule fois, une valeur codée en dur rend votre modèle plus facile à lire.
* Vous ne pouvez pas utiliser la fonction [référence](resource-group-template-functions-resource.md#reference) dans la section **variables** du modèle. La fonction **référence** dérive sa valeur de l’état d’exécution de la ressource. Toutefois, les variables sont résolues lors de l’analyse initiale du modèle. Construisez des valeurs qui ont besoin de la fonction **référence** directement dans la section **ressources** ou **outputs** du modèle.
* Incluez des variables pour les noms de ressource qui doivent être uniques, comme indiqué dans [Noms de ressources](#resource-names).
* Vous pouvez regrouper des variables dans des objets complexes. Utilisez le format **variable.subentry** pour référencer une valeur à partir d’un objet complexe. Le regroupement de variables peut vous aider à effectuer le suivi des variables liées. Il améliore également la lisibilité du modèle. Voici un exemple :
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > Un objet complexe ne peut pas contenir une expression qui fait référence à la valeur d’un objet complexe. Définissez une variable distincte à cette fin.
   > 
   > 
   
     Pour obtenir des exemples avancés de l’utilisation d’objets complexes en tant que variables, voir [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="resources"></a>les ressources
Les informations suivantes peuvent être utiles lorsque vous travaillez avec des ressources :

* Spécifiez des **commentaires** pour chaque ressource dans le modèle pour aider les autres contributeurs à comprendre l’objectif de la ressource :
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Vous pouvez utiliser des balises pour ajouter des métadonnées aux ressources. Utiliser des métadonnées pour ajouter des informations concernant vos ressources. Par exemple, vous pouvez ajouter des métadonnées à une ressource à des fins de détail de facturation. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
* Si vous utilisez un *point de terminaison public* dans votre modèle (par exemple, un point de terminaison public Azure Blob Storage), *ne codez pas en dur* l’espace de noms. Utilisez la fonction **référence** pour récupérer l’espace de noms dynamiquement. Cette approche vous permet de déployer le modèle dans différents environnements d’espace de noms publics sans modifier manuellement le point de terminaison dans le modèle. Définissez la version de l’API sur la version que vous utilisez pour le compte de stockage dans votre modèle :
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si le compte de stockage est déployé dans le même modèle que vous créez, il est inutile de spécifier l’espace de noms du fournisseur lors du référencement de la ressource. La syntaxe simplifiée est la suivante :
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si vous avez d’autres valeurs dans votre modèle configuré avec un espace de noms public, modifiez les valeurs de manière à ce qu’elles reflètent la même fonction de **référence**. Par exemple, vous pouvez définir la propriété **storageUri** du profil de diagnostic de la machine virtuelle :
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Vous pouvez également référencer un compte de stockage existant dans un autre groupe de ressources :

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Affectez des adresses IP publiques à une machine virtuelle uniquement si une application le nécessite. Pour vous connecter à une machine virtuelle (VM) pour le débogage, ou à des fins d’administration ou de gestion, utilisez les règles NAT entrantes, une passerelle de réseau virtuel ou une jumpbox.
   
     Pour plus d’informations sur la connexion aux machines virtuelles, consultez :
   
   * [Exécuter des machines virtuelles pour une architecture à plusieurs niveaux dans Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurer l’accès WinRM pour les machines virtuelles dans Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide de PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Autoriser l’accès externe à votre machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* La propriété **domainNameLabel** pour les adresses IP publiques doit être unique. La valeur **domainNameLabel** doit comporter entre 3 et 63 caractères et respecter les règles spécifiées par cette expression régulière : `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Comme la fonction **uniqueString** génère une chaîne de 13 caractères, le paramètre **dnsPrefixString** est limité à 50 caractères :

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Lors de l’ajout d’un mot de passe à une extension de script personnalisé, utilisez la propriété **commandToExecute** dans la propriété **protectedSettings** :
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Pour garantir que les clés secrètes sont chiffrées lorsqu’elles sont transmises comme paramètres à des machines virtuelles et à des extensions, utilisez la propriété **protectedSettings** des extensions appropriées.
   > 
   > 

## <a name="outputs"></a>outputs
Si vous utilisez un modèle pour créer des adresses IP publiques, il doit comporter une section **outputs** qui renvoie les détails de l’adresse IP et le nom de domaine complet. Vous pouvez utiliser des valeurs de sortie pour récupérer facilement plus d’informations sur les adresses IP publiques et sur les noms de domaine complets après le déploiement. Lors du référencement de la ressource, utilisez la version d’API qui a été utilisée pour la créer : 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>Modèle unique ou modèles imbriqués
Pour déployer votre solution, vous pouvez utiliser un modèle unique ou un modèle principal avec plusieurs modèles imbriqués. Les modèles imbriqués sont courants dans des scénarios plus avancés. L’utilisation d’un modèle imbriqué offre les avantages suivants :

* Ils permettent de décomposer une solution en composants ciblés.
* Ils peuvent être réutilisés dans différents modèles principaux.

Si vous choisissez d’utiliser les modèles imbriqués, les instructions suivantes peuvent vous aider à normaliser votre conception de modèle. Ces instructions sont basées sur les [modèles pour la conception de modèles Azure Resource Manager](best-practices-resource-manager-design-templates.md). Nous vous recommandons une conception disposant des modèles suivants :

* **Modèle principal** (azuredeploy.json). Utilisé pour les paramètres d’entrée.
* **Modèle de ressource partagé**. Utilisé pour déployer les ressources partagées que toutes les autres ressources utilisent (par exemple, réseau virtuel et groupes à haute disponibilité). Utilisez l’expression **dependsOn** pour garantir que ce modèle est déployé avant les autres modèles.
* **Modèle de ressource facultatif**. Utilisé pour effectuer un déploiement conditionnel des ressources en fonction d’un paramètre (par exemple, une jumpbox).
* **Modèle de ressource de membre**. Chaque type d’instance dans une couche application a sa propre configuration. Au sein d’un niveau, vous pouvez définir différents types d’instance. (Par exemple, la première instance crée un cluster et les instances supplémentaires sont ajoutées au cluster existant.) Chaque type d’instance a son propre modèle de déploiement.
* **Scripts**. Des scripts largement réutilisables sont applicables pour chaque type d’instance (par exemple, l’initialisation et le formatage de disques supplémentaires). Les scripts personnalisés que vous créez à des fins spécifiques de personnalisation diffèrent selon le type d’instance.

![Modèle imbriqué](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Pour plus d’informations, voir [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Établir un lien conditionnel au modèle imbriqué
Vous pouvez utiliser un paramètre pour lier les modèles imbriqués conditionnellement. Le paramètre devient partie intégrante de l’URI pour le modèle :

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>Format de modèle
Il est conseillé d’exécuter une validation JSON de votre modèle. Un validateur peut vous aider à supprimer les virgules, parenthèses et crochets superflus susceptibles de provoquer une erreur lors du déploiement. Essayez [JSONlint](http://jsonlint.com/) ou un package de linter pour votre environnement d’édition favori (Visual Studio Code, Atom, Sublime Text, Visual Studio).

Il est également judicieux de formater votre JSON pour une meilleure lisibilité. Vous pouvez utiliser un package de formatage JSON de votre éditeur local. Dans Visual Studio, appuyez sur **Ctrl + K, Ctrl + D** pour formater le document. Dans Visual Studio Code, appuyez sur **Alt + Maj + F**. Si votre éditeur local ne formate pas le document, vous pouvez utiliser un [formateur en ligne](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des conseils sur la conception de votre solution pour des machines virtuelles, voir [Exécution d’une machine virtuelle Windows sur Azure](../guidance/guidance-compute-single-vm.md) et [Exécution d’une machine virtuelle Linux sur Azure](../guidance/guidance-compute-single-vm-linux.md).
* Pour des conseils sur la configuration d’un compte de stockage, voir [Liste de contrôle des performances et de l’extensibilité d’Azure Storage](../storage/storage-performance-checklist.md).
* Pour recevoir de l’aide sur les réseaux virtuels, voir [Instructions pour les infrastructures réseau](../virtual-machines/windows/infrastructure-networking-guidelines.md).
* Pour en savoir plus sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


