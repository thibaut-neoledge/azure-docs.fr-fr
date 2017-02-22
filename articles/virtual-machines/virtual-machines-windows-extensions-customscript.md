---
title: "Extension de script personnalisé Azure pour Windows | Microsoft Docs"
description: "Automatiser les tâches de configuration de machine virtuelle Windows à l’aide de l’extension de script personnalisé"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: f9ea0d14a99a7881b816606058e5ad72a0fef499
ms.openlocfilehash: 01adbd43c5c77c3a80c5141e06a2ab14a49b8454


---
# <a name="custom-script-extension-for-windows"></a>Extension de script personnalisé pour Windows

L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du portail Azure ou de l’API REST de machine virtuelle Azure.

Ce document explique en détail l’utilisation de l’extension de script personnalisé à l’aide du module Azure PowerShell, des modèles Azure Resource Manager, et détaille également les étapes de résolution de problèmes sur les systèmes Windows.

## <a name="prerequisites"></a>Composants requis

### <a name="operating-system"></a>Système d’exploitation

L’extension de script personnalisé pour Windows peut être exécutée sur Windows Server 2008 R2, 2012, 2012 R2 et 2016.

### <a name="script-location"></a>Emplacement du script

Le script doit être stocké dans le stockage Azure ou tout autre emplacement accessible via une URL valide.

### <a name="internet-connectivity"></a>Connectivité Internet

L’extension de script personnalisé pour Windows nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma pour l’extension de script personnalisé. L’extension nécessite un emplacement de script (stockage Azure ou autre emplacement disposant d’une URL valide) et une commande à exécuter. Si vous utilisez le stockage Azure comme source de script, une clé de compte et un nom de compte de stockage Azure sont requis. Ces éléments doivent être traités comme des données sensibles et spécifiés dans la configuration du paramètre de protection des extensions. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| type | extensions |
| typeHandlerVersion | 1.8 |
| fileUris (p. ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (p. ex.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (p. ex.) | examplestorageacct |
| storageAccountKey (p. ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension de script personnalisé pendant un déploiement de modèle Azure Resource Manager. Un exemple de modèle qui inclut l’extension de script personnalisé est disponible ici, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Déploiement PowerShell

Vous pouvez utiliser la commande `Set-AzureRmVMCustomScriptExtension` pour ajouter l’extension de script personnalisé sur une machine virtuelle existante. Pour plus d’informations, consultez [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
-VMName myVM `
-Location myLocation `
-FileUri myURL `
-Run 'myScript.ps1' `
-Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide du module Azure PowerShell. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui se trouvent dans le répertoire suivant sur la machine virtuelle.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Le script proprement dit est téléchargé dans le répertoire suivant sur la machine virtuelle cible.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).





<!--HONumber=Jan17_HO3-->


