<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights - Commencer à analyser l'état d'intégrité et l'utilisation de votre application

*Application Insights est à l'état de version préliminaire.*

Application Insights vous permet d'analyser les éléments suivants de votre application en direct :

-   **Disponibilité** - Nous testons vos URL à des intervalles de quelques minutes dans le monde entier.
-   **Performances** - Détectez et diagnostiquez les problèmes et exceptions de performances.
-   **Utilisation** - Découvrez ce que les utilisateurs font avec votre application afin de l'améliorer et mieux les satisfaire.

La configuration est très facile, et les résultats sont visibles après quelques minutes. Nous prenons actuellement en charge les applications Web ASP.NET (sur vos propres serveurs ou sur Azure).

Vous aurez besoin d'un compte dans [Microsoft Azure][] (une période d'évaluation gratuite est intégrée).

La prise en main de Application Insights peut se faire de deux façons :

-   (Recommandé) [Ajout de Application Insights à votre projet dans Visual Studio][] afin d'analyser les performances et l'utilisation de l'application.
-   [Installation d'un agent sur votre serveur sans redéploiement][] - Analysez un site Web en direct sans le redéployer ni modifier votre code source. Cela vous permet d'analyser à la fois les performances et les exceptions. Vous pouvez ajouter l'analyse de l'utilisation ultérieurement.

> [WACOM.NOTE] Il existe une [ancienne version de Application Insights][] dans Visual Studio Online, qui prend en charge un plus grand nombre de types d'applications. Nous sommes en train de la redévelopper pour Microsoft Azure, et c'est cette nouvelle version dont nous parlons actuellement.

## <a name="add"></a>Ajout de Application Insights à votre projet

Vous avez besoin de [Visual Studio 2013 Update 3][] (ou une version ultérieure).

### S'il s'agit d'un nouveau projet...

Lorsque vous créez un projet dans Visual Studio 2013, assurez-vous que Application Insights est sélectionné.

![Création d'un projet ASP.NET][]

S'il s'agit de votre toute première utilisation, vous êtes invité à vous connecter ou à vous inscrire à la version préliminaire de Microsoft Azure. (indépendamment de votre compte Visual Studio Online).

Utilisez **Configurer les paramètres** si vous souhaitez que la ressource Azure porte un nom différent de celui de votre projet, ou si vous souhaitez que la ressource apparaisse sous le même groupe, en tant que projet distinct.

*Aucune option Application Insights n'est disponible ? Vérifiez que vous utilisez Visual Studio 2013 Update 3, que les outils Application Insights sont activés dans Extensions et mises à jour, et que vous êtes en train de créer un projet Web, Windows Store ou Windows Phone.*

### ... ou s'il s'agit d'un projet existant

Cliquez avec le bouton droit de la souris sur le projet dans l'Explorateur de solutions, puis sélectionnez Ajouter Application Insights.

![Sélection de Ajouter Application Insights][]

*Il existe une étape supplémentaire si vous souhaitez configurer l'analyse de l'utilisation Web, mais examinons d'abord quelques données...*

### <a name="run"></a>2. Exécution de votre projet

Exécutez votre application à l'aide de la touche F5 et essayez-la - ouvrez différentes pages.

Un décompte des événements qui ont été reçus s'affiche dans Visual Studio.

![][]

### <a name="monitor"></a>3. Affichage des données d'analyse

Ouvrez Application Insights à partir de votre projet.

![Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure][]

Recherchez des données dans les vignettes **Intégrité des applications**. Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données][]

Cliquez sur n'importe quelle vignette pour plus de détails. Vous pouvez modifier ce qui s'affiche dans les rapports. [Familiarisez-vous avec la configuration des rapports Intégrité des applications.][]

### <a name="webclient"></a>4. Configuration de l'analyse de l'utilisation Web

Si vous avez ajouté Application Insights à un projet Web *existant*, rien ne s'affiche dans les vignettes d'analyse de l'utilisation. Vous devez effectuer une étape supplémentaire.

Dans Application Insights, sélectionnez Quick start, Instrument your website.

![Dans votre projet dans Application Insights, cliquez sur Quick start, Instrument your website, et copiez le code][]

Copiez le code JavaScript dans les pages Web que vous souhaitez analyser, juste avant la balise de fermeture \</head\>. Dans un projet ASP.NET, un moyen efficace d'analyser toutes les pages consiste à placer le code dans la page maître, habituellement appelée `_SiteLayout` ou `Views\Shared\_Layout`. Notez que le code contient la clé Application Insights de votre application.

Exécutez à nouveau votre application ; les données apparaissent dans **Analyse de l'utilisation**.

![Cliquez sur d'autres éléments pour afficher plus de données][1]

[Cliquez sur les divers graphique pour plus de détails.][Familiarisez-vous avec la configuration des rapports Intégrité des applications.]

### <a name="deploy"></a>5. Déploiement de votre application

Déployez votre application et regardez les données s'accumuler.

Lorsque votre application est active, [configurez les test Web][] pour vous assurer qu'elle demeure active.

![Exemple d'analyse d'application dans Application Insights][]

### Vous souhaitez modifier le nom de votre application dans le portail ?

Vous pouvez modifier la ressource à laquelle votre projet envoie la télémétrie.

(La « ressource » est le volet de Application Insights où vos résultats s'affichent. Vous pouvez placer plusieurs ressources dans un groupe - par exemple, si vous avez plusieurs projets qui font partie d'une application commerciale.)

Dans l'Explorateur de solutions, cliquez avec le bouton droit de la souris sur ApplicationInsights.config, puis sélectionnez **Mettre à jour Application Insights**. L'assistant qui s'affiche vous permet de sélectionner une ressource existante différente ou d'en créer une.

Ensuite, revenez au portail et supprimez l'ancienne ressource.

## <a name="video"></a>Vidéos

### Introduction

> [AZURE.VIDEO application-insights-introduction]

### Prise en main

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Étapes suivantes

[Suivi de l'utilisation de votre application Web][]

[Analyse des performances dans les applications Web][Familiarisez-vous avec la configuration des rapports Intégrité des applications.]

[Capture et recherche des journaux de diagnostic][]

[Résolution des problèmes][]

## En savoir plus

-   [Application Insights - prise en main][]
-   [Analyse d'un serveur Web en direct dès maintenant][Installation d'un agent sur votre serveur sans redéploiement]
-   [Analyse des performances dans les applications Web][Familiarisez-vous avec la configuration des rapports Intégrité des applications.]
-   [Recherche des journaux de diagnostic][Capture et recherche des journaux de diagnostic]
-   [Suivi de la disponibilité avec les tests Web][configurez les test Web]
-   [Suivi de l'utilisation][Suivi de l'utilisation de votre application Web]
-   [Questions et réponses, et résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [Microsoft Azure]: http://azure.com
  [Ajout de Application Insights à votre projet dans Visual Studio]: #add
  [Installation d'un agent sur votre serveur sans redéploiement]: ../app-insights-monitor-performance-live-website-now/
  [ancienne version de Application Insights]: http://msdn.microsoft.com/en-us/library/dn481095.aspx
  [Visual Studio 2013 Update 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Création d'un projet ASP.NET]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Sélection de Ajouter Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Cliquez sur d'autres éléments pour afficher plus de données]: ./media/appinsights/appinsights-41firstHealth.png
  [Familiarisez-vous avec la configuration des rapports Intégrité des applications.]: ../app-insights-web-monitor-performance/
  [Dans votre projet dans Application Insights, cliquez sur Quick start, Instrument your website, et copiez le code]: ./media/appinsights/appinsights-06webcode.png
  [1]: ./media/appinsights/appinsights-05-usageTiles.png
  [configurez les test Web]: ../app-insights-monitor-web-app-availability/
  [Exemple d'analyse d'application dans Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Suivi de l'utilisation de votre application Web]: ../app-insights-web-track-usage/
  [Capture et recherche des journaux de diagnostic]: ../app-insights-search-diagnostic-logs/
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Application Insights - prise en main]: ../app-insights-start-monitoring-app-health-usage/
