---
title: "Utilisation d’une machine virtuelle de dépannage Windows avec Azure PowerShell | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de machines virtuelles Windows dans Azure en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide d’Azure PowerShell"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: 8b7821b4285e73d461af426bfdfb3fdcc4454517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Résoudre les problèmes d’une machine virtuelle Windows en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide d’Azure PowerShell
Si votre machine virtuelle Windows dans Azure rencontre une erreur de démarrage ou de disque, il vous faudra éventuellement appliquer la procédure de dépannage directement sur le disque dur virtuel. Comme exemple courant, citons l’échec de mise à jour d’une application qui empêche le bon démarrage de la machine virtuelle. Cet article vous explique comment utiliser Azure PowerShell pour connecter votre disque dur virtuel à une autre machine virtuelle Windows pour corriger les éventuelles erreurs, puis pour régénérer votre machine virtuelle d’origine.


## <a name="recovery-process-overview"></a>Vue d’ensemble du processus de récupération
Le processus de résolution de problème se présente comme suit :

1. Supprimez les machines virtuelles présentant des erreurs, en conservant les disques durs virtuels.
2. Fixez et montez le disque dur virtuel sur une autre machine virtuelle Windows, à des fins de résolution des problèmes.
3. Connectez-vous à la machine virtuelle de dépannage. Modifiez les fichiers ou exécutez des outils afin de corriger les problèmes sur le disque dur virtuel d’origine.
4. Démontez le disque dur virtuel d’origine et dissociez-le de la machine virtuelle de dépannage.
5. Créez une machine virtuelle à l’aide du disque dur virtuel d’origine.

Vérifiez que la [dernière version d’Azure PowerShell](/powershell/azure/overview) est installée et connectez-vous à votre abonnement :

```powershell
Login-AzureRMAccount
```

Dans les exemples suivants, remplacez les noms de paramètres avec vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.


## <a name="determine-boot-issues"></a>Identifier les problèmes de démarrage
Vous pouvez afficher une capture d’écran de votre machine virtuelle dans Azure pour vous aider à résoudre les problèmes de démarrage. Cette capture d’écran peut vous aider à identifier la cause de l’échec de démarrage d’une machine virtuelle. L’exemple suivant récupère la capture d’écran de la machine virtuelle Windows nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Examinez la capture d’écran afin d’identifier la raison pour laquelle le démarrage de la machine virtuelle est mis en échec. Notez les messages d’erreur ou les codes d’erreur spécifiques fournis.


## <a name="view-existing-virtual-hard-disk-details"></a>Afficher les détails du disque dur virtuel existant
Avant de pouvoir associer votre disque dur virtuel à une autre machine virtuelle, vous devez identifier le nom du disque dur virtuel.

L’exemple suivant récupère des informations pour la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Recherchez `Vhd URI` dans la section `StorageProfile` dans la sortie de la commande précédente. L’exemple de sortie tronquée suivant affiche `Vhd URI` vers la fin du bloc de code :

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Supprimer une machine virtuelle existante
Les disques durs virtuels et les machines virtuelles sont deux ressources distinctes dans Azure. Le disque dur virtuel est l’emplacement de stockage du système d’exploitation, des applications et des configurations. La machine virtuelle correspond à des métadonnées définissant la taille ou l’emplacement ; elle référence des ressources comme un disque dur virtuel ou une carte d’interface réseau virtuelle (NIC). Chaque disque dur virtuel associé à une machine virtuelle se voit attribuer un bail. Bien que l’association et la dissociation des disques de données puisse s’effectuer pendant l’exécution de la machine virtuelle, le disque du système d’exploitation ne peut pas être dissocié, sauf si la ressource de machine virtuelle est supprimée. Le bail continue à associer le disque du système d’exploitation à une machine virtuelle, même lorsque cette machine virtuelle se trouve dans un état d’arrêt ou de libération.

La première étape de la récupération de votre machine virtuelle consiste à supprimer la ressource de machine virtuelle. En supprimant la machine virtuelle, vous ne vous séparez pas des disques durs virtuels de votre compte de stockage. Une fois la machine virtuelle supprimée, vous associez le disque dur virtuel à une autre machine virtuelle afin de réparer et de corriger les erreurs.

