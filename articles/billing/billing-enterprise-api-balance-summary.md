---
title: API de facturation Azure Enterprise - Balance and Summary | Microsoft Docs
description: "En savoir plus sur les API Azure Billing Usage et RateCard, qui fournissent des vues d’ensemble des tendances et de la consommation des ressources Azure."
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
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: fr-fr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>API de création de rapports pour les clients Enterprise - Balance and Summary

L’API Balance and Summary (Solde et résumé) offre un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service Place de marché Azure, ajustements et dépassement des frais.


##<a name="request"></a>Demande 
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). Si aucune période de facturation n’est spécifiée, les données de la période de facturation en cours sont retournées.

|Méthode | URI de demande|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> Pour utiliser la préversion de l’API, remplacez v2 par v1 dans l’URL ci-dessus.
>

## <a name="response"></a>Réponse

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**Définitions des propriétés de réponse**

|Nom de la propriété| Type| Description
|-|-|-|
|id|string|ID unique d’une période de facturation et d’une inscription données|
|billingPeriodId|string |ID de période de facturation|
|currencyCode|string |Code devise|
|beginningBalance|Décimal| Solde de début de la période de facturation|
|endingBalance|Décimal| Solde de fin de la période de facturation (pour les périodes ouvertes, cette option est mise à jour quotidiennement)|
|newPurchases|Décimal| Montant total des nouveaux achats|
|adjustments|Décimal| Montant total d’ajustement|
|utilized|Décimal| Utilisation totale par de l’engagement|
|serviceOverage|Décimal| Dépassement des services Azure|
|chargesBilledSeparately|Décimal| Frais facturés séparément|
|totalOverage|Décimal| serviceOverage + chargesBilledSeparately|
|totalUsage|Décimal| Engagement de service Azure + total du dépassement|
|azureMarketplaceServiceCharges|Décimal| Total des frais pour la Place de marché Azure|
|newPurchasesDetails|Tableau de chaînes JSON de paires nom/valeur|Liste de nouveaux achats|
|adjustmentDetails|Tableau de chaînes JSON de paires nom/valeur|Liste d’ajustements (crédit de promotion, crédit SIE, etc.) |


<br/>
## <a name="see-also"></a>Voir aussi

* [API Périodes de facturation](billing-enterprise-api-billing-periods.md)

* [API Détails de l’utilisation](billing-enterprise-api-usage-detail.md) 

* [API Frais de la Place de marché](billing-enterprise-api-marketplace-storecharge.md) 

* [API Grille tarifaire](billing-enterprise-api-pricesheet.md)
