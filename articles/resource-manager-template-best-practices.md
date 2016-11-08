---
title: Bonnes pratiques pour les modèles Azure Resource Manager | Microsoft Docs
description: Instructions pour simplifier vos modèles Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2016
ms.author: tomfitz

---
# Bonnes pratiques relatives à la création de modèles Azure Resource Manager
Les instructions suivantes vous aideront à créer des modèles Resource Manager fiables et faciles à utiliser. Ces instructions sont uniquement des suggestions et ne sont pas impératives. Votre scénario peut nécessiter des variations de ces instructions.

## Noms de ressource
Il existe généralement trois types de noms de ressource avec lesquels vous travaillez :

1. des noms de ressource qui doivent être uniques ;
2. des noms de ressources qui ne doivent pas être uniques, mais qui doivent permettre d’identifier le contexte ;
3. des noms de ressources qui peuvent être génériques.

Pour établit une convention de nommage, consultez [Instructions de dénomination d’infrastructure](virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md). Pour plus d’informations sur les restrictions de noms de ressource, consultez [Conventions d’affectation de noms recommandées pour les ressources Azure](./guidance/guidance-naming-conventions.md).

### Noms de ressource uniques
Vous devez fournir un nom de ressource unique pour tout type de ressource disposant d’un point de terminaison d’accès aux données. Certains types courants nécessitent un nom unique, notamment :

* Compte de stockage
* Site web
* Serveur SQL
* Coffre de clés
* Le cache Redis
* Compte Batch
* Traffic Manager
* Service de recherche
* Cluster HDInsight

En outre, les noms de compte de stockage doivent être en minuscules, comporter 24 caractères au maximum et ne pas comprendre de traits d’union.

