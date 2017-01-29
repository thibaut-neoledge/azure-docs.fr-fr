---
title: "Suivi de messages B2B dans le portail Operations Management Suite à l’aide d’une requête | Microsoft Docs"
description: "Découvrez comment suivre des messages B2B dans le portail Operations Management Suite à l’aide d’une requête."
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
ms.sourcegitcommit: 684ab31903369070a235b35eb0b8c9adffd309e7
ms.openlocfilehash: 08689b8f57d5b29a47b98eac812eaee285cd13a0


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Suivi de messages B2B dans le portail Operations Management Suite à l’aide d’une requête
Pour suivre des messages B2B dans le portail Operations Management Suite, vous pouvez créer une requête qui filtre les données pour un numéro de contrôle d’échange spécifique.

## <a name="prerequisites"></a>Composants requis

Pour le débogage et des informations de diagnostic plus détaillées, activez les diagnostics dans votre [compte d’intégration](app-service-logic-monitor-b2b-message.md) pour vos [applications logiques](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) qui ont des connecteurs X12. Puis, effectuez les étapes de [publication des données de diagnostic](app-service-logic-track-b2b-messages-omsportal.md) dans le portail Operations Management Suite.

## <a name="to-create-a-query-to-search-for-a-specific-interchange-control-number"></a>Pour créer une requête afin de rechercher un numéro de contrôle d’échange spécifique

1. Sur la page de démarrage, sélectionnez **Recherche de journal**.  
![Sélectionner la recherche de journaux](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Dans la zone de recherche, entrez **Type="AzureDiagnostics"**. Pour filtrer les données, sélectionnez **Ajouter**.  
![Sélectionner la requête](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Saisissez **interchange**, sélectionnez **event_record_messageProperties_interchangeControlNumber_s**, puis sélectionnez **Ajouter**.  
![Ajouter un filtre](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Sélectionnez le numéro de contrôle pour lequel filtrer les données et cliquez sur **Appliquer**.  
![Rechercher le numéro de contrôle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Recherchez la requête créée pour filtrer les données pour le numéro de contrôle sélectionné.   
![Rechercher la requête](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Entrez un nom pour la requête, puis enregistrez-la.   
![Enregistrer la requête](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Pour afficher la requête et l’utiliser pour une recherche ultérieure, sélectionnez **Favoris**.  
![Afficher la requête](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. Vous pouvez mettre à jour la requête pour rechercher un nouveau numéro de contrôle d’échange.  
![Mettre à jour la requête](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [schéma de suivi personnalisé](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* En savoir plus sur les [schémas de suivi AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md).    
* En savoir plus sur le [schéma de suivi X12](app-service-logic-track-integration-account-x12-tracking-shemas.md).  
* En savoir plus sur [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).



<!--HONumber=Dec16_HO3-->


