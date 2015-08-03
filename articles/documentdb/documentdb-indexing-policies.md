<properties 
    pageTitle="Stratégies d'indexation de DocumentDB | Azure" 
    description="Découvrez le fonctionnement de l’indexation dans DocumentDB et apprenez à configurer et à modifier une stratégie d’indexation." 
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
    ms.date="07/19/2015" 
    ms.author="mimig"/>


# Stratégies d’indexation de DocumentDB

DocumentDB est un véritable système de base de données sans schéma. Il ne part pas du principe que vous utilisez des schémas et n’en réclame pas pour les documents JSON qu’il indexe. Cela vous permet de définir rapidement les modèles de données d'application et d'y effectuer des itérations. Dès que vous ajoutez des documents à une collection, DocumentDB indexe automatiquement toutes les propriétés des documents et vous pouvez donc les interroger. L’indexation automatique vous permet également de stocker des types de documents hétérogènes.

L’indexation automatique de documents est permise par une technique de maintenance des index structurée par des journaux, sans verrouillage et optimisée pour l’écriture. DocumentDB prend en charge un volume soutenu d’écritures rapides, tout en continuant de servir les requêtes cohérentes.

Le sous-système d'indexation de DocumentDB est conçu pour prendre en charge les éléments suivants :

-  Requêtes relationnelles et hiérarchiques efficaces et riches sans définitions de schéma ou d’index.
-  Résultats de requête cohérents tout en traitant un volume soutenu d’écritures. Dans le cas des charges de travail à débits d’écriture élevés avec des requêtes cohérentes, l’index est mis à jour de manière incrémentielle, efficacement et en ligne, tout en traitant des volumes soutenus d’écritures.
- Efficacité du stockage. pour des raisons économiques, la surcharge de stockage sur disque de l'index est limitée et prévisible.
- Architecture mutualisée. Les mises à jour de l'index sont effectuées dans le budget des ressources système allouées par collection DocumentDB. 

Dans la plupart des applications, vous pouvez utiliser la stratégie d’indexation automatique par défaut, car elle permet une plus grande flexibilité et un bon compromis entre performance et efficacité du stockage. En revanche, la spécification d’une stratégie d’indexation personnalisée vous permet d’obtenir un compromis granulaire entre les performances des requêtes, les performances d’écriture et les coûts de stockage des index.

Par exemple, en excluant de l'indexation certains documents ou chemins d'accès au sein des documents, vous pouvez réduire à la fois l'espace de stockage utilisé pour l'indexation, ainsi que le coût du temps d'insertion pour la maintenance de l'index. Vous pouvez modifier le type d'index pour qu'il soit mieux adapté aux requêtes de plage, ou augmenter la précision de l'index en octets et améliorer ainsi les performances des requêtes. Cet article décrit les différentes options de configuration de l'indexation disponibles dans DocumentDB et explique comment personnaliser la stratégie d'indexation pour vos charges de travail.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Comment DocumentDB prend-il en charge l'indexation de toutes les propriétés par défaut ?
- Comment puis-je remplacer les propriétés à inclure ou à exclure de l'indexation ?
- Comment puis-je configurer l'index pour des mises à jour éventuelles ?
- Comment puis-je configurer l'indexation afin d'effectuer des requêtes Order By ou de plage ?

## Fonctionnement de l'indexation dans DocumentDB

L’indexation dans DocumentDB tire parti du fait que la grammaire JSON permet que les documents soient **représentés sous forme d’arborescences**. Pour qu'un document JSON soit représenté sous forme d'arborescence, il est nécessaire de créer un nœud racine factice qui comporte le reste des nœuds réels du document en dessous. Chaque étiquette incluant les index de tableau d'un document JSON devient un nœud de l'arborescence. La figure ci-dessous illustre un exemple de document JSON et sa représentation correspondante sous forme d'arborescence.

![Stratégies d’indexation](media/documentdb-indexing-policies/image001.png)

Par exemple, la propriété JSON `{"headquarters": "Belgium"}` dans l’exemple ci-dessus correspond au chemin d’accès `/headquarters/Belgium`. Le tableau JSON `{"exports": [{"city": “Moscow"}, {"city": Athens"}]}` correspond aux chemins d’accès `/exports/[]/city/Moscow` et `/exports/[]/city/Athens`.

>[AZURE.NOTE]La représentation sous forme de chemin d’accès atténue la frontière entre la structure/le schéma et les valeurs d’instance des documents, ce qui permet que DocumentDB soit véritablement sans schéma.

