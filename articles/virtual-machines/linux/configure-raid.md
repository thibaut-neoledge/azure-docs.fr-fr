---
title: "Configuration logicielle de RAID sur une machine virtuelle exécutant Linux | Microsoft Docs"
description: "Apprenez à utiliser mdadm pour configurer RAID sur Linux dans Azure."
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.openlocfilehash: 12f540a700fbf85e579e8aadc9f6def039299ff7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="configure-software-raid-on-linux"></a>Configuration d’un RAID logiciel sur Linux
L'utilisation d'un RAID logiciel pour les machines virtuelles Linux sur Azure est un scénario fréquent afin de regrouper plusieurs disques de données attachés sous la forme d'un périphérique RAID unique. En règle générale, ce scénario permet d'optimiser les performances et d'améliorer le débit par rapport à l'utilisation d'un disque unique.

## <a name="attaching-data-disks"></a>Disques de données attachés
Au moins deux disques de données sont nécessaires pour configurer un périphérique RAID.  La raison principale pour la création d’un appareil RAID est d’améliorer les performances de vos E/S de disque.  En fonction de vos besoins d’E/S, vous pouvez choisir d’attacher des disques qui sont stockés dans notre stockage Standard, avec un maximum de 500 opérations d’E/S par disque ou dans notre stockage Premium avec un maximum de 5 000 opérations d’E/S par disque. Cet article n’aborde pas en détail la marche à suivre pour approvisionner et attacher des disques de données sur une machine virtuelle Linux.  Consultez l’article Microsoft Azure [Attacher un disque](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour obtenir des instructions détaillées sur la marche à suivre pour attacher un disque de données vide à une machine virtuelle Linux dans Azure.

## <a name="install-the-mdadm-utility"></a>Installation de l'utilitaire mdadm
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS et Oracle Linux**
```bash
sudo yum install mdadm
```

* **SLES et openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>Création des partitions de disque
Dans cet exemple, nous créons une partition de disque unique sur /dev/sdc. La nouvelle partition de disque sera appelée /dev/sdc1.

1. Démarrez `fdisk` pour lancer la création des partitions

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

2. Appuyez sur « n » à l’invite de commandes pour créer un  **n** partition de la nouvelle :

    ```bash
    Command (m for help): n
    ```

3. Ensuite, appuyez sur « p » à l'invite pour créer une partition **p**rincipale :

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Appuyez sur « 1 » pour sélectionner la partition numéro 1 :

    ```bash
    Partition number (1-4): 1
    ```

5. Sélectionnez le point de départ de la nouvelle partition ou appuyez sur `<enter>` pour accepter le paramètre par défaut et placer la partition au début de l’espace libre sur le disque :

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Sélectionnez la taille de la partition. Par exemple, tapez « +10G » pour créer une partition de 10 Go. Vous pouvez aussi appuyer sur `<enter>` pour créer une seule partition pour l’intégralité du disque :

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Ensuite, remplacez l'ID et le **t**ype de partition de l'ID par défaut « 83 » (Linux) par l'ID « fd » (raid auto Linux) :

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Enfin, écrivez la table de partition sur le disque et quittez fdisk :

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Création du contrôleur RAID
1. L’exemple suivant permet de créer un volume agrégé par bandes (RAID 0) de trois partitions sur trois disques de données distincts (sdc1, sdd1, sde1).  Après l’exécution de cette commande, un nouveau périphérique RAID dénommé **/dev/md127** est créé. Notez que si les disques de données précédemment utilisés font partie d’un autre contrôleur RAID qui n’existe plus, il peut s'avérer nécessaire d’ajouter le paramètre `--force` à la commande `mdadm`.

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Créez le système de fichiers sur le nouveau périphérique RAID.
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE et Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** : activez boot.md et créez mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Un redémarrage peut être nécessaire après avoir apporté ces modifications sur des systèmes SUSE. Cette étape n’est *pas* requise pour SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Ajout du nouveau système de fichiers à /etc/fstab
> [!IMPORTANT]
> si vous ne modifiez pas correctement le fichier /etc/fstab, il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

1. Créez le point de montage désiré pour le nouveau système de fichiers. Par exemple :

    ```bash
    sudo mkdir /data
    ```
2. Lors de la modification du fichier /etc/fstab, l' **identificateur unique universel** doit être utilisé pour faire référence au système de fichiers plutôt qu'au nom de périphérique.  Servez-vous de l'utilitaire `blkid` pour déterminer l'identificateur unique universel (UUID) du nouveau système de fichiers :

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Ouvrez /etc/fstab dans un éditeur de texte, puis ajoutez une entrée pour le nouveau système de fichiers, par exemple :

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Ou sur **SLES 11** :

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Ensuite, enregistrez et fermez /etc/fstab.

4. Vérifiez si l'entrée /etc/fstab est correcte :

    ```bash  
    sudo mount -a
    ```

    Si cette commande génère un message d’erreur, vérifiez que la syntaxe utilisée dans le fichier /etc/fstab est correcte.
   
    Ensuite, exécutez la commande `mount` pour vérifier que le système de fichiers est monté :

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Facultatif) Paramètres de démarrage fiables
   
    **Configuration fstab**
   
    De nombreuses distributions comprennent les paramètres de montage `nobootwait` ou `nofail` pouvant être ajoutés au fichier /etc/fstab. Ces paramètres autorisent les échecs lors du montage d'un système de fichiers donné et permettent au système Linux de continuer à démarrer même s'il n'a pas été en mesure de monter le système de fichiers RAID. Pour plus d’informations sur ces paramètres, reportez-vous à la documentation de votre distribution.
   
    Exemple (Ubuntu) :

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Paramètres de démarrage Linux**
   
    En plus des paramètres ci-dessus, le paramètre de noyau «`bootdegraded=true`» peut permettre au système de démarrer, même si le RAID est identifié comme endommagé ou détérioré, par exemple si un disque de données est supprimé par inadvertance de la machine virtuelle. Par défaut, cette situation peut également rendre impossible le démarrage du système.
   
    Pour plus d'informations sur la modification adéquate des paramètres de noyau, reportez-vous à la documentation de votre distribution. Par exemple, dans de nombreuses distributions (CentOS, Oracle Linux, SLES 11), ces paramètres peuvent être ajoutés manuellement au fichier «`/boot/grub/menu.lst`».  Sur Ubuntu, ce paramètre peut être ajouté à la variable `GRUB_CMDLINE_LINUX_DEFAULT` dans « /etc/default/grub ».


