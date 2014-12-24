<properties title="Interact with DocumentDB resources" pageTitle="Interaction avec les ressources DocumentDB | Azure" description="DocumentDB provides client SDKs for .NET, Python, Node.js and JavaScript - all of which are simple wrappers over the underlying REST APIs." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Interaction avec les ressources DocumentDB 
DocumentDB fournit un modèle de programmation RESTful simple et ouvert sur HTTP. Dans sa version préliminaire, DocumentDB propose des Kits de développement logiciel (SDK) clients pour .NET, Python, Node.js et JavaScript qui sont de simples wrappers sur les API REST sous-jacentes. Dans les prochaines versions, des Kits de développement logiciel (SDK) seront également proposés pour C++ et Java. Nous vous encourageons à écrire vos propres Kits de développement logiciel (SDK) adaptés à votre environnement de programmation spécifique et à le partager avec la communauté dans la mesure où nos propres Kits de développement logiciel (SDK) sont ouverts à tous. 

>[AZURE.NOTE] De même, DocumentDB fournit un protocole TCP très efficace qui est également RESTful dans son modèle de communication, disponible via le Kit de développement logiciel (SDK) .Net client.  

##Ressources
Les entités gérées par DocumentDB sont nommées **ressources**. Elles sont identifiées de façon unique par leur URI logique. Les développeurs peuvent interagir avec ces ressources en utilisant des verbes HTTP standard, des en-têtes de demande/réponse et des codes d'état. Comme l'illustre le schéma suivant, le **modèle de ressource** de DocumentDB regroupe des ensembles de ressources sous un compte de base de données, chacun d'eux étant adressable via un URI stable et logique. Dans ce document, le terme **flux** désigne un ensemble de ressources.  

![][1]  

##Modèle de ressource hiérarchique sous un compte de base de données ##

En tant que client de DocumentDB, vous commencez par approvisionner un **compte de base de données** DocumentDB en utilisant votre abonnement Azure. Un compte de base de données se compose d'un ensemble de **bases de données**. Chacune d'elle contient plusieurs **collections**, qui contiennent à leur tour **des procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents** et les **pièces jointes associées**. Une base de données est aussi associée à des **utilisateurs**, chacun possédant un ensemble d'**autorisations** permettant d'accéder à d'autres collections, procédures stockées, déclencheurs, fonctions définies par l'utilisateur, documents ou pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents et les pièces jointes contiennent du contenu JSON arbitraire défini par l'utilisateur.  

|Ressource 	|Description
|-----------|-----------
|Compte de base de données	|Un compte de base de données est associé à une ou plusieurs unités de capacité représentant le débit et le stockage de documents approvisionnés, un ensemble de bases de données et un stockage d'objets blob. Vous pouvez créer un ou plusieurs comptes de base de données à l'aide de votre abonnement Azure.
|Base de données	|Une base de données est un conteneur logique de stockage de documents partitionné entre des collections. Elle sert également de conteneur pour les utilisateurs.
|Utilisateur	|Espace de noms logique pour les autorisations d'étendue/de partitionnement. 
|Autorisation	|Jeton d'autorisation associé à un utilisateur pour un accès autorisé à une ressource spécifique.
|Collection	|Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.
|Procédure stockée	|Logique d'application écrite en JavaScript, inscrite avec une collection et exécutée via des transactions au sein du moteur de base de données.
|Déclencheur	|Logique d'application écrite en JavaScript modélisant les effets secondaires associés à des opérations d'insertion, de remplacement ou de suppression.
|Fonction définie par l'utilisateur (UDF)	|Logique d'application gratuite à effet secondaire écrite en JavaScript. Les fonctions définies par l'utilisateur permettent de modéliser un opérateur de requête personnalisé et ainsi d'étendre le langage de requête DocumentDB principal.
|Document	|Contenu JSON (arbitraire) défini par l'utilisateur. Par défaut, il n'est pas nécessaire de définir des schémas ni de fournir des index secondaires pour tous les documents ajoutés à une collection.
|Pièce-jointe	|Les pièces jointes sont des documents spéciaux contenant des références et les métadonnées associées à un élément multimédia/objet blob externe. Le développeur peut choisir de laisser DocumentDB gérer l'objet blob ou de le stocker avec un fournisseur de service de stockage d'objets blob tel que OneDrive, Dropbox etc. 

