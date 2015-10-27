<properties
	pageTitle="Connecter Azure Search avec les applications web ASP.NET MVC | Microsoft Azure"
	description="Raccorder une application web ASP.NET avec Azure Search. Apprenez à vous connecter, à interroger et à afficher des résultats à l’aide de la bibliothèque .Net ou de l’API REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="08/26/2015"
	ms.author="heidist"/>

#Processus d’intégration d’Azure Search avec des applications web ASP.NET MVC

ASP.NET MVC est l’infrastructure d’application web prédominante dans les solutions personnalisées s’intégrant à Azure Search. Dans cet article, vous allez apprendre à connecter votre application web ASP.NET à Azure Search, à démarrer rapidement avec des modèles de conception pour les opérations courantes et à passer en revue quelques pratiques de codage qui pourraient simplifier votre expérience de développement.

##Exemples et démonstrations de l’utilisation d’ASP.NET et d’Azure Search

Plusieurs exemples de code existent déjà et montrent comment Azure Search s’intègre avec ASP.NET. Vous pouvez accéder directement au code ou à une application de démonstration en visitant l’un des liens suivants :

- [Site de démonstration New York City (NYC) jobs](http://aka.ms/azjobsdemo)
- [Essayer Azure App Service avec Azure Search](search-tryappservice.md)
- [Liste complète des vidéos, didacticiels, démonstrations et exemples de code](earch-video-demo-tutorial-list.md)

##Connexion au service

Pour établir une connexion au service et émettre des demandes, votre application web a besoin de trois éléments uniquement :

- Une URL vers le service Azure Search que vous avez configuré, au format https://<service-name>.search.windows.net
- Une clé API (chaîne) qui authentifie la connexion à Azure Search
- Un HTTPClient ou SearchServiceClient pour formuler la demande de connexion

####URL et clés API

Vous pouvez trouver l’URL et la clé API dans le [portail](search-create-service-portal.md) ou les récupérer par programme à l’aide de l’[API REST de gestion](https://msdn.microsoft.com/library/dn832684.aspx).

En règle générale, les URL et la clé sont placées dans un fichier web.config de votre programme d’interaction utilisateur :

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

Le nom de service de Search peut être le nom court que vous avez spécifié lors de l’approvisionnement à condition que vous ajoutiez le domaine (search.windows.net) sur la connexion, ou bien vous pouvez spécifier le nom qualifié complet (<service-name>.search.windows.net) dans le fichier web.config sans le préfixe HTTPS.

La clé API est un jeton d’authentification généré au cours de l’approvisionnement du service (clés d’administration uniquement) ou généré manuellement si vous créez des clés de requête dans le portail. Le type de clé détermine les opérations de recherche qui sont disponibles pour votre application :

- Clés d’administration (autorisations en lecture-écriture, 2 par service)
- Clés de requête (lecture seule, jusqu’à 50 par service)

Les clés API sont des chaînes de 32 caractères. Visuellement, il n’existe aucune distinction entre clés d’administration et clés de requête. Si vous oubliez quel type de clé vous avez spécifié dans le code, vous devez le vérifier dans le portail ou utiliser l'API REST de gestion pour renvoyer le type de clé. Pour en savoir plus sur les clés, consultez [API REST du service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx).

> [AZURE.TIP]Une clé de requête offre une expérience en lecture seule au client. Consultez la page [Essayer App Service avec Azure Search](search-tryappservice.md) pour tester les opérations Azure Search qui sont disponibles dans un service en lecture seule. Notez qu’avec la méthode Essayer Azure App, le code d’application web est entièrement modifiable. Vous pouvez modifier le code C# dans le projet ASP.NET pour modifier la disposition de la page web, rechercher une construction de requête ou rechercher des résultats. Seules les opérations d’index de services et de chargement de documents Azure Search sont en lecture seule par l’inclusion d’une clé API de requête sur la connexion du service.

####Connexion client

Les deux extraits de code suivants établissent une connexion au service Search à l’aide de l’URL et de la clé API. N’oubliez pas que le nom du service et les clés API sont spécifiés dans le fichier web.config. Pour les appels REST, les clés d’administration doivent être transmises dans l’en-tête de demande, tandis que les clés de requête peuvent être transmises dans l’en-tête ou directement dans l’URL.

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) avec les appels d’API REST**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) avec .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##Modèles de conception

