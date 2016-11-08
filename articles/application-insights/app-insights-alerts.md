---
title: Configuration d’alertes dans Application Insights
description: Obtenez des courriers électroniques sur les pannes, les exceptions et les modifications des métriques.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: awills

---
# Configuration d’alertes dans Application Insights
[Visual Studio Application Insights][start] peut vous signaler les modifications des métriques de performances ou d’utilisation de votre application.

Application Insights analyse votre application en direct sur un [large éventail de plateformes][platforms] pour vous aider à diagnostiquer les problèmes de performances et comprendre les schémas d’utilisation.

Il existe deux types d’alertes :

* Les **tests Web** indiquent quand votre site est indisponible sur Internet ou répond lentement. [En savoir plus][availability].
* Les **alertes de métrique** indiquent quand une métrique dépasse une valeur seuil pendant une certaine période, comme le nombre d’échecs, la mémoire ou les affichages de page. 

Il existe une [page distincte sur les tests Web][availability], nous allons donc nous concentrer ici sur les alertes de métrique.

> [!NOTE]
> Vous pouvez également recevoir des e-mails de la fonctionnalité [Détection proactive](app-insights-proactive-detection.md) pour être averti de conditions de performances inhabituelles sur votre application. Contrairement aux alertes, ces notifications s’exécutent sans aucune configuration préalable. Leur rôle consiste davantage à ajuster les performances de votre application qu’à déclencher une alarme sur des problèmes immédiats.
> 
> 

## Alertes de métrique
Si vous n’avez pas configuré Application Insights pour votre application, [commencez par le faire][start].

Pour obtenir un message électronique lorsqu’une métrique dépasse un seuil, démarrez dans Metrics Explorer ou à partir de la vignette des Règles d’alerte sur le panneau Vue d’ensemble.

![Dans le panneau Règles d’alerte, cliquez sur Ajouter une alerte. Définissez votre application comme ressource à mesurer, fournissez un nom pour l’alerte et sélectionnez une métrique.](./media/app-insights-alerts/01-set-metric.png)

* Définissez la ressource avant les autres propriétés. **Choisissez la ressource « (composants) »**si vous souhaitez définir des alertes sur les mesures de performances ou d'utilisation.
* Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.
* Le nom que vous donnez à l’alerte doit être unique dans le groupe de ressources (pas uniquement dans votre application).
* Si vous cochez la case « Envoyer des e-mails aux propriétaires… », les alertes seront envoyées par courrier électronique à toute personne ayant accès à ce groupe de ressources. Pour étendre cet ensemble de personnes, ajoutez-le au [groupe de ressources ou à l’abonnement](app-insights-resources-roles-access-control.md) (pas à la ressource).
* Si vous spécifiez « E-mails supplémentaires », les alertes seront envoyées à ces personnes ou groupes (que vous ayez ou non coché la case « Envoyer des e-mails aux propriétaires… »). 
* Définissez une [adresse de webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) si vous avez configuré une application web qui répondra aux alertes. Elle sera appelée lorsque l'alerte sera Activée (c’est-à-dire déclenchée) ou Résolue. (Mais notez qu’à l’heure actuelle, les paramètres de requête ne sont pas transmis en tant que propriétés webhook.)
* Vous pouvez désactiver ou activer l'alerte avec les boutons en haut du panneau.

*Je ne vois pas le bouton Ajouter une alerte.* Utilisez-vous un compte professionnel ? Vous pouvez définir des alertes si vous avez un accès propriétaire ou collaborateur à cette ressource d’application. Examinez Paramètres -> Utilisateurs. [En savoir plus sur le contrôle d’accès][roles].

> [!NOTE]
> Dans le panneau Alertes, vous pouvez constater qu’une alerte est déjà configurée : [diagnostics proactifs NRT](app-insights-nrt-proactive-diagnostics.md). Il s'agit d'une alerte automatique qui surveille une métrique spécifique, le taux d'échec des demandes. Sauf si vous décidez de désactiver cette option, vous n'avez pas besoin de définir votre propre alerte pour les taux d'échec des demandes.
> 
> 

## Consultez vos alertes
Vous recevez un e-mail lorsqu’une alerte bascule entre les états inactive et active.

L’état actuel de chaque alerte est indiqué dans le panneau Règles d’alerte.

Il existe un résumé de l’activité récente dans la liste déroulante des alertes :

![](./media/app-insights-alerts/010-alert-drop.png)

L’historique des modifications d’état figure dans le journal d’audit :