###Ressources système ou ressources définies par l'utilisateur
Les ressources (telles que les comptes de base de données, les bases de données, les collections, les utilisateurs, les autorisations, les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur) ont toutes un schéma fixe et sont nommées " *ressources système* ". Par contre, les ressources telles que les documents et les pièces jointes n'ont pas de restriction de schéma et sont des exemples de *ressources définies par l'utilisateur*. Dans DocumentDB, les ressources système et définies par l'utilisateur sont représentées et gérées en tant qu'éléments JSON standard. Toutes les ressources, tant système que définies par l'utilisateur, ont les propriétés communes suivantes.

>[AZURE.NOTE] Toutes les propriétés générées par le système dans une ressource ont comme préfixe un trait de soulignement (_) dans leur représentation JSON.  


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>Propriété </b></p></td>
<td valign="top" ><p><b>Définie par l'utilisateur ou générée par le système ?</b></p></td>
<td valign="top" ><p><b>Objectif</b></p></td>
</tr>

<tr>
<td valign="top" ><p>_rid</p></td>
<td valign="top" ><p>Générée par le système</p></td>
<td valign="top" ><p>Identificateur hiérarchique, unique et généré par le système de la ressource</p></td>
</tr>

<tr>
<td valign="top" ><p>_etag</p></td>
<td valign="top" ><p>Générée par le système</p></td>
<td valign="top" ><p>etag de la ressource nécessaire au contrôle d'accès concurrentiel optimiste</p></td>
</tr>

<tr>
<td valign="top" ><p>_ts</p></td>
<td valign="top" ><p>Générée par le système</p></td>
<td valign="top" ><p>Dernier horodatage mis à jour de la ressource</p></td>
</tr>

<tr>
<td valign="top" ><p>_self</p></td>
<td valign="top" ><p>Générée par le système</p></td>
<td valign="top" ><p>URI adressable unique de la ressource </p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>Définissable par l'utilisateur</p></td>
<td valign="top" ><p>Nom unique défini par l'utilisateur de la ressource </p></td>
</tr>

</tbody>
</table>


###Représentation en réseau des ressources
DocumentDB n'oblige pas les extensions propriétaires à adopter la norme JSON ou des codages spéciaux ; l'application fonctionne avec les documents JSON standard.  
 
###Adressage d'une ressource
Toutes les ressources sont adressables via des URI. La valeur de la propriété **_self** d'une ressource représente l'URI relatif de la ressource. Le format de l'URI est composé des segments de chemin d'accès /<flux>/  /{_rid} :

|Value of the _self	|Description
|-------------------|-----------
|/dbs	|feed of databases under a database account
|/dbs/{_rid-db}	|Database with the unique id property with the value {_rid-db}
|/dbs/{_rid-db}/colls/	|feed of collections under a database 
|/dbs/{_rid-db}/colls/{_rid-coll}	|Collection with the unique id property with the value {_rid-coll}
|/dbs/{_rid-db}/users/	|feed of users under a database 
|/dbs/{_rid-db}/users/{_rid-user}	|User with the unique id property with the value {_rid-user}
|/dbs/{_rid-db}/users/{_rid-user}/permissions	|feed of permissions under a database 
|/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission}	|Permission with the unique id property with the value {_rid-permission}  
  

Une ressource a également un nom défini par l'utilisateur, qui est unique et exposé via la propriété d'ID de la ressource. L'ID est une chaîne longue de 256 caractères maximum, définie par l'utilisateur et unique dans le contexte d'une ressource parent spécifique. Par exemple, la valeur de la propriété d'ID de tous les documents d'une collection donnée est unique, mais pas systématiquement unique parmi les collections. De même, la valeur de la propriété d'ID de toutes les autorisations d'un utilisateur donné est unique, mais pas systématiquement unique parmi les utilisateurs. La propriété _rid permet de construire le lien _self adressable d'une ressource.   

