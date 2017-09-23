---
title: "Restauration d’un volume StorSimple à partir d’une sauvegarde | Microsoft Docs"
description: "Explique comment utiliser la page Catalogue de sauvegarde du service StorSimple Manager pour restaurer un volume StorSimple à partir d’un jeu de sauvegarde."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/22/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 99b76e3bc2939c65654cbf606fda6f8a45e0c44b
ms.contentlocale: fr-fr
ms.lasthandoff: 04/20/2017

---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restauration d’un volume StorSimple à partir d’un jeu de sauvegarde (Mise à jour 2)
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Vue d’ensemble
La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors de sauvegardes manuelles ou automatisées. Utilisez cette page pour répertorier et gérer les sauvegardes, restaurer à partir d’un jeu de sauvegarde ou cloner un volume.

 ![Page Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/restore.png)

Ce didacticiel explique comment utiliser la page **Catalogue de sauvegarde** pour restaurer l’appareil à partir d’un jeu de sauvegarde.

Vous pouvez restaurer un volume à partir d’une sauvegarde locale ou sur le cloud. Dans les deux cas, l’opération de restauration met le volume en ligne immédiatement pendant le téléchargement des données en arrière-plan. 

## <a name="before-you-restore"></a>Avant de procéder à la restauration
Avant de lancer une opération de restauration, prenez connaissance des mises en garde suivantes :

