---
title: "Ajouter un disque à une machine virtuelle Linux avec Azure CLI | Microsoft Docs"
description: "Découvrez comment ajouter un disque persistant à votre machine virtuelle Linux avec Azure CLI 1.0 et 2.0."
keywords: machine virtuelle Linux, ajouter un disque de ressources
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 02/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9db7d300b745001906bdc38769dcbe6e4d7c7b83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-disk-to-a-linux-vm"></a>Ajouter un disque à une machine virtuelle Linux
Cet article vous explique comment attacher un disque persistant à votre machine virtuelle afin de conserver vos données, et ce, même si votre machine virtuelle est remise en service en raison d’une opération de maintenance ou de redimensionnement. 


## <a name="use-managed-disks"></a>Utiliser des disques gérés
Azure Managed Disks simplifie la gestion des disques des machines virtuelles Azure, en gérant les comptes de stockage associés aux disques de machines virtuelles. Vous spécifiez simplement le type (Premium ou Standard) et la taille du disque dont vous avez besoin, et Azure crée et gère le disque pour vous. Pour plus d’informations, consultez [Vue d’ensemble des disques gérés](managed-disks-overview.md).


### <a name="attach-a-new-disk-to-a-vm"></a>Attacher un nouveau disque à une machine virtuelle
Si vous avez seulement besoin d’un nouveau disque sur votre machine virtuelle, utilisez la commande [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) avec le paramètre `--new`. Si votre machine virtuelle est dans une Zone de disponibilité, le disque est automatiquement créé dans la même zone que la machine virtuelle. Pour plus d'informations, consultez [Vue d’ensemble des zones de disponibilité](../../availability-zones/az-overview.md). L’exemple suivant crée un disque nommé *myDataDisk* avec une taille de *50* Go :

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Association d'un disque existant 
Dans de nombreux cas vous attachez des disques qui ont déjà été créés. Pour attacher un disque existant, rechercher l’ID de disque et transmettez-le à la commande [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach). L’exemple suivant interroge pour un disque nommé *myDataDisk* dans *myResourceGroup*, puis l’attache à la machine virtuelle nommée *myVM* :

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

