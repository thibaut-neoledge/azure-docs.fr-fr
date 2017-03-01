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
ms.devlang: na
ms.date: 02/02/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f520ed9cc1a3a7063d5b3ddacf7f0c8174e75a36
ms.openlocfilehash: 77cbdaaea0eec5265ef005f66dd5efdd8e237022
ms.lasthandoff: 02/21/2017

---
# <a name="add-a-disk-to-a-linux-vm"></a>Ajouter un disque à une machine virtuelle Linux
Cet article explique comment attacher un disque persistant à votre machine virtuelle afin de conserver vos données, et ce, même si votre machine virtuelle est remise en service en raison d’une opération de maintenance ou de redimensionnement. 

## <a name="quick-commands"></a>Commandes rapides
L’exemple suivant attache un disque de `50` Go à la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

Pour utiliser des disques gérés :

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk \
  –-new --size-gb 50
```

Pour utiliser des disques non gérés :

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```

## <a name="attach-a-managed-disk"></a>Attacher un disque géré

En utilisant des disques gérés, vous pouvez vous concentrer sur vos machines virtuelles et leurs disques sans vous soucier des comptes de stockage Azure. Vous pouvez rapidement créer et attacher un disque géré à une machine virtuelle en utilisant le même groupe de ressources Azure. Vous pouvez également créer autant de disques que vous le souhaitez et les attacher ensuite à la machine virtuelle.


### <a name="attach-a-new-disk-to-a-vm"></a>Attacher un nouveau disque à une machine virtuelle

Si vous avez uniquement besoin d’un nouveau disque sur votre machine virtuelle, vous pouvez utiliser la commande `az vm disk attach`.

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk \
  –-new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Association d'un disque existant 

Dans de nombreux cas, vous attachez des disques qui ont déjà été créés. Commencez par rechercher l’ID du disque pour l’intégrer à la commande `az vm disk attach`. L’exemple suivant utilise un disque créé avec `az disk create -g myResourceGroup -n myDataDisk --size-gb 50`.

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


## <a name="attach-an-unmanaged-disk"></a>Attacher un disque non géré

