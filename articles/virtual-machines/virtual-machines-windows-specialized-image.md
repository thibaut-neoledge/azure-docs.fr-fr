---
title: Création d’une copie de votre machine virtuelle Windows | Microsoft Docs
description: Découvrez comment créer une copie de votre machine virtuelle Azure exécutant Windows dans le modèle de déploiement Resource Manager en créant une *image spécialisée*.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# Création d’une copie d’une machine virtuelle Windows dans le modèle de déploiement Azure Resource Manager
Cet article explique comment créer une copie de votre machine virtuelle Azure (VM) exécutée sous Windows. Plus précisément, il décrit comment effectuer cette opération dans le modèle de déploiement Azure Resource Manager, à l’aide d’Azure PowerShell et du portail Azure. Il décrit comment créer une image *spécialisée* de votre machine virtuelle Azure qui gère les comptes d’utilisateurs et d’autres données d’état à partir de votre machine virtuelle d’origine. Une image spécialisée est utile pour transmettre votre machine virtuelle Windows d’un modèle de déploiement classique vers le modèle de déploiement Resource Manager, ou pour créer une copie de sauvegarde de votre machine virtuelle Windows créée dans le modèle de déploiement Resource Manager. Vous pouvez copier les disques du système d’exploitation et les disques de données de cette manière, puis configurer les ressources réseau pour créer la nouvelle machine virtuelle.

Si vous avez besoin de créer des déploiements en nombre de machines virtuelles Windows similaires, nous vous recommandons d’utiliser une image *généralisée*. Pour ce faire, consultez la page [Comment capturer une machine virtuelle Windows dans le modèle de déploiement Resource Manager](virtual-machines-windows-capture-image.md).

## Avant de commencer
Assurez-vous de disposer des composants requis suivants avant de commencer la procédure :

