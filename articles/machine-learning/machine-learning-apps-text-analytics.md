<properties
	pageTitle="Application Machine Learning : service Text Analytics pour l'analyse de sentiments | Azure "
	description="L'API Text Analytics est une suite de fonctions d'analyse de texte intégrée dans Microsoft Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré dans le cadre de différentes tâches, comme l'analyse de sentiments et l'extraction des phrases clés."
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
	ms.date="01/27/2015"
	ms.author="luisca"/>


# Service Text Analytics de Machine Learning#
##Vue d'ensemble
L'API Text Analytics API est une suite de [services web d'analyse de texte]( https://datamarket.azure.com/dataset/amla/text-analytics) intégrée dans Microsoft Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré dans le cadre de différentes tâches, comme l'analyse de sentiments et l'extraction d'expressions clés. Aucune donnée d'apprentissage n'est nécessaire pour savoir comment utiliser cette API : il vous suffit de fournir des données Texte. Pour l'instant, nous ne prenons en charge que la langue anglaise. Cette API utilise des techniques avancées de traitement du langage naturel.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
 
## Analyse de sentiments##
Cette API renvoie une valeur numérique de notation située entre 0 et 1. Les valeurs de notation proches de 1 indiquent un sentiment positif, tandis que les valeurs proches de 0 signalent un sentiment négatif. La valeur de notation du sentiment est générée via des techniques de classification. Les fonctionnalités d'entrée du classifieur incluent des services n-grams, des fonctionnalités générées à partir de balises morphosyntaxiques et des incorporations de mot.
 
## Extraction d'expressions clés##
L'API renvoie une liste de chaînes indiquant les principaux propos suggérés dans le texte en entrée. Nous utilisons des techniques fournies par la boîte à outils de traitement du langage naturel sophistiquée de Microsoft Office.

## Définition de l'API##

###GetSentiment###

**URL**	

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemple de demande**

Dans l'appel GET ci-dessous, nous demandons le sentiment de l'expression suivante : *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

En-têtes :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

Vous obtenez votre clé de compte [ici]( https://datamarket.azure.com/account/keys). 

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

Dans l'appel GET ci-dessous, nous demandons le sentiment les expressions clés dans le texte suivant : *Nous avons passé un formidable séjour dans cet hôtel, la décoration est exceptionnelle et le personnel très accueillant*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

En-têtes :

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

Vous obtenez votre clé de compte [ici]( https://datamarket.azure.com/account/keys). 


**Exemple de réponse**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49--> 