## <a name="trimunmap-support"></a>Prise en charge de TRIM/UNMAP
Certains noyaux Linux prennent en charge les opérations TRIM/UNMAP pour ignorer les blocs inutilisés sur le disque. Ces opérations sont particulièrement utiles dans un stockage standard pour informer Azure que des pages supprimées ne sont plus valides et peuvent être ignorées. Le fait d’ignorer des pages peut vous permettre de réaliser des économies si vous créez des fichiers volumineux, puis les supprimez.

> [!NOTE]
> RAID ne peut pas émettre de commandes Ignorer si la taille de segment de tableau est inférieure à la valeur par défaut (512 Ko). La raison en est que la granularité d’annulation de mappage sur l’ordinateur hôte est également de 512 Ko. Si vous avez modifié la taille de segment du tableau par l’intermédiaire du paramètre `--chunk=` de mdadm, les demandes TRIM ou d’annulation de mappage peuvent être ignorées par le noyau.

Il existe deux façons d’activer la prise en charge de TRIM sur votre machine virtuelle Linux. Comme d’habitude, consultez votre distribution pour connaître l’approche recommandée :

- Utilisez l’option de montage `discard` dans `/etc/fstab`, par exemple :

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Dans certains cas, l’option `discard` peut avoir un impact sur la performance. Vous pouvez également exécuter la commande `fstrim` manuellement à partir de la ligne de commande ou l’ajouter à votre crontab pour l’exécuter régulièrement :

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