Vous pouvez rapidement ajouter un nouveau disque qui sera créé dans le même compte de stockage que votre machine virtuelle. Tapez `azure vm disk attach-new` afin de créer un nouveau disque (Go) et de l’associer à votre machine virtuelle. Si vous n'identifiez pas explicitement un compte de stockage, les disques que vous créez sont placés dans le même compte de stockage que celui sur lequel réside le disque du système d'exploitation. L’exemple suivant attache un disque de `50` Go à la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Se connecter à la machine virtuelle Linux afin de monter le nouveau disque
> [!NOTE]
> Dans cette rubrique, la connexion à la machine virtuelle est effectuée à l’aide de noms d’utilisateurs et de mots de passe. Pour utiliser des paires de clés publiques et privées pour interagir avec votre machine virtuelle, consultez [Utilisation de SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
> 
> 

Vous devez exécuter SSH dans votre machine virtuelle Azure afin de partitionner, de formater et de monter votre nouveau disque pour que la machine virtuelle Linux puisse l’utiliser. Si vous n’êtes pas familier de la connexion via **ssh**, la commande prend la forme `ssh <username>@<FQDNofAzureVM> -p <the ssh port>` et ressemble à ceci :

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com -p 22
```

Sortie

```bash
The authenticity of host 'mypublicdns.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@mypublicdns.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myVM:~$
```

Maintenant que vous êtes connecté à votre machine virtuelle, vous êtes prêt à attacher un disque.  Recherchez tout d’abord le disque à l’aide de `dmesg | grep SCSI` (La méthode que vous utilisez pour découvrir les capacités du nouveau disque peut varier). Le résultat est alors le suivant :

```bash
dmesg | grep SCSI
```

Sortie

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

et dans le scénario évoqué dans cette rubrique, le disque `sdc` est celui que nous voulons. Partitionnons maintenant le disque avec `sudo fdisk /dev/sdc`, en supposant que dans votre cas, il s’agissait d’un disque `sdc` et utilisons-le en tant que disque principal sur la partition 1, en acceptant les autres valeurs par défaut.

```bash
sudo fdisk /dev/sdc
```

Sortie

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

Créez la partition en saisissant `p` lors de l’invite :

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

Écrivez un système de fichiers sur la partition à l’aide de la commande **mkfs** , en spécifiant le type de votre système de fichier et le nom de l’appareil. Dans cette rubrique, nous utilisons `ext4` et `/dev/sdc1` (voir ci-dessus) :

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Sortie

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

Nous créons désormais un répertoire afin de monter le système de fichiers à l’aide de `mkdir`:

```bash
sudo mkdir /datadrive
```

Vous montez le répertoire à l’aide de `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

Le disque de données est désormais utilisable en tant que `/datadrive`.

```bash
ls
```

Sortie

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier /etc/fstab. En outre, il est vivement recommandé d’utiliser l’UUID (identificateur global unique) dans /etc/fstab comme référence au lecteur, plutôt que le nom d’appareil uniquement (par exemple, `/dev/sdc1`). Si le système d’exploitation détecte une erreur disque pendant le démarrage, l’utilisation de l’UUID évite que le disque incorrect ne soit monté sur un emplacement donné. Les disques de données restants reçoivent alors les mêmes ID d’appareil. Pour rechercher l’UUID du nouveau lecteur, utilisez l’utilitaire **blkid** :

```bash
sudo -i blkid
```

Le résultat ressemble à ce qui suit :

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> si vous ne modifiez pas correctement le fichier **/etc/fstab** , il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.
> 
> 

Ensuite, ouvrez le fichier **/etc/fstab** dans un éditeur de texte :

```bash
sudo vi /etc/fstab
```

Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** créé lors des étapes précédentes et le point de montage **/datadrive**. Ajoutez la ligne suivante à la fin du fichier **/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> La suppression ultérieure d’un disque de données sans modifier fstab pourrait entraîner l’échec du démarrage de la machine virtuelle. La plupart des distributions proposent les options fstab `nofail` et/ou `nobootwait`. Ces options permettent à un système de démarrer même si le disque n’est pas monté au moment du démarrage. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution.
> 
> L’option **nofail** garantit que la machine virtuelle démarre même si le système de fichiers est endommagé ou si le disque n’existe pas au moment du démarrage. Sans cette option, vous pouvez être confronté au comportement décrit dans [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Connexion SSH vers machine virtuelle Linux impossible en raison d’erreurs FSTAB)

### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge de TRIM/UNMAP pour Linux dans Azure
Certains noyaux Linux prennent en charge les opérations TRIM/UNMAP pour ignorer les blocs inutilisés sur le disque. Cela est particulièrement utile dans le stockage standard pour informer Azure que des pages supprimées ne sont plus valides et peuvent être ignorées. Vous pouvez ainsi faire des économies si vous créez des fichiers volumineux, puis les supprimez.

Il existe deux façons d’activer la prise en charge de TRIM sur votre machine virtuelle Linux. Comme d’habitude, consultez votre distribution pour connaître l’approche recommandée :

* Utilisez l’option de montage `discard` dans `/etc/fstab`, par exemple :

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
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Étapes suivantes
* N’oubliez pas que votre nouveau disque n’est pas disponible sur votre machine virtuelle en cas de redémarrage, sauf si vous écrivez les informations sur votre fichier [fstab](http://en.wikipedia.org/wiki/Fstab) .
* Pour vous assurer que votre machine virtuelle Linux est correctement configurée, passez en revue les recommandations visant à [optimiser les performances de votre machine virtuelle Linux](virtual-machines-linux-optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
* Développez votre capacité de stockage en ajoutant des disques supplémentaires et [configurez RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour augmenter les performances.


