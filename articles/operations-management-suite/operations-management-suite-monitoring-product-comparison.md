---
title: Comparaison des produits de surveillance Microsoft | Microsoft Docs
description: "Microsoft Operations Management Suite (OMS) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.  Cet article identifie les différents services inclus dans OMS et fournit des liens vers leur documentation détaillée."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d7325b0bb900dcaf789c01e1a9b534d7f72c8c43


---
# <a name="microsoft-monitoring-product-comparison"></a>Comparaison des produits de surveillance Microsoft
Cet article confronte les solutions System Center Operations Manager (SCOM) et Log Analytics de la suite OMS (Operations Management Suite) en comparant leur architecture, la logique utilisée pour surveiller les ressources et la manière dont elles analysent les données collectées.  L’objectif est de vous présenter leurs principales différences ainsi que leurs points forts respectifs.  

## <a name="basic-architecture"></a>Architecture de base
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Tous les composants SCOM sont installés dans votre centre de données.  [Les agents sont installés](http://technet.microsoft.com/library/hh551142.aspx) sur des ordinateurs Windows et Linux gérés par SCOM.  Les agents se connectent aux [serveurs d’administration](https://technet.microsoft.com/library/hh301922.aspx) qui communiquent avec l’entrepôt de données et la base de données SCOM.  Les agents utilisent l’authentification de domaine pour se connecter aux serveurs d’administration.  Les agents qui se trouvent en dehors d’un domaine approuvé peuvent utiliser une authentification par certificat ou se connecter à un [serveur de passerelle](https://technet.microsoft.com/library/hh212823.aspx).

La solution SCOM nécessite deux bases de données SQL, une pour les données opérationnelles et un autre entrepôt de données pour la création de rapports et l’analyse de données.  Un [serveur de rapports](https://technet.microsoft.com/library/hh298611.aspx) exécute les services SQL Reporting Services pour consigner les données à partir de l’entrepôt de données. 

La solution SCOM peut surveiller les ressources cloud à l’aide de packs d’administration pour des produits tels qu’[Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708) et [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Ces packs d’administration utilisent un ou plusieurs agents locaux comme serveurs proxy pour détecter les ressources de cloud et exécuter des flux de travail visant à mesurer leurs performances et leur disponibilité.  Les agents proxy sont également utilisés pour [surveiller les appareils réseau](https://technet.microsoft.com/library/hh212935.aspx) et d’autres ressources externes.

La console Opérateur est une application Windows qui se connecte à l’un des serveurs d’administration pour permettre à l’administrateur de consulter et d’analyser les données collectées, mais aussi de configurer l’environnement SCOM.  Une console web peut être hébergée sur n’importe quel serveur IIS et analyse les données via un navigateur.

![Architecture SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
La plupart des composants OMS sont hébergés dans le cloud Azure, ce qui vous permet de les déployer et de les gérer en toute simplicité et à moindre coût.  Toutes les données collectées par Log Analytics sont stockées dans le référentiel OMS.

Log Analytics peut collecter des données à partir de l’une de ces trois sources :

* Des machines physiques et virtuelles exécutant Windows et l’agent [MMA (Microsoft Monitoring Agent)](https://technet.microsoft.com/library/mt484108.aspx) ou exécutant Linux et l’agent [Operations Management Suite pour Linux](https://technet.microsoft.com/library/mt622052.aspx).  Ces machines peuvent être locales ou virtuelles (hébergées dans Azure ou dans un autre cloud).
* Un compte de stockage Azure qui collecte les données [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) à partir d’un rôle de travail, d’un rôle Web ou d’une machine virtuelle dans Azure.
* Une [connexion à un groupe d’administration SCOM](https://technet.microsoft.com/library/mt484104.aspx).  Dans cette configuration, les agents communiquent avec les serveurs d’administration SCOM qui envoient les données à la base de données SCOM, qui les transmet ensuite au magasin de données OMS.
  Les administrateurs analysent les données collectées et configurent Log Analytics depuis le portail OMS qui est hébergé dans Azure et accessible à partir de n’importe quel navigateur.  Les applications mobiles permettant d’accéder à ces données sont disponibles pour les plateformes standard.

![Architecture Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Intégration des solutions SCOM et Log Analytics
Lorsque SCOM est utilisé comme source de données pour Log Analytics, vous pouvez tirer parti des fonctionnalités des deux solutions dans un environnement de surveillance hybride.  Dans la console Opérateur, vous pouvez configurer les agents SCOM existants pour qu’ils soient gérés par OMS, tout en continuant à exécuter les packs d’administration depuis SCOM.  
Les données d’un groupe d’administration SCOM connecté sont envoyées à Log Analytics via l’une des quatre méthodes suivantes :

* Les événements et les données de performance sont collectés par l’agent et transmis à SCOM.  Les serveurs d’administration dans SCOM envoient ensuite les données à Log Analytics.
* L’agent continue de transmettre directement à Log Analytics certains événements tels que les journaux IIS et les événements de sécurité.
* Certaines solutions ajoutent un logiciel supplémentaire à l’agent ou nécessitent l’installation d’un logiciel pour collecter des données supplémentaires.  Ces données sont généralement transmises directement à Log Analytics.
* Certaines solutions collectent les données directement à partir des serveurs d’administration SCOM sans passer par l’agent.  Par exemple, la [solution de gestion des alertes](https://technet.microsoft.com/library/mt484092.aspx) collecte les alertes à partir de SCOM après leur création.

## <a name="monitoring-logic"></a>Logique de surveillance
SCOM et Log Analytics exploitent le même type de données collectées auprès des agents. Cependant, les deux solutions n’utilisent pas la même logique pour collecter les données et les analysent différemment.

### <a name="operations-manager"></a>Operations Manager
La logique de surveillance de SCOM repose sur les [packs d’administration](https://technet.microsoft.com/library/hh457558.aspx) qui contiennent la logique de détection des composants à surveiller, d’évaluation de l’intégrité de ces composants et de collecte des données à analyser.  La surveillance des données peut simplement consister à collecter un événement ou à mesurer les performances, ou utiliser une logique complexe implémentée dans un script.  Les packs d’administration qui comprennent une solution de surveillance complète sont disponibles pour diverses [applications Microsoft et tierces](http://go.microsoft.com/fwlink/?LinkId=82105) en plus des appareils matériels et réseau.  Vous pouvez [créer vos propres packs d’administration](http://aka.ms/mpauthor) pour vos applications personnalisées.

Les packs d’administration contiennent plusieurs flux de travail qui exécutent chacun une fonction de surveillance spécifique, comme par exemple effectuer un échantillonnage du compteur de performances, vérifier l’état d’un service ou exécuter un script.  Chaque flux de travail s’exécute de manière indépendante et définit ses propres résultats tels que la base de données dans laquelle il va écrire et si une alerte sera générée. 

Vous pouvez modifier les détails des flux de travail tels que leur fréquence d’exécution, le seuil d’erreur et la gravité de l’alerte qu’ils génèrent.  Vous pouvez également déployer des fonctionnalités supplémentaires en ajoutant vos propres flux de travail.

![Remplace](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Les packs d’administration sont installés dans la base de données d’Operations Manager et automatiquement distribués aux agents par les serveurs d’administration.  Chaque agent télécharge les packs d’administration et charge les flux de travail associés aux applications dont ils disposent.  Les données collectées par l’agent sont envoyées sur le serveur d’administration afin d’être ajoutées dans la base de données et l’entrepôt de données SCOM.  La console Opérateur vous permet de consulter et d’analyser ces données sous la forme de vues personnalisées, de tableaux de bord et de rapports inclus dans le pack d’administration.

La distribution des packs d’administration est illustrée dans le schéma suivant.

![Flux du pack d’administration](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Collecte d’événements et de données de performance
Log Analytics collecte les événements et les compteurs de performances des systèmes de l’agent à l’aide de sources telles que le journal des événements Windows, les journaux IIS et Syslog.  Vous pouvez définir les critères de collecte des données depuis le portail Log Analytics et créer ensuite des requêtes de journal pour analyser les données collectées.  Un ensemble de critères standard est défini lorsque vous créez votre espace de travail OMS. Vous pouvez également définir des critères supplémentaires pour des applications spécifiques. 

![Définition des journaux des événements dans Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

La solution SCOM inclut de nombreux flux de travail détaillés qui définissent généralement des critères spécifiques pour la collecte des données et les actions associées à effectuer. La solution Log Analytics inclut quant à elle des critères plus généraux pour la collecte des données.  Les solutions et les requêtes de journal offrent des critères plus ciblés pour l’analyse des données et les actions à effectuer dans le cloud sur les données collectées.

#### <a name="solutions"></a>Solutions
Les solutions offrent une logique supplémentaire pour la collecte et l’analyse des données.  Vous pouvez sélectionner les solutions à ajouter à votre abonnement OMS à partir de la galerie de solutions.

![Galerie de solutions](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Les solutions s’exécutent principalement dans le cloud et fournissent une analyse des événements et des compteurs de performances collectés dans le référentiel OMS.  Elles permettent également de définir de nouveaux types de données à collecter qui pourront être analysés à l’aide de requêtes de journal ou via une interface utilisateur supplémentaire fournie par la solution dans le tableau de bord OMS. 

Par exemple, la [solution de suivi des modifications](https://technet.microsoft.com/library/mt484099.aspx) détecte les modifications de configuration apportées aux systèmes de l’agent et écrit les événements dans le référentiel OMS. Ces événements peuvent ensuite être analysés à partir de plusieurs vues graphiques qui récapitulent les modifications détectées.  Vous pouvez explorer les détails de la synthèse pour consulter les requêtes de journal qui affichent les données détaillées collectées par la solution.

Si vous pouvez sélectionner des solutions à ajouter à votre abonnement, vous ne pouvez cependant pas créer vos propres solutions.  Vous pouvez sélectionner les événements et les compteurs de performances pour collecter et créer des vues personnalisées en fonction de vos propres requêtes de journalisation.

La logique de surveillance de Log Analytics est expliquée dans le schéma suivant.

![Flux de la solution Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Surveillance de l’intégrité
### <a name="operations-manager"></a>Operations Manager
SCOM peut modéliser les différents composants d’une application et afficher en temps réel l’intégrité de chacun d’eux.  Cela vous permet non seulement d’afficher les erreurs détectées et les performances sur une période donnée, mais aussi de contrôler à tout moment l’état d’une application ou d’un système et de chacun de leurs composants.  En sachant à quel moment une application est disponible, le moteur d’intégrité de SCOM peut prendre en charge les contrats de niveau de service qui analysent et enregistrent la disponibilité d’une application au fil du temps.

Par exemple, la vue ci-dessous montre l’état en temps réel des moteurs de base de données SQL surveillés par SCOM.  L’état de chacune des bases de données pour l’un des moteurs de base de données est indiqué dans la partie inférieure de la vue.

![Vue d’état](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

L’explorateur d’intégrité pour l’un des moteurs de base de données est représenté ci-dessous avec les paramètres de surveillance utilisés pour définir son intégrité globale.  Ces paramètres de surveillance sont définis dans le pack d’administration SQL et exécutés pour tous les moteurs de base de données SQL détectés par SCOM.

![Explorateur d’intégrité](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Les composants de plusieurs systèmes peuvent être combinés pour analyser l’intégrité d’une application distribuée.  Cela peut être particulièrement utile pour les applications métier qui intègrent plusieurs composants distribués.  Vous pouvez créer un modèle qui analyse l’intégrité de chaque composant ayant un rôle dans la disponibilité de l’application.

Active Directory est un exemple de pack d’administration qui fournit un modèle pour analyser les composants distribués.  Le schéma ci-dessous montre l’intégrité de l’environnement global et les relations entre les forêts, les domaines et les contrôleurs de domaine.  Chacun de ces composants inclut des sous-composants et plusieurs paramètres de surveillance, comme dans l’exemple SQL ci-dessus.

![Vue schématique de SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS n’inclut aucun moteur commun pour modéliser les applications ou analyser leur intégrité en temps réel.  Les solutions individuelles peuvent évaluer l’intégrité globale de services spécifiques en fonction des données collectées et installer une logique personnalisée sur l’agent pour effectuer une analyse en temps réel.  Les solutions s’exécutent dans le cloud et ont accès au référentiel OMS. Elles offrent donc souvent une analyse plus approfondie que celle effectuée par les packs d’administration. 

Par exemple, les [solutions d’évaluation d’AD et de SQL](https://technet.microsoft.com/library/mt484102.aspx) analysent les données collectées et procèdent à une évaluation des différents aspects de l’environnement.  Elles recommandent des modifications à apporter pour améliorer la disponibilité et les performances de l’environnement.

![Solution d’évaluation d’AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Analyse des données
SCOM et Log Analytics offrent des fonctionnalités différentes en termes d’analyse des données collectées.  SCOM comprend des vues et des tableaux de bord dans la console Opérateur qui permettent d’analyser des données récentes à partir de divers formats et rapports pour présenter les données de l’entrepôt de données sous forme de tableau.  Log Analytics fournit un langage et une interface de requête de journal complets pour analyser les données dans le référentiel OMS.  Lorsque SCOM est utilisé comme source de données pour Log Analytics, le référentiel inclut les données collectées par SCOM afin que les outils Log Analytics puissent être utilisés pour analyser les données des deux systèmes.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Views
Dans la console Opérateur, les vues permettent d’afficher différents types de données collectées par SCOM dans différents formats, généralement sous forme de tableau pour les événements, les alertes et les données d’état, et sous forme de graphique linéaire pour les données de performance.  Les vues permettent d’effectuer une analyse minimale ou une consolidation des données, mais pas d’appliquer des filtres en fonction de critères spécifiques. 

![Views](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Les packs d’administration incluent généralement plusieurs vues pour l’application ou le système surveillé.  Par exemple, des vues d’état pour les différents objets détectés par le pack d’administration, des vues d’alerte pour les problèmes détectés et des vues de performance pour les compteurs.

Les vues sont idéales pour analyser l’état actuel de l’environnement, y compris les alertes actives et l’état d’intégrité des systèmes et objets surveillés.  Vous pouvez afficher les détails des événements ou des données de performance d’une alerte spécifique afin d’en identifier sa cause première. De même, vous pouvez afficher les performances et l’intégrité des différents composants d’une application afin d’évaluer son état actuel.

#### <a name="dashboards"></a>Tableaux de bord
Dans la console Opérateur, les tableaux de bord utilisent quasiment les mêmes données que les vues. Cependant, ils sont davantage personnalisables et peuvent fournir une meilleure visualisation des données.  Vous disposez d’un ensemble de tableaux de bord standard que vous pouvez facilement personnaliser selon vos besoins.  Vous pouvez également utiliser un widget PowerShell qui peut afficher les données obtenues à l’aide d’une requête PowerShell.

![Tableau de bord](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Les développeurs ont la possibilité d’ajouter des composants personnalisés aux tableaux de bord fournis dans leurs packs d’administration.  Ces composants peuvent être spécifiques à une application, comme le tableau de bord intégré au pack d’administration SQL représenté ci-dessous.  Ce tableau de bord peut également servir de modèle pour les versions personnalisées.

![Tableau de bord SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Rapports
Dans SCOM, les rapports permettent d’analyser les données de l’entrepôt de données sous forme de tableau.  Ils peuvent être imprimés et exécutés automatiquement dans différents formats de fichiers, notamment PDF, CSV et Word.  Les rapports utilisent les données de l’entrepôt de données pour permettre une analyse des tendances à long terme.

Les packs d’administration incluent généralement des rapports personnalisés pour une application spécifique.  Vous pouvez également sélectionner des rapports à partir d’une bibliothèque de rapports génériques que vous pouvez personnaliser pour vos propres applications ou utiliser pour effectuer une analyse ad hoc.

Voici un exemple de rapport de performances affichant les données collectées par le pack d’administration Active Directory.

![Rapport](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics intègre un [langage de requête](https://technet.microsoft.com/library/mt484120.aspx) que vous pouvez utiliser pour analyser les données provenant de plusieurs applications sans avoir besoin de créer une vue ou un rapport personnalisé.  OMS étant déployé dans le cloud, les performances des requêtes et de l’analyse de données ne sont pas soumises à des restrictions matérielles. Vous pouvez ainsi analyser rapidement des requêtes comprenant des millions d’enregistrements. 

Les requêtes Log Analytics offrent également d’autres fonctionnalités.  Vous pouvez enregistrer une requête, exporter ses résultats dans Excel ou la configurer pour qu’elle s’exécute à intervalles réguliers et génère une alerte si ses résultats répondent à des critères spécifiques.  

![Flux d’une requête de journal](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Voici un exemple d’une requête Log Analytics.  Dans cet exemple, tous les événements dont le nom contient le mot « démarré » sont consignés et regroupés par ID d’événement.  L’utilisateur se contente de formuler la requête et Log Analytics génère de manière dynamique l’interface utilisateur pour effectuer l’analyse.  Il suffit ensuite de sélectionner un événement dans la liste pour en afficher les données détaillées.

![Requête de journal](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Si les requêtes Log Analytics permettent d’effectuer une analyse ad hoc, elles peuvent également être enregistrées pour une utilisation ultérieure et ajoutées à votre [tableau de bord OMS](http://technet.microsoft.com/library/mt484090.aspx) comme indiqué dans l’exemple suivant.

![tableau de bord OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes
* Déployer [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx)
* S’inscrire au service [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics)  




<!--HONumber=Nov16_HO3-->


