<properties 
	pageTitle="Application Insights : détection proactive d’anomalies" 
	description="Application Insights réalise une analyse télémétrique approfondie de votre application et vous avertit des éventuels problèmes de performances." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="awills"/>

#  Application Insights : alertes proactives

*Application Insights est à l'état de version préliminaire.*


Application Insights réalise une analyse télémétrique approfondie de votre application et peut vous avertir des éventuels problèmes de performances. Vous lisez probablement cela parce que vous avez reçu l’une de nos alertes proactives par courrier électronique.

## À propos de l’alerte proactive

* *Pourquoi ai-je reçu cette alerte ?*
 * Application Insights analyse régulièrement vos données à l’aide de règles de reconnaissance de modèle. Il recherche les anomalies qui peuvent indiquer des problèmes de performances dans votre application.
* *La notification signifie-t-elle obligatoirement que mon application rencontre un problème ?*
 * Non. Il s’agit simplement d’une suggestion pour vous amener à examiner un élément de plus près. 
* *Que dois-je faire ?*
 * [Regardez les données présentées](#responding-to-an-alert) et évaluez si elles indiquent éventuellement un problème. Si ce n'est pas le cas, c'est parfait.
* *Mais alors, vous examinez mes données ?*
 * Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](app-insights-data-retention-privacy.md).


## Le processus de détection

* *Quels types d'anomalies sont détectées ?*
 * Des anomalies qui vous prendraient beaucoup de temps à vérifier. Par exemple, de faibles performances dans une combinaison spécifique de l'emplacement, de l'heure et de la plate-forme.
* *Puis-je créer mes propres règles de détection d’anomalies ?*
 * Pas encore. Mais vous pouvez :
 * [configurer des alertes](app-insights-alerts.md) qui vous indiquent lorsqu’une métrique dépasse un seuil.)
 * [Exporter la télémétrie](app-insights-export-telemetry.md) vers une [base de données](app-insights-code-sample-export-sql-stream-analytics.md) ou vers [PowerBI](app-insights-export-power-bi.md) ou [autres](app-insights-code-sample-export-telemetry-sql-database.md) outils, où vous pouvez les analyser vous-même.
* *À quelle fréquence sont réalisées les analyses ?*
 * Nous ne réalisons pas d’analyse de performances sur une ressource d’application dont la télémétrie est faible. Il est peu probable que vous obteniez des avertissements relatifs à vos sessions de débogage.


## Répondre à une alerte

Ouvrez le rapport des anomalies à partir de l'adresse e-mail ou de la liste des anomalies.

![](./media/app-insights-anomaly/02.png)

Remarque :

* La description
* La déclaration d'impact, qui indique le nombre ou la fréquence à laquelle les utilisateurs sont affectés.

Cliquez sur un graphique pour ouvrir un panneau plus détaillé.

Modifiez l’intervalle de temps et les filtres pour explorer la télémétrie.

## C’est très bien de recevoir ces alertes. Mais que puis-je faire pour améliorer les performances ?

Les réponses lentes et les échecs font partie des expériences les plus frustrantes pour les utilisateurs de site web, comme vous l’avez sans doute appris à vos dépens. Il est donc important de résoudre les problèmes.

### Tri

Tout d’abord, est-il important ? Si une page est toujours lente à charger, mais que seul 1 % des utilisateurs de votre site doivent la consulter, vous avez sans doute des choses plus importantes à faire. Cependant, si seul 1 % des utilisateurs ouvre cette page, et si cette dernière génère systématiquement des exceptions, il peut s’avérer judicieux de s’intéresser à la chose.

Utilisez la déclaration d’impact dans l’e-mail comme guide général, mais dites-vous bien que tout ne se résume pas à ça. Recueillez d’autres informations pour confirmer.

Examinez les paramètres du problème. S’il est lié à la géographie, configurez des [tests de disponibilité](app-insights-monitor-web-app-availability.md) incluant la région : il se peut qu’il y ait un problème de réseau dans la zone.

