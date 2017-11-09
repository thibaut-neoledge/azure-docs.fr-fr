---
title: "Mise à niveau vers la version v1.1 du Kit de développement logiciel (SDK) .NET Recherche Azure | Microsoft Docs"
description: "Mise à niveau vers la version du Kit de développement logiciel Azure Search .NET SDK version 1.1"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 66f89958-a320-4a24-87f9-69315848909f
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 9782454e3bfc697b63cde8aa28a14be0c393c36b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Mise à niveau vers la version du Kit de développement logiciel Azure Search .NET SDK version 3
Si vous utilisez la version préliminaire 2.0 ou une version antérieure du [Kit de développement logiciel (SDK) .NET Azure Search](https://aka.ms/search-sdk), cet article vous aidera à mettre à niveau votre application pour utiliser la version 3.

Pour avoir un aperçu général du kit de développement logiciel et avoir des exemples, voir [Comment utiliser Azure Search à partir d’une application .NET](search-howto-dotnet-sdk.md).

La version 3 du SDK .NET Azure Search contient des modifications par rapport aux versions antérieures. Elles sont pour la plupart mineures, et donc, la modification de votre code devrait ne nécessiter que peu d’effort. Consultez la page [Procédure de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier le code à utiliser avec la nouvelle version du kit de développement logiciel.

> [!NOTE]
> Si vous utilisez la version 1.0.2-preview ou une version antérieure, vous devez tout d’abord mettre à niveau vers la version 1.1, puis vers la version 3. Consultez [Annexe : Procédure de mise à niveau vers la version 1.1](#UpgradeStepsV1) pour obtenir des instructions.
>
> Votre instance de service Recherche Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Nouveautés de la version 3
La version 3 du SDK .NET Azure Search cible la dernière version disponible de l’API REST Azure Search, spécifiquement 2016-09-01. Cela permet d’utiliser de nombreuses nouvelles fonctionnalités d’Azure Search à partir d’une application .NET, notamment les suivantes :

* [Analyseurs personnalisés](https://aka.ms/customanalyzers)
* Prise en charge des indexeurs [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md) et [Stockage Table Azure](search-howto-indexing-azure-tables.md)
* Personnalisation des indexeurs avec les [mappages de champs](search-indexer-field-mappings.md)
* Prise en charge des ETag pour permettre la mise à jour simultanée sécurisée des définitions d’index, des indexeurs et des sources de données
* Prise en charge de la création déclarative des définitions de champ index en décorant votre classe de modèle et en utilisant la nouvelle classe `FieldBuilder`.
* Prise en charge de .NET Core et .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau
Tout d’abord, mettez à jour vos références NuGet `Microsoft.Azure.Search` , soit en utilisant la console NuGet Package, soit en effectuant un clic droit sur les références de votre projet et en sélectionnant « Gérer les packages NuGet » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, recompilez votre projet. En fonction de la structure de votre code, la recompilation peut réussir. Si c’est le cas, vous êtes prêt !

Si la compilation échoue, vous devriez voir une erreur de ce type :

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

L’étape suivante consiste à corriger cette erreur de build. Consultez [Dernières modifications dans la version 3](#ListOfChanges) pour plus d’informations sur la cause de l’erreur et les possibilités de correction.

D’autres avertissements de compilation sont susceptibles de s’afficher ; ils sont liés à des propriétés ou des méthodes obsolètes. Ces avertissements incluent des instructions sur les fonctionnalités à utiliser à la place de la fonctionnalité déconseillée. Par exemple, si votre application utilise la propriété `IndexingParameters.Base64EncodeKeys`, vous devriez obtenir un avertissement indiquant `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Une fois que vous avez résolu les erreurs de build, vous pouvez apporter des modifications à votre application pour exploiter les nouvelles fonctionnalités si vous le souhaitez. Les nouvelles fonctionnalités du SDK sont détaillées dans [Nouveautés de la version 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Dernières modifications dans la version 3
Il n’y a qu’un petit nombre de modifications dans la version 3 qui soient susceptibles de nécessiter des modifications de code en plus de la recompilation de votre application.

### <a name="indexesgetclient-return-type"></a>Type de retour Indexes.GetClient
La méthode `Indexes.GetClient` a un nouveau type de retour. Auparavant, elle renvoyait `SearchIndexClient`, mais elle a été remplacée par `ISearchIndexClient` dans la version 2.0-preview et cette modification a été répercutée dans la version 3. L’objectif est de prendre en charge les clients qui souhaitent simuler la méthode `GetClient` pour les tests unitaires en retournant une implémentation fictive de `ISearchIndexClient`.

#### <a name="example"></a>Exemple
Si votre code ressemble à ce qui suit :

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Les éléments AnalyzerName et DataType, entre autres, ne sont plus implicitement convertibles en chaînes
Il existe de nombreux types dans le SDK .NET Azure Search qui dérivent de `ExtensibleEnum`. Précédemment, ces types étaient tous implicitement convertibles en type `string`. Toutefois, un bogue a été découvert dans l'implémentation `Object.Equals` de ces classes et la résolution de ce bogue a nécessité la désactivation de cette conversion implicite. Une conversion explicite vers `string` reste autorisée.

#### <a name="example"></a>Exemple
Si votre code ressemble à ce qui suit :

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Membres obsolètes supprimés

Vous pouvez voir des erreurs de build liées à des méthodes ou propriétés qui ont été marquées comme obsolètes dans la version 2.0-preview puis supprimées de la version 3. Si vous rencontrez ces erreurs, voici comment les résoudre :

- Si vous utilisiez ce constructeur : `ScoringParameter(string name, string value)`, remplacez-le par `ScoringParameter(string name, IEnumerable<string> values)`
- Si vous utilisiez la propriété `ScoringParameter.Value`, remplacez-la par la propriété `ScoringParameter.Values` ou la méthode `ToString`.
- Si vous utilisiez la propriété `SearchRequestOptions.RequestId`, remplacez-la par la propriété `ClientRequestId`.

### <a name="removed-preview-features"></a>Fonctionnalités préliminaires supprimées

Si vous effectuez une mise à niveau de la version 2.0-preview vers la version 3, n’oubliez pas que la prise en charge de l'analyse JSON et CSV des indexeurs d’objets blob a été supprimée car ces fonctionnalités sont toujours en version préliminaire. En particulier, les méthodes suivantes de la classe `IndexingParametersExtensions` ont été supprimées :

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Si votre application dépend fortement de ces fonctionnalités, vous ne pourrez pas mettre à niveau vers la version 3 du SDK .NET Azure Search. Vous pouvez continuer à utiliser la version 2.0-preview. Mais n’oubliez pas que **nous déconseillons l’utilisation de Kits de développement logiciel en version préliminaire dans les applications de production**. Les fonctionnalités préliminaires servent uniquement à des fins d’évaluation et peuvent changer.

## <a name="conclusion"></a>Conclusion
Si vous souhaitez plus d’informations sur l’utilisation du Kit de développement logiciel (SDK) Azure Search .NET, consultez les articles [Procédures](search-howto-dotnet-sdk.md)récemment mis à jour.

N’hésitez pas à nous faire part de vos commentaires sur le kit de développement logiciel. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Si vous trouvez un bogue, vous pouvez signaler un problème dans le [Référentiel GitHub du Kit de développement logiciel (SDK) Azure .NET](https://github.com/Azure/azure-sdk-for-net/issues). N’oubliez pas de faire précéder le titre de votre problème du préfixe « Search SDK : ».

Merci d’utiliser Azure Search !

<a name="UpgradeStepsV1"></a>

## <a name="appendix-steps-to-upgrade-to-version-11"></a>Annexe : Procédure de mise à niveau vers la version 1.1
> [!NOTE]
> Cette section s’applique uniquement aux utilisateurs du SDK .NET Azure Search version 1.0.2-preview et versions antérieures.
> 
> 

Tout d’abord, mettez à jour vos références NuGet `Microsoft.Azure.Search` , soit en utilisant la console NuGet Package, soit en effectuant un clic droit sur les références de votre projet et en sélectionnant « Gérer les packages NuGet » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, recompilez votre projet.

Si vous utilisiez précédemment la version préliminaire 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview, la build doit réussir et vous pouvez donc vous lancer !

Si vous utilisiez précédemment la version préliminaire 0.13.0-preview ou une version antérieure, vous obtiendrez sans doute les erreurs de build suivantes :

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

L’étape suivante consiste à corriger les erreurs de build une par une. La plupart d’entre elles nécessitent la modification de certains noms de classe et de méthode qui ont été renommés dans le kit de développement logiciel. [Liste des dernières modifications dans la version 1.1](#ListOfChangesV1) répertorie ces changements de nom.

Si vous utilisez des classes personnalisées pour modéliser vos documents et que ces classes ont des propriétés de types primitifs ne pouvant avoir pour valeur null (par exemple, `int` ou `bool` en C#), il existe un correctif de bogue que vous devez connaître dans la version 1.1 du Kit de développement logiciel (SDK). Consultez [Résolution des bogues dans la version 1.1](#BugFixesV1) pour plus de détails.

Enfin, une fois que vous avez résolu les erreurs de build, vous pouvez apporter des modifications à votre application pour exploiter les nouvelles fonctionnalités si vous le souhaitez.

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>Liste des dernières modifications dans la version 1.1
La liste qui suit est classée selon la probabilité que la modification affecte votre code d’application.

#### <a name="indexbatch-and-indexaction-changes"></a>Modifications IndexBatch et IndexAction
`IndexBatch.Create` a été renommé `IndexBatch.New` et ne comporte plus d’argument `params`. Vous pouvez utiliser `IndexBatch.New` pour les lots qui combinent différents types d’actions (fusions, suppressions, etc.). En outre, voici les nouvelles méthodes statiques pour la création de lots comportant tous les mêmes actions : `Delete`, `Merge`, `MergeOrUpload` et `Upload`.

`IndexAction` ne contient plus de constructeurs publics et ses propriétés sont immuables. Vous devez utiliser les nouvelles méthodes statiques pour la création d’actions à des fins différentes : `Delete`, `Merge`, `MergeOrUpload` et `Upload`. `IndexAction.Create` a été supprimé. Si vous avez utilisé la surcharge qui accepte uniquement un document, veillez à utiliser `Upload` à la place.

##### <a name="example"></a>Exemple
Si votre code ressemble à ce qui suit :

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Si vous le souhaitez, vous pouvez encore le simplifier en le ramenant à ce qui suit :

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Modifications IndexBatchException
La propriété `IndexBatchException.IndexResponse` a été renommée `IndexingResults`, et son type est désormais `IList<IndexingResult>`.

##### <a name="example"></a>Exemple
Si votre code ressemble à ce qui suit :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

#### <a name="operation-method-changes"></a>Modifications des méthodes d’opération
Chaque opération du kit de développement logiciel .NET Azure Search est exposée en tant qu’ensemble de chargements de méthode pour les appelants synchrones et asynchrones. Les signatures et la factorisation de ces surcharges de méthode ont changé dans la version 1.1.

Par exemple, l’opération « Obtenir des statistiques d’Index » dans les versions antérieures du kit de développement expose ces signatures :

Dans `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

Dans `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Les signatures de méthode pour la même opération en version 1.1 ressemblent à ce qui suit :

Dans `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

Dans `IndexesOperationsExtensions`:

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

À partir de la version 1.1, le Kit de développement logiciel (SDK) .NET Azure Search organise les méthodes d'opération différemment :

* Les paramètres facultatifs sont désormais modélisés en tant que paramètres par défaut plutôt que les surcharges de méthode supplémentaires. Cela réduit le nombre de surcharges de méthode, parfois considérablement.
* Les méthodes d’extension masquent maintenant un grand nombre des détails superflus de HTTP de la part de l’appelant. Par exemple, les versions antérieures du SDK ont renvoyé un objet de réponse avec un code d’état HTTP, que vous n’avez pas besoin de contrôler, car les méthodes de fonctionnement lèvent `CloudException` pour un code d’état qui signale une erreur. Les nouvelles méthodes d’extension ne retournent que des objets de modèle, ce qui vous évite de les désencapsuler dans votre code.
* À l’inverse, les principales interfaces exposent maintenant les méthodes qui vous offrent davantage de contrôle au niveau HTTP si vous en avez besoin. Vous pouvez maintenant transférer des en-têtes HTTP personnalisés à inclure dans les demandes et le nouveau type de retour `AzureOperationResponse<T>` vous donne un accès direct à `HttpRequestMessage` et à `HttpResponseMessage` pour l’opération. `AzureOperationResponse` est défini dans l’espace de noms `Microsoft.Rest.Azure` et remplace `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Modifications ScoringParameters
Une nouvelle classe nommée `ScoringParameter` a été ajoutée à la dernière version du Kit de développement logiciel (SDK) pour faciliter la fourniture de paramètres de profils de score dans une requête de recherche. Précédemment, la propriété `ScoringProfiles` de la classe `SearchParameters` était de type `IList<string>`. À présent, elle est de type `IList<ScoringParameter>`.

##### <a name="example"></a>Exemple
Si votre code ressemble à ce qui suit :

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

vous pouvez le modifier pour résoudre les éventuelles erreurs de build : 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Modifications de modèles de classe
En raison des modifications de signature décrites dans [Modifications des méthodes d’opération](#OperationMethodChanges), de nombreuses classes de l’espace de noms `Microsoft.Azure.Search.Models` ont été renommées ou supprimées. Par exemple :

* `IndexDefinitionResponse` a été remplacé par `AzureOperationResponse<Index>`
* `DocumentSearchResponse` a été renommé en `DocumentSearchResult`
* `IndexResult` a été renommé en `IndexingResult`
* `Documents.Count()` renvoie désormais un élément `long` avec le nombre de documents et non un élément `DocumentCountResponse`
* `IndexGetStatisticsResponse` a été renommé en `IndexGetStatisticsResult`
* `IndexListResponse` a été renommé en `IndexListResult`

Pour résumer, les classes dérivées de `OperationResponse`qui servaient uniquement à encapsuler un objet de modèle ont été supprimées. Les classes restantes ont vu leur suffixe passer de `Response` à `Result`.

##### <a name="example"></a>Exemple
Si votre code ressemble à ce qui suit :

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

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

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

##### <a name="response-classes-and-ienumerable"></a>Les classes de réponse et IEnumerable
Une modification supplémentaire pouvant affecter votre code est que les classes de réponse contenant des collections ne sont plus mises en œuvre `IEnumerable<T>`. Au lieu de cela, vous pourrez directement accéder à la propriété de collection. Par exemple, si votre code ressemble à ceci :

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="special-case-for-web-applications"></a>Cas particulier pour les applications web
Si vous disposez d’une application web qui sérialise `DocumentSearchResponse` directement pour envoyer des résultats de recherche au navigateur, vous devez modifier votre code sinon les résultats ne seront pas sérialisés correctement. Par exemple, si votre code ressemble à ceci :

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

Vous devrez rechercher ces cas dans votre code vous-même. **Le compilateur ne vous avertira pas** parce que `JsonResult.Data` est de type `object`.

#### <a name="cloudexception-changes"></a>Modifications CloudException
La classe `CloudException` a été déplacée de l’espace de noms `Hyak.Common` à l’espace de noms `Microsoft.Rest.Azure`. En outre, sa propriété `Error` a été renommée en `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Modifications de SearchServiceClient et SearchIndexClient
Le type de la propriété `Credentials` est passé de `SearchCredentials` à sa classe de base, `ServiceClientCredentials`. Si vous avez besoin d’accéder à l’élément `SearchCredentials` d’un élément `SearchIndexClient` ou `SearchServiceClient`, veuillez utiliser la nouvelle propriété `SearchCredentials`.

Dans les versions antérieures du Kit de développement logiciel (SDK), `SearchServiceClient` et `SearchIndexClient` avaient des constructeurs incluant un paramètre `HttpClient`. Ils ont été remplacés par des constructeurs qui incluent un élément `HttpClientHandler` et un tableau d’objets `DelegatingHandler`. Cela facilite l’installation de gestionnaires personnalisés pour pré-traiter des demandes HTTP si nécessaire.

Enfin, les constructeurs incluant un élément `Uri` et `SearchCredentials` ont été modifiés. Par exemple, si vous avez un code qui ressemble à c qui suit :

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Notez également que le type du paramètre d’informations d’identification a été modifié en `ServiceClientCredentials`. Il est peu probable que cela affecte votre code, car l’élément `SearchCredentials` est dérivé de `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Transfert d’un ID de requête
Dans les versions antérieures du Kit de développement logiciel (SDK), vous pouviez définir un ID de demande sur `SearchServiceClient` ou `SearchIndexClient`, et il était inclus dans chaque demande adressée à l’API REST. Cela peut s’avérer utile pour résoudre les problèmes de votre service de recherche si vous devez contacter le support technique. Cependant, il peut être plus utile de définir un ID de requête unique pour chaque opération plutôt que d’utiliser le même ID pour toutes les informations. Pour cette raison, les méthodes `SetClientRequestId` de `SearchServiceClient` et `SearchIndexClient` ont été supprimées. Vous pouvez en revanche transférer un ID de demande à chaque méthode d’opération avec le paramètre facultatif `SearchRequestOptions` .

> [!NOTE]
> Dans une prochaine version du Kit de développement logiciel, nous allons ajouter un nouveau mécanisme permettant de définir globalement un ID de demande sur les objets clients compatibles avec l’approche utilisée par d’autres kits de développement logiciel Azure.
> 
> 

#### <a name="example"></a>Exemple
Si vous avez un code qui ressemble à ce qui suit :

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

vous pouvez le modifier pour résoudre les éventuelles erreurs de build :

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Modifications de nom d’interface
Les noms d’interface du groupe d’opération ont tous changé pour être cohérents avec les noms de propriété correspondants :

* Le type de `ISearchServiceClient.Indexes` a été renommé de `IIndexOperations` en `IIndexesOperations`.
* Le type de `ISearchServiceClient.Indexers` a été renommé de `IIndexerOperations` en `IIndexersOperations`.
* Le type de `ISearchServiceClient.DataSources` a été renommé de `IDataSourceOperations` en `IDataSourcesOperations`.
* Le type de `ISearchIndexClient.Documents` a été renommé de `IDocumentOperations` en `IDocumentsOperations`.

Cette modification n’affectera probablement pas votre code, à moins que vous créiez des versions fictives de ces interfaces à des fins de test.

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>Résolution des bogues dans la version 1.1
Les versions antérieures du kit de développement logiciel .NET Azure Search relatif à la sérialisation de classes de modèle personnalisé présentaient un bogue. Le bogue peut se produire si vous avez créé une classe de modèle personnalisé avec une propriété de type de valeur ne pouvant être définie sur null.

#### <a name="steps-to-reproduce"></a>Opérations à reproduire
Créez une classe de modèle personnalisé avec une propriété de type avec valeur ne pouvant être définie sur null. Par exemple, ajoutez une propriété `UnitCount` publique de type `int` au lieu de `int?`.

Si vous indexez un document avec une valeur par défaut de ce type (par exemple, 0 pour `int`), le champ sera null dans Azure Search. Si par la suite vous recherchez ce document, l’appel `Search` lancera une exception `JsonSerializationException` signalant qu’il est impossible de convertir `null` en `int`.

Les filtres peuvent également ne pas fonctionner comme prévu car c’est la valeur null qui est inscrite dans l’index, en non la valeur attendue.

#### <a name="fix-details"></a>Corriger des détails
Nous avons résolu ce problème dans la version 1.1 du Kit de développement logiciel (SDK). Maintenant, si vous avez une classe de modèle comme suit :

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

