<properties 
	pageTitle="Diagnostics proactifs en temps quasi-réel dans Application Insights" 
	description="Les diagnostics proactifs en temps quasi-réel vous informent automatiquement si le temps de réponse du serveur présente un comportement inhabituel. Aucune configuration n’est nécessaire." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# Diagnostics proactifs en temps quasi-réel

*Cette fonctionnalité est encore en phase de tests préliminaires.*

*Merci d’envoyer vos commentaires à :* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

[Visual Studio Application Insights](app-insights-overview.md) vous avertit automatiquement en temps quasi-réel si le taux de requêtes ayant échoué de votre application web augmente considérablement. Pour vous aider à prioriser et à diagnostiquer le problème, la notification s’accompagne d’une analyse des caractéristiques des requêtes ayant échoué et de la télémétrie connexe. Elle fournit également des liens vers le portail Application Insights pour un diagnostic plus poussé. La fonctionnalité ne requiert aucune installation ni configuration, puisqu’elle utilise des algorithmes d’apprentissage automatique pour prédire le taux d’échec normal de référence. Pour fonctionner, elle nécessite tout de même un volume de trafic minimum.

Voici un exemple d’alerte :

![Exemple d’alerte intelligente affichant l’analyse du cluster au moment de l’échec](./media/app-insights-nrt-proactive-diagnostics/010.png)

Pour obtenir des alertes d’échec, vous devez configurer [Application Insights pour votre application ASP.NET](app-insights-asp-net.md) ou [pour votre application web Java](app-insights-java-get-started.md).

## Fonctionnement

Les diagnostics proactifs en temps quasi-réel surveillent la télémétrie reçue depuis votre application, et en particulier le taux de requêtes ayant échoué. Cette mesure indique généralement le nombre de requêtes HTTP ayant renvoyé un code de réponse de 400 ou plus (à moins que vous n’ayez écrit du code personnalisé pour [filtrer](app-insights-api-filtering-sampling.md#filtering) ou générer vos propres appels [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

Selon le comportement précédent de cette mesure, le service prédit la plage de valeurs devant être attendue. Si la valeur réelle est beaucoup plus importante, il génère une alerte.

Lorsqu’une alerte est déclenchée, le service effectue une analyse des clusters sur plusieurs dimensions des requêtes, afin d’identifier un modèle de valeurs caractérisant les échecs. Dans l’exemple ci-dessus, l’analyse met en évidence que les échecs concernent pour la plupart un nom de requête spécifique, mais qu’ils sont indépendants de l’instance d’hôte ou de serveur.

L’analyseur recherche ensuite les exceptions et les défaillances de dépendance associées aux requêtes dans le cluster qu’il a identifié, avec un exemple de tous les journaux de suivi associés à ces requêtes.

L’analyse obtenue vous est envoyée par message électronique, sauf si vous ne l’avez pas configurée pour.

Comme les [alertes que vous définissez manuellement](app-insights-alerts.md), vous pouvez examiner l’état de l’alerte et la configurer dans le panneau Alertes de votre ressource Application Insights. Cependant, contrairement aux autres alertes, vous n’avez pas besoin d’installer ni de configurer l’alerte d’échec adaptative. Si vous le souhaitez, vous pouvez la désactiver ou changer l’adresse de messagerie électronique cible.

## Si vous recevez une alerte

Une alerte indique qu’une anomalie dans le taux de requêtes ayant échoué a été détectée. Il est probable que votre application ou son environnement rencontre un problème. Peut-être que la nouvelle version que vous venez de télécharger ne fonctionne pas bien, ou qu’une dépendance comme une base de données ou une ressource externe est défectueuse.

D’après le pourcentage de requêtes et le nombre d’utilisateurs touchés, vous pouvez évaluer l’urgence du problème.

Dans de nombreux cas, vous serez en mesure de diagnostiquer le problème rapidement à partir du nom de la demande, des exceptions, des dépendances et des autres données fournies.

Si vous devez approfondir vos recherches, les liens de chaque section vous dirigeront directement sur une [page de recherche](app-insights-diagnostic-search.md) comportant uniquement les requêtes, exceptions, dépendances ou suivis pertinents. Vous pouvez également ouvrir le [portail Azure](https://portal.azure.com), accéder à la ressource Application Insights pour votre application, et ouvrir le panneau Défaillances.

## Consulter les alertes récentes

Pour consulter les alertes dans le portail, accédez à **Paramètres, Événements opérationnels**.

![Résumé des alertes](./media/app-insights-nrt-proactive-diagnostics/040.png)

Cliquez sur une alerte pour afficher ses détails complets.


## Configurer des alertes 

> **L’expérience utilisateur de configuration n’est pas encore disponible.**
> 
> Merci d’envoyer vos commentaires à : [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)
>
> Voici comment la configuration pourrait fonctionner :

Ouvrez la page Alertes. L’alerte d’échec adaptative est incluse avec toutes les alertes que vous avez définies manuellement, et vous pouvez savoir si elle se trouve actuellement en état d’alerte.

![Dans la page Vue d’ensemble, cliquez sur la mosaïque Alertes, ou sur n’importe quelle page de mesures, cliquez sur le bouton Alertes.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Cliquez sur l’alerte pour la configurer.

![Configuration](./media/app-insights-nrt-proactive-diagnostics/030.png)

Notez que vous pouvez désactiver l’alerte d’échec adaptative, mais pas la supprimer (ni en créer une autre).


## Quelle est la différence ?

Les diagnostics proactifs en temps quasi-réel viennent compléter d’autres fonctionnalités d’Application Insights similaires mais distinctes.

* C’est vous qui définissez les [alertes de mesures](app-insights-alerts.md), qui peuvent surveiller un large éventail de mesures telles que l’occupation du processeur, les taux de requêtes, les temps de chargement de page, et bien d’autres. Vous pouvez les utiliser pour savoir si vous devez ajouter des ressources, par exemple. En revanche, les diagnostics proactifs en temps quasi-réel ne couvrent qu’une petite gamme de mesures critiques (pour l’instant, le taux de requêtes ayant échoué uniquement), conçues pour vous avertir en temps quasi-réel si le taux de requêtes ayant échoué de votre application web augmente de manière significative par rapport à la normale.
* La [détection proactive](app-insights-proactive-detection.md) utilise également l’intelligence artificielle pour découvrir des modèles inhabituels dans vos mesures, sans qu’aucune configuration ne soit nécessaire. Toutefois, contrairement aux diagnostics proactifs en temps quasi-réel, l’objectif de la détection proactive vise à repérer les segments de votre collecteur d’utilisation qui peuvent être mal pris en charge (par certaines pages sur un certain type de navigateur, par exemple). L’analyse est effectuée tous les jours, et si elle renvoie un résultat, il est souvent bien moins urgent qu’une alerte. En revanche, l’analyse des diagnostics proactifs en temps quasi-réel est exécutée en continu sur la télémétrie entrante, et si le taux de défaillance du serveur est plus élevé que prévu, vous en êtes averti en quelques minutes.


## Faites-nous part de vos commentaires

*Cette fonctionnalité est encore en phase de tests préliminaires. Tous vos commentaires sont les bienvenus. Merci de les envoyer à l’adresse * [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) *ou de cliquer sur le lien de commentaires dans le message d’alerte.*

<!---HONumber=AcomDC_0218_2016-->