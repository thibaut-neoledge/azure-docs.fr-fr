<properties 
	pageTitle="Interactions RESTful avec les ressources DocumentDB | Microsoft Azure" 
	description="Passez en revue les méthodes HTTP dans ce didacticiel des services web RESTful. Apprenez à effectuer des interactions RESTful avec les ressources Microsoft Azure DocumentDB à l'aide de verbes HTTP."
	keywords="http methods, restful services tutorial, restful web services tutorial, http verbs, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="h0n"/>

# Didacticiel des services web RESTful : interactions RESTful avec les ressources DocumentDB 

DocumentDB prend en charge l'utilisation de méthodes HTTP pour créer, lire, remplacer, obtenir et supprimer des ressources DocumentDB.

En lisant ce didacticiel des services web RESTful, vous pourrez répondre aux questions suivantes :

- Comment les méthodes HTTP standard fonctionnent-elles avec les ressources DocumentDB ?
- Comment créer une nouvelle ressource à l'aide de POST ?
- Comment inscrire une procédure stockée à l'aide de POST ?
- Comment DocumentDB prend-t-il en charge le contrôle d'accès concurrentiel ?
- Quelles sont les options de connectivité pour HTTPS et TCP ?

## Vue d'ensemble des verbes HTTP
Les ressources DocumentDB prennent en charge les verbes HTTP suivants dans leur interprétation standard :

1.	POST signifie créer une nouvelle ressource d'élément. 
2.	GET signifie lire une ressource de flux ou un élément existant 
3.	PUT signifie remplacer une ressource d'élément existante 
4.	DELETE signifie supprimer une ressource d'élément existante
5.	HEAD signifie GET sans la charge utile de réponse (uniquement les en-têtes) 

>[AZURE.NOTE]À l'avenir, nous envisageons d'ajouter la prise en charge des mises à jour sélectives via PATCH.

Comme illustré dans le schéma des verbes HTTP ci-dessous, POST ne peut être émis que sur une ressource de flux ; PUT et DELETE ne peuvent être émis que sur une ressource d’élément ; GET et HEAD peuvent être émis sur des ressources d’élément ou de flux.

![Vue d’ensemble des verbes HTTP de ce didacticiel des services RESTful.][1]

**Modèle d’interaction utilisant les méthodes HTTP standard**

## Création d’une ressource à l’aide de la méthode HTTP POST 
Pour mieux comprendre le modèle d'interaction, prenons l'exemple de la création d'une ressource (INSERT). Pour créer une ressource, vous devez émettre une demande HTTP POST dont le corps contient la représentation de la ressource par rapport à l'URI du flux de conteneurs auquel la ressource appartient. La seule propriété requise pour la demande est l'ID de la ressource.

Par exemple, pour créer une base de données, vous VALIDEZ une ressource de base de données (en définissant la propriété ID avec un nom unique) dans /dbs. De même, pour créer une collection, vous VALIDEZ une ressource de collection dans */dbs/\_rid/colls/* et ainsi de suite. La réponse contient la ressource entièrement validée avec les propriétés générées par le système, y compris le lien *\_self* de la ressource et qui permet d'accéder à d'autres ressources. Voici un exemple de modèle d'interaction HTTP simple : un client émet une demande HTTP pour créer une base de données dans un compte.

```
	POST https://fabrikam.documents.azure.com/dbs
	{
	      "id":"MyDb"
	}

The DocumentDB service responds with a successful response and a status code indicating successful creation of the database.  

	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/UoEi5w==/",
	      "_ts": 1407370063,
	      "_etag": "00000100-0000-0000-0000-54b636600000",
	      "_colls": "colls/",
	      "_users": "users/"
	}
```
  
## Enregistrement d’une procédure stockée à l’aide de la méthode HTTP POST
La procédure stockée « HelloWorld » simple écrite entièrement en JavaScript est un autre exemple de création et d’exécution d’une ressource.

```
 	function HelloWorld() {
 	var context = getContext();
 	var response = context.getResponse();
 	
        response.setBody("Hello, World");
     }
```

La procédure stockée peut être enregistrée dans une collection sous MyDb en émettant une demande POST vers */dbs/\_rid-db/colls/\_rid-coll/sprocs*.

```
	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs HTTP/1.1
	
	{
	  "id": "HelloWorld",
	  "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }"
	}
```

Le service DocumentDB répond en indiquant une réponse réussie et un code d'état spécifiant l'enregistrement réussi de la procédure stockée.

```
	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	       "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }",
	      "id": "HelloWorld"
	      "_rid": "UoEi5w+upwABAAAAAAAAgA==",
	      "_ts" :  1421227641
	      "_self": "dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/",
	      "_etag": "00002100-0000-0000-0000-50f71fda0000"
	}
```

## Exécution d’une procédure stockée à l’aide de la méthode HTTP POST
Pour terminer, pour exécuter la procédure stockée de l’exemple ci-dessus, il est nécessaire d’émettre une requête POST vers l’URI de la ressource de procédure stockée (/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/) comme illustré ci-dessous.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA== HTTP/1.1
	
Le service DocumentDB émet la réponse suivante.

	HTTP/1.1 200 OK
	
	"Hello World"

Notez que le verbe HTTP POST permet de créer une ressource, d’exécuter une procédure stockée et d’émettre une requête SQL. Pour illustrer l'exécution de la requête SQL, regardez ce qui suit.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	x-ms-documentdb-query-enable-scan: True
	Content-Type: application/query+json
	...
	
	{"query":"SELECT f.LastName AS Name, f.Address.City AS City FROM Families f WHERE f.id='AndersenFamily' OR f.Address.City='NY'"}

