<properties
pageTitle="Indexation d’objets blob CSV avec l’indexeur d’objets blob Azure Search | Microsoft Azure"
description="Apprendre à indexer les objets blob CSV avec Azure Search"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/12/2016"
ms.author="eugenesh" />

# Indexation d’objets blob CSV avec l’indexeur d’objets blob Azure Search 

Par défaut, l’[indexeur d’objets blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyse les objets blob de texte délimité comme un bloc de texte unique. Toutefois, avec des objets blob contenant des données CSV, vous souhaitez généralement traiter chaque ligne dans l’objet blob comme un document distinct. Par exemple, étant donné le texte délimité suivant :

	id, datePublished, tags
	1, 2016-01-12, "azure-search,azure,cloud" 
	2, 2016-07-07, "cloud,mobile" 

vous pouvez l’analyser dans 2 documents contenant chacun les champs « id », « datePublished » et « tags ».

Dans cet article, vous découvrirez comment analyser les objets blob CSV avec un indexeur d’objet blob Azure Search.

> [AZURE.IMPORTANT] Pour l’instant, cette fonctionnalité n’existe qu’en version préliminaire. Elle est uniquement disponible dans l’API REST utilisant la version **2015-02-28-Preview**. N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.

## Configuration de l’indexation CSV

Pour indexer des objets blob CSV, créer ou mettre à jour une définition d’indexeur le mode d’analyse `delimitedText` :

	{
	  "name" : "my-csv-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
	}

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders` Indique que la première ligne (non vide) de chaque objet blob contient des en-têtes. Si les objets blob ne contiennent pas de ligne d’en-tête initiale, les en-têtes doivent être spécifiés dans la configuration de l’indexeur :

	"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Actuellement, seul le format UTF-8 est pris en charge. En outre, seule la virgule `','` est prise en charge comme délimiteur. Si vous devez prendre en charge d’autres formats ou délimiteurs, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Lorsque vous utilisez le mode d’analyse de texte délimité, Azure Search suppose que tous les objets blob dans votre source de données seront de type CSV. Si vous devez prendre en charge une combinaison d’objets blob CSV et autres dans la même source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Exemples de requête

En résumé, voici des exemples complets de charges utiles.

Source de données :

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
	}   

Indexeur :

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-csv-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
	}

## Aidez-nous à améliorer Azure Search

Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0713_2016-->