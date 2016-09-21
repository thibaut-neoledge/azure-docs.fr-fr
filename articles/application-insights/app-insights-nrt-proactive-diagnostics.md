<properties 
	pageTitle="Diagnostics proactifs en temps quasi réel dans Application Insights | Microsoft Azure" 
	description="Vous avertissent en cas de modèles de défaillance inhabituels dans votre application et fournissent une analyse de diagnostic. Aucune configuration n’est nécessaire." 
	services="application-insights" 
    documentationCenter=""
	authors="yorac" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2016" 
	ms.author="awills"/>
 
# Diagnostics proactifs en temps quasi-réel

[Visual Studio Application Insights](app-insights-overview.md) vous avertit automatiquement en temps quasi-réel si une augmentation anormale du taux de demandes ayant échoué est détectée. Pour vous aider à prioriser et à diagnostiquer le problème, la notification s’accompagne d’une analyse des caractéristiques des requêtes ayant échoué et de la télémétrie connexe. Elle fournit également des liens vers le portail Application Insights pour un diagnostic plus poussé. La fonctionnalité ne requiert aucune installation ni configuration, puisqu’elle utilise des algorithmes d’apprentissage automatique pour prédire le taux d’échec normal.

Cette fonctionnalité est utilisée pour les applications web Java et ASP.NET, hébergées dans le cloud ou sur vos propres serveurs. Elle sert également pour n’importe quelle application qui génère de la télémétrie de demande : par exemple, si vous avez un rôle de travail qui appelle [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request).

Après avoir configuré [Application Insights pour votre projet](app-insights-overview.md), et si votre application génère un certain volume minimal de données de télémétrie, un délai de 24 heures est nécessaire aux diagnostics proactifs en temps quasi réel pour découvrir le comportement normal de votre application, être activés et envoyer des alertes.

Voici un exemple d’alerte.

![Exemple d’alerte intelligente affichant l’analyse du cluster au moment de l’échec](./media/app-insights-nrt-proactive-diagnostics/010.png)