Chaque ressource a également un identificateur de ressource hiérarchique défini par l'utilisateur (également appelé RID) qui est disponible via la propriété _rid. Le RID encode la hiérarchie entière d'une ressource donnée. C'est une représentation interne très pratique qui permet d'appliquer l'intégrité référentielle de manière distribuée. Le RID est unique au sein d'un compte de base de données. Il est utilisé en interne par DocumentDB pour un routage efficace sans nécessiter de recherche parmi les partitions. 

Les valeurs des propriétés _self et   _rid sont des représentations secondaires et canoniques d'une ressource.  
 
##Modèle d'interaction de base
Les ressources prennent en charge les verbes HTTP  suivants dans leur interprétation standard :

>[AZURE.NOTE] À l'avenir, nous envisageons d'ajouter la prise en charge des mises à jour sélectives via PATCH  

1.	POST signifie créer une nouvelle ressource d'élément. 
2.	GET signifie lire une ressource de flux ou un élément existant 
3.	PUT signifie remplacer une ressource d'élément existante 
4.	DELETE signifie supprimer une ressource  d'élément existante
5.	HEAD signifie GET sans la charge utile de réponse (uniquement les en-têtes)

Dans les prochaines versions, les ressources d'élément prendront également en charge le verbe PATCH.  

Comme indiqué dans la figure ci-dessous, POST peut être émis sur une ressource de flux ; PUT et DELETE ne peuvent être émis que sur une ressource d'élément ; GET et HEAD peuvent être émis sur des ressources d'élément ou de flux. 

![][2]  

**Modèle d'interaction utilisant les verbes HTTP standard**

Pour mieux comprendre le modèle d'interaction, prenons l'exemple de la création d'une ressource (INSERT). Pour créer une ressource, vous devez émettre une demande HTTP POST dont le corps contient la représentation de la ressource par rapport à l'URI du flux de conteneurs auquel la ressource appartient. La seule propriété requise pour la demande est l'ID de la ressource.  

Par exemple, pour créer une base de données, vous émettez une demande POST vers une ressource de base de données (en définissant la propriété d'ID sur un nom unique) envers /dbs. De même, pour créer une collection, vous émettez une demande POST vers une ressource de collection envers /dbs/_rid/colls/, etc. La réponse contient la ressource entièrement validée avec les propriétés générées par le système, y compris le lien _self de la ressource et qui permet d'accéder à d'autres ressources. Voici un exemple de modèle d'interaction HTTP simple : un client émet une demande HTTP pour créer une base de données dans un compte :  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}
DocumentDB répond en indiquant une réponse réussie et un code d'état spécifiant la création réussie de la base de données.  

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

DocumentDB répond en indiquant une réponse réussie et un code d'état spécifiant la création réussie de la base de données.  

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

Pour terminer, pour exécuter la procédure stockée de l'exemple ci-dessus, il est nécessaire d'émettre une demande POST vers l'URI de la ressource de la procédure stockée (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). En voici une illustration :  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

Le service DocumentDB émet la réponse suivante :  

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

Notez que le verbe POST permet de créer une ressource, d'exécuter une procédure stockée et d'émettre une requête SQL. Pour illustrer l'exécution de la requête SQL, regardez ce qui suit :  

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


Le remplacement ou la lecture d'une ressource revient à émettre des verbes PUT (avec un corps de demande valide) et GET vers le lien _self de la ressource, respectivement. De même, la suppression d'une ressource revient à émettre un verbe DELETE vers le lien _self de la ressource. Il est important de noter que l'organisation hiérarchique des ressources dans le modèle de ressource DocumentDB nécessite la prise en charge des suppressions en cascade où la suppression de la ressource de propriétaire entraîne la suppression des ressources dépendantes. Ces dernières peuvent être distribuées parmi des nœuds autres que les ressources de propriétaire, ce qui peut provoquer une suppression différée. Indépendamment des mécanismes du garbage collection, à la suppression d'une ressource, le quota est libéré et disponible instantanément pour utilisation. L'intégrité référentielle est conservée par le système. Par exemple, vous ne pouvez pas insérer une collection dans une base de données qui est supprimée, ni remplacer ou interroger un document d'une collection qui n'existe plus.  
 
