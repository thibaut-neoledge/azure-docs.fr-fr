---
title: "Scripts personnalisés sur des machines virtuelles Windows utilisant des modèles | Microsoft Docs"
description: "Automatisation des tâches de configuration de machine virtuelle Windows à l’aide de l’extension de script personnalisé avec des modèles Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ac63fd346f158d52b359f2d6b398d0d00ea0c67e


---
# <a name="windows-vm-custom-script-extensions-with-azure-resource-manager-templates"></a>Extension de script personnalisé de machine virtuelle Windows avec les modèles Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## <a name="template-example-for-a-windows-vm"></a>Exemple de modèle pour une machine virtuelle sous Windows
Définissez la ressource suivante dans la section Ressource du modèle.

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

Dans l’exemple précédent, remplacez l’URL et le nom du fichier par vos propres paramètres.
Une fois le modèle créé, vous pouvez le déployer en utilisant Azure PowerShell.

Si vous souhaitez préserver la confidentialité des URL et paramètres de scripts, vous pouvez définir l’URL du script en tant que **privée**. Si l’URL du script est définie en tant que **privée**, elle est accessible uniquement avec un nom et une clé de compte de stockage envoyés en tant que paramètres protégés. Les paramètres du script peuvent également être fournis en tant que paramètres protégés avec la version 1.7 ou ultérieure de l’extension de script personnalisé.

## <a name="template-example-for-a-windows-vm-with-protected-settings"></a>Exemple de modèle pour une machine virtuelle Windows avec des paramètres protégés
        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
Pour plus d’informations sur le schéma des versions plus récentes de l’extension de script personnalisé, voir [Exemples de configuration d’extension de machine virtuelle Microsoft Azure](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour des exemples de configuration d’application sur une machine virtuelle à l’aide de l’extension de script personnalisé, voir [Extension de script personnalisé sur une machine virtuelle Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).




<!--HONumber=Nov16_HO3-->


