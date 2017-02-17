---
title: "Conseils Azure - Didacticiels | Microsoft Docs"
description: Meilleures pratiques et conseils pour Azure
services: guidance
author: carolz
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: guidance
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: f5ced8f436f4d1426e4d2b307fcfb6b644e94aaf
ms.openlocfilehash: c05d311633b9c9aa5490e55baafe83376d0c8fbc

---
# <a name="azure-guidance"></a>Conseils Azure
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

L’équipe de modèles et pratiques Microsoft fait partie de l’équipe de conseil clientèle Azure. Notre objectif est d’aider les développeurs, les architectes et les professionnels de l’informatique à maîtriser la plateforme Microsoft Azure. Nous formulons des conseils qui présentent les meilleures pratiques pour la création de solutions cloud sur Azure.

## <a name="checklists"></a>Checklists
Ces listes sont une référence rapide pour passer en revue les aspects fondamentaux de la disponibilité et de l’évolutivité. 

* [Liste de contrôle de disponibilité][AvailabilityChecklist] 
  
    Résumé des pratiques recommandées pour garantir la disponibilité.
* [Liste de contrôle de l’extensibilité][ScalabilityChecklist]
  
    Résumé des pratiques recommandées pour la conception et l’implémentation de services évolutifs et pour le traitement de la gestion des données.

## <a name="best-practices-articles"></a>Articles sur les meilleures pratiques
Ces articles fournissent une présentation détaillée des concepts importants généralement associés au cloud computing. 

* [Conception de l’API][APIDesign] 
  
    Présentation des problèmes de conception à prendre en compte lorsque vous concevez une API web.
* [Implémentation de l’API][APIImplementation] 
  
    Ensemble de pratiques recommandées pour implémenter et publier une API web.
* [Conseils de sécurité pour les API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    Présentation des problèmes d’authentification et d’autorisation (par exemple, types de jeton, protocoles d’autorisation, flux d’autorisation et atténuation des menaces).
* [Recommandations en matière de mise à l’échelle automatique][AutoscalingGuidance] 
  
    Résumé des considérations relatives à la mise à l’échelle de solutions sans intervention manuelle.
* [Conseils sur les travaux en arrière-plan][BackgroundJobsGuidance] 
  
    Description des options disponibles et des pratiques recommandées pour l’implémentation de tâches qui doivent être exécutées en arrière-plan, indépendamment de toute opération de premier plan ou opération interactive.
* [Aide relative au réseau de distribution de contenu (CDN)][CDNGuidance] 
  
    Conseils d’ordre général et pratique recommandée pour l’utilisation du CDN, afin de réduire la charge sur vos applications et d’optimiser les performances et la disponibilité.
* [Recommandations en matière de cache][CachingGuidance] 
  
    Résumé sur la façon d’utiliser la mise en cache pour améliorer les performances et l’évolutivité d’un système.
* [Recommandations sur le partitionnement des données][DataPartitioningGuidance]
  
    Stratégies que vous pouvez utiliser pour partitionner des données, afin d’améliorer l’évolutivité, de réduire la contention et d’optimiser les performances.
* [Guide de surveillance et de diagnostic][MonitoringandDiagnosticsGuidance] 
  
    Conseils pour contrôler la façon dont vos utilisateurs utilisent votre système, pour suivre l’utilisation des ressources et généralement pour surveiller l’intégrité et les performances de votre système.
* [Conventions d'affectation de noms][naming-conventions] 
  
    Conventions d’affectation de noms recommandées pour les ressources Azure.
* [Conseils généraux sur les nouvelles tentatives][RetryGeneralGuidance] 
  
    Présentation des concepts généraux pour le traitement des erreurs temporaires.
* [Guide spécifique relatif au service de nouvelle tentative][RetryServiceSpecificGuidance]
  
    Résumé des fonctionnalités de nouvelle tentative pour plusieurs services Azure, notamment des informations pour vous aider à utiliser, adapter ou étendre le mécanisme de nouvelle tentative pour ce service.

## <a name="scenario-guides"></a>Guides de scénario
* [Exécution d’Elasticsearch sur Azure][elasticsearch] 
  
    Elasticsearch est un moteur de recherche open source hautement évolutif utilisant une base de données. Il convient aux situations nécessitant une analyse et une découverte rapides des informations contenues dans des jeux de données volumineux. Ce guide aborde certains aspects essentiels à prendre en compte lors de la création d’un cluster Elasticsearch.
* [Gestion des identités pour les applications multi-locataires][identity-multitenant] 
  
    L’architecture mutualisée est une architecture dans laquelle plusieurs locataires partagent une application, mais sont isolés les uns des autres. Ce guide vous explique comment gérer des identités d’utilisateurs dans une application multi-locataire, en utilisant [Azure Active Directory][AzureAD] pour gérer la connexion et l’authentification.
* [Développement de solutions de Big Data](https://msdn.microsoft.com/library/dn749874.aspx)
  
    Ce guide explore l’utilisation de HDInsight pour des scénarios comme l’exploration itérative, un entrepôt de données, pour les processus ETL et l’intégration dans les systèmes BI existants. Il inclut également des conseils sur la compréhension des concepts de Big Data, sur la planification et la conception de solutions Big Data et sur l’implémentation de ces solutions.

## <a name="patterns"></a>Modèles
* [Modèles de conception de cloud : conseils sur l’architecture prédictive pour les applications cloud](https://msdn.microsoft.com/library/dn568099.aspx)
  
    Les modèles de conception de cloud sont une bibliothèque de modèles de conception et de rubriques de conseils connexes. Elle expose les avantages de l’application de modèles en montrant comment chaque élément peut s’adapter dans les architectures d’application cloud.
* [Optimisation des performances pour les applications cloud](https://github.com/mspnp/performance-optimization)
  
    Ce guide est une exploration des anti-modèles courants qui empêchent les applications d’être mises à l’échelle sous charge. Il inclut des exemples illustrant les huit anti-modèles, un [primer d’analyse des performances](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) et un guide pour [l’évaluation des performances par rapport aux métriques clés](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Architectures de référence
Nos architectures de référence sont organisées par scénario.
Chaque architecture propose des pratiques recommandées, des étapes préconisées et un composant exécutable qui représente les recommandations.

La bibliothèque actuelle des architectures de référence est disponible à l’adresse [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Aide relative à la résilience
Ces rubriques décrivent comment concevoir des applications résistantes aux défaillances dans un environnement cloud distribué.   

* [Vue d’ensemble de la résilience][ResiliencyOvervew]
  
     Comment créer des applications sur la plateforme Azure qui peuvent récupérer après des défaillances et continuer à fonctionner. Décrit une approche structurée pour bénéficier de résilience, de la conception à l’implémentation, en passant par le déploiement et les opérations.
* [Liste de contrôle de résilience][resiliency-checklist]
  
    Liste de contrôle des recommandations qui vous aidera à planifier une variété de modes d’échec susceptibles de se produire.
* [Analyse du mode d’échec][resiliency-fma] 
  
    L’analyse du mode d’échec (FMA) est un processus de création de résilience dans un système, en identifiant les points de défaillance possibles. Point de départ pour votre processus FMA, cet article contient un catalogue des modes de défaillance potentiels et les mesures de corrections associées. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Feb17_HO3-->