![Dans le panneau Vue d’ensemble, cliquez sur Paramètres, puis sur Journaux d'audit.](./media/app-insights-alerts/09-alerts.png)

## Fonctionnement des alertes
* Une alerte a trois états : « Jamais activée », « Activée » et « Résolue ». L’état Activé signifie que la condition spécifiée était vraie lors de la dernière évaluation.
* Une notification est générée lorsqu'une alerte change d'état. (Si la condition d'alerte était déjà vraie lorsque vous avez créé l'alerte, il est possible que vous ne receviez pas de notification tant que la condition n’est pas fausse.)
* Chaque notification génère un message électronique si vous avez coché la case E-mails ou fourni des adresses de messagerie électronique. Vous pouvez également consulter la liste déroulante Notifications.
* Une alerte est évaluée chaque fois qu’une mesure arrive, mais pas autrement.
* L’évaluation agrège la mesure de la période précédente, puis la compare à la valeur de seuil pour déterminer le nouvel état.
* La période que vous choisissez spécifie la durée pendant laquelle les mesures sont agrégées. Cela n’affecte pas la fréquence à laquelle l’alerte est évaluée, mais cela dépend de la fréquence d’arrivée des mesures.
* Si aucune donnée n’arrive pour une mesure particulière pendant un certain temps, l’écart a des effets différents sur l’évaluation de l’alerte et sur les graphiques dans Metrics Explorer. Dans Metrics Explorer, si aucune donnée n’apparaît plus longtemps que l’intervalle d’échantillonnage du graphique, le graphique affichera la valeur 0. Cependant, une alerte basée sur la même mesure n’est pas réévaluée et l’état de l’alerte reste inchangé.
  
    Lorsque les données arrivent par la suite, le graphique revient à une valeur différente de zéro. L’alerte sera évaluée en fonction des données disponibles pour la période spécifiée. Si le nouveau point de données est le seul disponible dans la période, l’agrégat reposera uniquement sur ce point.
* Une alerte peut osciller fréquemment entre les états alerte et intègre même si vous définissez une longue période. Cela peut se produire si la valeur de la mesure est placée autour du seuil. Le seuil est cohérent : la transition vers l’état alerte se produit à la même valeur que la transition vers l’état intègre.

## Alertes de disponibilité
Vous pouvez définir des tests Web qui testent n’importe quel site web à partir de points du monde entier. [En savoir plus][availability].

## Quelles sont les alertes à définir ?
Cela dépend de votre application. Pour commencer, il est préférable de ne pas définir un trop grand nombre de métriques. Prenez le temps d’examiner vos graphiques de métriques pendant l’exécution de votre application, pour mieux comprendre comment elle se comporte normalement. Cela vous aidera à trouver des moyens d’améliorer ses performances. Définissez ensuite des alertes qui vous indiquent lorsque les métriques quittent la zone normale.

Les alertes les plus appréciées sont les suivantes :

* Les [tests Web][availability] sont importants si votre application est un site Web ou un service Web qui est visible sur l’Internet public. Ils vous indiquent si votre site tombe en panne ou répond lentement : même si le problème est plutôt dû au transporteur qu’à votre application. Ce sont des tests synthétiques, qui ne mesurent pas l’expérience réelle de vos utilisateurs.
* Les [mesures de navigateur][client], surtout les **temps de chargement des pages** de navigateur, sont efficaces pour les applications web. Si votre page comporte un grand nombre de scripts, il peut être intéressant de rechercher des **exceptions du navigateur**. Pour obtenir ces métriques et alertes, vous devez configurer la [surveillance de page Web][client].
* **Temps de réponse de serveur** et **échec des demandes** pour le côté serveur des applications web. Outre la définition des alertes, gardez un œil sur ces métriques pour voir si elles varient énormément en cas de taux de demandes élevés : cela peut indiquer que votre application manque de ressources.
* **Exceptions du serveur** - pour les afficher, vous devrez peut-être effectuer une [installation supplémentaire](app-insights-asp-net-exceptions.md).

## Automation
* [Utiliser PowerShell pour automatiser la configuration d’alertes](app-insights-powershell-alerts.md)
* [Utiliser les webhooks pour automatiser la réponse aux alertes](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## Voir aussi
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md)
* [Automatiser la configuration d’alertes](app-insights-powershell-alerts.md)
* [Détection proactive](app-insights-proactive-detection.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md



<!---HONumber=AcomDC_0622_2016-->