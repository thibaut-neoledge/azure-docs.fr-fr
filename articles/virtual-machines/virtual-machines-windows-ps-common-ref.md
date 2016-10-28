<properties 
   pageTitle="Commandes PowerShell courantes pour les machines virtuelles | Microsoft Azure"
   description="Commandes PowerShell courantes pour vous aider à démarrer la création et la gestion de vos machines virtuelles dans Azure sur Windows."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# Commandes PowerShell courantes permettant de créer et de gérer des machines virtuelles

Cet article présente certaines des commandes Azure PowerShell que vous pouvez utiliser pour créer et gérer des machines virtuelles dans votre abonnement Azure. Pour en savoir plus sur les commutateurs et options de ligne de commande spécifiques, vous pouvez utiliser la *commande* **Get-Help**.

## Création de ressources via Azure PowerShell

Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour savoir comment installer la dernière version d’Azure PowerShell, sélectionner l’abonnement à utiliser et vous connecter à votre compte Azure.

Task | Commande
-------------- | -------------------------
Créer une configuration de machine virtuelle | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm\_name" -VMSize "vm\_size"<BR></BR><BR></BR>La configuration de machine virtuelle est utilisée pour définir ou mettre à jour les paramètres d’une machine virtuelle. La configuration est initialisée avec le nom de la machine virtuelle et sa [taille](virtual-machines-windows-sizes.md).
Ajouter des paramètres de configuration | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer\_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Des paramètres du système d’exploitation, y compris les [informations d’identification](https://technet.microsoft.com/library/hh849815.aspx), sont ajoutés à l’objet de configuration que vous avez créé, via New-AzureRmVMConfig.
Ajouter une interface réseau | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Une machine virtuelle doit être associée à une [interface réseau](virtual-machines-windows-ps-create.md) pour pouvoir communiquer au sein d’un réseau virtuel. Vous pouvez également utiliser la commande [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) pour récupérer un objet d’interface réseau existant.
Spécifier une image de plateforme | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher\_name" -Offer "publisher\_offer" -Skus "product\_sku" -Version "latest"<BR></BR><BR></BR>[Des informations sur l’image](virtual-machines-windows-cli-ps-findimage.md) sont ajoutées à l’objet de configuration que vous avez créé, via New-AzureRmVMConfig. L’objet renvoyé par cette commande est utilisé uniquement lorsque vous définissez le disque du système d’exploitation pour l’utilisation d’une image de plateforme.
Définir le disque du système d’exploitation pour l’utilisation d’une image de plateforme | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk\_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>Le nom du disque du système d’exploitation, ainsi que son emplacement dans le [stockage](../storage/storage-powershell-guide-full.md), sont ajoutés à l’objet de configuration que vous avez créé.
Définir le disque du système d’exploitation pour l’utilisation d’une image généralisée | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk\_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Le nom du disque du système d’exploitation, l’emplacement de l’image source et celui du disque au sein du [stockage](../storage/storage-powershell-guide-full.md) sont ajoutés à l’objet de configuration que vous avez créé.
Définir le disque du système d’exploitation pour l’utilisation d’une image spécialisée | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "name\_of\_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
Créer une machine virtuelle | [New-AzureRmVM]() -ResourceGroupName "resource\_group\_name" -Location "location\_name" -VM $vm<BR></BR><BR></BR>Toutes les ressources sont créées dans un [groupe de ressources](../powershell-azure-resource-manager.md). La machine virtuelle doit être créée au même [emplacement](https://msdn.microsoft.com/library/azure/dn495177.aspx) que le groupe de ressources. Avant d’exécuter cette commande, exécutez les commandes New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface et Set-AzureRmVMOSDisk.
Répertorier les machines virtuelles au sein d’un abonnement| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Répertorier les machines virtuelles au sein d’un groupe de ressources | Get-AzureRmVM -ResourceGroupName "resource\_group\_name"<BR></BR><BR></BR>Pour obtenir une liste de groupes de ressources dans votre abonnement, utilisez la commande [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obtenir des informations sur une machine virtuelle | Get-AzureRmVM -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Démarrer une machine virtuelle | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Arrêter une machine virtuelle | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Redémarrer une machine virtuelle en cours d’exécution | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Supprimer une machine virtuelle | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Généraliser une machine virtuelle | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm\_name" -Generalized<BR></BR><BR></BR>Vous devez exécuter cette commande avant la commande Save-AzureRmVMImage.
Capturer une machine virtuelle | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource\_group\_name" -VMName "vm\_name" -DestinationContainerName "image\_container" -VHDNamePrefix "image\_name\_prefix" -Path "C:\\filepath\\filename.json"<BR></BR><BR></BR>Une machine virtuelle doit être [éteinte et généralisée](virtual-machines-windows-capture-image.md) pour pouvoir servir à créer une image. Avant d’exécuter cette commande, exécutez la commande Set-AzureRmVm.
Mettre à jour une machine virtuelle | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource\_group\_name" -VM $vm<BR></BR><BR></BR>Obtenez la configuration de machine virtuelle actuelle à l’aide de la commande Get-AzureRmVM, modifiez les paramètres de configuration sur l’objet de machine virtuelle, puis exécutez cette commande.
Ajouter un disque de données à une machine virtuelle | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk\_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Utilisez la commande Get-AzureRmVM pour obtenir l’objet de machine virtuelle. Spécifiez le numéro de LUN et la taille du disque. Exécutez la commande Update-AzureRmVM pour appliquer les modifications apportées à la configuration à la machine virtuelle. Le disque que vous ajoutez n’est pas initialisé. Pour ce faire, voir [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).
Supprimer un disque de données à partir d'une machine virtuelle | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk\_name"<BR></BR><BR></BR>Utilisez la commande Get-AzureRmVM pour obtenir l’objet de machine virtuelle. Exécutez la commande Update-AzureRmVM pour appliquer les modifications apportées à la configuration à la machine virtuelle.
Ajouter une extension à une machine virtuelle | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource\_group\_name" -Location "azure\_location" -VMName "vm\_name" -Name "extension\_name" -Publisher "publisher\_name" -Type "extension\_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Exécutez cette commande avec les [informations de configuration](virtual-machines-windows-extensions-configuration-samples.md) appropriées correspondant à l’extension à installer.
Supprimer une extension de machine virtuelle | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource\_group\_name" -Name "extension\_name" -VMName "vm\_name"

## Étapes suivantes

- Consultez les étapes de base pour la création d’une machine virtuelle, décrites dans la rubrique [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](virtual-machines-windows-ps-create.md).

<!---HONumber=AcomDC_0629_2016-->