---
title: "Gérer une machine virtuelle Azure avec la collecte d’inventaire | Microsoft Docs"
description: "Gérer une machine virtuelle avec la collecte d’inventaire"
services: automation
keywords: inventaire, automatisation, suivi des modifications
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gérer une machine virtuelle Azure avec la collecte d’inventaire

Le suivi d’inventaire peut être activé pour une machine virtuelle Azure à partir de la page de ressources de la machine. Cette méthode fournit une interface utilisateur basée sur un navigateur pour la configuration de la collecte d’inventaire.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
Si vous n’avez pas de machine virtuelle Azure, créez une [machine virtuelle](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Activer la collecte d’inventaire à partir de la page de ressources de la machine virtuelle

1. À gauche de l’écran, sélectionnez **Machines virtuelles**.
1. Dans la liste, sélectionnez une machine virtuelle.
1. Sélectionnez **Inventory (préversion)** dans le menu de ressource sous **Opérations**.
1. Une bannière apparaît en haut de la page pour vous informer que la solution n’est pas activée. Cliquez sur la bannière pour activer la solution.
1. Sélectionnez un espace de travail Log Analytics pour stocker vos journaux de données. Si aucun espace de travail n’est disponible pour cette région, vous êtes invité à créer un espace de travail par défaut et un compte Automation. Cliquez sur **Activer** pour démarrer l’intégration de votre ordinateur.

   ![Afficher les options d’intégration](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Une barre d’état vous informe que la solution est en cours d’activation. Ce processus peut prendre jusqu’à 15 minutes. Pendant ce temps, vous pouvez fermer le panneau ou le laisser ouvert, puis vous serez informé lorsque la solution sera activée. Vous pouvez surveiller l’état du déploiement dans le volet des notifications.

   ![Afficher la solution d’inventaire immédiatement après l’intégration](./media/automation-vm-inventory/inventory-onboarded.png)

1. Lorsque le déploiement est terminé, la barre d’état disparaît. À ce stade, le système est toujours en cours de collecte des données d’inventaire et les données peuvent ne pas encore être visibles. Une collecte complète des données peut prendre 24 heures.

## <a name="configure-your-inventory-settings"></a>Configurer les paramètres d’inventaire

Par défaut, les logiciels, les services Windows et les démons Linux sont configurés pour la collecte. Pour collecter l’inventaire des fichiers et registres Windows, configurez les paramètres de collecte d’inventaire.

1. Dans la vue **Inventory (préversion)**, sélectionnez le bouton **Modifier les paramètres** en haut de la page.
2. Pour ajouter un nouveau paramètre de collecte, accédez à la catégorie de paramètre que vous souhaitez ajouter à l’aide des onglets **Registre Windows**, **Fichiers Windows** et **Fichiers Linux**. Cliquez sur **Ajouter** en haut de la page.
3. Pour afficher les détails et les descriptions de chaque propriété de paramètre, consultez la [page de documentation Inventory](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>Déconnexion de la machine virtuelle de la gestion

Pour supprimer votre machine de la gestion de l’inventaire, procédez comme suit :

1. Dans le menu de gauche dans le portail Azure, cliquez sur **Log Analytics**, puis sélectionnez l’espace de travail que vous avez utilisé lors de l’intégration de votre machine virtuelle.
1. Sur votre page Log Analytics, sélectionnez **Machines virtuelles** sous la catégorie **Sources de données de l’espace de travail** dans le menu de la ressource. 
1. Sélectionnez la machine virtuelle que vous souhaitez supprimer de la liste. Une coche verte s’affiche en regard du texte indiquant « Cet espace de travail » dans la colonne **Connexion OMS**. Cliquez sur **Déconnecter** en haut de la page suivante et sur **Oui** dans la boîte de dialogue de confirmation afin de déconnecter la machine virtuelle de la gestion.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la gestion des modifications dans les paramètres de registre et les fichiers sur vos machines virtuelles, consultez l’article relatif au [suivi des modifications](../log-analytics/log-analytics-change-tracking.md).
* Pour en savoir plus sur la gestion des mises à jour de package et Windows sur vos machines virtuelles, consultez l’article relatif à la [gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md)

