---
title: Utiliser un stockage de sauvegarde moderne avec un Serveur de sauvegarde Azure v2 | Microsoft Docs
description: "Découvrez les nouvelles fonctionnalités du Serveur de sauvegarde Azure v2. Cet article décrit comment mettre à niveau votre installation de serveur de sauvegarde."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="add-storage-to-azure-backup-server-v2"></a>Ajouter du stockage au Serveur de sauvegarde Azure v2

Le Serveur de sauvegarde Azure v2 est fourni avec le stockage de sauvegarde moderne du Data Protection Manager de System Center 2016. Le stockage de sauvegarde moderne offre des économies de stockage de 50 %, des sauvegardes trois fois plus rapides et un stockage plus efficace. Il s’agit également d’un stockage prenant en compte la charge de travail. 

> [!NOTE]
> Pour utiliser le stockage de sauvegarde moderne, vous devez exécuter le Serveur de sauvegarde v2 sur Windows Server 2016. Si vous exécutez le Serveur de sauvegarde v2 sur une version antérieure de Windows Server, le Serveur de sauvegarde Azure ne peut pas tirer parti du stockage de sauvegarde moderne. Au lieu de cela, il protège les charges de travail comme il le fait avec le Serveur de sauvegarde v1. Pour plus d’informations, voir la [matrice protection](backup-mabs-protection-matrix.md) de la version du Serveur de sauvegarde.

## <a name="volumes-in-backup-server-v2"></a>Volumes dans le Serveur de sauvegarde v2

Le Serveur de sauvegarde v2 accepte les volumes de stockage. Lorsque vous ajoutez un volume, le Serveur de sauvegarde formate le volume au format Resilient File System (ReFS) que nécessite le stockage de sauvegarde moderne. Pour ajouter un volume et le développer ultérieurement si nécessaire, nous vous suggérons d’utiliser flux de travail suivant :

1.  Configurez le Serveur de sauvegarde v2 sur une machine virtuelle.
2.  Créez un volume sur un disque virtuel dans un pool de stockage :
    1.  Ajoutez un disque à un pool de stockage, et créez un disque virtuel avec une disposition de stockage simple.
    2.  Ajoutez des disques supplémentaires, puis étendez le disque virtuel.
    3.  Créez des volumes sur le disque virtuel.
3.  Ajoutez les volumes au Serveur de sauvegarde.
4.  Configurez un stockage prenant en compte la charge de travail.

## <a name="create-a-volume-for-modern-backup-storage"></a>Créer un volume pour le stockage de sauvegarde moderne

L’utilisation du Serveur de sauvegarde v2 avec des volumes tels qu’un stockage sur disque peut vous aider à contrôler le stockage. Un volume peut être un disque unique. Toutefois, si vous souhaitez étendre le stockage à l’avenir, créez un volume à partir d’un disque créé à l’aide d’espaces de stockage. Cela peut être utile si vous souhaitez étendre le volume du stockage de sauvegarde. Cette section présente les meilleures pratiques en matière de création de volume avec ce programme d’installation.

1. Dans le Gestionnaire de serveur, sélectionnez **Services de fichiers et de stockage** > **Volumes** > **Pools de stockage**. Sous **Disques physiques**, sélectionnez **Nouveau pool de stockage**. 

    ![Créer un pool de stockage](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Dans la zone de liste déroulante **Tâches**, sélectionnez **Nouveau disque virtuel**.

    ![Ajouter un disque virtuel](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Sélectionnez le pool de stockage, puis choisissez **Ajouter un disque physique**.

    ![Ajouter un disque physique](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Sélectionnez le disque physique, puis choisissez **Étendre le disque virtuel**.

    ![Étendre le disque virtuel](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Sélectionnez le disque virtuel, puis choisissez **Nouveau volume**.

    ![Créer un volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Dans la boîte de dialogue **sélectionner le serveur et le disque**, sélectionnez le serveur et le nouveau disque. Ensuite, sélectionnez **Suivant**.

    ![Sélectionner le serveur et le disque](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Ajouter des volumes de stockage au stockage sur disque du Serveur de sauvegarde

Pour ajouter un volume au Serveur de sauvegarde, dans le volet **Gestion**, réanalysez le stockage, puis sélectionnez **Ajouter**. La liste de tous les volumes disponibles pour ajout au stockage du Serveur de sauvegarde s’affiche. Une fois les volumes disponibles ajoutés à la liste des volumes sélectionnés, vous pouvez leur donner un nom convivial pour faciliter leur gestion. Pour formater ces volumes au format ReFS de façon à ce que le Serveur de sauvegarde puisse tirer parti des avantages du stockage de sauvegarde moderne, sélectionnez **OK**.

![Ajouter des volumes disponibles](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurer un stockage prenant en compte la charge de travail

Avec un stockage prenant en compte les charges de travail, vous pouvez sélectionner les volumes qui stockent de préférence certains genres de charges de travail. Par exemple, vous pouvez définir des volumes coûteux prenant en charge un nombre élevé d’opérations d’entrée/sortie par seconde (IOPS) pour stocker uniquement les charges de travail qui nécessitent des sauvegardes de volumes importants fréquentes. Un exemple est SQL Server avec les journaux des transactions. D’autres charges de travail sauvegardées moins fréquemment, telles que les machines virtuelles, peuvent être sauvegardées sur des volumes bon marché.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Vous pouvez configurer un stockage prenant en compte les charges de travail à l’aide de l’applet de commande PowerShell Update-DPMDiskStorage, ce qui a pour effet de mettre à jour les propriétés d’un volume dans le pool de stockage sur un serveur Data Protection Manager.

Syntaxe :

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
La capture d’écran suivante montre l’applet de commande Update-DPMDiskStorage dans la fenêtre PowerShell.

![Commande Update-DPMDiskStorage dans la fenêtre PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Les modifications apportées à l’aide de PowerShell apparaissent sur la Console Administrateur du Serveur de sauvegarde.

![Disques et volumes dans la Console Administrateur](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Étapes suivantes
Après avoir installé le Serveur de sauvegarde, apprenez à préparer votre serveur ou commencez à protéger une charge de travail.

- [Préparer les charges de travail du Serveur de sauvegarde](backup-azure-microsoft-azure-backup.md)
- [Utiliser le Serveur de sauvegarde pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md)
- [Utiliser le serveur de sauvegarde pour sauvegarder SQL Server](backup-azure-sql-mabs.md)


