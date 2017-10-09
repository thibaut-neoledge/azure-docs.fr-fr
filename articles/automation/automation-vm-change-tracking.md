---
title: Suivi les modifications sur des machines virtuelles Azure | Microsoft Docs
description: Utiliser le suivi des modifications pour suivre les modifications dans les fichiers et le Registre des machines virtuelles
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>Suivi les modifications sur des machines virtuelles Azure

L’activation du suivi des modifications permet de suivre les modifications apportées aux fichiers et clés de Registre Windows des machines virtuelles. . Identifier les modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Vous pouvez activer le suivi des modifications directement à partir de votre machine virtuelle Azure.

Si vous ne disposez pas de machine virtuelle Azure, vous pouvez en créer une à l’aide du [guide de démarrage rapide Windows](../virtual-machines/windows/quick-create-portal.md) ou du [guide de démarrage rapide Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Activer le suivi des modifications pour une machine virtuelle Azure

1. Sur le côté gauche de l’écran, sélectionnez **Machines virtuelles**.
1. Sélectionnez la machine virtuelle dans la liste.
1. Dans l’écran de la machine virtuelle, dans la section **Operations**, cliquez sur **Suivi des modifications**. L’écran **Activer la gestion des mises à jour** s’ouvre.

   ![Suivi des modifications sur une machine virtuelle intégrée](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

Une validation est effectuée pour déterminer si le suivi des modifications est activé pour cette machine virtuelle. S’il est activé, une bannière apparaît et vous donne la possibilité d’activer la solution.

   ![Bannière de configuration intégrée du suivi des modifications](./media/automation-vm-change-tracking/change-onboard-banner.png)

Cliquez sur la bannière pour activer la solution. Si vous ne disposez pas des éléments suivants, ils sont ajoutés automatiquement :

* Espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md)
* Compte [Automation](../automation/automation-offering-get-started.md)

Sélectionnez un espace de travail Log Analytics pour stocker les journaux de données de suivi des modifications et un compte Automation pour suivre les modifications, puis cliquez sur **Activer**.

Une barre d’état vous informe que la solution est en cours d’activation. Ce processus peut prendre jusqu’à 15 minutes.

## <a name="configure-change-tracking"></a>Configurer le suivi des modifications

Une fois activé le suivi des modifications, l’écran **Suivi des modifications** s’affiche. Sélectionnez les fichiers et les clés de Registre à suivre en cliquant sur **Modifier les paramètres**.

![Modification des paramètres de suivi des modifications](./media/automation-vm-change-tracking/change-edit-settings.png)

L’écran **Configuration de l’espace de travail** s’ouvre. Cliquez sur **Ajouter** dans l’onglet approprié pour ajouter les clés de Registre Windows, les fichiers Windows ou les fichiers Linux à suivre.

## <a name="add-a-windows-registry-key"></a>Ajouter une clé de Registre Windows

1. Dans l’onglet **Registre Windows** de l’écran **Configuration de l’espace de travail**, cliquez sur **Ajouter**. L’écran **Ajouter le Registre Windows pour le suivi des modifications** s’ouvre.

   ![Ajout d’un Registre pour le suivi des modifications](./media/automation-vm-change-tracking/change-add-registry.png)

1. Sous **Activé**, sélectionnez **True**.
1. Entrez un nom convivial dans le champ **Nom de l’élément**.
1. Dans le champ **Groupe**, entrez un nom de groupe (facultatif).
1. Dans le champ **Clé de Registre Windows**, entrez le nom de la clé de Registre que vous souhaitez suivre.
1. Cliquez sur **Enregistrer**.

## <a name="add-a-windows-file"></a>Ajouter un fichier Windows

1. Dans l’onglet **Fichiers Windows** de l’écran **Configuration de l’espace de travail**, cliquez sur **Ajouter**. L’écran **Ajouter le fichier Windows pour le suivi des modifications** s’ouvre.

   ![Ajout d’un fichier Windows pour le suivi des modifications](./media/automation-vm-change-tracking/change-add-win-file.png)

1. Sous **Activé**, sélectionnez **True**.
1. Entrez un nom convivial dans le champ **Nom de l’élément**.
1. Dans le champ **Groupe**, entrez un nom de groupe (facultatif).
1. Dans le champ **Entrer le chemin**, indiquez le chemin d’accès complet et le nom du fichier que vous souhaitez suivre.
1. Cliquez sur **Enregistrer**.

## <a name="add-a-linux-file"></a>Ajouter un fichier Linux

1. Dans l’onglet **Fichiers Linux** de l’écran **Configuration de l’espace de travail**, cliquez sur **Ajouter**. L’écran **Ajouter le fichier Linux pour le suivi des modifications** s’ouvre.

   ![Ajout d’un fichier Linux pour le suivi des modifications](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. Sous **Activé**, sélectionnez **True**.
1. Entrez un nom convivial dans le champ **Nom de l’élément**.
1. Dans le champ **Groupe**, entrez un nom de groupe (facultatif).
1. Dans le champ **Entrer le chemin**, indiquez le chemin d’accès complet et le nom du fichier que vous souhaitez suivre.
1. Dans le champ **Type de chemin**, sélectionnez **Fichier** ou **Répertoire**.
1. Sous **Récursivité**, sélectionnez **Activé** pour suivre les modifications apportées au chemin d’accès spécifié et à tous les fichiers et chemins d’accès qui se trouvent sous ce dernier. Pour suivre uniquement le chemin d’accès ou fichier sélectionné, sélectionnez **Désactivé**.
1. Sous **Sudo**, sélectionnez **Activé** pour suivre les fichiers qui nécessitent la commande `sudo` pour y accéder. Dans le cas contraire, sélectionnez **Désactivé**.
1. Cliquez sur **Enregistrer**.

## <a name="view-changes"></a>Afficher les modifications

L’écran **Suivi des modifications** affiche les modifications dans chacune des catégories de la machine virtuelle au fil du temps.

   ![Affichage du suivi des modifications](./media/automation-vm-change-tracking/change-view-changes.png)

Vous pouvez sélectionner les catégories et l’intervalle de temps des modifications à afficher. En haut de l’écran se trouve une vue graphique des modifications au fil du temps.
En bas de l’écran s’affiche la liste des modifications récentes.

## <a name="view-change-tracking-log-data"></a>Afficher les données du journal de suivi des modifications

Le suivi des modifications génère des données de journal qui sont envoyées à Log Analytics. Pour rechercher les journaux en exécutant des requêtes, cliquez sur **Log Analytics** en haut de l’écran **Suivi des modifications**.

   ![Log Analytics pour le suivi des modifications](./media/automation-vm-change-tracking/change-log-analytics.png)

Pour en savoir plus sur la recherche de fichiers journaux dans Log Analytics, consultez [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le suivi des modifications, consultez [Suivi des modifications](../log-analytics/log-analytics-change-tracking.md).
* Pour en savoir plus sur la gestion des mises à jour pour les machines virtuelles, consultez [Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md).

