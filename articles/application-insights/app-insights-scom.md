---
title: "Intégration de SCOM à Application Insights | Microsoft Docs"
description: "Si vous êtes un utilisateur SCOM, analysez les performances et diagnostiquez les problèmes avec Application Insights. Tableaux de bord complets, alertes intelligentes, requêtes analytiques et outils de diagnostic efficaces."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/12/2016
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 68ec072b972e38d8cd020adda4dcc85cdaccfb76
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016

---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Analyse des performances des applications à l’aide d’Application Insights pour SCOM
Si vous utilisez System Center Operations Manager (SCOM) pour gérer vos serveurs, vous pouvez analyser les performances et diagnostiquer les problèmes afférents à l’aide [d’Azure Application Insights](app-insights-asp-net.md). Application Insights analyse les demandes entrantes de votre application web, les appels SQL et REST sortants, les exceptions et les suivis de journal. Elle fournit des tableaux de bord avec des graphiques de mesure et des alertes intelligentes, ainsi que des requêtes analytiques et des outils de recherche de diagnostic efficaces sur ces données de télémétrie. 

Vous pouvez activer l’analyse Application Insights à l’aide d’un pack d’administration SCOM.

## <a name="before-you-start"></a>Avant de commencer
Nous partons de l’hypothèse suivante :

* Vous maîtrisez SCOM et vous utilisez SCOM 2012 R2 ou 2016 pour gérer vos serveurs web IIS.
* Vous avez déjà installé sur vos serveurs une application web que vous souhaitez analyser avec Application Insights.
* La version de l’infrastructure de l’application est .NET 4.5 ou une version ultérieure.
* Vous avez accès à un abonnement dans [Microsoft Azure](https://azure.com) et pouvez vous connecter au [portail Azure](https://portal.azure.com). Votre organisation doit posséder un abonnement et peut y ajouter votre compte Microsoft.

(L’équipe de développement peut créer le [kit de développement logiciel (SDK) Application Insights](app-insights-asp-net.md) dans l’application web. Cette instrumentation en cours de création lui donne plus de flexibilité dans l’écriture des données de télémétrie personnalisées. Toutefois, cela n’a aucune importance : vous pouvez suivre les étapes décrites ici, avec ou sans le kit de développement logiciel (SDK) intégré.)

## <a name="one-time-install-application-insights-management-pack"></a>(Une fois) Installer le pack d’administration Application Insights
Sur l’ordinateur sur lequel vous exécutez Operations Manager :

1. Désinstallez les anciennes versions du pack d’administration :
   1. Dans Operations Manager, ouvrez Administration, Packs d’administration. 
   2. Supprimez l’ancienne version.
2. Téléchargez et installez le pack d’administration à partir du catalogue.
3. Redémarrez Operations Manager.

## <a name="create-a-management-pack"></a>Créer un pack d’administration
1. Dans Operations Manager, ouvrez **Création**, **.NET...with Application Insights** (.NET... avec Application Insights), **Assistant Ajout d’analyse**, et choisissez à nouveau **.NET...with Application Insights** (.NET... avec Application Insights).
   
    ![](./media/app-insights-scom/020.png)
2. Nommez la configuration d’après votre application. (Vous devez instrumenter une application à la fois.)
   
    ![](./media/app-insights-scom/030.png)
3. Sur la même page de l’Assistant, créez un pack d’administration ou sélectionnez un pack que vous avez créé précédemment pour Application Insights.
   
     (Le [pack d’administration](https://technet.microsoft.com/library/cc974491.aspx) Application Insights est un modèle à partir duquel vous créez une instance. Vous pouvez réutiliser la même instance ultérieurement.)

    ![Dans l’onglet Propriétés générales, saisissez le nom de l’application. Cliquez sur Nouveau et saisissez un nom pour un pack d’administration. Cliquez sur OK, puis sur Suivant.](./media/app-insights-scom/040.png)

1. Choisissez une application que vous souhaitez analyser. La fonctionnalité de recherche effectue une recherche parmi les applications installées sur vos serveurs.
   
    ![Dans l’onglet What to Monitor (Éléments à analyser), cliquez sur Ajouter, saisissez une partie du nom de l’application, cliquez sur Rechercher, choisissez l’application, ensuite cliquez sur Ajouter, puis sur OK.](./media/app-insights-scom/050.png)
   
    Le champ de portée Analyse facultatif peut être utilisé pour spécifier un sous-ensemble de vos serveurs, si vous ne souhaitez pas analyser l’application dans tous les serveurs.
2. Sur la page suivante de l’Assistant, vous devez d’abord fournir vos informations d’identification pour vous connecter à Microsoft Azure.
   
    Sur cette page, vous choisissez la ressource Application Insights dans laquelle vous souhaitez que les données de télémétrie soient analysées et affichées. 
   
   * Si l’application a été configurée pour Application Insights pendant le développement, sélectionnez sa ressource existante.
   * Sinon, créez une ressource nommée d’après l’application. S’il existe d’autres applications qui sont des composants du même système, placez-les dans le même groupe de ressources, pour faciliter l’accès aux données de télémétrie à gérer.
     
     Vous pouvez modifier ces paramètres plus tard.
     
     ![Dans l’onglet Application Insights settings (Paramètres d’Application Insights), cliquez sur « se connecter » et indiquez vos informations d’identification de compte Microsoft pour Azure. Choisissez ensuite un abonnement, un groupe de ressources et une ressource.](./media/app-insights-scom/060.png)
3. Terminez l’Assistant.
   
    ![Click Create](./media/app-insights-scom/070.png)

Répétez cette procédure pour chaque application que vous souhaitez analyser.

Si vous devez modifier les paramètres plus tard, ouvrez à nouveau les propriétés d’analyse à partir de la fenêtre de création.

![Dans la fenêtre de création, sélectionnez .NET Application Performance Monitoring with Application Insights (Analyse des performances des applications .NET avec Application Insights), choisissez votre analyse et cliquez sur Propriétés.](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>Vérifier l’analyse
L’analyse que vous avez mise en place recherche votre application sur chaque serveur. Aux endroits où elle trouve l’application, elle configure Application Insights Status Monitor pour analyser l’application. Si nécessaire, elle installe d’abord Status Monitor sur le serveur.

Vous pouvez vérifier quelles sont les instances de l’application trouvées :

![Dans la fenêtre d’analyse, ouvrez Application Insights.](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Afficher les données de télémétrie dans Application Insights
Dans le [portail Azure](https://portal.azure.com), accédez à la ressource de votre application. Vous [pouvez voir les graphiques affichant les données de télémétrie](app-insights-dashboards.md) depuis votre application. (S’ils ne sont pas encore affichés sur la page principale, cliquez sur Live Metrics Stream (Diffusion des mesures en direct).)

## <a name="next-steps"></a>Étapes suivantes
* [Configurez un tableau de bord](app-insights-dashboards.md) pour rassembler les graphiques d’analyse les plus importants de cette application et d’autres applications.
* [En savoir plus sur les métriques](app-insights-metrics-explorer.md)
* [Configurer des alertes](app-insights-alerts.md)
* [Diagnostiquer des problèmes de performances](app-insights-detect-triage-diagnose.md)
* [Requêtes Analytics efficaces](app-insights-analytics.md)
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md)


