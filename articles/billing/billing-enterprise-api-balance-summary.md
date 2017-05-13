---
title: API de facturation Azure Enterprise - Balance and Summary | Microsoft Docs
description: "En savoir plus sur les API Azure Billing Usage et RateCard, qui fournissent des vues d’ensemble des tendances et de la consommation des ressources Azure."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 7af4865a5455c314c1bdeb315fb87b0f92c1ddf9
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary-preview"></a>API de création de rapports pour les clients Enterprise - Balance and Summary (préversion)

L’API Balance and Charge (Solde et frais) permet un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service Place de marché Azure, ajustements et dépassement des frais.


##<a name="request"></a>Requête 
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). Si aucune période de facturation n’est spécifiée, les données de la période de facturation en cours sont retournées.

|Méthode | URI de demande|
|-|-|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|


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
