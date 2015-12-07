<properties
   pageTitle="Extension de script personnalisé pour machine virtuelle Windows | Microsoft Azure"
   description="Automatisation des tâches de configuration de machine virtuelle Azure à l’aide de l’extension de script personnalisé pour exécuter des scripts PowerShell sur une machine virtuelle Windows distante"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Extension de script personnalisé pour machines virtuelles Windows

Cet article donne une vue d’ensemble de l’utilisation de l’extension de script personnalisé sur des machines virtuelles Windows à l’aide d’applets de commande Azure PowerShell.

Les extensions de machine virtuelle créées par Microsoft et les éditeurs tiers de confiance étendent les fonctionnalités de la machine virtuelle. Pour consulter une vue d’ensemble des extensions de machine virtuelles, voir [Fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-extensions-features.md).

Lien :[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-extensions-customscript%20-with%20template.md).


## Vue d’ensemble de l’extension de script personnalisé

L’extension de script personnalisé pour Windows vous permet d’exécuter des scripts PowerShell sur une machine virtuelle distante, sans vous connecter à celle-ci. Les scripts peuvent être exécutés après l’approvisionnement de la machine virtuelle ou à tout moment pendant le cycle de vie de la machine virtuelle sans avoir à ouvrir de ports supplémentaires sur celle-ci. Le cas d’utilisation le plus courant de l’extension du script personnalisé correspond à l’exécution, l’installation et la configuration de logiciels supplémentaires sur la machine virtuelle après son approvisionnement.

### Conditions préalables pour exécuter l’extension de script personnalisé

1. Installez les applets de commande Microsoft Azure PowerShell version 0.8.0 ou ultérieure à partir d’<a href="http://azure.microsoft.com/downloads" target="_blank">ici</a>.
2. Si les scripts sont exécutés sur une machine virtuelle existante, assurez-vous que l’agent de machine virtuelle est activé sur la machine virtuelle, sinon suivez les instructions de cet <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">article</a> pour en installer un.
3. Téléchargez les scripts que vous souhaitez exécuter sur la machine virtuelle vers Azure Storage. Les scripts peuvent provenir d’un seul ou de plusieurs conteneurs de stockage.
4. Le script doit être conçu de manière à ce que le script d’entrée lancé par l’extension lance à son tour les autres scripts.

## Scénarios d’utilisation de l’extension de script personnalisé

### Charger des fichiers vers le conteneur par défaut

Si vos scripts se trouvent dans le conteneur de stockage du compte par défaut de votre abonnement, l’exemple ci-dessous montre comment vous pouvez les exécuter sur la machine virtuelle. Le paramètre ContainerName représente l’emplacement vers lequel vous chargez les scripts. Le compte de stockage par défaut peut être vérifié à l’aide de la commande **Get-AzureSubscription –Default**.

Dans l’exemple suivant, une nouvelle machine virtuelle est créée, mais le même scénario peut être également exécuté sur une machine virtuelle existante.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Charger des fichiers vers un conteneur de stockage autre que le conteneur par défaut

Ce cas d’utilisation montre comment charger des scripts ou des fichiers en utilisant un stockage non défini en tant que stockage par défaut dans le même abonnement ou dans un autre abonnement. Dans notre exemple, nous allons utiliser une machine virtuelle existante, mais les mêmes opérations peuvent être effectuées pendant la création d'une machine virtuelle.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Charger des scripts vers plusieurs conteneurs sur différents comptes de stockage

  Si les fichiers de script sont stockés sur plusieurs conteneurs, pour les exécuter, vous devez fournir leur URL SAS complète.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Ajout de l'extension de script personnalisé à partir du portail Azure en version préliminaire

Accédez à la machine virtuelle dans le <a href="https://portal.azure.com/ " target="_blank">portail Azure en version préliminaire</a> et ajoutez l’extension en spécifiant le fichier de script à exécuter.

  ![][5]


### Désinstallation de l’extension de script personnalisé

L’extension de script personnalisé peut être désinstallée de la machine virtuelle à l’aide de la commande ci-dessous.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Utilisation de l’extension de script personnalisé avec des modèles

Pour en savoir plus sur l’utilisation de l’extension de script personnalisé avec les modèles, voir la documentation [ici](virtual-machines-extensions-customscript -with template.md).

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=AcomDC_1125_2015-->