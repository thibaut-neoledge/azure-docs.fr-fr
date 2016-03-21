<properties 
	pageTitle="Application Machine Learning : service de détection des anomalies | Microsoft Azure" 
	description="L’API de détection des anomalies est un exemple d’API généré avec Microsoft Azure Machine Learning. Elle détecte des anomalies dans les données de séries chronologiques présentant des valeurs numériques qui sont réparties uniformément dans le temps." 
	services="machine-learning" 
	documentationCenter="" 
	authors="pingf" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="03/07/2016" 
	ms.author="pingf"/>


# Service de détection des anomalies Machine Learning#

##Vue d'ensemble

L’API de détection des anomalies est un exemple d’API généré avec Microsoft Azure Machine Learning. Elle détecte des anomalies dans les données de séries chronologiques présentant des valeurs numériques qui sont réparties uniformément dans le temps.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ce service de détection des anomalies peut identifier les types d’anomalies suivants dans les données de séries chronologiques :

1. Tendances positives et négatives : pendant l’analyse de l’utilisation de la mémoire de l’infrastructure informatique, par exemple, une tendance à la hausse est signe d’une fuite de mémoire.

2. Augmentation de la plage de valeurs dynamiques : par exemple, pendant l’analyse des exceptions déclenchées par un service, toute augmentation de la plage de valeurs dynamiques peut indiquer une instabilité de l’intégrité du service.

3. Pics et creux : par exemple, quand vous analysez le nombre d’échecs de connexion à un service ou le nombre de validations dans un site de commerce électronique, les pics ou creux d’activité peuvent indiquer un comportement anormal.


Ces détecteurs effectuent le suivi des changements de valeurs au fil du temps, signalant les changements en continu au sein de leurs valeurs. Ils ne nécessitent pas de réglage du seuil ad hoc et leurs notations peuvent être utilisées pour contrôler le taux de faux positifs. L’API de détection des anomalies est utile dans plusieurs scénarios, tels que la surveillance des services via le suivi des indicateurs de performance clés au fil du temps, les mesures d’utilisation comme le nombre de recherches et le nombre de clics, les compteurs de performance comme la mémoire, les processeurs, le nombre de lectures de fichiers, etc. au fil du temps.

##Définition de l’API

Le service fournit des API REST sur HTTPS qui peuvent être utilisées de différentes façons, notamment une application web ou mobile, R, Python, Excel, etc. Nous avons une [application web Azure](http://anomalydetection-aml.azurewebsites.net/) qui permet d’exécuter le service web de détection d’anomalies sur vos données et de visualiser les résultats.

Vous pouvez également envoyer vos données de séries chronologiques à ce service via un appel d’API REST ; celui-ci exécute une combinaison des trois types de détection d’anomalie décrits ci-dessus. Le service s’exécute sur la plate-forme Microsoft Azure Machine Learning, qui s’adapte aux besoins de votre activité, en toute transparence, et répond à des contrats SLA de 99,95 %.

La figure ci-dessous illustre un exemple d’anomalie détecté dans une série chronologique à l’aide de l’infrastructure ci-dessus. La série chronologique inclut 2 changements de niveau distincts, ainsi que 3 pics. Les points rouges indiquent l’heure à laquelle le changement de niveau a été détecté, tandis que les flèches rouges vers le haut signalent les pics détectés.


![][1]

##Entrée

L’API prend deux paramètres d’entrée :

1. « data » représente la série chronologie d’entrée, au format t1=v1;t2=v2;... 

 
	Exemples de données :
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. « param » est défini sur « SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3 », ce qui contrôle le niveau de sensibilité des détecteurs de pics. Des valeurs supérieures permettent d’intercepter des pics plus élevés, et vice versa.

	Exemple d’URL avec les paramètres en entrée mentionnés ci-dessus :

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###Sortie###

L’API exécute ces détecteurs sur vos données de séries chronologiques et renvoie les résultats d’anomalies à chaque point dans le temps. Cette sortie peut être analysée à l’aide d’un analyseur simple, comme celui du site suivant : [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/). Voici un exemple de code, qui montre comment se connecter à l’API et analyser la sortie. Les alertes générées peuvent être visualisées sur un tableau de bord et/ou transmises à des experts humains, qui peuvent prendre des mesures correctives.

Exemple de sortie pour l’entrée ci-dessus :

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

ce qui correspond à une représentation du tableau suivant :

Time|Données|Tspike|Zspike|Valeurs martingales|Indicateur d’alerte|Valeurs martingales (2)|Indicateur d’alerte (2)
---|---|---|---|---|---|---|---
21/9/2014 11:05|3|0|0|-0,687952591|0|-0,687952591|0|   
21/9/2014 11:10|9,09|0|0|-1,070304977|0|-0,884548154|0|    
21/9/2014 11:15|0|0|0|-1,051862374|0|-1,1738002814|0|   
   

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=AcomDC_0309_2016-->