L'émission d'une instruction GET vers un flux de ressources ou l'interrogation d'une collection peut résulter en des millions d'éléments potentiels, rendant impossible pour le serveur de les matérialiser et pour les clients de les utiliser dans le cadre d'un seul échange de demande/réponse. Pour résoudre ce problème, DocumentDB permet aux clients de paginer les flux volumineux une page à la fois. Les clients peuvent utiliser l'en-tête de réponse [x-ms-continuationToken] en tant que curseur pour accéder à la page suivante.   

##Contrôle d'accès concurrentiel optimiste
La plupart des applications web reposent sur une balise d'entité basée sur le contrôle d'accès concurrentiel optimiste pour éviter les problèmes de perte de mise à jour ou de suppression. La balise d'entité est un horodatage HTTP convivial et logique associé à une ressource. DocumentDB prend en charge de manière native le contrôle d'accès concurrentiel optimiste en s'assurant que chaque réponse HTTP contient la version (durablement) associée à la ressource spécifique. Les conflits du contrôle d'accès concurrentiel sont correctement détectés dans les cas suivants :  

1.	Si deux clients émettent simultanément des demandes en mutation (via les verbes PUT/ DELETE) sur une ressource avec la dernière version de celle-ci (spécifiée par l'en-tête de demande [if-match]), le moteur de base de données DocumentDB les soumet au contrôle d'accès concurrentiel transactionnel.
2.	Si un client présente une ancienne version de la ressource (spécifiée via l'en-tête de demande [if-match]), sa demande est rejetée.  

##Options de connectivité
DocumentDB expose un modèle d'adressage logique où chaque ressource a un URI stable et logique identifié par son lien _self. En tant que système de stockage distribué entre les régions, les ressources sous divers comptes de base de données dans DocumentDB sont partitionnées entre de nombreux ordinateurs et chaque partition est répliquée à des fins de haute disponibilité. Les réplicas qui gèrent les ressources d'une partition donnée enregistrent des adresses physiques. Alors que les adresses physiques changent au fil du temps suite aux défaillances, leurs adresses logiques restent stables et constantes. La logique de conversion des adresses physiques est conservée dans une table de routage qui est également disponible en interne en tant que ressource. DocumentDB expose deux modes de connectivité :  

1.	**Mode passerelle :** les clients sont protégés de la conversion des adresses logiques en adresses physiques ou des détails du routage ; ils s'occupent simplement des URI logiques et accèdent via RESTful au modèle de ressource. Les clients émettent les demandes à l'aide de l'URI logique qui est converti par les ordinateurs du périmètre en adresse physique du réplica qui gère la ressource et transfère la demande. Avec les ordinateurs du périmètre qui mettent en cache (et actualisent régulièrement) la table de routage, le routage est véritablement efficace. 
2.	**Mode Connectivité directe :** les clients gèrent directement la table de routage dans leur espace de traitement et l'actualisent régulièrement. Ils peuvent se connecter directement aux réplicas et ignorer les ordinateurs du périmètre.   



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
                    <strong>Kits de développement logiciel (SDK) DocumentDB</strong>
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

##Références
-	REST [http://fr.wikipedia.org/wiki/Representational_State_Transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Spécification JSON  [http://-www.ietf.org/rfc/rfc4627.txt](http://-www.ietf.org/rfc/rfc4627.txt)
-	Spécification HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Balises d'entité [http://fr.wikipedia.org/wiki/Balise-entit%C3%A9_ETag_HTTP](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Interrogation de DocumentDB](/documentation/articles/documentdb-sql-query/)
-	[Référence SQL DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
-	[Programmation DocumentDB : procédures stockées, déclencheurs et fonctions définies par l'utilisateur](/documentation/articles/documentdb-programming/)
-	[Documentation de référence DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402384)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
[2]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
