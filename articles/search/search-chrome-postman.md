<properties
	pageTitle="Utilisation de Chrome Postman avec Azure Search"
	description="Utilisation de Chrome Postman avec Azure Search"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Utilisation de Chrome Postman avec Azure Search #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") est un outil fourni avec Google Chrome, permettant aux développeurs de travailler efficacement avec des services API REST comme Azure Search. Vous pouvez utiliser Postman pour rapidement créer et interroger vos index de recherche en envoyant des appels API via Postman sans être obligé d'écrire du code. Cette approche est un moyen efficace d'apprendre l'API et de tester de nouvelles fonctionnalités.

![][1]

## Conditions requises ##

Vous devez disposer d'un service Azure Search. Comme avec toute application personnalisée qui utilise Azure Search, vous aurez besoin de l'URL pointant vers votre service ainsi que d'un administrateur `api-key` pour pouvoir créer l'index. Pour savoir comment obtenir les valeurs de votre service de recherche, consultez [Créer un service dans le portail](search-create-service-portal.md).

## Installation de Postman ##
Pour télécharger Postman, visitez le [Google Chrome Store](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm). Le lien de cette page vous permet de télécharger et d'installer le client REST pour Postman. Une fois installé, vous pouvez lancer Postman à partir du lanceur d'applications Chrome.

![][2]

## Configuration de Postman pour interroger Azure Search ##
Pour configurer Postman, procédez comme suit :

1. Entrez l'URL de votre service Azure Search dans le champ « Enter request URL here ».  
2. Ajoutez à l'URL : `?api-version=2015-02-28`. Vous pouvez également spécifier une version d'API différente. Pour plus d'informations, consultez [Contrôle de version du service Azure Search](https://msdn.microsoft.com/library/azure/dn864560.aspx).
3. Vérifiez que `GET` est sélectionné.
4. Cliquez sur le bouton **En-têtes**.
5. Entrez les valeurs suivantes :
	- `api-key` : [clé administrateur]
	- `Content-Type` : `application/json; charset=utf-8`
6. Cliquez sur **Envoyer** pour transmettre l'appel REST à Azure Search et afficher la réponse JSON.

![][3]

## Création d'un index Azure Search avec Postman ##

Nous allons maintenant développer ce que nous effectué à la dernière étape en envoyant un appel REST pour créer un index Azure Search. Contrairement à l'appel précédent, la création de l'index nécessite un élément HTTP PUT ainsi qu'un document JSON contenant la définition du schéma d'index. Pour cet exemple, nous allons créer un index de différents chemins de randonnée (trails). Pour ce faire :

1. Remplacez l'URL par `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28` en utilisant le nom de votre service de recherche.
2. Remplacez le type de requête `GET` par `PUT`.
3. Dans le contenu RAW, entrez le JSON suivant :

	    {
	    "name": "trails",
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false},
	    {"name": "name", "type": "Edm.String"},
	    {"name": "county", "type": "Edm.String"},
	    {"name": "elevation", "type": "Edm.Int32"},
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. Cliquez sur **Envoyer**.

![][4]

## Envoi de documents vers un index Azure Search avec Postman ##
Maintenant que l'index est créé, nous pouvons y charger des documents. Pour cela, nous allons envoyer un groupe de documents dans un lot en utilisant les données de cinq trails du jeu de données United States Geological Survey (USGS) :

1. Remplacez l'URL par `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28` en utilisant le nom de votre service de recherche. Notez que l'URL contient le chemin vers l'index que vous venez de créer.
2. Remplacez le type HTTP par `POST`.
3. Dans le contenu RAW, entrez le JSON suivant :

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }

4. Cliquez sur **Envoyer**.

![][5]

## Interrogation de l'index avec Postman ##
L'étape finale consiste à interroger l'index et à envoyer une simple requête de recherche en texte intégral pour le mot *trail*.

1. Entrez les informations suivantes dans l'URL : `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail` en utilisant le nom de votre service de recherche. Notez que l'URL inclut le paramètre de requête `search` et le terme de recherche *trail*.
2. Remplacez le type de requête HTTP par `GET`.
3. Cliquez sur **Envoyer**.

La réponse devrait afficher les résultats de la recherche JSON effectuée par Azure Search.

![][6]

## Étapes suivantes ##
Maintenant que nous avons passé en revue les principes fondamentaux de l'utilisation d'Azure Search avec Postman, plusieurs éléments vous aideront dans les étapes suivantes :

1. Postman prend en charge `Collections`, un moyen pratique d'enregistrer les requêtes fréquemment émises. Vous pouvez partager les collections avec d'autres personnes. Elles les recevront dans leur propre système Postman.
2. Dans la documentation Azure Search, veillez à noter le type de requête HTTP (`GET`, `PUT`, etc.) associé à chaque appel et à le modifier si nécessaire dans Postman.

Vous trouverez la documentation de l'API REST sur [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Vous pouvez également obtenir d'autres exemples grâce aux [vidéos et didacticiels](https://msdn.microsoft.com/library/azure/dn818681.aspx).

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png
 

<!---HONumber=July15_HO4-->