* **Mettez le volume hors connexion** : placez le volume hors connexion sur l’hôte et sur l’appareil avant de lancer une opération de restauration. Bien que l’opération de restauration mette automatiquement le volume en ligne sur l’appareil, vous devez le faire manuellement sur l’hôte. Vous pouvez mettre le volume en ligne sur l’hôte quand le volume est en ligne sur l’appareil. (Il est inutile d’attendre la fin de l’opération de restauration.) Pour connaître les procédures, consultez [Mise hors connexion d’un volume](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Type de volume après l’opération de restauration** : les volumes supprimés sont restaurés en fonction du type de la capture instantanée. Les volumes qui ont été épinglés localement sont restaurés en tant que volumes épinglés localement, et les volumes qui étaient hiérarchisés sont restaurés en tant que volumes hiérarchisés.
  
    Pour les volumes existants, le type de volume selon l’utilisation actuelle remplace le type stocké dans l’instantané. Par exemple, si vous restaurez un volume à partir d’une capture instantanée qui a été prise lorsque le type de volume était hiérarchisé et que celui-ci est désormais épinglé localement (à la suite d’une opération de conversion), le volume est restauré en tant que volume épinglé localement. De même, si un volume épinglé localement existant est étendu puis restauré à partir d’une capture instantanée précédente prise lorsque le volume était plus petit, le volume restauré conserve la taille étendue actuelle.
  
    Vous ne pouvez pas convertir un volume hiérarchisé en un volume épinglé localement ou _vice versa_ pendant la restauration du volume. Vous devez attendre la fin de l’opération de restauration avant de convertir le volume en un autre type. Pour plus d’informations sur la conversion d’un volume, consultez la page [Changer de type de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **La taille du volume est reflétée dans le volume restauré** : il s’agit d’un élément important si vous restaurez un volume épinglé localement qui a été supprimé (car les volumes épinglés localement sont complètement configurés). Vérifiez que vous disposez d’un espace suffisant avant d’essayer de restaurer un volume épinglé localement qui a été supprimé. 
* **Vous ne pouvez pas étendre un volume pendant sa restauration** – Attendez la fin de l’opération de restauration avant d’essayer d’étendre le volume. Pour plus d’informations sur l’extension d’un volume, consultez la page [Modifier un volume](storsimple-manage-volumes-u2.md#modify-a-volume).
* **Vous pouvez effectuer une sauvegarde lorsque vous restaurez un volume local** – Pour connaître les procédures, accédez à [Utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde](storsimple-manage-backup-policies.md).
* **Vous pouvez annuler une opération de restauration** : si vous annulez le travail de restauration, le volume est restauré à l’état où il se trouvait avant le lancement de l’opération de restauration. Pour connaître les procédures, consultez la page [Annuler un travail](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Fonctionnement du processus de restauration
Pour les appareils exécutant Update 4 ou version ultérieure, une restauration basée sur une carte thermique est implémentée. Étant donné que l’hôte demande à accéder aux données de l’appareil, ces demandes font l’objet d’un suivi et une carte thermique est créée. Un taux de demande élevé se traduit par des segments de données avec plus de chaleur, tandis qu’un taux de demande plus faible se traduit par des segments avec moins de chaleur. Vous devez accéder aux données à deux reprises minimum pour qu’elles soient marquées comme _chaudes_. Un fichier modifié est également marqué comme _chaud_. Une fois que vous lancez la restauration, le rafraîchissement proactif des données se produit en fonction de la carte thermique. Pour les versions antérieures à Update 4, le téléchargement des données a eu lieu pendant la restauration basée sur l’accès uniquement. 

Le suivi basé sur la carte thermique est activé uniquement pour les volumes hiérarchisés (les volumes épinglés localement ne sont pas pris en charge). La restauration basée sur la carte thermique n’est pas prise en charge lors du clonage d’un volume sur un autre appareil. Si une restauration en place et si une capture instantanée locale existent sur l’appareil pour le volume à restaurer, aucun rafraîchissement n’est nécessaire (car les données sont déjà disponibles localement). Par défaut, lors de la restauration, les travaux de rafraîchissement sont initiés et rafraîchissent les données de façon proactive en fonction de la carte thermique. Dans Update 4, les cmdlets Windows PowerShell peuvent être utilisées pour interroger des travaux de rafraîchissement en cours d’exécution, annuler un travail de rafraîchissement et obtenir l’état du travail de rafraîchissement.

* `Get-HcsRehydrationJob` : cette cmdlet obtient l’état du travail de rafraîchissement. Un seul travail de rafraîchissement est déclenché pour un volume.
* `Set-HcsRehydrationJob` : cette cmdlet vous permet d’interrompre, d’arrêter ou de reprendre le travail de rafraîchissement au cours du rafraîchissement. 

Pour plus d’informations sur les cmdlets de rafraîchissement, consultez le [document de référence sur les cmdlets Windows PowerShell pour StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

En général, avec le rafraîchissement automatique, des performances de lecture temporaires plus élevées sont attendues. La véritable magnitude des améliorations dépend de divers facteurs tels que le modèle d’accès, l’évolution des données et le type de données. Pour annuler un travail de rafraîchissement vous pouvez utiliser la cmdlet PowerShell. Si vous souhaitez désactiver définitivement les travaux de rafraîchissement pour toutes les restaurations futures, contactez le Support Microsoft.

## <a name="how-to-use-the-backup-catalog"></a>Utilisation du catalogue de sauvegarde
La page **Catalogue de sauvegarde** comprend une zone de requête qui vous permet d’affiner la sélection des ensembles de sauvegarde. Vous pouvez filtrer les jeux de sauvegarde récupérés selon les paramètres suivants :

* **Appareil** : appareil sur lequel le jeu de sauvegarde a été créé.
* **Stratégie de sauvegarde** ou **volume** : stratégie de sauvegarde ou volume associé à ce jeu de sauvegarde.
* **De** et **À** : plage de dates et d’heures de création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite affichés sous forme de tableau sur la base des attributs suivants :

* **Nom** : nom de la stratégie de sauvegarde ou du volume associé à ce jeu de sauvegarde.
* **Taille** : taille réelle du jeu de sauvegarde.
* **Créé le** : date et heure auxquelles les sauvegardes ont été créées. 
* **Type** : les jeux de sauvegarde peuvent être des instantanés locaux ou des instantanés cloud. Une capture instantanée locale est une sauvegarde de toutes les données des volumes stockées localement sur l’appareil. Une capture instantanée cloud fait référence à la sauvegarde des données des volumes résidant dans le cloud. Les instantanés locaux offrent un accès plus rapide, alors que les instantanés cloud sont choisis pour la résilience des données.
* **Initié par** : les sauvegardes peuvent être lancées automatiquement suivant une planification ou manuellement par vous. (Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Vous pouvez également utiliser l’option **Effectuer une sauvegarde** pour effectuer une sauvegarde interactive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Comment restaurer votre volume StorSimple à partir d’une sauvegarde
Vous pouvez utiliser la page **Catalogue de sauvegarde** pour restaurer votre volume StorSimple à partir d’une sauvegarde spécifique. Gardez toutefois à l’esprit que la restauration d’un volume rétablit l’état dans lequel il se trouvait au moment de la sauvegarde. Toutes les données qui ont été ajoutées après l’opération de sauvegarde sont perdues.

> [!WARNING]
> La restauration à partir d’une sauvegarde remplace les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte des données qui ont été écrites après la sauvegarde.
> 
> 

### <a name="to-restore-your-volume"></a>Pour restaurer votre volume
1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde** .
   
    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Sélectionnez un jeu de sauvegarde comme suit :
   
   1. Sélectionnez l’appareil approprié.
   2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde à sélectionner.
   3. Indiquez l’intervalle de temps.
   4. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) pour exécuter cette requête.
      
      Les sauvegardes associées au volume ou à la stratégie de sauvegarde sélectionné doivent figurer dans la liste des jeux de sauvegarde.
3. Développez le jeu de sauvegarde pour afficher les volumes associés. Ces volumes doivent être mis hors connexion sur l’hôte et l’appareil avant leur restauration. Accédez aux volumes à la page **Conteneurs de volumes** , puis suivez les étapes de la rubrique [Mise hors connexion d’un volume](storsimple-manage-volumes-u2.md#take-a-volume-offline) pour les mettre hors connexion.
   
   > [!IMPORTANT]
   > Veillez à mettre les volumes hors connexion sur l’ordinateur hôte avant de les mettre hors connexion sur l’appareil. Sans quoi, vous vous exposez à un risque d’altération des données.
   > 
   > 
4. Revenez à l’onglet **Catalogue de sauvegarde** et sélectionnez un jeu de sauvegarde.
5. Cliquez sur **Restaurer** en bas de la page.
6. Vous êtes invité à confirmer l’opération. Passez en revue les informations de restauration, puis cochez la case de confirmation.
   
    ![Page Confirmation](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Un travail de restauration commence. Vous pouvez afficher le travail en accédant à la page **Travaux**. 
8. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes a été remplacé par les volumes provenant de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo expliquant comment utiliser les fonctionnalités de clonage et de restauration dans StorSimple pour récupérer des fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Si la restauration échoue
Vous recevez une alerte si l’opération de restauration échoue pour une raison quelconque. Le cas échéant, actualisez la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide et que vous restaurez à partir du cloud, le problème peut être dû à des problèmes de connectivité. 

Pour terminer l’opération de restauration, mettez le volume hors connexion sur l’hôte et recommencez l’opération de restauration. Toutes les modifications apportées aux données des volumes pendant le processus de restauration sont perdues.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [gérer des volumes StorSimple](storsimple-manage-volumes-u2.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).


