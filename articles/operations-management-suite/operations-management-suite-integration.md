---
title: "Intégration avec Operations Management Suite (OMS) | Microsoft Docs"
description: "Outre l’utilisation des fonctionnalités standard d’OMS, vous pouvez intégrer celles-ci à d’autres applications et services de gestion pour fournir un environnement de gestion hybride, offrir des scénarios de gestion personnalisés propres à votre environnement ou fournir une gestion personnalisée pour vos clients.  Cet article fournit une vue d’ensemble des différentes options pour l’intégration avec OMS, ainsi que des liens vers des articles incluant des informations techniques détaillées."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>Intégration avec Operations Management Suite (OMS)
Operations Management Suite est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et dans le cloud.  Outre l’utilisation des fonctionnalités standard d’OMS, vous pouvez intégrer celles-ci à d’autres applications et services de gestion pour fournir un environnement de gestion hybride, offrir des scénarios de gestion personnalisés propres à votre environnement ou fournir une gestion personnalisée pour vos clients.  Cet article fournit une vue d’ensemble des différentes options pour l’intégration avec les services OMS, ainsi que des liens vers des articles incluant des informations techniques détaillées. 

## <a name="log-analytics"></a>Log Analytics
Les données de gestion collectées par Log Analytics sont stockées dans le référentiel OMS, qui est hébergé dans Azure.  Toutes les données stockées dans le référentiel sont disponibles dans les recherches de journal qui procurent une analyse rapide d’immenses volumes de données.  Vos besoins d’intégration peuvent être de remplir le référentiel avec de nouvelles données afin de le rendre disponible pour analyse, ou d’extraire des données du référentiel afin de fournir une nouvelle visualisation ou d’intégrer un autre outil de gestion.

Chacune des données du référentiel OMS sont stockées sous forme d’enregistrement.  Lorsque vous remplissez le référentiel, vous devez fournir aux utilisateurs le type d’enregistrement qu’utilise votre solution et une description de ses propriétés.  Lorsque vous récupérez des données, vous avez besoin de ces informations à propos des données que vous utilisez.