L’exemple suivant supprime la machine virtuelle nommée `myVM` du groupe de ressource nommé `myResourceGroup` :

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Attendez la suppression définitive de la machine virtuelle avant d’associer le disque dur virtuel à une autre machine virtuelle. Le bail du disque dur virtuel, qui l’associe à la machine virtuelle, doit être libéré avant l’association du disque dur virtuel à une autre machine virtuelle.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Associer un disque dur virtuel existant à une autre machine virtuelle
Pour les prochaines étapes, vous utilisez une autre machine virtuelle à des fins de résolution des problèmes. Vous associez le disque dur virtuel existant à cette machine virtuelle de dépannage et modifiez le contenu du disque. Ce processus vous permet de corriger les éventuelles erreurs de configuration ou d’examiner des fichiers journaux supplémentaires de système ou d’application, par exemple. Sélectionnez ou créez une autre machine virtuelle à des fins de résolution des problèmes.

Lorsque vous associez le disque dur virtuel existant, spécifiez l’URL du disque obtenu dans la commande `Get-AzureRmVM` précédente. L’exemple suivant associe un disque dur virtuel existant à la machine virtuelle de dépannage nommée `myVMRecovery` dans le groupe de ressources nommé `myResourceGroup` :

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> L’ajout d’un disque nécessite de spécifier la taille du disque. Lorsque nous attachons un disque existant, la valeur `-DiskSizeInGB` est spécifiée en tant que `$null`. Cette valeur garantit que le disque de données est correctement attaché sans avoir besoin de déterminer la taille réelle du disque de données.


## <a name="mount-the-attached-data-disk"></a>Monter le disque de données associé

1. Effectuez une connexion Bureau à distance sur votre machine virtuelle de dépannage à l’aide des informations d’identification appropriées. L’exemple suivant télécharge le fichier de connexion Bureau à distance pour la machine virtuelle nommée `myVMRecovery` dans le groupe de ressources nommé `myResourceGroup` et le télécharge vers `C:\Users\ops\Documents` »

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Le disque de données est automatiquement détecté et attaché. Affichez la liste des volumes attachés pour déterminer la lettre de lecteur comme suit :

    ```powershell
    Get-Disk
    ```

    L’exemple de sortie suivant montre le disque dur virtuel connecté à un disque **2**. (Vous pouvez également utiliser `Get-Volume` pour afficher la lettre de lecteur) :

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Résoudre des problèmes sur le disque dur virtuel d’origine
Avec le disque dur virtuel existant monté, vous pouvez désormais exécuter les procédures de maintenance et de dépannage requises. Une fois que vous avez résolu les problèmes, passez aux étapes suivantes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Démonter et dissocier le disque dur virtuel d’origine
Une fois les erreurs résolues, vous démontez et dissociez le disque dur virtuel existant de votre machine virtuelle de dépannage. Vous ne pouvez pas utiliser votre disque dur virtuel avec une autre machine virtuelle avant la publication du bail associant le disque dur virtuel à la machine virtuelle de dépannage.

1. À partir de votre session Bureau à distance, démontez le disque de données sur votre machine virtuelle de récupération. Vous devez obtenir le numéro du disque à partir de l’applet de commande `Get-Disk` précédente. Ensuite, utilisez `Set-Disk` pour définir le disque comme étant hors connexion :

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirmez que le disque est désormais défini comme étant hors connexion en utilisant `Get-Disk` à nouveau. L’exemple de sortie suivant montre que le disque est désormais défini comme étant hors connexion :

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Quittez la session Bureau à distance. À partir de votre session Azure PowerShell, supprimez le disque dur virtuel de la machine virtuelle de dépannage.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Créer une machine virtuelle à partir du disque dur d’origine
Pour créer une machine virtuelle à partir de votre disque dur d’origine, utilisez [ce modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Le véritable modèle JSON se trouve sur le lien suivant :

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

Le modèle déploie une machine virtuelle dans un réseau virtuel existant, à l’aide de l’URL de disque dur virtuel de la commande précédente. L’exemple suivant déploie le modèle du groupe de ressources nommé `myResourceGroup` :

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Répondez aux invites pour le modèle, relatives notamment au nom de machine virtuelle, au type de système d’exploitation et à la taille de machine virtuelle. L’élément `osDiskVhdUri` est celui utilisé lors de l’association du disque dur virtuel existant à la machine virtuelle de dépannage.


## <a name="re-enable-boot-diagnostics"></a>Réactiver les diagnostics de démarrage

Lorsque vous créez votre machine virtuelle à partir du disque dur virtuel existant, les diagnostics de démarrage peuvent ne pas être automatiquement activés. L’exemple suivant active l’extension de diagnostic sur la machine virtuelle nommée `myVMDeployed`, dans le groupe de ressources nommé `myResourceGroup` :

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez [Dépannage d’une connexion Bureau à distance à une machine virtuelle Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour résoudre les problèmes liés à l’accès aux applications exécutées sur votre machine virtuelle, consultez [Résoudre les problèmes de connectivité des applications sur une machine virtuelle Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus d’informations sur l’utilisation de Resource Manager, consultez la page [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
