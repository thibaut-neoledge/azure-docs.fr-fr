<properties 
	pageTitle="Interactions RESTful avec les ressources DocumentDB | Azure" 
	description="Apprenez à effectuer des interactions RESTful avec les ressources Microsoft Azure DocumentDB à l'aide de verbes HTTP." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="mimig"/>

#Interactions RESTful avec les ressources DocumentDB 

DocumentDB prend en charge l'utilisation de méthodes HTTP pour créer, lire, remplacer, obtenir et supprimer des ressources DocumentDB.

En lisant cet article, vous serez en mesure de répondre aux questions suivantes :

- Comment les méthodes HTTP standard fonctionnent-elles avec les ressources DocumentDB ?
- Comment créer une nouvelle ressource à l'aide de POST ?
- Comment inscrire une procédure stockée à l'aide de POST ?
- Comment DocumentDB prend-t-il en charge le contrôle d'accès concurrentiel ?
- Quelles sont les options de connectivité pour HTTPS et TCP ?

##Vue d'ensemble des verbes HTTP
Les ressources DocumentDB prennent en charge les verbes HTTP suivants dans leur interprétation standard :

1.	POST signifie créer une nouvelle ressource d'élément. 
2.	GET signifie lire une ressource de flux ou un élément existant 
3.	PUT signifie remplacer une ressource d'élément existante 
4.	DELETE signifie supprimer une ressource d'élément existante
5.	HEAD signifie GET sans la charge utile de réponse (uniquement les en-têtes) 

>[AZURE.NOTE] À l'avenir, nous envisageons d'ajouter la prise en charge des mises à jour sélectives via PATCH.  

Comme indiqué dans le schéma ci-dessous, POST ne peut être émis que sur une ressource de flux ; PUT et DELETE ne peuvent être émis que sur une ressource d'élément ; GET et HEAD peuvent être émis sur des ressources d'élément ou de flux. 

![][1]  

**Modèle d'interaction utilisant les méthodes HTTP standard**

##Création d'une ressource à l'aide de POST 
Pour mieux comprendre le modèle d'interaction, prenons l'exemple de la création d'une ressource (INSERT). Pour créer une ressource, vous devez émettre une demande HTTP POST dont le corps contient la représentation de la ressource par rapport à l'URI du flux de conteneurs auquel la ressource appartient. La seule propriété requise pour la demande est l'ID de la ressource.  

Par exemple, pour créer une base de données, vous émettez une demande POST vers une ressource de base de données (en définissant la propriété d'ID sur un nom unique) envers /dbs. De même, pour créer une collection, vous émettez une demande POST vers une ressource de collection envers /dbs/_rid/colls/, etc. La réponse contient la ressource entièrement validée avec les propriétés générées par le système, y compris le lien _self de la ressource et qui permet d'accéder à d'autres ressources. Voici un exemple de modèle d'interaction HTTP simple : un client émet une demande HTTP pour créer une base de données dans un compte.  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

Le service DocumentDB répond en indiquant une réponse réussie et un code d'état spécifiant la création réussie de la base de données.  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
##Enregistrement d'une procédure stockée à l'aide de POST
La procédure stockée suivante écrite entièrement en JavaScript est un autre exemple de création et d'exécution d'une ressource.   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

La procédure stockée peut être enregistrée dans une collection sous MyDb en émettant une demande POST vers /dbs/_rid-db/colls/_rid-coll/sprocs. 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
Le service DocumentDB répond en indiquant une réponse réussie et un code d'état spécifiant l'enregistrement réussi de la procédure stockée.  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
	       ...
	}

##Exécution d'une procédure stockée à l'aide de POST
Pour terminer, pour exécuter la procédure stockée de l'exemple ci-dessus, il est nécessaire d'émettre une demande POST vers l'URI de la ressource de procédure stockée (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Cette opération est illustrée dans le code suivant.  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

Le service DocumentDB émet la réponse suivante.  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

Notez que le verbe POST permet de créer une ressource, d'exécuter une procédure stockée et d'émettre une requête SQL. Pour illustrer l'exécution de la requête SQL, regardez ce qui suit.  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

Le service répond avec les résultats de la requête SQL.   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


##Utilisation d'une instruction PUT, GET et DELETE
Le remplacement ou la lecture d'une ressource revient à émettre des verbes PUT (avec un corps de demande valide) et GET vers le lien _self de la ressource, respectivement. De même, la suppression d'une ressource revient à émettre un verbe DELETE vers le lien _self de la ressource. Il est important de noter que l'organisation hiérarchique des ressources dans le modèle de ressource DocumentDB nécessite la prise en charge des suppressions en cascade où la suppression de la ressource de propriétaire entraîne la suppression des ressources dépendantes. Ces dernières peuvent être distribuées parmi des nœuds autres que les ressources de propriétaire, ce qui peut provoquer une suppression différée. Indépendamment des mécanismes du garbage collection, à la suppression d'une ressource, le quota est libéré et disponible instantanément pour utilisation. L'intégrité référentielle est conservée par le système. Par exemple, vous ne pouvez pas insérer une collection dans une base de données qui est supprimée, ni remplacer ou interroger un document d'une collection qui n'existe plus.  
 
