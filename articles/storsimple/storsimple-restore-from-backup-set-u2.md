<properties 
   pageTitle="Restauration d’un volume StorSimple à partir d’une sauvegarde | Microsoft Azure"
   description="Explique comment utiliser la page Catalogue de sauvegarde du service StorSimple Manager pour restaurer un volume StorSimple à partir d’un jeu de sauvegarde."
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
   ms.date="12/14/2015"
   ms.author="v-sharos" />

# Restauration d’un volume StorSimple à partir d’un jeu de sauvegarde (Mise à jour 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## Vue d’ensemble

La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors de sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer des sauvegardes, ou utiliser une sauvegarde pour restaurer ou cloner un volume.

 ![Page Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/HCS_BackupCatalog.png)

Ce didacticiel explique comment utiliser la page **Catalogue de sauvegarde** pour restaurer l’appareil à partir d’un jeu de sauvegarde.

Vous pouvez restaurer un volume à partir d’une sauvegarde locale ou sur le cloud. Dans les deux cas, l’opération de restauration met le volume en ligne immédiatement pendant le téléchargement des données en arrière-plan.

Avant de lancer une opération de restauration, prenez connaissance des éléments suivants :

- **Vous devez mettre le volume hors connexion** – Placez le volume hors connexion sur l’hôte et sur l’appareil avant de lancer une opération de restauration. Bien que l’opération de restauration mette automatiquement le volume en ligne sur l’appareil, vous devez le faire manuellement sur l’hôte. Vous pouvez mettre le volume en ligne sur l’hôte dès que le volume est en ligne sur l’appareil. (Il est inutile d’attendre la fin de l’opération de restauration.) Pour connaître les procédures, consultez [Mise hors connexion d’un volume](storsimple-manage-volumes.md#take-a-volume-offline)

- **Type de volume après restauration** – Les volumes supprimés sont restaurés en fonction du type dans l’instantané. Ainsi, les volumes qui étaient épinglés localement sont restaurés comme tels et les volumes à plusieurs niveaux de même.

    Pour les volumes existants, le type de volume selon l’utilisation actuelle remplace le type stocké dans l’instantané. Par exemple, si vous restaurez un volume à partir d’un instantané qui a été pris lorsque le type de volume était à plusieurs niveaux et que celui-ci est désormais épinglé localement (à la suite d’une opération de conversion), le volume sera restauré comme volume épinglé localement. De même, si un volume épinglé localement existant a été étendu puis restauré à partir d’un instantané précédent pris lorsque le volume était plus petit, le volume restauré conserve la taille étendue actuelle.

    Vous ne pouvez pas convertir un volume à plusieurs niveaux en un volume épinglé localement, ou un volume épinglé localement en un volume à plusieurs niveaux pendant sa restauration. Vous devez attendre la fin de l’opération de restauration avant de convertir le volume en un autre type. Pour plus d’informations sur la conversion d’un volume, consultez la page [Changer de type de volume](storsimple-manage-volumes-u2.md#change-the-volume-type).

- **La taille du volume est reflétée dans le volume restauré** – Il s’agit d’un élément important si vous restaurez un volume épinglé localement qui a été supprimé (car les volumes épinglés localement sont complètement configurés). Vérifiez que vous disposez d’un espace suffisant avant d’essayer de restaurer un volume épinglé localement qui a été supprimé.

- **Vous ne pouvez pas étendre un volume pendant sa restauration** – Attendez la fin de l’opération de restauration avant d’essayer d’étendre le volume. Pour plus d’informations sur l’extension d’un volume, consultez la page [Modifier un volume](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Vous pouvez effectuer une sauvegarde lorsque vous restaurez un volume local** – Pour connaître les procédures, accédez à [Utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde](storsimple-manage-backup-policies.md).

- **Vous pouvez annuler une opération de restauration** – Si vous annulez le travail de restauration, le volume sera restauré à l’état où il se trouvait avant le lancement de l’opération de restauration. Pour connaître les procédures, consultez la page [Annuler un travail](storsimple-manage-jobs.md#cancel-a-job).

## Utilisation du catalogue de sauvegarde

La page **Catalogue de sauvegarde** comprend une zone de requête qui vous permet d’affiner la sélection des ensembles de sauvegarde. Vous pouvez filtrer les jeux de sauvegarde récupérés selon les paramètres suivants :

- **Appareil** : appareil sur lequel le jeu de sauvegarde a été créé.
- **Stratégie de sauvegarde** ou **volume** : stratégie de sauvegarde ou volume associé à ce jeu de sauvegarde.
- **De** et **À** : plage de dates et d’heures de création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite affichés sous forme de tableau sur la base des attributs suivants :

- **Nom** : nom de la stratégie de sauvegarde ou du volume associé à ce jeu de sauvegarde.
- **Taille** : taille réelle du jeu de sauvegarde.
- **Créé le** : date et heure auxquelles les sauvegardes ont été créées. 
- **Type** : les jeux de sauvegarde peuvent être des instantanés locaux ou des instantanés cloud. Un instantané local est une sauvegarde de toutes les données de volume stockées localement sur l’appareil, tandis qu’un instantané cloud correspond à la sauvegarde des données de volume résidant dans le cloud. Les instantanés locaux offrent un accès plus rapide, alors que les instantanés cloud sont choisis pour la résilience des données.
- **Initié par** : les sauvegardes peuvent être lancées automatiquement suivant une planification ou manuellement par un utilisateur. (Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Vous pouvez également utiliser l’option **Effectuer une sauvegarde** pour effectuer une sauvegarde interactive.)

## Comment restaurer votre volume StorSimple à partir d’une sauvegarde

Vous pouvez utiliser la page **Catalogue de sauvegarde** pour restaurer votre volume StorSimple à partir d’une sauvegarde spécifique. N’oubliez pas cependant que la restauration d’un volume rétablit l’état dans lequel il se trouvait au moment de la sauvegarde. Toutes les données qui ont été ajoutées après l’opération de sauvegarde seront perdues.

> [AZURE.WARNING] La restauration à partir d’une sauvegarde remplace les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte des données qui ont été écrites après la sauvegarde.

### Pour restaurer votre volume

1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde**.

    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/HCS_Restore.png)

2. Sélectionnez un jeu de sauvegarde comme suit :
  1. Sélectionnez l’appareil approprié.
  2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde à sélectionner.
  3. Indiquez l’intervalle de temps.
  4. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) pour exécuter cette requête.
 
    Les sauvegardes associées au volume ou à la stratégie de sauvegarde sélectionné doivent figurer dans la liste des jeux de sauvegarde.

3. Développez le jeu de sauvegarde pour afficher les volumes associés. Ces volumes doivent être mis hors connexion sur l’hôte et l’appareil avant leur restauration. Accédez aux volumes à la page **Conteneurs de volumes**, puis suivez les étapes de la rubrique [Mise hors connexion d’un volume](storsimple-manage-volumes-u2.md#take-a-volume-offline) pour les mettre hors connexion.

    > [AZURE.IMPORTANT] Veillez à mettre les volumes hors connexion sur l’ordinateur hôte avant de les mettre hors connexion sur l’appareil. Sans quoi, vous vous exposez à un risque d’altération des données.

4. Revenez à l’onglet **Catalogue de sauvegarde** et sélectionnez un jeu de sauvegarde.

5. Cliquez sur **Restaurer** en bas de la page.

6. Vous êtes invité à confirmer l’opération. Passez en revue les informations de restauration, puis cochez la case de confirmation.

    ![Page Confirmation](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Cette opération lance une tâche de restauration que vous pouvez afficher en accédant à la page **Tâches**.

8. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes a été remplacé par les volumes provenant de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo expliquant comment utiliser les fonctionnalités de clonage et de restauration dans StorSimple pour récupérer des fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## Si la restauration échoue

Vous recevez une alerte si l’opération de restauration échoue pour une raison quelconque. Le cas échéant, actualisez la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide et que vous restaurez à partir du cloud, le problème peut être dû à des problèmes de connectivité.

Pour terminer l’opération de restauration, mettez le volume hors connexion sur l’hôte et recommencez l’opération de restauration. Notez que toutes les modifications apportées aux données du volume pendant le processus de restauration seront perdues.

## Étapes suivantes

- Découvrez comment [gérer des volumes StorSimple](storsimple-manage-volumes-u2.md).

- Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration-u2.md).

<!---HONumber=AcomDC_0128_2016-->