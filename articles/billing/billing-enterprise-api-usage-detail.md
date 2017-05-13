---
title: "API de facturation Azure Enterprise - Détails d’utilisation | Microsoft Docs"
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
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>API de création de rapports pour les clients Enterprise - Détails d’utilisation (préversion)

L’API Usage Details (Détails d’utilisation) offre une analyse quotidienne des quantités consommées et des frais estimés pour un abonnement. Le résultat inclut également des informations sur les instances, les compteurs et les services. L’API peut être interrogée par période de facturation ou en indiquant une date de début et de fin. 
## <a name="consumption-apis"></a>API de consommation


##<a name="request"></a>Requête 
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). Si aucune période de facturation n’est spécifiée, les données de la période de facturation en cours sont retournées. Des plages de dates personnalisées peuvent être spécifiées avec les paramètres de date de début et de fin au format AAAA-MM-JJ. La période maximale prise en charge est de 36 mois.  

|Méthode | URI de demande|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>Réponse

> En raison de grands volumes potentiels de données, l’ensemble de résultats s’affiche sur plusieurs pages. La propriété nextLink, si elle est présente, spécifie le lien vers la page suivante de données. Si le lien est vide, cela indique qu’il s’agit de la dernière page. 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**Définitions des propriétés de réponse**

|Nom de la propriété| Type| Description
|-|-|-|
|id| string| ID unique de l’appel d’API. |
|données| Tableau JSON| Tableau des détails d’utilisation quotidienne pour chaque instance ou compteur|
|nextLink| string| Lorsqu’il y a plus de pages de données, le lien nextLink pointe vers l’URL qui retourne la page suivante de données |

## <a name="see-also"></a>Voir aussi
* [API Périodes de facturation](billing-enterprise-api-billing-periods.md)

* [API Solde et résumé](billing-enterprise-api-balance-summary.md)

* [API Frais de la Place de marché](billing-enterprise-api-marketplace-storecharge.md) 

* [API Grille tarifaire](billing-enterprise-api-pricesheet.md)
