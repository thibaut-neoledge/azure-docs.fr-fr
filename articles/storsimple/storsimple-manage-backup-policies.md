<properties 
   pageTitle="Gestion de vos stratégies de sauvegarde StorSimple | Microsoft Azure"
   description="Explique comment utiliser le service du gestionnaire d’instantanés StorSimple pour créer et gérer des tâches de sauvegarde et des planifications de sauvegarde."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/17/2015"
   ms.author="v-sharos"/>

# Gérer vos stratégies de sauvegarde StorSimple

## Vue d'ensemble

Ce didacticiel vous explique comment utiliser la page **Stratégies de sauvegarde** du service StorSimple Manager pour contrôler les processus de sauvegarde et la rétention de sauvegarde pour vos volumes StorSimple. Il décrit également comment effectuer une sauvegarde manuelle.

La page **Stratégies de sauvegarde** vous permet de gérer les stratégies de sauvegarde et de planifier les instantanés locaux et cloud. (Les stratégies de sauvegarde sont utilisées pour configurer les planifications de sauvegarde et la rétention de sauvegarde pour un ensemble de volumes). Les stratégies de sauvegarde permettent de prendre un instantané de plusieurs volumes simultanément. Ce qui signifie que les sauvegardes créées par une stratégie de sauvegarde sont des copies cohérentes de l’incident. Cette page répertorie les stratégies de sauvegarde, leurs types, les volumes associés, le nombre de sauvegarde retenues et l’option d’activation de ces stratégies.

Sur la page **Stratégies de sauvegarde**, vous pouvez filtrer les stratégies existantes de sauvegarde à l’aide de l’un ou de plusieurs des champs suivants :

- **Nom de la stratégie** : nom associé à la stratégie. Les différents types de stratégies sont les suivants :

   - stratégies planifiées, qui sont explicitement créées par l'utilisateur ;
   - stratégies automatiques, qui sont créées lorsque la sauvegarde par défaut associée à cette option de volume a été activée lors de la création du volume. Ces stratégies portent le nom NomVolume_Default, où « NomVolume » désigne le nom du volume StorSimple configuré par l’utilisateur dans le portail de gestion. Ces stratégies automatiques entraînent la génération quotidienne d’instantanés cloud, commençant à 22 h 30 (heure de l’appareil) ;
   - stratégies importées, qui ont été créées dans le gestionnaire d'instantanés StorSimple. Ces éléments présentent une balise décrivant l’hôte du gestionnaire d’instantanés StorSimple depuis lequel les stratégies ont été importées.

- **Volumes** : les volumes associés à la stratégie. Tous les volumes associés à une stratégie de sauvegarde sont regroupés lors de la création des sauvegardes.

- **Dernière sauvegarde réussie** : la date et l’heure de la dernière sauvegarde réussie associée à cette stratégie.

- **Prochaine sauvegarde** : la date et l’heure de la prochaine sauvegarde planifiée qui sera lancée par cette stratégie.

- **Planifications** : le nombre de planifications associées à la stratégie de sauvegarde.

Les opérations fréquentes pouvant être effectuées à partir de cette page sont les suivantes :

- Ajouter une stratégie de sauvegarde 
- Ajouter ou modifier une planification 
- Supprimer une stratégie de sauvegarde 
- Exécuter une sauvegarde manuelle 
- Créer une stratégie de sauvegarde personnalisée comportant plusieurs volumes et planifications 

## Ajouter une stratégie de sauvegarde

Ajoutez une stratégie de sauvegarde pour planifier automatiquement vos sauvegardes. Pour ajouter une stratégie de sauvegarde dédiée à votre appareil StorSimple, suivez les étapes suivantes dans le portail de gestion. Après avoir ajouté la stratégie, vous pouvez définir une planification (voir la section [Ajouter ou modifier une planification](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]


## Ajouter ou modifier une planification

Vous pouvez ajouter ou modifier une planification associée à une stratégie de sauvegarde existante sur votre appareil StorSimple. Pour ajouter ou modifier une planification, suivez la procédure suivante dans le portail de gestion.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## Supprimer une stratégie de sauvegarde

Pour supprimer une stratégie de sauvegarde sur votre appareil StorSimple, suivez la procédure suivante dans le portail de gestion.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## Exécuter une sauvegarde manuelle

Pour créer une sauvegarde à la demande (manuelle) pour un volume unique, suivez les étapes suivantes dans le portail de gestion.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## Créer une stratégie de sauvegarde personnalisée comportant plusieurs volumes et planifications

Pour créer une stratégie de sauvegarde personnalisée présentant plusieurs volumes et planifications, suivez les étapes suivantes dans le portail de gestion.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## Étapes suivantes

Découvrez comment gérer les sauvegardes StorSimple avec le [Gestionnaire d’instantanés StorSimple](https://msdn.microsoft.com/library/azure/dn772365.aspx).

<!---HONumber=July15_HO2-->