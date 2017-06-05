---
title: "Effectuer le suivi des messages AS2, X12 et EDIFACT à l’aide d’une requête - Azure Logic Apps | Microsoft Docs"
description: "Utiliser des requêtes pour suivre les messages B2B dans le portail Operations Management Suite"
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
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 67313e4ff780e6c64dfc3d767ea49167c2a390f4
ms.openlocfilehash: 701a3bbadb340c9390b486a5295eced056db70a0
ms.contentlocale: fr-fr
ms.lasthandoff: 01/31/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Suivi de messages B2B dans le portail Operations Management Suite à l’aide d’une requête
Pour suivre des messages B2B dans le portail Operations Management Suite, vous pouvez créer une requête qui filtre les données pour un numéro de contrôle d’échange spécifique.

## <a name="prereqs"></a>Conditions préalables

Pour le débogage et des informations de diagnostic plus détaillées, activez les diagnostics dans votre [compte d’intégration](logic-apps-monitor-b2b-message.md) pour vos [applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) qui ont des connecteurs X12. Puis, effectuez les étapes de [publication des données de diagnostic](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) dans le portail Operations Management Suite.

## <a name="search-for-an-interchange-control-number"></a>Rechercher un numéro de contrôle d’échange

1. Sur la page de démarrage, sélectionnez **Recherche de journal**.  
![Sélectionner la recherche de journaux](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Dans la zone de recherche, entrez **Type="AzureDiagnostics"**. Pour filtrer les données, sélectionnez **Ajouter**.  
![Sélectionner la requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Saisissez **interchange**, sélectionnez **event_record_messageProperties_interchangeControlNumber_s**, puis sélectionnez **Ajouter**.  
![Ajouter un filtre](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Sélectionnez le numéro de contrôle pour lequel filtrer les données et cliquez sur **Appliquer**.  
![Rechercher le numéro de contrôle](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Recherchez la requête créée pour filtrer les données pour le numéro de contrôle sélectionné.   
![Rechercher la requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Entrez un nom pour la requête, puis enregistrez-la.   
![Enregistrer la requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Pour afficher la requête et l’utiliser pour une recherche ultérieure, sélectionnez **Favoris**.  
![Afficher la requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. Vous pouvez mettre à jour la requête pour rechercher un nouveau numéro de contrôle d’échange.  
![Mettre à jour la requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [schéma de suivi personnalisé](logic-apps-track-integration-account-custom-tracking-schema.md).   
* En savoir plus sur les [schémas de suivi AS2](logic-apps-track-integration-account-as2-tracking-schemas.md).    
* En savoir plus sur le [schéma de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md).  
* En savoir plus sur [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

