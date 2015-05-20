<properties 
	pageTitle="Application Insights pour Windows Phone et les applications du Windows Store" 
	description="Analysez l'utilisation et les performances de votre application pour appareil Windows avec Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>

# Application Insights pour Windows Phone et les applications du Windows Store

*Application Insights est à l'état de version préliminaire.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights vous permet d'analyser les points suivants de vos applications publiées :

* [**Utilisation**][windowsUsage] : découvrez le nombre de vos utilisateurs et ce qu'ils font avec votre application.
* [**Incidents**][windowsCrash] : récupérez des rapports de diagnostics des incidents et comprenez leur impact sur les utilisateurs.

![](./media/appinsights/appinsights-d018-oview.png)

Pour de nombreux types d'application, [Visual Studio peut ajouter Application Insights à votre application](#ide) sans que vous remarquiez quoi que ce soit. Mais puisque vous lisez ceci pour mieux comprendre ce que cet ajout entraîne, nous vous proposons de suivre cette procédure, étape par étape.

Vous devez avoir :

* Un abonnement à [Microsoft Azure][azure].
* Visual Studio 2013 ou une version ultérieure.

## 1\. Création d’une ressource Application Insights dans Azure 

Dans le [portail Azure][portal], créez une ressource Application Insights.

![Cliquez sur Nouveau, Services de développement, Application Insights](./media/app-insights-windows-get-started/01-new.png)

Dans Azure, une [ressource][roles] correspond à l’instance d'un service. Cette ressource correspond à l’emplacement où les données de télémétrie de votre application sont analysées avant de vous être présentées.

#### Copie de la clé d'instrumentation

Cette clé identifie la ressource. Vous en aurez besoin rapidement, pour configurer le Kit de développement logiciel \(SDK\) pour envoyer les données à la ressource.

![Ouvrez le tiroir de liste déroulante Essentials et sélectionnez la clé d'instrumentation](./media/app-insights-windows-get-started/02-props.png)


## 2\. Ajout du Kit de développement logiciel \(SDK\) Application Insights à votre application

Dans Visual Studio, ajoutez le Kit de développement logiciel \(SDK\) approprié à votre projet.

S’il s’agit d’une application Windows Universal, répétez les étapes pour le projet Windows Phone et le projet Windows.

1. Cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions, puis sélectionnez **Gérer les packages NuGet**.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. Sélectionnez **En ligne**, **Inclure la version préliminaire**, puis recherchez « Application Insights ».

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. Sélectionnez la dernière version du package correspondant parmi :
   * Application Insights pour les applications Windows : *pour les applications Windows Store*
   * Application Insights pour les applications Windows Phone
   * Application Insights pour les applications Web : *à utiliser pour une application de bureau* 
4. Modifiez ApplicationInsights.config \(qui a été ajouté par l'installation NuGet\). Insérez ceci juste avant la balise de fermeture :

    `<InstrumentationKey>`\*la clé que vous avez copiée\*`</InstrumentationKey>`

**Applications Windows Universal** : répétez ces étapes pour les projets Windows Phone et Windows Store.

## <a name="network"></a>3. Activation de l'accès réseau pour votre application

Si votre application n’a pas déjà [demandé un accès réseau sortant](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), vous devez ajouter ceci à son manifeste en tant que [capacité requise](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Exécution de votre projet

[Exécutez votre application en appuyant sur F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) et utilisez-la pour générer des données de télémétrie.

Un décompte des événements qui ont été reçus s'affiche dans Visual Studio.

![](./media/appinsights/appinsights-09eventcount.png)

En mode débogage, les données de télémétrie sont envoyées dès qu'elles sont générées. En mode version finale, les données de télémétrie sont stockées sur le périphérique et envoyées uniquement lors de la reprise de l'application.

## <a name="monitor"></a>5. Affichage des données surveillées

Ouvrez Application Insights à partir de votre projet.

![Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure](./media/appinsights/appinsights-04-openPortal.png)


Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données](./media/appinsights/appinsights-26-devices-01.png)

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

Cliquez sur n'importe quel graphique pour afficher plus de détails.


## <a name="deploy"></a>5. Publication de votre application dans Windows Store

[Publiez votre application](http://dev.windows.com/publish) et observez les données s'accumuler lorsque les utilisateurs la téléchargent et l'utilisent.

## <a name="ide"></a>Méthode automatisée

Si vous préférez utiliser Visual Studio pour effectuer les étapes de configuration, vous pouvez le faire avec Windows Phone, Windows Store et de nombreux autres types d'applications.

###<a name="new"></a> Si vous créez un nouveau projet d'application Windows...

Dans la boîte de dialogue Nouveau projet, sélectionnez Application Insights.

Si vous êtes invité à le faire, utilisez les informations d'identification de votre compte Azure \(qui est distinct de votre compte Visual Studio Online\).

![](./media/appinsights/appinsights-d21-new.png)


###<a name="existing"></a> Ou s’il s’agit d’un projet existant...

Ajoutez Application Insights à partir de l'Explorateur de solutions.


![](./media/appinsights/appinsights-d22-add.png)


## <a name="usage"></a>Étapes suivantes

[Suivi de l'utilisation de votre application][windowsUsage]

[Détection et diagnostic des incidents touchant votre application][windowsCrash]

[Capture et recherche des journaux de diagnostic][diagnostic]

[Utiliser l'API pour envoyer des données de télémétrie personnalisées][api]

[Résolution des problèmes][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md


<!--HONumber=54-->