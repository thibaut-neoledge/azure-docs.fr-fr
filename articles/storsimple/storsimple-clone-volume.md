---
title: Clonage de votre volume StorSimple | Microsoft Docs
description: "Décrit les différents types de clone et les moments auxquels les utiliser et explique comment vous pouvez utiliser un jeu de sauvegarde pour cloner un volume individuel."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 8f1936fac543f559a44ad0f9c35b30d1a92dce68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Utiliser le service StorSimple Manager pour cloner un volume
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Vue d'ensemble
La page **Catalogue de sauvegarde** du service StorSimple Manager affiche tous les jeux de sauvegarde créés lors de sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer des sauvegardes, ou utiliser une sauvegarde pour restaurer ou cloner un volume.

![Page Catalogue de sauvegarde](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Ce didacticiel explique comment vous pouvez utiliser un jeu de sauvegarde pour cloner un volume individuel. Il explique également la différence entre les clones *temporaires* et *permanents*. 

## <a name="create-a-clone-of-a-volume"></a>Création du clone d’un volume
Vous pouvez créer un clone sur le même appareil, sur un autre ou même une machine virtuelle à l’aide d’un instantané local ou cloud.

#### <a name="to-clone-a-volume"></a>Clonage d’un volume
1. Sur la page de service StorSimple Manager, cliquez sur l’onglet **Catalogue de sauvegarde** et sélectionnez un jeu de sauvegarde.
2. Développez le jeu de sauvegarde pour afficher les volumes associés. Cliquez sur un volume et sélectionnez-le dans le jeu de sauvegarde.
   
     ![Clonage d’un volume](./media/storsimple-clone-volume/HCS_Clone.png) 
3. Cliquez sur **Cloner** pour commencer le clonage du volume sélectionné.
4. Dans l’Assistant Cloner le volume, sous **Spécifier le nom et l’emplacement**:
   
   1. Identifiez un appareil cible. Il s’agit de l’emplacement où sera créé le clone. Vous pouvez choisir le même appareil ou en spécifier un autre. Si vous choisissez un volume associé à d’autres fournisseurs de service cloud (autres qu’Azure), la liste déroulante pour l’appareil cible affiche uniquement des appareils physiques. Vous ne pouvez pas cloner un volume associé à d’autres fournisseurs de services cloud sur un appareil virtuel.
      
      > [!NOTE]
      > Assurez-vous que la capacité requise pour le clone est inférieure à la capacité disponible sur le périphérique cible.
      > 
      > 
   2. Indiquez un nom de volume unique pour votre clone. Le nom doit contenir entre 3 et 127 caractères.
   3. Cliquez sur l'icône en forme de flèche  ![icône-flèche](./media/storsimple-clone-volume/HCS_ArrowIcon.png) pour passer à la page suivante.
5. Sous **Spécifier des hôtes qui peuvent utiliser ce volume**:
   
   1. Spécifiez un enregistrement de contrôle d’accès (ACR) pour le clone. Vous pouvez ajouter un nouvel ACR ou en choisir un dans la liste existante.
   2. Cliquez sur l’icône en forme de coche  ![icône-coche](./media/storsimple-clone-volume/HCS_CheckIcon.png)pour terminer l’opération.
6. Un travail de clonage sera lancé et vous serez averti lorsque le clone sera créé avec succès. Cliquez sur **Afficher la tâche** pour surveiller le travail de clonage sur la page **Tâches**.
7. Une fois le travail de clonage terminé :
   
   1. Accédez à la page **Appareils**, puis sélectionnez l’onglet **Conteneurs de volumes**. 
   2. Sélectionnez le conteneur de volume associé au volume source que vous avez cloné. Dans la liste des volumes, vous devez voir le clone qui vient d’être créé.

> [!NOTE]
> La surveillance et la sauvegarde par défaut sont automatiquement désactivées sur un volume cloné.
> 
> 

Un clone créé de cette manière est un clone temporaire. Pour plus d’informations sur les types de clone, consultez la page [Clones temporaires et permanents](#transient-vs-permanent-clones).

Ce clone est désormais un volume normal et toute opération possible sur un volume sera disponible pour le clone. Vous devez configurer ce volume pour toutes les sauvegardes.

## <a name="transient-vs-permanent-clones"></a>Clones temporaires et permanents
Les clones temporaires et permanents sont créés uniquement lorsque vous effectuez un clonage sur un autre appareil. Vous pouvez cloner un volume spécifique à partir d’un jeu de sauvegarde sur un autre appareil. Un clone créé de cette manière est un clone *temporaire* . Le clone temporaire aura des références au volume d’origine et utilisera ce volume pour lire lors de l’écriture locale. 

Si vous prenez un instantané de cloud d’un clone temporaire, le clone qui en résulte devient *permanent* . Le clone permanent est indépendant et n’a pas de références au volume d’origine à partir duquel il a été cloné.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénarios pour les clones temporaires et permanents
Les sections suivantes décrivent des exemples de situations dans lesquelles les clones temporaires et permanents peuvent être utilisés.

### <a name="item-level-recovery-with-a-transient-clone"></a>Récupération au niveau de l’élément avec un clone temporaire
Vous devez récupérer un fichier de présentation Microsoft PowerPoint datant d’un an. Votre administrateur informatique identifie la sauvegarde spécifique dans cette période et filtre ensuite le volume. Ensuite, l’administrateur clone le volume, trouve le fichier que vous recherchez et vous le fournit. Dans ce scénario, un clone temporaire est utilisé. 

![Vidéo disponible](./media/storsimple-clone-volume/Video_icon.png)**Vidéo disponible**

Pour visionner une vidéo expliquant comment utiliser les fonctionnalités de clonage et de restauration dans StorSimple pour récupérer des fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test dans l’environnement de production avec un clone permanent
Vous devez vérifier un bogue de test dans l’environnement de production. Vous créez un clone du volume dans l’environnement de production en prenant un instantané cloud de ce clone. Le volume cloné est alors indépendant. Dans ce scénario, un clone permanent est utilisé.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [restaurer un volume StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

