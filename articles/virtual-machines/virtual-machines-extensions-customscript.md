<properties
   pageTitle="Extension de script personnalisé pour Windows"
   description="Automatisation des tâches de configuration de machine virtuelle Azure à l&#39;aide de l&#39;extension de script personnalisé pour Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Extension de script personnalisé pour Windows

Cet article donne une vue d'ensemble de l'utilisation de l'extension de script personnalisé pour Windows à l'aide d'applets de commande Azure Powershell.


Les extensions de machine virtuelle créées par Microsoft et les éditeurs tiers de confiance étendent les fonctionnalités de la machine virtuelle. Pour une vue d’ensemble détaillée des extensions de machine virtuelle, reportez-vous à la <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">documentation MSDN</a>.

## Vue d'ensemble de l'extension de script personnalisé

L'extension de script personnalisé pour Windows vous permet d'exécuter des scripts Powershell sur une machine virtuelle distante, sans vous connecter à celle-ci. Les scripts peuvent être exécutés après l'approvisionnement de la machine virtuelle ou à tout moment pendant le cycle de vie de la machine virtuelle sans avoir à ouvrir de ports supplémentaires sur celle-ci. Le cas d'utilisation le plus courant du script personnalisé est l'exécution de l'installation et de la configuration de logiciels supplémentaires sur la machine virtuelle après son approvisionnement.

### Conditions préalables pour exécuter l'extension de script personnalisé

1. Installez les applets de commande Microsoft Azure PowerShell version 8.0 ou ultérieure à partir d’<a href="http://azure.microsoft.com/downloads" target="_blank">ici</a>.
2. Si les scripts sont exécutés sur une machine virtuelle existante, assurez-vous que l’agent de machine virtuelle est activé sur la machine virtuelle, sinon suivez les instructions de cet <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">article</a> pour en installer un.
3. Téléchargez les scripts que vous souhaitez exécuter sur la machine virtuelle vers Azure Storage. Les scripts peuvent provenir d'un seul ou de plusieurs conteneurs de stockage.
4. Le script doit être conçu de manière à ce que le script d'entrée lancé par l'extension lance à son tour les autres scripts.

## Scénarios d'utilisation de l'extension de script personnalisé :

 ### Télécharger des fichiers vers le conteneur par défaut : si vos scripts se trouvent dans le conteneur de stockage du compte par défaut de votre abonnement, l’extrait d’applet de commande ci-dessous montre comment vous pouvez les exécuter sur la machine virtuelle. Le paramètre ContainerName dans l'exemple ci-dessous représente l'emplacement vers lequel vous téléchargez les scripts. Le compte de stockage par défaut peut être vérifié à l’aide de l’applet de commande Get-AzureSubscription –Default.

Remarque : ce cas d’usage entraîne la création d’une machine virtuelle, mais les mêmes opérations peuvent être effectuées sur une machine virtuelle existante.

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

### Télécharger des fichiers vers des conteneurs de stockage autres que le conteneur par défaut.

Ce cas d'utilisation montre comment télécharger des scripts ou des fichiers en utilisant un stockage non défini en tant que stockage par défaut dans le même abonnement ou dans un autre abonnement. Dans notre exemple, nous allons utiliser une machine virtuelle existante, mais les mêmes opérations peuvent être effectuées pendant la création d'une machine virtuelle.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### Téléchargez des scripts vers plusieurs conteneurs sur différents comptes de stockage. Si les fichiers de script sont stockés sur plusieurs conteneurs, pour les exécuter actuellement, vous devez fournir leur URL SAP complète.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Ajouter l'extension de script personnalisé à partir du portail.
Accédez à la machine virtuelle dans le <a href="https://portal.azure.com/ " target="_blank">portail Microsoft Azure en version préliminaire</a> et ajoutez l’extension en spécifiant le fichier de script à exécuter. ![][5]

  ### Désinstallation de l'extension de script personnalisé.

L'extension de script personnalisé peut être désinstallée de la machine virtuelle à l'aide de l'applet de commande ci-dessous :

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Utilisation de l'extension de script personnalisé avec les modèles

Pour en savoir plus sur l'utilisation de l'extension de script personnalisé avec les modèles, cliquez dans la documentation [ici](virtual-machines-extensions-customscript -with template.md).

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=August15_HO8-->