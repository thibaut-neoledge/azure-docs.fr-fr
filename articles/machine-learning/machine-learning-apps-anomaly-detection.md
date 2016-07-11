<properties 
	pageTitle="Application Machine Learning : service de détection des anomalies | Microsoft Azure" 
	description="L’API de détection des anomalies est un exemple d’API généré avec Microsoft Azure Machine Learning. Elle détecte des anomalies dans les données de séries chronologiques présentant des valeurs numériques qui sont réparties uniformément dans le temps." 
	services="machine-learning" 
	documentationCenter="" 
	authors="alokkirpal" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="06/29/2016" 
	ms.author="alokkirpal"/>


# Service de détection des anomalies Machine Learning#

##Vue d'ensemble

L’API de détection des anomalies est un exemple d’API généré avec Microsoft Azure Machine Learning. Elle détecte des anomalies dans les données de séries chronologiques présentant des valeurs numériques qui sont réparties uniformément dans le temps.

Cette API peut détecter les types suivants de schémas anormaux dans les données de séries chronologiques :

* **Tendances positives et négatives :** par exemple, pendant l’analyse de l’utilisation de la mémoire de l’infrastructure informatique, une tendance à la hausse peut être digne d’intérêt, car elle est susceptible d’être le signe d’une fuite de mémoire.

* **Changements de la plage de valeurs dynamiques :** par exemple, pendant l’analyse des exceptions déclenchées par un service cloud, tout changement de la plage de valeurs dynamiques peut indiquer une instabilité de l’intégrité du service.

* **Pics et creux :** par exemple, quand vous analysez le nombre d’échecs de connexion pour un service ou le nombre de validations dans un site de commerce électronique, les pics ou creux d’activité peuvent indiquer un comportement anormal.

Ces détecteurs Machine Learning effectuent le suivi de tels changements de valeurs au fil du temps, signalant les changements en continu au sein de leurs valeurs en tant que résultats d’anomalies. Ils ne nécessitent pas de réglage du seuil ad hoc et leurs notations peuvent être utilisées pour contrôler le taux de faux positifs. L’API de détection des anomalies est utile dans plusieurs scénarios, tels que la surveillance des services via le suivi des indicateurs de performance clés au fil du temps, la surveillance de l’utilisation via des mesures comme le nombre de recherches et le nombre de clics, la surveillance des performances via des compteurs comme la mémoire, les processeurs, le nombre de lectures de fichiers, etc. au fil du temps.

