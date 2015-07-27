<properties
	pageTitle="Application Machine Learning : analyse de sentiments | Microsoft Azure"
	description="L’API Text Analytics est une suite de fonctions d’analyse de texte intégrée dans Microsoft Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré dans le cadre de différentes tâches, comme l’analyse de sentiments et l’extraction d’expressions clés."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="luisca"/>


# Application Machine Learning : service Text Analytics pour l'analyse de sentiments#
##Vue d'ensemble
L’API Text Analytics API est une suite de [services Web](https://datamarket.azure.com/dataset/amla/text-analytics) d’analyse de texte intégrée dans Microsoft Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré dans le cadre de différentes tâches, comme l’analyse de sentiments et l’extraction d’expressions clés. L’utilisation de cette API ne requiert aucune formation. Il vous suffit d’importer vos données de texte. Pour l’instant, nous ne prenons en charge que la langue anglaise. Cette API utilise des techniques avancées de traitement du langage naturel.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## Analyse de sentiments##
Cette API renvoie une valeur numérique de notation située entre 0 et 1. Les valeurs de notation proches de 1 indiquent un sentiment positif, tandis que les valeurs proches de 0 signalent un sentiment négatif. La valeur de notation du sentiment est générée via des techniques de classification. Les fonctionnalités d’entrée du classifieur incluent des services n-grams, des fonctionnalités générées à partir de balises morphosyntaxiques et des incorporations de mot.
 
## Extraction d’expressions clés##
L’API renvoie une liste de chaînes indiquant les principaux propos suggérés dans le texte en entrée. Nous utilisons des techniques fournies par la boîte à outils de traitement du langage naturel sophistiquée de Microsoft Office.

## Définition de l’API##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemple de demande**

Dans l’appel GET ci-dessous, nous demandons le sentiment de l’expression suivante *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

En-têtes :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous obtenez votre clé de compte [ici](https://datamarket.azure.com/account/keys).

**Exemple de réponse**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Exemple de demande**

Dans l’appel GET ci-dessous, nous demandons le sentiment des expressions clés dans le texte *Nous avons passé un formidable séjour dans cet hôtel, la décoration est exceptionnelle et le personnel très accueillant*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

En-têtes :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Vous obtenez votre clé de compte [ici](https://datamarket.azure.com/account/keys).


**Exemple de réponse**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Exemple de demande**

Dans l’appel POST ci-dessous, nous demandons les sentiments des expressions suivantes : Hello World, Hello Foo World, Hello My World dans le corps de la demande

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corps :

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


En-têtes :

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


Vous obtenez votre clé de compte [ici](https://datamarket.azure.com/account/keys).

**Exemple de réponse**

Dans la réponse ci-dessous, vous obtenez la liste de résultats associée à vos ID de texte :

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[] 
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Exemple de demande**

Dans l’appel POST ci-dessous, nous demandons la liste de sentiments des expressions clés dans les textes suivants :

*Nous avons passé un formidable séjour dans cet hôtel, la décoration est exceptionnelle et le personnel très accueillant*
 
*La conférence était exceptionnelle, avec des discussions très intéressantes*

*La circulation était horrible, le trajet vers l’aéroport a duré trois heures*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corps :

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

En-têtes :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Vous obtenez votre clé de compte [ici](https://datamarket.azure.com/account/keys).


**Exemple de réponse**

Dans la réponse ci-dessous, vous obtenez la liste des expressions clés associées à vos ID de texte :

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[ ]
	}

---

**Remarques concernant le traitement par lots**

Les ID d’entrée saisis dans le système sont les ID retournés par le système. Le service Web ne vérifie pas que les ID sont uniques. Il incombe à l’appelant d’en vérifier l’unicité.
 

<!---HONumber=July15_HO3-->