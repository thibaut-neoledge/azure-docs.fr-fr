---
title: "Sauvegarde Azure : récupérer des fichiers et des dossiers à partir d’une sauvegarde de machine virtuelle Azure | Microsoft Docs"
description: "Récupérer des fichiers à partir d’un point de récupération de machine virtuelle Azure"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "récupération au niveau élément ; récupération de fichiers à partir d’une sauvegarde de machine virtuelle Azure ; restaurer des fichiers à partir d’une machine virtuelle Azure"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 873c64dfbd4ad6ced9e5a9eeb80d7ad6dbc558a6
ms.lasthandoff: 03/17/2017


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure (Aperçu)

La sauvegarde Azure offre la possibilité de restaurer des [machines virtuelles et des disques Azure](./backup-azure-arm-restore-vms.md) à partir de sauvegardes de machines virtuelles Azure. Cet article explique comment récupérer des éléments tels que des fichiers et des dossiers à partir d’une sauvegarde de machine virtuelle Azure.

> [!Note]
> Cette fonctionnalité est disponible pour les machines virtuelles Azure déployées à l’aide du modèle Resource Manager et protégées dans un coffre Recovery Services.
> La récupération de fichiers à partir d’une sauvegarde de machine virtuelle chiffrée n’est pas prise en charge.
>

## <a name="mount-the-volume-and-copy-files"></a>Monter le volume et copier les fichiers

