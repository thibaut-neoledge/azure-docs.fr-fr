<properties 
   pageTitle="Didacticiel de sauvegarde de StorSimple Virtual Array | Microsoft Azure"
   description="Décrit comment sauvegarder des partages et des volumes StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/01/2016"
   ms.author="alkohli" />

# Sauvegarder votre StorSimple Virtual Array

## Vue d'ensemble 

Ce didacticiel s'applique à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local ou appareil virtuel StorSimple) exécutant la version de mise à la disposition générale (mars 2016).

StorSimple Virtual Array est un périphérique virtuel local de stockage cloud hybride qui peut être configuré comme un serveur de fichiers ou un serveur iSCSI. Il peut créer des sauvegardes, restaurer des données à partir de sauvegardes et effectuer le basculement d'un appareil si une récupération d'urgence est nécessaire. Configuré comme serveur de fichiers, il permet également la récupération au niveau de l’élément. Ce didacticiel explique comment utiliser le portail Azure Classic ou l'interface utilisateur web StorSimple pour créer des sauvegardes planifiées et manuelles de votre StorSimple Virtual Array.


## Sauvegarder des partages et des volumes

Les sauvegardes fournissent une protection jusqu’à une date et une heure, et optimisent la récupération tout en réduisant les délais de restauration pour les partages et les sauvegardes. Vous pouvez sauvegarder un partage ou un volume sur votre appareil StorSimple de deux manières : **planifiée** ou **manuelle**. Chacune des méthodes est abordée dans les sections suivantes.

> [AZURE.NOTE] Dans cette version, les sauvegardes planifiées sont créées à l’aide d’une stratégie par défaut qui s'exécute tous les jours à un moment précis et sauvegarde tous les partages ou volumes sur l'appareil. Il n'est pour l’instant pas possible de créer des stratégies personnalisées pour les sauvegardes planifiées.

## Modifier la planification de sauvegarde

Votre appareil virtuel StorSimple comporte une stratégie de sauvegarde par défaut qui démarre à une heure spécifique de la journée (22h30) et sauvegarde une fois par jour tous les partages ou volumes sur le périphérique. Vous pouvez modifier l'heure à laquelle la sauvegarde démarre, mais la fréquence et la durée de rétention (qui spécifie le nombre de sauvegardes à conserver) ne peuvent pas être modifiées. Au cours de ces sauvegardes, la totalité de l’appareil virtuel est enregistrée ; par conséquent, nous vous recommandons de planifier ces sauvegardes pendant les heures creuses.

Procédez comme suit dans le [portail Azure Classic](https://manage.windowsazure.com/) pour modifier l’heure de début de la sauvegarde par défaut.

#### Pour modifier l'heure de début de la stratégie de sauvegarde par défaut

1. Naviguez jusqu’à l’onglet **Configuration** de l’appareil.

2. Sous la section **Sauvegarde**, spécifiez l'heure de début de la sauvegarde quotidienne.

3. Cliquez sur **Enregistrer**.

### Exécuter une sauvegarde manuelle

Outre les sauvegardes planifiées, vous pouvez à tout moment effectuer une sauvegarde manuelle (à la demande).

#### Pour créer une sauvegarde manuelle (à la demande)

1. Accédez à l’onglet **Partages** ou l’onglet **Volumes**.

2. En bas de la page, cliquez sur **Sauvegarder tout**. Vous devrez confirmer que vous souhaitez effectuer la sauvegarde maintenant. Cliquez sur l'icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-backup/image3.png) pour poursuivre la sauvegarde.

    ![confirmation de sauvegarde](./media/storsimple-ova-backup/image4.png)

    Un message vous informe qu'une tâche de sauvegarde a démarré.

    ![démarrage de sauvegarde](./media/storsimple-ova-backup/image5.png)

    Un message vous informe que la tâche a été créée.

    ![travail de sauvegarde créé](./media/storsimple-ova-backup/image7.png)

3. Pour suivre la progression de la tâche, cliquez sur l’icône **Afficher la tâche**.

4. Une fois le travail de sauvegarde terminé, accédez à l’onglet **Catalogue de sauvegarde**. Votre sauvegarde devrait être terminée.

    ![Sauvegarde terminée](./media/storsimple-ova-backup/image8.png)

5. Définissez les sélections de filtre pour l’appareil approprié, la stratégie de sauvegarde et la plage horaire, puis cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-backup/image3.png).

    La sauvegarde doit apparaître dans la liste des jeux de sauvegarde qui s’affiche dans le catalogue.

## Afficher les sauvegardes existantes

Procédez comme suit dans le portail Azure Classic pour afficher les sauvegardes existantes.

#### Pour afficher les sauvegardes existantes

1. Dans la page du service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde**.

2. Sélectionnez un jeu de sauvegarde comme suit :

    1. Sélectionnez le périphérique.

    2. Dans la liste déroulante, choisissez le partage ou le volume pour la sauvegarde à sélectionner.

    3. Indiquez l’intervalle de temps.

    4. Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-backup/image3.png) pour exécuter cette requête.

    Les sauvegardes associées au partage ou volume sélectionné doivent figurer dans la liste des jeux de sauvegarde.

![icône\_vidéo](./media/storsimple-ova-backup/video_icon.png) **Vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez créer des partages, sauvegarder les partages et restaurer des données sur un StorSimple Virtual Array.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## Étapes suivantes

En savoir plus sur la [gestion de votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->