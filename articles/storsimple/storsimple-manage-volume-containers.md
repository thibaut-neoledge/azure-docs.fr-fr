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
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 64f88e27267b36ea8654093b6e725e942f5f5c1c
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Utiliser le service StorSimple Manager pour gérer les conteneurs de volumes StorSimple
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Pour afficher la version de cet article applicable au nouveau portail Azure, accédez à [Utiliser le service StorSimple Manager pour gérer les conteneurs de volumes StorSimple](storsimple-8000-manage-volume-containers.md). Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Vue d'ensemble
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

