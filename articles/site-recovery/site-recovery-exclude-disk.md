---
title: "Exclure un disque de la protection à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Décrit pourquoi et comment exclure un ou plusieurs disques de machine virtuelle de la réplication pour les scénarios VMware vers Azure et Hyper-V vers Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0
ms.lasthandoff: 02/17/2017


---
#<a name="exclude-disk-from-replication"></a>Exclure un disque de la réplication
Cet article décrit comment exclure un ou plusieurs disques de la réplication pour optimiser la bande passante de réplication consommée ou les ressources côté cible utilisées par ces disques. Cette fonctionnalité est prise en charge pour les scénarios VMware vers Azure et Hyper-V vers Azure.

##<a name="prerequisites"></a>Composants requis

Par défaut, tous les disques d’une machine sont répliqués. Pour exclure un disque de la réplication, vous devez installer le service Mobilité manuellement sur la machine avant d’activer la réplication si vous procédez à une réplication de **VMware vers Azure**.


## <a name="why-exclude-disks-from-replication"></a>Pourquoi exclure des disques de la réplication ?
L’exclusion de disques de la réplication se révèle souvent nécessaire pour les raisons suivantes :

1. Les données hautement évolutives sur le disque exclu ne sont pas importantes ou n’ont pas besoin d’être répliquées.

2. Le fait de ne pas répliquer ces données fréquemment évolutives permet d’économiser des ressources de stockage et réseau.

##<a name="what-are-the-typical-scenarios"></a>Quels sont les scénarios classiques ?
Il existe quelques exemples spécifiques de données hautement évolutives qui sont aisément identifiables et particulièrement susceptibles d’être exclues, par exemple les écritures de fichiers d’échange, les écritures tempdb de Microsoft SQL Server, etc. Selon la charge de travail et le sous-système de stockage, le fichier d’échange peut enregistrer une quantité significative de données à fort taux d’évolution. Toutefois, la réplication de ces données du site principal vers Azure nécessiterait de nombreuses ressources. Par conséquent, la réplication d’une machine virtuelle dotée d’un seul disque virtuel contenant à la fois le système d’exploitation et le fichier d’échange peut être optimisée à l’aide des opérations suivantes :

1. Fractionnement du disque virtuel en deux disques distincts, l’un stockant le système d’exploitation et l’autre contenant le fichier d’échange
2. Exclusion du disque de fichier d’échange de la réplication

De la même façon, lorsque Microsoft SQL Server présente un fichier de base de données système et une base de données tempdb sur le même disque, il est possible d’optimiser la réplication en effectuant les opérations suivantes :

1. Stockage de la base de données système et de la base de données tempdb sur deux disques différents
2. Exclusion du disque de base de données tempdb de la réplication

##<a name="how-to-exclude-disk-from-replication"></a>Comment exclure un disque de la réplication ?

###<a name="vmware-to-azure"></a>VMware vers Azure
Pour protéger une machine virtuelle du portail Azure Site Recovery, suivez le workflow [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). La 4e étape de la procédure Activer la réplication comporte une colonne **DISQUES À RÉPLIQUER** qui permet d’exclure un disque de la réplication. Par défaut, tous les disques sont sélectionnés. Désélectionnez le disque que vous souhaitez exclure de la réplication, puis exécutez la procédure d’activation de la réplication. 

