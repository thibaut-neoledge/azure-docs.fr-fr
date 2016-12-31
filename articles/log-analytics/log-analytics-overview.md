---
title: "Présentation de Log Analytics | Microsoft Docs"
description: "Log Analytics est un service d’Operations Management Suite (OMS) qui vous permet de collecter et d’analyser les données opérationnelles générées par les ressources de votre cloud et de vos environnements locaux.  Cet article fournit une vue d&quot;ensemble des différents composants de Log Analytics ainsi que des liens vers un contenu détaillé."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: bf1455cf3975b925d114acc3e1d4cba55f2a17a9
ms.openlocfilehash: 3e7e42e2ac2deedf936ffc4d246553f72ee4dcd3


---
# <a name="what-is-log-analytics"></a>Présentation de Log Analytics
Log Analytics est un service [d’Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) qui vous permet de collecter et d’analyser les données générées par les ressources de vos environnements cloud et locaux. Il vous fournit des informations en temps réel à l'aide d’une recherche intégrée et de tableaux de bord personnalisés permettant d’analyser facilement des millions d'enregistrements dans l'ensemble de vos charges de travail et serveurs, quel que soit leur emplacement physique.

## <a name="log-analytics-components"></a>Composants de Log Analytics
Au cœur de Log Analytics se trouve le référentiel OMS, qui est hébergé dans le cloud Azure.  Les données sont collectées dans le référentiel à partir de sources connectées en configurant des sources de données et en ajoutant des solutions à votre abonnement.  Les sources de données et les solutions créeront chacune différents types d'enregistrements avec leur propre jeu de propriétés, mais elles peuvent toujours être analysées ensemble dans des requêtes vers le référentiel.  Vous pouvez ainsi utiliser les mêmes outils et méthodes pour travailler avec différents types de données collectées par différentes sources.

![Référentiel OMS](media/log-analytics-overview/overview.png)

Les sources connectées représentent les ordinateurs et autres ressources qui génèrent des données collectées par Log Analytics.  Cela peut inclure des agents installés sur des ordinateurs [Windows](log-analytics-windows-agents.md) et [Linux](log-analytics-linux-agents.md) directement connectés, ou des agents d’un [groupe d’administration System Center Operations Manager connecté](log-analytics-om-agents.md).  Log Analytics peut également collecter des données à partir d’un [stockage Azure](log-analytics-azure-storage.md).

[sources de données](log-analytics-data-sources.md) représentent les différents types de données collectées à partir de chaque source connectée.  Cela inclut les événements et les [données de performances](log-analytics-data-sources-performance-counters.md) d’agents [Windows](log-analytics-data-sources-windows-events.md) et Linux en plus des sources telles que les [journaux IIS](log-analytics-data-sources-iis-logs.md) et les [journaux de texte personnalisés](log-analytics-data-sources-custom-logs.md).  Vous configurez chaque source de données que vous souhaitez collecter, et la configuration est automatiquement remise à chaque source connectée.

## <a name="analyzing-log-analytics-data"></a>Analyse des données Log Analytics
La plupart de vos interactions avec Log Analytics s’effectuent via le portail OMS qui s'exécute dans un navigateur et vous fournit un accès aux paramètres de configuration et à plusieurs outils pour analyser et agir sur les données collectées.  À partir du portail, vous pouvez utiliser des [recherches de journaux](log-analytics-log-searches.md) pour créer des requêtes et analyser les données collectées, des [tableaux de bord](log-analytics-dashboards.md) personnalisables avec des vues graphiques de vos recherches les plus précieuses, et des [solutions](log-analytics-add-solutions.md) intégrant des fonctionnalités et des outils d’analyse supplémentaires.

![Portail OMS](media/log-analytics-overview/portal.png)

Log Analytics fournit une syntaxe de requête pour rapidement récupérer et consolider les données dans le référentiel.  Vous pouvez créer et enregistrer [des recherches de journaux](log-analytics-log-searches.md) afin d’analyser les données dans le portail OMS, ou exécuter automatiquement des recherches de journaux pour créer une alerte si les résultats de la requête signalent une condition importante.

![Recherche de journaux](media/log-analytics-overview/log-search.png)

Pour obtenir un aperçu graphique de l'intégrité de votre environnement global, vous pouvez ajouter à votre [tableau de bord](log-analytics-dashboards.md)des visualisations des recherches de journaux enregistrées.   

![tableau de bord](media/log-analytics-overview/dashboard.png)

Pour analyser des données en dehors de Log Analytics, vous pouvez exporter les données du référentiel OMS vers des outils tels que [Power BI](log-analytics-powerbi.md) ou Excel.  Vous pouvez également utiliser les [API de recherche de journal](log-analytics-log-search-api.md) pour créer des solutions personnalisées qui exploitent les données Log Analytics, ou pour les intégrer à d'autres systèmes.

## <a name="solutions"></a>solutions
Les solutions permettent d’ajouter des fonctionnalités à Log Analytics.  Elles s'exécutent principalement dans le cloud et fournissent une analyse des données collectées dans le référentiel OMS. Elles peuvent également définir de nouveaux types d'enregistrements à collecter qui peuvent être analysés avec des recherches de journaux ou via une interface utilisateur supplémentaire fournie par la solution dans le tableau de bord OMS.  

![Solution de suivi des modifications](media/log-analytics-overview/change-tracking.png)

Des solutions sont disponibles pour une variété de fonctions, et vous pouvez facilement parcourir les solutions disponibles et [les ajouter à votre espace de travail OMS](log-analytics-add-solutions.md) à partir de la galerie de solutions.  Nombre d’entre elles sont automatiquement déployées et immédiatement opérationnelles, tandis que d'autres peuvent nécessiter une configuration.

![Galerie de solutions](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Architecture Log Analytics
Les exigences de déploiement de Log Analytics sont minimes car les composants centraux sont hébergés dans le cloud Azure.  Cela inclut le référentiel en plus des services qui vous permettent de mettre en corrélation et d’analyser les données collectées.  Le portail étant accessible depuis n'importe quel navigateur, aucun logiciel client n’est requis.

Vous devez installer les agents sur des ordinateurs [Windows](log-analytics-windows-agents.md) et [Linux](log-analytics-linux-agents.md), mais aucun agent supplémentaire n’est nécessaire pour les ordinateurs déjà membres d’un [groupe d’administration SCOM connecté](log-analytics-om-agents.md).  Les agents SCOM continueront à communiquer avec les serveurs d'administration qui transmettront leurs données à Log Analytics.  Certaines solutions nécessiteront cependant que les agents communiquent directement avec Log Analytics.  La documentation de chaque solution spécifie les besoins de communication.

Lorsque vous vous [inscrivez à Log Analytics](log-analytics-get-started.md), vous créez un espace de travail OMS.  Considérez cet espace de travail comme un environnement OMS unique avec son propre référentiel de données, et ses propres sources de données et solutions. Vous pouvez créer plusieurs espaces de travail dans votre abonnement afin de prendre en charge plusieurs environnements, par exemple de production et de test.

![Architecture Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Étapes suivantes
* [Inscrivez-vous pour obtenir un compte Log Analytics gratuit](log-analytics-get-started.md) que vous pouvez tester dans votre environnement.
* Affichez les différentes [sources de données](log-analytics-data-sources.md) disponibles pour collecter des données dans le référentiel OMS.
* [Parcourez les solutions disponibles dans la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités à Log Analytics.




<!--HONumber=Dec16_HO2-->


