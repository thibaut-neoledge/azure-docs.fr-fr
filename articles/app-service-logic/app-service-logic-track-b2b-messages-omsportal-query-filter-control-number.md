---
title: Suivre des messages B2B dans le portail OMS | Microsoft Docs
description: Suivi des messages B2B dans le portail OMS
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
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>Créer une requête dans le portail OMS 
Étapes pour créer une requête qui filtre les données pour un numéro de contrôle d’échange spécifique

## <a name="prerequisites"></a>Composants requis

Activez les diagnostics pour votre [compte d’intégration](app-service-logic-monitor-b2b-message.md) et vos [applications logiques](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) dotés de 12 connecteurs pour des informations et un débogage plus précis.  Suivez les étapes [ici](app-service-logic-track-b2b-messages-omsportal.md) pour publier des données de diagnostic sur le portail OMS.

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>Créer une requête qui recherche les données pour un numéro de contrôle d’échange spécifique

1. Sélection de la **Recherche de journaux** sur la page d’accueil  
![Sélectionner la recherche de journaux](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Saisissez **Type="AzureDiagnostics"** dans la fenêtre de recherche pour une extraction de toutes les données.  Cliquez sur **Ajouter** pour filtrer les données  
![Sélectionner la requête](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Saisissez **échange**, sélectionnez **event_record_messageProperties_interchangeControlNumber_s**, puis cliquez sur **Ajouter**  
![Sélectionner le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Sélectionnez le numéro de contrôle souhaité pour filtrer les données et cliquez sur **Appliquer**  
![Sélectionner le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Vous pouvez trouver la requête créée pour filtrer les données pour le numéro de contrôle sélectionné   
![Sélectionner le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Donnez un nom à la requête et enregistrez-la   
![Sélectionner le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. Sélectionnez les **favoris** pour afficher la requête et l’utiliser pour une recherche ultérieure  
![Sélectionner le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. La requête peut être mise à jour pour rechercher un nouveau numéro de contrôle d’échange  
![Sélectionner le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>Étapes suivantes
[Schéma de suivi personnalisé](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Schéma de suivi AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Schéma de suivi X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


