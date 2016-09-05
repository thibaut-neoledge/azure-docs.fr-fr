<properties
   pageTitle="Extension de script personnalisé pour machine virtuelle Windows | Microsoft Azure"
   description="Automatisation des tâches de configuration de machine virtuelle Azure à l’aide de l’extension de script personnalisé pour exécuter des scripts PowerShell sur une machine virtuelle Windows distante"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Extension de script personnalisé pour machines virtuelles Windows

Cet article donne une vue d’ensemble de l’utilisation de l’extension de script personnalisé sur des machines virtuelles Windows en utilisant des applets de commande Azure PowerShell avec des API de gestion des services Azure.

Les extensions de machine virtuelle créées par Microsoft et les éditeurs tiers de confiance étendent les fonctionnalités de la machine virtuelle. Pour consulter une vue d’ensemble des extensions de machine virtuelles, voir [Fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](virtual-machines-windows-extensions-customscript.md).

## Vue d’ensemble de l’extension de script personnalisé

L’extension de script personnalisé pour Windows vous permet d’exécuter des scripts PowerShell sur une machine virtuelle distante, sans vous connecter à celle-ci. Vous pouvez exécuter les scripts après la configuration de la machine virtuelle ou à tout moment pendant le cycle de vie de celle-ci sans ouvrir de ports supplémentaires. Les cas d’utilisation les plus courants d’exécution de l’extension de script personnalisé sont l’exécution, l’installation et la configuration de logiciels supplémentaires sur la machine virtuelle après configuration de celle-ci.

### Conditions préalables pour exécuter l’extension de script personnalisé

1. Installez les <a href="http://azure.microsoft.com/downloads" target="_blank">applets de commande Azure PowerShell</a> version 0.8.0 ou ultérieure.
2. Si vous voulez que les scripts s’exécutent sur une machine virtuelle existante, assurez-vous que l’agent de machine virtuelle est activé sur celle-ci. S’il n’est pas installé, suivez ces [étapes](virtual-machines-windows-classic-agents-and-extensions.md). Si la machine virtuelle est créée à partir du portail Azure, l’agent de machine virtuelle est installé par défaut.
3. Téléchargez les scripts que vous souhaitez exécuter sur la machine virtuelle vers Azure Storage. Les scripts peuvent provenir d’un seul ou de plusieurs conteneurs de stockage.
4. Le script doit être conçu de manière à ce que le script d’entrée démarré par l’extension lance d’autres scripts.

## Scénarios d’utilisation de l’extension de script personnalisé

### Charger des fichiers vers le conteneur par défaut

L’exemple suivant montre comment exécuter vos scripts sur la machine virtuelle s’ils se trouvent dans le conteneur de stockage du compte par défaut de votre abonnement. Vous téléchargez vos scripts dans ContainerName. Vous pouvez vérifier le compte de stockage par défaut à l’aide de la commande **Get-AzureSubscription –Default**.

L’exemple suivant crée une machine virtuelle, mais vous pouvez exécuter le même scénario sur une machine virtuelle existante.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Charger des fichiers vers un conteneur de stockage autre que le conteneur par défaut

Ce cas d’utilisation montre comment charger des scripts ou des fichiers en utilisant un conteneur de stockage non défini par défaut dans le même abonnement ou dans un autre abonnement. Cet exemple montre une machine virtuelle existante, mais vous pouvez effectuer les mêmes opérations lors de la création d’une machine virtuelle.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Charger des scripts vers plusieurs conteneurs sur différents comptes de stockage

  Si les fichiers de script sont stockés sur plusieurs conteneurs, pour exécuter les scripts, vous devez fournir leurs URL de signature d’accès partagé (SAP) complètes.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Ajouter l’extension de script personnalisé à partir du portail Azure

Accédez à la machine virtuelle dans le <a href="https://portal.azure.com/ " target="_blank">portail Azure</a>, puis ajoutez l’extension en spécifiant le fichier de script à exécuter.

  ![Spécifier le fichier de script][5]


### Désinstaller l’extension de script personnalisé

Vous pouvez désinstaller l’extension de script personnalisé de la machine virtuelle à l’aide de la commande suivante.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Utiliser une extension de script personnalisé avec des modèles

Pour savoir comment utiliser l’extension de script personnalisé avec des modèles Azure Resource Manager, voir [Utilisation de l’extension de script personnalisé pour les machines virtuelles Windows avec les modèles Azure Resource Manager](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0824_2016-->