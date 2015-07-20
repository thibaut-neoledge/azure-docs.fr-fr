<properties
   pageTitle="Clonage de votre volume StorSimple"
   description="Décrit les différents types de clone et les moments auxquels les utiliser et explique comment vous pouvez utiliser un jeu de sauvegarde pour cloner un volume individuel."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/27/2015"
   ms.author="alkohli" />

# Clonage d’un volume StorSimple

## Vue d'ensemble

La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors de sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer des sauvegardes, ou utiliser une sauvegarde pour restaurer ou cloner un volume.

  ![catalogue-sauvegarde](./media/storsimple-clone-volume/HCS_BackupCatalog.png)

**Figure 1 : catalogue de sauvegarde**

Ce didacticiel explique comment vous pouvez utiliser un jeu de sauvegarde pour cloner un volume individuel. Il explique également la différence entre les clones *temporaires* et *permanents*.

## Création du clone d’un volume

Vous pouvez créer un clone sur le même appareil, sur un autre ou même une machine virtuelle à l’aide d’un instantané local ou cloud.

## Clonage d’un volume

1. Sur la page de service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde** et sélectionnez un jeu de sauvegarde.

2. Développez le jeu de sauvegarde pour afficher les volumes associés. Cliquez sur un volume et sélectionnez-le dans le jeu de sauvegarde.

     ![Cloner](./media/storsimple-clone-volume/HCS_Clone.png)

3. Cliquez sur **Cloner** pour commencer le clonage du volume sélectionné.

4. Dans l’Assistant Cloner le volume, sous **Spécifier le nom et l’emplacement** :

  1. Identifiez un appareil cible. Il s’agit de l’emplacement où sera créé le clone. Vous pouvez choisir le même appareil ou en spécifier un autre. Si vous choisissez un volume associé à d’autres fournisseurs de service cloud (autres qu’Azure), la liste déroulante pour l’appareil cible affiche uniquement des appareils physiques. Vous ne pouvez pas cloner un volume associé à d’autres fournisseurs de services cloud sur un appareil virtuel.

        >  [AZURE.NOTE] Make sure that the capacity required for the clone is lower than the capacity available on the target device.
  2. Indiquez un nom de volume unique pour votre clone. Le nom doit contenir entre 3 et 127 caractères.
  3. Cliquez sur l’icône en forme de flèche ![icône-flèche](./media/storsimple-clone-volume/HCS_ArrowIcon.png)pour passer à la page suivante.

5. Sous **Spécifier des hôtes qui peuvent utiliser ce volume** :

  1. Spécifiez un enregistrement de contrôle d’accès (ACR) pour le clone. Vous pouvez ajouter un nouvel ACR ou en choisir un dans la liste existante.
  2. Cliquez sur l’icône en forme de coche ![icône-coche](./media/storsimple-clone-volume/HCS_CheckIcon.png)pour terminer l’opération.

6. Un travail de clonage sera lancé et vous serez averti lorsque le clone sera créé avec succès. Cliquez sur **Afficher le travail** pour surveiller le travail de clonage sur la page **Travaux**.

7. Une fois le travail de clonage terminé :

  1. Accédez à la page **Appareils**, puis sélectionnez l’onglet **Conteneurs de volumes**.
  2. Sélectionnez le conteneur de volume associé au volume source que vous avez cloné. Dans la liste des volumes, vous devez voir le clone qui vient d’être créé.

>[AZURE.NOTE]La surveillance et la sauvegarde par défaut sont automatiquement désactivées sur un volume cloné.

Un clone créé de cette manière est un clone temporaire. Pour plus d’informations sur les types de clone, consultez la page [Clones temporaires et permanents](#transient-vs.-permanent-clones).

Ce clone est désormais un volume normal et toute opération possible sur un volume sera disponible pour le clone. Vous devez configurer ce volume pour toutes les sauvegardes.

## Clones temporaires et permanents

Vous pouvez cloner un volume spécifique à partir d’un jeu de sauvegarde. Un clone créé de cette manière est un clone *temporaire*. Le clone temporaire aura des références au volume d’origine et utilisera ce volume pour lire lors de l’écriture locale. Cela peut entraîner des performances lentes, en particulier si le volume cloné est important.

Si vous prenez un instantané de cloud d’un clone temporaire, le clone qui en résulte devient *permanent*. Le clone permanent est indépendant et n’a pas de références au volume d’origine à partir duquel il a été cloné. Pour améliorer les performances, nous vous recommandons de créer des clones permanents.

## Scénarios pour les clones temporaires et permanents

Les sections suivantes décrivent des exemples de situations dans lesquelles les clones temporaires et permanents peuvent être utilisés.

### Récupération au niveau de l’élément avec un clone temporaire

Vous devez récupérer un fichier de présentation Microsoft PowerPoint datant d’un an. Votre administrateur informatique identifie la sauvegarde spécifique dans cette période et filtre ensuite le volume. Ensuite, l’administrateur clone le volume, trouve le fichier que vous recherchez et vous le fournit. Dans ce scénario, un clone temporaire est utilisé.
 
### Test dans l’environnement de production avec un clone permanent

Vous devez vérifier un bogue de test dans l’environnement de production. Vous créez un clone du volume dans l’environnement de production. Pour améliorer les performances, vous devez prendre un instantané de cloud de ce clone. Le volume cloné est désormais indépendant, ce qui entraîne de meilleures performances. Dans ce scénario, un clone permanent est utilisé.

## Étapes suivantes
Découvrez comment [restaurer un volume StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).


 

<!---HONumber=July15_HO2-->