Dans DocumentDB, les documents sont organisés en collections qui peuvent être interrogées à l’aide de SQL ou traitées au sein de l’étendue d’une seule transaction. Chaque collection peut être configurée avec sa propre stratégie d'indexation exprimée en termes de chemins d'accès. Dans la section suivante, nous verrons comment configurer le comportement d'indexation d'une collection DocumentDB.

## Configuration de la stratégie d'indexation d'une collection

Pour chaque collection DocumentDB, vous pouvez configurer les options suivantes :

- Mode d’indexation : **Cohérent**, **Différent** (pour les mises à jour asynchrones) ou **Aucun** (seulement accès basé sur l’ID).
- Chemins d’accès inclus et exclus : choisissez les chemins d’accès dans JSON qui sont inclus et exclus.
- Type d’index :**hachage** (pour les requêtes d’égalité), **Plage** (pour les requêtes d’égalité, de plage de données et Trier par avec un espace de stockage plus élevé).
- Précision d’index : 1 à 8 ou maximale (-1) pour un compromis entre les performances et le stockage.
- Automatique : **true** ou **false** pour activer, ou **manuel** (à choisir à chaque insertion).

L’extrait de code .NET suivant montre comment définir une stratégie d’indexation personnalisée lors de la création d’une collection. Ici, nous définissons la stratégie avec un index de plage pour les chaînes et les chiffres à la précision maximale. Cette stratégie nous permet d’exécuter des requêtes Trier par sur les chaînes.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE]Le schéma JSON de la stratégie d’indexation a été modifié avec la version de l’API REST 2015-06-03 pour prendre en charge les index de plage dans des chaînes. Le Kit de développement logiciel (SDK) .NET 1.2.0 et les Kits de développement logiciel (SDK) Java, Python et Node.js 1.1.0 prennent en charge le nouveau schéma de stratégie. Des Kits de développement (SDK) plus anciens utilisent la version 2015-04-08 de l'API REST et prennent en charge le schéma de stratégie d'indexation plus ancien.
>
>Par défaut, DocumentDB indexe régulièrement toutes les propriétés de chaîne au sein des documents avec un index de hachage, et les propriétés numériques avec un index de plage.

### Modes d’indexation

Vous pouvez choisir des mises à jour d’index synchrones (**cohérentes**), asynchrones (**différées**), ou vous pouvez ne pas en choisir (**Aucune**). Par défaut, l’index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d’un document au niveau de la collection. Ainsi, les requêtes peuvent honorer le même niveau de cohérence que les lectures de document sans que l’index ne soit soumis à un quelconque délai de rattrapage.

Alors que DocumentDB est optimisé pour les écritures et prend en charge les volumes soutenus d'écritures de documents, ainsi que la maintenance synchrone des index, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l'index soit effectuée en différé. L'indexation différée est très utile pour les scénarios où les données sont écrites en rafales et que vous souhaitez amortir le travail requis pour indexer le contenu sur une longue période de temps. Cela vous permet d'utiliser efficacement le débit configuré et de répondre aux demandes d'écriture aux heures de pointe avec une latence minimale. Si l’indexation différée est activée, les résultats des requêtes seront cohérents, indépendamment du niveau de cohérence configuré pour le compte de base de données.

L'exemple suivant montre comment utiliser le Kit de développement logiciel (SDK) .NET de DocumentDB pour créer une collection DocumentDB avec une indexation automatique cohérente de toutes les insertions de document.


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### Chemins d’accès de l’index

Dans les documents, vous pouvez choisir les chemins d'accès qui doivent être inclus ou exclus de l'indexation. Il peut en résulter de meilleures performances d'écriture et un stockage des index inférieur pour les scénarios lorsque les modèles de requête sont connus au préalable.

Les chemins d’accès de l’index commencent par la racine (/) et se terminent généralement par l’opérateur générique ?, ce qui signifie qu’il y a plusieurs valeurs possibles pour le préfixe. Par exemple, pour traiter SELECT * FROM Families F WHERE F.familyName = "Andersen", vous devez inclure un chemin d'index pour /familyName/? dans la stratégie d'index de la collection.

