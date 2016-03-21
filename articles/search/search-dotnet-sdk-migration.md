<properties
   pageTitle="Mise à niveau vers la version du Kit de développement logiciel Azure Search .NET SDK version 1.1 | Microsoft Azure | Service de recherche cloud hébergé"
   description="Mise à niveau vers la version du Kit de développement logiciel Azure Search .NET SDK version 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/09/2016"
   ms.author="brjohnst"/>

# Mise à niveau vers la version du Kit de développement logiciel Azure Search .NET SDK version 1.1

Si vous utilisez la version préliminaire 1.0.2 ou une version antérieure du [Kit de développement logiciel (SDK) .NET Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx), cet article vous aidera à mettre à niveau votre application pour utiliser la version la plus récente, la version 1.1 mise à la disposition générale.

Pour avoir un aperçu général du kit de développement logiciel et avoir des exemples, voir [Comment utiliser Azure Search à partir d’une application .NET](search-howto-dotnet-sdk.md).

La version 1.1 du Kit de développement logiciel (SDK) .NET Azure Search contient plusieurs modifications par rapport aux versions antérieures à la version préliminaire 1.0.0 (version préliminaire 0.13.0 et versions antérieures incluses). Elles sont pour la plupart mineures, et donc, la modification de votre code devrait ne nécessiter que peu d’effort. Consultez la page [Procédure de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier le code à utiliser avec la nouvelle version du kit de développement logiciel.

<a name="WhatsNew"></a>
## Nouveautés dans la version 1.1

La version 1.1 cible la même version d'API REST que les versions antérieures du Kit de développement logiciel (SDK) .NET Azure Search (28/02/2015). Cette version ne propose donc pas de nouvelle fonctionnalité de service. Toutefois, il existe de nouvelles fonctionnalités de sérialisation côté client.

Le kit de développement logiciel utilise JSON.NET pour sérialiser et désérialiser les documents. La nouvelle version du kit de développement logiciel prend en charge la sérialisation personnalisée via `JsonConverter` et `IContractResolver` (voir la [documentation JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) pour plus de détails). Cela peut s’avérer utile lorsque vous souhaitez adapter une classe de modèle existante de votre application à utiliser avec Azure Search et d’autres scénarios plus avancés. Par exemple, avec la sérialisation personnalisée, vous pouvez :

 - Incluez ou exclure certaines propriétés de votre classe de modèle dans le stockage en tant que champs de document.
 - Mappage des noms de propriété dans le code et des noms de champ de votre index.
 - Créez des attributs personnalisés pouvant être utilisés à la fois pour le mappage des propriétés sur les champs du document champs du document et pour la création de la définition d’index correspondante.

Vous pouvez trouver des exemples d’implémentation de sérialisation personnalisée dans les tests d’unités du kit de développement logiciel Azure Search .NET Azure GitHub. [Ce dossier](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models) est un bon point de départ. Il contient des classes qui sont utilisées par les tests de sérialisation personnalisés.

En plus de la sérialisation personnalisée, le nouveau Kit de développement logiciel prend également en charge la sérialisation des objets `SearchContinuationToken`. Cela peut être utile si vous appelez Azure Search à partir d'une application web et que vous avez besoin d'échanger des jetons de liaison avec un navigateur ou un client mobile lors de la pagination des résultats de recherche.

<a name="UpgradeSteps"></a>
## Opérations de mise à niveau

Tout d'abord, mettez à jour vos références NuGet `Microsoft.Azure.Search` en utilisant soit la console NuGet Package, soit en effectuant un clic droit sur les références de votre projet et en sélectionnant « Gérer les packages NuGet » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, recompilez votre projet.

Si vous utilisiez précédemment la version préliminaire 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview, la build doit réussir et vous pouvez donc vous lancer !

Si vous utilisiez précédemment la version préliminaire 0.13.0-preview ou une version antérieure, vous obtiendrez sans doute les erreurs de build suivantes :

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

L’étape suivante consiste à corriger les erreurs de build une par une. La plupart d’entre elles nécessitent la modification de certains noms de classe et de méthode qui ont été renommés dans le kit de développement logiciel. La [Liste des dernières modifications dans la version 1.1](#ListOfChanges) répertorie ces changements de nom.

Si vous utilisez des classes personnalisées pour modéliser vos documents et que ces classes ont des propriétés de types primitifs ne pouvant avoir pour valeur null (par exemple, `int` ou `bool` en C#), il existe un correctif de bogue que vous devez connaître dans la version 1.1 du Kit de développement logiciel (SDK). Consultez [Résolution des bogues dans la version 1.1](#BugFixes) pour plus de détails.

Enfin, une fois que vous avez résolu les erreurs de build, vous pouvez apporter des modifications à votre application pour exploiter les nouvelles fonctionnalités si vous le souhaitez. La fonctionnalité de sérialisation personnalisée dans le nouveau Kit de développement logiciel (SDK) est détaillée dans [Nouveautés dans la version 1.1](#WhatsNew).

<a name="ListOfChanges"></a>
## Liste des dernières modifications dans la version 1.1

La liste qui suit est classée selon la probabilité que la modification affecte votre code d’application.

### Modifications IndexBatch et IndexAction

`IndexBatch.Create` a été renommé `IndexBatch.New` et ne comporte plus d’argument `params`. Vous pouvez utiliser `IndexBatch.New` pour les lots qui combinent différents types d’actions (fusions, suppressions, etc.). En outre, voici les nouvelles méthodes statiques pour la création de lots comportant tous les mêmes actions : `Delete`, `Merge`, `MergeOrUpload` et `Upload`.

`IndexAction` ne contient plus de constructeurs publics et ses propriétés sont immuables. Vous devez utiliser les nouvelles méthodes statiques pour la création d’actions à des fins différentes : `Delete`, `Merge`, `MergeOrUpload` et `Upload`. `IndexAction.Create` a été supprimé. Si vous avez utilisé la surcharge qui accepte uniquement un document, vérifiez que vous utilisez `Upload` à la place.

#### Exemple

Si votre code ressemble à ce qui suit :

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Si vous le souhaitez, vous pouvez encore le simplifier en le ramenant à ce qui suit :

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### Modifications IndexBatchException

La propriété `IndexBatchException.IndexResponse` a été renommée `IndexingResults`, et son type est désormais `IList<IndexingResult>`.

#### Exemple

Si votre code ressemble à ce qui suit :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
### Modifications des méthodes d’opération

Chaque opération du kit de développement logiciel .NET Azure Search est exposée en tant qu’ensemble de chargements de méthode pour les appelants synchrones et asynchrones. Les signatures et la factorisation de ces surcharges de méthode ont changé dans la version 1.1.

Par exemple, l’opération « Obtenir des statistiques d’Index » dans les versions antérieures du kit de développement expose ces signatures :

Dans `IIndexOperations` :

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

Dans `IndexOperationsExtensions` :

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Les signatures de méthode pour la même opération en version 1.1 ressemblent à ce qui suit :

Dans `IIndexesOperations` :

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

Dans `IndexesOperationsExtensions` :

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

À partir de la version 1.1, le Kit de développement logiciel (SDK) .NET Azure Search organise les méthodes d'opération différemment :

 - Les paramètres facultatifs sont désormais modélisés en tant que paramètres par défaut plutôt que les surcharges de méthode supplémentaires. Cela réduit le nombre de surcharges de méthode, parfois considérablement.
 - Les méthodes d’extension masquent maintenant un grand nombre des détails superflus de HTTP de la part de l’appelant. Par exemple, les versions antérieures du kit de développement ont renvoyé un objet de réponse avec un code d’état HTTP, que vous n’avez pas besoin de contrôler, car les méthodes de fonctionnement lèvent `CloudException` pour un code d’état qui signale une erreur. Les nouvelles méthodes d’extension ne retournent que des objets de modèle, ce qui vous évite de les désencapsuler dans votre code.
 - À l’inverse, les principales interfaces exposent maintenant les méthodes qui vous offrent davantage de contrôle au niveau HTTP si vous en avez besoin. Vous pouvez maintenant transférer des en-têtes HTTP personnalisés à inclure dans les demandes et le nouveau type de retour `AzureOperationResponse<T>` vous donne un accès direct à `HttpRequestMessage` et à `HttpResponseMessage` pour l’opération. `AzureOperationResponse` est défini dans l’espace de nom `Microsoft.Rest.Azure` remplace `Hyak.Common.OperationResponse`.

### Modifications ScoringParameters

Une nouvelle classe nommée `ScoringParameter` a été ajoutée à la dernière version du kit de développement logiciel (SDK) pour faciliter la fourniture de paramètres de profils de score dans une requête de recherche. Précédemment ,la propriété `ScoringProfiles` de la classe `SearchParameters` était de type `IList<string>`. À présent, elle est de type `IList<ScoringParameter>`.

#### Exemple

Si votre code ressemble à ce qui suit :

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam:featured", "mapCenterParam:" + lon + "," + lat };

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", "featured"),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### Modifications de modèles de classe

En raison des modifications de signature décrites dans [Modifications des méthodes d’opération](#OperationMethodChanges), de nombreuses classes de l’espace de noms `Microsoft.Azure.Search.Models` ont été renommées ou supprimées. Par exemple :

 - `IndexDefinitionResponse` a été remplacé par `AzureOperationResponse<Index>`
 - `DocumentSearchResponse` a été renommé en `DocumentSearchResult`
 - `IndexResult` a été renommé en `IndexingResult`
 - `Documents.Count()` renvoie désormais un élément `long` avec le nombre de documents et non un élément `DocumentCountResponse`
 - `IndexGetStatisticsResponse` a été renommé en `IndexGetStatisticsResult`
 - `IndexListResponse` a été renommé en `IndexListResult`

Pour résumer, les classes dérivées de `OperationResponse` qui servaient uniquement à encapsuler un objet de modèle ont été supprimées. Les classes restantes ont vu leur suffixe passer de `Response` à `Result`.

#### Exemple

Si votre code ressemble à ce qui suit :

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### Les classes de réponse et IEnumerable

Une modification supplémentaire pouvant affecter votre code est que les classes de réponse contenant des collections ne sont plus mises en œuvre `IEnumerable<T>`. Au lieu de cela, vous pourrez directement accéder à la propriété de collection. Par exemple, si votre code ressemble à ceci :

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### Remarque importante pour les applications web

Si vous disposez d’une application web qui sérialise `DocumentSearchResponse` directement pour envoyer des résultats de recherche au navigateur, vous devez modifier votre code sinon les résultats ne seront pas sérialisés correctement. Par exemple, si votre code ressemble à ceci :

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Vous pouvez le modifier en faisant en sorte que la propriété `.Results` de la réponse de la recherche corrige le rendu des résultats de recherche :

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Vous devrez rechercher ces cas dans votre code vous-même. **Le compilateur ne vous avertira pas **parce que `JsonResult.Data` est de type `object`.

### Modifications CloudException

La classe `CloudException` a été déplacée de l’espace de noms `Hyak.Common` à l’espace de noms `Microsoft.Rest.Azure`. En outre, sa propriété `Error` a été renommée en `Body`.

### Modifications de SearchServiceClient et SearchIndexClient

Le type de la propriété `Credentials` est passé de `SearchCredentials` à sa classe de base, `ServiceClientCredentials`. Si vous avez besoin d’accéder à l’élément `SearchCredentials` d’un élément `SearchIndexClient` ou `SearchServiceClient`, veuillez utiliser la nouvelle propriété `SearchCredentials`.

Dans les versions antérieures du kit de développement logiciel (SDK), `SearchServiceClient` et `SearchIndexClient` avaient des constructeurs incluant un paramètre `HttpClient`. Ils ont été remplacés par des constructeurs qui incluent un élément `HttpClientHandler` et un tableau d’objets `DelegatingHandler`. Cela facilite l’installation de gestionnaires personnalisés pour pré-traiter des demandes HTTP si nécessaire.

Enfin, les constructeurs incluant un élément `Uri` et `SearchCredentials` ont été modifiés. Par exemple, si vous avez un code qui ressemble à c qui suit :

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Notez également que le type de paramètre d’informations d’identification a été modifié en `ServiceClientCredentials`. Il est peu probable que cela affecte votre code, car l’élément `SearchCredentials` est dérivé de `ServiceClientCredentials`.

### Transfert d’un ID de requête

Dans les versions antérieures du kit de développement logiciel (SDK), vous pouviez définir un ID de demande sur `SearchServiceClient` ou `SearchIndexClient`, et il était inclus dans chaque demande adressée à l’API REST. Cela peut s’avérer utile pour résoudre les problèmes de votre service de recherche si vous devez contacter le support technique. Cependant, il peut être plus utile de définir un ID de requête unique pour chaque opération plutôt que d’utiliser le même ID pour toutes les informations. Pour cette raison, les méthodes `SetClientRequestId` de `SearchServiceClient` et `SearchIndexClient` ont été supprimées. Vous pouvez en revanche transférer un ID de demande à chaque méthode d’opération via le paramètre facultatif `SearchRequestOptions`.

> [AZURE.NOTE] Dans une prochaine version du Kit de développement logiciel, nous allons ajouter un nouveau mécanisme permettant de définir globalement un ID de demande sur les objets clients compatibles avec l’approche utilisée par d’autres kits de développement logiciel Azure.

#### Exemple

Si vous avez un code qui ressemble à ce qui suit :

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### Modifications de nom d’interface

Les noms d’interface du groupe d’opération ont tous changé pour être cohérents avec les noms de propriété correspondants :

 - Le type de `ISearchServiceClient.Indexes` a été renommé de `IIndexOperations` en `IIndexesOperations`.
 - Le type de `ISearchServiceClient.Indexers` a été renommé de `IIndexerOperations` en `IIndexersOperations`.
 - Le type de `ISearchServiceClient.DataSources` a été renommé de `IDataSourceOperations` en `IDataSourcesOperations`.
 - Le type de `ISearchIndexClient.Documents` a été renommé de `IDocumentOperations` en `IDocumentsOperations`.

Cette modification n’affectera probablement pas votre code, à moins que vous créiez des versions fictives de ces interfaces à des fins de test.

<a name="BugFixes"></a>
## Résolution des bogues dans la version 1.1

Les versions antérieures du kit de développement logiciel .NET Azure Search relatif à la sérialisation de classes de modèle personnalisé présentaient un bogue. Le bogue peut se produire si vous avez créé une classe de modèle personnalisé avec une propriété de type de valeur ne pouvant être définie sur null.

### Opérations à reproduire

Créez une classe de modèle personnalisé avec une propriété de type avec valeur ne pouvant être définie sur null. Par exemple, ajoutez une propriété `UnitCount` publique de type `int` au lieu de `int?`.

Si vous indexez un document avec la valeur par défaut de ce type (par exemple, 0 pour `int`), le champ sera null dans Azure Search. Si par la suite vous recherchez ce document, l’appel `Search` lancera une exception `JsonSerializationException` signalant qu’il est impossible de convertir `null` en `int`.

Les filtres peuvent également ne pas fonctionner comme prévu car c’est la valeur null qui est inscrite dans l’index, en non la valeur attendue.

### Corriger des détails

Nous avons résolu ce problème dans la version 1.1 du Kit de développement logiciel (SDK). Maintenant, si vous avez une classe de modèle comme suit :

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

et si vous définissez `IntValue` sur 0, cette valeur est correctement sérialisée en tant que 0 sur le câble et stockée en tant que 0 dans l’index. Le retour fonctionne également comme prévu.

Cette approche présente un problème à ne pas ignorer : si vous utilisez un type de modèle avec une propriété ne pouvant être définie sur null, vous devez **garantir** qu’aucun document dans votre index ne contient de valeur null pour le champ correspondant. Ni le kit de développement logiciel ni l’API REST Azure Search ne vous aideront à appliquer cette recommandation.

Il ne s’agit pas d’une préoccupation hypothétique : imaginez un scénario dans lequel vous ajoutez un nouveau champ à un index existant qui est de type `Edm.Int32`. Après la mise à jour de la définition d’index, ce nouveau champ prendra la valeur null pour tous les documents (car tous les types peuvent avoir la valeur null dans Azure Search). Si vous utilisez ensuite une classe de modèle avec une propriété `int` ne pouvant être définie sur null pour ce champ, vous obtiendrez l’exception `JsonSerializationException` ci-dessous lorsque vous tenterez de récupérer des documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous vous recommandons d’utiliser des types pour lesquels la valeur null est autorisée en tant que meilleure pratique.

Pour plus d’informations sur ce bogue et le correctif, consultez [ce problème sur GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

## Conclusion
Si vous souhaitez plus d’informations sur l’utilisation du kit de développement logiciel (SDK) Azure Search .NET, consultez les articles [Procédures](search-howto-dotnet-sdk.md) récemment mis à jour.

N’hésitez pas à nous faire part de vos commentaires sur le kit de développement logiciel. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=azuresearch). Si vous trouvez un bogue, vous pouvez signaler un problème dans le [Référentiel GitHub du kit de développement logiciel (SDK) Azure .NET ](https://github.com/Azure/azure-sdk-for-net/issues). N’oubliez pas de faire précéder le titre de votre problème du préfixe « Search SDK : ».

Merci d’utiliser Azure Search !

<!------HONumber=AcomDC_0309_2016-->