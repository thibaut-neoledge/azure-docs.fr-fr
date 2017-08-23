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
ms.date: 08/08/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 5e672bd6b9356ce16663e843e4a4e7365cb159c3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Présentation de l’intégration des journaux Microsoft Azure
Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement.

## <a name="overview"></a>Vue d'ensemble

L’intégration des journaux Azure est une solution gratuite qui permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux.

L’intégration des journaux Azure collecte les événements Windows à partir des chaînes de l’Observateur d’événements, des [journaux d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), des [alertes Azure Security Center](../security-center/security-center-intro.md) et des [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) des ressources Azure. Cette intégration aide votre solution SIEM à offrir un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.

>[!NOTE]
À l’heure actuelle, seuls les clouds Azure Commercial et Azure Government sont pris en charge. Les autres clouds ne sont pas pris en charge pour le moment.

![Intégration des journaux Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?
Azure génère une journalisation complète pour chaque service Azure. Ces journaux représentent trois types de journaux :

* **Journaux de contrôle / gestion** permet de consulter les opérations CREATE, UPDATE et DELETE d’[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Les journaux d’activité Azure sont un exemple de ce type de journal.
* **Journaux des plans de données** permet de consulter les événements déclenchés lors de l’utilisation d’une ressource Azure. Les chaînes **Système**, **Sécurité** et **Application** de l’Observateur d’événements Windows sont des exemples de ce type de journal, dans une machine virtuelle Windows. Les journaux de diagnostics, configurés via Azure Monitor, en sont un autre exemple.
* Les **Événements traités** fournissent des informations sur les alertes et les événements analysés en votre nom. Les alertes Azure Security Center sont un exemple de ce type d’événement. Azure Security Center traite et analyse votre abonnement pour vous envoyer des alertes utiles pour votre dispositif de sécurité en cours.

L’intégration des journaux Azure prend actuellement en charge l’intégration des journaux d’activité Azure, du journal des événements Windows à partir des machines virtuelles Windows de votre abonnement Azure, des alertes Azure Security Center, des journaux de diagnostic Azure et des journaux d’audit Azure Active Directory.

>[!NOTE]
Bien que l’intégration des journaux Azure soit une solution gratuite, le stockage des informations des fichiers de journaux est facturé, via Stockage Azure.

Le tableau suivant explique la catégorie des journaux et les détails de l’intégration SIEM

| Type de journal  |Log Analytics prenant en charge JSON (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Journaux d’audit AAD |  Oui | Nécessite la création d’un fichier d’analyseur JSON FlexConnector. Pour plus d’informations, consultez la documentation d’ArcSight.  |  Vous devez créer une extension de source de journaux. Pour plus d’informations, consultez la documentation de QRadar. |  
| Journaux d’activité  | Oui  |  Le fichier de l’analyseur JSON FlexConnector est disponible dans le centre de téléchargement, ainsi que l’intégration des journaux Azure |  [Module DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)(envoyer via Syslog) |  
| Alertes ASC  | Oui  |  Nécessite la création d’un fichier d’analyseur JSON FlexConnector. Pour plus d’informations, consultez la documentation d’ArcSight. | [Module DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)(envoyer via Syslog)   |   
| Journaux de diagnostic (journaux des ressources) | Oui | L’utilisateur final doit créer le fichier de l’analyseur JSON FlexConnector. Pour plus d’informations sur cette opération, consultez la documentation d’ArcSight. | Vous devez créer une extension de source de journaux. Pour plus d’informations, consultez la documentation de QRadar. |
| Journaux des machines virtuelles | Oui, via les événements transmis et non via JSON | Oui, via les événements transmis | Oui, via les événements transmis |

Pour obtenir plus d’informations sur les types de journaux pris en charge, consultez le [FAQ](security-azure-log-integration-faq.md)


L’aide de la Communauté est disponible via le [forum MSDN d’intégration des journaux Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Le forum permet aux membres de la communauté AzLog de s’entraider, grâce à des questions, des réponses, des conseils et des astuces, afin de profiter au mieux de l’intégration des journaux Azure. En outre, l’équipe d’intégration des journaux Azure supervise ce forum et apporte son aide lorsque cela est possible.

Vous pouvez également ouvrir une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour ce faire, sélectionnez **intégration du journal** comme service pour lequel vous demandez de l’aide.

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a présenté une vue d’ensemble de l’intégration des journaux Azure. Pour plus d’informations sur l’intégration des journaux Azure et les types de journaux pris en charge, consultez les rubriques suivantes :

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) : centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation pour l’intégration des journaux Azure.
* [Prise en main de l’intégration des journaux Azure](security-azure-log-integration-get-started.md) : ce didacticiel vous guide dans la procédure d’installation de l’intégration des journaux Azure et d’intégration des journaux du stockage Azure WAD, des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure Active Directory.
* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar. Ce blog représente notre position actuelle concernant la configuration des solutions de partenaires. Dans tous les cas, reportez-vous d’abord à la documentation de la solution du partenaire.
* [Integrate Azure Activity logs and Azure Security Center Alerts over Syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) : ce billet de blog détaille les étapes pour envoyer des alertes d’activité et Azure Security Center sur syslog à QRadar
* [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
* [Intégration des alertes Security Center avec l’intégration des journaux Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) : ce document vous montre comment synchroniser les alertes Azure Security Center avec l’intégration des journaux Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

