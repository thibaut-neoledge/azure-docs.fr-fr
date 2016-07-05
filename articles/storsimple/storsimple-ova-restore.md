<properties
   pageTitle="Restaurer à partir d’une sauvegarde de votre StorSimple Virtual Array"
   description="En savoir plus sur la restauration d’une sauvegarde de votre StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# Restaurer à partir d’une sauvegarde de votre StorSimple Virtual Array

## Vue d'ensemble 

Cet article s’applique à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local ou appareil virtuel StorSimple) exécutant la version de mise à la disposition générale (mars 2016) ou des versions ultérieures. Cet article décrit étape par étape comment effectuer une restauration à partir d’un jeu de sauvegarde de vos partages ou volumes sur votre StorSimple Virtual Array. L'article détaille également le fonctionnement de la récupération au niveau de l’élément sur votre StorSimple Virtual Array configuré comme un serveur de fichiers.


## Restauration de partages à partir d’un jeu de sauvegarde


**Avant d'essayer de restaurer des partages, assurez-vous de disposer de suffisamment d'espace sur l’appareil pour terminer cette opération.** Pour effectuer une restauration à partir d’une sauvegarde, procédez comme suit dans le [portail Azure Classic](https://manage.windowsazure.com/).

#### Pour restaurer un partage

1.  Accédez au **Catalogue de sauvegarde**. Filtrez par appareil et par plage horaire pour rechercher vos sauvegardes. Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-restore/image1.png) pour exécuter la requête.


1.  Dans la liste des jeux de sauvegarde qui apparaît, sélectionnez un jeu de sauvegarde spécifique. Développez la sauvegarde pour afficher les différents partages qu'elle contient. Sélectionnez un partage que vous souhaitez restaurer.

2.  En bas de la page, cliquez sur **Restaurer comme nouveau**.

3.  Cette opération lance l’assistant **Restaurer comme nouveau partage**. Dans la page **Spécifiez un nom et un emplacement** :


	1.  Vérifiez le nom de l’appareil source. Il doit s'agir de l’appareil contenant le partage que vous souhaitez restaurer. La sélection de l'appareil est grisée. Pour sélectionner un autre appareil source, vous devez quitter l'assistant et sélectionner à nouveau le jeu de sauvegarde.

	2.  Nommez le partage. Le nom du partage doit contenir entre 3 et 127 caractères.

	3.  Passez en revue la taille, le type et les autorisations associées au partage que vous essayez de restaurer. Vous pourrez modifier les propriétés du partage via l'Explorateur Windows une fois la restauration terminée.

	4.  Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-restore/image1.png).

		![](./media/storsimple-ova-restore/image9.png)

1.  Une fois le travail de restauration terminé, la restauration démarre et une autre notification apparaît. Pour surveiller la progression de la restauration, cliquez sur **Afficher la tâche**. Vous accédez alors à la page **Tâches**.

2.  Vous pouvez suivre la progression de la restauration. Lorsque la restauration est terminée à 100 %, accédez à la page **Partages** sur votre appareil.

3.  Vous pouvez maintenant afficher le nouveau partage restauré dans la liste des partages sur votre appareil. Notez que la restauration est effectuée pour le même type de partage. Un partage à plusieurs niveaux est restauré comme un partage à plusieurs niveaux, et un partage épinglé localement comme un partage épinglé localement.

Vous avez maintenant terminé la configuration de l’appareil et appris à sauvegarder ou restaurer un partage.


## Restauration de volumes à partir d’un jeu de sauvegarde


Pour effectuer une restauration à partir d’une sauvegarde, procédez comme suit dans le portail Azure Classic. L'opération de restauration restaure la sauvegarde vers un nouveau volume sur le même appareil virtuel ; vous ne pouvez pas restaurer vers un autre appareil.

#### Pour restaurer un volume

1.  Accédez au **Catalogue de sauvegarde**. Filtrez par appareil et par plage horaire pour rechercher vos sauvegardes. Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-restore/image1.png) pour exécuter la requête.

2.  Dans la liste des jeux de sauvegarde qui apparaît, sélectionnez un jeu de sauvegarde spécifique. Développez la sauvegarde pour afficher les différents volumes qu'elle contient. Vous devez mettre ces volumes mis hors connexion sur l’hôte et l’appareil avant leur restauration. Accédez aux volumes sur la page **Volumes** et mettez-les hors connexion.

3.  Revenez à l’onglet **Catalogue de sauvegarde** et sélectionnez un jeu de sauvegarde.

5.  En bas de la page, cliquez sur **Restaurer comme nouveau**. L’assistant **Restaurer comme nouveau volume** démarre.

1.  Dans la page **Spécifiez un nom et un emplacement** :


	1.  Vérifiez le nom de l’appareil source. Il doit s'agir de l’appareil contenant le volume que vous souhaitez restaurer. La sélection de l'appareil n'est pas disponible. Pour sélectionner un autre appareil source, vous devez quitter l'assistant et sélectionner à nouveau le jeu de sauvegarde.

	2.  Spécifiez un nom de volume. Le nom du volume doit contenir entre 3 et 127 caractères.

	3.  Cliquez sur l'icône en forme de flèche.

		![](./media/storsimple-ova-restore/image12.png)

