<properties 
	pageTitle="Application Analytics, outil de recherche puissant pour Application Insights" 
	description="Présentation d’Application Analytics, l’outil de recherche puissant pour Application Insights." 
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
	ms.date="03/11/2016" 
	ms.author="awills"/>




# Application Insights Analytics : présentation du langage


[Application Insights Analytics](app-analytics.md) est un moteur de requête puissant pour vos données de télémétrie [Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Application Insights (AIQL).

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Une requête AIQL classique est une table *source* suivie d’une série d’*opérateurs* séparés par des `|`.

Par exemple, essayons de découvrir à quelle heure les citoyens de Hyderabad accèdent à notre application web. Et parallèlement, découvrons les codes de résultats qui sont retournés à leurs requêtes HTTP.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Nous comptons des adresses IP client distinctes, en les regroupant par heure de la journée sur les 30 derniers jours.

Affichons les résultats avec la présentation graphique à barres, en choisissant d’empiler les résultats issus de codes de réponse différents :

![Sélectionnez le graphique à barres, les axes x et y, puis la segmentation](./media/app-analytics/020.png)

Il semblerait que notre application soit plus populaire lors de la pause déjeuner et à l’heure du coucher à Hyderabad. (Et nous devrions examiner ces 500 codes).


Il existe également des opérations statistiques puissantes :

![](./media/app-analytics/025.png)


Le langage possède de nombreuses fonctionnalités attrayantes :

* [Filtrer](app-analytics-queries.md) vos données de télémétrie d’application brutes sur tous les champs, y compris les propriétés et mesures personnalisées.
* [Joindre](app-analytics-queries.md#join-operator) plusieurs tables ; mettez en corrélation les demandes avec les affichages de page, les appels de dépendance, les exceptions et les suivis du journal.
* [Agrégations](app-analytics-aggregations.md) statistiques puissantes.
* Toutes aussi puissantes que SQL, mais beaucoup plus facile pour les requêtes complexes : au lieu d’imbriquer des instructions, vous dirigez les données d’une opération élémentaire à l’autre.
* Visualisations immédiates et puissantes.



>[AZURE.NOTE] Nous vous recommandons de commencer par la [visite guidée du langage](app-analytics-tour.md).


## Connectez-vous à vos données Application Insights


Ouvrez Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application dans Application Insights :

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->