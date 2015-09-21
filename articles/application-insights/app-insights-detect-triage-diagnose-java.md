<properties
	pageTitle="Applications web J2EE de détection, de tri et de diagnostic"
	description="Analyser les pannes et détecter et diagnostiquer les problèmes de performances dans vos applications web Java"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/09/2015"
	ms.author="awills"/>

# Applications J2EE de détection, de tri et de diagnostic avec Application Insights

*Application Insights est à l'état de version préliminaire.*


Une fois que vous avez publié votre application, Application Insights vous permet de vous assurer qu'elle fonctionne correctement. S'il y a un problème, vous souhaitez le savoir rapidement et savoir ce qu'il faut faire.

* *« Il y a quelques jours, nous avons déployé un correctif secondaire. Nous n'avons pas effectué de tests complets, mais malheureusement certaines modifications inattendues ont été ajoutées à la charge utile, ce qui a créé une incompatibilité entre le début et la fin. Immédiatement, des exceptions sont apparues sur le serveur, nos alertes se sont déclenchées et nous avons été mis au fait de la situation. En quelques clics sur le portail Application Insights, nous avons obtenu suffisamment d'informations dans la pile des appels d'exception pour cerner le problème. Nous avons immédiatement restauré la version précédente et limité les dégâts. Application Insights facilite et simplifie cette partie du cycle des opérations de développement. »*

On peut considérer cette partie du cycle des opérations de développement comme un pipeline :

![Détection-tri-diagnostic](./media/app-insights-detect-triage-diagnose-java/01-pipe1.png)


Une fois que vous avez diagnostiqué le problème, vous savez où vous devez concentrer vos efforts : effectuer un débogage de votre code, allouer davantage de mémoire ou vérifier une dépendance. Enfin, vous pouvez vérifier si votre correction a fonctionné :



![Réparer et valider](./media/app-insights-detect-triage-diagnose-java/02-pipe2.png)


Voyons comment Application Insights fonctionne à chaque étape de ce pipeline.

Application Insights fonctionne pour les applications mobiles et les applications web. Dans cette procédure pas à pas, nous allons parler des applications web. Nous allons suivre l'équipe OBS de la banque Fabrikam, chargée du système des opérations bancaires en ligne. Ils ont ajouté [Application Insights à leurs projets web](app-insights-java-get-started.md).


![Exemple de site web d'une banque](./media/app-insights-detect-triage-diagnose-java/03-bank.png)



## Détecter une faible disponibilité


Marcela Markova est la spécialiste de test de l'équipe OBS et elle est responsable de la surveillance des performances en ligne. Elle crée plusieurs [tests web][availability] :

* Un test d'URL unique pour la page d'accueil principale de l'application, http://fabrikambank.com/onlinebanking/. Elle définit des critères de code HTTP 200 et le texte « Bienvenue ! ». Si ce test échoue, il y a un sérieux problème de réseau ou un problème sur les serveurs, voire un problème de déploiement. (Ou bien quelqu'un a modifié sans l'informer le message de bienvenue sur la page d'accueil.)


* Un test en plusieurs étapes plus en profondeur, qui se connecte et obtient la liste des comptes actuels, en vérifiant quelques détails importants sur chaque page. Ce test vérifie que le lien vers la base de données des comptes fonctionne. Elle utilise un ID de client fictif : elle en conserve quelques-uns pour ses tests.


Une fois ces tests configurés, Marcela sait que l'équipe sera rapidement avertie en cas d'interruption.


Les défaillances sont indiquées par des points rouge dans le graphique de test web :

![Affichage des tests web exécutés sur la période précédente](./media/app-insights-detect-triage-diagnose-java/04-webtests.png)


Mais surtout, une alerte est envoyée à l'équipe de développement pour toute erreur. De cette façon, ils en sont informés presque avant tous les clients.


## Analyser les mesures de performances


Dans la page Vue d’ensemble dans Application Insights, un graphique montre une série de [métriques essentielles][perf].

![Différentes mesures](./media/app-insights-detect-triage-diagnose-java/05-perfMetrics.png)

