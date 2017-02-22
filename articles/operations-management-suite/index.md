---
title: "Documentation Azure Operations Management Suite (OMS) - Didacticiels | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) est une solution de gestion informatique de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud. Cet article identifie les différents services inclus dans OMS et fournit des liens vers leur documentation détaillée."
services: operations-management-suite
author: carolz
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: ccfbd45def1ecf036a2464f560aa89817c37b419
ms.openlocfilehash: 54b3ce2c8c3966ee36350fed71bdc48aa5bffa63

---
# <a name="what-is-operations-management-suite-oms"></a>Présentation d’Operations Management Suite (OMS)
Microsoft Operations Management Suite (OMS) est une solution de gestion informatique de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.  La solution OMS étant implémentée sous la forme d’un service informatique, elle peut être opérationnelle rapidement, avec un investissement minimal dans des services d’infrastructure.  Les nouvelles fonctionnalités sont fournies automatiquement, ce qui vous permet d’économiser les coûts de mise à niveau et de maintenance.

En plus de fournir de précieux services de manière autonome, OMS peut intégrer des composants System Center tels que System Center Operations Manager, afin d’étendre dans le cloud vos investissements de gestion existants.  System Center et OMS peuvent fonctionner simultanément pour fournir une expérience de gestion hybride.

Les sections suivantes fournissent une description générale des différents avantages d’OMS et des services qui les implémentent.  Vous pouvez examiner l’architecture OMS afin d’avoir une vue d’ensemble des différents composants OMS avant de consulter la documentation détaillée de chacun d’entre eux.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Insight & Analytics](media/operations-management-suite-overview/icon-insight-analytics.png) Insight & Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) vous permet de collecter, corréler, rechercher et agir sur les données de journal et de performance générées par les applications et les systèmes d’exploitation. Elle vous fournit des informations opérationnelles en temps réel à l’aide d’une recherche intégrée et de tableaux de bord personnalisés permettant d’analyser facilement des millions d’enregistrements dans l’ensemble de vos charges de travail et serveurs, quel que soit leur emplacement physique.

Vous pouvez facilement ajouter des solutions à Log Analytics pour définir les données à collecter et la logique de leur analyse.  Ces solutions peuvent inclure des fonctionnalités supplémentaires qui sont fournies automatiquement aux agents avec une configuration minimale voire inexistante.  Outre l’utilisation d’outils d’analyse fournis par chacune des solutions, vous pouvez effectuer des recherches personnalisées sur l’ensemble du jeu de données afin de corréler les données entre les systèmes et les applications.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automation & Control](media/operations-management-suite-overview/icon-automation-control.png) Automation & Control
Azure Automation automatise les processus administratifs grâce à des [Runbooks](../automation/automation-runbook-types.md) basés sur PowerShell et exécutés dans le cloud Azure.  Les Runbooks ont accès à tout produit ou service pouvant être géré avec PowerShell, y compris aux ressources situées dans d’autres clouds tels qu’Amazon Web Services (AWS).  Les Runbooks peuvent également être exécutés sur un serveur de votre centre de données local pour gérer des ressources locales.

Azure Automation assure la gestion de la configuration avec [PowerShell DSC](../automation/automation-dsc-overview.md).  Vous pouvez créer et gérer les ressources DSC hébergées dans Azure et les appliquer à des systèmes de cloud et locaux pour définir et appliquer automatiquement leur configuration.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protection et récupération](media/operations-management-suite-overview/icon-protection-recovery.png) Protection et récupération d’urgence
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) protège les données de vos applications et les conserve des années, sans investissement en capital et avec des frais de fonctionnement minimaux.  Cette solution permet de sauvegarder des données à partir de serveurs Windows physiques et virtuels, en plus des charges de travail des applications telles que SQL Server et SharePoint.  Elle peut également être utilisée par System Center Data Protection Manager (DPM) afin de répliquer les données protégées vers Azure pour la redondance et le stockage à long terme.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles locales Hyper-V et VMware, ainsi que de serveurs physiques Windows ou Linux. Vous pouvez répliquer des machines vers un centre de données secondaire ou étendre votre centre de données en les répliquant sur Azure. Site Recovery fournit également un basculement et une récupération des charges de travail en toute simplicité. Cette solution s’intègre avec des mécanismes de récupération d’urgence tels que SQL Server AlwaysOn et fournit des plans de récupération pour un basculement facile des charges de travail réparties sur plusieurs machines.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Sécurité et conformité d’OMS](media/operations-management-suite-overview/icon-security-compliance.png) Sécurité et conformité
La sécurité et la conformité vous aident à identifier, évaluer et atténuer les risques de sécurité qui menacent votre infrastructure.  Ces fonctionnalités d’OMS sont implémentées via plusieurs solutions dans Log Analytics, qui analysent les données de journal et la configuration de systèmes agents afin de vous aider à assurer la sécurité continue de votre environnement.

* La [solution de sécurité et d’audit](oms-security-getting-started.md) collecte et analyse les événements de sécurité sur les systèmes gérés, afin d’identifier toute activité suspecte.
* La [solution anti-programmes malveillants](../log-analytics/log-analytics-malware.md) fournit des rapports sur l’état de protection des systèmes gérés contre les programmes malveillants.  
* La solution Mises à jour du système effectue une analyse des mises à jour de sécurité et des autres mises à jour sur vos systèmes gérés, afin que vous puissiez identifier facilement ceux qui nécessitent une mise à jour corrective.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* En savoir plus sur [Azure Automation](../automation/automation-intro.md).
* En savoir plus sur [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* En savoir plus sur [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).




<!--HONumber=Feb17_HO2-->