1.  Dans la page **Spécifiez les hôtes qui peuvent utiliser ce volume**, sélectionnez les ACR appropriés dans la liste déroulante.

	![](./media/storsimple-ova-restore/image13.png)

1.  Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-restore/image1.png). Cette opération lance une tâche de restauration, et la notification suivante confirme que la tâche est en cours.

2.  Une fois le travail de restauration terminé, la restauration démarre et une autre notification apparaît. Pour surveiller la progression de la restauration, cliquez sur **Afficher la tâche**. Vous accédez alors à la page **Tâches**.

3.  Vous pouvez suivre la progression de la restauration. Quand la restauration est terminée à 100 %, accédez à la page **Volumes** sur votre appareil.

4.  Vous pouvez maintenant afficher le nouveau volume restauré dans la liste des volumes sur votre appareil. Notez que la restauration est effectuée pour le même type de volume. Un volume à plusieurs niveaux est restauré comme un volume à plusieurs niveaux, et un volume épinglé localement comme un volume épinglé localement.

> [AZURE.IMPORTANT] Lorsque vous tentez de restaurer un volume ou un partage à partir d’un jeu de sauvegarde, si le travail de restauration échoue, un volume ou partage cible peut toujours être créé dans le portail. Il est important de supprimer ce volume ou partage cible dans le portail pour réduire les problèmes futurs découlant de cet élément.

## Récupération au niveau de l'élément (ILR)

Cette version présente la récupération au niveau de l’élément (ILR) sur un appareil virtuel StorSimple configuré comme un serveur de fichiers. Cette fonctionnalité vous permet d'effectuer une récupération granulaire de fichiers et de dossiers à partir d'une sauvegarde cloud contenant tous les partages sur l'appareil StorSimple. Grâce à un modèle en libre-service, les utilisateurs peuvent récupérer des fichiers supprimés à partir de sauvegardes récentes.

Chaque partage comporte un dossier *.backups* contenant les sauvegardes les plus récentes. L'utilisateur peut naviguer vers la sauvegarde souhaitée, copier les fichiers et dossiers correspondants à partir de la sauvegarde, puis les restaurer. Cette procédure évite de contacter les administrateurs pour restaurer des fichiers à partir de sauvegardes.

1.  Lorsque vous effectuez la récupération ILR, vous pouvez afficher les sauvegardes dans l'Explorateur Windows. Cliquez sur le partage spécifique pour lequel vous souhaitez afficher la sauvegarde. Sous le partage, vous verrez un dossier *.backups* contenant toutes les sauvegardes. Développez le dossier *.backups* pour afficher les sauvegardes. Le dossier affichera ensuite la vue éclatée de toute la hiérarchie de sauvegarde. La création de cette vue s’effectue à la demande et ne prend que quelques secondes.

	Les 5 dernières sauvegardes s’affichent de cette façon et peuvent être utilisées pour effectuer une récupération au niveau de l’élément. Les 5 sauvegardes récentes incluent les sauvegardes planifiées par défaut et les sauvegardes manuelles.

	
	-   **Sauvegardes planifiées** nommées &lt;Nom de l’appareil&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC.

	-   **Sauvegardes manuelles** nommées Ad-hoc-YYYYMMDD-HHMMSS-UTC.
	
		![](./media/storsimple-ova-restore/image14.png)

1.  Identifiez la sauvegarde contenant la version la plus récente du fichier supprimé. Bien que le nom du dossier contienne un horodatage UTC dans chacun des cas ci-dessus, l'heure de création du dossier correspond à l’heure réelle de démarrage de la sauvegarde sur l’appareil. Utilisez l'horodatage du dossier pour localiser et identifier les sauvegardes.

2.  Recherchez le dossier ou fichier que vous voulez restaurer dans la sauvegarde que vous avez identifiée à l'étape précédente. Notez que vous pouvez uniquement afficher les fichiers ou dossiers pour lesquels vous disposez d'autorisations. Si vous ne parvenez pas à accéder à certains fichiers ou dossiers, vous devrez contacter un administrateur de partage qui peut utiliser l'Explorateur Windows pour modifier les autorisations de partage et vous permettre d'accéder à ce fichier ou dossier. Il est recommandé que l'administrateur de partage soit un groupe d'utilisateurs plutôt qu'un utilisateur unique.

3.  Copiez le fichier ou le dossier dans le partage approprié sur votre serveur de fichiers StorSimple.

![icône\_vidéo](./media/storsimple-ova-restore/video_icon.png) **Vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez créer des partages, sauvegarder les partages et restaurer des données sur un StorSimple Virtual Array.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## Étapes suivantes

En savoir plus sur la [gestion de StorSimple Virtual Array à l’aide de l’interface utilisateur web locale](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->