Le temps de chargement de la page du navigateur provient de la télémétrie envoyée directement depuis vos pages web. Le temps de réponse du serveur, le nombre de demandes au serveur et le nombre de demandes ayant échoué sont mesurés dans le serveur web puis envoyés à Application Insights à partir de là.




Le nombre de demandes ayant échoué indique les cas dans lesquels les utilisateurs ont vu une erreur, suite en général à une exception dans le code. Peut-être ont-ils vu un message indiquant « Nous ne pouvons pas mettre à jour vos informations maintenant » ou, dans le pire des cas, le vidage de la pile sur l'écran de l'utilisateur, via le serveur web.


Marcela aime consulter ces graphiques de temps à autre. L'absence de demandes ayant échoué est positive, même si lorsqu’elle modifie l’intervalle du graphique pour couvrir la semaine précédente, des défaillances occasionnelles apparaissent. Il s'agit d'un niveau acceptable pour un serveur très sollicité. Mais si un pic est indiqué pour les défaillances ou pour d’autres mesures comme le temps de réponse du serveur, Marcela a besoin de le savoir immédiatement. Cela peut indiquer un problème imprévu, causé par une certaine version du code ou un échec dans une dépendance, comme une base de données, ou peut-être encore une réaction anormale à un nombre élevé de demandes.


Marcela ne reste pas les bras croisés à attendre les alertes. Après chaque redéploiement, elle examine les [temps de réponse][perf], à savoir les chiffres globaux aussi bien que la table des demandes les plus lentes, ainsi que le nombre d'exceptions.



![Graphique des temps de réponse et grille des temps de réponse du serveur.](./media/app-insights-detect-triage-diagnose-java/09-dependencies.png)

Elle peut évaluer l'effet de chaque déploiement sur les performances, en comparant chaque semaine avec la précédente. Si l'état s'aggrave soudainement, elle le signale aux développeurs responsables du composant affecté.

Il existe également des graphiques de dépendances, des compteurs de performance, des exceptions, des données d’utilisation et de nombreux autres événements et métriques.

#### Alertes

Elle définit deux [alertes][metrics] : une pour les temps de réponse supérieurs à un certain seuil et l'autre pour un nombre de demandes ayant échoué supérieur au volume actuel.


Combinées aux alertes de disponibilité, elle sait que ces alertes l’avertiront dès qu’un événement inhabituel se produira.


Il est également possible de définir des alertes sur un grand nombre d'autres mesures. Par exemple, vous pouvez recevoir des courriers électroniques si le nombre d'exceptions est trop élevé ou si la mémoire disponible est faible, ou bien s'il existe un pic dans les demandes des clients.



