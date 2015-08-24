<properties 
    pageTitle="Utilisation de données géographiques dans Azure DocumentDB | Microsoft Azure" 
    description="Comprendre comment créer, indexer et interroger les objets spatiaux avec Azure DocumentDB." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="08/11/2015" 
    ms.author="arramac"/>
    
# Utilisation de données géospatiales dans Azure DocumentDB

Cet article est une introduction aux fonctionnalités géospatiales dans [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/). Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Comment puis-je stocker des données spatiales dans Azure DocumentDB ?
- Comment puis-je interroger des données géographiques dans Azure DocumentDB dans SQL et LINQ ?
- Comment puis-je activer ou désactiver l'indexation spatiale dans DocumentDB ?

Consultez ce [projet Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Queries.Spatial) pour obtenir des échantillons de code.

## Présentation des données spatiales

Les données spatiales décrivent la position et la forme des objets dans l'espace. Dans la plupart des applications, ils correspondent aux objets sur terre, c'est-à-dire aux données géographiques. Les données spatiales peuvent servir à représenter l'emplacement d'une personne, d'un point d'intérêt ou de la limite d'une ville ou un lac. Les scénarios d'utilisation courants impliquent souvent des requêtes de proximité, comme « rechercher tous les cafés près de mon emplacement actuel ».

### GeoJSON
DocumentDB prend en charge l'indexation et l’interrogation des données géographique représentées à l'aide de la [spécification GeoJSON](http://geojson.org/geojson-spec.html). Les structures de données GeoJSON sont toujours des objets JSON valides, afin de pouvoir les stocker et les interroger à l'aide de DocumentDB, sans bibliothèques ou outils spécialisés. Les kits de développement logiciel (SDK) DocumentDB fournissent des classes d'assistance et des méthodes qui facilitent la manipulation des données spatiales.

### Points, chaînes de ligne et polygones
Un **point** désigne une position unique dans l'espace. Dans les données géographiques, un point représente l'emplacement exact, qui peut être une adresse postale d'une épicerie, un kiosque, une voiture ou une ville. Un point est représenté dans GeoJSON (et DocumentDB) à l'aide de sa paire de coordonnées ou de longitude et latitude. Voici un exemple JSON pour un point.

**Points dans DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE]La spécification GeoJSON spécifie la longitude d’abord, puis la latitude. Comme dans d'autres applications de mappage, la longitude et la latitude sont des angles et sont exprimées en degrés. Les valeurs de longitude sont mesurées à partir du premier méridien et sont comprises entre -180 et 180 degrés. Les valeurs de latitude sont mesurées à partir de l'Équateur et sont comprises entre -90 et 90 degrés.
>
> DocumentDB interprète les coordonnées représentées par le système de référence WGS-84. Voir ci-dessous pour plus d'informations sur les systèmes de coordonnées de référence.

Cela peut être incorporé dans un document DocumentDB, comme illustré dans cet exemple d'un profil utilisateur contenant des données d'emplacement :

**Utilisation de profil avec emplacement stocké dans DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

En plus des points, GeoJSON prend en charge les polygones et LineStrings. Les **LineStrings** représentent une série de deux ou plusieurs points dans l'espace et les segments de ligne qui les connectent. Dans les données géographiques, les Linestrings sont généralement utilisées pour représenter les autoroutes ou les cours d'eau. Un **polygone** est une limite de points reliés qui constitue une LineString fermée. Les polygones sont couramment utilisés pour représenter des formations naturelles comme des lacs ou des juridictions politiques comme les villes et les États. Voici un exemple d'un polygone dans DocumentDB.

**Polygones dans DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE]La spécification GeoJSON nécessite que, pour les polygones valides, la dernière paire de coordonnées fournie soit identique à la première, pour créer une forme fermée.
>
>Les points dans un polygone doivent être spécifiés dans le sens antihoraire. Un polygone spécifié dans le sens horaire représente l'inverse de la région qu'il contient.

En plus des points, des LineStrings et des polygones, GeoJSON spécifie également la représentation pour le regroupement de plusieurs emplacements géographiques, ainsi que l’association de propriétés arbitraires avec la géolocalisation comme **fonctionnalité**. Étant donné que ces objets sont des JSON valides, ils peuvent tous être stockés et traités dans DocumentDB.

### Coordination des systèmes de référence

Étant donné que la forme de la terre est irrégulière, les coordonnées des données géospatiales sont représentées dans de nombreux systèmes de coordonnées de référence (CRS), chacun ayant ses propres images de référence et unités de mesure. Par exemple, le « National Grid of Britain » est un système de référence très précis pour le Royaume-Uni, mais pas à l'extérieur.

Le CRS moderne le plus populaire est le World Geodetic System [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). Les périphériques GPS et de nombreux services de mappage, notamment les API Bing Maps et Google Maps, utilisent le WGS-84. DocumentDB prend en charge l'indexation et l'interrogation de données géographiques à l'aide de la CRS WGS 84 uniquement.