> [AZURE.NOTE] Par défaut, vous obtenez un format de message plus court que dans cet exemple. Toutefois, vous pouvez [basculer vers ce format détaillé](#configure-alerts).

Notez qu’il vous indique :

* le taux d’échec par rapport au comportement normal de l’application ;
* combien d’utilisateurs sont affectés : afin de savoir dans quelle mesure vous devez vous inquiéter ;
* un modèle caractéristique associé aux échecs. Cet exemple contient un code de réponse, un nom de demande (opération) et une version de l’application spécifiques. Ces informations vous indiquent immédiatement où commencer la recherche dans votre code. Les autres possibilités peuvent être un type de navigateur ou un système d’exploitation client spécifique ;
* l’exception, le suivi des journaux et l’échec de dépendance (bases de données ou autres composants externes) qui semblent associés aux demandes identifiées ayant échoué ;
* les liens directs aux recherches pertinentes sur la télémétrie dans Application Insights.

## Avantages des alertes proactives

Les [alertes de mesure](app-insights-alerts.md) ordinaires vous indiquent un problème potentiel. Mais les diagnostics proactifs NRT démarrent le travail de diagnostic à votre place, effectuent la majeure partie de l’analyse que vous auriez à effectuer vous-même. Vous obtenez les résultats clairement empaquetés, ce qui vous permet d’accéder rapidement à l’origine du problème.

## Fonctionnement

Les diagnostics proactifs en temps quasi-réel surveillent la télémétrie reçue depuis votre application, et en particulier le taux de requêtes ayant échoué. Cette mesure détermine le nombre de demandes dont la propriété `Successful request` a la valeur false. Par défaut, `Successful request== (resultCode < 400)` (sauf si vous avez ajouté du code personnalisé pour [filtrer](app-insights-api-filtering-sampling.md#filtering) ou générer vos propres appels [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

Les performances de votre application présentent un modèle de comportement typique. Certaines requêtes seront davantage sujettes aux erreurs que d’autres ; et le taux d’échec global peut s’accroître à mesure que la charge augmente. Les diagnostics proactifs en temps quasi réel utilisent Machine Learning pour rechercher ces anomalies.

Comme la télémétrie entre dans Application Insights à partir de votre application web, les diagnostics proactifs NRT comparent le comportement actuel avec les modèles constatés au cours des derniers jours. Si une augmentation anormale du taux d’échec est observée par rapport aux performances précédentes, une analyse est déclenchée.

Quand une analyse est déclenchée, le service effectue une analyse des clusters pour chaque demande ayant échoué, dans le but d’identifier un modèle de valeurs caractérisant les échecs. Dans l’exemple ci-dessus, l’analyse a découvert que la plupart des échecs sont liés à un code de résultat, un nom de demande, un hôte de l’URL serveur et une instance de rôle spécifiques. En revanche, l’analyse a découvert que la propriété du système d’exploitation client est distribuée sur plusieurs valeurs et qu’elle n’est donc pas répertoriée.

Quand votre service est instrumenté avec cette télémétrie, l’analyseur recherche une exception et un échec de dépendance qui sont associés aux demandes dans le cluster qu’il a identifié, avec un exemple de tous les journaux de suivi associés à ces demandes.

L’analyse obtenue vous est envoyée sous forme d’alerte, sauf si vous n’avez pas configuré cette option.

Comme les [alertes que vous définissez manuellement](app-insights-alerts.md), vous pouvez examiner l’état de l’alerte et la configurer dans le panneau Alertes de votre ressource Application Insights. Cependant, contrairement aux autres alertes, vous n’avez pas besoin d’installer ni de configurer les diagnostics proactifs en temps quasi réel. Si vous le souhaitez, vous pouvez la désactiver ou changer l’adresse de messagerie électronique cible.


## Configurer des alertes 

Vous pouvez désactiver les diagnostics proactifs, modifier les destinataires d’e-mails, créer un webhook ou vous abonner à des messages d’alerte plus détaillés.

Ouvrez la page Alertes. Les diagnostics proactifs sont inclus avec toutes les alertes que vous avez définies manuellement, ce qui vous permet de savoir s’ils se trouvent actuellement en état d’alerte.

![Dans la page Vue d’ensemble, cliquez sur la mosaïque Alertes, ou sur n’importe quelle page de mesures, cliquez sur le bouton Alertes.](./media/app-insights-nrt-proactive-diagnostics/021.png)

Cliquez sur l’alerte pour la configurer.

![Configuration](./media/app-insights-nrt-proactive-diagnostics/031.png)


Notez que vous pouvez désactiver les diagnostics proactifs, mais pas les supprimer (ni en créer d’autres).

#### Alertes détaillées

Si vous sélectionnez « Receive detailed analysis (Recevoir l’analyse détaillée) », l’e-mail contient plus d’informations de diagnostic. Les données figurant dans l’e-mail peuvent parfois suffire pour vous permettre de diagnostiquer le problème.

Il se peut que l’alerte détaillée contienne des informations sensibles, car elle comprend des messages d’exception de traçage. Toutefois, cela se produit uniquement si votre code autorise que ces informations sensibles soient incluses dans ces messages.


## Triage et diagnostic d’une alerte

Une alerte indique qu’une augmentation anormale du taux de demandes ayant échoué a été détectée. Il est probable que votre application ou son environnement rencontre un problème.

D’après le pourcentage de requêtes et le nombre d’utilisateurs touchés, vous pouvez évaluer l’urgence du problème. Dans l’exemple ci-dessus, le taux d’échec de 22.5 % est comparé à un taux normal d’1 %, ce qui indique un problème. En revanche, seuls 11 utilisateurs ont été affectés. S’il s’agissait de votre application, vous pourriez évaluer le niveau de gravité.

Dans de nombreux cas, vous serez en mesure de diagnostiquer le problème rapidement à partir du nom de la demande, de l’exception, de l’échec de dépendance et du journal de suivi fournis.

Il existe certains autres indices. Par exemple, le taux d’échec de dépendance dans cet exemple est identique au taux d’exception (89,3 %). Cela signifie que l’exception émane directement de l’échec de dépendance, ce qui vous donne une idée précise de l’emplacement dans votre code où commencer la recherche.

Pour approfondir vos recherches, les liens de chaque section vous dirigent directement vers une [page de recherche](app-insights-diagnostic-search.md) comportant uniquement les demandes, l’exception, la dépendance ou les journaux de suivi pertinents. Vous pouvez également ouvrir le [portail Azure](https://portal.azure.com), accéder à la ressource Application Insights pour votre application et ouvrir le panneau Échecs.

Dans cet exemple, en cliquant sur le lien "Afficher les détails des échecs de dépendance", vous ouvrez le panneau de recherche Application Insights sur l’instruction SQL à l’origine du problème : des valeurs NULL fournies dans des champs obligatoires n’ont pas été validées pendant l’opération d’enregistrement.


![Recherche de diagnostic](./media/app-insights-nrt-proactive-diagnostics/051.png)

## Consulter les alertes récentes

Pour consulter les alertes dans le portail, accédez à **Paramètres, Journaux d’audit**.

![Résumé des alertes](./media/app-insights-nrt-proactive-diagnostics/041.png)


Cliquez sur une alerte pour afficher ses détails complets.

Ou cliquez sur **Détection Proactive** pour accéder à l’alerte la plus récente :

![Résumé des alertes](./media/app-insights-nrt-proactive-diagnostics/070.png)




## Quelle est la différence ?

Les diagnostics proactifs en temps quasi-réel viennent compléter d’autres fonctionnalités d’Application Insights similaires mais distinctes.

* C’est vous qui définissez les [alertes de mesures](app-insights-alerts.md), qui peuvent surveiller un large éventail de mesures telles que l’occupation du processeur, les taux de demandes, les temps de chargement de page, etc. Vous pouvez les utiliser pour savoir si vous devez ajouter des ressources, par exemple. En revanche, les diagnostics proactifs en temps quasi-réel ne couvrent qu’une petite gamme de mesures critiques (pour l’instant, le taux de requêtes ayant échoué uniquement), conçues pour vous avertir en temps quasi-réel si le taux de requêtes ayant échoué de votre application web augmente de manière significative par rapport à la normale.

    Les diagnostics proactifs NRT ajuste automatiquement son seuil en réponse aux conditions en vigueur.

    Ils démarrent le travail de diagnostic à votre place.
* La [détection proactive](app-insights-proactive-detection.md) utilise également l’intelligence artificielle pour découvrir des modèles inhabituels dans vos mesures, sans qu’aucune configuration ne soit nécessaire. Toutefois, contrairement aux diagnostics proactifs en temps quasi-réel, l’objectif de la détection proactive vise à repérer les segments de votre collecteur d’utilisation qui peuvent être mal pris en charge (par certaines pages sur un certain type de navigateur, par exemple). L’analyse est effectuée tous les jours, et si elle renvoie un résultat, il est souvent bien moins urgent qu’une alerte. En revanche, l’analyse des diagnostics proactifs en temps quasi-réel est exécutée en continu sur la télémétrie entrante, et si le taux de défaillance du serveur est plus élevé que prévu, vous en êtes averti en quelques minutes.

## Si vous recevez une alerte de diagnostics proactifs NRT

*Pourquoi ai-je reçu cette alerte ?*

*	Nous avons détecté une augmentation anormale du taux de demandes ayant échoué par rapport au taux de référence de la période précédente. Après l’analyse des défaillances et de la télémétrie associée, nous pensons qu’il existe un problème que vous devez examiner.

*La notification signifie-t-elle obligatoirement que mon application rencontre un problème ?*

*	Nous essayons de vous alerter sur l’interruption de l’application ou la dégradation, mais vous êtes la seule personne habilitée à comprendre la sémantique et l’impact sur l’application ou les utilisateurs.

*Mais alors, vous examinez mes données ?*

*	Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](app-insights-data-retention-privacy.md).

*Dois-je m’abonner à cette alerte ?*

*	Non. Chaque application de télémétrie qui envoie de la télémétrie de demande comporte cette règle d’alerte.

*Puis-je me désabonner ou obtenir des notifications envoyées à mes collègues ?*

*	Oui. Dans les règles d’alerte, cliquez sur la règle de diagnostics proactifs pour la configurer. Vous pouvez désactiver cette alerte, ou modifier les destinataires de l’alerte.

*J’ai perdu le courrier électronique Où puis-je trouver les notifications dans le portail ?*

*	Dans les journaux d’audit. Cliquez sur Paramètres, Journaux d’audit, puis sur une alerte pour afficher son occurrence, avec des détails limités.

*Certaines des alertes sont des problèmes connus et je ne souhaite pas les recevoir.*

*	Notre backlog comporte la suppression de l’alerte.


## Faites-nous part de vos commentaires

*Votre avis sur le sujet nous intéresse. Merci d’envoyer vos commentaires à :* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).

## Étapes suivantes

Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](app-insights-metrics-explorer.md)
* [Navigateur de recherche](app-insights-diagnostic-search.md)
* [Analytics : un puissant langage de requête](app-insights-analytics-tour.md)

Les détections proactives sont entièrement automatiques. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](app-insights-alerts.md)
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->