<properties 
	pageTitle="Stratégies d'indexation de DocumentDB | Azure" 
	description="Découvrez le fonctionnement de l'indexation dans DocumentDB et apprenez à configurer une stratégie d'indexation." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/13/2015" 
	ms.author="mimig"/>


Stratégies d'indexation de DocumentDB
============================

DocumentDB est un véritable système de base de données sans schéma. Il ne part pas du principe que vous utilisez des schémas et n'en réclame pas pour les documents JSON qu'il indexe. Cela vous permet de définir rapidement les modèles de données d'application et d'y effectuer des itérations. Dès que vous ajoutez des documents à une collection, DocumentDB indexe automatiquement toutes les propriétés des documents et vous pouvez donc les interroger. L'indexation automatique vous permet également de stocker des types de documents hétérogènes.

L'indexation automatique de documents est permise par une technique de maintenance des index structurée par des journaux, sans verrouillage et optimisée pour l'écriture. DocumentDB prend en charge un volume soutenu d'écritures rapides, tout en continuant de servir les requêtes cohérentes.

Le sous-système d'indexation de DocumentDB est conçu pour prendre en charge les éléments suivants :

·         Requêtes relationnelles et hiérarchiques efficaces et riches sans définitions de schéma ou d'index.

·         Résultats de requête cohérents tout en traitant un volume soutenu d'écritures. Dans le cas des charges de travail à débits d'écriture élevés avec des requêtes cohérentes, l'index est mis à jour de manière incrémentielle, efficacement et en ligne, tout en traitant des volumes soutenus d'écritures.

·         Efficacité du stockage. pour des raisons économiques, la surcharge de stockage sur disque de l'index est limitée et prévisible.

·         Architecture mutualisée. Les mises à jour de l'index sont effectuées dans le budget des ressources système allouées par collection DocumentDB.

Dans la plupart des applications, vous pouvez utiliser la stratégie d'indexation automatique par défaut, car elle permet une plus grande flexibilité et un bon compromis entre performance et efficacité du stockage. En revanche, la spécification d'une stratégie d'indexation personnalisée vous permet d'obtenir un compromis granulaire entre les performances des requêtes, les performances d'écriture et les coûts de stockage des index.

Par exemple, en excluant de l'indexation certains documents ou chemins d'accès au sein des documents, vous pouvez réduire à la fois l'espace de stockage utilisé pour l'indexation, ainsi que le coût du temps d'insertion pour la maintenance de l'index. Vous pouvez modifier le type d'index pour qu'il soit mieux adapté aux requêtes de plage, ou augmenter la précision de l'index en octets et améliorer ainsi les performances des requêtes. Cet article décrit les différentes options de configuration de l'indexation disponibles dans DocumentDB et explique comment personnaliser la stratégie d'indexation pour vos charges de travail.

<a id="HowWorks"></a>Fonctionnement de l'indexation dans DocumentDB
-----------------------------

L'indexation au sein de DocumentDB tire parti du fait que la grammaire JSON permet que les documents soient **représentés sous forme d'arborescences**. Pour qu'un document JSON soit représenté sous forme d'arborescence, il est nécessaire de créer un nœud racine factice qui comporte le reste des nœuds réels du document en dessous. Chaque étiquette incluant les index de tableau d'un document JSON devient un nœud de l'arborescence. La figure ci-dessous illustre un exemple de document JSON et sa représentation correspondante sous forme d'arborescence.

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


Par exemple, la propriété JSON {"headquarters": "Belgium"} de l'exemple ci-dessus correspond au chemin /"headquarters"/"Belgium". Le tableau JSON {"exports": [{"city": "Moscow"}, {"city": Athens"}]} correspond aux chemins /"exports"/0/"city"/"Moscow" et /"exports"/1/"city"/"Athens".

**Remarque** La représentation sous forme de chemin d'accès atténue la frontière entre la structure/le schéma et les valeurs d'instance des documents, ce qui permet que DocumentDB soit véritablement sans schéma.

Dans DocumentDB, les documents sont organisés en collections qui peuvent être interrogées à l'aide de SQL ou traitées au sein de l'étendue d'une seule transaction. Chaque collection peut être configurée avec sa propre stratégie d'indexation exprimée en termes de chemins d'accès. Dans la section suivante, nous verrons comment configurer le comportement d'indexation d'une collection DocumentDB.

<a id="ConfigPolicy"></a>Configuration de la stratégie d'indexation d'une collection
-------------------------------------------

