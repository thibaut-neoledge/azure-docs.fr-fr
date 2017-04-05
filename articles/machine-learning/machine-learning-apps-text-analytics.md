---
title: "API Machine Learning : analyses de texte | Microsoft Docs"
description: "Les API Machine Learning Text Analytics de Microsoft peuvent être utilisées pour analyser le texte non structuré dans le cadre de l’analyse de sentiments, l’extraction d’expressions clés, la détection de la langue et la détection de la rubrique."
services: machine-learning
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: 5b60694e-5521-4e4d-bf6a-1a92fdf94b65
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: onewth
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: ../cognitive-services/cognitive-services-text-analytics-quick-start
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f4389705a81b531bd706cbabc0b4c3b171febd5f
ms.lasthandoff: 11/17/2016


---
# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>API Machine Learning : analyse de texte pour déterminer les sentiments, l’extraction d’expressions clés, la détection de la langue et la détection de la rubrique
> [!NOTE]
> Ce guide concerne la version 1 de l’API. Pour la version 2, [**consultez ce document**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). La version 2 est désormais la version par défaut de cette API.
> 
> 

## <a name="overview"></a>Vue d'ensemble
L’API Text Analytics est une suite de [services web](https://datamarket.azure.com/dataset/amla/text-analytics) d’analyse de texte intégrée dans Azure Machine Learning. Cette API peut être utilisée pour analyser le texte non structuré dans le cadre de différentes tâches, comme l’analyse de sentiments, l’extraction d’expressions clés, la détection de la langue et la détection de la rubrique. L’utilisation de cette API ne requiert aucune formation. Il vous suffit d’importer vos données de texte. Cette API utilise des techniques avancées de traitement du langage naturel pour effectuer des prédictions de pointe.

Vous pouvez voir une démonstration de l’analyse de texte sur notre [site de démo](https://text-analytics-demo.azurewebsites.net/), où vous trouverez également des [exemples](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) d’implémentation de l’analyse de texte dans C# et Python.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

- - -
## <a name="sentiment-analysis"></a>analyse de sentiments
Cette API renvoie une valeur numérique de notation située entre 0 et 1. Les valeurs de notation proches de 1 indiquent un sentiment positif, tandis que les valeurs proches de 0 signalent un sentiment négatif. La valeur de notation du sentiment est générée via des techniques de classification. Les fonctionnalités d’entrée du classifieur incluent des services n-grams, des fonctionnalités générées à partir de balises morphosyntaxiques et des incorporations de mot. Actuellement, l’anglais est la seule langue prise en charge.

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés
L’API renvoie une liste de chaînes indiquant les principaux propos suggérés dans le texte en entrée. Nous utilisons des techniques fournies par la boîte à outils de traitement du langage naturel sophistiquée de Microsoft Office. Actuellement, l’anglais est la seule langue prise en charge.

## <a name="language-detection"></a>Détection de la langue
L’API indique la langue détectée et un score entre 0 et 1. Un score proche de 1 indique une certitude à 100 % que la langue identifiée est la bonne. Au total, 120 langues sont prises en charge.

## <a name="topic-detection"></a>Détection de la rubrique
Il s’agit d’une API lancée récemment, qui renvoie les premières rubriques détectées pour une liste d’enregistrements texte soumis. Une rubrique est identifiée par une expression clé, représentée par un ou plusieurs mots associés. Cette API nécessite l’envoi d’au moins 100 enregistrements texte, mais elle est conçu pour détecter des rubriques parmi des centaines de milliers d'enregistrements. Notez que cette API facture 1 transaction par enregistrement texte soumis. L'API est conçue pour fonctionner correctement avec un texte court écrit par un humain, par exemple des évaluations et des commentaires d’utilisateurs.

- - -
## <a name="api-definition"></a>Définition de l’API
### <a name="headers"></a>En-têtes
Veillez à inclure les bons en-têtes dans votre requête, qui doit se présenter comme suit :

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous trouverez votre clé de compte dans votre compte sur [Azure Data Market](https://datamarket.azure.com/account/keys). Actuellement, seul JSON est accepté pour les formats d’entrée et de sortie. XML n’est pas pris en charge.

- - -
## <a name="single-response-apis"></a>API à réponse unique
### <a name="getsentiment"></a>GetSentiment
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

- - -
### <a name="getkeyphrases"></a>GetKeyPhrases
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

- - -
### <a name="getlanguage"></a>GetLanguage
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

- - -
## <a name="batch-apis"></a>API Batch
Le service d’analyse de texte vous permet d’effectuer des extractions de sentiments et d’expressions clés en mode Batch. Notez que chacun des enregistrements notés compte comme une transaction unique. Par exemple, si vous demandez le sentiment pour 1 000 enregistrements en un seul appel, 1 000 transactions sont déduites.

Remarque : les ID saisis dans le système sont les ID retournés par le système. Le service web ne vérifie pas que ces ID sont uniques. Il incombe à l’appelant d’en vérifier l’unicité. 

### <a name="getsentimentbatch"></a>GetSentimentBatch
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
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


- - -
### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch
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

    { "odata.metadata":"<url>",
         "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

- - -
### <a name="getlanguagebatch"></a>GetLanguageBatch

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

- - -
## <a name="topic-detection-apis"></a>API de détection de la rubrique
Il s’agit d’une API lancée récemment, qui renvoie les premières rubriques détectées pour une liste d’enregistrements texte soumis. Une rubrique est identifiée par une expression clé, représentée par un ou plusieurs mots associés. Notez que cette API facture 1 transaction par enregistrement texte soumis.

Cette API nécessite l’envoi d’au moins 100 enregistrements texte, mais elle est conçu pour détecter des rubriques parmi des centaines de milliers d'enregistrements.

### <a name="topics--submit-job"></a>Rubriques – Envoyer le travail
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Exemple de demande**

Dans l’appel POST ci-dessous, nous demandons des rubriques pour un ensemble de 100 articles, où les premier et dernier articles d’entrée sont affichés, et deux StopPhrases sont inclus.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corps de la requête :

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Dans la réponse ci-dessous, vous obtenez le JobId du travail soumis :

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Une liste de mots uniques ou de phrases qui ne doivent pas être renvoyés comme des rubriques. Permet de filtrer des rubriques très génériques. Par exemple, dans un jeu de données sur les évaluations d’un hôtel, « hotel » et « hostel » peuvent être des StopPhrases.  

### <a name="topics--poll-for-job-results"></a>Rubriques – Recherche des résultats d’un travail
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Exemple de demande**

Passez le JobId renvoyé à partir de l'étape « Envoyer le travail » pour extraire les résultats. Nous vous recommandons d'appeler ce point de terminaison toutes les minutes jusqu'à ce que la réponse affiche Status=’Complete’. Le travail prendra environ 10 minutes, ou plus pour les travaux impliquant des milliers d'enregistrements.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Pendant le traitement, la réponse se présente comme suit :

    {
        "odata.metadata":"<url>",
        "Status":"Running",
         "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


L'API renvoie un résultat au format JSON suivant :

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
          ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Les propriétés de chaque partie de la réponse sont les suivantes :

**Propriétés de TopicInfo**

| Clé | Description |
|:--- |:--- |
| TopicId |Identificateur unique de chaque rubrique. |
| Score |Nombre d’enregistrements affectés à une rubrique. |
| KeyPhrase |Mot ou phrase résumant la rubrique. Peut contenir un ou plusieurs mots. |

**Propriétés de TopicAssignment**

| Clé | Description |
|:--- |:--- |
| ID |Identificateur de l'enregistrement. Équivaut à l'ID inclus dans l'entrée. |
| TopicId |ID de rubrique auquel l’enregistrement a été affecté. |
| Distance |Niveau de confiance que l’enregistrement appartient à la rubrique. Plus la distance est proche de zéro, plus le niveau de confiance est élevé. |