Une application web qui s’intègre à Azure Search devra formuler des requêtes et afficher les résultats. Cette section fournit des conseils sur la manière de structurer le code pour les tâches exécutées dans un programme qui contient du code d’interaction utilisateur. La définition de schéma, la génération d’index et l’ingestion de données sont délibérément exclues. Pour obtenir des instructions sur la façon de coder ces opérations, consultez les procédures pas à pas et les exemples répertoriés dans [Vidéos, exemples et didacticiels dans Azure Search](search-video-demo-tutorial-list.md).

###Formulation de requêtes

Une recherche en texte intégral sur votre index est exécutée sur les champs marqués comme **isSearchable** dans le schéma qui définit l’index. Selon l’entrée de terme de recherche (représentée par la chaîne "q" ci-dessous), le moteur de recherche recherche une correspondance dans tous les champs pouvant faire l’objet d’une recherche et renvoie les résultats à partir des champs marqués comme **isRetrievable**.

> [AZURE.NOTE]Bien que la plupart des champs puissent faire l’objet d’une recherche, un index peut contenir des champs utilisés uniquement dans les expressions de filtre, auquel cas vous les marqueriez comme ne pouvant pas faire l’objet d’une recherche (afin de les exclure de la recherche en texte intégral) et non affichables dans les résultats d’une recherche (afin de les exclure des résultats de la recherche).

Une requête de recherche encapsule le terme d’entrée fourni par l’utilisateur dans une demande Azure Search qui spécifie l’index cible, ainsi que les paramètres utilisés pour filtrer ou affiner la demande. Les opérateurs incorporés dans la chaîne de recherche, tels que +, - ou |, sont gérés automatiquement, ce qui signifie qu’il n’existe aucune exigence de codage pour analyser un terme de recherche. Toute analyse est effectuée par le moteur de recherche, comme une opération interne. Vous pouvez supposer que la chaîne que vous transmettez sera analysée par le moteur.

Les requêtes de recherche sont disponibles en deux versions : **Recherche** ou **Suggestions**. Vous devriez définir des méthodes distinctes pour chaque type de requête. **Recherche** est la recherche en texte intégral sur les champs de votre index. **Suggestions** est la fonctionnalité de requête de prédiction de saisie ou de saisie semi-automatique dans Azure Search qui génère une liste de termes de recherche potentiels basée sur les trois premiers caractères de l’entrée utilisateur. Dans la plupart des cas, vous devez limiter les **Suggestions** aux champs qui contiennent des valeurs relativement uniques ou distinctives (comme un nom de produit ou de publication), par opposition aux champs qui contiennent des données indifférenciées.

L’extrait de code suivant capture une entrée de terme de recherche dans un programme qui utilise l’API REST. Le terme d’entrée est représenté par la chaîne q et les paramètres restants sont utilisés pour transmettre des valeurs de filtre à partir d’une structure de navigation par facettes sur la même page de recherche. Les paramètres de terme d’entrée et de filtre sont utilisés dans la méthode Recherche.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
La méthode **Recherche** qui accepte cette requête est définie comme suit. Notez qu’elle définit les paramètres de la chaîne de requête, ainsi que la structure de navigation par facettes (prise en charge par le biais des filtres qui effectuent le gros du travail pour affiner les résultats de recherche) et l’ordre de tri.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