L'exemple suivant montre comment définir une stratégie d'indexation personnalisée au moment de la création d'une collection, à l'aide de l'API REST de DocumentDB. L'exemple illustre la stratégie d'indexation exprimée en termes de chemins d'accès, de types d'index et de précisions.


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "id":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/"nonIndexedContent"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**Remarque** : la stratégie d'indexation d'une collection doit être spécifiée au moment de la création. Il n'est pas possible actuellement de modifier la stratégie d'indexation après la création de la collection, mais cette fonctionnalité sera prise en charge dans une future version de DocumentDB.

**Remarque** : par défaut, DocumentDB indexe régulièrement tous les chemins d'accès au sein des documents avec un index de hachage. Le chemin d'accès interne Timestamp (_ts) est stocké avec un index de plage.

### Indexation automatique

Vous pouvez choisir si vous souhaitez que la collection indexe tous les documents automatiquement ou non. Par défaut, tous les documents sont indexés automatiquement, mais vous pouvez choisir de désactiver cette option. Lorsque l'indexation est désactivée, les documents sont accessibles uniquement par le biais de leurs liens réflexifs ou de requêtes avec l'ID.

Si l'indexation automatique est désactivée, vous ne pouvez continuer à ajouter des documents spécifiques à l'index que de façon sélective. À l'inverse, vous pouvez laisser l'indexation automatique activée et choisir ne d'exclure de façon sélective que des documents spécifiques. Les configurations d'indexation activée/désactivée sont utiles lorsque vous n'avez qu'un sous-ensemble de documents à interroger.

Vous pouvez configurer la stratégie par défaut en spécifiant la valeur de la propriété automatique comme true ou false. Pour remplacer un document unique, vous pouvez définir l'en-tête de demande x-ms-indexingdirective pendant l'insertion ou le remplacement d'un document.

Par exemple, l'exemple suivant montre comment inclure un document explicitement à l'aide du [Kit de développement logiciel (SDK) .NET DocumentDB](https://github.com/Azure/azure-documentdb-java) et de la propriété [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Id = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### Modes d'indexation

Vous avez le choix entre les mises à jour d'index synchrones (**Consistent**) et asynchrones (**Lazy**). Par défaut, l'index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d'un document au niveau de la collection. Ainsi, les requêtes peuvent honorer le même niveau de cohérence que les lectures de document sans que l'index ne soit soumis à un quelconque délai de rattrapage.

Alors que DocumentDB est optimisé pour les écritures et prend en charge les volumes soutenus d'écritures de documents, ainsi que la maintenance synchrone des index, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l'index soit effectuée en différé. L'indexation différée est très utile pour les scénarios où les données sont écrites en rafales et que vous souhaitez amortir le travail requis pour indexer le contenu sur une longue période de temps. Cela vous permet d'utiliser efficacement le débit configuré et de répondre aux demandes d'écriture aux heures de pointe avec une latence minimale. Si l'indexation différée est activée, les résultats des requêtes seront cohérents, indépendamment du niveau de cohérence configuré pour le compte de base de données.

L'exemple suivant montre comment utiliser le Kit de développement logiciel (SDK) .NET de DocumentDB pour créer une collection DocumentDB avec une indexation automatique cohérente de toutes les insertions de document.


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Id ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### Types d'index et précision

Le type ou le schéma utilisé pour les entrées d'index a un impact direct sur les performances et le stockage des index. Pour un schéma utilisant une précision plus élevée, les requêtes sont généralement plus rapides. Toutefois, la surcharge de stockage de l'index est plus élevée. Le choix d'une précision inférieure signifie qu'un plus grand nombre de documents doit être traité pendant l'exécution des requêtes, mais que la surcharge de stockage sera inférieure.

La précision de l'index pour les valeurs d'un chemin d'accès peut être comprise entre 3 et 7 octets.
Dans la mesure où le même chemin d'accès peut avoir des valeurs numériques et des valeurs de chaîne dans différents documents, celles-ci peuvent être contrôlées séparément. Dans le Kit de développement logiciel (SDK) .NET, ces valeurs correspondent aux propriétés [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) et [StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx).

Il existe deux types de prise en charge des types d'index : plage et hachage. Le choix du type d'index **hachage** permet des requêtes d'efficacité égale. Dans la plupart des cas d'utilisation, les index de hachage ne nécessitent pas une précision plus élevée que la valeur par défaut de 3 octets.

Le choix du type d'index **plage** permet les requêtes de plage (à l'aide de >, <, >=, <=, !=). Pour les chemins qui ont de grandes plages de valeurs, il est recommandé d'utiliser une précision plus élevée telle que 6 octets. Un cas courant d'utilisation qui requiert un index de plage de précision plus élevée est celui des horodateurs stockés comme heure d'époque.

