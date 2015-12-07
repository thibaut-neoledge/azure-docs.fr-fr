<properties
	pageTitle="API Machine Learning : analyses de texte | Microsoft Azure"
	description="API d’analyse de texte fournies par Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré pour la déduction de sentiments, l’extraction d’expressions clés et la détection de la langue."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="onewth"/>


# API Machine Learning : analyse de texte pour déterminer les sentiments, extraction d’expressions clés et détection de la langue

## Vue d'ensemble

L’API Text Analytics est une suite de [services web](https://datamarket.azure.com/dataset/amla/text-analytics) d’analyse de texte intégrée dans Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré dans le cadre de différentes tâches, comme l’analyse de sentiments, l’extraction d’expressions clés et la détection de la langue. L’utilisation de cette API ne requiert aucune formation. Il vous suffit d’importer vos données de texte. Cette API utilise des techniques avancées de traitement du langage naturel pour effectuer des prédictions de pointe.

Vous pouvez voir une démonstration de l’analyse de texte sur notre [site de démo](https://text-analytics-demo.azurewebsites.net/), où vous trouverez également des [exemples](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) d’implémentation de l’analyse de texte dans C# et Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Analyse de sentiments

Cette API renvoie une valeur numérique de notation située entre 0 et 1. Les valeurs de notation proches de 1 indiquent un sentiment positif, tandis que les valeurs proches de 0 signalent un sentiment négatif. La valeur de notation du sentiment est générée via des techniques de classification. Les fonctionnalités d’entrée du classifieur incluent des services n-grams, des fonctionnalités générées à partir de balises morphosyntaxiques et des incorporations de mot. Actuellement, l’anglais est la seule langue prise en charge.
 
## Extraction d’expressions clés

L’API renvoie une liste de chaînes indiquant les principaux propos suggérés dans le texte en entrée. Nous utilisons des techniques fournies par la boîte à outils de traitement du langage naturel sophistiquée de Microsoft Office. Actuellement, l’anglais est la seule langue prise en charge.

## Détection de la langue

L’API indique la langue détectée et un score entre 0 et 1. Un score proche de 1 indique une certitude à 100 % que la langue identifiée est la bonne. Au total, 120 langues sont prises en charge.

---

## Définition de l’API

### En-têtes

Veillez à inclure les bons en-têtes dans votre requête, qui doit se présenter comme suit :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous trouverez votre clé de compte dans votre compte sur [Azure Data Market](https://datamarket.azure.com/account/keys).

---

## API à réponse unique

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemple de demande**

Dans l’appel ci-dessous, nous demandons l’analyse du sentiment de l’expression « Hello World » :

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Cette requête renverra une réponse du type :

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Exemple de demande**

Dans l’appel ci-dessous, nous demandons les expressions clés dans le texte « Nous avons passé un formidable séjour dans cet hôtel, la décoration est exceptionnelle et le personnel très accueillant » :

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Cette requête renverra une réponse du type :

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Exemple de demande**

Dans l’appel GET ci-dessous, nous demandons le sentiment des expressions clés dans le texte *Hello World*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

Cette requête renverra une réponse du type :

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**Paramètres facultatifs**

`NumberOfLanguagesToDetect` est un paramètre facultatif. La valeur par défaut est 1.

---

## API Batch

Le service d’analyse de texte vous permet d’effectuer des extractions de sentiments et d’expressions clés en mode Batch. Notez que chacun des enregistrements notés compte comme une transaction unique. Par exemple, si vous demandez le sentiment pour 1 000 enregistrements en un seul appel, 1 000 transactions sont déduites.

Remarque : les ID saisis dans le système sont les ID retournés par le système. Le service web ne vérifie pas que ces ID sont uniques. Il incombe à l’appelant d’en vérifier l’unicité.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Exemple de demande**

Dans l’appel POST ci-dessous, nous demandons les sentiments des expressions « Hello World », « Hello Foo World » et « Hello My World » dans le corps de la requête :

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corps de la requête :

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	    {"Id":"2","Text":"hello foo world"},
    	    {"Id":"3","Text":"hello my world"},
	]}

Dans la réponse ci-dessous, vous obtenez la liste de résultats associée à vos ID de texte :

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Exemple de demande**

Dans cet exemple, nous demandons la liste de sentiments des expressions clés dans les textes suivants :

* « Nous avons passé un formidable séjour dans cet hôtel, la décoration est exceptionnelle et le personnel très accueillant »
* « La conférence était exceptionnelle, avec des discussions très intéressantes »
* « La circulation était horrible, le trajet vers l’aéroport a duré trois heures »

Cette requête est effectuée comme un appel POST au point de terminaison :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corps de la requête :

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Dans la réponse ci-dessous, vous obtenez la liste des expressions clés associées à vos ID de texte :

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

Dans l’appel POST ci-dessous, nous demandons à détecter la langue pour deux entrées de texte :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Corps de la requête :

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Cet exemple renvoie la réponse suivante, où l’anglais est détecté dans la première entrée et le français dans la seconde entrée :

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

<!---HONumber=AcomDC_1125_2015-->