Les chemins d'index peuvent aussi utiliser l'opérateur générique * pour spécifier le comportement des chemins de manière récursive sous le préfixe. Par exemple, /payload/* peut être utilisé pour exclure de l'indexation tout ce qui figure sous la propriété « payload ».

Voici les modèles courants de spécification des chemins d'index :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Chemin d’accès</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Description/Cas d’utilisation</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
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
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d’index requis pour traiter les requêtes comme les suivantes (avec, respectivement, les types hachage ou plage)&#160;:
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d'index pour tous les chemins d'accès sous l'étiquette spécifiée. Fonctionne avec les requêtes suivantes
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d’accès de l’index requis pour traiter l’itération et les requêtes&#160;JOIN dans les tableaux de scalaires comme ["a", "b", "c"]&#160;:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[] /subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d’accès de l’index requis pour traiter l’itération et les requêtes&#160;JOIN dans les tableaux d’objets comme [{subprop: "a"}, {subprop: "b"}]&#160;:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Chemin d’index requis pour traiter les requêtes (avec, respectivement, les types hachage ou plage)&#160;:
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Lors de la définition des chemins d’accès de l’index personnalisé, il est nécessaire de spécifier la règle d’indexation par défaut pour la totalité de l’arborescence du document, désignée par le chemin d’accès spécial « /* ».

L’exemple suivant configure un chemin d’accès spécifique avec l’indexation de plage et une valeur personnalisée de précision de 20 octets :

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### Types de données d’index, types d’index et précisions d’index

Maintenant que nous avons vu comment spécifier des chemins d’accès, examinons les options que nous pouvons utiliser pour configurer la stratégie d’indexation pour un chemin d’accès. Vous pouvez spécifier une ou plusieurs définitions d’indexation pour chaque chemin d’accès :

- Type de données : **chaîne** ou **nombre** (ne pouvant contenir qu’une seule entrée par type de données par chemin d’accès)
- Type d’index : **hachage** (requêtes d’égalité) ou **plage** (requêtes d’égalité, de plage ou requêtes Trier par)
- Précision : 1 à 8 ou -1 (précision maximale) pour les nombres, 1 à 100 (précision maximale) pour les chaînes

#### Type d’index

DocumentDB prend en charge deux types d’index pour chaque paire de type de données et de chemin d’accès.

- **Hachage** prend en charge les requêtes d’égalité efficaces. Dans la plupart des cas d’utilisation, les index de hachage ne nécessitent pas une précision plus élevée que la valeur par défaut de 3 octets.
- **Plage** prend en charge les requêtes d’égalité efficaces, les requêtes de plage (avec >, <>, =, < =,! =) et les requêtes Trier par. Par défaut, les requêtes Trier par nécessitent également une précision d’index maximale (-1).

#### Précision d’index

La précision d’index vous permet trouver un compromis entre le traitement du stockage de l’index et les performances des requêtes. Pour les nombres, nous recommandons d’utiliser la configuration de précision par défaut définie sur -1. Comme les nombres correspondent à 8 octets dans JSON, cela équivaut à une configuration de 8 octets. Si vous choisissez une valeur inférieure pour la précision, par exemple 1 à 7, les valeurs de certaines plages sont mappées à la même entrée d’index. Ce faisant, vous réduisez l’espace de stockage des index, mais l’exécution des requêtes peut devoir traiter plus de documents et, par conséquent, consommer davantage de débit, c’est-à-dire d’unités de demande.

La configuration de la précision d’index est plus pratique avec les plages de chaînes. Comme les chaînes peuvent avoir n’importe quelle longueur arbitraire, le choix de la précision d’index peut avoir des conséquences sur les performances des requêtes de plage de chaînes et sur l’espace de stockage requis pour les index. Les index de plage de chaînes peuvent être configurés avec une valeur comprise entre 1 et 100, ou la valeur de précision maximale (-1). Si vous devez exécuter une requête Trier par sur des chaînes, vous devez préciser le chemin d’accès spécifié (-1).

L’exemple suivant montre comment augmenter la précision des index de plage d’une collection à l’aide du Kit de développement (SDK) .NET. Notez qu’il utilise le chemin d’accès par défaut « /* ».

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE]DocumentDB retourne une erreur lorsqu’une requête utilise Trier par, mais n’a pas d’index de plage par rapport au chemin d’accès de requête avec la précision maximale.
>
> Une erreur est retournée pour des requêtes avec des opérateurs de plage comme >= s’il n’existe aucun index de plage (de toute précision), mais celles-ci peuvent être traitées s’il existe d’autres filtres pouvant être traités par l’index.
> 
> Les requêtes peuvent être effectuées sans un index de plage à l'aide de l'en-tête x-ms-documentdb-enable-scans header dans l'API REST ou l'option de requête EnableScanInQuery à l'aide du Kit de développement logiciel (SDLK) .NET.

De même, des chemins d’accès peuvent être exclus complètement de l’indexation. L'exemple suivant montre comment exclure toute une section de documents (également appelé une sous-arborescence) de l'indexation à l'aide du caractère générique « * ».

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


### Indexation automatique

Vous pouvez choisir si vous souhaitez que la collection indexe automatiquement tous les documents. Par défaut, tous les documents sont indexés automatiquement, mais vous pouvez choisir de désactiver cette option. Lorsque l’indexation est désactivée, les documents sont accessibles uniquement par le biais de leurs liens réflexifs ou de requêtes avec l’ID.

Si l'indexation automatique est désactivée, vous ne pouvez continuer à ajouter des documents spécifiques à l'index que de façon sélective. À l'inverse, vous pouvez laisser l'indexation automatique activée et choisir ne d'exclure de façon sélective que des documents spécifiques. Les configurations d'indexation activée/désactivée sont utiles lorsque vous n'avez qu'un sous-ensemble de documents à interroger.

Ainsi, l’exemple suivant montre comment inclure un document explicitement à l’aide du [Kit de développement logiciel (SDK) .NET DocumentDB](https://github.com/Azure/azure-documentdb-java) et de la propriété [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Réglage des performances

Les API DocumentDB fournissent des informations sur les mesures des performances telles que le stockage d’index utilisé et le coût du débit (unités de demande) pour chaque opération. Ces informations peuvent être utilisées pour comparer différentes stratégies d’indexation et pour le réglage des performances.

Pour vérifier le quota de stockage et l’utilisation d’une collection, exécutez une demande HEAD ou GET sur la ressource de collection et examinez les en-têtes x-ms-request-quota et x-ms-request-usage. Dans le Kit de développement (SDK) .NET, les propriétés [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) et [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contiennent ces valeurs correspondantes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Pour mesurer la surcharge de l’indexation sur chaque opération d’écriture (création, mise à jour ou suppression), inspectez l’en-tête x-ms-request-charge (ou la propriété [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) équivalente dans [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) au sein du Kit de développement logiciel (SDK) .NET) qui permet de mesurer le nombre d’unités de demande consommées par ces opérations.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## Modifications apportées à la spécification de la stratégie d'indexation
Une modification dans le schéma de la stratégie d'indexation a été introduite le 7 juillet 2015 avec la version 2015-06-03 de l'API REST. Les classes correspondantes dans les versions du Kit de développement logiciel (SDK) ont de nouvelles implémentations pour correspondre au schéma.

Les modifications suivantes ont été implémentées dans la spécification JSON :

- La stratégie d'indexation prend en charge les index de plage pour les chaînes
- Chaque chemin d'accès peut avoir plusieurs définitions d'index, un pour chaque type de données
- L'indexation de précision prend en charge les nombres de 1 à 8, les chaînes de 1 à 100 et -1 (précision maximale)
- Les segments des chemins d'accès ne nécessitent pas de doubles guillemets pour éviter chaque chemin d'accès. Par exemple, vous pouvez ajouter un chemin d’accès pour /title/? au lieu de /"title"/?
- Le chemin d'accès racine représentant « tous les chemins d'accès » peut être représenté comme /* (en plus de /)

Si votre code approvisionne des collections avec une stratégie d'indexation personnalisée écrite avec la version 1.1.0 du Kit de développement logiciel (SDK) .NET ou une version antérieure, vous devrez modifier le code de votre application pour gérer ces modifications afin de les déplacer vers la version 1.2.0 du Kit de développement logiciel (SDK). Si vous n’avez pas le code qui configure la stratégie d'indexation, ou si vous envisagez de continuer à l'aide d'une version du Kit de développement logiciel (SDK) plus ancienne, aucune modification n'est requise.

À titre de comparaison pratique, voici un exemple de stratégie d’indexation personnalisée écrite à l’aide de l’API REST version 2015-06-03 et de la version précédente 2015-04-08.

**Stratégie d’indexation JSON précédente**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**Stratégie d’indexation JSON actuelle**

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
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## Étapes suivantes

Suivez les liens ci-dessous pour accéder à des exemples de gestion de stratégie d’index et pour en savoir plus sur le langage de requête de DocumentDB.

1.	[Exemples de code de gestion d’index DocumentDB .NET](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[Opérations sur la collection de l’API REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[Interrogation avec le langage SQL de DocumentDB](documentdb-sql-query.md)

 

<!---HONumber=July15_HO4-->