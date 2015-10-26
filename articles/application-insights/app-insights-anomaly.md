<properties 
	pageTitle="Application Insights : détection proactive d’anomalies" 
	description="Application Insights réalise une analyse télémétrique approfondie de votre application et vous avertit des éventuels problèmes de performances." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="awills"/>

#  Application Insights : détection proactive d’anomalies

*Application Insights est à l'état de version préliminaire.*


Application Insights réalise une analyse télémétrique approfondie de votre application et peut vous avertir des éventuels problèmes de performances. Vous lisez probablement cela parce que vous avez reçu l’une de nos alertes d’anomalie par e-mail.

## À propos des alertes d'anomalies

* *Pourquoi ai-je reçu cette alerte ?*
 * Application Insights analyse régulièrement vos données avec des règles de reconnaissance de modèle. Il recherche les anomalies qui peuvent indiquer des problèmes de performances dans votre application.
* *La notification signifie-t-elle obligatoirement que mon application rencontre un problème ?*
 * Non. Il s’agit simplement d’une suggestion pour vous amener à examiner un élément de plus près. 
* *Que dois-je faire ?*
 * [Regardez les données présentées](#responding-to-an-alert) et évaluez si elles indiquent éventuellement un problème. Si ce n'est pas le cas, c'est parfait.
* *Mais alors, examinez-vous mes données ?*
 * Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](app-insights-data-retention-privacy.md).


## Le processus de détection

* *Quels types d'anomalies sont détectées ?*
 * Des anomalies qui vous prendraient beaucoup de temps à vérifier. Par exemple, de faibles performances dans une combinaison spécifique de l'emplacement, de l'heure et de la plate-forme.
* *Puis-je créer mes propres règles de détection d’anomalies ?*
 * Pas pour ce type d'analyse approfondie. (Mais vous pouvez [configurer des alertes](app-insights-alerts.md) qui vous indiquent lorsqu’un métrique dépasse un seuil.)
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
* *J’ai perdu l’e-mail. Où puis-je trouver les notifications dans le portail ?*
 * Dans la vue d’ensemble Application Insights de votre application, cliquez sur la vignette **Anomalies**. 






 

<!---HONumber=Oct15_HO3-->