L'émission d'une instruction GET vers un flux de ressources ou l'interrogation d'une collection peut résulter en des millions d'éléments potentiels, rendant impossible pour le serveur de les matérialiser et pour les clients de les utiliser dans le cadre d'un seul échange de demande/réponse. Pour résoudre ce problème, DocumentDB permet aux clients de paginer les flux volumineux une page à la fois. Les clients peuvent utiliser l'en-tête de réponse [x-ms-continuationToken] en tant que curseur pour accéder à la page suivante.   

##Contrôle d'accès concurrentiel optimiste
La plupart des applications web reposent sur une balise d'entité basée sur le contrôle d'accès concurrentiel optimiste pour éviter les problèmes de perte de mise à jour ou de suppression. La balise d'entité est un horodatage HTTP convivial et logique associé à une ressource. DocumentDB prend en charge de manière native le contrôle d'accès concurrentiel optimiste en s'assurant que chaque réponse HTTP contient la version (durablement) associée à la ressource spécifique. Les conflits du contrôle d'accès concurrentiel sont correctement détectés dans les cas suivants :  

1.	Si deux clients émettent simultanément des demandes en mutation (via les verbes PUT/ DELETE) sur une ressource avec la dernière version de celle-ci (spécifiée par l'en-tête de demande [if-match]), le moteur de base de données DocumentDB les soumet au contrôle d'accès concurrentiel transactionnel.
2.	Si un client présente une ancienne version de la ressource (spécifiée via l'en-tête de demande [if-match]), sa demande est rejetée.  

##Options de connectivité
DocumentDB expose un modèle d'adressage logique où chaque ressource a un URI stable et logique identifié par son lien _self. En tant que système de stockage distribué entre les régions, les ressources sous divers comptes de base de données dans DocumentDB sont partitionnées entre de nombreux ordinateurs et chaque partition est répliquée à des fins de haute disponibilité. Les réplicas qui gèrent les ressources d'une partition donnée enregistrent des adresses physiques. Alors que les adresses physiques changent au fil du temps suite aux défaillances, leurs adresses logiques restent stables et constantes. La logique de conversion des adresses physiques est conservée dans une table de routage qui est également disponible en interne en tant que ressource. DocumentDB expose deux modes de connectivité :  

1.	**Mode de passerelle** : les clients ne sont pas en charge de la traduction des adresses logiques en adresses physiques ou des données du routage. Ils s'occupent simplement des URI logiques et accèdent avec RESTful au modèle de ressource. Les clients émettent les demandes à l'aide de l'URI logique et les machines Edge traduisent l'URI logique en l'adresse physique du réplica qui gère la ressource et transfère la demande. Les machines Edge mettent en cache (et actualisent périodiquement) la table de routage, ce qui rend le routage extrêmement efficace. 
2.	**Mode de connectivité direct** : les clients gèrent directement la table de routage dans leur espace de processus et l'actualisent régulièrement. Les clients peuvent se connecter directement avec les réplicas et ignorer ainsi les machines Edge.   


<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>Mode de connectivité</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>Protocole</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>Détails</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>Kits de développement logiciel (SDK) de DocumentDB</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Passerelle
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Les applications se connectent directement aux nœuds de périmètre à l'aide des URI logiques. Cela se produit dans un tronçon de réseau supplémentaire.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    API REST
                </p>
                <p>
                    .NET, JavaScript, Node.js, Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Connectivité directe
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS et
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Les applications peuvent accéder directement à la table de routage et effectuer le routage côté client pour se connecter directement aux réplicas.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##Étapes suivantes
Explorez la page [Référence de l'API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour en savoir plus sur l'utilisation des ressources à l'aide de l'API REST.

##Références
-   [Référence de l'API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
-	REST [http://fr.wikipedia.org/wiki/Representational_State_Transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Spécification JSON  [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	Spécification HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Balises d'entité [http://fr.wikipedia.org/wiki/Balise-entit%C3%A9_ETag_HTTP](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Interrogation de DocumentDB](documentdb-sql-query.md)
-	[Référence SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[Programmation DocumentDB : procédures stockées, déclencheurs et fonctions définies par l'utilisateur](../documentdb-programming/)
-	[Documentation de référence DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=49-->