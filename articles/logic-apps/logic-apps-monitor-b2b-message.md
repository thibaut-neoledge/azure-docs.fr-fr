---
title: Surveiller les transactions B2B et configurer la journalisation - Azure Logic Apps | Microsoft Docs
description: "Surveiller les messages AS2, X12 et EDIFACT, démarrer la journalisation des diagnostics pour votre compte d’intégration"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 59aa8fc907d68485b7d78ae7466e2d2298d7d7d6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---

# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Surveiller et configurer la journalisation des diagnostics pour la communication B2B dans des comptes d’intégration

Une fois la communication B2B configurée entre deux processus ou applications d’entreprise en cours d’exécution via votre compte d’intégration, ces entités peuvent échanger des messages entre elles. Pour vérifier que cette communication fonctionne comme prévu, vous pouvez configurer la surveillance des messages AS2, X12 et EDIFACT, ainsi que la journalisation des diagnostics pour votre compte d’intégration via le service [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Ce service dans [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) surveille vos environnements cloud et local pour vous aider à maintenir leur disponibilité et leurs performances, et collecte des détails d’exécution et des événements pour un débogage enrichi. Vous pouvez également [utiliser vos données de diagnostic avec d’autres services](#extend-diagnostic-data), tels que Stockage Azure et Azure Event Hubs.

## <a name="requirements"></a>Configuration requise

* Une application logique configurée avec une journalisation des diagnostics. Découvrez comment [configurer la journalisation pour cette application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Une fois cette condition remplie, vous devez disposer d’un espace de travail dans [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Vous devez utiliser l’espace de travail OMS que vous avez utilisé quand vous avez configuré la journalisation pour votre compte d’intégration. Si vous n’avez pas d’espace de travail OMS, découvrez comment [créer un espace de travail OMS](../log-analytics/log-analytics-get-started.md).

* Un compte d’intégration lié à votre application logique. Découvrez comment [créer un compte d’intégration lié à votre application logique](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Activer la journalisation des diagnostics pour votre compte d’intégration

Vous pouvez activer la journalisation directement à partir de votre compte d’intégration ou [via le service Azure Monitor](#azure-monitor-service). Azure Monitor exerce une surveillance de base avec des données de niveau de l’infrastructure. En savoir plus sur [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Activer la journalisation des diagnostics directement à partir de votre compte d’intégration

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre compte d’intégration. Sous **Surveillance**, choisissez **Journaux de diagnostic** comme illustré ici :

   ![Rechercher et sélectionner votre compte d’intégration, choisissez « Journaux de diagnostic »](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Lorsque vous sélectionnez votre compte d’intégration, les valeurs suivantes sont automatiquement sélectionnées. Si ces valeurs sont correctes, choisissez **Activer les diagnostics**. Autrement, sélectionnez les valeurs souhaitées :

   1. Sous **Abonnement**, sélectionnez l’abonnement Azure que vous utilisez avec votre compte d’intégration.
   2. Sous **Groupe de ressources**, sélectionnez le groupe de ressources que vous utilisez avec votre compte d’intégration.
   3. Sous **Type de ressource**, sélectionnez **Comptes d'intégration**. 
   4. Sous **Ressource**, sélectionnez votre compte d’intégration. 
   5. Choisissez **Activer les diagnostics**.

   ![Configurer les diagnostics pour votre compte d’intégration](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Sous **Paramètres de diagnostic**, puis **État**, choisissez **Activé**.

   ![Activer Azure Diagnostics](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Sélectionner à présent l’espace de travail OMS et les données à utiliser pour la journalisation comme illustré :

   1. Sélectionnez **Envoyer à Log Analytics**. 
   2. Sous **Log Analytics**, choisissez **Configurer**. 
   3. Sous **Espaces de travail OMS**, sélectionnez l’espace de travail OMS à utiliser pour la journalisation.
   4. Sous **Journal**, sélectionnez la catégorie **IntegrationAccountTrackingEvents**.
   5. Choisissez **Enregistrer**.

   ![Configurer Log Analytics pour pouvoir envoyer des données de diagnostic à un journal](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. À présent, [configurez le suivi de vos messages B2B dans OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Activer la journalisation des diagnostics via Azure Monitor

1. Dans le [portail Azure](https://portal.azure.com), dans le menu principal Azure, choisissez **Surveiller**, **Journaux de diagnostic**. Sélectionnez ensuite votre compte d’intégration comme illustré ici :

   ![Choisir « Surveiller », « Journaux de diagnostic », sélectionner votre compte d’intégration](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Lorsque vous sélectionnez votre compte d’intégration, les valeurs suivantes sont automatiquement sélectionnées. Si ces valeurs sont correctes, choisissez **Activer les diagnostics**. Autrement, sélectionnez les valeurs souhaitées :

   1. Sous **Abonnement**, sélectionnez l’abonnement Azure que vous utilisez avec votre compte d’intégration.
   2. Sous **Groupe de ressources**, sélectionnez le groupe de ressources que vous utilisez avec votre compte d’intégration.
   3. Sous **Type de ressource**, sélectionnez **Comptes d'intégration**.
   4. Sous **Ressource**, sélectionnez votre compte d’intégration.
   5. Choisissez **Activer les diagnostics**.

   ![Configurer les diagnostics pour votre compte d’intégration](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Sous **Paramètres de diagnostic**, choisissez **Activé**.

   ![Activer Azure Diagnostics](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Sélectionnez à présent l’espace de travail OMS et la catégorie d'événement pour la journalisation comme illustré :

   1. Sélectionnez **Envoyer à Log Analytics**. 
   2. Sous **Log Analytics**, choisissez **Configurer**. 
   3. Sous **Espaces de travail OMS**, sélectionnez l’espace de travail OMS à utiliser pour la journalisation.
   4. Sous **Journal**, sélectionnez la catégorie **IntegrationAccountTrackingEvents**.
   5. Une fois ces opérations effectuées, sélectionnez **Enregistrer**.

   ![Configurer Log Analytics pour pouvoir envoyer des données de diagnostic à un journal](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. À présent, [configurez le suivi de vos messages B2B dans OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Étendre la manière dont vous utilisez les données de diagnostic et l’emplacement où vous les utilisez avec d’autres services

Avec Azure Log Analytics, vous pouvez étendre le mode d’utilisation des données de diagnostic de votre application logique avec d’autres services Azure, par exemple : 

* [Archivage des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Diffuser en continu les journaux de diagnostic vers Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Vous pouvez ensuite obtenir une surveillance en temps réel en utilisant les ressources de télémétrie et d’analyse d’autres services, tels que [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) et [Power BI](../log-analytics/log-analytics-powerbi.md). Par exemple :

* [Diffuser les données d’Event Hubs vers Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyser les données de diffusion avec Stream Analytics et créer un tableau de bord analytique en temps réel dans Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Selon les options que vous souhaitez configurer, veillez au préalable à [créer un compte de stockage Azure](../storage/storage-create-storage-account.md) ou à [créer un hub d’événements Azure](../event-hubs/event-hubs-create.md). Sélectionnez ensuite les options concernant l’emplacement où vous souhaitez envoyer les données de diagnostic :

![Envoyer des données à un compte de stockage ou à un hub d’événements Azure](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Les périodes de rétention s’appliquent uniquement lorsque vous choisissez d’utiliser un compte de stockage.

## <a name="supported-tracking-schemas"></a>Schémas de suivi pris en charge

Azure prend en charge les types de schémas de suivi ci-dessous, qui ont tous des schémas fixes, à l’exception du type personnalisé.

* [Schéma de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma de suivi X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schéma de suivi personnalisé](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* [Suivre les messages B2B dans OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Suivre les messages B2B dans OMS")
* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")