![Ajouter un panneau d'alerte](./media/app-insights-detect-triage-diagnose-java/07-alerts.png)



## Détection des exceptions


Les exceptions non interceptées sont signalées automatiquement à Application Insights.


En outre, vous pouvez envoyer des rapports d’exceptions que vous interceptez en insérant des appels à [trackException()](app-insights-api-custom-events-metrics.md#track-exception) dans le gestionnaire :

``` Java

   TelemetryClient telemetry = new TelemetryClient();
   try {
    ...
   } catch (Exception ex) {
    // Set up some properties:
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("Game", currentGame.getName());

    Map<String, Double> measurements = new HashMap<String, Double>();
    measurements.put("Users", currentGame.getUsers().getCount());

    // Send the exception telemetry:
    telemetry.trackException(ex, properties, measurements);
   }

```



L'équipe de la banque Fabrikam a pour règle de toujours envoyer la télémétrie en cas d'exception, sauf s'il existe une solution évidente.

En règle générale, elle envoie toutes les propriétés qui peuvent être utiles au diagnostic.

Les exceptions et les événements apparaissent dans le panneau [Recherche de diagnostic][diagnostic]. Vous pouvez afficher plus de détails dans les propriétés supplémentaires et dans le suivi de la pile.

![Dans Recherche de diagnostic, utilisez les filtres pour afficher certains types de données.](./media/app-insights-detect-triage-diagnose-java/appinsights-333facets.png)




## Tri


Tri : évaluation de la gravité et de l'étendue d'un problème. Première étape après la détection. Est-il nécessaire d'appeler l'équipe à minuit ? Ou est-il possible de laisser le problème de côté en attendant que l'équipe ait un peu de temps pour s'y attaquer ? Le tri pose certaines questions importantes.


Le problème se pose-t-il souvent ? Les graphiques du panneau Vue d'ensemble mettent le problème en perspective. Par exemple, l'application Fabrikam a généré en une nuit quatre alertes de test web. En examinant le graphique le matin, l'équipe a constaté qu'il y avait effectivement des points rouges, bien que la plupart des tests soient encore en vert. En ouvrant les détails du graphique de disponibilité, il était clair que tous ces problèmes intermittents venaient d'un emplacement de test. Il s'agissait d'un problème réseau affectant un seul itinéraire, qui disparaîtra probablement de lui-même.


En revanche, une augmentation importante et constante dans le graphique du nombre d'exceptions ou des temps de réponse est évidemment source de panique.


Une bonne tactique de tri est le test réel. Si vous rencontrez le même problème, vous savez qu'il est bel et bien réel.


Quelle est la part des utilisateurs affectés ? Pour obtenir une réponse approximative, divisez le taux d'échec par le nombre de sessions.


![Graphique des demandes et des sessions ayant échoué](./media/app-insights-detect-triage-diagnose-java/10-failureRate.png)

En cas de réponse lente, comparez la table des demandes présentant la réponse la moins rapide avec la fréquence d'utilisation de chaque page.


Quelle est l'importance du scénario bloqué ? S'agit-il d'un problème fonctionnel qui bloque un parcours utilisateur particulier et est-il important ? Si les clients ne peuvent pas payer leurs factures, c'est grave. S'ils ne peuvent simplement pas changer les préférences de couleur d'écran, peut-être que le problème peut attendre. Les détails de l'événement ou de l'exception, ou l'identité de la page qui présente un temps de chargement trop long, vous indiquent où les clients rencontrent des difficultés.


## Diagnostic


Le diagnostic n'est pas tout à fait la même chose que le débogage. Avant de commencer le suivi via le code, vous devez avoir une idée du pourquoi, du quand et du où le problème se produit.


**Quand cela se produit-il ?** La vue historique fournie par les graphiques des événements et des mesures facilite la mise en corrélation des effets avec les causes possibles. S'il y a des pics intermittents dans les temps de réponse ou les taux d'exceptions, examinez le nombre de demandes : si elle augmente en même temps, il peut s'agir d'un problème de ressources. Est-il nécessaire d'allouer davantage de processeur ou de mémoire ? Ou s'agit-il d'une dépendance qui ne peut pas gérer la charge ?


**Le problème vient-il de nous ?** Si vous constatez une chute soudaine des performances d'un type de demande particulier, par exemple lorsque le client souhaite obtenir un relevé de compte, il est possible que le problème vienne d'un sous-système externe plutôt que de votre application web. Dans Metrics Explorer, sélectionnez les taux d'échec de dépendance et les taux de durée de la dépendance et consultez leur historique sur quelques heures ou jours avec en tête le problème que vous avez détecté. S'il y a une corrélation dans les changements, un sous-système externe peut être à l'origine du problème.


![Graphiques des échecs des dépendances et durée des appels aux dépendances](./media/app-insights-detect-triage-diagnose-java/11-dependencies.png)

Certains problèmes de dépendances lentes sont dus à des problèmes de géolocalisation. La banque Fabrikam utilise des machines virtuelles Azure et l'équipe a découvert que leur serveur web et le compte de ce serveur avaient été placés par inadvertance dans des pays différents. La migration d'un de ces deux éléments a apporté des améliorations considérables.


**Qu'avons-nous fait ?** Si le problème ne paraît pas venir d'une dépendance, et s’il n'a pas toujours été là, il est probablement dû à une modification récente. La perspective historique fournie par les graphiques des mesures et des événements facilite la mise en corrélation de changements soudains avec les déploiements. Cela permet de réduire le champ de la recherche du problème.


**Que se passe-t-il ?** Certains problèmes se produisent rarement et peuvent être difficiles à détecter en cas de test hors connexion. Tout ce que nous pouvons faire, c'est essayer de capturer le bogue lorsqu'il se produit en temps réel. Vous pouvez inspecter les vidages de pile dans les rapports d'exceptions. En outre, vous pouvez écrire les appels de suivi, soit avec votre framework de journalisation favori, soit avec trackTrace() ou trackEvent().


Fabrikam avait un problème intermittent avec les transferts entre comptes, mais uniquement avec certains types de compte. Pour mieux comprendre ce qui se produisait, ils ont inséré des appels trackTrace() à des points clés du code, en joignant le type de compte en tant que propriété à chaque appel. Cela a permis de filtrer plus facilement le suivi dans la recherche de diagnostic. Ils ont aussi joint les valeurs des paramètres en tant que propriétés, ainsi que des mesures pour les appels de trace.


## Gestion du problème


Une fois que vous avez diagnostiqué le problème, vous pouvez mettre en place les mesures nécessaires pour le résoudre. Peut-être devrez-vous annuler une modification récente ou peut-être pouvez-vous simplement résoudre le problème. Une fois la correction appliquée, Application Insights vous indiquera si vous avez réussi.


L'équipe de développement de la banque Fabrikam adopte une approche plus structurée de la mesure des performances qu'avant l'utilisation d'Application Insights.

* Elle définit des objectifs de performances sur des mesures spécifiques dans la page Vue d'ensemble Application Insights.

* Elle intègre dès le départ des mesures de performances dans l'application, par exemple des indicateurs qui mesurent la progression de l'utilisateur dans certains segments.




## Utilisation

Application Insights peut également servir à apprendre ce que les utilisateurs font avec une application. Une fois que cette dernière s'exécute correctement, l'équipe souhaite savoir quelles sont les fonctionnalités les plus populaires, ce que les utilisateurs ont comme difficultés et s'ils reviennent souvent. Ces données permettront de hiérarchiser le travail à venir. Et l'équipe peut prévoir de mesurer la réussite de chaque fonctionnalité dans le cadre du cycle de développement. [En savoir plus][usage].



## Suivi des activités des utilisateurs.

Si le temps de réponse reste excellent et qu’il y a peu d’exceptions, l’équipe de développement peut réfléchir à l’amélioration de l’expérience utilisateur et à la manière d’aider plus d’utilisateurs à atteindre leurs objectifs.


Par exemple, la visite d’un utilisateur typique sur un site web présente un parcours évident : de nombreux clients regardent les taux pour différents types de prêts ; certains remplissent un formulaire de devis et, parmi ceux qui reçoivent le devis, un petit nombre d’entre eux se décident et contractent un prêt.

![](./media/app-insights-detect-triage-diagnose-java/12-funnel.png)

En regardant à quel moment le plus grand nombre de clients s’est désintéressé, l’entreprise peut réfléchir à un moyen pour encourager davantage d’utilisateurs à aller plus loin dans leur exploration du site. Dans certains cas, il peut y avoir une défaillance de l'expérience utilisateur : par exemple, le bouton « suivant » est difficile de trouver ou les instructions ne sont pas claires. Toutefois, il est plus probable que ce désintéressement soit lié à des raisons commerciales : le taux du prêt est peut-être trop élevé.

Quelque soit les raisons, les données permettent à l’équipe de comprendre ce que font les utilisateurs. Plusieurs appels de suivi peuvent être insérés pour obtenir davantage de détails. TrackEvent() peut être utilisé pour compter toutes les actions d’un utilisateur, des moindres détails relatifs aux clics sur un bouton aux résultats les plus significatifs tels que le remboursement d’un prêt.

L'équipe est habituée à recevoir des informations sur l’activité d’un utilisateur. Aujourd'hui, chaque fois qu'ils conçoivent une nouvelle fonctionnalité, ils anticipent les avis qu’ils pourraient obtenir sur celle-ci. Ils conçoivent de zéro des appels de suivi dans la fonctionnalité. Ils exploitent les avis afin d’améliorer la fonctionnalité pour chaque cycle de développement.




## Vos applications

Voici donc une équipe qui utilise Application Insights non seulement pour résoudre les problèmes, mais aussi pour améliorer le cycle de développement. J'espère que ceci vous a donné quelques idées sur comment Application Insights peut vous aider à améliorer les performances de vos propres applications.

## Vidéo

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[usage]: app-insights-web-track-usage.md
 

<!---HONumber=Sept15_HO2-->