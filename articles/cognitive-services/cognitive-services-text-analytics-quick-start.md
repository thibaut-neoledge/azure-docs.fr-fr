<properties
	pageTitle="Guide de démarrage rapide : API Machine Learning Text Analytics | Microsoft Azure"
	description="Azure Machine Learning Text Analytics - Guide de démarrage rapide"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="onewth"/>

# Prise en main des API Text Analytics pour détecter le sentiment, les expressions clés et la langue

<a name="HOLTop"></a>

Ce document décrit comment intégrer votre service ou application pour utiliser les [API Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711). Ces API vous permettent de détecter le sentiment, les expressions clés, les rubriques et le langage de votre texte. [Cliquez ici pour voir une démonstration interactive de l’expérience.](//go.microsoft.com/fwlink/?LinkID=759712)

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour obtenir des informations techniques sur les API.

Ce guide concerne la version 2 des API. Pour plus d’informations sur la version 1 des API, [consultez ce document](../machine-learning/machine-learning-apps-text-analytics.md).

À la fin de ce didacticiel, vous serez en mesure de détecter par programmation les informations suivantes :

- **Sentiment** : Le texte véhicule-t-il un sentiment positif ou négatif ?

- **Expressions clés** : Quels sont les sujets abordés dans un article donné ?

- **Rubriques** : Quels sont les sujets abordés dans un ensemble d’articles ?

- **Langue** : Dans quelle langue le texte est-il écrit ?

Notez que cette API facture 1 transaction par enregistrement texte soumis.

<a name="Overview"></a>
## Présentation générale ##

Ce document est un guide pas à pas. Notre objectif est de vous guider à travers les étapes nécessaires pour effectuer l’apprentissage d’un modèle et de vous indiquer les ressources qui vous permettront d’implémenter ce modèle dans votre environnement de production. Cet exercice prend environ 30 minutes.

Pour effectuer les tâches ci-dessous, vous devez utiliser un éditeur et appeler les points de terminaison RESTful dans la langue de votre choix.

C’est parti !

## Tâche 1 : Inscription aux API Text Analytics ####

Au cours de cette tâche, vous allez vous inscrire au service Text Analytics.

1. Accédez à **Cognitive Services** dans le [portail Azure](//go.microsoft.com/fwlink/?LinkId=761108) et assurez-vous que le type d’API **Text Analytics** est sélectionné.

1. Sélectionnez un plan. Vous pouvez sélectionner le **niveau gratuit pour 5 000 transactions/mois**. Étant donné qu’il s’agit d’un plan gratuit, son utilisation ne vous sera pas facturée. Vous devrez vous connecter à votre abonnement Azure.

1. Renseignez les autres champs et créez votre compte.

1. Une fois que vous êtes inscrit au service Text Analytics, recherchez votre **clé API**. Notez la clé primaire, car vous en aurez besoin pour utiliser les services d’API.


## Tâche 2 : Détecter le sentiment, les expressions clés et les langues ####

Vous pouvez facilement détecter le sentiment, les expressions clés et les langues de votre texte. Vous obtenez par programmation les mêmes résultats que ceux présentés dans la [démonstration de l’expérience](//go.microsoft.com/fwlink/?LinkID=759712).

>[AZURE.TIP] Pour l’analyse de sentiment, nous vous recommandons de fractionner le texte en phrases. Cela vous permettra généralement d’obtenir des prédictions de sentiment plus précises.

Notez que les langues prises en charge sont les suivantes :

| Fonctionnalité | Codes des langues prises en charge |
|:-----|:----|
| Sentiments | `en` (Anglais), `es` (Espagnol), `fr` (Français), `pt` (Portugais) |
| Expressions clés | `en` (Anglais), `es` (Espagnol), `de` (Allemand), `ja` (Japonais) |


1. Définissez les en-têtes comme ci-dessous. Notez que JSON est actuellement le seul format d’entrée accepté pour les API. XML n’est pas pris en charge.

		Ocp-Apim-Subscription-Key: <your API key>
		Content-Type: application/json
		Accept: application/json

1. Ensuite, mettez vos lignes d’entrée au format JSON. Le format est le même pour la détection du sentiment, des expressions clés et de la langue. Chaque ID doit être unique. Il correspond à l’ID renvoyé par le système. La taille maximale par document envoyé est de 10 Ko et la taille totale maximale de l’entrée envoyée est de 1 Mo. Vous ne pouvez pas envoyer plus de 1 000 documents dans un seul appel. La langue est un paramètre optionnel qui doit être spécifié si vous analysez du texte dans une langue autre que l’anglais. Vous trouverez ci-dessous un exemple d’entrée, où le paramètre facultatif `language` pour l’analyse des sentiments ou l’extraction d’expressions clés est inclus :

		{
			"documents": [
				{
					"language": "en",
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"language": "en",
					"id": "100",
					"text": "Final document"
				}
			]
		}

1. Adressez un appel **POST** au système avec l’entrée pour le sentiment, les expressions clés et la langue. Les URL doivent se présenter comme suit :

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Cet appel renvoie une réponse au format JSON, contenant les ID et les propriétés détectées. Un exemple de résultat pour le sentiment est présenté ci-dessous (sans les détails d’erreur). Dans le cas du sentiment, un score compris entre 0 et 1 est renvoyé pour chaque document :

        // Sentiment response
		{
		  	"documents": [
				{
					"id": "1",
					"score": "0.934"
		        },
                ...
                {
					"id": "100",
					"score": "0.002"
		        },
			]
		}

        // Key phrases response
        {
		  	"documents": [
				{
					"id": "1",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
                ...
                {
					"id": "100",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
			]
		}

        // Languages response
        {
		  	"documents": [
				{
					"id": "1",
					"detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
		        },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
		        }
			]
		}


## Tâche 3 : Détecter les rubriques dans un corpus de texte ####

Il s'agit d'une API lancée récemment, qui renvoie les premières rubriques détectées pour une liste d’enregistrements texte soumis. Une rubrique est identifiée par une expression clé, représentée par un ou plusieurs mots associés. L'API est conçue pour fonctionner correctement avec un texte court écrit par un humain, par exemple des évaluations et des commentaires d’utilisateurs.

Cette API nécessite l’envoi **d’au moins 100 enregistrements texte**, mais elle est conçue pour détecter des rubriques parmi des centaines de milliers d'enregistrements. Les enregistrements de langue non anglaise ou contenant moins de 3 mots sont ignorés et ne sont donc pas affectés à des rubriques. Pour la détection des rubriques, la taille maximale par document envoyé est de 30 Ko et la taille totale maximale de l’entrée envoyée est de 30 Mo.

Vous pouvez utiliser deux paramètres d’entrée **facultatifs** supplémentaires pour améliorer la qualité des résultats :

- **Mots exclus.** Ces mots et leurs formes dérivées proches (par exemple, les pluriels) sont exclus de l’ensemble du pipeline de détection des rubriques. Utilisez ce paramètre pour les mots courants (« problème », « erreur » et « utilisateur » sont des exemples pertinents de mots à exclure pour les plaintes des utilisateurs concernant les logiciels). Chaque chaîne doit contenir un seul mot.
- **Expressions exclues**. Ces expressions sont exclues de la liste des rubriques renvoyées. Utilisez ce paramètre pour exclure les rubriques génériques que vous ne souhaitez pas voir apparaître dans les résultats. « Microsoft » et « Azure » sont des exemples pertinents de rubriques à exclure. Les chaînes peuvent contenir plusieurs mots.

Suivez ces étapes pour détecter les rubriques dans votre texte.

1. Spécifiez l’entrée au format JSON. Cette fois, vous pouvez définir des mots exclus et des expressions exclues.

		{
			"documents": [
				{
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"id": "100",
					"text": "Final document"
				}
			],
			"stopWords": [
				"issue", "error", "user"
			],
			"stopPhrases": [
				"Microsoft", "Azure"
			]
		}

1. En utilisant les mêmes en-têtes que ceux définis dans la tâche 2, adressez un appel **POST** au point de terminaison des rubriques :

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. La réponse renvoyée est un en-tête `operation-location` dont la valeur est l’URL à interroger pour obtenir les rubriques :

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Interrogez régulièrement le `operation-location` retourné à l’aide d’une demande **GET**. Nous vous recommandons de l’interroger toutes les minutes.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. Le point de terminaison retourne une réponse contenant `{"status": "notstarted"}` avant le traitement, `{"status": "running"}` pendant le traitement et `{"status": "succeeded"}` avec le résultat à la fin du traitement. Vous pouvez ensuite utiliser le résultat qui s’affiche dans le format suivant (notez que cet exemple n’inclut pas certains détails, tels que les dates et le format d’erreur) :

		{
			"status": "succeeded",
			"operationProcessingResult": {
			  	"topics": [
                    {
					    "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
					    "score": "5"
					    "keyPhrase": "first topic name"
                    },
                    ...
                    {
					    "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
					    "score": "3"
					    "keyPhrase": "final topic name"
                    }
                ],
			  	"topicAssignments": [
                    {
					    "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
					    "documentId": "1",
					    "distance": "0.354"
                    },
                    ...
                    {
					    "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
					    "documentId": "55",
					    "distance": "0.758"
                    },            
                ]
			}
		}

À noter que la réponse correcte pour les rubriques du point de terminaison `operations` possède le schéma suivant :

	{
    		"topics" : [{
        		"id" : "string",
        		"score" : "number",
        		"keyPhrase" : "string"
    		}],
    		"topicAssignments" : [{
        		"documentId" : "string",
        		"topicId" : "string",
        		"distance" : "number"
    		}],
    		"errors" : [{
        		"id" : "string",
        		"message" : "string"
    		}]
    	}

Les explications concernant chaque partie de la réponse sont les suivantes :

**topics**

| Clé | Description |
|:-----|:----|
| id | Identificateur unique de chaque rubrique. |
| de votre application | Nombre de documents affectés à la rubrique. |
| keyPhrase | Mot ou phrase résumant la rubrique. |

**topicAssignments**

| Clé | Description |
|:-----|:----|
| documentId | Identificateur du document. Équivaut à l'ID inclus dans l'entrée. |
| topicId | ID de rubrique auquel le document a été affecté. |
| distance | Score d’affiliation d’un document à une rubrique compris entre 0 et 1. Plus le score de distance est faible, plus l’affiliation de rubrique est forte. |

**errors**

| Clé | Description |
|:-----|:----|
| id | Identificateur unique de document d’entrée auquel l’erreur fait référence. |
| message | Message d’erreur. |

## Étapes suivantes ##

Félicitations ! Vous avez terminé l’analyse des données de votre texte. Vous pouvez maintenant examiner les résultats à l’aide d’un outil tel que [Power BI](//powerbi.microsoft.com) pour visualiser vos données et automatiser vos analyses pour avoir une vue en temps réel des données de votre texte.

<!---HONumber=AcomDC_0706_2016-->