Une méthode .NET qui construit une chaîne de recherche peut être placée dans une vue MVC ou dans un contrôleur. Cette fonction transmet la chaîne au contrôleur d’accueil. En outre, elle définit une structure de données pour les résultats.

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

Une méthode .NET pour l’appel de **Recherche** peut ressembler à ceci, contenu dans le principal programme C# qui fournit la connexion et l’opération de recherche :

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###Gestion des résultats de recherche

Les résultats de recherche sont renvoyés comme un ensemble de lignes composé de champs qui sont marqués dans le schéma d’index comme isRetrievable. L’un des moyens les plus simples d’afficher un jeu de résultats consiste à utiliser l’objet système ViewBag dans MVC. L’extrait de code suivant provient d’Index.cshtml dans le [projet AdventureWorksDemo sur CodePlex](https://azuresearchadventureworksdemo.codeplex.com/).

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###Navigation à facettes

Dans le même fichier Index.cshtml, vous trouverez le code HTML utilisé pour créer une structure de navigation par facettes qui fournit des classifications pour le filtrage autonome, en affinant progressivement les résultats de recherche par couleur, par prix ou par catégorie.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###Mise en surbrillance des correspondances

L’application d’un style à l’instance du terme de recherche dans un résultat de recherche est appelée mise en surbrillance des correspondances. Dans Azure Search, la mise en surbrillance des correspondances est spécifiée dans la requête, via le paramètre de mise en surbrillance de la recherche auquel vous attribuez une liste de champs délimitée par des virgules à analyser pour les termes correspondants. Le style réel que vous appliquez dépend de vous. Les trois extraits de code suivants sont issus du didacticiel [Essayer Azure App Service avec Azure Search](search-tryappservice.md).

Tout d’abord, spécifiez la mise en surbrillance des correspondances comme paramètre de recherche et répertoriez les champs à vérifier pour les termes correspondants. Spécifiez le style HTML à utiliser sur la mise en surbrillance des correspondances.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

Ensuite, effectuez une itération dans les résultats de la recherche pour rechercher la chaîne devant être mise en surbrillance. private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

En dernier lieu, fournissez la mise en page des résultats de recherche, en spécifiant le jeu de résultats qui a été évalué dans l’extrait précédent.

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


##Pratiques de codage courantes

Vous débutez avec MVC, la programmation .NET ou les API REST ? Les sections suivantes proposent quelques pratiques de codage pour vous aider à être opérationnel.

###Modèle MVC

Le tableau suivant résume la manière dont les composants du modèle MVC sont utilisés dans les applications qui incluent Azure Search. Si vous utilisez MVC 4 ou MVC 5, le code qui intègre Azure Search sera généralement ajouté à ces modules.

Fichier|Description
----|-----------
Web.config|Fournissez l’URL et la clé API du service. Ajoutez une référence à System.Configuration dans votre module de programme principal pour lire les valeurs.
Program.cs|Dans le programme principal, définissez un HttpClient ou un SearchServiceClient pour établir une connexion au service. Ajoutez la méthode Recherche à ce programme.
DataModel|Non utilisé. En supposant que les opérations de création d’index et de chargement de données sont dans des programmes différents, aucun modèle de données n’est requis pour Azure Search dans votre application web.
Views|Une vue contient le code HTML de la page web d’application, des entrées de la zone de recherche au code HTML dynamique pour la gestion des résultats de la recherche.
Controllers|La construction des requêtes et la gestion des erreurs se trouvent généralement dans HomeController.cs. Au minimum, le contrôleur doit inclure une méthode de recherche qui récupère les résultats d’Azure Search et transmet le jeu de résultats à la vue. 

Éventuellement, si vous utilisez des suggestions pour les requêtes de saisie semi-automatique, vous pouvez inclure une méthode qui renvoie des requêtes suggérées, selon que votre index contient une valeur correspondant à l’entrée de terme de recherche fourni par l’utilisateur.

###Quand utiliser la bibliothèque cliente .NET par opposition à l’ API REST

Pour les applications ASP.NET, la bibliothèque cliente .NET est considérée comme un meilleur choix car elle configure la connexion HTTP et gère la sérialisation et la désérialisation JSON pour vous, ce qui simplifie votre code.

Dans certains cas, votre choix d’API peut être dicté par parité des fonctionnalités entre les deux approches. En général, la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) et [l’API REST de service](https://msdn.microsoft.com/library/azure/dn798935.aspx) sont interchangeables dans la mesure où les opérations dont vous avez besoin sont mises en place dans les deux. Cependant, parfois, de nouvelles fonctionnalités apparaissent d’abord dans l’API REST dans le cadre d’une version préliminaire et sont uniquement ajoutées à la bibliothèque .NET des mois plus tard. Par exemple, les indexeurs, qui sont utilisés pour automatiser les opérations de chargement de données à partir de types de sources de données spécifiques, apparaissent dans l’aperçu API REST préliminaire avant d’apparaître dans la bibliothèque cliente quelques mois plus tard. Les restrictions d’implémentation des fonctionnalités sont indiquées dans la documentation des fonctionnalités.

###Inclure AzureSearchHelper.cs pour la sérialisation et la désérialisation JSON dans l’API REST

Contrairement à la bibliothèque .NET qui effectue cette opération pour vous, les API REST de service doivent sérialiser et désérialiser les documents JSON dans l’échange demande-réponse avec le service. JSON est un format de charge utile pour la transmission de données lors du chargement ou de l’actualisation des documents dans l’index.

Le code pour la sérialisation JSON peut se trouver dans plusieurs exemples, dans un fichier nommé **AzureSearchHelper.cs** :

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}

