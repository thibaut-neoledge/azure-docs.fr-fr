<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Surveillance de la disponibilité et de la réactivité de votre application web

Après avoir déployé votre application web, vous pouvez configurer des tests web pour surveiller sa disponibilité et sa réactivité. Application Insights envoie des demandes web à intervalles réguliers de différents points dans le monde et vous alerte si votre application réagit lentement ou pas du tout.

Vous pouvez configurer des tests web pour n'importe quel point de terminaison HTTP accessible à partir du réseau Internet public.

1.  [Création d'une application][Création d'une application]
-   [Configuration d'un test web][Configuration d'un test web]
-   [Affichage des rapports de surveillance][Affichage des rapports de surveillance]
-   [Modification ou désactivation d'un test][Modification ou désactivation d'un test]
-   [Étapes suivantes][Étapes suivantes]

## <a name="create"></a>1. Création d'une application

Ignorez cette étape si vous [surveillez déjà l'utilisation et l'intégrité][surveillez déjà l'utilisation et l'intégrité] de cette application web et si vous voulez afficher les données de disponibilité au même endroit.

Mais si vous voulez conserver ces résultats séparément, connectez-vous à la version préliminaire de Microsoft Azure et créez une application dans Application Insights.

![New \> Application Insights][New \> Application Insights]

## <a name="setup"></a>2. Configuration d'un test web

Dans le volet d'accueil de votre application dans Application Insights, cliquez sur la vignette vide des tests web.

![Click the empty availability test][Click the empty availability test]

> *Vous avez déjà des tests web ? Cliquez sur la vignette des tests web et sélectionnez Ajouter un test web.*

Fournissez un nom pour le test et l'URL que vous voulez tester. Celle-ci doit être visible sur le réseau Internet public.

![Fill at least the URL of your website][Fill at least the URL of your website]

-   Les emplacements de test sont les lieux où nos serveurs envoient des demandes web à votre URL. Choisissez-en deux ou trois de façon à distinguer les problèmes de votre site web des problèmes de réseau. Vous ne pouvez pas sélectionner plus de trois emplacements.

-   Critères de réussite - Indiquez les codes de retour HTTP acceptables. 200 est une valeur courante.

    Vous pouvez également spécifier une chaîne qui doit se trouver dans chaque réponse correcte. Ce doit une chaîne standard sans caractère générique. N'oubliez pas que si votre contenu change, vous devrez peut-être l'actualiser.

-   Alertes - Par défaut, des alertes vous sont envoyées en cas d'erreurs répétées pendant plus de 15 minutes. Cependant, vous pouvez la modifier de manière à ce qu'elle soit plus sensible ; vous pouvez également modifier les adresses de messagerie notifiées.

### Test d'autres URL

Vous pouvez ajouter d'autres tests pour autant d'URL que vous le souhaitez. Exemple : outre le test de votre page d'accueil, vous pouvez vérifier que votre base de données fonctionne correctement en testant une recherche sur l'URL.

![On the web tests blade, choose Add][On the web tests blade, choose Add]

## <a name="monitor"></a>3. Affichage des rapports de surveillance

Après 1 à 2 minutes, fermez et rouvrez le volet des applications. (Il n'est pas automatiquement actualisé dans cette version).

![Summary results on the home blade][Summary results on the home blade]

Il s'agit d'un résumé. Si vous avez défini plusieurs tests web pour cette application, ils sont tous représentés ici.

Cliquez sur le volet des tests web pour en afficher la liste.

Ouvrez un test web.

![Click a specific webtest][Click a specific webtest]

Dans le volet du test web, faites défiler jusqu'à **Échec des tests** et sélectionnez un résultat.

![Click a specific webtest][1]

Le résultat indique la raison de l'échec.

![Webtest run result][Webtest run result]

Pour plus d'informations, téléchargez le fichier de résultats et examinez-le dans Visual Studio.

## <a name="edit"></a>4. Modification ou désactivation d'un test

Ouvrez un test à modifier ou à désactiver.

![Edit or disable a web test][Edit or disable a web test]

Vous pouvez par exemple désactiver des tests web lorsque vous effectuez des opérations de maintenance sur votre service.

## <a name="next"></a>Étapes suivantes

[Recherche dans les journaux de diagnostics][Recherche dans les journaux de diagnostics]

[Résolution des problèmes][Résolution des problèmes]

## <a name="next"></a>En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][surveillez déjà l'utilisation et l'intégrité]
-   [Surveillance d'un serveur web en activité][Surveillance d'un serveur web en activité]
-   [Exploration des mesures dans Application Insights][Exploration des mesures dans Application Insights]
-   [Recherche dans un journal de diagnostics][Recherche dans les journaux de diagnostics]
-   [Suivi de la disponibilité avec des tests web][Suivi de la disponibilité avec des tests web]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [Création d'une application]: #create
  [Configuration d'un test web]: #setup
  [Affichage des rapports de surveillance]: #monitor
  [Modification ou désactivation d'un test]: #edit
  [Étapes suivantes]: #next
  [surveillez déjà l'utilisation et l'intégrité]: ../app-insights-monitor-application-health-usage/
  [New \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Click the empty availability test]: ./media/appinsights/appinsights-12avail.png
  [Fill at least the URL of your website]: ./media/appinsights/appinsights-13availChoices.png
  [On the web tests blade, choose Add]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Summary results on the home blade]: ./media/appinsights/appinsights-14availSummary.png
  [Click a specific webtest]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Webtest run result]: ./media/appinsights/appinsights-18-availDetails.png
  [Edit or disable a web test]: ./media/appinsights/appinsights-19-availEdit.png
  [Recherche dans les journaux de diagnostics]: ../app-insights-search-diagnostic-logs/
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Surveillance d'un serveur web en activité]: ../app-insights-monitor-performance-live-website-now/
  [Exploration des mesures dans Application Insights]: ../app-insights-explore-metrics/
  [Suivi de la disponibilité avec des tests web]: ../app-insights-monitor-web-app-availability/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-track-usage-custom-events-metrics/
