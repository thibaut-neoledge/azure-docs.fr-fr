---
title: "API de facturation Azure Enterprise - Détails d’utilisation | Microsoft Docs"
description: "Découvrez les API Azure Billing Usage et RateCard, qui fournissent des vues d’ensemble des tendances et de la consommation des ressources Azure."
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
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>API de création de rapports pour les clients Enterprise - Détails d’utilisation

L’API Usage Details (Détails d’utilisation) offre une analyse quotidienne des quantités consommées et des frais estimés pour un abonnement. Le résultat inclut également des informations sur les instances, les compteurs et les services. L’API peut être interrogée par période de facturation ou en indiquant une date de début et de fin. 
## <a name="consumption-apis"></a>API de consommation


##<a name="request"></a>Requête 
Les propriétés d’en-tête communes qui doivent être ajoutées sont spécifiées [ici](billing-enterprise-api.md). Si aucune période de facturation n’est spécifiée, les données de la période de facturation en cours sont retournées. Des plages de dates personnalisées peuvent être spécifiées avec les paramètres de date de début et de fin au format AAAA-MM-JJ. La période maximale prise en charge est de 36 mois.  

|Méthode | URI de demande|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Pour utiliser la préversion de l’API, remplacez v2 par v1 dans l’URL ci-dessus.
>

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
            "Cost": 0,
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
        ],
        "nextLink": "string"
    }

<br/>
**Définitions des propriétés de réponse**

|Nom de la propriété| Type| Description
|-|-|-|
|id| string| ID unique de l’appel d’API. |
|données| Tableau JSON| Tableau des détails d’utilisation quotidienne pour chaque instance ou compteur.|
|nextLink| string| Quand il y a plus de pages de données, le lien nextLink pointe vers l’URL qui retourne la page suivante de données. |
|accountId| int| Champ obsolète. Présent à des fins de compatibilité descendante. |
|productId| int| Champ obsolète. Présent à des fins de compatibilité descendante. |
|resourceLocationId| int| Champ obsolète. Présent à des fins de compatibilité descendante. |
|consumedServiceID| int| Champ obsolète. Présent à des fins de compatibilité descendante. |
|departmentId| int| Champ obsolète. Présent à des fins de compatibilité descendante. |
|accountOwnerEmail| string| Compte de messagerie du propriétaire du compte. |
|accountName| string| Nom du compte entré par le client. |
|serviceAdministratorId| string| Adresse e-mail de l’administrateur de service. |
|subscriptionId| int| Champ obsolète. Présent à des fins de compatibilité descendante. |
|subscriptionGuid| string| Identificateur global unique de l’abonnement. |
|subscriptionName| string| Nom de l’abonnement. |
|date| string| Date à laquelle la consommation s’est produite. |
|product| string| Détails supplémentaires sur le compteur. Exemple : A1(VM)Windows - Est de l’Asie-Pacifique|
|meterId| string| Identificateur du compteur qui a émis l’utilisation. |
|meterCategory| string| Service de plateforme Azure qui a été utilisé. |
|meterSubCategory| string| Définit le type de service Azure pouvant affecter le tarif. Exemple : A1 VM (Non-Windows|
|meterRegion| string| Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement. |
|meterName| string| Nom du compteur. |
|consumedQuantity| double| Quantité du compteur consommée. |
|resourceRate| double| Taux applicable par unité facturable. |
|coût| double| Frais encourus pour le compteur. |
|resourceLocation| string| Identifie le centre de données où le compteur s’exécute. |
|consumedService| string| Service de plateforme Azure qui a été utilisé. |
|instanceId| string| Cet identificateur est le nom de la ressource ou l’ID de ressource complet. Pour plus d’informations, consultez [API Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|serviceInfo1| string| Métadonnées de service Azure interne. |
|serviceInfo2| string| Par exemple, le type d’image d’une machine virtuelle et le nom du fournisseur de services Internet pour ExpressRoute. |
|additionalInfo| string| Métadonnées relatives au service. Par exemple, le type d’image d’une machine virtuelle. |
|tags| string| Balises ajoutées par le client. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags). |
|storeServiceIdentifier| string| Cette colonne n’est pas utilisée. Présent à des fins de compatibilité descendante. |
|departmentName| string| Nom du service. |
|costCenter| string| Centre de coût avec lequel l’utilisation est associée. |
|unitOfMeasure| string| Identifie l’unité dans laquelle le service est facturé. Par exemple : Go, heures, 10 000 s. |
|resourceGroup| string| Groupe de ressources dans lequel le compteur déployé s’exécute. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
<br/>
## <a name="see-also"></a>Voir aussi

* [API Périodes de facturation](billing-enterprise-api-billing-periods.md)

* [API Solde et résumé](billing-enterprise-api-balance-summary.md)

* [API Frais de la Place de marché](billing-enterprise-api-marketplace-storecharge.md) 

* [API Grille tarifaire](billing-enterprise-api-pricesheet.md)

