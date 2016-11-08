Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage. Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.

> [!NOTE]
> Dans Azure, une machine virtuelle utilise différents types de disque, comme le disque du système d’exploitation, un disque temporaire local et des disques de données facultatifs. Pour en savoir plus, consultez la section [À propos des disques et VHD pour machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). Vous ne pouvez pas détacher un disque de système d’exploitation si vous ne supprimez pas également la machine virtuelle.
> 
> 

## Recherche du disque
Avant de pouvoir détacher un disque d'une machine virtuelle, vous devez rechercher le nombre LUN (numéro d’unité logique), qui est un identificateur pour le disque à détacher. Pour ce faire, procédez comme suit :

1. Ouvrez l’interface de ligne de commande (CLI) Azure, puis [connectez-vous à votre abonnement Azure](../articles/xplat-cli-connect.md). Assurez-vous que vous êtes en mode Azure Service Management (`azure config mode asm`).
2. Recherchez les disques attachés à votre machine virtuelle à l’aide de la commande`azure vm disk list <virtual-machine-name>`:
   
    $azure vm disk list UbuntuVM
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
3. Notez le LUN ou le **numéro d'unité logique** pour le disque que vous souhaitez détacher.

## Supprimer les références de système d’exploitation sur le disque
Avant de détacher le disque de l’invité Linux, assurez-vous que toutes les partitions sur le disque ne sont pas en cours d’utilisation. Assurez-vous que le système d’exploitation n’essaie pas de les remonter après un redémarrage. Ces étapes annulent la configuration que vous avez probablement créée lors de [l’attachement](../articles/virtual-machines/virtual-machines-linux-classic-attach-disk.md) du disque.

1. Utilisez la commande `lsscsi` pour détecter l’iddentifiateur de disque. `lsscsi` peut être installé par `yum install lsscsi` (dans des distributions Red Hat) ou `apt-get install lsscsi` (dans des distributions Debian). Vous pouvez trouver l’identificateur de disque que vous recherchez à l’aide du numéro de LUN. Le dernier numéro du tuple dans chaque ligne est le LUN. Dans l’exemple ci-dessous LUN 0 correspond à */dev/sdc*
   
            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
            [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
2. Utilisez `fdisk -l <disk>` pour détecter les partitions associées au disque à détacher.
3.          $ sudo fdisk -l /dev/sdc
            Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
            Units = sectors of 1 * 512 = 512 bytes
            Sector size (logical/physical): 512 bytes / 512 bytes
            I/O size (minimum/optimal): 512 bytes / 512 bytes
            Disk label type: dos
            Disk identifier: 0x5a1d2a1a
   
               Device Boot      Start         End      Blocks   Id  System
            /dev/sdc1            2048  2145386495  1072692224   83  Linux
4. Démontez chaque partition répertoriée pour le disque. Dans cet exemple : `$ sudo umount /dev/sdc1`
5. Utilisez la commande `blkid` pour détecter les UUID de toutes les partitions.
   
            $ sudo blkid
            /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
            /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
            /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
            /dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
6. Supprimez les entrées dans le fichier **/etc/fstab** associé aux chemins d’accès de l’appareil ou aux UUID pour toutes les partitions du disque à détacher. Pour cet exemple, les entrées peuvent être les suivantes :
   
        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ou
   
        /dev/sdc1   /datadrive   ext4   defaults   1   2

## Détachement du disque
Une fois le numéro de LUN du disque identifié et les références de système d’exploitation supprimées, vous pouvez détacher le disque :

1. Détachez le disque sélectionné de la machine virtuelle en exécutant la commande `azure vm disk detach
   <virtual-machine-name> <LUN>` :
   
    $azure vm disk detach UbuntuVM 0
    info:    Executing command vm disk detach
   
   * Getting virtual machines
   * Removing Data-Disk
     info:    vm disk detach command OK
2. Vous pouvez vérifier si le disque est détaché en exécutant la commande suivante :
   
    $azure vm disk list UbuntuVM
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     info:    vm disk list command OK

Le disque détaché reste dans le stockage, mais il n'est plus attaché à une machine virtuelle.

<!---HONumber=AcomDC_0824_2016-->