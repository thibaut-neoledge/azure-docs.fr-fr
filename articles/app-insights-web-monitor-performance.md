<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Analyse des performances dans les applications Web

*Application Insights est à l'état de version préliminaire.*

Assurez-vous que votre application fonctionne correctement et identifiez rapidement toutes les défaillances éventuelles. [Application Insights][] vous signale tous les problèmes et exceptions de performances, et vous aide à diagnostiquer les causes principales.

Application Insights est capable d'analyser des applications Web ASP.NET hébergées sur site ou sur des machines virtuelles, ainsi que des sites Web Microsoft Azure.

-   [Configuration de l'analyse des performances][]
-   [Affichage des données][]
-   [Signification][]
-   [Problèmes de diagnostic][]
-   [Étapes suivantes][]

## <a name="setup"></a>Configuration de l'analyse des performances

Si vous n'avez pas encore ajouté Application Insights à votre projet (il n'inclut pas ApplicationInsights.config), sélectionnez l'une des options suivantes pour commencer :

-   [Ajout de Application Insights à votre projet dans Visual Studio][Application Insights] - Recommandé. Vous pouvez effectuer une analyse passive des performances, insérer des journalisations de diagnostic et procéder au suivi de l'utilisation.
-   [Analyse des performances d'un site Web en direct dès maintenant][] - De cette façon, vous n'avez plus besoin de mettre à jour le projet de l'application ni de redéployer le site Web.

## <a name="view"></a>Affichage des rapports

Exécutez votre application à l'aide de la touche F5 et essayez-la - ouvrez différentes pages.

Un décompte des événements qui ont été reçus s'affiche dans Visual Studio.

![][]

Ouvrez Application Insights à partir de votre projet.

![Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure][]

Recherchez des données dans les vignettes **Intégrité des applications**. Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données][]

Lorsque vous exécutez l'application en mode débogage, la télémétrie est envoyée via le pipeline, ce qui vous permet d'accéder aux données en quelques secondes. Lorsque vous déployez votre application, les données s'accumulent plus lentement.

Si aucune donnée ne s'affiche au début, attendez une minute, puis cliquez sur Actualiser.

### Exploration des métriques

Cliquez sur n'importe quelle vignette pour plus de détails et afficher les résultats plus longtemps. Par exemple, cliquez sur la vignette Demandes et sélectionnez une plage de temps :

