---
title: "Détection intelligente des anomalies de type échec dans Application Insights | Microsoft Docs"
description: "Vous prévient en cas de modifications inhabituelles du taux d’échec des demandes adressées à votre application web et fournit une analyse du diagnostic. Aucune configuration n’est nécessaire."
services: application-insights
documentationcenter: 
author: yorac
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: mbullwin
ms.openlocfilehash: ca484f4d11cf8ab18db2d0c6152f369a90311f10
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="smart-detection---failure-anomalies"></a>Détection intelligente des anomalies de type échec
[Application Insights](app-insights-overview.md) vous avertit automatiquement en temps quasi-réel si une augmentation anormale du taux des requêtes en échec est détectée dans votre application web. Il détecte une augmentation inhabituelle du nombre de demandes HTTP ou d’appels de dépendance signalés comme défaillants. Les demandes ayant échoué sont généralement celles dont le code de réponse est supérieur ou égal à 400. Pour vous aider à prioriser et à diagnostiquer le problème, la notification s’accompagne d’une analyse des caractéristiques des requêtes ayant échoué et de la télémétrie connexe. Elle fournit également des liens vers le portail Application Insights pour un diagnostic plus poussé. La fonctionnalité ne requiert ni installation ni configuration, puisqu’elle utilise des algorithmes d’apprentissage automatique pour prédire le taux d’échec normal.

