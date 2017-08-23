---
title: "Les noms d’appareil des machines virtuelles Linux sont modifiés dans Azure | Microsoft Docs"
description: "Explique la raison pour laquelle les noms des appareils sont modifiés et fournit des solutions pour résoudre ce problème."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>Résolution des problèmes : les noms d’appareil des machines virtuelles Linux sont modifiés

Cet article explique pourquoi les noms des appareils sont modifiés après le redémarrage d’une machine virtuelle Linux, ou après le rattachement des disques. Il fournit également la solution à ce problème.

## <a name="symptom"></a>Symptôme

Vous pourriez rencontrer les problèmes suivants lors de l’exécution de machines virtuelles Linux dans Microsoft Azure.

- La machine virtuelle ne parvient pas à démarrer après un redémarrage.

- Si les disques de données sont détachés et rattachés, les noms d’appareil des disques sont modifiés.

- Les applications ou un scripts faisant référence à un disque par son nom d’appareil échouent. Vous remarquez que le nom d’appareil du disque a été modifié.

## <a name="cause"></a>Cause :

Les chemins d’accès des appareils dans Linux ne sont pas toujours cohérents entre les redémarrages. Les noms d’appareil se composent de numéros principaux (lettre) et secondaires.  Lorsque le pilote du périphérique de stockage Linux détecte un nouvel appareil, il lui attribue un numéro de périphérique principal et secondaire à partir de la plage disponible. En cas de retrait d’un appareil, les numéros de périphérique sont libérés pour une utilisation ultérieure.

Le problème se produit parce que l’analyse des appareils présents dans Linux, telle qu’elle est planifiée par le sous-système SCSI, est asynchrone. L’attribution du nom de chemin d’un appareil peut varier entre les redémarrages. 

## <a name="solution"></a>Solution

Pour résoudre ce problème, utilisez un dispositif d’affectation de noms persistants. Il existe quatre méthodes d’affectation de noms persistants : étiquette de système de fichiers, UUID, ID et chemin d’accès. Nous vous recommandons d’utiliser les étiquettes de système de fichiers et les UUID pour les machines virtuelles Linux Azure. 

La plupart des distributions fournissent également les options fstab **nofail** ou **nobootwait**. Ces options permettent à un système de démarrer même si le disque n’est pas monté au moment du démarrage. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution. Pour savoir comment configurer une machine virtuelle Linux pour qu’elle utiliser un UUID lorsque vous ajoutez un disque de données, consultez la section [Se connecter à la machine virtuelle Linux afin de monter le nouveau disque](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Lorsque l’agent Linux Azure est installé sur une machine virtuelle, il utilise des règles Udev pour construire un ensemble de liens symboliques sous **/dev/disk/azure**. Ces règles Udev peuvent être utilisées par les applications et scripts pour identifier les disques attachés à la machine virtuelle, leur type et leur numéro d’unité logique.

## <a name="more-information"></a>Plus d’informations

### <a name="identify-disk-luns"></a>Identifier les numéros d’unité logique des disques

Une application peut utiliser des numéros d’unité logique (LUN) pour rechercher tous les disques attachés et créer des liens symboliques. L’agent Azure Linux inclut désormais des règles Udev permettant de configurer des liens symboliques entre les LUN et les appareils, comme suit :

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

Les informations sur les LUN peuvent également être récupérées à partir de l’invité Linux à l’aide d’lsscsi ou d’un outil similaire, comme suit.

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Ces informations peuvent être utilisées avec les métadonnées d’abonnement Azure pour identifier l’emplacement du disque dur virtuel qui stocke les données de partition dans le stockage Azure. Par exemple, utilisez la CLI Azure :

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Découvrir les UUID de système de fichiers à l’aide de l’utilitaire blkid

Un script ou une application peut lire la sortie de blkid, ou des informations de sources similaires, et créer des liens symboliques dans **/dev**pour les utiliser. La sortie affiche les UUID de tous les disques attachés à la machine virtuelle et le fichier du périphérique auquel ils sont associés :

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Les règles udev waagent créent un ensemble de liens symboliques sous **/dev/disk/azure** :


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


L’application peut utiliser ces informations pour identifier l’unité de disque de démarrage et le disque de ressources (éphémère). Dans Azure, les applications doivent faire référence à **/dev/disk/azure/root-part1** ou **/dev/disk/azure-resource-part1** pour découvrir ces partitions.

S’il existe des partitions supplémentaires dans la liste blkid, elles se trouvent sur un disque de données. Les applications peuvent gérer les UUID de ces partitions et utiliser un chemin comme celui-ci pour découvrir le nom de l’appareil lors de l’exécution :

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Obtenir les dernières règles de stockage Azure

Pour obtenir les dernières règles de stockage Azure, exécutez les commandes suivantes :

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


Pour plus d’informations, consultez les articles suivants :

- [Ubuntu: Using UUID](https://help.ubuntu.com/community/UsingUUID) (Utilisation des UUID)

- [Red Hat: Persistent Naming](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html) (Attribution de noms persistants)

- [Linux: What UUIDs can do for you](https://www.linux.com/news/what-uuids-can-do-you) (Ce que les UUID peuvent faire pour vous)

- [Udev: Introduction to Device Management In Modern Linux System](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) (Présentation de la gestion des périphériques dans les systèmes Linux modernes)