Plutôt que de fournir un paramètre pour ces noms de ressource et essayer de deviner un nom unique au cours du déploiement, vous pouvez créer une variable qui utilise la fonction [uniqueString()](resource-group-template-functions.md#uniquestring) pour générer un nom. Souvent, vous souhaitez également ajouter un préfixe ou un suffixe au résultat de **uniqueString** pour vous permettre de déterminer plus facilement le type de ressource en examinant le nom. Par exemple, vous pouvez générer un nom unique pour un compte de stockage avec la variable suivante.

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

Les comptes de stockage avec un préfixe uniqueString ne seront pas mis en cluster dans les mêmes racks.

### Noms de ressource pour l’identification
Pour les types de ressources que vous souhaitez nommer mais dont vous n’avez pas à garantir l’unicité, entrez simplement un nom qui identifie le contexte et le type de la ressource. Vous pouvez éventuellement fournir un nom descriptif qui vous aide à l’identifier dans une liste de noms de ressource. Si vous avez besoin de modifier le nom de ressource au cours des déploiements, utilisez un paramètre pour le nom :

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

S’il est inutile de transmettre un nom au cours du déploiement, utilisez une variable :

    "variables": {
        "vmName": "demoLinuxVM"
    }

Sinon, une valeur codée en dur :

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### Noms de ressource génériques
Pour les types de ressources qui sont accessibles en grande partie par le biais d’une autre ressource, vous pouvez utiliser un nom générique codé en dur dans le modèle. Par exemple, vous ne souhaitez probablement pas fournir un nom personnalisable pour les règles de pare-feu dans un serveur SQL Server.

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## Paramètres
1. Minimisez les paramètres autant que possible. Si vous pouvez utiliser une variable ou une expression littérale, faites-le. Fournissez uniquement des paramètres pour :
   
   * les réglages que vous souhaitez faire évoluer en fonction de l’environnement (par exemple, la référence, la taille ou la capacité) ;
   * les noms de ressources que vous souhaitez spécifier pour faciliter l’identification ;
   * les valeurs que vous utilisez souvent pour effectuer d’autres tâches (par exemple, le nom d’utilisateur administrateur) ;
   * les clés secrètes (notamment les mots de passe) ;
   * le nombre ou le tableau de valeurs à utiliser lors de la création de plusieurs instances d’un type de ressource.
2. Les noms de paramètre doivent suivre **camelCasing**.
3. Fournissez une description dans les métadonnées pour chaque paramètre.
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
4. Définissez des valeurs par défaut pour les paramètres (à l’exception des mots de passe et des clés SSH).
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
5. Utilisez **securestring** pour tous les mots de passe et les clés secrètes.
   
        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
6. Lorsque cela est possible, évitez d’utiliser un paramètre pour spécifier **l’emplacement**. Au lieu de cela, utilisez la propriété d’emplacement du groupe de ressources. Avec l’expression **resourceGroup ().location** pour toutes vos ressources, les ressources dans le modèle sont déployées dans le même emplacement que le groupe de ressources.
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
   
     Si un type de ressource est uniquement pris en charge dans un nombre limité d’emplacements, envisagez de spécifier un emplacement valide directement dans le modèle. Si vous devez utiliser un paramètre d’emplacement, partagez cette valeur de paramètre autant que possible avec les ressources qui sont susceptibles de se trouver dans le même emplacement. Cette approche réduit le nombre d’utilisateurs devant fournir des emplacements pour chaque type de ressource.
7. Évitez d’utiliser un paramètre ou une variable pour la version de l’API pour un type de ressource. Les propriétés de ressource et les valeurs peuvent varier selon le numéro de version. IntelliSense dans des éditeurs de code n’est pas en mesure de déterminer le schéma correct lorsque la version de l’API est définie sur un paramètre ou une variable. Au lieu de cela, codez en dur la version de l’API dans le modèle.

## Variables
1. Utilisez des variables pour les valeurs que vous devez utiliser plusieurs fois dans un modèle. Si une valeur est utilisée une seule fois, une valeur codée en dur rend votre modèle plus facile à lire.
2. Vous ne pouvez pas utiliser la fonction [reference](resource-group-template-functions.md#reference) dans la section Variables. La fonction reference dérive sa valeur de l’état d’exécution de la ressource, mais les variables sont résolues lors de l’analyse initiale du modèle. Au lieu de cela, construisez des valeurs qui ont besoin de la fonction **reference** directement dans la section **resources** ou **outputs** du modèle.
3. Incluez des variables pour les noms de ressource qui doivent être uniques, comme indiqué dans [Noms de ressources](#resource-names).
4. Vous pouvez regrouper des variables dans des objets complexes. Vous pouvez référencer une valeur à partir d’un objet complexe au format **variable.subentry**. Le regroupement de variables vous permet d’effectuer le suivi de variables liées et améliore la lisibilité du modèle.
   
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
   
   > [!NOTE]
   > Un objet complexe ne peut pas contenir une expression qui fait référence à la valeur d’un objet complexe. Définissez une variable distincte à cette fin.
   > 
   > 
   
     Pour obtenir des exemples plus avancés de l’utilisation d’objets complexes en tant que variables, consultez la rubrique [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

## Ressources
1. Spécifiez des **commentaires** pour chaque ressource dans le modèle pour aider les autres contributeurs à comprendre l’objectif de la ressource.
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]
2. Utilisez des balises pour ajouter des métadonnées aux ressources qui vous permettent d’ajouter des informations supplémentaires concernant vos ressources. Par exemple, vous pouvez ajouter des métadonnées à une ressource à des fins de détail de facturation. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
3. Si vous utilisez un **point de terminaison public** dans votre modèle (par exemple, un point de terminaison public de stockage d’objets blob), **ne codez pas en dur** l’espace de noms. Utilisez la fonction **référence** pour récupérer l’espace de noms dynamiquement. Cela vous permet de déployer le modèle dans différents environnements d’espace de noms publics sans modifier manuellement le point de terminaison dans le modèle. Définissez l’apiVersion sur la version que vous utilisez pour le compte de stockage (storageAccount) dans votre modèle.
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     Si le compte de stockage est déployé dans le même modèle, il est inutile de spécifier l’espace de noms du fournisseur lors du référencement de la ressource. La syntaxe simplifiée est la suivante :
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     Si vous avez d’autres valeurs dans votre modèle configuré avec un espace de noms public, modifiez-les de manière à ce qu’elles reflètent la même fonction de référence. Par exemple, la propriété storageUri de la machine virtuelle diagnosticsProfile.
   
        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
   
     Vous pouvez également **référencer** un compte de stockage existant dans un autre groupe de ressources.

        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. Affectez publicIPAddresses à une machine virtuelle uniquement si une application le nécessite. Pour vous connecter à des fins de débogage, de gestion ou d’administration, utilisez inboundNatRules, virtualNetworkGateways ou une jumpbox.
   
     Pour plus d’informations sur la connexion aux machines virtuelles, consultez :
   
   * [Running VMs for an N-tier architecture on Azure](./guidance/guidance-compute-3-tier-vm.md) (Exécution de machines virtuelles pour une architecture à plusieurs niveaux dans Azure)
   * [Configuration de l’accès WinRM pour les machines virtuelles dans Azure Resource Manager](virtual-machines/virtual-machines-windows-winrm.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide de PowerShell](virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
   * [Ouverture des ports et des points de terminaison](virtual-machines/virtual-machines-linux-nsg-quickstart.md)
2. La propriété **domainNameLabel** pour publicIPAddresses doit être unique. domainNameLabel doit être composé de 3 et 63 caractères et respecter les règles spécifiées par cette expression régulière `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Comme la fonction uniqueString génère une chaîne de 13 caractères dans l’exemple ci-dessous, il est supposé qu’il a été vérifié que la chaîne de préfixe dnsPrefixString ne dépasse pas 50 caractères et se conforme à ces règles.
   
        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }
3. Lors de l’ajout d’un mot de passe pour une **customScriptExtension**, utilisez la propriété **commandToExecute** dans protectedSettings.
   
        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }
   
   > [!NOTE]
   > Pour garantir que les clés secrètes transmises comme paramètres à des machines virtuelles/extensions sont chiffrées, la propriété protectedSettings des extensions appropriées doit être utilisée.
   > 
   > 

## Sorties
Si un modèle crée de nouvelles **publicIPAddresses**, il doit comporter une section **output** qui fournit des détails de l’adresse IP et du domaine entièrement qualifié créé pour récupérer facilement ces détails après le déploiement. Lors du référencement de la ressource, utilisez la version d’API qui a été utilisée pour la créer.

```
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

## Modèle unique ou modèles imbriqués
Pour déployer votre solution, vous pouvez utiliser un modèle unique ou un modèle principal avec plusieurs modèles imbriqués. Les modèles imbriqués sont courants dans des scénarios plus avancés. Les modèles imbriqués offrent les avantages suivants :

1. Ils permettent de décomposer une solution en composants ciblés.
2. Ils peuvent être réutilisés dans différents modèles principaux.

Lorsque vous décidez de décomposer votre conception de modèle en plusieurs modèles imbriqués, les instructions suivantes vous aident à normaliser la conception. Ces instructions sont basées sur la documentation des [modèles pour la conception de modèles Azure Resource Manager](best-practices-resource-manager-design-templates.md). La conception recommandée se compose des modèles suivants.

* **Modèle principal** (azuredeploy.json). Utilisé pour les paramètres d’entrée.
* **Modèle de ressource partagé**. Déploie les ressources partagées que toutes les autres ressources utilisent (par exemple, réseau virtuel et groupes à haute disponibilité). L’expression dependsOn impose que ce modèle soit déployé avant les autres modèles.
* **Modèle de ressource facultatif**. Effectue un déploiement conditionnel des ressources en fonction d’un paramètre (par exemple, une jumpbox)
* **Modèles de ressource de membre**. Chaque type d’instance dans une couche application a sa propre configuration. Différents types d’instance peuvent être définis au sein d’un niveau (par exemple, si la première instance crée un cluster, des instances supplémentaires sont ajoutées au cluster existant). Chaque type d’instance a son propre modèle de déploiement.
* **Scripts**. Des scripts largement réutilisables sont applicables pour chaque type d’instance (par exemple, l’initialisation et le formatage de disques supplémentaires). Les scripts personnalisés sont créés à des fins spécifiques de personnalisation diffèrent selon le type d’instance.

![modèle imbriqué](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Pour plus d’informations, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## Établissez un lien conditionnel au modèle imbriqué
Vous pouvez établir un lien conditionnel aux modèles imbriqués en utilisant un paramètre qui devient partie intégrante de l’URI du modèle.

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

## Format de modèle
1. Il est conseillé de d’exécuter une validation JSON de votre modèle pour supprimer les virgules, les parenthèses et les crochets superflus qui peuvent provoquer une erreur lors du déploiement. Essayez [JSONlint](http://jsonlint.com/) ou un package de linter pour votre environnement d’édition favori (Visual Studio Code, Atom, Sublime Text, Visual Studio, etc.).
2. Il est également judicieux de formater votre JSON pour une meilleure lisibilité. Vous pouvez utiliser un package de formatage JSON de votre éditeur local. Dans Visual Studio, formatez le document avec **Ctrl + K, Ctrl + D**. Dans VS Code, utilisez **Alt + Maj + F**. Si votre éditeur local ne formate pas le document, vous pouvez utiliser un [formateur en ligne](https://www.bing.com/search?q=json+formatter).

## Étapes suivantes
1. Pour obtenir des conseils sur la conception de votre solution pour des machines virtuelles, consultez [Exécution d’une machine virtuelle Windows sur Azure](./guidance/guidance-compute-single-vm.md) et [Exécution d’une machine virtuelle Linux sur Azure](./guidance/guidance-compute-single-vm-linux.md).
2. Pour des conseils sur la configuration d’un compte de stockage, consultez [Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage](storage/storage-performance-checklist.md).
3. Pour recevoir de l’aide sur les réseaux virtuels, consultez [Instructions pour les infrastructures réseau](virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md).

<!---HONumber=AcomDC_0720_2016-->