###Organisation de votre code

Le fractionnement de vos charges de travail dans des projets autonomes au sein de la même solution Visual Studio vous offre davantage de flexibilité dans la façon dont vous concevez, gérez et exécutez chaque programme. Nous vous recommandons d’en créer trois :

- Code de création d’index
- Code d’ingestion de données
- Code d’interaction utilisateur

Dans Azure Search, les opérations d’indexation et de document (comme l’ajout ou la mise à jour de documents, ou l’exécution de requêtes) sont totalement indépendantes les unes des autres. Cela signifie que vous pouvez dissocier la gestion des index de votre code d’interaction utilisateur ASP.NET qui formule des demandes de recherche et affiche les résultats.

Dans la plupart de nos exemples de code, l’index est à la fois créé et chargé dans un projet (appelé DataIndexer, CatalogIndexer ou DataCatalog dans différents exemples), tandis que le code qui gère les demandes de recherche et les réponses est placé dans un projet d’application ASP.NET MVC. Dans les exemples de code, il est pratique de regrouper la création d’index et le chargement de documents dans un projet, mais un code de production isolerait probablement ces opérations. Une fois qu’un index est créé, il est rarement modifié (et, s’il est modifié, il doit dans certains cas être reconstitué), tandis que les documents sont susceptibles d’être actualisés de façon récurrente.

Séparer les charges de travail offre d’autres avantages : différents niveaux d’autorisations pour Azure Search (droits d’administrateur complets par rapport aux droits de requêtes uniquement), utilisation de différents langages de programmation, plus de dépendances spécifiques par programme, ainsi que possibilité de réviser les programmes de manière indépendante ou de créer plusieurs applications frontales qui fonctionnent toutes sur l’index constitué et géré par une application d’indexation centrale.

##Étapes suivantes

Pour mieux comprendre l’intégration entre Azure Search et ASP.NET, consultez les liens suivants :

- [Comment utiliser Azure Search à partir d'une application .NET](search-howto-dotnet-sdk.md) 
- [Étude de cas d’un développeur Azure Search](search-dev-case-study-whattopedia.md)
- [Flux de travail typique de développement Azure Search](search-workflow.md) 

<!---HONumber=Oct15_HO4-->