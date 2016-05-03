<properties
pageTitle="Indexation d’objets blob JSON avec l’indexeur d’objets blob Azure Search"
description="Apprendre à indexer les objets blob JSON avec Azure Search"
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
ms.date="04/20/2016"
ms.author="eugenesh" />

# Indexation d’objets blob JSON avec l’indexeur d’objets blob Azure Search 

Par défaut, l’[indexeur d’objets blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyse les objets blob JSON comme un bloc de texte unique. Souvent, vous souhaiterez conserver la structure de vos documents JSON. Par exemple, dans le document JSON

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

vous pouvez analyser les champs « text », « datePublished » et « tags » dans votre index de recherche.

Cet article explique comment configurer l’indexeur d’objets blob Azure Search pour effectuer une analyse JSON. Joyeuse indexation !

> [AZURE.IMPORTANT] Pour l’instant, cette fonctionnalité n’existe qu’en version préliminaire. Elle est uniquement disponible dans l’API REST utilisant la version **2015-02-28-Preview**. N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.

## Configuration de l’indexation JSON

Pour indexer des objets blob JSON, utilisez l’indexeur d’objets blob en mode « Analyse JSON ». Activez le paramètre de configuration `useJsonParser` dans la propriété `parameters` de la définition de l’indexeur :

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

Si nécessaire, utilisez les **mappages de champ** pour sélectionner les propriétés du document JSON source utilisé pour renseigner votre index de recherche cible. Ceci est décrit en détail ci-après.

> [AZURE.IMPORTANT] Lorsque vous utilisez le mode d’analyse JSON, Azure Search suppose que tous les objets blob dans votre source de données seront de type JSON. Si vous devez prendre en charge une combinaison d’objets blob JSON et autres dans la même source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Utilisation des mappages de champ pour créer des documents de recherche 

Actuellement, Azure Search ne peut pas indexer des documents JSON arbitraires directement, car il ne prend en charge que les types de données primitifs, les tableaux de chaînes et les points GeoJSON. Toutefois, vous pouvez utiliser les **mappages de champ** pour sélectionner des parties de votre document JSON et les intégrer dans les champs de niveau supérieur du document de recherche. Pour en savoir plus sur les principes de base des mappages de champ, consultez [Personnalisation des indexeurs Azure Search](search-indexers-customization.md).

Revenons à notre exemple de document JSON :

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Imaginons que votre index de recherche contient les champs suivants : `text` de type Edm.String, `date` de type Edm.DateTimeOffset et `tags` de type Collection(Edm.String). Pour mapper votre document JSON à la forme souhaitée, utilisez les mappages de champ suivants :

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

Les noms de champ source dans les mappages sont spécifiés selon la notation de [pointeur JSON](http://tools.ietf.org/html/rfc6901). Vous débutez par une barre oblique pour faire référence à la racine de votre document JSON, puis vous recherchez la propriété souhaitée (au niveau arbitraire de l’imbrication) en utilisant un chemin d’accès séparé par des barres obliques avant.

Vous pouvez également faire référence à des éléments de tableau en utilisant un index de base zéro. Par exemple, pour sélectionner le premier élément du tableau « tags » dans l’exemple ci-dessus, utilisez un mappage de champ similaire au suivant :

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Si un nom de champ source dans un chemin de mappage de champ fait référence à une propriété qui n’existe pas dans JSON, ce mappage est ignoré sans erreur. Cela nous permet de prendre en charge les documents avec un schéma différent (cas fréquent). Comme il n’y a aucune validation, vous devez veiller à éviter les fautes de frappe dans la spécification du mappage de champ.

Si vos documents JSON ne contiennent que des propriétés de niveau supérieur, il se peut que vous n’ayez pas besoin d’aucun mappage de champ. Par exemple, si votre document JSON ressemble à ceci, les propriétés de niveau supérieur « text », « datePublished » et « tags » seront mappées directement aux champs correspondants dans l’index de recherche :
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] Pour l’instant, Azure Search ne prend en charge que l’analyse d’un objet blob JSON dans un document de recherche. Si vos objets blob contiennent des tableaux JSON que vous souhaitez analyser dans plusieurs documents de recherche, votez pour [cette suggestion UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d) afin de relever la priorité de ce travail.

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
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

Indexeur :

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Aidez-nous à améliorer Azure Search

Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0420_2016-->