---
title: "Traitement d’événements en temps réel avec Stream Analytics | Microsoft Docs"
description: "Découvrez comment un ensemble de services Azure peut interagir pour l’analyse et le traitement des événements en temps réel."
keywords: "traitement en temps réel, traitement des événements, architecture de référence"
services: stream-analytics,event-hubs,storage,sql-database
documentationcenter: 
author: samacha
manager: jhubbard
editor: 
ms.assetid: 11af48bc-313c-4527-8c80-91088dc9f3c6
ms.service: stream-analytics
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: a5206f74e61c3cfd210ff80654e31f466ce08977
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architecture de référence : Traitement d’événements en temps réel avec Microsoft Azure Stream Analytics
L’architecture de référence associée au traitement des événements en temps réel avec Azure Stream Analytics fournit un modèle générique de déploiement d’une solution de traitement des flux PaaS (Platform as a Service) en temps réel avec Microsoft Azure.

## <a name="summary"></a>Résumé
Généralement, les solutions d’analyse reposent sur différentes fonctionnalités, comme le processus ETL (extraction, transformation et chargement) et l’entreposage de données, où sont stockées les données avant analyse. La modification des besoins, y compris l’accélération de l’arrivée des données, pousse le modèle existant jusqu’à ses dernières limites. Il est possible d’analyser les données des flux de données en déplacement avant stockage. Même si cette fonctionnalité n’est pas nouvelle, cette approche ne s’est pas généralisée. 

Microsoft Azure fournit un catalogue étendu de technologies d’analyse compatibles avec de nombreuses solutions et spécifications. La diversité des offres peut grandement compliquer le choix des services Azure à déployer pour une solution de bout en bout. Cet article décrit les fonctionnalités et l’interopérabilité des services Azure qui prennent en charge une solution de diffusion en continu des événements. Il décrit également les situations dans lesquelles les clients peuvent profiter de ce type d’approche.

## <a name="contents"></a>Sommaire
* Résumé
* Présentation de l’analyse en temps réel
* Proposition de valeur des données en temps réel dans Azure
* Scénarios courants d’analyse en temps réel
* Architecture et composants
  * Sources de données
  * Couche d’intégration de données
  * Couche d’analyse en temps réel
  * Couche de stockage des données
  * Couche présentation/consommation
* Conclusion

**Auteur :** Charles Feddersen, architecte de solutions, Data Insights Center of Excellence, Microsoft Corporation

**Publié :** janvier 2015

**Révision :** 1.0

**Téléchargement :** [Traitement d’événements en temps réel avec Microsoft Azure Stream Analytics](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