## Création de documents avec les données spatiales
Lorsque vous créez des documents qui contiennent des valeurs GeoJSON, ils sont automatiquement indexés avec un index spatial conformément à la stratégie d'indexation de la collection. Si vous travaillez avec un Kit de développement logiciel (SDK) DocumentDB dans un langage saisi dynamiquement comme Python ou Node.js, vous devez créer un GeoJSON valide.

**Création d’un document avec les données géographiques dans Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(collectionLink, userProfileDocument, function (err, created) {
        // additional code within the callback
    });

Si vous travaillez avec les Kits de développement logiciel (SDK) .NET (ou Java), vous pouvez utiliser les nouvelles classes de points et de polygones dans l'espace de noms Microsoft.Azure.Documents.Spatial pour incorporer des informations d'emplacement au sein des objets de votre application. Ces classes permettent de simplifier la sérialisation et la désérialisation de données spatiales dans GeoJSON.

**Création d’un document avec les données géographiques dans .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        collection.SelfLink, 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Si vous n'avez pas les informations de latitude et de longitude, mais disposez des adresses physiques ou du nom d'emplacement comme la ville ou le pays, vous pouvez rechercher les coordonnées réelles à l'aide d'un service de géocodage comme Bing Maps REST Services. En savoir plus sur le géocodage de Bing Maps [ici](https://msdn.microsoft.com/library/ff701713.aspx).

## Interrogation des types spatiaux

Maintenant que nous avons vu comment insérer des données géographiques, examinons comment interroger ces données à l'aide de DocumentDB avec SQL et LINQ.

### Fonctions spatiales SQL intégrées
DocumentDB prend en charge les fonctions intégrées Open Geospatial Consortium (OGC) suivantes pour les requêtes géospatiales. Pour plus d'informations sur l'ensemble complet de fonctions intégrées dans le langage SQL, reportez-vous à [Requête DocumentDB](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Renvoie la distance entre les deux&#160;expressions de point de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Renvoie une expression booléenne qui indique si le point de GeoJSON spécifié dans le premier argument est dans le polygone&#160;GeoJSON dans le deuxième&#160;argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Renvoie une valeur booléenne indiquant si l'expression de points ou de polygones&#160;GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie une valeur&#160;JSON contenant une valeur booléenne si l'expression de points ou de polygones&#160;GeoJSON spécifiée est valide et si elle est non valide, le motif sous forme de valeur de chaîne.</td>
</tr>
</table>

Les fonctions spatiales peuvent être utilisées pour effectuer des requêtes de proximité par rapport aux données spatiales. Par exemple, voici une requête qui retourne tous les documents de famille se trouvant dans un rayon de 30 kilomètres de l'emplacement spécifié à l'aide de la fonction intégrée ST\_DISTANCE.

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Si vous incluez l'indexation spatiale dans votre stratégie d'indexation, les « requêtes à distance » seront servies efficacement dans l'index. Pour plus d'informations sur l'indexation spatiale, consultez la section ci-dessous. Si vous n'avez pas un index spatial pour les chemins d'accès spécifiés, vous pouvez quand même effectuer des requêtes spatiales en spécifiant l’en-tête de requête `x-ms-documentdb-query-enable-scan` avec la valeur définie sur « true ». Dans .NET, cela est possible en passant l’argument facultatif **FeedOptions** aux requêtes avec [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) défini sur true.

ST\_WITHIN peut être utilisé pour vérifier si un point se trouve dans un polygone. Généralement, les polygones sont utilisés pour représenter des limites comme les codes postaux, les frontières d'états ou les formations naturelles. Si vous incluez l'indexation spatiale dans votre stratégie d'indexation, les requêtes « within » seront servies efficacement dans l'index.

Les arguments de polygone dans ST\_WITHIN peuvent contenir un seul cercle, cela signifie que les polygones ne doivent pas contenir de trous. Vérifiez les [limites DocumentDB](documentdb-limits.md) pour obtenir le nombre maximal de points autorisés dans un polygone pour une requête ST\_WITHIN.

**Requête**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
    	'type':'Polygon', 
    	'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Résultats**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE]Tout comme pour les types non correspondants dans une requête DocumentDB, si la valeur de l'emplacement spécifié dans un argument est incorrecte ou non valide, elle prend alors la valeur **indéfinie** et le document évalué est ignoré des résultats de requête. Si votre requête ne retourne aucun résultat, exécutez ST\_ISVALIDDETAILED afin de déboguer l’absence de validité du type spatial.

ST\_ISVALID et ST\_ISVALIDDETAILED peuvent être utilisés pour vérifier si un objet spatial est valide. Par exemple, la requête suivante vérifie la validité d'un point avec une valeur de latitude hors limites (-132.8). ST\_ISVALID retourne simplement une valeur booléenne et ST\_ISVALIDDETAILED renvoie la valeur booléenne et une chaîne contenant la raison pour laquelle il est non valide.

\*\* Requête \*\*

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Résultats**

    [{
      "$1": false
    }]

Ces fonctions peuvent également être utilisées pour valider des polygones. Par exemple, nous utilisons ici ST\_ISVALIDDETAILED pour valider un polygone non fermé.

**Requête**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
    	[ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
    	]]})

