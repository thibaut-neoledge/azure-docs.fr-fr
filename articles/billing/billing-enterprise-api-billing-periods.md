---
title: "API de facturation Azure Enterprise - Périodes de facturation | Microsoft Docs"
description: "En savoir plus sur les API de création de rapports qui permettent aux clients d’Azure Enterprise d’extraire leurs données de consommation par programme."
services: 
documentationcenter: 
author: cwatson-cat
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
ms.openlocfilehash: c6880b79189e0683387a7aafbd6fa4805b3b42ef
ms.contentlocale: fr-fr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---billing-periods"></a>API de création de rapports pour les clients Enterprise : périodes de facturation

L’API Périodes de facturation renvoie une liste des périodes de facturation contenant les données de consommation pour l’abonnement indiqué, par ordre chronologique inverse. Chaque période contient une propriété qui pointe vers l’API Route (Itinéraire) pour les quatre ensembles de données : BalanceSummary (Solde et résumé), UsageDetails (Détails d’utilisation), Marketplace Charges (Frais de la Place de marché) et PriceSheet (Grille tarifaire). Si la période n’a pas de données, la propriété correspondante est Null. 


##<a name="request"></a>Requête 
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). 

|Méthode | URI de demande|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingperiods|

> [!Note]
> Pour utiliser la version d’évaluation de l’API, remplacez v2 par v1 dans l’URL ci-dessus.
>

## <a name="response"></a>Réponse
 
    
    
      [
            {
                "billingPeriodId": "201704",
                "billingStart": "2017-04-01T00:00:00Z",
                "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },          
            ....
      ]
    

**Définitions des propriétés de réponse**

|Nom de la propriété| Type| Description
|-|-|-|
|billingPeriodId| string| ID unique qui représente une période de facturation donnée|
|billingStart| datetime| Chaîne ISO 8601 représentant la date de début de la période|
|billingEnd| datetime| Chaîne ISO 8601 représentant la date de fin de la période|
|balanceSummary| string| Chemin d’accès d’URL qui route vers les données de résumé de solde pour cette période|
|usageDetails| string| Chemin d’accès d’URL qui route vers les données de détails d’utilisation pour cette période|
|marketplaceCharges| string| Chemin d’accès d’URL qui route vers les données de frais de Place de marché pour cette période|
|priceSheet| string| Chemin d’accès d’URL qui route vers les données de grille tarifaire pour cette période|

<br/>
## <a name="see-also"></a>Voir aussi

* [API Solde et résumé](billing-enterprise-api-balance-summary.md)

* [API Détails de l’utilisation](billing-enterprise-api-usage-detail.md) 

* [API Frais de la Place de marché](billing-enterprise-api-marketplace-storecharge.md) 

* [API Grille tarifaire](billing-enterprise-api-pricesheet.md)