1. Connectez-vous au [portail Azure](http://portal.Azure.com). Recherchez le coffre Recovery Services approprié et l’élément de sauvegarde requis.

2. Dans le panneau Élément de sauvegarde, cliquez sur **Récupération de fichier (aperçu)**.

    ![Ouvrir l’élément de sauvegarde du coffre Recovery Services.](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    Le panneau **Récupération de fichier** s’affiche.

    ![Panneau Récupération de fichier](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. Dans le menu déroulant **Sélectionner le point de récupération**, sélectionnez le point de récupération qui contient les fichiers dont vous avez besoin. Le dernier point de récupération est sélectionné par défaut.

4. Cliquez sur **Download Executable** (Télécharger le fichier exécutable) pour une machine virtuelle Azure Windows ou **Download Script** (Télécharger le script) pour une machine virtuelle Azure Linux pour télécharger le logiciel que vous utiliserez pour copier des fichiers à partir du point de récupération.

  Le fichier exécutable/script crée une connexion entre l’ordinateur local et le point de récupération spécifié.

5. Sur l’ordinateur sur lequel vous souhaitez récupérer les fichiers, exécutez le fichier exécutable/de script. Vous devez l’exécuter en tant qu’administrateur. Si vous exécutez le script sur un ordinateur avec un accès restreint, vérifiez l’accès aux éléments suivants :

    - go.microsoft.com
    - points de terminaison Azure utilisés pour les sauvegardes de machines virtuelles Azure
    - port sortant 3260

   Pour Linux, le script requiert les composants « open-iscsi » et « lshw » pour vous connecter au point de récupération. S’ils n’existent pas sur la machine utilisée pour l’exécution, il demande l’autorisation d’installer les composants concernés et les installe avec votre accord.
      
    ![Panneau Récupération de fichier](./media/backup-azure-restore-files-from-vm/executable-output.png)
    
   
   Vous pouvez exécuter le script sur n’importe quelle machine dotée du même système d’exploitation (ou d’un système d’exploitation compatible) que la machine virtuelle sauvegardée. Consultez le [tableau de compatibilité des systèmes d’exploitation](backup-azure-restore-files-from-vm.md#compatible-os) pour connaître les systèmes d’exploitation compatibles. Si la machine virtuelle Azure protégée utilise des espaces de stockage Windows (pour les machines virtuelles Azure Windows) ou le LVM/des baies RAID (pour les machines virtuelles Linux), vous ne pouvez pas exécuter le script exécutable sur cette machine virtuelle. Au lieu de cela, exécutez-la sur n’importe quelle autre machine avec un système d’exploitation compatible.

### <a name="compatible-os"></a>Systèmes d’exploitation compatibles

#### <a name="for-windows"></a>Pour Windows

Le tableau suivant indique la compatibilité entre les systèmes d’exploitation des serveurs et des ordinateurs. Lors de la récupération de fichiers, vous ne pouvez pas restaurer des fichiers entre des systèmes d’exploitation incompatibles.

|Système d’exploitation serveur | Système d’exploitation client compatible  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Pour Linux

Sous Linux, l’exigence fondamentale est que le système d’exploitation de la machine sur laquelle le script est exécuté doit prendre en charge le système de fichiers des fichiers présents dans la machine virtuelle Linux sauvegardée. Lorsque vous sélectionnez une machine pour exécuter le script, assurez-vous qu’elle dispose d’un système d’exploitation compatible et des versions mentionnées dans le tableau ci-dessous.

|Système d’exploitation Linux | Versions  |
| --------------- | ---- |
| Ubuntu | 12.04 et versions ultérieures |
| CentOS | 6.5 et versions ultérieures  |
| RHEL | 6.7 et versions ultérieures |
| Debian | 7 et versions ultérieures |
| Oracle Linux | 6.4 et versions ultérieures |

Le script requiert également les composants python et bash pour exécuter et établir une connexion sécurisée avec le point de récupération.

|Composant | Version  |
| --------------- | ---- |
| bash | 4 et versions ultérieures |
| python | 2.6.6 et versions ultérieures  |


### <a name="identifying-volumes"></a>Identification des volumes

#### <a name="for-windows"></a>Pour Windows

Lorsque vous exécutez l’exécutable, le système d’exploitation monte les nouveaux volumes et attribue des lettres de lecteur. Vous pouvez utiliser l’Explorateur Windows ou l’Explorateur de fichiers pour parcourir ces lecteurs. Les lettres de lecteur affectées aux volumes peuvent ne pas être les mêmes que celles de la machine virtuelle d’origine. Le nom de volume est cependant conservé. Par exemple, si le volume sur la machine virtuelle d’origine était « Disque de données (E:\) », ce volume peut être connecté en tant que « Disque de données (n’importe quelle lettre de lecteur disponible:\) » sur l’ordinateur local. Parcourez tous les volumes mentionnés dans la sortie du script jusqu’à trouver vos fichiers/votre dossier.  
       
   ![Panneau Récupération de fichier](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Pour Linux

Sous Linux, les volumes de point de récupération sont montés sur le dossier dans lequel le script est exécuté. Les disques attachés, les volumes et les chemins d’accès de montage correspondants sont affichés en conséquence. Ces chemins de montage sont visibles aux utilisateurs ayant un accès root. Parcourez les volumes mentionnés dans la sortie du script.

  ![Panneau Récupération de fichier Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Fermeture de la connexion

Après avoir identifié les fichiers et les avoir copiés dans un emplacement de stockage local, supprimez (ou démontez) les lecteurs supplémentaires. Pour démonter les lecteurs, dans le panneau **Récupération de fichier** du portail Azure, cliquez sur **Unmount Disks** (Démonter les disques).

![Démonter les disques](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Une fois les disques démontés, vous recevez un message vous informant que l’opération a bien été effectuée. L’actualisation de la connexion de sorte que vous pouvez supprimer les disques peut prendre quelques minutes.

Sous Linux, une fois la connexion au point de récupération interrompue, le système d’exploitation ne supprime pas les chemins d’accès de montage correspondants automatiquement. Ils existent en tant que volumes « orphelins » et ils sont visibles, mais génèrent une erreur lorsque vous accédez aux fichiers ou écrivez dessus. Ils peuvent être supprimés manuellement. Le script, lorsqu’il est exécuté, identifie de tels volumes existants à partir des points de récupération précédents et les nettoie après accord.

## <a name="special-configurations"></a>Configurations spéciales

### <a name="windows-storage-spaces"></a>Espaces de stockage Windows

Les espaces de stockage Windows sont une technologie de stockage de Windows qui vous permet de virtualiser le stockage. Grâce aux espaces de stockage Windows, vous pouvez regrouper les disques standard dans des pools de stockage, puis créer des disques virtuels, appelés espaces de stockage, à partir de l’espace disponible dans ces pools de stockage.

Si la machine virtuelle Azure sauvegardée utilise des espaces de stockage Windows, vous ne pouvez pas exécuter le script exécutable sur la même machine virtuelle. Au lieu de cela, exécutez le script exécutable sur n’importe quelle autre machine avec un système d’exploitation compatible.

### <a name="lvmraid-arrays"></a>Baies LVM/RAID

Sous Linux, le gestionnaire de volumes logiques (LVM) et/ou les baies RAID logicielles permettent de gérer des volumes logiques sur plusieurs disques. Si la machine virtuelle Linux sauvegardée utilise LVM et/ou des baies RAID, vous ne pouvez pas exécuter le script sur la même machine virtuelle. Exécutez plutôt le script sur une autre machine avec un système d’exploitation compatible et qui prend en charge le système de fichiers de la machine virtuelle sauvegardée.

La sortie du script affiche les disques LVM et/ou des baies RAID et les volumes avec le type de partition, comme indiqué ci-dessous

   ![Panneau de sortie de LVM Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Les commandes suivantes doivent être exécutées par l’utilisateur pour mettre en ligne ces partitions. 

**Pour les partitions LVM**

```
$ pvs <volume name as shown above in the script output> 
```
Répertorie les noms de groupes de volumes sous un volume physique.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```
Cela répertorie tous les volumes logiques, noms et chemins d’accès dans un groupe de volumes.

```
$ mount <LV path> </mountpath>
```
Pour monter les volumes logiques sur le chemin d’accès de votre choix.


**Pour les tableaux RAID**

```
$ mdadm –detail –scan
```
Cela affiche des détails sur tous les disques RAID. Le disque RAID pertinent s’affichera en tant que `/dev/mdm/<RAID array name in the backed up VM>`

Utilisez la commande mount si le disque RAID dispose de volumes physiques.
```
$ mount [RAID Disk Path] [/mounthpath]
```

Si ce disque RAID a un autre LVM configuré, suivez la même procédure que ci-dessus pour les partitions LVM dont le nom de volume est le nom de disque RAID

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la récupération de fichiers à partir de machines virtuelles, consultez le tableau suivant pour plus d’informations.

| Message d’erreur/Scénario | Cause probable | Action recommandée |
| ------------------------ | -------------- | ------------------ |
| Sortie du script exécutable : *Exception lors de la connexion à la cible* |Le script n’est pas en mesure d’accéder au point de récupération.    | Vérifiez si l’ordinateur remplit les conditions d’accès mentionnées ci-dessus.|  
|    Sortie du script exécutable : *La cible a déjà été connectée via une session iSCSI.* |    Le script a déjà été exécuté sur le même ordinateur et les lecteurs ont été connectés. |    Les volumes du point de récupération ont déjà été connectés. Ils NE peuvent PAS être montés avec les mêmes lettres de lecteur que celles de la machine virtuelle d’origine. Parcourez tous les volumes disponibles dans l’Explorateur de fichiers pour votre fichier. |
| Sortie du script exécutable : *Ce script n’est pas valide, car les disques ont été démontés via le portail/ont dépassé la limite de 12 h. Téléchargez un nouveau script à partir du portail.* |    Les disques ont été démontés à partir du portail ou la limite de 12 h a été dépassée |    Ce fichier exécutable en particulier n’est plus valide et ne peut pas être exécuté. Si vous souhaitez accéder aux fichiers de ce point de récupération, visitez le portail pour un obtenir un nouveau fichier exécutable.|
| Sur l’ordinateur sur lequel est exécuté le fichier .exe : les nouveaux volumes ne sont pas démontés après avoir cliqué sur le bouton Démonter |    L’initiateur iSCSI de l’ordinateur ne répond pas/actualise sa connexion à la cible et met à jour le cache. |    Attendez quelques minutes après avoir cliqué sur le bouton Démonter. Si les nouveaux volumes ne sont toujours pas démontés, parcourez tous les volumes. Cela force l’initiateur à actualiser la connexion et le volume est démonté avec un message d’erreur indiquant que le disque n’est pas disponible.|
| Sortie du script exécutable : Le script est exécuté avec succès, mais le message « New volumes attached » (Nouveaux volumes connectés) ne s’affiche pas dans la sortie du script |    Il s’agit d’une erreur temporaire.    | Les volumes seraient déjà connectés. Ouvrez l’Explorateur pour parcourir les volumes. Si vous utilisez toujours le même ordinateur pour exécuter les scripts, essayez de le redémarrer et la liste devrait s’afficher lors des prochaines exécutions du fichier exécutable. |
| Pour Linux : impossible d’afficher les volumes souhaités | Le système d’exploitation de la machine sur laquelle est exécuté le script peut ne pas reconnaître le système de fichiers sous-jacent de la machine virtuelle sauvegardée | Vérifiez si le point de récupération est cohérent en cas d’incident ou cohérent avec les fichiers. S’il est cohérent avec les fichiers, exécutez le script sur une autre machine dont le système d’exploitation reconnaît le système de fichiers de la machine virtuelle sauvegardée |

