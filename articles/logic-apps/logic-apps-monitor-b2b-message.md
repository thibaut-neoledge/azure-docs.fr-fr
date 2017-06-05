---
title: "Surveiller les messages dans les transactions B2B - Azure Logic Apps | Microsoft Docs"
description: "Surveillez les messages et effectuez leur suivi durant les communications B2B entre les processus et les applications, à l’aide de Logic Apps dans votre compte d’intégration."
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
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.contentlocale: fr-fr
ms.lasthandoff: 03/01/2017

---

# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>Démarrage ou activation de la journalisation des messages AS2, X12 et EDIFACT pour surveiller la réussite, les erreurs et les propriétés de message

La communication B2B implique des échanges de messages entre deux processus ou applications métier en cours d’exécution. La relation définit un accord entre les processus métier. Une fois la communication établie, vous pouvez définir la surveillance des messages afin de vous assurer du bon fonctionnement de la communication. Pour plus de détails et pour bénéficier d’un meilleur débogage, définissez les diagnostics pour votre compte d’intégration.

Le suivi des messages est disponible pour ces protocoles B2B : AS2, X12 et EDIFACT. 

## <a name="prerequisites"></a>Composants requis

* Un compte Azure (vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)).
* Un compte d’intégration (vous pouvez créer un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md)).
* Une application logique (vous pouvez créer une [application logique](logic-apps-create-a-logic-app.md) et [activer la journalisation](logic-apps-monitor-your-logic-apps.md)).

## <a name="enable-logging-for-an-integration-account"></a>Activer la journalisation pour un compte d’intégration

Vous pouvez activer la journalisation pour un compte d’intégration via le **portail Azure** ou **Monitor**.

### <a name="enable-logging-with-azure-portal"></a>Activation de la journalisation avec le portail Azure

1. Sélectionnez votre compte d’intégration, puis **Journaux de diagnostic**.

    ![Sélectionnez votre compte d’intégration](media/logic-apps-monitor-b2b-message/pic5.png)

2. Sélectionnez votre **Abonnement** et votre **Groupe de ressources**. À partir de **Type de ressource**, sélectionnez **Comptes d'intégration**. À partir de **Ressource**, sélectionnez votre compte d’intégration. Cliquez sur **Activer les diagnostics** afin d’activer les diagnostics pour votre compte d’intégration sélectionné.

    ![Configurer les diagnostics pour votre compte d’intégration](media/logic-apps-monitor-b2b-message/pic2.png)

3. Sélectionnez l’option **ACTIVÉ**.

    ![Activer l’état de diagnostic](media/logic-apps-monitor-b2b-message/pic3.png)

4. Sélectionnez **Envoyer à Log Analytics** et configurez Log Analytics pour émettre des données.

    ![Envoyer des données de diagnostic dans un journal](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>Activation de la journalisation avec Monitor

0. Sélectionnez **Monitor**, **Journaux de diagnostics**.

    ![Sélectionnez Monitor, Journaux de diagnostics.](media/logic-apps-monitor-b2b-message/pic1.png)

0. Sélectionnez votre **Abonnement** et votre **Groupe de ressources**. À partir de **Type de ressource**, sélectionnez **Comptes d'intégration**. À partir de **Ressource**, sélectionnez votre compte d’intégration. Cliquez sur **Activer les diagnostics** afin d’activer les diagnostics pour votre compte d’intégration sélectionné.

    ![Configurer les diagnostics pour votre compte d’intégration](media/logic-apps-monitor-b2b-message/pic2.png)

0. Sélectionnez l’option **ACTIVÉ**.

    ![Activer l’état de diagnostic](media/logic-apps-monitor-b2b-message/pic3.png) 

0. Sélectionnez **Envoyer à Log Analytics** et configurez **Log Analytics** pour émettre des données.

    ![Envoyer des données de diagnostic dans un journal](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>Étendre vos solutions

Outre **Log Analytics**, vous pouvez configurer votre compte d’intégration et vos [applications logiques](./logic-apps-monitor-your-logic-apps.md) sur un concentrateur d’événements ou un compte de stockage.

![Paramètres des diagnostics Azure](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

Vous pouvez utiliser ces données de télémétrie du concentrateur d’événement ou du compte de stockage dans d’autres services comme [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) et [Power BI](https://powerbi.com) pour bénéficier de l’analyse en temps réel de vos flux de travail d’intégration.

## <a name="supported-tracking-schema"></a>Schéma de suivi pris en charge

Nous prenons en charge ces types de schéma de suivi, qui sont tous fixes, à l’exception du type personnalisé.

* [Schéma de suivi personnalisé](logic-apps-track-integration-account-custom-tracking-schema.md)
* [Schéma de suivi AS2](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes

[Suivi des messages B2B dans le portail OMS](logic-apps-track-b2b-messages-omsportal.md "Suivi des messages B2B")

[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")