L’offre de détection des anomalies inclut des outils utiles pour bien commencer.
* [L’application web](http://anomalydetection-aml.azurewebsites.net/) vous aide à évaluer et à visualiser les résultats de l’exécution de l’API de détection des anomalies sur vos données.
* [L’exemple de code](http://adresultparser.codeplex.com/) montre comment accéder à l’API et analyser les résultats en C# par programmation.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Définition de l’API

Le service fournit des API REST sur HTTPS qui peuvent être utilisées de différentes façons, notamment une application web ou mobile, R, Python, Excel, etc. L’envoi des données de séries chronologiques à ce service se fait via un appel d’API REST ; celui-ci exécute une combinaison des trois types de détection d’anomalie décrits ci-dessus. Le service s’exécute sur la plateforme Microsoft Azure Machine Learning, qui s’adapte aux besoins de votre activité, en toute transparence, et répond à des contrats de niveau de service.

###En-têtes
Veillez à inclure les bons en-têtes dans votre requête, qui doit se présenter comme suit :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous trouverez votre clé de compte dans votre compte sur [Azure Data Market](https://datamarket.azure.com/account/keys).

###API Score

L’API Score est utilisée pour la détection des anomalies dans les données de séries chronologiques non saisonnières. L’API exécute un certain nombre de détecteurs d’anomalies sur les données et renvoie leurs résultats d’anomalies. La figure ci-dessous illustre un exemple d’anomalies détectables par l’API Score. Cette série chronologique inclut 2 changements de niveau distincts, ainsi que 3 pics. Les points rouges indiquent l’heure à laquelle le changement de niveau a été détecté, tandis que les points noirs signalent les pics détectés.

![API Score][1]
	
**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Exemple de corps de requête**

Dans la requête ci-dessous, nous envoyons à l’API une série chronologique (tronquée ici) avec des points de données allant du 01/03/2016 au 10/03/2016 et les paramètres des détecteurs de pics afin de détecter toute anomalie parmi ces points de données.

	{
	  "data": [
	    [ "9/21/2014 11:05:00 AM", "3" ],
	    [ "9/21/2014 11:10:00 AM", "9.09" ],
	    [ "9/21/2014 11:15:00 AM", "0" ]
	  ],
	  "params": {
	    "tspikedetector.sensitivity": "3",
	    "zspikedetector.sensitivity": "3",
	    "trenddetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "postprocess.tailRows": "2"
	  }
	}

La réponse à cette requête se présente ainsi :

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
	  "ADOutput":"{
	    "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
		"ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
		"Values":[
		  ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
		  ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
		]
	  }"
	}

###API ScoreWithSeasonality

L’API ScoreWithSeasonality est utilisée pour la détection des anomalies dans les séries chronologiques qui présentent des schémas saisonniers. Cette API est utile pour détecter les écarts dans les schémas saisonniers.

La figure suivante illustre un exemple d’anomalies détectées dans une série chronologique saisonnière. Cette série chronologique présente 1 pic (le 1er point noir), 2 creux (le 2e point noir et celui à la fin) et 1 changement de niveau (le point rouge). Notez que le creux au milieu de la série chronologique et le changement de niveau ne sont visibles qu’une fois les composantes saisonnières supprimées de la série.

![API Saisonnalité][2]

**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Exemple de corps de requête**

Dans la requête ci-dessous, nous envoyons à l’API une série chronologique (tronquée ici) avec des points de données allant du 01/03/2016 au 10/03/2016 et les paramètres afin de détecter toute anomalie parmi ces points de données.

	{
	  "data": [
	    [ "9/21/2014 11:10:00 AM", "9" ],
	    [ "9/21/2014 11:15:00 AM", "12" ],
	    [ "9/21/2014 11:20:00 AM", "10" ]
	  ],
	  "params": {
	    "preprocess.aggregationInterval": "0",
	    "preprocess.aggregationFunc": "mean",
	    "preprocess.replaceMissing": "lkv",
	    "postprocess.tailRows": "1",
	    "zspikedetector.sensitivity": "3",
	    "tspikedetector.sensitivity": "3",
	    "upleveldetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "trenddetector.sensitivity": "3.25",
	    "detectors.historywindow": "500",
	    "seasonality.enable": "true",
	    "seasonality.transform": "deseason",
	    "seasonality.numSeasonality": "1"
	  }
	}

La réponse à cette requête se présente ainsi :

	{
		"odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
		"ADOutput": "{
			"ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
		  	"ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
		  	"Values":[
		    	["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
		  	]
		}"
	}

###Détecteurs

L’API de détection des anomalies prend en charge les détecteurs de 3 grandes catégories. Le tableau suivant contient des informations détaillées sur les paramètres d’entrée spécifiques et les sorties de chaque détecteur.

|Catégorie de détecteurs|Détecteur|Description|Paramètres d’entrée|Sorties
|---|---|---|---|---|
|Détecteurs de pics|Détecteurs TSpike|Détecter les pics et les creux suivant la sensibilité définie|*tspikedetector.sensitivity :* prend une valeur entière comprise entre 1 et 10, par défaut : 3 ; plus la valeur est élevée, moins la sensibilité est importante|TSpike : valeurs binaires (1 si un pic/creux est détecté, 0 dans le cas contraire)|
|Détecteur ZSpike|Détecter les pics et les creux suivant la sensibilité définie|*zspikedetector.sensitivity :* prend une valeur entière comprise entre 1 et 10, par défaut : 3 ; plus la valeur est élevée, moins la sensibilité est importante|ZSpike : valeurs binaires (1 si un pic/creux est détecté, 0 dans le cas contraire)|
|Détecteur de tendances lentes|Détecteur de tendances lentes|Détecter les tendances positives lentes suivant la sensibilité définie|*trenddetector.sensitivity :* seuil pour le résultat du détecteur (par défaut : 3.25, une valeur comprise entre 3.25 et 5 est raisonnable ; plus la valeur est élevée, moins la sensibilité est importante)|tscore : nombre flottant représentant le résultat d’anomalies pour une tendance|
|Détecteurs de changements de niveau|Détecteur de changements de niveau unidirectionnels|Détecter les changements de niveau vers le haut suivant la sensibilité définie|Identiques à ceux du détecteur de tendances lentes|pscore : nombre flottant représentant le résultat d’anomalies pour un changement de niveau vers le haut|
|Détecteur de changements de niveau bidirectionnels|Détecter les changements de niveau vers le haut et vers le bas suivant la sensibilité définie|Identiques à ceux du détecteur de tendances lentes|rscore : nombre flottant représentant le résultat d’anomalies pour un changement de niveau vers le haut et vers le bas

###Paramètres

Le tableau ci-dessous contient des informations plus détaillées sur ces paramètres d’entrée :

|Paramètres d’entrée|Description|Paramètre par défaut|Type|Plage valide|Plage suggérée|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervalle d’agrégation en secondes pour l’agrégation de séries chronologiques d’entrée|0 (aucune agrégation n’est effectuée)|integer|0 : ignorer l’agrégation, > 0 autrement|5 minutes à 1 jour, dépend des séries chronologiques
|preprocess.aggregationFunc|Fonction utilisée pour agréger les données dans l’intervalle d’agrégation spécifié|mean|enumerated|mean, sum, length|N/A|
|preprocess.replaceMissing|Valeurs utilisées pour imputer les données manquantes|lkv (dernière valeur connue)|enumerated|zero, lkv, mean|N/A|
|detectors.historyWindow|Historique (en nombre de points de données) utilisé pour le calcul du résultat d’anomalies|500|integer|10-2000|Dépend des séries chronologiques|
|upleveldetector.sensitivity|Sensibilité du détecteur de changements de niveau vers le haut |3\.25|double|Aucun|3\.25-5 (plus la valeur est élevée, moins la sensibilité est importante)|
|bileveldetector.sensitivity|Sensibilité du détecteur de changements de niveau bidirectionnels |3\.25|double|Aucun|3\.25-5 (plus la valeur est élevée, moins la sensibilité est importante)|
|trenddetector.sensitivity|Sensibilité du détecteur de tendances positives |3\.25|double|Aucun|3\.25-5 (plus la valeur est élevée, moins la sensibilité est importante)|
|tspikedetector.sensitivity |Sensibilité du détecteur TSpike|3|integer|1-10|3-5 (plus la valeur est élevée, moins la sensibilité est importante)|
|zspikedetector.sensitivity |Sensibilité du détecteur ZSpike|3|integer|1-10 |3-5 (plus la valeur est élevée, moins la sensibilité est importante)|
|seasonality.enable|Exécution de l’analyse de saisonnalité|true|booléenne|true, false|Dépend des séries chronologiques|
|seasonality.numSeasonality |Nombre maximal de cycles périodiques à détecter|1|integer|1, 2|1-2|
|seasonality.transform |Suppression des composantes de tendances (et) saisonnières avant l’exécution de la détection des anomalies|deseason|enumerated|none, deseason, deseasontrend|N/A|
|postprocess.tailRows |Nombre de points de données récents à conserver dans les résultats de sortie|0|integer|0 (conserver tous les points de données) ou spécifier le nombre de points à conserver dans les résultats|N/A|

###Sortie
L’API exécute tous les détecteurs sur vos données de séries chronologiques et renvoie les résultats d’anomalies et les indicateurs de pics binaires pour chaque point dans le temps. Le tableau ci-dessous répertorie les sorties de l’API.

|Sorties|Description|
|---|---|
|Time|Horodatages issus des données brutes ou des données agrégées (et/ou) imputées si l’imputation des données agrégées (et/ou) manquantes est appliquée.|
|OriginalData|Valeurs issues des données brutes ou des données agrégées (et/ou) imputées si l’imputation des données agrégées (et/ou) manquantes est appliquée.|
|ProcessedData|Soit : <ul><li>Séries chronologiques sans composantes saisonnières en cas de détection d’une saisonnalité importante et de sélection de l’option deseason ;</li><li>Séries chronologiques sans composantes saisonnières et de tendances en cas de détection d’une saisonnalité importante et de sélection de l’option deseasontrend ;</li><li>Sinon, même chose que pour OriginalData.</li>|
|TSpike|Indicateur binaire signalant si un pic est détecté par le détecteur TSpike|
|ZSpike|Indicateur binaire signalant si un pic est détecté par le détecteur ZSpike|
|Pscore|Nombre flottant représentant le résultat d’anomalies pour un changement de niveau vers le haut|
|Palert|Valeur de 1/0 indiquant s’il y a une anomalie de changement de niveau vers le haut en fonction de la sensibilité d’entrée|
|RPScore|Nombre flottant représentant le résultat d’anomalies pour un changement de niveau bidirectionnel|
|RPAlert|Valeur de 1/0 indiquant s’il y a une anomalie de changement de niveau vers le haut en fonction de la sensibilité d’entrée|
|TScore|Nombre flottant représentant le résultat d’anomalies pour une tendance positive|
|TAlert|Valeur de 1/0 indiquant s’il y a une anomalie de tendance positive en fonction de la sensibilité d’entrée|


Cette sortie peut être analysée à l’aide d’un [analyseur simple](https://adresultparser.codeplex.com/) ; celui-ci propose un exemple de code qui montre comment se connecter à l’API et analyser la sortie. Les anomalies détectées peuvent être visualisées sur un tableau de bord et/ou transmises à des experts pour qu’ils appliquent les mesures correctives appropriées, ou intégrées à des systèmes de tickets.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

<!---HONumber=AcomDC_0629_2016-->