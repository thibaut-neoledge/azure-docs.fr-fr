---
title: Gestion des alertes dans les produits Microsoft Monitoring | Microsoft Docs
description: "Une alerte indique un problème qui nécessite une attention particulière de la part d’un administrateur.  Cet article décrit les différences relatives à la création et à la gestion des alertes dans System Center Operations Manager (SCOM) et Log Analytics et indique les meilleures pratiques en tirant parti de ces deux produits pour une stratégie de gestion des alertes hybride."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f2b0655bdddc162c7c8ef7d478b2ce1fd1993f15


---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Gestion des alertes à l’aide de Microsoft Monitoring
Une alerte indique un problème qui nécessite une attention particulière de la part d’un administrateur.  Il existe des différences entre System Center Operations Manager (SCOM) et Log Analytics dans Operations Management Suite (OMS) en termes de création, de gestion et d’analyse des alertes et de notification en cas de détection d’un problème critique.

## <a name="alerts-in-operations-manager"></a>Alertes dans Operations Manager
Dans SCOM, les alertes sont générées par des règles individuelles ou des moniteurs pour indiquer un problème spécifique.  Un moniteur peut générer une alerte lorsqu’il rencontre une erreur tandis qu’une règle peut générer une alerte pour indiquer un problème critique qui n’est pas directement lié à l’intégrité d’un objet géré.  Les packs de gestion incluent divers workflows créant des alertes pour l’application ou le service qu’ils gèrent.  Le processus de configuration d’un nouveau pack de gestion comprend son paramétrage afin de s’assurer que vous ne recevez pas trop d’alertes pour les problèmes que vous ne considérez pas comme étant critiques.

![Affichage des alertes SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM offre une gestion complète des alertes avec possibilité de modification de leur état par les administrateurs à mesure qu’ils travaillent à leur résolution.  Lorsque le problème a été résolu, l’administrateur clôt l’alerte de manière à ce qu’elle n’apparaisse plus dans les vues affichant les alertes actives.  Les alertes générées à partir de moniteurs peuvent être résolues automatiquement dès que le moniteur retrouve son intégrité.

![État des alertes](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertes dans Log Analytics
Une alerte dans Log Analytics est créée à partir d’une requête de journal qui s’exécute automatiquement à intervalles réguliers.  Vous pouvez créer une règle d’alerte à partir de n’importe quelle requête de journal.  Si la requête retourne des résultats qui correspondent aux critères que vous spécifiez, une alerte est créée.  Il peut s’agir d’une requête spécifique créant une alerte en cas de détection d’un événement particulier, ou d’une requête plus générale qui recherche tous les événements d’erreur liés à une application en particulier.

Les alertes Log Analytics sont écrites dans le référentiel OMS en tant qu’événement et peuvent être récupérées à l’aide d’une requête de journal.  Elles ne disposent pas d’un état SCOM qui vous permet d’indiquer quand le problème a été résolu.

![Alerte OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Lorsque SCOM est utilisé comme source de données pour Log Analytics, les alertes SCOM sont écrites dans le référentiel OMS au fil de leur création et modification.  

![Alerte SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

La [solution de gestion des alertes](http://technet.microsoft.com/library/mt484092.aspx) propose un résumé des alertes actives et plusieurs requêtes courantes permettant de récupérer différents ensembles d’alertes.  Cette analyse de vos alertes est plus efficace qu’un rapport dans SCOM.  Les synthèses vous permettent d’examiner des données détaillées et vous pouvez créer des requêtes ad hoc permettant de récupérer différents ensembles d’alertes.

![solution de gestion des alertes](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notifications
Dans SCOM les alertes vous envoient un e-mail ou un message en réponse aux alertes qui correspondent à des critères particuliers.  Vous pouvez créer différents abonnements aux notifications permettant de notifier diverses personnes en fonction de critères, tels que l’objet analysé, la gravité de l’alerte, le type de problème détecté ou l’heure de la journée.

Plusieurs abonnements peuvent être utilisés pour implémenter une stratégie de notification complète pour un grand nombre de packs de gestion.

![Alertes SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Log Analytics peut vous informer par e-mail qu’une alerte a été créée en définissant une action de notification par courrier électronique sur chaque [règle d’alerte](http://technet.microsoft.com/library/mt614775.aspx).  À la différence de SCOM, cet outil ne permet pas de s’inscrire à plusieurs alertes avec une seule règle.  Vous devez également créer vos propres règles d’alerte dans la mesure où OMS ne fournit aucune règle préconfigurée.

![Alertes Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Cependant, vous ne pouvez pas gérer complètement les alertes SCOM dans Log Analytics étant donné que seule la console Opérateur permet leur modification.  Toutefois, Log Analytics est utile dans le cadre d’un processus de gestion des alertes pour proposer des outils d’analyse, dont SCOM à lui seul ne dispose pas.

## <a name="alert-remediation"></a>Correction des alertes
[Correction](http://technet.microsoft.com/library/mt614775.aspx) fait référence à une tentative de correction automatique du problème identifié par une alerte.

SCOM vous permet d’exécuter les diagnostics et récupérations en réponse à un moniteur présentant un défaut d’intégrité.  Cela se produit simultanément sur moniteur créant l’alerte.  Les diagnostics et récupérations sont généralement implémentés sous la forme d’un script qui s’exécute sur l’agent.  Un diagnostic tente de collecter plus d’informations sur le problème détecté tandis qu’une récupération tente de résoudre le problème.

Log Analytics vous permet de démarrer un [runbook Azure Automation](https://azure.microsoft.com/documentation/services/automation/) ou d’appeler un webhook en réponse à une alerte Log Analytics.  Les runbooks peuvent comporter une logique complexe implémentée dans PowerShell.  Le script s’exécute dans Azure et peut accéder aux ressources Azure ou aux ressources externes disponibles à partir du cloud.  Azure Automation a la possibilité d’exécuter des runbooks sur un serveur dans votre centre de données local, mais cette fonctionnalité n’est pas disponible actuellement lors du démarrage du runbook en réponse aux alertes Log Analytics.

Les récupérations dans SCOM et les runbooks dans OMS peuvent contenir des scripts PowerShell, toutefois les récupérations sont plus difficiles à créer et à gérer, dans la mesure où elles doivent être contenues dans un pack de gestion.  Les runbooks sont stockés dans Azure Automation qui fournit des fonctionnalités permettant de les créer, les tester et les gérer.

Si vous utilisez SCOM comme source de données pour Log Analytics, vous pouvez créer une alerte Log Analytics à l’aide d’une requête de journal pour récupérer les alertes SCOM stockées dans le référentiel OMS.  Cela vous permettrait d’exécuter un runbook Azure Automation en réponse à une alerte SCOM.  Bien sûr, étant donné que le runbook s’exécute dans Azure, cela ne serait pas une stratégie viable pour les récupérations de problèmes locaux.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus d’informations sur les [alertes dans System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).




<!--HONumber=Nov16_HO3-->