Le résultat ressemble à ce qui suit (vous pouvez appliquer l’option `-o table` à n’importe quelle commande afin d’obtenir le bon format de sortie) :

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="use-unmanaged-disks"></a>Utiliser des disques non gérés
Les disques non gérés requièrent des coûts de fonctionnement supplémentaires pour créer et gérer les comptes de stockage sous-jacents. Les disques non gérés sont créés dans le même compte de stockage que le disque de votre système d’exploitation. Pour créer et attacher un disque non géré, utilisez la commande [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk?view=azure-cli-latest#az_vm_unmanaged_disk_attach). L’exemple suivant attache un disque non géré de *50* Go à la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Se connecter à la machine virtuelle Linux afin de monter le nouveau disque
Vous devez exécuter SSH dans votre machine virtuelle Azure afin de partitionner, de formater et de monter votre nouveau disque pour que la machine virtuelle Linux puisse l’utiliser. Pour plus d’informations, consultez l’article [Utilisation de SSH avec Linux sur Azure](mac-create-ssh-keys.md). L’exemple suivant permet de se connecter à une machine virtuelle avec l’entrée DNS publique de *mypublicdns.westus.cloudapp.azure.com* avec le nom d’utilisateur *azureuser* : 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Une fois connecté à votre machine virtuelle, vous êtes prêt à attacher un disque. Recherchez tout d’abord le disque à l’aide de `dmesg` (la méthode que vous utilisez pour découvrir votre nouveau disque peut varier). L’exemple suivant utilise dmesg pour filtrer les disques *SCSI* :

```bash
dmesg | grep SCSI
```

Le résultat ressemble à l’exemple suivant :

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Ici, *sdc* est le disque que nous recherchons. Partitionnez le disque avec `fdisk`, faites-en le disque principal sur la partition 1 et acceptez les autres valeurs par défaut. L’exemple suivant démarre le processus `fdisk` sur */dev/sdc* :

```bash
sudo fdisk /dev/sdc
```

Le résultat ressemble à l’exemple suivant :

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Créez la partition en saisissant `p` lors de l’invite, comme suit :

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

À présent, écrivez un système de fichiers dans la partition avec la commande `mkfs`. Spécifiez le type de votre système de fichiers et le nom de l’appareil. L’exemple suivant crée un système de fichiers *ext4* sur la partition */dev/sdc1* créée dans les étapes précédentes :

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Le résultat ressemble à l’exemple suivant :

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

À présent, créez un répertoire afin de monter le système de fichiers à l’aide de `mkdir`. L’exemple suivant crée un répertoire sous */datadrive* :

```bash
sudo mkdir /datadrive
```

Utilisez `mount` pour monter le système de fichiers. L’exemple suivant monte la partition */dev/sdc1* pour le point de montage */datadrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier */etc/fstab*. Il est aussi vivement recommandé d’utiliser l’UUID (identificateur global unique) dans */etc/fstab* comme référence au lecteur, plutôt que le nom d’appareil uniquement (par exemple, */dev/sdc1*). Si le système d’exploitation détecte une erreur disque pendant le démarrage, l’utilisation de l’UUID évite que le disque incorrect ne soit monté sur un emplacement donné. Les disques de données restants reçoivent alors les mêmes ID d’appareil. Pour rechercher l’UUID du nouveau lecteur, utilisez l’utilitaire `blkid` :

```bash
sudo -i blkid
```

Le résultat ressemble à l’exemple qui suit :

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> si vous ne modifiez pas correctement le fichier **/etc/fstab** , il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

Ensuite, ouvrez le fichier */etc/fstab* dans un éditeur de texte, comme suit :

```bash
sudo vi /etc/fstab
```

Dans cet exemple, nous utilisons la valeur UUID pour l’appareil */dev/sdc1* créé lors des étapes précédentes et le point de montage */datadrive*. Ajoutez la ligne suivante à la fin du fichier */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> La suppression ultérieure d’un disque de données sans modifier fstab pourrait entraîner l’échec du démarrage de la machine virtuelle. La plupart des distributions fournissent les options fstab *nofail* et/ou *nobootwait*. Ces options permettent à un système de démarrer même si le disque n’est pas monté au moment du démarrage. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution.
> 
> L’option *nofail* garantit que la machine virtuelle démarre même si le système de fichiers est endommagé ou si le disque n’existe pas au moment du démarrage. Sans cette option, vous pouvez être confronté au comportement décrit dans [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Connexion SSH vers machine virtuelle Linux impossible en raison d’erreurs FSTAB)

### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge de TRIM/UNMAP pour Linux dans Azure
Certains noyaux Linux prennent en charge les opérations TRIM/UNMAP pour ignorer les blocs inutilisés sur le disque. Cette fonctionnalité est particulièrement utile dans le stockage standard pour informer Azure que des pages supprimées ne sont plus valides et peuvent être ignorées. Elle peut vous permettre d’économiser de l’argent si vous créez des fichiers volumineux, puis que vous les supprimez.

Il existe deux façons d’activer la prise en charge de TRIM sur votre machine virtuelle Linux. Comme d’habitude, consultez votre distribution pour connaître l’approche recommandée :

* Utilisez l’option de montage `discard` dans */etc/fstab*, par exemple :

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Dans certains cas, l’option `discard` peut avoir un impact sur la performance. Vous pouvez également exécuter la commande `fstrim` manuellement à partir de la ligne de commande ou l’ajouter à votre crontab pour l’exécuter régulièrement :
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Résolution des problèmes
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Étapes suivantes
* N’oubliez pas que votre nouveau disque n’est pas disponible sur votre machine virtuelle en cas de redémarrage, sauf si vous écrivez les informations sur votre fichier [fstab](http://en.wikipedia.org/wiki/Fstab) .
* Pour vous assurer que votre machine virtuelle Linux est correctement configurée, passez en revue les recommandations visant à [optimiser les performances de votre machine virtuelle Linux](optimization.md) .
* Développez votre capacité de stockage en ajoutant des disques supplémentaires et [configurez RAID](configure-raid.md) pour augmenter les performances.

