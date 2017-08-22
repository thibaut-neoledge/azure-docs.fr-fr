---
title: "API de facturation d’entreprise Azure - Grille tarifaire | Microsoft Docs"
description: "Découvrez les API de création de rapports qui permettent aux clients Azure en entreprise d’extraire leurs données de consommation par programmation."
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: fr-fr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>API de création de rapports pour les clients en entreprise - Grille tarifaire

L’API Grille tarifaire fournit les tarifs applicables pour chaque compteur selon l’abonnement et la période de facturation donnés.

##<a name="request"></a>Requête
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). Si aucune période de facturation n’est spécifiée, les données de la période de facturation en cours sont retournées.

|Méthode | URI de demande|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|

> [!Note]
> Pour utiliser la préversion de l’API, remplacez v2 par v1 dans l’URL ci-dessus.
>

## <a name="response"></a>Réponse

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                "billingPeriodId": "201704",
                "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
                "meterName": "A1 VM",
                "unitOfMeasure": "100 Hours",
                "includedQuantity": 0,
                "partNumber": "N7H-00015",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            {
                "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                "billingPeriodId": "201404",
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>Si vous utilisez la préversion de l’API, le champ meterId n’est pas disponible.
>

**Définitions des propriétés de réponse**

|Nom de la propriété| Type| Description
|-|-|-|
|id| chaîne| ID unique qui représente un élément de grille tarifaire donné (compteur par période de facturation)|
|billingPeriodId| chaîne| ID unique qui représente une période de facturation donnée|
|meterId| chaîne| Identificateur du compteur. Il peut être mappé au meterId d’utilisation.|
|meterName| chaîne| Nom du compteur|
|unitOfMeasure| chaîne| Unité de mesure pour mesurer le service|
|includedQuantity| décimal| Quantité incluse |
|partNumber| chaîne| Numéro de référence associé au compteur|
|unitPrice| décimal| Prix unitaire du compteur|
|currencyCode| chaîne| Code de devise du prix unitaire|
<br/>
## <a name="see-also"></a>Voir aussi

* [API Périodes de facturation](billing-enterprise-api-billing-periods.md)

* [API Détails de l’utilisation](billing-enterprise-api-usage-detail.md)

* [API Solde et résumé](billing-enterprise-api-balance-summary.md)

* [API Frais de la Place de marché](billing-enterprise-api-marketplace-storecharge.md)

