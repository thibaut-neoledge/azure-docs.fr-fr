
<properties
	pageTitle="Obtention de recommandations par lots : API Recommandations Machine Learning | Microsoft Azure"
	description="Recommandations Azure Machine Learning - Obtention de recommandations par lots"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="luisca"/>

# Obtenir des recommandations par lots

>[AZURE.NOTE] Il est plus compliqué d’obtenir des recommandations par lots que d’obtenir une recommandation à la fois. Vérifiez les API pour obtenir des informations sur l’obtention de recommandations pour une seule requête :

> [Recommandations élément-élément](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br> [Recommandations utilisateur-élément](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> La notation de lot fonctionne uniquement pour les builds créées après le 21 juillet 2016.


Dans certaines situations, vous devez obtenir des recommandations pour plusieurs éléments à la fois. Par exemple, vous pouvez être intéressé par la création d’un cache de recommandations, voire par l’analyse des types de recommandation obtenus.

Les opérations de notation de lot, comme nous les appelons, sont asynchrones. Vous devez soumettre la requête, attendre que l’opération soit terminée, puis collecter les résultats.

Pour être plus précis, voici les étapes à suivre :

1.	Créez un conteneur Azure Storage si vous n’en avez pas.
2.	Chargez un fichier d’entrée décrivant chacune de vos requêtes de recommandation vers Azure Blob Storage.
3.	Démarrez le travail de notation de lot.
4.	Attendez la fin de l’opération asynchrone.
5.	Lorsque l’opération est terminée, rassemblez les résultats depuis le stockage Blob.

Examinons chacune de ces étapes.

## Créez un conteneur Azure Storage si vous n’en avez pas.

Accédez au [portail Azure](https://portal.azure.com) et créez un compte de stockage si vous n’en avez pas déjà un. Pour ce faire, accédez à **Nouveau** > **Données** + **stockage** > **Compte de stockage**.

Une fois que vous disposez d’un compte de stockage, vous devez créer les conteneurs d’objets blob où vous stockerez l’entrée et la sortie de l’exécution par lots.

Chargez un fichier d’entrée décrivant chacune de vos requêtes de recommandation vers Blob Storage. Appelons-le input.json. Une fois le conteneur créé, vous devez charger un fichier qui décrit chaque requête que vous devez effectuer à partir du service de recommandations.

Un lot ne peut effectuer qu’un seul type de requête à partir d’une build spécifique. Nous expliquerons comment définir ces informations dans la section suivante. Maintenant, supposons que nous allons réaliser des recommandations d’éléments à partir d’une build spécifique. Le fichier d’entrée contient alors les informations d’entrée (dans ce cas, les éléments initiaux) pour chacune des requêtes.

Voici un exemple de fichier input.json :

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Comme vous pouvez le constater, il s’agit d’un fichier JSON, dans lequel chaque requête comporte les informations nécessaires pour envoyer une requête de recommandation. Créez un fichier JSON similaire pour les requêtes que vous devez traiter, puis copiez-le dans le conteneur que vous venez de créer dans Blob Storage.

## Démarrer le travail de notation de lot

L’étape suivante consiste à soumettre un nouveau travail Batch. Pour plus d’informations, consultez la [référence de l’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

Le corps de la requête de l’API doit définir les emplacements de stockage des fichiers d’entrée, de sortie et d’erreur. Il doit également définir les informations d’identification nécessaires pour accéder à ces emplacements. En outre, vous devez spécifier certains paramètres qui s’appliquent à la totalité du lot (le type de recommandations à demander, le modèle ou la build à utiliser, le nombre de résultats par appel, etc.).

Voici un exemple de corps de requête :

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Voici quelques points importants à prendre en considération :

-	Actuellement, **authenticationType** doit toujours être défini sur **PublicOrSas**.

-	Vous devez obtenir un jeton de signature d’accès partagé (SAP) pour autoriser l’API Recommandations à lire et écrire depuis/vers votre compte Blob Storage. Vous trouverez plus d’informations sur la génération de jetons SAS sur [la page Recommendations d’API](../storage/storage-dotnet-shared-access-signature-part-1.md).

-	Le seul **apiName** actuellement pris en charge est **ItemRecommend**, qui est utilisé pour les recommandations élément à élément. Le traitement par lots ne prend actuellement pas en charge les recommandations utilisateur-élément.

## Attendez la fin de l’opération asynchrone.

Lorsque vous démarrez l’opération par lots, la réponse renvoie l’en-tête Operation-Location qui vous donne les informations nécessaires pour effectuer le suivi de l’opération. Vous effectuez le suivi de l’opération à l’aide de [l’API Récupérer l’état de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), comme vous le feriez pour le suivi d’une opération de génération.

## Obtenir les résultats

Une fois l’opération terminée, et en supposant qu’elle ne comporte aucune erreur, vous pouvez collecter les résultats à partir de votre stockage d’objets blob de sortie.

L’exemple ci-dessous montre ce à quoi la sortie peut ressembler. Dans cet exemple, nous présentons les résultats d’un lot avec seulement deux requêtes (par souci de concision).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## En savoir plus sur les limitations

-	Un seul traitement par lots peut être appelé par abonnement à la fois.
-	Un fichier d’entrée de traitement par lots ne peut pas avoir une taille supérieure à 2 Mo.

<!---HONumber=AcomDC_0824_2016-->