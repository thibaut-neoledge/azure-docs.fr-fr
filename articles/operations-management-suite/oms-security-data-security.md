---
title: "Solution de sécurité et d’audit d’Operations Management Suite - Sécurité des données | Microsoft Docs"
description: "Ce document explique de quelle manière les données sont gérées et protégées dans la solution de sécurité et d’audit d’Operations Management Suite."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 6af6c10cef317453131197db74a9380518afadf0


---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Solution de sécurité et d’audit d’Operations Management Suite - Sécurité des données
Pour aider les clients à empêcher, détecter et répondre aux menaces, [la solution de sécurité et d’audit d’Operations Management Suite](operations-management-suite-overview.md) collecte et traite les données concernant vos ressources, ce qui inclut :

* Journaux des événements de sécurité
* Suivi d’événements pour les événements Windows (ETW)
* Événements d’audit AppLocker
* Journal du pare-feu Windows
* Événements Advanced Threat Analytics
* Résultats de l’évaluation de la base de référence
* Résultats de l’analyse anti-programme malveillant
* Résultats de l’évaluation des correctifs/mises à jour
* Flux de données Syslogs explicitement activés sur l’agent

Nous prenons des engagements fermes pour protéger la confidentialité et la sécurité de ces données. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.
Cet article explique comment les données sont gérées et protégées dans la solution de sécurité et d’audit d’Operations Management Suite (OMS).

## <a name="data-sources"></a>Sources de données
La solution de sécurité et d’audit d’OMS analyse les données des machines virtuelles et des ordinateurs physiques sur lesquels l’agent OMS est installé. Elle peut collecter les informations de configuration relatives aux événements de sécurité, tels que les journaux IIS, les journaux d’audit et les journaux des événements Windows, ainsi que les messages syslog. Il peut s’agir des données suivantes : type et version de système d’exploitation, processus en cours d’exécution, nom de machine, adresses IP, utilisateur connecté et ID de locataire.  

## <a name="data-protection"></a>Protection des données
**Ségrégation des données :**les données client sont maintenues séparées logiquement sur chaque composant, dans l’ensemble du service. Toutes les données sont balisées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service. 

**Accès aux données** : afin de fournir des recommandations de sécurité et d’examiner les menaces de sécurité potentielles, le personnel de Microsoft peut accéder aux informations collectées ou analysées par les services. Nous respectons les [Conditions d’utilisation de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), qui indiquent que Microsoft n’utilisera pas les données client ou ne tirera pas d’informations de ces dernières à des fins commerciales, publicitaires ou similaires. Pour fournir des recommandations de sécurité et examiner les menaces de sécurité potentielles, le personnel de Microsoft peut accéder aux informations collectées ou analysées par les services. Nous utilisons uniquement les données client en fonction des besoins pour vous proposer des services Azure, notamment des utilisations compatibles avec la fourniture de ces services. Vous conservez tous les droits sur vos données.

**Utilisation des données** : Microsoft utilise des modèles et des informations sur les menaces observées auprès de multiples locataires pour améliorer ses fonctionnalités de prévention et de détection. Cette utilisation s’effectue en accord avec les engagements de confidentialité décrits dans la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) de Microsoft.

> [!NOTE]
> L’emplacement des données est configuré au niveau de l’espace de travail OMS, lors de la création de ce dernier, opération entrant dans le processus de configuration de la solution de sécurité et d’audit d’OMS.
> 
> 

## <a name="see-also"></a>Voir aussi
Ce document explique comment les données sont gérées et protégées dans OMS. Pour en savoir plus sur la solution de sécurité et d’audit d’OMS, consultez les rubriques suivantes :

* [Présentation - Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Surveiller et répondre aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-responding-alerts.md)
* [Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-monitoring-resources.md)




<!--HONumber=Dec16_HO1-->


