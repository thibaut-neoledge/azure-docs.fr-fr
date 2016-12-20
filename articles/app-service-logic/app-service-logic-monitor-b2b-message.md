---
title: Surveiller des messages B2B | Microsoft Docs
description: "Comment surveiller un compte d’intégration"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>Surveiller des messages B2B
La communication B2B implique des échanges de messages entre deux processus ou applications métier en cours d’exécution. La relation définit un accord entre les processus métier. Une fois la communication établie, il doit exister un moyen de contrôler si elle se produit comme prévu.  Le suivi des messages a été implémenté pour les protocoles B2B : AS2, X12 et EDIFACT.  Vous pouvez configurer votre compte d’intégration pour utiliser les diagnostics afin de bénéficier de davantage de détails et d’un meilleur débogage

## <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration (vous pouvez créer un [compte d’intégration](app-service-logic-enterprise-integration-create-integration-account.md))
* Une application logique (vous pouvez créer une [application logique](app-service-logic-create-a-logic-app.md) et activer la journalisation en suivant les étapes présentées [ici](app-service-logic-monitor-your-logic-apps.md))

## <a name="enable-logging-for-an-integration-account"></a>Activer la journalisation pour un compte d’intégration
Vous pouvez activer la journalisation pour un compte d’intégration via le **portail Azure** ou **Monitor**

### <a name="enable-logging-with-azure-portal"></a>Activation de la journalisation avec le portail Azure

1. Sélectionnez **Compte d’intégration**, puis **Journaux de diagnostic** 
![Sélectionner un compte d’intégration](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. Sélectionnez votre **abonnement** et votre **groupe de ressources**, **Comptes d’intégration** dans Type de ressource, puis sélectionnez votre **compte d’intégration** dans la liste déroulante de ressources pour activer les diagnostics.  Cliquez sur **Activer les diagnostics** pour activer les diagnostics pour le compte d’intégration sélectionné               
![sélection du compte d’intégration](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. Sélectionnez l’option **ACTIVÉ**       
![Activer les diagnostics](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Sélectionnez **Envoyer à Log Analytics** et configurez Log Analytics pour émettre des données               
![Activer les diagnostics](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>Activation de la journalisation avec Monitor

1. Sélectionnez **Monitor** et cliquez sur **Journaux de diagnostic**   
![sélection de Monitor](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. Sélectionnez votre **abonnement** et votre **groupe de ressources**, **Comptes d’intégration** dans Type de ressource, puis sélectionnez votre **compte d’intégration** dans la liste déroulante de ressources pour activer les diagnostics.  Cliquez sur **Activer les diagnostics** pour activer les diagnostics pour le compte d’intégration sélectionné               
![sélection du compte d’intégration](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. Sélectionnez l’option **ACTIVÉ**       
![Activer les diagnostics](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Sélectionnez **Envoyer à Log Analytics** et configurez Log Analytics pour émettre des données ![Activer les diagnostics](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>Extension de vos solutions
Outre **Log Analytics**, vous pouvez configurer votre compte d’intégration et vos [applications logiques](./app-service-logic-monitor-your-logic-apps.md) sur un Event Hub ou un compte de stockage         
![Paramètres des diagnostics Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

Vous pouvez utiliser ces données de télémétrie de l’Event Hub ou du compte de stockage dans d’autres services comme [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) et [Power BI](https://powerbi.com) pour bénéficier de l’analyse en temps réel de vos flux de travail d’intégration.

## <a name="supported-tracking-schema"></a>Schéma de suivi pris en charge
Nous prenons en charge les types de schéma de suivi ci-dessous.  Tous comportent des schémas fixes, à l’exception du type Personnalisé.

* [Schéma de suivi personnalisé](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [Schéma de suivi AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Schéma de suivi X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>Étapes suivantes
[Suivi des messages B2B dans le portail OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


