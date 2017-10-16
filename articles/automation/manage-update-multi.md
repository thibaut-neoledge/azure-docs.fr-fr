---
title: "Gérer les mises à jour pour plusieurs machines virtuelles Azure | Microsoft Docs"
description: "Intégrez des machines virtuelles Azure pour gérer les mises à jour."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Gérer les mises à jour pour plusieurs machines virtuelles Azure

La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles Azure.
Dans votre compte [Azure Automation](automation-offering-get-started.md), vous pouvez rapidement intégrer des machines virtuelles, évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour requises et passer en revue les résultats de déploiement pour vérifier que les mises à jour ont été appliquées avec succès sur toutes les machines virtuelles dans lesquelles la gestion des mises à jour est activée.

## <a name="prerequisites"></a>Composants requis

Pour effectuer les étapes décrites dans ce guide, vous avez besoin des éléments suivants :

* Un compte Azure Automation. Pour obtenir des instructions sur la création d’un compte d’identification Azure Automation, consultez [Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md).
* Une machine virtuelle Azure Resource Manager (non classique). Pour obtenir des instructions sur la création d’une machine virtuelle, consultez [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Activer la gestion des mises à jour pour les machines virtuelles Azure

1. Dans le portail Azure, ouvrez le compte Automation.
2. Sur le côté gauche de l’écran, sélectionnez **Gestion des mises à jour**.
3. En haut de l’écran, cliquez sur **Ajouter une machine virtuelle Azure**.
    ![Intégrer des machines virtuelles](./media/manage-update-multi/update-onboard-vm.png)
4. Sélectionnez une machine virtuelle à intégrer. L’écran **Activer la gestion des mises à jour** apparaît.
5. Cliquez sur **Enable**.

   ![Activer la gestion des mises à jour](./media/manage-update-multi/update-enable.png)

La gestion des mises à jour est activée pour votre machine virtuelle.

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît. Vous pouvez voir une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Pour installer les mises à jour, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions.
Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure les correctifs cumulatifs.

Planifiez un nouveau déploiement de mises à jour pour une ou plusieurs machines virtuelles en cliquant sur **Planifier le déploiement de la mise à jour** en haut de l’écran **Gestion des mises à jour**. Dans l’écran **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : spécifiez un nom unique pour identifier le déploiement de mises à jour.
* **Type de système d’exploitation** : sélectionnez Windows ou Linux.
* **Ordinateurs à mettre à jour** : sélectionnez les machines virtuelles que vous souhaitez mettre à jour.

  ![Sélectionner les machines virtuelles à mettre à jour](./media/manage-update-multi/update-select-computers.png)

* **Classification de la mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour inclura dans le déploiement. Les types de classification sont les suivants :
  * Mises à jour critiques
  * Mises à jour de sécurité
  * Correctifs cumulatifs
  * Packs de fonctionnalités
  * Service Packs
  * Mises à jour de définitions
  * Outils
  * Mises à jour
* **Paramètres de planification** : vous pouvez accepter la date et l’heure par défaut, qui est de 30 minutes après l’heure actuelle, ou spécifier un moment différent.
   Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique. Cliquez sur l’option Périodique sous Périodicité pour définir une planification périodique.

   ![Écran Paramètres de planification des mises à jour](./media/manage-update-multi/update-set-schedule.png)

* **Fenêtre de maintenance (en minutes)** : spécifiez la période de temps pendant laquelle le déploiement des mises à jour doit se produire.  Cela permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer**. Vous revenez ensuite au tableau de bord des états.
Notez que le tableau **Planifié** indique la planification de déploiement que vous avez créée.

> [!WARNING]
> Pour les mises à jour nécessitant un redémarrage, la machine virtuelle est automatiquement redémarrée.

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié est démarré, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans l’écran **Gestion des mises à jour**.
S’il est en cours d’exécution, son état est **En cours d’exécution**. Une fois le déploiement terminé, s’il est réussi, l’état passe à **Réussi**.
S’il existe un échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.

![État du déploiement des mises à jour ](./media/manage-update-multi/update-view-results.png)

Cliquez sur le déploiement des mises à jour terminé pour voir le tableau de bord dédié à ce déploiement de mises à jour.

Dans la vignette **Résultats des mises à jour**, vous pouvez voir un récapitulatif du nombre total de mises à jour et les résultats du déploiement sur la machine virtuelle.
Dans le tableau de droite se trouve une répartition détaillée de chaque mise à jour et les résultats de l’installation, soit l’une des valeurs suivantes :

* Aucune tentative effectuée : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* Réussi : la mise à jour a réussi.
* Échec : la mise à jour a échoué.

Cliquez sur **Tous les journaux** pour afficher toutes les entrées de journal créées par le déploiement.

Cliquez sur la vignette **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Cliquez sur **Erreurs** pour afficher les informations détaillées sur les erreurs du déploiement.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la gestion des mises à jour, consultez la rubrique [Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md).