**Résultats**

    [{
       "$1": { 
      	  "valid": false, 
      	  "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
      	}
    }]
    
### Interrogation LINQ dans le Kit de développement logiciel (SDK) .NET

Le Kit de développement logiciel (SDK) .NET DocumentDB fournit également les méthodes de stub `Distance()` et `Within()` pour une utilisation dans des expressions LINQ. Le fournisseur DocumentDB LINQ traduit ces appels de méthode pour les appels de fonction intégrés SQL équivalents (ST\_DISTANCE et ST\_WITHIN, respectivement).

Voici un exemple d'une requête LINQ qui recherche tous les documents de la collection DocumentDB dont la valeur « location » est dans un rayon de 30 kilomètres du point spécifié à l'aide de LINQ.

**Requête LINQ de distance**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

De même, voici une requête pour rechercher tous les documents dont la « location » est dans la zone/polygone spécifié.

**Requête LINQ within**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Maintenant que nous vu l'interrogation de documents à l'aide de LINQ et SQL, examinons comment configurer DocumentDB pour l'indexation spatiale.

## Indexation

Comme décrit dans le livre [Schema Agnostic Indexing with Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), nous avons conçu le moteur de base de données DocumentDB pour être véritablement indépendant du schéma et fournir une assistance exceptionnelle pour JSON. Le moteur de base de données optimisé en écriture de DocumentDB comprend maintenant également les données spatiales représentées dans la norme GeoJSON en mode natif.

En bref, la géométrie est projetée à partir des coordonnées géodésiques sur un plan 2D, puis divisée progressivement en cellules à l'aide un **quadtree**. Ces cellules sont mappées en 1D selon l'emplacement de la cellule dans une **courbe de remplissage d'espace de Hilbert** qui permet de préserver la localité des points. En outre lorsque les données d'emplacement sont indexées, il passe par un processus connu sous le nom de **pavage**, c'est-à-dire que toutes les cellules qui se croisent à un emplacement sont identifiées et stockées en tant que clés dans l'index DocumentDB. Au moment de la requête, des arguments comme les points et les polygones sont également fractionnés pour extraire les plages d'ID de cellule appropriées, puis utilisés pour récupérer des données à partir de l'index.

Si vous spécifiez une stratégie d'indexation qui inclut un index spatial pour /\* (tous les chemins d'accès), tous les points trouvés dans la collection sont indexés pour des requêtes spatiales efficaces (ST\_WITHIN et ST\_DISTANCE). Les index spatiaux n'ont pas une valeur de précision et utilisent toujours une valeur de précision par défaut.

L'extrait JSON suivant montre une politique d'indexation avec l’indexation spatiale activée, c'est-à-dire n'importe quel point GeoJSON trouvé au sein de documents pour l'interrogation spatiale d'index. Si vous modifiez la stratégie d'indexation à l'aide du portail Azure en version préliminaire, vous pouvez spécifier le JSON suivant pour la stratégie d'indexation pour activer l’indexation spatiale pour votre collection.

**Stratégie d’indexation de collection JSON avec Spatial activé**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                }
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Voici un extrait de code dans .NET qui montre comment créer une collection avec l’indexation spatiale activée pour tous les chemins d'accès contenant des points.

**Création d’une collection avec l'indexation spatiale**

    IndexingPolicy spatialIndexingPolicy = new IndexingPolicy();
    spatialIndexingPolicy.IncludedPaths.Add(new IncludedPath
    {
        Path = "/*",
        Indexes = new System.Collections.ObjectModel.Collection<Index>()
            {
                new RangeIndex(DataType.Number) { Precision = -1 },
                new RangeIndex(DataType.String) { Precision = -1 },
                new SpatialIndex(DataType.Point)
            }
    });

    Console.WriteLine("Creating new collection...");
    collection = await client.CreateDocumentCollectionAsync(dbLink, collectionDefinition);

Voici comment vous pouvez modifier un regroupement existant pour tirer parti de l'indexation spatiale sur tous les points stockés au sein de documents.

**Modification d’une collection existante avec l'indexation spatiale**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = spatialIndexingPolicy; 
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(collection.SelfLink);
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE]Si la valeur GeoJSON d'emplacement dans le document est incorrecte ou non valide, elle n’est pas indexée pour les requêtes spatiales. Vous pouvez valider les valeurs d'emplacement à l'aide de ST\_ISVALID et ST\_ISVALIDDETAILED.

## Étapes suivantes
Maintenant que vous avez appris à utiliser la prise en charge géographique dans DocumentDB, vous pouvez :

- Commencer à coder avec les [exemples de code .NET Geospatial sur Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Queries.Spatial)
- Découvrir l’interrogation géographique sur le [DocumentDB Query Playground](www.documentdb.com/sql/demo)
- En savoir plus sur les [requêtes DocumentDB](documentdb-sql-query.md)
- En savoir plus sur les [stratégies d'indexation DocumentDB](documentdb-indexing-policies.md).

<!---HONumber=August15_HO7-->