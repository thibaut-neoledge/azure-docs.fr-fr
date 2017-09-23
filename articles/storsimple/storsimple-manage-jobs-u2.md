---
title: "Affichage et gestion des tâches StorSimple | Microsoft Docs"
description: "Décrit la page Tâches du service StorSimple Manager et explique comment l’utiliser pour effectuer le suivi des tâches de sauvegarde planifiées, actuelles et récentes."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: cdd3e9e8-7ccd-40a3-8c07-0ab1338c0e59
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6df1b27ce76de7a781ecc40af8430114d80b20d6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>Utilisez le service StorSimple Manager pour afficher et gérer les tâches StorSimple (Mise à jour 2)
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Vue d'ensemble
La page **Tâches** est un portail centralisé unique qui permet de consulter et de gérer les tâches qui ont été lancées sur les appareils connectés à votre service StorSimple Manager. Vous pouvez consulter les tâches planifiées, en cours d'exécution, terminées, annulées et en échec pour plusieurs appareils. Les résultats sont présentés sous forme de tableau. 

![Page Tâches](./media/storsimple-manage-jobs-u2/jobs.png)

Vous pouvez rechercher rapidement les tâches qui vous intéressent en filtrant sur les champs, à savoir :

* **État** : les tâches peuvent être en cours d'exécution, terminées, annulées, en échec, en cours d'annulation ou terminées avec des erreurs.
* **De et À** : les tâches peuvent être filtrées selon la date et l'heure.
* **Type** : le type de tâche peut être sauvegarde, sauvegarde manuelle, restauration, clonage, basculement d'appareil, créer un volume épinglé localement, modifier le volume, mettre à jour, prendre en charge le package ou approvisionnement de l'appareil virtuel.
* **Appareils** : les tâches sont initiées sur un certain appareil connecté à votre service.
  Les tâches filtrées sont ensuite affichées sous forme de tableau sur la base des attributs suivants :
  
  * **Type** : sauvegarde, sauvegarde manuelle, restauration, clonage, basculement d'appareil, créer un volume épinglé localement, modifier le volume, mettre à jour, prendre en charge le package ou approvisionnement de l'appareil virtuel.
  * **État** : en cours d'exécution, terminées, annulées, en échec, en cours d'annulation ou terminées avec des erreurs.
  * **Entité** : les tâches peuvent être associées à un volume, une stratégie de sauvegarde ou un appareil. Par exemple, une tâche de clonage est associée à un volume, tandis qu'une tâche de sauvegarde planifiée est associée à une stratégie de sauvegarde. Une tâche d’appareil est créée à la suite d’une récupération d'urgence ou d’une opération de restauration.
  * **Appareil** : nom de l'appareil sur lequel la tâche a été lancée.
  * **Démarré le** : heure à laquelle la tâche a été lancée.
  * **Progression** : pourcentage d'achèvement d'une tâche en cours d'exécution. Pour une tâche terminée, le pourcentage doit toujours être de 100 %.

La liste des tâches est actualisée toutes les 30 secondes.

Cette page vous permet d’effectuer diverses actions liées aux tâches, à savoir :

* Affichage des détails d’une tâche
* Annulation d’une tâche

## <a name="view-job-details"></a>Affichage des détails d’une tâche
Pour afficher les détails d’une tâche, procédez comme suit.

#### <a name="to-view-job-details"></a>Pour afficher les détails d’une tâche
1. Dans la page **Tâches** , affichez la ou les tâches qui vous intéressent en exécutant une requête avec les filtres appropriés. Vous pouvez rechercher des tâches terminées, en cours d'exécution ou annulées.
2. Sélectionnez une tâche.
3. En bas de la page, cliquez sur **Détails**.
4. Dans la boîte de dialogue **Détails de la tâche de sauvegarde** , vous pouvez consulter l'état, les détails, les statistiques temporelles et les statistiques de données.
   
    ![Page Détails de la tâche](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Annulation d’une tâche
Pour annuler une tâche en cours d’exécution, procédez comme suit.

> [!NOTE]
> Certaines tâches, telles que la modification d'un volume pour modifier le type de volume ou l'extension d'un volume, ne peuvent pas être annulées.
> 
> 

### <a name="to-cancel-a-job"></a>Pour annuler une tâche
1. Dans la page **Tâches** , affichez les tâches en cours d’exécution que vous voulez annuler en exécutant une requête avec les filtres appropriés.
2. Sélectionnez la tâche.
3. En bas de la page, cliquez sur **Annuler**.
4. Cliquez sur **Oui**lorsque vous êtes invité à confirmer l’opération.

Cette tâche est à présent annulée.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [gestion de vos stratégies de sauvegarde StorSimple](storsimple-manage-backup-policies.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).