* **Vous disposez d’une machine virtuelle Azure exécutant Windows** créée à l’aide du modèle de déploiement classique ou du modèle de déploiement Resource Manager. Vous avez configuré le système d’exploitation et les disques de données associés, et effectué d’autres procédures de personnalisation, telles que l’installation des applications requises. Vous utiliserez cette machine virtuelle pour créer la copie. Si vous avez besoin d’aide pour créer cette machine virtuelle, voir [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](virtual-machines-windows-ps-create.md).
* Vous avez installé **Azure PowerShell 1.0 ou version ultérieure** sur votre ordinateur et vous êtes connecté à votre abonnement Azure. Pour plus d’informations, consultez l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
* Vous avez téléchargé et installé l’**utilitaire AzCopy**. Pour plus d’informations sur cet utilitaire, consultez la rubrique [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../storage/storage-use-azcopy.md).
* Vous disposez d’un **groupe de ressources**, d’un **compte de stockage** et d’un **conteneur d’objets blob** créés dans ce groupe de ressources pour y copier les disques durs virtuels. Pour savoir comment utiliser un compte de stockage existant ou en créer un nouveau, voir la section [Pour créer ou rechercher un compte de stockage Azure à l’aide du portail Azure](virtual-machines-windows-upload-image.md#createstorage).

> [!NOTE]
> Des étapes similaires s’appliquent pour une machine virtuelle créée en utilisant l’un des deux modèles de déploiement comme image source. Le cas échéant, cet article vous indique les différences mineures.
> 
> 

## Copie de disques durs virtuels sur votre compte de stockage Resource Manager
1. Libérez les disques durs virtuels utilisés par la machine virtuelle source en exécutant l’une des procédures suivantes :
   
   * Pour copier votre machine virtuelle source, vous devez l’arrêter, puis la libérer.
     
     * Dans le cas d’une machine virtuelle créée à l’aide du modèle de déploiement Classic, vous pouvez soit vous connecter au [Portail](https://portal.azure.com) et cliquer sur **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Arrêter**, soit utiliser la commande PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`.
     * Dans le cas d’une machine virtuelle créée à l’aide du modèle de déploiement Resource Manager, vous pouvez soit vous connecter au portail et cliquer sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**, soit utiliser la commande PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Notez que l’état de la machine virtuelle dans le portail passe de la valeur **En cours d’exécution** à la valeur **Arrêté (désalloué)**.
   * Si vous souhaitez effectuer la migration de votre machine virtuelle source, supprimez cette machine et utilisez le disque dur virtuel désormais disponible. Accédez à votre machine virtuelle sur le [portail](https://portal.azure.com), puis cliquez sur **Supprimer**.
2. Recherchez les clés d’accès pour le compte de stockage qui contient votre disque dur virtuel source, ainsi que le compte de stockage dans lequel vous allez copier votre disque dur virtuel pour créer la machine virtuelle. La clé du compte à partir duquel nous copions le disque dur virtuel est appelée *Clé source*, et la clé du compte dans lequel il sera copié est appelée *Clé de destination*. Pour plus d’informations sur les clés d’accès, consultez la page [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).
   
   * Si votre machine virtuelle source a été créée à l’aide du modèle de déploiement Classic, cliquez sur **Parcourir** > **Comptes de stockage (classiques)** > *votre compte de stockage* > **Tous les paramètres** > **Clés**. Copiez la clé portant le nom **CLÉ D’ACCÈS PRIMAIRE**.
   * Pour une machine virtuelle créée à l’aide du modèle de déploiement Resource Manager, ou pour le compte de stockage que vous utiliserez avec votre nouvelle machine virtuelle, cliquez sur **Parcourir** > **Comptes de stockage** > *votre compte de stockage* > **Tous les paramètres** > **Clés d’accès**. Copiez la clé portant le nom **key1**.
3. Identifiez les URL d’accès à vos comptes de stockage source et de destination. Dans le portail, accédez à votre compte de stockage, puis cliquez sur **Objets blob**. Cliquez ensuite sur le conteneur qui héberge votre disque dur virtuel source (par exemple, *vhds* pour le modèle de déploiement classique) ou sur le conteneur dans lequel souhaitez copier le disque dur virtuel. Cliquez sur le champ **Propriétés** du conteneur et copiez le texte intitulé **URL**. Vous aurez besoin des URL des conteneurs source et de destination. Les URL ressembleront à ceci : `https://myaccount.blob.core.windows.net/mycontainer`.
4. Sur votre ordinateur local, ouvrez une fenêtre de commande et naviguez jusqu’au dossier dans lequel AzCopy est installé (ce dossier peut être semblable à *C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy*). À partir de ce dossier, exécutez la commande suivante : </br>
   
        AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
   </br>

> [!NOTE]
> Vous devez copier séparément les disques de système d’exploitation et de données, en utilisant l’utilitaire AzCopy comme décrit précédemment.
> 
> 

## Création d’une machine virtuelle à l’aide du disque dur virtuel copié
Vous pouvez maintenant utiliser Azure PowerShell pour créer une machine virtuelle Windows basée sur Resource Manager dans un nouveau réseau virtuel en utilisant le disque dur virtuel copié dans les étapes précédentes. Le disque dur virtuel doit être présent dans le même compte de stockage que la machine virtuelle qui sera créée.

Configurez un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle en utilisant le script suivant. Utilisez les valeurs des variables (représentées par le symbole **$**) adaptées à votre application.

    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

    $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


Ensuite, définissez les configurations de machines virtuelles et utilisez les disques durs virtuels copiés pour créer une machine virtuelle. </br>

    #Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

    #Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

Les URL des disques de données et des disques du système d’exploitation se présentent comme suit : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pour obtenir les URL, rendez-vous sur le portail, accédez au conteneur de stockage de destination, cliquez sur le disque dur virtuel du système d’exploitation ou de données qui a été copié, puis copiez le contenu de l’URL.

    #Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Si la commande a été exécutée avec succès, vous obtiendrez une sortie similaire à celle-ci :

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK


Vous devez voir la machine virtuelle nouvellement créée soit dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles**, soit en utilisant les commandes PowerShell suivantes :

    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name

Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP Bureau à distance. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle.

## Étapes suivantes
Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0928_2016-->