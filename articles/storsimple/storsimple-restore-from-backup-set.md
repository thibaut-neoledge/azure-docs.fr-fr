---
title: "Restauration d’un volume StorSimple à partir d’une sauvegarde | Microsoft Docs"
description: "Explique comment utiliser la page Catalogue de sauvegarde du service StorSimple Manager pour restaurer un volume StorSimple à partir d’un jeu de sauvegarde."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 12484338f5b4d489604d70a657ef0992b6267297
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restauration d’un volume StorSimple à partir d’un jeu de sauvegarde
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Vue d'ensemble
La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors de sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer des sauvegardes, ou utiliser une sauvegarde pour restaurer ou cloner un volume.

 ![Page Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Ce didacticiel explique comment utiliser la page **Catalogue de sauvegarde** pour restaurer un volume sur l’appareil à partir d’un jeu de sauvegarde.

## <a name="how-to-use-the-backup-catalog"></a>Utilisation du catalogue de sauvegarde
La page **Catalogue de sauvegarde** comprend une zone de requête qui vous permet d’affiner la sélection des ensembles de sauvegarde. Vous pouvez filtrer les jeux de sauvegarde récupérés selon les paramètres suivants :

* **Appareil** : appareil sur lequel le jeu de sauvegarde a été créé.
* **Stratégie de sauvegarde** ou **volume** : stratégie de sauvegarde ou volume associé à ce jeu de sauvegarde.
* **De** et **À** : plage de dates et d’heures de création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite affichés sous forme de tableau sur la base des attributs suivants :

* **Nom** : nom de la stratégie de sauvegarde ou du volume associé à ce jeu de sauvegarde.
* **Taille** : taille réelle du jeu de sauvegarde.
* **Créé le** : date et heure auxquelles les sauvegardes ont été créées. 
* **Type** : les jeux de sauvegarde peuvent être des instantanés locaux ou des instantanés cloud. Un instantané local est une sauvegarde de toutes les données de volume stockées localement sur l’appareil, tandis qu’un instantané cloud correspond à la sauvegarde des données de volume résidant dans le cloud. Les instantanés locaux offrent un accès plus rapide, alors que les instantanés cloud sont choisis pour la résilience des données.
* **Initié par** : les sauvegardes peuvent être lancées automatiquement suivant une planification ou manuellement par un utilisateur. (Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Vous pouvez également utiliser l’option **Effectuer une sauvegarde** pour effectuer une sauvegarde interactive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Comment restaurer votre volume StorSimple à partir d’une sauvegarde
Vous pouvez utiliser la page **Catalogue de sauvegarde** pour restaurer votre volume StorSimple à partir d’une sauvegarde spécifique. 

> [!WARNING]
> La restauration à partir d’une sauvegarde remplace les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte des données qui ont été écrites après la sauvegarde.
> 
> 

Avant de lancer la restauration d’un volume, assurez-vous que celui-ci est hors connexion. Vous devrez mettre le volume hors connexion sur l’ordinateur hôte en premier, puis sur l’appareil. Suivez les étapes de la [Mise hors connexion d’un volume](storsimple-manage-volumes.md#take-a-volume-offline). Procédez comme suit pour restaurer un volume à partir d’un jeu de sauvegarde.

### <a name="to-restore-from-a-backup-set"></a>Pour restaurer à partir d’un jeu de sauvegarde
1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde** .
   
    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. Sélectionnez un jeu de sauvegarde comme suit :
   
   1. Sélectionnez l’appareil approprié.
   2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde à sélectionner.
   3. Indiquez l’intervalle de temps.
   4. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) pour exécuter cette requête.
      
      Les sauvegardes associées au volume ou à la stratégie de sauvegarde sélectionné doivent figurer dans la liste des jeux de sauvegarde.
3. Développez le jeu de sauvegarde pour afficher les volumes associés. Ces volumes doivent être mis hors connexion sur l’hôte et l’appareil avant leur restauration. Suivez les étapes de la [Mise hors connexion d’un volume](storsimple-manage-volumes.md#take-a-volume-offline).
   
   > [!IMPORTANT]
   > Veillez à mettre les volumes hors connexion sur l’ordinateur hôte avant de les mettre hors connexion sur l’appareil. Sans quoi, vous vous exposez à un risque d’altération des données.
   > 
   > 
4. Sélectionnez un jeu de sauvegarde. Cliquez sur **Restaurer** en bas de la page.
5. Vous êtes invité à confirmer l’opération. 
   
    ![Page Confirmation](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. Passez en revue les informations de restauration, puis cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Cette opération lance une tâche de restauration que vous pouvez afficher en accédant à la page **Tâches** . 
7. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes a été remplacé par les volumes provenant de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo expliquant comment utiliser les fonctionnalités de clonage et de restauration dans StorSimple pour récupérer des fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [gérer des volumes StorSimple](storsimple-manage-volumes.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

