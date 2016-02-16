<properties 
   pageTitle="Essayer Azure Search gratuitement avec Azure App Service | Microsoft Azure"
   description="Essayez gratuitement Azure Search pendant une heure à l’aide du modèle Azure App Service."
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="01/13/2016"
   ms.author="heidist"/>

# Essayer Azure Search gratuitement avec Azure App Service

[Essayer Azure App Service](https://tryappservice.azure.com/) est une session gratuite d'une heure, basée sur navigateur, ayant pour but d'essayer les services Azure, y compris **Azure Search**, sans inscription requise à un abonnement. Le site permet de choisir entre plusieurs modèles. Lorsque vous sélectionnez le modèle ASP.NET, qui inclut Azure Search, vous accédez à un site web entièrement fonctionnel, pris en charge par les services que vous avez sélectionnés.

Dans le cadre de l'offre [Essayer Azure App Service](https://tryappservice.azure.com/), le service Azure Search est déjà créé et prêt à recevoir des requêtes de recherche. Vous ne pouvez pas télécharger ni utiliser votre index ou vos données, mais vous pouvez exécuter des requêtes et effectuer un certain nombre de modifications du code pour redéfinir l'expérience utilisateur.

Les données de recherche proviennent du [United States Geological Survey (USGS, Institut d'études géologiques des États-Unis)](http://geonames.usgs.gov/domestic/download_data.htm) et comprennent 3 millions de lignes de repères, de sites historiques, de bâtiments et autres éléments géographiques aux États-Unis.

## Prise en main

Si vous n’avez pas encore démarré votre session d’une heure, procédez comme suit pour commencer.

1. Accédez à [https://tryappservice.azure.com](https://tryappservice.azure.com/) et faites défiler vers le bas pour sélectionner **Application web**. 
2. Cliquez sur **Next**.
3. Choisissez le modèle **Site ASP.NET + Azure Search**.

    ![][1]

4. Cliquez sur **Create**.
5. Choisissez une méthode de connexion, et indiquez le nom d’utilisateur et le mot de passe.

    ![][2]

6. Attendez que le site soit configuré. Lorsqu’il est prêt, vous verrez une page similaire à celle-ci. Chaque page affiche une minuterie afin que vous sachiez toujours combien de temps il vous reste.

    ![][3]

7. Choisissez **Modifier avec Visual Studio Team Services** pour afficher la solution et parcourir le site.
9. Dans Visual Studio Team Services, développez les options de session en haut de la page, puis cliquez sur **Parcourir le site web**.

    ![][4]

10. Vous devez voir la page d’accueil de votre site web Azure Search. Cliquez sur le bouton **Commencer** pour ouvrir le site.

    ![][5]

11. Un site web ASP.NET s’ouvre dans le navigateur, fournissant une zone de recherche. Entrez un terme familier à rechercher, tel que *Yellowstone* ou une montagne connue, telle que *Mont Rainier*. Commencer par un repère familier facilite l’évaluation des résultats.

    ![][6]


## Premières étapes

Étant donné que l’index de recherche est totalement opérationnel, la première étape consiste à essayer quelques requêtes. Azure Search prend en charge tous les opérateurs de recherche standard (+, -, |), les guillemets pour les correspondances littérales, les caractères génériques (*) et les opérateurs de priorité. Vous pouvez examiner la référence de syntaxe de requête pour la liste complète des opérateurs.

- Commencez par une recherche générique en ajoutant un astérisque (`*`). Cela vous indique le nombre de documents se trouvant dans l’index : 2 262 578.
- Ensuite, entrez « Yellowstone », puis ajoutez « +centre », « +bâtiment » et « -ND » pour affiner progressivement les résultats de la recherche aux centres d’informations de Yellowstone, en excluant ceux situés dans le Dakota du Nord :`Yellowstone +center +building -ND`.  
- Essayez une expression de recherche qui combine les opérateurs de priorité et de correspondance de chaîne :`statue+(park+MT)`. Vous devez voir des résultats similaires à ceux de la capture d’écran ci-dessous. Notez que les catégories de facette apparaissent sous Classe de fonctionnalité, offrant un filtrage autodirigé via la navigation par facettes, une fonctionnalité couramment utilisée dans la plupart des applications de recherche.

    ![][7]

Prêt à continuer ? Nous allons modifier quelques lignes de code pour voir l’impact sur les opérations de recherche en texte intégral.

## Modifier searchMode.All

Azure Search comporte une propriété **searchMode** configurable que vous pouvez utiliser pour contrôler le comportement de l’opérateur de recherche. Les valeurs valides pour cette propriété sont `Any` (par défaut) ou `All`. Pour plus d’instructions sur la définition de ces options, voir [Syntaxe de requête simple](https://msdn.microsoft.com/library/dn798920.aspx).

- **searchMode.Any** stipule que toute correspondance sur un terme de recherche est suffisante pour inclure un élément dans les résultats de recherche. Si votre expression de recherche est `Yellowstone visitor center`, tout document contenant un de ces termes est inclus dans les résultats de recherche. Ce mode est orienté vers le *rappel*.
- **searchModel.All**, utilisé dans cet exemple, requiert que tous les termes spécifiés soient présents dans le document. Ce mode est plus strict que **searchMode.Any**, mais si vous recherchez avant tout la *précision* plutôt que le rappel, il s’agit probablement du bon choix pour votre application. 

> [AZURE.NOTE] **searchMode.Any** fonctionne de manière optimale lorsque la construction de la requête se compose principalement d’expressions, avec un minimum d’opérateurs. La règle générale est que les utilisateurs à la recherche d’applications consommateur, telles que les sites de commerce électronique, ont tendance à utiliser uniquement des termes, alors que les personnes effectuant des recherches sur le contenu ou les données sont plus susceptibles d’inclure des opérateurs dans l’expression à rechercher. Si vous pensez que les recherches sont susceptibles d’inclure des opérateurs, en particulier l’opérateur `NOT (-)`, commencez avec **searchModel.All**. En revanche, **searchMode.Any** changera en `OR` l’opérateur `NOT` avec d’autres termes de recherche, ce qui peut considérablement augmenter le nombre de résultats. L’exemple ci-dessous peut vous aider à comprendre la différence.

Dans cette tâche, vous allez modifier **searchMode** et comparer les résultats de recherche selon le mode.

1. Ouvrez la fenêtre de navigateur contenant l’exemple d’application, puis choisissez **Se connecter à Visual Studio Team Services**.

    ![][8]

2. Ouvrez **Search.cshtml**, recherchez `searchMode.All` à la ligne 39 et remplacez-le par `searchMode.Any`.

    ![][9]

3. Dans la barre d’index à droite, cliquez sur **Exécuter**.

    ![][10]
 
Dans la fenêtre d’application reconstruite, entrez un terme de recherche que vous avez utilisé précédemment, tel que `Yellowstone +center +building -ND`, et comparez les résultats avant et après les modifications apportées à **searchMode**.

Il existe une différence assez importante. Au lieu de 7 résultats de recherche, vous en obtenez plus de 2 millions.

   ![][11]
 
Le comportement que vous observez est dû à l’inclusion de l’opérateur `NOT` (dans ce cas, « -ND »), qui se transforme en *OR* plutôt qu’en *AND* lorsque **searchMode** est défini sur `Any`.

Avec cette configuration, les résultats de recherche incluent les correspondances pour les termes de recherche `Yellowstone`, `center` et `building`, mais également tous les documents qui sont `NOT North Dakota`. Étant donné que seulement 13 081 documents contiennent l’expression `North Dakota`, presque tous les groupes de données sont renvoyés.

Certes, c’est un scénario peu probable, mais il illustre les effets de **searchMode** sur les expressions de recherche qui incluent l’opérateur `NOT`. Il est utile de comprendre pourquoi le problème se produit et comment le corriger si le résultat n’est pas celui que vous souhaitez.

Pour continuer avec ce didacticiel, vous devez rétablir **searchMode** à sa valeur d’origine (la valeur `All` à la ligne 39), exécuter le programme et utiliser l’application reconstruite pour les tâches restantes.
 
## Ajouter un filtre global pour l’État de Washington

Normalement, si vous souhaitez effectuer une recherche sur un sous-ensemble de données disponibles, vous devez définir le filtre à la source de données lors de l’importation des données. À des fins d’apprentissage et puisque nous travaillons avec des données en lecture seule, nous allons définir le filtre de notre application pour renvoyer uniquement les documents qui incluent l’État de Washington.

1. Ouvrez Search.cshtml, recherchez le bloc de code **SearchParameters** (à partir de la ligne 36) et ajoutez une ligne de commentaire avec filtre.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


Les filtres sont spécifiés à l’aide de la syntaxe OData et sont fréquemment utilisés avec la navigation par facettes ou inclus dans la chaîne de requête pour limiter la requête. Pour plus d’informations, voir [Syntaxe de filtre OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

2. Cliquez sur **Exécuter**.

3. Ouvrez l’application.

4. Tapez le caractère générique (*) pour renvoyer un nombre. Notez que les résultats sont désormais limités à 42 411 éléments, qui correspondent à tous les documents pour toutes les fonctionnalités géographiques dans l’État de Washington.

   ![][12]

## Ajouter la mise en surbrillance des correspondances

Maintenant que vous avez apporté une série de modifications du code d’une ligne, vous souhaitez peut-être appliquer des changements plus importants nécessitant des modifications du code à plusieurs endroits. La version suivante de **Search.cshtml** peut être collée sur le fichier Search.cshtml dans votre session active.

Le code suivant ajoute la mise en surbrillance des correspondances. Prenez note des nouvelles propriétés déclarées dans [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). Il existe également une nouvelle fonction qui permet d’effectuer une itération sur la collection de résultats pour obtenir les documents nécessitant la mise en surbrillance, ainsi que le format HTML pour afficher le résultat.

Lorsque vous exécutez cet exemple de code, les entrées de terme de recherche qui ont une valeur correspondante dans les champs spécifiés sont mises en surbrillance en gras.

   ![][14]

Vous souhaiterez peut-être enregistrer une copie du fichier **Search.cshtml** d’origine pour pouvoir comparer les deux versions.

> [AZURE.NOTE] Les commentaires ont été tronqués pour réduire la taille du fichier.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }

## Pourquoi choisir Azure Search ?

Bien que les autres services Azure, tels que la base de données SQL permettent la recherche de texte intégral, Azure Search vous offre le contrôle du réglage, la pagination et les nombres, la mise en surbrillance des correspondances, la saisie semi-automatique, la prise en charge du langage naturel, la navigation par facettes, le filtrage, et bien plus encore. Comme le démontrent nos différents [exemples](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search), il est possible de développer une application complète basée sur la recherche à l’aide d’Azure Search et d’ASP.NET.

## Étapes suivantes

Si vous utilisez le service en lecture seule fourni sur le site [Essayer Azure App Service](https://tryappservice.azure.com/), vous avez vu la syntaxe de requête et la recherche en texte intégral en action, découvert searchMode et les filtres et ajouté la mise en surbrillance des correspondances à votre application de recherche. Pour l’étape suivante, envisagez de passer à la création et à la mise à jour d’index. Cela permet d’effectuer les actions suivantes :

- [Définir les profils de score](https://msdn.microsoft.com/library/dn798928.aspx) utilisés pour le paramétrage des résultats de recherche afin que les articles de grande valeur apparaissent en premier.
- [Définir des générateurs de suggestions](https://msdn.microsoft.com/library/mt131377.aspx) qui ajoutent des suggestions prédictives ou de saisie semi-automatique en réponse à une entrée utilisateur.
- [Définir les indexeurs](https://msdn.microsoft.com/library/dn946891.aspx) qui mettent à jour vos index automatiquement chaque fois que la source de données est la base de données SQL Azure ou Azure DocumentDB.

Pour effectuer toutes ces tâches, vous aurez besoin d’un abonnement Azure afin de pouvoir créer et remplir des index dans un service. Pour plus d’informations sur la façon de s’inscrire à un essai gratuit, visitez le site [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Pour en savoir plus sur Azure Search, visitez notre [page de documentation](https://azure.microsoft.com/documentation/services/search/) sur [http://azure.microsoft.com](https://azure.microsoft.com/) ou découvrez des [exemples et des vidéos](search-video-demo-tutorial-list.md) qui explorent toute la gamme des fonctionnalités Azure Search.

## Informations sur le code et Azure Search

Azure Search est un service [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (Platform as a Service) entièrement géré qui permet aux développeurs d’intégrer facilement des expériences de recherche performantes dans des applications web et mobiles. Les données utilisées dans les opérations de recherche sont stockées avec votre service de recherche dans Azure, où la proximité des données avec les opérations garantit une faible latence et des comportements de recherche cohérents. Regardons cela plus en détail :

- Les données interrogeables sont stockées dans un index géré par Azure Search.
- Le schéma qui définit votre index est défini par l’utilisateur et spécifie les champs pouvant faire l’objet d’une recherche, les champs ne pouvant pas faire l’objet d’une recherche qui pourraient être utiles dans une expression de filtre et les constructions, telles que les profils de score pour le paramétrage des résultats.
- Un index de recherche contient un ou plusieurs documents (qui s'apparentent à des lignes de tableau) qui peuvent être interrogés et récupérés.
- La plupart des index sont chargés à partir d’un groupe de données unique, préparé à l’avance par vous-même pour inclure uniquement les champs utiles dans le cadre des opérations de recherche. 
- Les données peuvent être chargées automatiquement par un indexeur (pris en charge pour la base de données SQL Azure ou Azure DocumentDB uniquement) ou transmises à un index de recherche vis des API Azure Search. Lorsque vous utilisez les API, vous pouvez envoyer des données à partir de n’importe quelle source de données, du moment qu’elles sont au format JSON.

Dans l’option [Azure App Service](https://tryappservice.azure.com/), le modèle « Site ASP.NET + Azure Search » fournit le code source pour l’application web, modifiable dans Visual Studio Team Services (disponible dans le cadre de la session d’une heure). Aucun outil de développement distinct n’est requis pour afficher ou modifier le code.

Le code est écrit en C#, à l’aide de la [bibliothèque cliente Azure Search .NET](https://msdn.microsoft.com/library/dn951165.aspx) pour exécuter des requêtes sur l’index, fournir une navigation par facettes, ainsi qu’afficher des nombres et des résultats de recherche dans une page web.

Un autre code, non inclus dans le modèle, a été utilisé pour créer et charger l’index de recherche USGS. Dans la mesure où le service est en lecture seule, toutes les opérations qui requièrent un accès en écriture doivent être effectuées à l’avance. Vous pourrez voir une [copie du schéma](#schema) utilisé pour générer le schéma à la fin de cet article.

<a name="Schema"></a>
## À propos du schéma

La capture d’écran suivante montre le schéma utilisé pour créer l’index de ce modèle.
 
   ![][13]

### Fichier schema.json

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }

<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=AcomDC_0211_2016-->