---
title: "Gestion de vos stratégies de sauvegarde StorSimple | Microsoft Docs"
description: "Explique comment utiliser le service StorSimple Manager pour créer et gérer des sauvegardes manuelles, des planifications de sauvegarde et une rétention de sauvegarde."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 4a2db707-bbfc-425c-bfeb-bc5c97781873
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8bf90b0ca5e488b676b50c37b24202002824c99b
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde (Mise à jour 2)
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Pour afficher la version de cet article applicable au nouveau portail Azure, accédez à [Utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde (mise à jour 2)](storsimple-8000-manage-backup-policies-u2.md). Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous explique comment utiliser la page **Stratégies de sauvegarde** du service StorSimple Manager pour contrôler les processus de sauvegarde et la rétention de sauvegarde pour vos volumes StorSimple. Il décrit également comment effectuer une sauvegarde manuelle.

Lorsque vous sauvegardez un volume, vous pouvez choisir de créer un instantané local ou un instantané cloud. Si vous sauvegardez un volume épinglé localement, nous vous recommandons de spécifier un instantané cloud. Le fait de prendre un grand nombre d'instantanés locaux d’un volume épinglé localement associé à un jeu de données qui possède une attrition élevée entraîne une situation dans laquelle vous pourriez rapidement manquer d'espace local. Si vous choisissez de prendre des instantanés locaux, nous vous recommandons de prendre moins d'instantanés quotidiens pour sauvegarder l'état le plus récent, de les conserver pendant un jour et de les supprimer.

Lorsque vous prenez un instantané cloud d'un volume épinglé localement, vous copiez uniquement les données modifiées dans le cloud, où elles sont dédupliquées et compressées. 

## <a name="the-backup-policies-page"></a>La page Stratégies de sauvegarde
La page **Stratégies de sauvegarde** vous permet de gérer les stratégies de sauvegarde et de planifier les instantanés locaux et cloud. (Les stratégies de sauvegarde  sont utilisées pour configurer les planifications de sauvegarde et la rétention de sauvegarde pour un ensemble de volumes). Les stratégies de sauvegarde permettent de prendre un instantané de plusieurs volumes simultanément. Ce qui signifie que les sauvegardes créées par une stratégie de sauvegarde sont des copies cohérentes de l’incident. La page **Stratégies de sauvegarde** répertorie les stratégies de sauvegarde, leurs types, les volumes associés, le nombre de sauvegarde retenues et l'option d'activation de ces stratégies.

Sur la page **Stratégies de sauvegarde** , vous pouvez filtrer les stratégies existantes de sauvegarde à l’aide de l’un ou de plusieurs des champs suivants :

* **Nom de la stratégie** : nom associé à la stratégie. Les différents types de stratégies sont les suivants :
  
  * stratégies planifiées, qui sont explicitement créées par l’utilisateur ;
  * stratégies automatiques, qui sont créées lorsque la sauvegarde par défaut associée à cette option de volume a été activée lors de la création du volume. Ces stratégies portent le nom *NomVolume*_Default, où *NomVolume* désigne le nom du volume StorSimple configuré par l'utilisateur dans le portail Azure Classic. Ces stratégies automatiques entraînent la génération quotidienne d’instantanés cloud, commençant à 22 h 30 (heure de l’appareil) ;
  * stratégies importées, qui ont été créées dans le gestionnaire d’instantanés StorSimple. Ces éléments présentent une balise décrivant l’hôte du gestionnaire d’instantanés StorSimple depuis lequel les stratégies ont été importées.
* **Volumes** : les volumes associés à la stratégie. Tous les volumes associés à une stratégie de sauvegarde sont regroupés lors de la création des sauvegardes.
* **Dernière sauvegarde réussie** : la date et l’heure de la dernière sauvegarde réussie associée à cette stratégie.
* **Prochaine sauvegarde** : la date et l’heure de la prochaine sauvegarde planifiée qui sera lancée par cette stratégie.
* **Planifications** : le nombre de planifications associées à la stratégie de sauvegarde.

Les opérations fréquentes pouvant être effectuées à partir de cette page sont les suivantes :

* Ajouter une stratégie de sauvegarde 
* Ajouter ou modifier une planification 
* Supprimer une stratégie de sauvegarde 
* Exécuter une sauvegarde manuelle 
* Créer une stratégie de sauvegarde personnalisée comportant plusieurs volumes et planifications 

## <a name="add-a-backup-policy"></a>Ajouter une stratégie de sauvegarde
Ajoutez une stratégie de sauvegarde pour planifier automatiquement vos sauvegardes. Pour ajouter une stratégie de sauvegarde dédiée à votre appareil StorSimple, procédez comme suit dans le portail Azure Classic. Après avoir ajouté la stratégie, vous pouvez définir une planification (voir [Ajouter ou modifier une planification](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Vidéo disponible](./media/storsimple-manage-backup-policies-u2/Video_icon.png)**Vidéo disponible**

Pour visionner une vidéo expliquant comment créer une stratégie de sauvegarde locale ou dans le cloud, cliquez [ici](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## <a name="add-or-modify-a-schedule"></a>Ajouter ou modifier une planification
Vous pouvez ajouter ou modifier une planification associée à une stratégie de sauvegarde existante sur votre appareil StorSimple. Pour ajouter ou modifier une planification, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Supprimer une stratégie de sauvegarde
Pour supprimer une stratégie de sauvegarde sur votre appareil StorSimple, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Exécuter une sauvegarde manuelle
Pour créer une sauvegarde à la demande (manuelle) pour un volume unique, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Créer une stratégie de sauvegarde personnalisée comportant plusieurs volumes et planifications
Pour créer une stratégie de sauvegarde personnalisée présentant plusieurs volumes et planifications, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

