---
title: "Configurer LVM sur une machine virtuelle exécutant Linux | Microsoft Docs"
description: "Apprenez à configurer LVM sur Linux dans Azure."
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 9b77d0e6d21ece908960a60d17a0460b806399ee
ms.openlocfilehash: df7a1f44f5c7733a4c4b889db13291f8d3d1a3d8


---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurer LVM sur une machine virtuelle Linux dans Azure
Ce document vous explique comment configurer le gestionnaire de volume logique (LVM) sur votre machine virtuelle Azure. Bien qu’il soit possible de configurer LVM sur tout disque connecté à la machine virtuelle, par défaut la plupart des images cloud n’auront pas LVM configuré sur le disque du système d’exploitation. Cela vise à éviter les problèmes liés aux groupes de volumes en double si le disque du système d’exploitation est joint à une autre machine virtuelle de la distribution et du même type, par exemple lors d’un scénario de récupération. Par conséquent, il est recommandé de n’utiliser LVM que sur les disques de données.

## <a name="linear-vs-striped-logical-volumes"></a>Volumes logiques linéaires et agrégé par bandes
LVM peut être utilisé pour combiner plusieurs disques physiques en un seul volume de stockage. Par défaut, LVM crée généralement des volumes logiques linéaires, ce qui signifie que le stockage physique est concaténé. Dans ce cas les opérations de lecture/d’écriture sont généralement envoyées à un seul disque. En revanche, nous pouvons également créer des volumes logiques agrégés par bandes où les lectures et écritures sont distribuées à plusieurs disques contenus dans le groupe de volumes (par exemple, similaire à RAID 0). Pour obtenir des performances optimales, il est recommandé d’agréger les volumes logiques afin que les lectures et écritures utilisent tous les disques de données joints.

Ce document décrit comment combiner plusieurs disques de données dans un seul groupe de volumes, puis créer un volume logique agrégé par bandes. Les étapes ci-dessous sont généralisées de manière à fonctionner avec la plupart des distributions. Dans la plupart des cas les utilitaires et les workflows de gestion LVM sur Azure ne sont pas fondamentalement différents des autres environnements. Comme d’habitude, veuillez également consulter votre fournisseur Linux pour obtenir la documentation et les meilleures pratiques pour utiliser LVM avec votre distribution spécifique.

## <a name="attaching-data-disks"></a>Disques de données attachés
Il est généralement recommandé de commencer avec au moins deux disques de données pour utiliser LVM. En fonction de vos besoins d’E/S, vous pouvez choisir d’attacher des disques qui sont stockés dans notre stockage Standard, avec un maximum de 500 opérations d’E/S par disque ou dans notre stockage Premium avec un maximum de 5 000 opérations d’E/S par disque. Cet article n’aborde pas en détail la marche à suivre pour approvisionner et attacher des disques de données sur une machine virtuelle Linux. Consultez l’article Microsoft Azure [Attacher un disque](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour obtenir des instructions détaillées sur la marche à suivre pour attacher un disque de données vide à une machine virtuelle Linux dans Azure.

## <a name="install-the-lvm-utilities"></a>Installer les utilitaires LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 et openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    Sur SLES11, vous devez également modifier `/etc/sysconfig/lvm` et définir `LVM_ACTIVATED_ON_DISCOVERED` sur « activer » :

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurer LVM
Dans ce guide, nous supposons que vous avez connecté trois disques de données, nommés `/dev/sdc`, `/dev/sdd` et `/dev/sde`. Notez que les chemins peuvent être différents dans votre machine virtuelle. Vous pouvez exécuter ’`sudo fdisk -l`’ ou une commande semblable pour répertorier vos disques disponibles.

1. Préparer les volumes physiques :

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Créez un groupe de volumes. Dans cet exemple, nous appelons le groupe de volumes `data-vg01` :

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Créez le ou les volumes logiques. La commande ci-dessous crée un volume logique appelé `data-lv01` pour couvrir le groupe de volumes entier. Notez qu’il est également possible de créer plusieurs volumes logiques dans le groupe de volumes.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formater le volume logique

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Avec SLES 11, utilisez `-t ext3` plutôt qu’ext4. SLES 11 prend uniquement en charge l’accès en lecture seule aux systèmes de fichiers ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Ajout du nouveau système de fichiers à /etc/fstab
> [!IMPORTANT]
> si vous ne modifiez pas correctement le fichier `/etc/fstab`, il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier `/etc/fstab` avant de le modifier.

1. Créez le point de montage désiré pour le nouveau système de fichiers. Par exemple :

    ```bash  
    sudo mkdir /data
    ```

2. Trouver le chemin du volume logique

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Ouvrez `/etc/fstab` dans un éditeur de texte, puis ajoutez une entrée pour le nouveau système de fichiers, par exemple :

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Ensuite, enregistrez et fermez `/etc/fstab`.

4. Vérifiez si l'entrée `/etc/fstab` est correcte :

    ```bash    
    sudo mount -a
    ```

    Si cette commande génère un message d’erreur, vérifiez que la syntaxe utilisée dans le fichier `/etc/fstab` est correcte.
   
    Ensuite, exécutez la commande `mount` pour vérifier que le système de fichiers est monté :

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Facultatif) Paramètres de démarrage fiables dans `/etc/fstab`
   
    De nombreuses distributions comprennent les paramètres de montage `nobootwait` ou `nofail` pouvant être ajoutés au fichier `/etc/fstab`. Ces paramètres autorisent les échecs lors du montage d'un système de fichiers donné et permettent au système Linux de continuer à démarrer même s'il n'a pas été en mesure de monter le système de fichiers RAID. Pour plus d'informations sur ces paramètres, reportez-vous à la documentation de votre distribution.
   
    Exemple (Ubuntu) :

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Prise en charge de TRIM/UNMAP
Certains noyaux Linux prennent en charge les opérations TRIM/UNMAP pour ignorer les blocs inutilisés sur le disque. Ces opérations sont particulièrement utiles dans un stockage standard pour informer Azure que des pages supprimées ne sont plus valides et peuvent être ignorées. Le fait d’ignorer des pages peut vous permettre de réaliser des économies si vous créez des fichiers volumineux, puis les supprimez.

Il existe deux façons d’activer la prise en charge de TRIM sur votre machine virtuelle Linux. Comme d’habitude, consultez votre distribution pour connaître l’approche recommandée :

- Utilisez l’option de montage `discard` dans `/etc/fstab`, par exemple :

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Dans certains cas, l’option `discard` peut avoir un impact sur la performance. Vous pouvez également exécuter la commande `fstrim` manuellement à partir de la ligne de commande ou l’ajouter à votre crontab pour l’exécuter régulièrement :

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```



<!--HONumber=Dec16_HO1-->


