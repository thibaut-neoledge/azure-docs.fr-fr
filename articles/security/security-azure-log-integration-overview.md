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
ms.date: 08/10/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 1aa93ac52d1d5c4efe222c6da505e3639170cf55
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Présentation de l’intégration des journaux Microsoft Azure
Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement.

## <a name="overview"></a>Vue d'ensemble

L’intégration des journaux Azure est une solution gratuite qui permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux.

Le service Azure Log Integration collecte les événements Windows à partir des journaux de l’Observateur d’événements, des [journaux d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), des [alertes Azure Security Center](../security-center/security-center-intro.md) et des [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) des ressources Azure. Cette intégration aide votre solution SIEM à offrir un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.

>[!NOTE]
À l’heure actuelle, seuls les clouds Azure Commercial et Azure Government sont pris en charge. Les autres clouds ne sont pas pris en charge.

![Intégration des journaux Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?
Azure génère une journalisation complète pour chaque service Azure. Ces journaux représentent trois types de journaux :

* **Journaux de contrôle / gestion** permet de consulter les opérations CREATE, UPDATE et DELETE d’[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Les journaux d’activité Azure sont un exemple de ce type de journal.
* **Journaux des plans de données** permet de consulter les événements déclenchés lors de l’utilisation d’une ressource Azure. Les chaînes **Système**, **Sécurité** et **Application** de l’Observateur d’événements Windows sont des exemples de ce type de journal, dans une machine virtuelle Windows. Les journaux de diagnostics, configurés via Azure Monitor, en sont un autre exemple.
* Les **Événements traités** fournissent des informations sur les alertes et les événements analysés en votre nom. Les alertes Azure Security Center sont un exemple de ce type d’événement. Azure Security Center traite et analyse votre abonnement pour vous envoyer des alertes utiles pour votre dispositif de sécurité en cours.

Azure Log Integration prend en charge ArcSight, QRadar et Splunk. Dans tous les cas, veuillez vous renseigner auprès de votre vendeur de SIEM pour évaluer s’ils disposent d’un connecteur natif. Dans certains cas, vous n’aurez pas besoin d’utiliser Azure Log Integration lorsque des connecteurs natifs seront disponibles. Pour plus d’informations sur les types de journaux pris en charge, consultez le FAQ.

>[!NOTE]
Bien que l’intégration des journaux Azure soit une solution gratuite, le stockage des informations des fichiers de journaux est facturé, via Stockage Azure.

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

