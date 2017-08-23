---
title: "API de facturation Azure Enterprise - Frais de la Place de marché | Microsoft Docs"
description: "En savoir plus sur les API de création de rapports qui permettent aux clients d’Azure Enterprise d’extraire leurs données de consommation par programme."
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
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: fr-fr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>API de création de rapports pour les clients Enterprise - Marketplace Store Charge

L’API Marketplace Store Charge renvoie l’analyse détaillée des frais de magasin basée sur l’utilisation par jour pour la période de facturation ou les dates de début et de fin indiquées (les frais ponctuels ne sont pas inclus).

##<a name="request"></a>Requête 
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). Si aucune période de facturation n’est spécifiée, les données de la période de facturation en cours sont retournées. Des plages de dates personnalisées peuvent être spécifiées avec les paramètres de date de début et fin au format AAAA-MM-JJ. La période maximale prise en charge est de 36 mois.  

|Méthode | URI de demande|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Pour utiliser la préversion de l’API, remplacez v2 par v1 dans l’URL ci-dessus.
>

## <a name="response"></a>Réponse
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**Définitions des propriétés de réponse**

|Nom de la propriété| Type| Description
|-|-|-|
|id|string|ID unique de l’élément Frais de la Place de marché|
|subscriptionGuid|Guid|GUID de l’abonnement|
|subscriptionName|string|Nom de l’abonnement|
|meterId|string|ID du compteur émis|
|usageStartDate|DateTime|Heure de début de l’enregistrement de l’utilisation|
|usageEndDate|DateTime|Heure de fin de l’enregistrement de l’utilisation|
|offerName|string|Nom de l’offre|
|resourceGroup|string|Groupe de ressources|
|instanceId|string|ID de l’instance|
|additionalInfo|string|Chaîne JSON avec informations supplémentaires|
|tags|string|Chaîne JSON de balise|
|orderNumber|string|Numéro de commande|
|unitOfMeasure|string|Unité de mesure du compteur|
|costCenter|string|Centre de coût|
|accountId|int|ID de compte|
|accountName|string |Nom de compte|
|accountOwnerId|string|ID du propriétaire du compte|
|departmentId|int|ID du service|
|departmentName|string|Nom du service|
|publisherName|string|Nom de l’éditeur|
|planName|string|Nom de l’offre|
|consumedQuantity|Décimal|Quantité consommée pendant cette période|
|resourceRate|Décimal|Prix unitaire du compteur|
|extendedCost|Décimal|Estimation des frais en fonction de la quantité consommée et du coût global|
<br/>
## <a name="see-also"></a>Voir aussi

* [API Périodes de facturation](billing-enterprise-api-billing-periods.md)

* [API Détails de l’utilisation](billing-enterprise-api-usage-detail.md) 

* [API Solde et résumé](billing-enterprise-api-balance-summary.md)

* [API Grille tarifaire](billing-enterprise-api-pricesheet.md)