![Activer la réplication](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * Vous pouvez exclure uniquement les disques sur lesquels le service Mobilité est déjà installé. Vous devez installer le service Mobilité manuellement, car il n’est installé qu’à l’aide du mécanisme Push après l’activation de la réplication.
> * Seuls les disques de base peuvent être exclus de la réplication. Vous ne pouvez pas exclure de système d’exploitation ni de disque dynamique.
> * Une fois la réplication activée, vous ne pouvez pas ajouter ni supprimer de disques pour la réplication. Si vous voulez ajouter ou exclure un disque, vous devez désactiver la protection de la machine, puis la réactiver.
> * Si vous excluez un disque requis pour le bon fonctionnement d’une application, après le basculement vers Azure, vous devrez le créer manuellement dans Azure afin que l’application répliquée puisse s’exécuter. Vous pouvez également intégrer Azure Automation dans un plan de récupération afin de créer le disque pendant le basculement de la machine.
> * Machine virtuelle Windows : les disques que vous créez manuellement dans Azure ne sont pas restaurés automatiquement. Par exemple, si vous basculez trois disques et que vous en créez deux directement dans une machine virtuelle Azure, seuls les trois disques qui ont été basculés sont restaurés automatiquement. Vous ne pouvez pas inclure de disques créés manuellement dans le processus de restauration automatique ou de reprotection à partir de l’hôte local vers Azure.
> * Machine virtuelle Linux : les disques que vous créez manuellement dans Azure sont restaurés automatiquement. Par exemple, si vous basculez trois disques et créez deux disques directement dans Azure, les cinq disques seront restaurés. Vous ne pouvez pas exclure de disques créés manuellement dans la restauration automatique.
> 

###<a name="hyper-v-to-azure"></a>Hyper-V vers Azure
Pour protéger une machine virtuelle du portail Azure Site Recovery, suivez le workflow [Activer la réplication](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication). La 4e étape de la procédure Activer la réplication comporte une colonne **DISQUES À RÉPLIQUER** qui permet d’exclure des disques de la réplication. Par défaut, tous les disques sont sélectionnés pour la réplication. Désélectionnez le disque que vous souhaitez exclure de la réplication, puis exécutez la procédure d’activation de la réplication. 

![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * Seuls les disques de base peuvent être exclus de la réplication. Vous ne pouvez pas exclure le disque du système d’exploitation, et il est déconseillé d’exclure les disques dynamiques. ASR ne peut pas identifier quel disque du disque dur virtuel est un disque de base ou un disque dynamique au sein de la machine virtuelle invitée.  Si tous les disques de volume dynamique dépendants ne sont pas exclus, un disque dynamique protégé apparaît comme un disque défectueux sur la machine virtuelle de basculement, et les données de ce disque ne sont pas accessibles.    
> * Une fois la réplication activée, vous ne pouvez pas ajouter ni supprimer de disques pour la réplication. Si vous voulez ajouter ou exclure un disque, vous devez désactiver la protection de la machine virtuelle, puis la réactiver.
> * Si vous excluez un disque requis pour le bon fonctionnement d’une application, après le basculement vers Azure, vous devez le créer manuellement dans Azure afin que l’application répliquée puisse s’exécuter. Vous pouvez également intégrer Azure Automation dans un plan de récupération afin de créer le disque pendant le basculement de la machine.
> * Les disques que vous créez manuellement dans Azure ne seront pas restaurés automatiquement. Par exemple, si vous basculez trois disques et que vous en créez deux directement dans une machine virtuelle Azure, seuls les trois disques qui ont été basculés seront restaurés automatiquement à partir d’Azure sur Hyper-V. Vous ne pouvez pas inclure de disques créés manuellement dans le processus de restauration automatique ou de réplication inverse d’Hyper-V vers Azure.
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>Scénarios d’exclusion de disques de bout en bout
Pour vous aider à bien comprendre la fonctionnalité d’exclusion de disques, considérons deux scénarios.

1. Disque de base de données tempdb SQL Server
2. Disque de fichier d’échange

###<a name="excluding-the-sql-server-tempdb-disk"></a>Exclusion du disque de base de données tempdb SQL Server
Considérons l’exemple d’une machine virtuelle SQL Server dotée d’un disque de base de données tempdb pouvant être exclu.

Nom de la machine virtuelle : SalesDB. Les disques sur la machine virtuelle source sont les suivants :


**Nom du disque** | **N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disque de système d’exploitation
DB-Disk1| Disk1 | D:\ | Base de données système SQL et base de données utilisateur&1;
DB-Disk2 (disque exclu de la protection) | Disk2 | E:\ | Fichiers temporaires
DB-Disk3 (disque exclu de la protection) | Disk3 | F:\ | Base de données tempdb SQL (chemin du dossier (F:\MSSQL\Data\) --> notez le chemin du dossier avant de procéder au basculement
DB-Disk4 | Disk4 |G:\ |Base de données utilisateur&2;

Étant donné que les données évolutives stockées sur deux disques de la machine virtuelle sont temporaires par nature, lorsque vous protégez la machine virtuelle SalesDB, excluez les disques « Disk2 » et « Disk3 » de la réplication. Azure Site Recovery ne répliquera pas ces disques, et lors du basculement, ces disques ne seront pas présents sur la machine virtuelle de basculement sur Azure

Disques sur la machine virtuelle Azure après le basculement :

**N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | ---
DISK0 |    C:\ | Disque de système d’exploitation
Disk1 |    E:\ | Stockage temporaire [Azure ajoute ce disque et lui attribue la première lettre de lecteur disponible]
Disk2 | D:\ | Base de données système SQL et base de données utilisateur&1;
Disk3 | G:\ | Base de données utilisateur&2;

Étant donné que les disques Disk2 et Disk3 ont été exclus de la machine virtuelle SalesDB, E: est la première lettre de lecteur disponible dans la liste. Azure attribue donc la lettre E: au volume de stockage temporaire. Pour tous les disques répliqués, la lettre de lecteur reste la même.

Le disque Disk3 qui était un disque de base de données tempdb SQL (chemin du dossier de tempdb F:\MSSQL\Data\) ayant été exclu de la réplication, ce disque n’est pas disponible sur la machine virtuelle de basculement. Par conséquent, le service SQL présente l’état arrêté et a besoin du chemin F:\MSSQL\Data.

Vous pouvez créer ce chemin de deux manières.

1. Ajoutez un nouveau disque et attribuez-lui le chemin du dossier de tempdb.
2. Utilisez un disque de stockage temporaire existant pour le chemin du dossier de tempdb.

####<a name="add-a-new-disk"></a>Ajouter un nouveau disque :

1. Notez le chemin des fichiers SQL tempdb.mdf et tempdb.ldf avant de procéder au basculement.
2. À partir du Portail Azure, ajoutez à la machine virtuelle de basculement un nouveau disque présentant une taille identique ou supérieure à celle du disque de base de données tempdb SQL source (Disk3).
3. Connectez-vous à la machine virtuelle Azure. À partir de la console de gestion des disques (diskmgmt.msc), initialisez et formatez le disque que vous venez d’ajouter.
4. Attribuez-lui la lettre de lecteur qui était utilisée par le disque de base de données tempdb SQL (F:).
5. Créez le dossier de tempdb sur le volume F: (F:\MSSQL\Data).
6. Démarrez le service SQL à partir de la console de service.

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>Utiliser un disque de stockage temporaire existant pour le chemin du dossier de la base de données tempdb SQL :

1. Ouvrez une console de ligne de commande.
2. Exécutez SQL Server en mode de récupération à partir de la console de ligne de commande.

        Net start MSSQLSERVER /f / T3608

3. Exécutez la commande sqlcmd pour remplacer le chemin de la base de données tempdb par un nouveau chemin.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;        
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');        
        GO


4. Arrêtez le service Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Démarrez le service Microsoft SQL Server.

        Net start MSSQLSERVER

Consultez les recommandations Azure ci-après concernant le disque de stockage temporaire :

* Using SSDs in Azure VMs to store SQL Server TempDB and Buffer Pool Extensions (Utilisation des disques SSD dans les machines virtuelles Azure pour stocker TempDB et les extensions des pools de mémoires tampons de SQL Server)
* Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure

###<a name="failback-from-azure-to-on-premises"></a>Restauration automatique (d’Azure vers l’hôte local)
À présent, découvrons la façon dont tous les disques sont répliqués lorsque vous procédez à un basculement d’Azure vers votre hôte Hyper-V ou VMware local. Les disques que vous créez manuellement dans Azure ne seront pas répliqués. Par exemple, si vous basculez trois disques et que vous en créez deux directement dans une machine virtuelle Azure, seuls les trois disques qui ont été basculés seront restaurés automatiquement. Vous ne pouvez pas inclure de disques créés manuellement dans le processus de restauration automatique ou de reprotection à partir de l’hôte local vers Azure. En outre, le disque de stockage temporaire n’est pas répliqué au niveau local.

####<a name="failback-to-original-location-recovery-olr"></a>Restauration automatique pour la récupération à l’emplacement d’origine (OLR)

Configuration des disques de la machine virtuelle Azure dans l’exemple ci-dessus :

**N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque** 
--- | --- | --- 
DISK0 | C:\ | Disque de système d’exploitation
Disk1 |    E:\ | Stockage temporaire [Azure ajoute ce disque et lui attribue la première lettre de lecteur disponible]
Disk2 |    D:\ | Base de données système SQL et base de données utilisateur&1;
Disk3 |    G:\ | Base de données utilisateur&2;


####<a name="vmware-to-azure"></a>VMware vers Azure
Lorsque la restauration automatique est effectuée à l’emplacement d’origine, la configuration des disques de la machine virtuelle de restauration automatique ne comporte aucun disque exclu. Cela signifie que les disques qui étaient exclus de VMware vers Azure ne seront pas disponibles sur la machine virtuelle de restauration automatique. 

Disques sur la machine virtuelle VMWare (emplacement d’origine) après le basculement planifié d’Azure vers l’hôte VMware local :

**N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque** 
--- | --- | --- 
DISK0 | C:\ | Disque de système d’exploitation
Disk1 |    D:\ | Base de données système SQL et base de données utilisateur&1;
Disk2 |    G:\ | Base de données utilisateur&2;

####<a name="hyper-v-to-azure"></a>Hyper-V vers Azure
Lorsque la restauration automatique est effectuée à l’emplacement d’origine, la configuration des disques de la machine virtuelle de restauration automatique reste la même que celle des disques de la machine virtuelle d’origine pour Hyper-V. Cela signifie que les disques qui étaient exclus du site Hyper-V vers Azure seront disponibles sur la machine virtuelle de restauration automatique.

Disques sur la machine virtuelle Hyper-V (emplacement d’origine) après le basculement planifié d’Azure vers l’hôte Hyper-V local :

**Nom du disque** | **N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | Disque de système d’exploitation
DB-Disk1 | Disk1 | D:\ | Base de données système SQL et base de données utilisateur&1;
DB-Disk2 (disque exclu) | Disk2 | E:\ | Fichiers temporaires
DB-Disk3 (disque exclu) | Disk3 | F:\ | Base de données tempdb SQL (chemin du dossier (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Base de données utilisateur&2;


####<a name="exclude-paging-file-disk"></a>Exclure le disque de fichier d’échange

Considérons l’exemple d’une machine virtuelle dotée d’un disque de fichier d’échange pouvant être exclu.
Il existe deux cas :

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>Cas 1 : Fichier d’échange configuré sur le lecteur D:
Configuration des disques :


**Nom du disque** | **N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disque de système d’exploitation
DB-Disk1 (disque exclu de la protection) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Données utilisateur 1
DB-Disk3 | Disk3 | F:\ | Données utilisateur 2

Paramètres du fichier d’échange sur la machine virtuelle source :

![Activer la réplication](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

Disques sur la machine virtuelle Azure après le basculement de la machine virtuelle de VMware vers Azure ou d’Hyper-V vers Azure :
**Nom du disque** | **N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disque de système d’exploitation
DB-Disk1 | Disk1 | D:\ | Stockage temporaire –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | Données utilisateur 1
DB-Disk3 | Disk3 | F:\ | Données utilisateur 2

Étant donné que le disque Disk1 (D:) a été exclu, D: est la première lettre de lecteur disponible dans la liste. Azure attribue donc la lettre D: au volume de stockage temporaire.  D: étant disponible sur la machine virtuelle Azure, le paramétrage du fichier d’échange de la machine virtuelle reste le même.

Paramètres du fichier d’échange sur la machine virtuelle Azure :

![Activer la réplication](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>Cas 2 : Fichier d’échange configuré sur tout autre lecteur (autre que le lecteur D:)

Configuration des disques de la machine virtuelle source :

**Nom du disque** | **N° de disque de SE invité** | **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disque de système d’exploitation
DB-Disk1 (disque exclu de la protection) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Données utilisateur 1
DB-Disk3 | Disk3 | F:\ | Données utilisateur 2

Paramètres du fichier d’échange sur la machine virtuelle locale :

![Activer la réplication](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Disques sur la machine virtuelle Azure après le basculement de la machine virtuelle de VMware/Hyper-V vers Azure :

**Nom du disque**| **N° de disque de SE invité**| **Lettre de lecteur** | **Type de données sur le disque**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disque de système d’exploitation
DB-Disk1 | Disk1 | D:\ | Stockage temporaire –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | Données utilisateur 1
DB-Disk3 | Disk3 | F:\ | Données utilisateur 2

Étant donné que D: est la première lettre de lecteur disponible dans la liste, Azure attribue la lettre D: au volume de stockage temporaire. Pour tous les disques répliqués, la lettre de lecteur reste la même. Le disque G: n’étant pas disponible, le système utilisera le lecteur C: pour le fichier d’échange.

Paramètres du fichier d’échange sur la machine virtuelle Azure :

![Activer la réplication](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Étapes suivantes
Une fois votre déploiement configuré et effectué, pour en savoir plus sur les différents types de basculement, [cliquez ici](site-recovery-failover.md) .