Si votre cas d'utilisation ne nécessite pas des requêtes de plage efficaces, la valeur par défaut des index de hachage offre le meilleur compromis entre le stockage et les performances. Notez que pour prendre en charge les requêtes de plage, vous devez spécifier une stratégie d'index personnalisée.

> [AZURE.NOTE] Les index de plage ne sont pris en charge que pour les valeurs numériques..
  
L'exemple suivant montre comment augmenter la précision des index de plage d'une collection à l'aide du Kit de développement (SDK) .NET. Notez que l'exemple utilise un chemin d'accès spécial " / " - voir les explications de la section suivante.


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Id = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### Chemins d'accès de l'index

Dans les documents, vous pouvez choisir les chemins d'accès qui doivent être inclus ou exclus de l'indexation. Il peut en résulter de meilleures performances d'écriture et un stockage des index inférieur pour les scénarios lorsque les modèles de requête sont connus au préalable.

Les chemin d'accès de l'index commencent par la racine (/) et se terminent généralement par l'opérateur générique ?, ce qui signifie qu'il y a plusieurs valeurs possibles pour le préfixe. Par exemple, pour traiter SELECT * FROM Families F WHERE F.familyName = "Andersen", vous devez inclure un chemin d'index pour /"familyName"/? dans la stratégie d'index de la collection.

Les chemins d'index peuvent aussi utiliser l'opérateur générique * pour spécifier le comportement des chemins de manière récursive sous le préfixe. Par exemple, /"payload"/* peut être utilisé pour exclure de l'indexation tout ce qui figure sous la propriété " payload ".

Voici les modèles courants de spécification des chemins d'index :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Chemin d'accès</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Description/cas d'utilisation</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin par défaut de la collection. Récursif et s'applique à toute l'arborescence du document.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d'index requis pour traiter les requêtes comme les suivantes (avec, respectivement, les types hachage ou plage) :
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d'index pour tous les chemins d'accès sous l'étiquette spécifiée. Spécifiée uniquement pour l'exclusion de l'indexation.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d'index utilisé pendant l'exécution de la requête pour nettoyer les documents qui n'ont pas le chemin d'accès spécifié.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d'index requis pour traiter les requêtes (avec, respectivement, les types hachage ou plage) :
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] Lors de la définition des chemins d'accès de l'index personnalisé, il est nécessaire de spécifier la règle d'indexation par défaut pour la totalité de l'arborescence du document, désignée par le chemin d'accès spécial " / ".

L'exemple suivant configure un chemin spécifique avec l'indexation de plage et une valeur personnalisée de précision de 7 octets :


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Id = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/"CreatedTimestamp"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


DocumentDB renvoie une erreur lorsqu'une requête utilise un opérateur de plage, mais n'a pas d'index de plage sur le chemin demandé ni d'autre filtre pouvant être fourni par l'index. Cependant, ces requêtes peuvent toujours être exécutées sans index de plage à l'aide de l'en-tête x-ms-documentdb-allow-scans de l'API REST ou de l'option AllowScanInQueryrequest à l'aide du Kit de développement logiciel (SDK) .NET.

L'exemple suivant exclut de l'indexation une sous-arborescence de chemins à l'aide du
caractère générique " * ".

	var excluded = new DocumentCollection { Id = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/" nonIndexedContent"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


Réglage des performances
------------------

Lorsque vous évaluez différentes configurations de stratégie d'indexation, il convient de mesurer les implications de débit et de stockage de la stratégie via les API DocumentDB.

Pour vérifier le quota de stockage et l'utilisation d'une collection, exécutez une demande HEAD ou GET sur la ressource de collection et examinez les en-têtes x-ms-request-quota et x-ms-request-usage. Dans le Kit de développement (SDK) .NET, les propriétés [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) et [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) dans [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contiennent ces valeurs correspondantes.


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


Pour mesurer la charge de l'indexation de chaque opération d'écriture (création, mise à jour ou suppression), inspectez l'en-tête x-ms-request-charge (ou la propriété [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) équivalente dans [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) au sein du Kit de développement logiciel .NET) qui permet de mesurer le nombre d'unités de requête consommées par ces opérations.


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=49-->