![Remplissage du référentiel OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Remplissage du référentiel Log Analytics
Il existe plusieurs méthodes permettant de remplir le référentiel OMS.  La méthode que vous utilisez dépend de facteurs tels que l’emplacement de la source de données, le format des données et les clients que vous devez prendre en charge.  Une fois que les données sont stockées dans le référentiel, la manière dont elles ont été collectées importe peu.

Les sections suivantes décrivent différentes options pour remplir le référentiel OMS.

#### <a name="connected-sources-and-data-sources"></a>Sources de données et sources connectées
Les sources connectées sont les emplacements où les données peuvent être extraites du référentiel OMS.  Les solutions et les sources de données s’exécutent sur les sources connectées et définissent les données spécifiques qui sont collectées.  Si votre application écrit des données dans une de ces sources de données, vous pouvez les collecter en configurant la source de données.  Par exemple, si votre application crée des événements Syslog, ceux-ci peuvent être collectés par la source de données Syslog sur un agent Linux.

* [Sources de données dans Log Analytics](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Solutions
Les solutions étendent les capacités d’OMS.  Une solution peut collecter des données à partir de la source connectée ou elle peut effectuer une analyse sur les enregistrements déjà collectés dans le référentiel.  Chaque solution fournie par Microsoft est présentée dans un article spécifique qui fournit des informations sur les données collectées.

* [Solutions dans Log Analytics](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>API Collecte de données HTTP
L’API REST Collecte de données HTTP Log Analytics permet d’ajouter des données JSON au référentiel Log Analytics.  Vous pouvez tirer parti de cette API lorsque vous disposez d’une application qui ne fournit pas de données via une des autres sources de données ou solutions.  Elle peut être utilisée pour renseigner le référentiel à partir d’un client qui peut appeler l’API et ne repose pas sur la planification de la collecte d’une source de données ou d’une solution.

* [API Collecte de données HTTP Log Analytics](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Récupération des données du référentiel Log Analytics
Il existe plusieurs méthodes permettant de récupérer les données du référentiel OMS.  Vous pouvez faire en sorte que les utilisateurs récupèrent des données à l’aide de la console OMS et leur fournir différents types de visualisations et d’analyses.  Vous pouvez également récupérer les données à partir d’un processus externe comme une autre solution de gestion.

#### <a name="log-searches"></a>Recherches dans les journaux
Toutes les données stockées dans le référentiel OMS sont disponibles via des recherches dans les journaux.  Les utilisateurs peuvent effectuer leur propre analyse ad hoc dans la console OMS ou créer un tableau de bord avec une visualisation pour une recherche de journal spécifique.  Les solutions peuvent contenir des vues personnalisées avec des visualisations basées sur les recherches prédéfinies.  Vous pouvez utiliser l’API Recherche de journal pour accéder aux données du référentiel OMS à partir d’un outil de gestion ou d’une application externe.  

* [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md)
* [API REST Recherche de journal Log Analytics](../log-analytics/log-analytics-log-search-api.md)
* [Applets de commande Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Vues personnalisées
Le Concepteur de vues permet de créer des vues personnalisées dans la console OMS. Celles-ci fournissent aux utilisateurs des visualisations et analyses des données de la solution.  Chaque vue inclut une vignette qui s’affiche sur la page principale de la console et des éléments de visualisation basés sur des recherches de journaux que vous définissez.

* [Concepteur de vues Log Analytics](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Log Analytics peut exporter automatiquement les données entre le référentiel OMS et Power BI afin de vous permettre de tirer parti de ses visualisations et de ses outils d’analyse.  Il effectue cette exportation selon une planification afin que les données soient à jour. 

* [Exportation de données Log Analytics vers Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automatisation
OMS peut automatiser les processus pour réagir aux données collectées ou pour exécuter d’autres fonctions de gestion.  Il peut collecter des données à partir de votre application et les insérer dans le référentiel OMS. Vous pouvez également automatiser la correction d’un problème connu en réponse aux données trouvées dans le référentiel. 

![Automatisation](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks
Dans Azure Automation, les runbooks exécutent des flux de travail et des scripts PowerShell dans le cloud Azure.  Vous pouvez les utiliser pour gérer les ressources dans Azure ou d’autres ressources accessibles à partir du cloud.  Les runbooks peuvent également être exécutés dans un centre de données local à l’aide de Runbook Worker hybride.  Vous pouvez démarrer un runbook à partir du portail Azure ou de processus externes à l’aide d’un certain nombre de méthodes telles que PowerShell ou l’API Automation.

* [Démarrage d’un Runbook dans Azure Automation](../automation/automation-starting-a-runbook.md)
* [Applets de commande Azure Automation](https://msdn.microsoft.com/library/dn690262.aspx)
* [API REST Automation](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertes
Les règles d’alerte exécutent automatiquement les recherches de journaux selon une planification.  Si les résultats correspondent aux critères particuliers, l’alerte qui en résulte peut démarrer un runbook dans Azure Automation ou appeler un webhook qui peut démarrer un processus externe.  Ces deux réponses peuvent inclure des détails sur l’alerte, y compris les données renvoyées dans la recherche de journal.

* [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md)
* [API Alerte Log Analytics](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Sauvegarde et Site Recovery
Les services Sauvegarde Azure et Azure Site Recovery Azure permettent de protéger vos données d’entreprise et garantissent la disponibilité des serveurs et des applications.  Vous pouvez tirer parti de ces services pour exécuter des scénarios tels que la fourniture de services de sauvegarde pour votre application ou l’initialisation du basculement d’une machine virtuelle.

* [Applets de commande Sauvegarde Azure](https://msdn.microsoft.com/library/mt619253.aspx)
* [API REST Azure Site Recovery](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Applets de commande Azure Site Recovery](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Solutions personnalisées
Vous pouvez encapsuler la logique d’intégration dans une solution personnalisée à exécuter dans votre espace de travail ou dans l’espace de travail du client.  Votre solution peut inclure une des méthodes d’intégration décrites dans cet article ainsi que d’autres ressources pour fournir un scénario de gestion complet.  Les ressources de la solution sont packagées de façon à ce que, quand la solution est supprimée, toutes les ressources qu’elle a créées sont supprimées de l’espace de travail OMS et de l’abonnement Azure.

Par exemple, votre solution peut inclure un runbook Automation qui permet de collecter et de traiter les données, puis de renseigner le référentiel Log Analytics à l’aide de l’API de collecte de données HTTP.  Vous pouvez également inclure une vue personnalisée qui affiche et analyse les données collectées.  

* Création de solutions personnalisées (bientôt disponible)    

## <a name="next-steps"></a>Étapes suivantes
* Consultez le [Kit de développement logiciel (SDK) OMS](operations-management-suite-sdk.md) pour accéder à des informations techniques sur l’automatisation des services OMS.  

