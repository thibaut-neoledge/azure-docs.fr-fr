<properties 
	pageTitle="Application Insights : détection proactive" 
	description="Application Insights réalise une analyse télémétrique approfondie de votre application et vous avertit des éventuels problèmes de performances." 
	services="application-insights" 
    documentationCenter="windows"
	authors="antonfrMSFT" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016" 
	ms.author="awills"/>

#  Application Insights : détection proactive

*Application Insights est à l'état de version préliminaire.*


Application Insights réalise une analyse télémétrique approfondie de votre application et peut vous avertir des éventuels problèmes de performances. Vous lisez probablement cela parce que vous avez reçu l’une de nos alertes proactives par courrier électronique.


## Qu’est-ce que la détection proactive ?

La détection proactive permet de détecter les anomalies de performances dans votre application en analysant les données de télémétrie qu’elle envoie à Application Insights.

Elle recherche en particulier les problèmes de performances qui touchent uniquement certains de vos utilisateurs ou bien l’ensemble des utilisateurs mais dans certains cas de figure seulement.

Par exemple, elle peut vous avertir que les pages de votre application se chargent beaucoup plus lentement dans un type de navigateur que dans d’autres ou que les demandes sont traitées plus lentement à partir d’un serveur particulier. Elle peut aussi détecter les problèmes liés à des combinaisons de propriétés, tels que des pages qui se chargent lentement dans une zone géographique déterminée à des heures précises de la journée.

Les anomalies de ce type, qui sont très difficiles à détecter par la simple inspection des données, sont plus courantes que vous ne l’imaginez. Souvent, elles sont révélées par les plaintes des clients. Mais il est déjà trop tard : les utilisateurs affectés sont déjà passés à la concurrence !

À l’heure actuelle, nos algorithmes examinent le temps de chargement des pages, le temps de réponse aux demandes et le temps de réponse des dépendances.

Vous n’avez pas à définir de seuils ni à configurer des règles. Des algorithmes d’apprentissage automatique (« Machine Learning ») et d’exploration de données sont utilisés pour détecter les schémas anormaux.

Nous sommes impatients de recevoir vos commentaires. Faites-nous savoir en quoi la détection proactive vous aide, dans quelle mesure nous pouvons l’améliorer et quelles sont les fonctionnalités supplémentaires que vous souhaitez nous voir ajouter. Vous pouvez nous fournir vos commentaires via Envoyer un sourire/Envoyer un smiley mécontent dans le portail ou en nous envoyant un e-mail à AppInsightsML@microsoft.com.

## À propos de l’alerte proactive

* *Pourquoi ai-je reçu ce courrier électronique ?*
 * La détection proactive a analysé la télémétrie que votre application a envoyée à Application Insights et a détecté un problème de performances dans votre application. 
* *La notification signifie-t-elle obligatoirement que mon application rencontre un problème ?*
 * Non. Il s’agit simplement d’une suggestion pour vous amener à examiner un élément de plus près. 
* *Que dois-je faire ?*
 * [Consultez les données présentées](#responding-to-an-alert). Metrics Explorer permet de passer en revue l’historique des performances et d’obtenir des mesures supplémentaires. Utilisez la recherche pour filtrer des événements spécifiques qui vous aideront à identifier la cause racine. 
* *Mais alors, vous examinez mes données ?*
 * Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](app-insights-data-retention-privacy.md).


## Le processus de détection

* *Quels types d'anomalies sont détectées ?*
 * Des anomalies qui vous prendraient beaucoup de temps à vérifier. Par exemple, de faibles performances dans une combinaison spécifique de l'emplacement, de l'heure et de la plate-forme.
* *Analysez-vous toutes les données collectées par Application Insights ?*
 * Pas à l'heure actuelle. Actuellement, nous analysons le temps de réponse des demandes, le temps de réponse des dépendances et le temps de chargement des pages. L’analyse de mesures supplémentaires sera bientôt disponible. 
* *Puis-je créer mes propres règles de détection d’anomalies ?*
 * Pas encore. Mais vous pouvez :
 * [configurer des alertes](app-insights-alerts.md) qui vous indiquent qu'une métrique dépasse un seuil ;
 * [exporter la télémétrie](app-insights-export-telemetry.md) vers une [base de données](app-insights-code-sample-export-sql-stream-analytics.md), vers [PowerBI](app-insights-export-power-bi.md) ou d'[autres](app-insights-code-sample-export-telemetry-sql-database.md) outils, où vous pourrez les analyser vous-même.
* *À quelle fréquence sont réalisées les analyses ?*
 * Nous exécutons l’analyse tous les jours sur la télémétrie du jour précédent.
* **Cela remplace-t-il les [alertes de métrique](app-insights-alerts.md) ?
 * Non. Nous ne détectons pas tous les comportements que vous pouvez considérer comme étant anormaux.

## Comment examiner les problèmes soulevés par la détection proactive ?

Ouvrez le rapport des anomalies à partir de l'adresse e-mail ou de la liste des anomalies.

![](./media/app-insights-proactive-detection/03.png)


* **Quand** (When) montre l'heure à laquelle le problème a été détecté.
* **Quoi** (What) décrit :
 * le problème qui a été détecté ;
 * les caractéristiques du jeu d’événements qui a affiché le comportement du problème.
* Le tableau compare le jeu aux faibles performances avec le comportement moyen de tous les autres événements.

Cliquez sur les liens pour ouvrir Metric Explorer et effectuez une recherche dans les rapports appropriés qui sont filtrés sur l’heure et les propriétés du jeu dont l’exécution est lente.

