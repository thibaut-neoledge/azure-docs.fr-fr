---
title: "Intégrer des journaux à partir de ressources Azure dans vos systèmes SIEM | Microsoft Docs"
description: "Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 434961f7d25195eee3310461a3a97e03ffdd8917
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Présentation de l’intégration des journaux Microsoft Azure
Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement.

## <a name="overview"></a>Vue d'ensemble
La plateforme en tant que Service (PaaS) et l’infrastructure en tant que Service (IaaS) hébergées dans Azure génèrent une grande quantité de données dans des journaux de sécurité. Ces journaux contiennent des informations critiques qui peuvent fournir des informations essentielles sur les violations de stratégie, les menaces internes et externes, les problèmes de conformité et anomalies du réseau, de l’hôte et de l’activité des utilisateurs.

L’intégration des journaux Azure est une solution gratuite qui permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux. L’intégration des journaux Azure collecte des diagnostics Azure à partir de vos machines virtuelles Windows *(WAD)*, de journaux d’activité Azure, d’alertes Azure Security Center et de journaux du fournisseur de ressources Azure. Cette intégration offre un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.

![Intégration des journaux Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?
Azure génère une journalisation complète pour chaque service Azure. Ces journaux sont classés selon ces deux types principaux :

* **Journaux de contrôle/gestion**, qui vous offre une visibilité sur les opérations CREATE, UPDATE et DELETE d’Azure Resource Manager. Les journaux d’activité Azure sont un exemple de ce type de journal.
* **Journaux des plans de données**, qui vous offre une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource Azure. Il s’agit par exemple des journaux du système d’événements, de la sécurité et des applications Windows dans une machine virtuelle, ou des journaux de diagnostic configurés via Azure Monitor.
* **Événements traités**, qui donne les événements/alertes traités en votre nom. Les alertes Azure Security Center sont un exemple de ce type, où Azure Security Center a traité et analysé votre abonnement et fournit des alertes de sécurité très concises.

L’intégration des journaux Azure prend actuellement en charge l’intégration des journaux d’activité Azure, du journal des événements Windows à partir de la machine virtuelle Windows de votre abonnement Azure, des alertes Azure Security Center, des journaux de diagnostic Azure et des journaux d’audit Azure Active Directory.

Le tableau suivant explique la catégorie des journaux et les détails de l’intégration SIEM

| Type de journal  |Log Analytics prenant en charge JSON (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Journaux d’audit AAD |  Oui | Nécessite la création d’un fichier d’analyseur JSON FlexConnector. Pour plus d’informations, consultez la documentation d’ArcSight.  |  Vous devez créer une extension de source de journaux. Pour plus d’informations, consultez la documentation de QRadar. |  
| Journaux d’activité  | Oui  |  Le fichier de l’analyseur JSON FlexConnector est disponible dans le centre de téléchargement, ainsi que l’intégration des journaux Azure |  [Module DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)(envoyer via Syslog) |  
| Alertes ASC  | Oui  |  Nécessite la création d’un fichier d’analyseur JSON FlexConnector. Pour plus d’informations, consultez la documentation d’ArcSight. | [Module DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)(envoyer via Syslog)   |   
| Journaux de diagnostic (journaux des ressources) | Oui | L’utilisateur final doit créer le fichier de l’analyseur JSON FlexConnector. Pour plus d’informations sur cette opération, consultez la documentation d’ArcSight. | Vous devez créer une extension de source de journaux. Pour plus d’informations, consultez la documentation de QRadar. |
| Journaux des machines virtuelles | Oui, via les événements transmis et non via JSON | Oui, via les événements transmis | Oui, via les événements transmis |

L’intégration des journaux Azure est une solution gratuite : vous n’avez pas besoin de payer pour les fichiers binaires associés. Toutefois, il existe des coûts liés au stockage Azure requis pour les informations du fichier journal.

Si vous avez des questions sur l’intégration des journaux Azure, envoyez un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a présenté une vue d’ensemble de l’intégration des journaux Azure. Pour plus d’informations sur l’intégration des journaux Azure et les types de journaux pris en charge, consultez les rubriques suivantes :

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) : Centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation sur l’intégration des journaux Azure.
* [Prise en main de l’intégration des journaux Azure](security-azure-log-integration-get-started.md) : ce didacticiel vous guide dans la procédure d’installation de l’intégration des journaux Azure et d’intégration des journaux du stockage Azure WAD, des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure Active Directory.
* [Integrate Azure Diagnostic logs streamed to Event Hubs to SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) : ce billet de blog décrit les étapes permettant d’intégrer les journaux de diagnostic à l’aide de l’intégration des journaux Azure
* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar.
* [Integrate Azure Activity logs and Azure Security Center Alerts over Syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) : ce billet de blog détaille les étapes pour envoyer des alertes d’activité et Azure Security Center sur syslog à QRadar
* [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
* [Intégration des alertes du Security Center avec les journaux Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) : ce document montre comment synchroniser les alertes du Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par Azure Diagnostics et les journaux d’audit Azure dans leur solution SIEM ou Log Analytics.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

