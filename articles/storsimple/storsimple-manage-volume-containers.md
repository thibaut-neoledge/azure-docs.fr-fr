---
title: "Gérer vos conteneurs de volumes StorSimple | Microsoft Docs"
description: "Explique comment utiliser la page des conteneurs de volumes du service StorSimple Manager pour ajouter, modifier ou supprimer un conteneur de volumes."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: bb55a7a4bff0fd4319de6f6ce958686ad8a4142b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Utiliser le service StorSimple Manager pour gérer les conteneurs de volumes StorSimple
## <a name="overview"></a>Vue d’ensemble
Ce didacticiel explique comment utiliser le service StorSimple Manager pour créer et gérer des conteneurs de volumes StorSimple.

Un conteneur de volumes créé dans un appareil Microsoft Azure StorSimple contient un ou plusieurs volumes hébergeant les paramètres de consommation du compte de stockage, du chiffrement et de la bande passante. Un appareil peut comporter plusieurs conteneurs de volumes pour l’ensemble de ses volumes. 

Un conteneur de volumes présente les attributs suivants :

* **Volumes** : les volumes StorSimple à plusieurs niveaux ou épinglés en local et hébergés dans le conteneur de volumes. Un conteneur de volumes peut contenir jusqu’à 256 volumes StorSimple.
* **Chiffrement** : clé de chiffrement pouvant être définie pour chaque conteneur de volumes. Cette clé est utilisée pour chiffrer les données envoyées depuis votre appareil StorSimple sur le cloud. Une clé AES 256 bits de niveau militaire est utilisée avec la clé saisie par l’utilisateur. Pour sécuriser vos données, nous vous recommandons de toujours activer le chiffrement du stockage cloud.
* **Compte de stockage** : le compte de stockage qui est lié à votre fournisseur de services de stockage cloud. L’ensemble des volumes hébergés dans votre conteneur de volumes partagent ce compte de stockage. Vous pouvez choisir un compte de stockage à partir d’une liste existante ou créer un compte lors de la création du conteneur de volumes, avant de spécifier les informations d’identification d’accès associées à ce compte.
* **Bande passante du cloud** : consommation de bande passante de l’appareil lors de l’envoi de ses données vers le cloud. Vous pouvez appliquer un contrôle de bande passante en définissant une valeur comprise entre 1 et 1 000 Mbits/s lorsque vous définissez ce conteneur. Si vous souhaitez que l’appareil consomme toute la bande passante disponible, définissez la valeur llimité dans ce champ. Vous pouvez également créer et appliquer un modèle de bande passante, afin d’allouer la bande passante suivant une planification.

![Page des conteneurs de volumes](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Les procédures suivantes expliquent comment utiliser la page **Conteneurs de volume** StorSimple pour effectuer les opérations courantes ci-dessous :

* Ajouter un conteneur de volumes 
* Modifier un conteneur de volumes 
* Supprimer un conteneur de volumes 

## <a name="add-a-volume-container"></a>Ajouter un conteneur de volumes
Pour ajouter un conteneur de volumes, procédez comme suit.

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modifier un conteneur de volumes
Pour modifier un conteneur de volumes, procédez comme suit.

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Supprimer un conteneur de volumes
Un conteneur de volumes comporte plusieurs volumes. Il peut être supprimé uniquement après la suppression de l’ensemble des volumes qu’il héberge. Pour supprimer un conteneur de volumes, procédez comme suit.

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [gestion des volumes StorSimple](storsimple-manage-volumes.md). 
* En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).


