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
ms.sourcegitcommit: 5c31fcac727a1206cb416cbe7340392a2816729a
ms.openlocfilehash: 93b689c132ec7e391a9107e4e3eb73a48e3ba09d


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure (Aperçu)

La sauvegarde Azure offre la possibilité de restaurer des [machines virtuelles et des disques Azure](./backup-azure-arm-restore-vms.md) à partir de sauvegardes de machines virtuelles Azure. Cet article explique comment récupérer des éléments tels que des fichiers et des dossiers à partir d’une sauvegarde de machine virtuelle Azure.

> [!Note]
> Cette fonctionnalité est disponible pour les machines virtuelles Azure déployées à l’aide du modèle Resource Manager et protégées dans un coffre Recovery Services.
> La récupération de fichiers à partir d’une sauvegarde de machine virtuelle Windows Azure est actuellement en version préliminaire. La récupération de fichiers à partir d’une machine virtuelle Linux Azure sera bientôt disponible.
La récupération de fichiers à partir d’une sauvegarde de machine virtuelle chiffrée n’est actuellement pas prise en charge.
>

## <a name="mount-the-volume-and-copy-files"></a>Monter le volume et copier les fichiers

1. Connectez-vous au [portail Azure](http://portal.Azure.com). Recherchez le coffre Recovery Services approprié et l’élément de sauvegarde requis.

2. Dans le panneau Élément de sauvegarde, cliquez sur **Récupération de fichier (aperçu)**.

    ![Ouvrir l’élément de sauvegarde du coffre Recovery Services.](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    Le panneau **Récupération de fichier** s’affiche.

    ![Panneau Récupération de fichier](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. Dans le menu déroulant **Sélectionner le point de récupération**, sélectionnez le point de récupération qui contient les fichiers dont vous avez besoin. Le dernier point de récupération est sélectionné par défaut.

4. Cliquez sur **Download Executable** (Télécharger le fichier exécutable) pour télécharger le logiciel que vous utiliserez pour copier des fichiers à partir du point de récupération.

  Le fichier exécutable est un script qui crée une connexion entre l’ordinateur local et le point de récupération spécifié.

5. Sur l’ordinateur sur lequel vous souhaitez récupérer les fichiers, exécutez le fichier exécutable. Vous devez exécuter le script en tant qu’administrateur. Si vous exécutez le script sur un ordinateur avec un accès restreint, vérifiez l’accès aux éléments suivants :

    - go.microsoft.com
    - points de terminaison Azure utilisés pour les sauvegardes de machines virtuelles Azure
    - port sortant 3260

    ![Panneau Récupération de fichier](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Vous pouvez exécuter le script sur n’importe quel ordinateur doté d’un système d’exploitation identique (ou compatible) par rapport à celui de l’ordinateur utilisé pour générer le point de récupération. Consultez le [tableau de compatibilité des systèmes d’exploitation](backup-azure-restore-files-from-vm.md#compatible-os) pour connaître les systèmes d’exploitation compatibles. Si la machine virtuelle Azure protégée utilise des espaces de stockage Windows, vous ne pouvez pas exécuter le script exécutable sur cette machine virtuelle. Au lieu de cela, exécutez le script exécutable sur un autre ordinateur qui utilise des espaces de stockage Windows. Il est recommandé d’exécuter le script exécutable sur un ordinateur utilisant un système d’exploitation compatible.

    ![Panneau Récupération de fichier](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

### <a name="compatible-os"></a>Systèmes d’exploitation compatibles

Le tableau suivant indique la compatibilité entre les systèmes d’exploitation des serveurs et des ordinateurs. Lors de la récupération de fichiers, vous ne pouvez pas restaurer des fichiers entre des systèmes d’exploitation incompatibles.

|Système d’exploitation serveur | Système d’exploitation client compatible  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |


### <a name="drive-letter-assignments"></a>Affectations de lettres de lecteur

Lorsque vous exécutez le script, le système d’exploitation monte les nouveaux volumes et attribue des lettres de lecteur. Vous pouvez utiliser l’Explorateur Windows ou l’Explorateur de fichiers pour parcourir ces lecteurs. Les lettres de lecteur affectées aux volumes peuvent ne pas être les mêmes que celles de la machine virtuelle d’origine. Le nom de volume est cependant conservé. Par exemple, si le volume sur la machine virtuelle d’origine était « Disque de données (E:\) », ce volume peut être connecté en tant que « Disque de données (n’importe quelle lettre de lecteur disponible:\) » sur l’ordinateur local. Parcourez tous les volumes mentionnés dans la sortie du script jusqu’à trouver vos fichiers/votre dossier.  


## <a name="closing-the-connection"></a>Fermeture de la connexion

Après avoir identifié les fichiers et les avoir copiés dans un emplacement de stockage local, supprimez (ou démontez) les lecteurs supplémentaires. Pour démonter les lecteurs, dans le panneau **Récupération de fichier** du portail Azure, cliquez sur **Unmount Disks** (Démonter les disques).

![Démonter les disques](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Une fois les disques démontés, vous recevez un message vous informant que l’opération a bien été effectuée. L’actualisation de la connexion de sorte que vous pouvez supprimer les disques peut prendre quelques minutes.

## <a name="windows-storage-spaces"></a>Espaces de stockage Windows

Les espaces de stockage Windows sont une technologie de stockage de Windows qui vous permet de virtualiser le stockage. Grâce aux espaces de stockage Windows, vous pouvez regrouper les disques standard dans des pools de stockage, puis créer des disques virtuels, appelés espaces de stockage, à partir de l’espace disponible dans ces pools de stockage.

Si la machine virtuelle Azure sauvegardée utilise des espaces de stockage Windows, vous ne pouvez pas exécuter le script exécutable sur cette machine virtuelle. Au lieu de cela, exécutez le script exécutable sur un autre ordinateur qui utilise des espaces de stockage Windows. Il est recommandé d’exécuter le script exécutable sur un ordinateur utilisant un système d’exploitation compatible.

## <a name="troubleshooting"></a>résolution des problèmes

Si vous rencontrez des problèmes lors de la récupération de fichiers à partir de machines virtuelles, consultez le tableau suivant pour plus d’informations.

| Message d’erreur/Scénario | Cause probable | Action recommandée |
| ------------------------ | -------------- | ------------------ |
| Sortie du script exécutable : *Exception lors de la connexion à la cible* |Le script n’est pas en mesure d’accéder au point de récupération.    | Vérifiez si l’ordinateur remplit les conditions d’accès mentionnées ci-dessus.|  
|    Sortie du script exécutable : *La cible a déjà été connectée via une session iSCSI.* |    Le script a déjà été exécuté sur le même ordinateur et les lecteurs ont été connectés. |    Les volumes du point de récupération ont déjà été connectés. Ils NE peuvent PAS être montés avec les mêmes lettres de lecteur que celles de la machine virtuelle d’origine. Parcourez tous les volumes disponibles dans l’Explorateur de fichiers pour votre fichier. |
| Sortie du script exécutable : *Ce script n’est pas valide, car les disques ont été démontés via le portail/ont dépassé la limite de 12 h. Téléchargez un nouveau script à partir du portail.* |    Les disques ont été démontés à partir du portail ou la limite de 12 h a été dépassée. |    Ce fichier exécutable en particulier n’est plus valide et ne peut pas être exécuté. Si vous souhaitez accéder aux fichiers de ce point de récupération, visitez le portail pour un obtenir un nouveau fichier exécutable.|
| Sur l’ordinateur sur lequel est exécuté le fichier .exe : les nouveaux volumes ne sont pas démontés après avoir cliqué sur le bouton Démonter |    L’initiateur iSCSI de l’ordinateur ne répond pas/actualise sa connexion à la cible et met à jour le cache. |    Attendez quelques minutes après avoir cliqué sur le bouton Démonter. Si les nouveaux volumes ne sont toujours pas démontés, parcourez tous les volumes. Cela force l’initiateur à actualiser la connexion et le volume est démonté avec un message d’erreur indiquant que le disque n’est pas disponible.|
| Sortie du script exécutable : Le script est exécuté avec succès, mais le message « New volumes attached » (Nouveaux volumes connectés) ne s’affiche pas dans la sortie du script |    Il s’agit d’une erreur temporaire.    | Les volumes seraient déjà connectés. Ouvrez l’Explorateur pour parcourir les volumes. Si vous utilisez toujours le même ordinateur pour exécuter les scripts, essayez de le redémarrer et la liste devrait s’afficher lors des prochaines exécutions du fichier exécutable. |



<!--HONumber=Feb17_HO1-->


