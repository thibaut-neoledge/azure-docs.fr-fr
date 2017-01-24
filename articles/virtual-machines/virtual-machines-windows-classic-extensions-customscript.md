---
title: "Extension de script personnalisé sur une machine virtuelle Windows | Microsoft Docs"
description: "Automatisation des tâches de configuration de machine virtuelle Azure à l’aide de l’extension de script personnalisé pour exécuter des scripts PowerShell sur une machine virtuelle Windows distante"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 862b19b334b0a4da6e61983f428e3e6732189870
ms.openlocfilehash: a5e3fc8a1160fc3eef3c98320840992f86b67d14


---
# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extension de script personnalisé pour machines virtuelles Windows

Cet article donne une vue d’ensemble de l’utilisation de l’extension de script personnalisé sur des machines virtuelles Windows en utilisant des applets de commande Azure PowerShell avec des API de gestion des services Azure.

Les extensions de machine virtuelle créées par Microsoft et les éditeurs tiers de confiance étendent les fonctionnalités de la machine virtuelle. Pour consulter une vue d’ensemble des extensions de machine virtuelles, consultez la page [Fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="custom-script-extension-overview"></a>Vue d’ensemble de l’extension de script personnalisé

L’extension de script personnalisé pour Windows vous permet d’exécuter des scripts PowerShell sur une machine virtuelle distante, sans vous connecter à celle-ci. Vous pouvez exécuter les scripts après la configuration de la machine virtuelle ou à tout moment pendant le cycle de vie de celle-ci sans ouvrir de ports supplémentaires. Les cas d’utilisation les plus courants d’exécution de l’extension de script personnalisé sont l’exécution, l’installation et la configuration de logiciels supplémentaires sur la machine virtuelle après configuration de celle-ci.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Conditions préalables pour exécuter l’extension de script personnalisé

1. Installez les <a href="http://azure.microsoft.com/downloads" target="_blank">applets de commande Azure PowerShell</a> version 0.8.0 ou ultérieure.
2. Si vous voulez que les scripts s’exécutent sur une machine virtuelle existante, assurez-vous que l’agent de machine virtuelle est activé sur celle-ci. S’il n’est pas installé, suivez ces [étapes](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Si la machine virtuelle est créée à partir du portail Azure, l’agent de machine virtuelle est installé par défaut.
3. Téléchargez les scripts que vous souhaitez exécuter sur la machine virtuelle vers Azure Storage. Les scripts peuvent provenir d’un seul ou de plusieurs conteneurs de stockage.
4. Le script doit être conçu de manière à ce que le script d’entrée démarré par l’extension lance d’autres scripts.

## <a name="custom-script-extension-scenarios"></a>Scénarios d’utilisation de l’extension de script personnalisé

### <a name="upload-files-to-the-default-container"></a>Charger des fichiers vers le conteneur par défaut

L’exemple suivant montre comment exécuter vos scripts sur la machine virtuelle s’ils se trouvent dans le conteneur de stockage du compte par défaut de votre abonnement. Vous téléchargez vos scripts dans ContainerName. Vous pouvez vérifier le compte de stockage par défaut à l’aide de la commande **Get-AzureSubscription –Default** .

L’exemple suivant crée une machine virtuelle, mais vous pouvez exécuter le même scénario sur une machine virtuelle existante.

```powershell
# Create a new VM in Azure.
$vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
$vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password

# Add Custom Script extension to the VM. 
# The container name refers to the storage container that contains the file.
$vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
New-AzureVM -ServiceName $servicename -Location $location -VMs $vm

# Viewing the  script execution output.
$vm = Get-AzureVM -ServiceName $servicename -Name $name
# Use the position of the extension in the output as index.
$vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList
```

### <a name="upload-files-to-a-non-default-storage-container"></a>Charger des fichiers vers un conteneur de stockage autre que le conteneur par défaut

Ce cas d’utilisation montre comment charger des scripts ou des fichiers en utilisant un conteneur de stockage non défini par défaut dans le même abonnement ou dans un autre abonnement. Cet exemple montre une machine virtuelle existante, mais vous pouvez effectuer les mêmes opérations lors de la création d’une machine virtuelle.

```powershell
Get-AzureVM -Name $name -ServiceName $servicename | ` 
Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey ` 
-ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | ` 
Update-AzureVM
```

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Charger des scripts vers plusieurs conteneurs sur différents comptes de stockage

Si les fichiers de script sont stockés sur plusieurs conteneurs, pour exécuter les scripts, vous devez fournir leurs URL de signature d’accès partagé (SAP) complètes.

```powershell
Get-AzureVM -Name $name -ServiceName $servicename | ` 
Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey ` 
-ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | ` 
Update-AzureVM
```

### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Ajouter l’extension de script personnalisé à partir du portail Azure

Accédez à la machine virtuelle dans le <a href="https://portal.azure.com/ " target="_blank">portail Azure</a>, puis ajoutez l’extension en spécifiant le fichier de script à exécuter.

![Spécifier le fichier de script][5]

### <a name="uninstall-the-custom-script-extension"></a>Désinstaller l’extension de script personnalisé

Vous pouvez désinstaller l’extension de script personnalisé de la machine virtuelle à l’aide de la commande suivante.

```powershell
get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | ` 
Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM
```

### <a name="use-the-custom-script-extension-with-templates"></a>Utiliser une extension de script personnalisé avec des modèles

Pour savoir comment utiliser l’extension de script personnalisé avec des modèles Azure Resource Manager, consultez [Utilisation de l’extension de script personnalisé pour les machines virtuelles Windows avec les modèles Azure Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Dec16_HO3-->


