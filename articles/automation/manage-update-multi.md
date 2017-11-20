---
title: "Gérer les mises à jour pour plusieurs machines virtuelles Azure | Microsoft Docs"
description: "Cette rubrique décrit la gestion des mises à jour pour les machines virtuelles Azure."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: f97b28d1588e959728163f7ab16d2550a79f610e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Gérer les mises à jour pour plusieurs ordinateurs

La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos ordinateurs Windows et Linux.
Dans votre compte [Azure Automation](automation-offering-get-started.md), vous pouvez rapidement intégrer des machines, évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour requises et passer en revue les résultats de déploiement pour vérifier que les mises à jour ont été appliquées avec succès sur toutes les machines virtuelles dans lesquelles la gestion des mises à jour est activée.

## <a name="prerequisites"></a>Composants requis

Pour utiliser la gestion de la mise à jour, vous devez avoir :

* Un compte Azure Automation. Pour obtenir des instructions sur la création d’un compte d’identification Azure Automation, consultez [Prise en main d’Azure Automation](automation-offering-get-started.md).

* Une machine virtuelle ou un ordinateur virtuel avec l’un des systèmes d’exploitation pris en charge.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

La gestion de la mise à jour est prise en charge par les systèmes d’exploitation suivants.

### <a name="windows"></a>Windows

* Windows Server 2008 et versions ultérieures et déploiements de mise à jour par rapport à Windows Server 2008 R2 SP1 et versions ultérieures.  Les options d’installation Server Core et Nano Server ne sont pas prises en charge.

    > [!NOTE]
    > La prise en charge du déploiement des mises à jour vers Windows Server 2008 R2 SP1 nécessite .NET Framework 4.5 et WMF 5.0 ou une version ultérieure.
    > 
* Les systèmes d’exploitation clients Windows ne sont pas pris en charge.

Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services) ou avoir accès à Microsoft Update.

> [!NOTE]
> L’agent Windows ne peut pas être géré simultanément par System Center Configuration Manager.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) et 7 (x 64)  
* Red Hat Enterprise 6 (x86/x64) et 7 (x 64)  
* SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)  
* Ubuntu 12.04 LTS et x86/x64 plus récente   

> [!NOTE]  
> Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations sur cette configuration, consultez la [rubrique Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Les agents Linux doivent avoir accès à un référentiel de mise à jour.

> [!NOTE]
> Un agent OMS pour Linux configuré pour rapporter à plusieurs espaces de travail OMS n’est pas pris en charge avec cette solution.  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Activer la gestion des mises à jour pour les machines virtuelles Azure

1. Dans le portail Azure, ouvrez le compte Automation.
2. Sur le côté gauche de l’écran, sélectionnez **Gestion des mises à jour**.
3. En haut de l’écran, cliquez sur **Ajouter une machine virtuelle Azure**.
    ![Intégrer des machines virtuelles](./media/manage-update-multi/update-onboard-vm.png)
4. Sélectionnez une machine virtuelle à intégrer. L’écran **Activer la gestion des mises à jour** apparaît.
5. Cliquez sur **Enable**.

   ![Activer la gestion des mises à jour](./media/manage-update-multi/update-enable.png)

La gestion des mises à jour est activée pour votre machine virtuelle.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Activer la gestion des mises à jour pour les machines et ordinateurs virtuels non Azure

Pour obtenir des instructions sur l’activation de la gestion de la mise à jour pour les machines et ordinateurs virtuels Windows non Azure, consultez [Connecter des ordinateurs Windows au service Log Analytics dans Azure](../log-analytics/log-analytics-windows-agents.md).

Pour obtenir des instructions sur l’activation de la gestion de la mise à jour pour les machines et ordinateurs virtuels Linux non Azure, consultez [Connecter des ordinateurs Linux à Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît. Vous pouvez voir une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

## <a name="data-collection"></a>Collecte des données

Les agents installés sur des machines et ordinateurs virtuels collectent des données sur les mises à jour et les envoient à la gestion de la mise à jour Azure.

### <a name="supported-agents"></a>Agents pris en charge

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Pris en charge | Description |
| --- | --- | --- |
| Agents Windows |Oui |La gestion de la mise à jour collecte des informations sur les mises à jour système des agents et lance l’installation des mises à jour obligatoires. |
| Agents Linux |Oui |La gestion de la mise à jour collecte des informations sur les mises à jour système des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |Oui |La gestion de la mise à jour collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté. |
| Compte Azure Storage |Non |Le stockage Azure n’inclut aucune information sur les mises à jour du système. |

### <a name="collection-frequency"></a>Fréquence de collecte

Pour chaque ordinateur Windows géré, une analyse est effectuée deux fois par jour. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé et si une analyse de conformité est lancée.  Pour chaque ordinateur Linux géré, une analyse est effectuée toutes les 3 heures.

L’affichage des données mises à jour des ordinateurs gérés dans le tableau de bord peut prendre de 30 minutes à 6 heures.

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

Pour obtenir des informations détaillées sur les journaux, les sorties et des informations sur les erreurs, consultez [Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la gestion des mises à jour, consultez la rubrique [Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md).