Modifiez l’intervalle de temps et les filtres pour explorer la télémétrie.

## Comment améliorer les performances ?

Les réponses lentes et les échecs font partie des expériences les plus frustrantes pour les utilisateurs de site web, comme vous l’avez sans doute appris à vos dépens. Il est donc important de résoudre les problèmes.

### Tri

Tout d’abord, est-il important ? Si une page est toujours lente à charger, mais que seul 1 % des utilisateurs de votre site doivent la consulter, vous avez sans doute des choses plus importantes à faire. Cependant, si seul 1 % des utilisateurs ouvre cette page, et si cette dernière génère systématiquement des exceptions, il peut s’avérer judicieux de s’intéresser à la chose.

Utilisez la déclaration d’impact dans l’e-mail comme guide général, mais dites-vous bien que tout ne se résume pas à ça. Recueillez d’autres informations pour confirmer.

Examinez les paramètres du problème. Si le problème est lié à la géographie, configurez des [tests de disponibilité](app-insights-monitor-web-app-availability.md) incluant la région. Il se peut qu'il y ait un problème de réseau dans la zone.

### Diagnostiquer des chargements de page lents 

Où est le problème ? Le serveur est-il long à répondre, est-ce la page qui est très lente, ou le navigateur a-t-il du mal à l’afficher ?

Ouvrez le panneau métrique des navigateurs. L'[affichage segmenté du temps de chargement de la page de navigateur](app-insights-javascript.md#explore-your-data) montre le délai qui s'écoule.

* Si le **Temps d'envoi de demande** est élevé, le serveur répond lentement ou la demande contient une importante quantité de données. Examinez les [mesures de performances](app-insights-web-monitor-performance.md#metrics) pour étudier les temps de réponse. 
* Configurez le [suivi des dépendances](app-insights-dependencies.md) pour voir si cette lenteur est due à des services externes ou à votre base de données.
* Si la **réception de réponse** est prédominante, votre page et ses composants dépendants (JavaScript, CSS, images, etc., mais pas de données chargées de manière asynchrone) sont longs. Configurez un [test de disponibilité](app-insights-monitor-web-app-availability.md) et veillez à définir l'option de chargement des éléments dépendants. Lorsque vous obtenez des résultats, ouvrez les détails d’un résultat et développez-les pour afficher les temps de chargement de différents fichiers.
* Des **durées de traitement du client** prolongées suggèrent que l'exécution des scripts est lente. Si la raison n’est pas évidente, pensez à ajouter un code de minutage et à envoyer les heures dans les appels trackMetric.

### Améliorer les pages lentes

Il existe un site web plein de conseils sur l’amélioration des temps de chargement des réponses serveur et des pages, et nous n’essaierons pas de tous les répéter ici. Voici quelques conseils que vous connaissez sans doute déjà, simplement pour susciter votre réflexion :

* Chargement lent dus à de gros fichiers : charger des scripts et les autres éléments de façon asynchrone. Utiliser le regroupement de scripts. Divisiez la page principale en widgets chargeant leurs données séparément. N’envoyez pas d’ancien langage HTML pour les longs tableaux : utilisez un script pour demander les données JSON ou un autre format compact, puis renseignez le tableau sur place. Il existe de grandes structures pour aider à cela. (Elles aussi comportent des scripts volumineux, bien sûr.)
* Ralentir les dépendances de serveur : prendre en compte des emplacements géographiques de vos composants. Par exemple, si vous utilisez Azure, assurez-vous que le serveur web et la base de données se trouvent dans la même région. Les requêtes récupèrent-elles plus d’informations que nécessaire ? La mise en mémoire cache ou en lot peut-elle aider ?
* Problèmes de capacité : examinez les métriques de serveur des temps de réponse et le nombre de demandes. Si les temps de réponse présentent des pics disproportionnés en termes de nombre de requêtes, vos serveurs sont étirés. 


## E-mails de notification

* *Dois-je m’abonner à ce service pour pouvoir recevoir des notifications ?*
 * Non. Notre robot interroge régulièrement les données de tous les utilisateurs d’Application Insights et envoie des notifications s’il détecte des problèmes.
* *Puis-je me désabonner ou obtenir des notifications envoyées à mes collègues ?*
 * Cliquez sur le lien de désabonnement dans l'alerte ou l'e-mail. 
 
    Actuellement, ils sont envoyés aux personnes qui ont un [accès en écriture à la ressource Application Insights](app-insights-resources-roles-access-control.md).

    Vous pouvez également modifier les paramètres de la liste des destinataires dans le panneau Détection proactive.
* *Je ne souhaite pas recevoir tous ces messages.*
 * Ils sont limités à un par jour et concernent le problème le plus pertinent sur lequel nous n’avons pas encore établi de rapport. Vous n’obtiendrez pas plusieurs fois le même message.
* *Si je ne fais rien, vais-je recevoir un rappel ?*
 * Non, vous ne recevez qu’un message pour chaque problème. 
* *J’ai perdu le courrier électronique Où puis-je trouver les notifications dans le portail ?*
 * Dans la vue d'ensemble Application Insights de votre application, cliquez sur la mosaïque **Détection proactive**. Vous serez en mesure de retrouver toutes les notifications jusqu’à 7 jours dans le passé.


## Articles connexes

* [Détecter, trier et diagnostiquer](app-insights-detect-triage-diagnose.md)
* [Définir les alertes de mesures](app-insights-alerts.md)
* [Metrics Explorer](app-insights-metrics-explorer.md)
* [Navigateur de recherche](app-insights-diagnostic-search.md)
 

<!---HONumber=AcomDC_0121_2016-->