Cette fonctionnalité est utilisée pour les applications web Java et ASP.NET, hébergées dans le cloud ou sur vos propres serveurs. Elle sert également pour n’importe quelle application qui génère de la télémétrie de demande ou de dépendance : par exemple, si vous avez un rôle de travail qui appelle [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) ou [TrackDependency()](app-insights-api-custom-events-metrics.md#trackdependency).

Après que vous avez configuré [Application Insights pour votre projet](app-insights-overview.md), et sous réserve que votre application génère une certaine quantité minimale de données de télémétrie, 24 heures sont nécessaires à la détection intelligente des anomalies de type échec pour découvrir le comportement normal de votre application avant d’être activée et de pouvoir envoyer des alertes.

Voici un exemple d’alerte.

![Exemple d’alerte de détection intelligente affichant l’analyse du cluster au moment de l’échec](./media/app-insights-proactive-failure-diagnostics/013.png)

> [!NOTE]
> Par défaut, vous obtenez un format de message plus court que dans cet exemple. Toutefois, vous pouvez [basculer vers ce format détaillé](#configure-alerts).
>
>

Notez qu’il vous indique :

* le taux d’échec par rapport au comportement normal de l’application ;
* combien d’utilisateurs sont affectés : afin de savoir dans quelle mesure vous devez vous inquiéter ;
* un modèle caractéristique associé aux échecs. Cet exemple contient un code de réponse, un nom de demande (opération) et une version de l’application spécifiques. Ces informations vous indiquent immédiatement où commencer la recherche dans votre code. Les autres possibilités peuvent être un type de navigateur ou un système d’exploitation client spécifique ;
* l’exception, le suivi des journaux et l’échec de dépendance (bases de données ou autres composants externes) qui semblent associés à des défaillances identifiées ;
* les liens directs aux recherches pertinentes sur la télémétrie dans Application Insights.

## <a name="benefits-of-smart-detection"></a>Avantages de la détection intelligente
Les [alertes de mesure](app-insights-alerts.md) ordinaires vous indiquent un problème potentiel. Mais la détection intelligente démarre le travail de diagnostic à votre place, effectue la majeure partie de l’analyse que vous auriez à effectuer vous-même. Vous obtenez les résultats clairement empaquetés, ce qui vous permet d’accéder rapidement à l’origine du problème.

## <a name="how-it-works"></a>Fonctionnement
La détection intelligente analyse les données de télémétrie reçues de votre application, en particulier le taux d’échecs. Cette règle compte le nombre de demandes dont la propriété `Successful request` a la valeur false, et le nombre d’appels de dépendance dont la propriété `Successful call` a la valeur false. Pour les demandes, `Successful request == (resultCode < 400)` par défaut (sauf si vous avez ajouté du code personnalisé pour [filtrer](app-insights-api-filtering-sampling.md#filtering) ou générer vos propres appels [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest)). 

Les performances de votre application présentent un modèle de comportement typique. Certaines demandes ou certains appels de dépendance sont davantage sujets aux erreurs que d’autres ; et le taux d’échec global peut augmenter en même temps que la charge. La détection intelligente utilise Machine Learning pour rechercher ces anomalies.

Comme les données de télémétrie arrivent dans Application Insights à partir de votre application web, la détection intelligente compare le comportement actuel avec les modèles observés au cours des derniers jours. Si une augmentation anormale du taux d’échec est observée par rapport aux performances précédentes, une analyse est déclenchée.

Quand une analyse est déclenchée, le service effectue une analyse des clusters pour chaque demande ayant échoué, dans le but d’identifier un modèle de valeurs caractérisant les échecs. Dans l’exemple ci-dessus, l’analyse a découvert que la plupart des échecs sont liés à un code de résultat, un nom de demande, un hôte de l’URL serveur et une instance de rôle spécifiques. En revanche, l’analyse a découvert que la propriété du système d’exploitation client est distribuée sur plusieurs valeurs et qu’elle n’est donc pas répertoriée.

Quand votre service est instrumenté avec cette télémétrie, l’analyseur recherche une exception et un échec de dépendance en lien avec les demandes dans le cluster qu’il a identifié, ainsi qu’un exemple de journaux de suivi associés à ces demandes.

L’analyse obtenue vous est envoyée sous forme d’alerte, sauf si vous n’avez pas configuré cette option.

Comme les [alertes que vous définissez manuellement](app-insights-alerts.md), vous pouvez examiner l’état de l’alerte et la configurer dans le panneau Alertes de votre ressource Application Insights. Cependant, contrairement aux autres alertes, vous n’avez pas besoin d’installer ni de configurer la détection intelligente. Si vous le souhaitez, vous pouvez la désactiver ou changer l’adresse de messagerie électronique cible.

## <a name="configure-alerts"></a>Configurer des alertes
Vous pouvez désactiver la détection intelligente, modifier les destinataires de courrier électronique, créer un webhook ou opter pour des messages d’alerte plus détaillés.

Ouvrez la page Alertes. La fonctionnalité Anomalies des échecs est incluse dans les alertes que vous avez définies manuellement, et vous pouvez savoir si elle se trouve actuellement à l’état d’alerte.

![Dans la page Vue d’ensemble, cliquez sur la mosaïque Alertes, ou sur n’importe quelle page de mesures, cliquez sur le bouton Alertes.](./media/app-insights-proactive-failure-diagnostics/021.png)

Cliquez sur l’alerte pour la configurer.

![Configuration](./media/app-insights-proactive-failure-diagnostics/032.png)

Notez que vous pouvez désactiver la détection intelligente, mais pas la supprimer (ni en créer une autre).

#### <a name="detailed-alerts"></a>Alertes détaillées
Si vous sélectionnez « Get more detailed diagnostics (Obtenir des diagnostics plus détaillés) », l’e-mail contient plus d’informations de diagnostic. Les données figurant dans l’e-mail peuvent parfois suffire pour vous permettre de diagnostiquer le problème.

Il se peut que l’alerte détaillée contienne des informations sensibles, car elle comprend des messages d’exception de traçage. Toutefois, cela se produit uniquement si votre code autorise que ces informations sensibles soient incluses dans ces messages.

## <a name="triaging-and-diagnosing-an-alert"></a>Triage et diagnostic d’une alerte
Une alerte indique qu’une augmentation anormale du taux de demandes ayant échoué a été détectée. Il est probable que votre application ou son environnement rencontre un problème.

D’après le pourcentage de requêtes et le nombre d’utilisateurs touchés, vous pouvez évaluer l’urgence du problème. Dans l’exemple ci-dessus, le taux d’échec de 22.5 % est comparé à un taux normal d’1 %, ce qui indique un problème. En revanche, seuls 11 utilisateurs ont été affectés. S’il s’agissait de votre application, vous pourriez évaluer le niveau de gravité.

Dans de nombreux cas, vous serez en mesure de diagnostiquer le problème rapidement à partir du nom de la demande, de l’exception, de l’échec de dépendance et du journal de suivi fournis.

Il existe certains autres indices. Par exemple, le taux d’échec de dépendance dans cet exemple est identique au taux d’exception (89,3 %). Cela signifie que l’exception émane directement de l’échec de dépendance, ce qui vous donne une idée précise de l’emplacement dans votre code où commencer la recherche.

Pour approfondir vos recherches, les liens de chaque section vous dirigent directement vers une [page de recherche](app-insights-diagnostic-search.md) comportant uniquement les demandes, l’exception, la dépendance ou les journaux de suivi pertinents. Vous pouvez également ouvrir le [portail Azure](https://portal.azure.com), accéder à la ressource Application Insights pour votre application et ouvrir le panneau Échecs.

Dans cet exemple, cliquez sur le lien « View dependency failures details (Afficher les détails sur les échecs de dépendance) » pour ouvrir le panneau de recherche d’Application Insights. Il affiche l’instruction SQL qui contient un exemple de l’origine du problème : des valeurs NULL fournies dans les champs obligatoires ont bloqué la validation pendant l’opération d’enregistrement.

![Recherche de diagnostic](./media/app-insights-proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Consulter les alertes récentes

Cliquez sur **Détection intelligente** pour accéder à l’alerte la plus récente :

![Résumé des alertes](./media/app-insights-proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Quelle est la différence ?
La détection intelligente des anomalies de type échec vient compléter d’autres fonctionnalités d’Application Insights similaires, mais distinctes.

* [Metric Alerts](app-insights-alerts.md) , qui peuvent surveiller un large éventail de mesures telles que l’occupation du processeur, les taux de demandes, les temps de chargement de page, etc. Vous pouvez les utiliser pour savoir si vous devez ajouter des ressources, par exemple. En revanche, la détection intelligente des anomalies de type échec ne couvre qu’une petite gamme de mesures critiques (pour l’instant, le taux de requêtes en échec uniquement), conçues pour vous avertir en temps quasi réel si le taux de requêtes en échec de votre application web augmente de manière significative par rapport à la normale.

    La détection intelligente ajuste automatiquement son seuil en réponse aux conditions en vigueur.

    Elle démarre le travail de diagnostic à votre place.
* La [détection intelligente des anomalies de performance](app-insights-proactive-performance-diagnostics.md) utilise également l’intelligence artificielle pour découvrir des modèles inhabituels dans vos mesures, sans qu’aucune configuration ne soit nécessaire. Toutefois, contrairement à la détection intelligente des anomalies de type échec, l’objectif de la détection intelligente des anomalies de performance est de repérer les segments de votre collecteur d’utilisation qui peuvent être mal pris en charge (par certaines pages sur un certain type de navigateur, par exemple). L’analyse est effectuée tous les jours, et si elle renvoie un résultat, il est souvent bien moins urgent qu’une alerte. En revanche, l’analyse des anomalies de type échec est exécutée en continu sur les données de télémétrie entrantes. Si le taux d’échec du serveur est plus élevé que prévu, vous en êtes averti en quelques minutes.

## <a name="if-you-receive-a-smart-detection-alert"></a>Si vous recevez une alerte de la détection intelligente
*Pourquoi ai-je reçu cette alerte ?*

* Nous avons détecté une augmentation anormale du taux de demandes ayant échoué par rapport au taux de référence de la période précédente. Après l’analyse des défaillances et de la télémétrie associée, nous pensons qu’il existe un problème que vous devez examiner.

*La notification signifie-t-elle obligatoirement que mon application rencontre un problème ?*

* Nous essayons de vous alerter sur l’interruption ou la dégradation de l’application, mais vous êtes la seule personne habilitée à comprendre pleinement la sémantique et l’impact sur l’application ou les utilisateurs.

*Mais alors, vous examinez mes données ?*

* Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](app-insights-data-retention-privacy.md).

*Dois-je m’abonner à cette alerte ?*

* Non. Chaque application qui envoie des données de télémétrie de requête possède la règle d’alerte de la détection intelligente.

*Puis-je me désabonner ou obtenir des notifications envoyées à mes collègues ?*

* Oui. Dans les règles d’alerte, cliquez sur la règle de la détection intelligente pour la configurer. Vous pouvez désactiver cette alerte, ou modifier les destinataires de l’alerte.

*J’ai perdu le courrier électronique Où puis-je trouver les notifications dans le portail ?*

* Dans les journaux d’activité. Dans Azure, ouvrez la ressource Application Insights correspondant à votre application, puis sélectionnez Journaux d’activité.

*Certaines des alertes concernent des problèmes connus, et je ne souhaite pas les recevoir.*

* Notre backlog comporte la suppression de l’alerte.

## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](app-insights-metrics-explorer.md)
* [Navigateur de recherche](app-insights-diagnostic-search.md)
* [Analytics : un puissant langage de requête](app-insights-analytics-tour.md)

Les détections intelligentes sont entièrement automatiques. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](app-insights-alerts.md)
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md)
