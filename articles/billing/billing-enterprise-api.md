---
title: API de facturation Azure Enterprise | Microsoft Docs
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 343b71e28adfd32295b837a40ecf64083341b972
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="overview-of-reporting-apis-for-enterprise-customers-preview"></a>Vue d’ensemble des API de création de rapports pour les clients Enterprise (préversion)
Les API de création de rapports permettent aux clients Azure Enterprise d’extraire leurs données de consommation et de facturation par programme pour les transférer vers les outils d’analyse de données de leur choix. 

## <a name="enabling-data-access-to-the-api"></a>Autoriser l’API à accéder aux données
* **Génération/récupération de la clé API** : connectez-vous au portail Enterprise et suivez le tutoriel présenté sous Aide - API de création de rapports. La première section située sous cet article d’aide explique comment générer/récupérer la clé API pour l’inscription spécifiée.
* **Transmission des clés dans l’API** : vous devez transmettre la clé API pour chaque appel à des fins d’authentification et d’autorisation. Les en-têtes HTTP doivent contenir la propriété suivante

|Clé d’en-tête de demande | Valeur|
|-|-|
|Autorisation| Indiquez la valeur dans ce format : **porteur {API_CLÉ}** <br/> Exemple : porteur eyr... 09|

## <a name="consumption-apis"></a>API de consommation
Un point de terminaison Swagger est disponible [ici](https://consumption.azure.com/v1/swagger) pour les API décrites ci-dessous. Il doit faciliter l’introspection de l’API et la capacité à générer des Kits de développement logiciel (SDK) clients en utilisant [AutoRest](https://github.com/Azure/AutoRest) ou [CodeGen Swagger](http://swagger.io/swagger-codegen/). À compter du 1er mai 2014, ces données sont disponibles via cette API. 

* **Balance and Summary** (Solde et résumé) : l’[API Balance and Summary](billing-enterprise-api-balance-summary.md) permet un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché Azure, ajustements et dépassement des frais.

* **Usage Details** (Détails d’utilisation) : l’[API Usage Details](billing-enterprise-api-usage-detail.md) offre une analyse quotidienne des quantités consommées et des frais estimés pour un abonnement. Le résultat inclut également des informations sur les instances, les compteurs et les services. L’API peut être interrogée par période de facturation ou en indiquant une date de début et de fin. 

* **Marketplace Store Charge** (Frais de stockage de la Place de marché) : l’[API Marketplace Store Charge](billing-enterprise-api-marketplace-storecharge.md) renvoie l’analyse détaillée des frais de Place de marché basée sur l’utilisation par jour pour la période de facturation ou les dates de début et de fin indiquées (les frais ponctuels ne sont pas inclus).

* **Price Sheet** (Grille tarifaire) : l’[API Price Sheet](billing-enterprise-api-pricesheet.md) fournit les tarifs applicables pour chaque compteur selon l’abonnement et la période de facturation donnés. 

## <a name="helper-apis"></a>API d’assistance
 **List Billing Periods** (Liste des périodes de facturation) : l’[API List Billing Periods](billing-enterprise-api-billing-periods.md) renvoie une liste des périodes de facturation contenant les données de consommation pour l’abonnement indiqué, par ordre chronologique inverse. Chaque période contient une propriété qui pointe vers l’API Route (Itinéraire) pour les quatre ensembles de données : BalanceSummary (Solde et résumé), UsageDetails (Détails d’utilisation), Marketplace Charges (Frais de la Place de marché) et PriceSheet (Grille tarifaire).


## <a name="api-response-codes"></a>Codes de réponse HTTP  
|Code du statut de réponse|Message|Description|
|-|-|-|
|200| OK|Aucune erreur|
|401| Non autorisé| Clé API introuvable, non valide, expirée, etc.|
|404| Non disponible| Point de terminaison de rapport introuvable|
|400| Demande incorrecte| Paramètres non valides : plages de dates, nombres de Contrats Entreprise (EA), etc.|
|500| Erreur de serveur| Erreur inattendue lors du traitement de la demande| 










