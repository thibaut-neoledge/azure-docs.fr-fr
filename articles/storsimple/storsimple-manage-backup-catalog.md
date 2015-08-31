<properties 
   pageTitle="Gérer votre catalogue de sauvegarde StorSimple | Microsoft Azure"
   description="Explique comment utiliser le service StorSimple Manager pour gérer des jeux de sauvegarde pour un volume."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/13/2015"
   ms.author="v-sharos" />

# Gérer votre catalogue de sauvegarde StorSimple

## Vue d'ensemble

La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors de sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer des sauvegardes, ou utiliser une sauvegarde pour restaurer ou cloner un volume.

Ce didacticiel explique comment répertorier, sélectionner et supprimer un jeu de sauvegarde. Pour savoir comment restaurer votre appareil à partir d’une sauvegarde, accédez à [Restaurer l’appareil à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md). Pour découvrir comment cloner un volume, accédez à [Cloner un volume StorSimple](storsimple-clone-volume.md).

![Catalogue de sauvegarde](./media/storsimple-manage-backup-catalog/HCS_BackupCatalog.png)

**Figure 1 : catalogue de sauvegarde**

La page **Catalogue de sauvegarde** comprend une zone de requête pour affiner la sélection des ensembles de sauvegarde. Vous pouvez filtrer les jeux de sauvegarde récupérés selon les paramètres suivants :

- **Appareil** : appareil sur lequel le jeu de sauvegarde a été créé.

- **Stratégie de sauvegarde ou volume** : stratégie de sauvegarde ou volume associé à ce jeu de sauvegarde.

- **De et À** : plage de dates et d’heures de création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite affichés sous forme de tableau sur la base des attributs suivants :

- **Nom** : nom de la stratégie de sauvegarde ou du volume associé à ce jeu de sauvegarde.

- **Taille** : taille réelle du jeu de sauvegarde.

- **Créé le** : date et heure auxquelles les sauvegardes ont été créées.

- **Type** : les jeux de sauvegarde peuvent être des instantanés locaux ou des instantanés cloud. Un instantané local est une sauvegarde de toutes les données de volume stockées localement sur l’appareil, tandis qu’un instantané cloud correspond à la sauvegarde des données de volume résidant dans le cloud. Les instantanés locaux offrent un accès plus rapide, alors que les instantanés cloud sont choisis pour la résilience des données.

- **Initié par** : les sauvegardes peuvent être lancées automatiquement par une planification ou manuellement par un utilisateur. Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Vous pouvez également utiliser l’option **Effectuer une sauvegarde** pour effectuer une sauvegarde interactive.

## Répertorier les jeux de sauvegarde pour un volume
 
Procédez comme suit pour répertorier toutes les sauvegardes pour un volume.

#### Pour répertorier les jeux de sauvegarde

1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde**.

2. Filtrez les sélections comme suit :

    1. Sélectionnez l’appareil approprié.

    2. Dans la liste déroulante, choisissez un volume pour afficher les sauvegardes correspondantes.

    3. Indiquez l’intervalle de temps.

    4. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) pour exécuter cette requête.
 
    Les sauvegardes associées au volume sélectionné doivent figurer dans la liste des jeux de sauvegarde.

## Sélectionner un jeu de sauvegarde

Procédez comme suit pour sélectionner un jeu de sauvegarde pour un volume ou une stratégie de sauvegarde.

#### Pour sélectionner un jeu de sauvegarde

1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde**.

2. Filtrez les sélections comme suit :

    1. Sélectionnez l’appareil approprié.

    2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde à sélectionner.

    3. Indiquez l’intervalle de temps.

    4. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) pour exécuter cette requête.

    Les sauvegardes associées au volume ou à la stratégie de sauvegarde sélectionné doivent figurer dans la liste des jeux de sauvegarde.

3. Sélectionnez un jeu de sauvegarde et développez-le. Les options **Restaurer et Supprimer** apparaissent en bas de la page. Vous pouvez effectuer une de ces actions sur le jeu de sauvegarde que vous avez sélectionné.

## Supprimer un jeu de sauvegarde

Supprimez une sauvegarde quand vous ne souhaitez plus conserver les données qui lui sont associées. Procédez comme suit pour supprimer un jeu de sauvegarde.

#### Pour supprimer un jeu de sauvegarde

1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde**.

2. Filtrez les sélections comme suit :

    1. Sélectionnez l’appareil approprié.

    2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde à sélectionner.

    3. Indiquez l’intervalle de temps.

    4. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) pour exécuter cette requête.

    Les sauvegardes associées au volume ou à la stratégie de sauvegarde sélectionné doivent figurer dans la liste des jeux de sauvegarde.

3. Sélectionnez un jeu de sauvegarde et développez-le. Les options **Restaurer et Supprimer** apparaissent en bas de la page. Cliquez sur **Supprimer**.

4. Vous serez informé de la progression de la suppression et de son issue. Une fois la suppression terminée, actualisez la requête dans cette page. Le jeu de sauvegarde supprimé n’apparaîtra plus dans la liste des jeux de sauvegarde.

## Étapes suivantes

Découvrez comment utiliser le catalogue de sauvegarde pour [restaurer l’appareil à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

<!---HONumber=August15_HO8-->