![Cliquez sur d'autres éléments pour afficher plus de données et sélectionnez une plage de temps][]

Cliquez sur un graphique pour sélectionner les métriques qu'il affiche :

![Cliquez sur un graphique pour sélectionner les métriques][]

> [AZURE.NOTE] **Décochez toutes les métriques** pour afficher toutes les options disponibles. Les métriques se répartissent en trois groupes ; lorsqu'un membre d'un groupe est sélectionné, seuls les autres membres de ce groupe s'affichent.

## <a name="metrics"></a>Signification Vignettes de performances et rapports

Vous pouvez accéder à de multiples métriques de performances. Commençons par celles qui s'affichent par défaut dans le volet de l'application.

### Demandes

Le nombre de demandes HTTP reçues pendant une période spécifiée. Comparez cette valeur avec les résultats des autres rapports pour savoir comment votre application se comporte lorsque la charge à laquelle elle est soumise varie.

Les demandes HTTP incluent toutes les demandes GET ou POST pour les pages, données et images.

Cliquez sur la vignette pour obtenir les décomptes associés aux URL spécifiques.

### Temps de réponse moyen

Mesure le temps entre le moment où une demande Web est reçue par votre application et le moment où la réponse est renvoyée.

Les points indiquent une moyenne qui évolue. En présence de nombreuses demandes, certaines d'entre elles peuvent s'écarter de la moyenne sans indiquer de pic ou de creux dans le graphique.

Recherchez les pics inhabituels. En règle générale, il est normal que le temps de réponse augmente lorsque le nombre de demandes augmente. Si l'augmentation est disproportionnée, il se peut que votre application ait atteint une limite de ressources telles que l'UC ou la capacité d'un service qu'elle utilise.

Cliquez sur la vignette pour obtenir les temps associés aux URL spécifiques.

![][1]

### Demandes les plus lentes

![][2]

Indique les demandes dont les performances doivent probablement être améliorées.

### Demandes ayant échoué

![][3]

Décompte des demandes qui ont généré des exceptions non interceptées.

Cliquez sur la vignette pour afficher le détail des échecs spécifiques, puis sélectionnez une demande individuelle pour afficher ses informations détaillées.

Seul un échantillon représentatif des échecs est prélevé pour chaque inspection individuelle.

### Autres métriques

Pour savoir quelles autres métriques vous pouvez afficher, cliquez sur un graphique, puis désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles. Cliquez sur (i) pour afficher la définition de chaque métrique.

![Désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles][]

La sélection de n'importe quelle métrique masquera les autres métriques qui peuvent s'afficher sur le même graphique.

## <a name="diagnosis"></a>Problèmes de diagnostic

Voici quelques conseils pour identifier et diagnostiquer les problèmes de performances :

-   Configurez les [tests Web][] pour être alerté en cas de défaillance ou de réponse incorrecte/lente de votre site Web.
-   Comparez le nombre de demandes avec les autres métriques afin de savoir si ces défaillances ou réponses lentes sont liées à la charge du site Web.
-   [Insérez et recherchez des déclarations de trace][] dans votre code afin de vous aider à identifier les problèmes.

## <a name="next"></a>Étapes suivantes

[Tests Web][tests Web] - Faites envoyer des tests Web à votre application à intervalles réguliers et depuis n'importe où dans le monde.

[Capture et recherche de traces de diagnostic][Insérez et recherchez des déclarations de trace] - Insérez des appels de trace et passez au crible les résultats afin d'identifier les problèmes.

[Suivi de l'utilisation][] - Découvrez ce que les utilisateurs font avec votre application.

[Résolution des problèmes][] - et questions et réponses

## En savoir plus

-   [Application Insights - prise en main][Application Insights]
-   [Analyse d'un serveur Web en direct dès maintenant][Analyse des performances d'un site Web en direct dès maintenant]
-   [Analyse des performances dans les applications Web][]
-   [Recherche des journaux de diagnostic][Insérez et recherchez des déclarations de trace]
-   [Suivi de la disponibilité avec les tests Web][tests Web]
-   [Suivi de l'utilisation][]
-   [Questions et réponses, et résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [Application Insights]: ../app-insights-start-monitoring-app-health-usage/
  [Configuration de l'analyse des performances]: #setup
  [Affichage des données]: #view
  [Signification]: #metrics
  [Problèmes de diagnostic]: #diagnosis
  [Étapes suivantes]: #next
  [Analyse des performances d'un site Web en direct dès maintenant]: ../app-insights-monitor-performance-live-website-now/
  []: ./media/appinsights/appinsights-09eventcount.png
  [Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Cliquez sur d'autres éléments pour afficher plus de données]: ./media/appinsights/appinsights-41firstHealth.png
  [Cliquez sur d'autres éléments pour afficher plus de données et sélectionnez une plage de temps]: ./media/appinsights/appinsights-48metrics.png
  [Cliquez sur un graphique pour sélectionner les métriques]: ./media/appinsights/appinsights-61perfchoices.png
  [1]: ./media/appinsights/appinsights-42reqs.png
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-46failed.png
  [Désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles]: ./media/appinsights/appinsights-62allchoices.png
  [tests Web]: ../app-insights-monitor-web-app-availability/
  [Insérez et recherchez des déclarations de trace]: ../app-insights-search-diagnostic-logs/
  [Suivi de l'utilisation]: ../app-insights-web-track-usage/
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Analyse des performances dans les applications Web]: ../app-insights-web-monitor-performance/