### Diagnostiquer des chargements de page lents 

Où est le problème ? Le serveur est-il long à répondre, est-ce la page qui est très lente, ou le navigateur a-t-il du mal à l’afficher ?

Ouvrez le panneau métrique des navigateurs. L’[affichage segmenté du temps de chargement de la page](app-insights-javascript.md#explore-your-data) montre où le temps passe.

* Si le **Temps d’envoi de demande** est élevé, le serveur répond lentement ou la demande est une contient une importante quantité de données. Examinez les [mesures de performances](app-insights-web-monitor-performance.md#metrics) pour étudier les temps de réponse. 
* Configurez le [suivi des dépendances](app-insights-dependencies.md) pour voir si cette lenteur est due à des services externes ou à votre base de données.
* Si la **réception de réponse** est prédominante, votre page et ses composants dépendants, JavaScript, CSS, images, etc. (mais pas de données chargées de manière asynchrone) sont longs. Configurez un [test de disponibilité](app-insights-monitor-web-app-availability.md), et veillez à définir l’option de chargement des éléments dépendants. Lorsque vous obtenez des résultats, ouvrez les détails d’un résultat et développez-les pour afficher les temps de chargement de différents fichiers.
* Des **temps de traitement client** prolongés suggèrent que l’exécution des scripts est lente. Si la raison n’est pas évidente, pensez à ajouter un code de minutage et à envoyer les heures dans les appels trackMetric.

### Améliorer les pages lentes

Il existe un site web plein de conseils sur l’amélioration des temps de chargement des réponses serveur et des pages, et nous n’essaierons pas de tous les répéter ici. Voici quelques conseils que vous connaissez sans doute déjà, simplement pour susciter votre réflexion :

* Chargement lent dus à de gros fichiers : charger des scripts et les autres éléments de façon asynchrone. Utiliser le regroupement de scripts. Divisiez la page principale en widgets chargeant leurs données séparément. N’envoyez pas d’ancien langage HTML pour les longs tableaux : utilisez un script pour demander les données JSON ou un autre format compact, puis renseignez le tableau sur place. Il existe de grandes structures pour aider à cela. (Elles aussi comportent des scripts volumineux, bien sûr.)
* Ralentir les dépendances de serveur : prendre en compte des emplacements géographiques de vos composants. Par exemple, si vous utilisez Azure, assurez-vous que le serveur web et la base de données se trouvent dans la même région. Les requêtes récupèrent-elles plus d’informations que nécessaire ? La mise en mémoire cache ou en lot peut-elle aider ?
* Problèmes de capacité : examinez les métriques de serveur des temps de réponse et le nombre de demandes. Si les temps de réponse présentent des pics disproportionnés en termes de nombre de requêtes, vos serveurs sont étirés. 


## E-mails de notification

* *Dois-je m’abonner à ce service pour pouvoir recevoir des notifications ?*
 * Non. Notre robot interroge régulièrement les données de tous les utilisateurs d’Application Insights et envoie des notifications s’il détecte des problèmes.
* *Puis-je me désabonner ou obtenir des notifications envoyées à mes collègues ?*
 * Cliquez sur le lien dans l'alerte ou l’e-mail. Ouvrez les paramètres des anomalies.
 
    ![](./media/app-insights-anomaly/01.png)

    Actuellement, ils sont envoyés aux personnes qui ont un [accès en écriture à la ressource Application Insights](app-insights-resources-roles-access-control.md).
* *Je ne souhaite pas recevoir tous ces messages.*
 * Ceux-ci sont limités à trois par jour. Vous n’obtiendrez pas plusieurs fois le même message.
* *Si je ne fais rien, vais-je recevoir un rappel ?*
 * Non, vous ne recevez qu’un message pour chaque problème.
* *J’ai perdu le courrier électronique Où puis-je trouver les notifications dans le portail ?*
 * Dans la vue d’ensemble Application Insights de votre application, cliquez sur la vignette **Anomalies**. 






 

<!---HONumber=Nov15_HO2-->