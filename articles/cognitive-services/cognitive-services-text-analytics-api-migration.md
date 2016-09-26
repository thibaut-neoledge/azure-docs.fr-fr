<properties
	pageTitle="Mise à niveau vers la Version 2 de l’API Analyse de texte | Microsoft Azure"
	description="Analyse de texte Azure Machine Learning - Mise à niveau vers la Version 2"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="onewth"/>

# Mise à niveau vers la Version 2 de l’API Analyse de texte #

Ce guide vous accompagne à travers le processus de mise à niveau de votre code, de l’utilisation de la [première version de l’API](../machine-learning/machine-learning-apps-text-analytics.md) à l’utilisation de la seconde version.

Si vous n’avez pas utilisé l’API et que vous souhaitez en savoir plus, vous pouvez **[en savoir plus sur l’API ici](//go.microsoft.com/fwlink/?LinkID=759711)** ou **[suivre le guide de démarrage rapide](//go.microsoft.com/fwlink/?LinkID=760860)**. Pour la référence technique, reportez-vous à la **[définition d’API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### Partie 1. Obtenir une nouvelle clé ###

Tout d’abord, vous devez obtenir une nouvelle clé d’API à partir du **portail Azure** :

1. Accédez au service d’analyse de texte via la [galerie Cortana Intelligence](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Vous y trouverez également des liens vers la documentation et des exemples de code.

1. Cliquez sur **S’inscrire**. Ce lien vous dirigera vers le portail de gestion Azure, où vous pouvez vous inscrire au service.

1. Sélectionnez un plan. Vous pouvez sélectionner le **niveau gratuit pour 5 000 transactions/mois**. Étant donné qu’il s’agit d’un plan gratuit, son utilisation ne vous sera pas facturée. Vous devrez vous connecter à votre abonnement Azure.

1. Une fois inscrit à l’analyse de texte, vous recevrez une **clé d’API**. Copiez cette clé, car vous en aurez besoin lors de l’utilisation des services d’API.

### Partie 2. Mettre à jour les en-têtes ###

Mettez à jour les valeurs d’en-tête envoyées comme illustré ci-dessous. Notez que la clé de compte n’est plus codée.

**Version 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Version 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### Partie 3. Mettre à jour l’URL de base ###

**Version 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Version 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### Partie 4a. Mettre à jour les formats des sentiments, expressions clés et langues ###

#### Points de terminaison ####

Les points de terminaison GET étant désormais déconseillés, toutes les entrées doivent être envoyées en tant que requêtes POST. Mettez à jour les points de terminaison vers ceux indiqués ci-dessous.

| |Point de terminaison unique de la version 1|Point de terminaison de lot de la version 1|Point de terminaison de la version 2|
|---|---|---|---|
|Type d’appel|GET|POST|POST|
|Sentiments|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Expressions clés|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Langues|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### Formats d’entrée ####

Notez que seul le format POST est désormais accepté. Vous devez donc reformater toute entrée qui utilisait les points de terminaison de document uniques en conséquence. Les entrées ne sont pas sensibles à la casse.

**Version 1 (lot)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### Sortie à partir de sentiments ####

**Version 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### Sortie à partir d’expressions clés ####

**Version 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### Sortie à partir de langues ####


**Version 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### Partie 4b. Mettre à jour les formats pour les rubriques ###

#### Points de terminaison ####

| |Point de terminaison de la version 1 | Point de terminaison de la version 2|
|---|---|---|
|Envoyer pour la détection de rubrique (POST)|```StartTopicDetection```|```topics```|
|Extraire les résultats de rubrique (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### Formats d’entrée ####

**Version 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### Résultats de l’envoi ####

**Version 1 (POST)**

Auparavant, une fois la tâche terminée, vous receviez la sortie JSON suivante, où jobId était ajouté à une URL pour récupérer la sortie.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Version 2 (POST)**

La réponse comporte désormais une valeur d’en-tête comme la suivante, où `operation-location` est utilisé comme point de terminaison à interroger pour les résultats :

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### Operation results ####

**Version 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2 (GET)**

Comme auparavant, **interrogez régulièrement la sortie** (l’intervalle suggéré est toutes les minutes) jusqu’à ce qu’elle soit renvoyée.

Une fois que l’API de rubriques a terminé, l’état `succeeded` est renvoyé. Les résultats de sortie au format suivant seront alors inclus :

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### Partie 5. Faites le test ! ###

Vous devriez être fin prêt ! Testez votre code avec un petit exemple pour vous assurer que vous pouvez traiter vos données.

<!---HONumber=AcomDC_0914_2016-->