Le service répond avec les résultats de la requête SQL.

```
	HTTP/1.1 200 Ok
	...
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 4
	x-ms-request-charge: 3.1
	Content-Type: application/json1
	...
	{"_rid":"UoEi5w+upwA=","Documents":[{"Name":"Andersen","City":"Seattle"},{"Name":"Wakefield","City":"NY"}],"_count":2}
```


## Utilisation des verbes HTTP PUT, GET et DELETE
Le remplacement ou la lecture d'une ressource revient à émettre des instructions PUT (avec un corps de demande valide) et GET vers le lien *\_self* de la ressource, respectivement. De même, la suppression d'une ressource revient à émettre une instruction DELETE vers le lien *\_self* de la ressource. Il est important de noter que l'organisation hiérarchique des ressources dans le modèle de ressource DocumentDB nécessite la prise en charge des suppressions en cascade où la suppression de la ressource de propriétaire entraîne la suppression des ressources dépendantes. Ces dernières peuvent être distribuées parmi des nœuds autres que les ressources de propriétaire, ce qui peut provoquer une suppression différée. Indépendamment des mécanismes du garbage collection, à la suppression d'une ressource, le quota est libéré et disponible instantanément pour utilisation. L'intégrité référentielle est conservée par le système. Par exemple, vous ne pouvez pas insérer une collection dans une base de données qui est supprimée, ni remplacer ou interroger un document d'une collection qui n'existe plus.
 
L'émission d'une instruction GET vers un flux de ressources ou l'interrogation d'une collection peut résulter en des millions d'éléments potentiels, rendant impossible pour le serveur de les matérialiser et pour les clients de les utiliser dans le cadre d'un seul échange de demande/réponse. Pour résoudre ce problème, DocumentDB permet aux clients de paginer les flux volumineux une page à la fois. Les clients peuvent utiliser l’en-tête de réponse [x-ms-continuation] en tant que curseur pour accéder à la page suivante.

## Contrôle d'accès concurrentiel optimiste
La plupart des applications web reposent sur une balise d'entité basée sur le contrôle d'accès concurrentiel optimiste pour éviter les problèmes de perte de mise à jour ou de suppression. La balise d'entité est un horodatage HTTP convivial et logique associé à une ressource. DocumentDB prend en charge de manière native le contrôle d'accès concurrentiel optimiste en s'assurant que chaque réponse HTTP contient la version (durablement) associée à la ressource spécifique. Les conflits du contrôle d'accès concurrentiel sont correctement détectés dans les cas suivants :

1.	Si deux clients émettent simultanément des demandes en mutation (via les verbes PUT/ DELETE) sur une ressource avec la dernière version de celle-ci (spécifiée par l'en-tête de demande [if-match]), le moteur de base de données DocumentDB les soumet au contrôle d'accès concurrentiel transactionnel.
2.	Si un client présente une ancienne version de la ressource (spécifiée via l'en-tête de demande [if-match]), sa demande est rejetée.  

## Options de connectivité
DocumentDB expose un modèle d'adressage logique où chaque ressource a un URI stable et logique identifié par son lien *\_self*. En tant que système de stockage distribué entre les régions, les ressources sous divers comptes de base de données dans DocumentDB sont partitionnées entre de nombreux ordinateurs et chaque partition est répliquée à des fins de haute disponibilité. Les réplicas qui gèrent les ressources d'une partition donnée enregistrent des adresses physiques. Alors que les adresses physiques changent au fil du temps suite aux défaillances, leurs adresses logiques restent stables et constantes. La logique de conversion des adresses physiques est conservée dans une table de routage qui est également disponible en interne en tant que ressource. DocumentDB expose deux modes de connectivité :

1.	**Mode passerelle :** Les clients sont protégés de la conversion des adresses logiques en adresses physiques ou des détails du routage ; ils s’occupent simplement des URI logiques et accèdent via RESTful au modèle de ressource. Les clients émettent les demandes à l'aide de l'URI logique qui est converti par les ordinateurs du périmètre en adresse physique du réplica qui gère la ressource et transfère la demande. Avec les ordinateurs du périmètre qui mettent en cache (et actualisent régulièrement) la table de routage, le routage est véritablement efficace. 
2.	**Mode connectivité directe :** Les clients gèrent directement la table de routage dans leur espace de traitement et l’actualisent régulièrement. Ils peuvent se connecter directement aux réplicas et ignorer les ordinateurs du périmètre.   


Mode de connectivité|Protocole|Détails|Kits de développement logiciel (SDK) DocumentDB
---|---|---|---
Passerelle|HTTPS|Les applications se connectent directement aux nœuds de périmètre à l'aide des URI logiques. Cela se produit dans un tronçon de réseau supplémentaire.|API REST, .NET, Node.js, Java, Python, JavaScript
Connectivité directe|HTTPS et TCP|Les applications peuvent accéder directement à la table de routage et effectuer le routage côté client pour se connecter directement aux réplicas.|.NET


## Étapes suivantes
Explorez la page [Référence de l’API REST d’Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour en savoir plus sur l’utilisation des ressources à l’aide de l’API REST.

## Références
- [Référence de l’API REST d’Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
- [Interrogation de DocumentDB](../documentdb-sql-query/)
- [Référence SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
- [Programmation DocumentDB : Procédures stockées, déclencheurs et fonctions définies par l’utilisateur](../documentdb-programming/)
- [Documentation de référence DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- REST [http://en.wikipedia.org/wiki/Representational\_state\_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
- Spécification JSON [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
- Spécification HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
- Balises d’entité [http://en.wikipedia.org/wiki/HTTP\_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
 

<!---HONumber=Oct15_HO3-->