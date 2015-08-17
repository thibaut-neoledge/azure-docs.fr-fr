<properties
	pageTitle="Prise en main de votre première application Azure Search dans .NET | Microsoft Azure"
	description="Didacticiel sur la création d'une solution Visual Studio à l'aide de la bibliothèque cliente .NET à partir du kit de développement logiciel (SDK) .NET Azure Search."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>


<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>


#Prise en main de votre première application Azure Search dans .NET#

Apprenez à créer une application de recherche .NET personnalisée dans Visual Studio 2013 ou version ultérieure qui utilise Azure Search pour son expérience de recherche. Le didacticiel utilise le [kit de développement logiciel (SDK) .NET Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx) pour générer des classes pour les objets et les opérations utilisés dans cet exercice, ainsi que l'API REST du service Azure Search.

Pour exécuter cet exemple, vous devez disposer d'un service Azure Search, auquel vous pouvez vous connecter dans le [portail Azure](https://portal.azure.com).

> [AZURE.TIP]Téléchargez le code source pour ce didacticiel dans les [exemples d'Azure Search .NET](http://go.microsoft.com/fwlink/p/?LinkId=530196).

##À propos des données##

Cet exemple d'application utilise des données de l’[USGS (United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Rhode Island pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui renvoie des bâtiments repères, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Dans cette application, le programme **DataIndexer** crée et charge l'index à l'aide d'une construction de type [Index](https://msdn.microsoft.com/library/azure/dn798918.aspx), en récupérant le jeu de données USGS filtré à partir d'une base de données SQL Azure publique. Les informations d'identification et de connexion à la source de données en ligne sont fournies dans le code du programme. Aucune configuration supplémentaire n'est nécessaire.

> [AZURE.NOTE]Nous avons appliqué un filtre à ce jeu de données pour ne pas dépasser la limite de 10 000 documents du niveau de tarification gratuit. Si vous utilisez le niveau standard, cette limite ne s'applique pas. Pour plus d'informations sur la capacité de chaque niveau de tarification, consultez la section [Limites et contraintes](https://msdn.microsoft.com/library/azure/dn798934.aspx).

##Créer le service##

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la barre de lancement, cliquez sur **Nouveau** | **Données + stockage** | **Recherche**.

     ![][1]

3. Définissez le nom du service, le niveau de tarification, le groupe de ressources, l'abonnement et l'emplacement. Ces paramètres sont requis et ne sont plus modifiables une fois le service approvisionné.

     ![][2]

	- Le **nom du service** doit être unique, en minuscules et contenir moins de 15 caractères sans espaces. Il devient partie intégrante du point de terminaison de votre service Azure Search. Consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) pour plus d'informations sur les conventions d'affectation des noms.

	- Le **niveau de tarification** détermine la capacité et la facturation. Les deux niveaux offrent les mêmes fonctionnalités, mais à des niveaux de ressources différents.

		- La **version gratuite** s'applique aux clusters partagés avec d'autres abonnés. Elle offre une capacité suffisante pour tester les didacticiels et écrire du code de validation technique, mais elle n'est pas destinée aux applications de production. En général, le déploiement d'un service gratuit ne prend que quelques minutes.
		- La version **standard** s'applique aux ressources dédiées et est hautement évolutive. À l'origine, un service standard est fourni avec un réplica et une partition, mais vous pouvez ajuster la capacité une fois le service créé. Le déploiement d'un service standard prend plus de temps, environ quinze minutes.

	- Les **groupes de ressources** sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, Sites web Azure ou le stockage d'objets blob, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

	- L’**abonnement** vous permet de choisir votre abonnement, si vous en avez plusieurs.

	- L’**emplacement** correspond à la zone géographique du centre de données. Actuellement, toutes les ressources doivent s'exécuter dans le même centre de données. La répartition de ressources entre plusieurs centres de données n'est pas prise en charge.

4. Cliquez sur **Créer** pour approvisionner le service.

Surveillez les notifications dans la barre de lancement. Une notification s'affiche lorsque le service est prêt à être utilisé.

<a id="sub-2"></a>
##Recherche du nom du service et des clés api de votre service Azure Search

Une fois le service créé, vous pouvez revenir au portail pour obtenir l'URL ou la `api-key`. Pour vous connecter à votre service de recherche, vous devez saisir l'URL et une `api-key` afin d’authentifier l'appel.

1. Dans la barre de lancement, cliquez sur **Accueil** puis sur le service de recherche pour ouvrir le tableau de bord du service.

2. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.

  	![][3]

3. Copiez l'URL du service et une clé administrateur. Vous en aurez besoin plus tard, lorsque vous les ajouterez aux fichiers app.config et web.config dans vos projets Visual Studio.

##Lancement d'un nouveau projet et solution##

Cette solution incluera deux projets :

- **DataIndexer**, une application console Visual C#, utilisée pour charger des données.
- **SimpleSearchMVCApp**, une application Web Visual C# ASP.NET MVC, utilisée pour interroger et renvoyer les résultats de la recherche.

Dans cette étape, vous allez créer les deux projets.

1. Démarrez **Visual Studio** | **Nouveau projet** | **Visual C#** | **Application console**.
2. Nommez le projet **DataIndexer** puis nommez la solution **AzureSearchDotNetDemo**.
3. Dans l'Explorateur de solutions, cliquez avec le bouton droit de la souris sur la solution et sélectionnez **Ajouter** | **Nouveau projet** | **Visual C#** | **Application Web ASP.NET**.
4. Nommez le projet **SimpleSearchMVCApp**.
5. Dans le nouveau projet ASP.NET, choisissez le modèle MVC et désactivez les options pour éviter de créer des artefacts de programme que vous n'utiliserez pas dans ce didacticiel.

   Désactivez les cases à cocher pour l'hébergement Azure et les tests unitaires, puis définissez l'authentification sur Aucune.

   ![][10]

Lorsque vous avez terminé la création des projets, votre solution doit ressembler à l'exemple ci-dessous.

   ![][4]

##Installation de la bibliothèque cliente .NET et mise à jour d'autres packages

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**.
2. Spécifiez **Mises à jour** | **Stable uniquement** | **Tout mettre à jour**.

   ![][11]

3. Acceptez les installations de packages supplémentaires, afin que toutes les dépendances soient également installées.

4. Ensuite, installez la bibliothèque cliente .NET Azure Search. Veillez à spécifier la recherche correctement ou vous ne trouverez pas facilement le package. Cliquez de nouveau avec le bouton droit sur **Gérer les packages NuGet**.

5. Spécifiez **En ligne** | **nuget.org** | **Inclure la version préliminaire**, puis recherchez *azure.search* et installez la bibliothèque.

   ![][12]

Vous trouverez ci-dessous une liste partielle des assemblages utilisés dans cet exemple.

   ![][5]

##Ajout d'une référence d'assemblage pour System.Configuration

**DataIndexer** utilise **System.Configuration** pour lire les paramètres de configuration dans app.config.

1. Cliquez avec le bouton droit sur **DataIndexer** | **Ajouter** | **Référence** | **Framework** | **System.Configuration**. Activez la case à cocher.
2. Cliquez sur **OK**.

##Mise à jour des fichiers de configuration

Chaque projet inclut des fichiers de configuration qui spécifient le nom du service et la clé d'api.

1. Dans **DataIndexer**, remplacez App.config par l'exemple suivant, en mettant à jour le [NOM DE SERVICE\] et la [CLÉ du SERVICE\] avec des valeurs qui sont valides pour votre service.

   Le nom du service n'est pas l'URL complète. Par exemple, si le point de terminaison de votre service de recherche est **https://mysearchsrv.search.microsoft.net*, le nom de service à entrer dans App.config est *mysearchsrv*.

	    <?xml version="1.0" encoding="utf-8"?>
	    <configuration>
	      <startup>
	         <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />

	      </startup>
	      <appSettings>
	        <add key="SearchServiceName" value="[SERVICE NAME]" />

	        <add key="SearchServiceApiKey" value="[SERVICE KEY]" />

	      </appSettings>
	    </configuration>

2. Dans **SimpleSearchMVCApp**, remplacez Web.config par l'exemple suivant, en mettant de nouveau à jour le [NOM DE SERVICE\] et la [CLÉ du SERVICE\] avec des valeurs qui sont valides pour votre service.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		  For more information on how to configure your ASP.NET application, please visit
		  http://go.microsoft.com/fwlink/?LinkId=152368
		  -->
		<configuration>
		  <configSections>
		    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
		    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />

		  </configSections>
		  <connectionStrings>
		    <add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />

		  </connectionStrings>
		  <appSettings>
		    <add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />

		    <add key="SearchServiceApiKey" value="[API KEY]" />


		    <add key="webpages:Version" value="2.0.0.0" />

		    <add key="webpages:Enabled" value="false" />

		    <add key="PreserveLoginUrl" value="true" />

		    <add key="ClientValidationEnabled" value="true" />

		    <add key="UnobtrusiveJavaScriptEnabled" value="true" />

		  </appSettings>
		  <system.web>
		    <httpRuntime targetFramework="4.5" />

		    <compilation debug="true" targetFramework="4.5" />

		    <authentication mode="Forms">
		      <forms loginUrl="~/Account/Login" timeout="2880" />

		    </authentication>
		    <pages>
		      <namespaces>
		        <add namespace="System.Web.Helpers" />

		        <add namespace="System.Web.Mvc" />

		        <add namespace="System.Web.Mvc.Ajax" />

		        <add namespace="System.Web.Mvc.Html" />

		        <add namespace="System.Web.Optimization" />

		        <add namespace="System.Web.Routing" />

		        <add namespace="System.Web.WebPages" />

		      </namespaces>
		    </pages>
		    <profile defaultProvider="DefaultProfileProvider">
		      <providers>
		        <add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />

		      </providers>
		    </profile>
		    <membership defaultProvider="DefaultMembershipProvider">
		      <providers>
		        <add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />

		      </providers>
		    </membership>
		    <roleManager defaultProvider="DefaultRoleProvider">
		      <providers>
		        <add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />

		      </providers>
		    </roleManager>
		    <!--
		            If you are deploying to a cloud environment that has multiple web server instances,
		            you should change session state mode from "InProc" to "Custom". In addition,
		            change the connection string named "DefaultConnection" to connect to an instance
		            of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
		      -->
		    <sessionState mode="InProc" customProvider="DefaultSessionProvider">
		      <providers>
		        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />

		      </providers>
		    </sessionState>
		  </system.web>
		  <system.webServer>
		    <validation validateIntegratedModeConfiguration="false" />

		    <handlers>
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />

		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />

		      <remove name="ExtensionlessUrlHandler-Integrated-4.0" />

		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />

		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />

		      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />

		      <remove name="OPTIONSVerbHandler" />

		      <remove name="TRACEVerbHandler" />

		    </handlers>
		  </system.webServer>
		  <entityFramework>
		    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
		      <parameters>
		        <parameter value="v12.0" />

		      </parameters>
		    </defaultConnectionFactory>
		  </entityFramework>
		  <runtime>
		    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
		      <dependentAssembly>
		        <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />

		        <bindingRedirect oldVersion="0.0.0.0-6.0.0.0" newVersion="6.0.0.0" />

		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />

		        <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />

		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />

		        <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />

		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />

		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />

		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />

		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />

		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />

		        <bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />

		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />

		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />

		      </dependentAssembly>
		    </assemblyBinding>
		  </runtime>
		</configuration>


##Modification de DataIndexer

Ce programme est une application de console qui se connecte à votre service de recherche, comme spécifié dans app.config, crée l'index et charge ensuite le jeu de données USGS stocké dans une base de données SQL Azure.

Actuellement, la version préliminaire de la bibliothèque cliente ne prend pas en charge les indexeurs. Nous allons donc utiliser l'API REST pour créer et utiliser un indexeur pour cette partie du didacticiel.

Avant de pouvoir exécuter ce programme, vous allez effectuer deux modifications :

- Ajouter **AzureSearchhelper.cs**, utilisé lors de l'appel de l'API REST pour gérer les connexions, sérialiser et désérialiser les requêtes et les réponses JSON.

- Remplacer **Program.cs**, utilisé pour créer l'index, l'indexeur, charger les données et écrire des messages.

###Création de AzureSearchHelper.cs

Le code qui appelle l'API REST doit inclure une classe qui gère les connexions, ainsi que la sérialisation et la désérialisation des requêtes et des réponses JSON. Dans les exemples fournis avec Azure Search, cette classe est généralement appelée **AzureSearchHelper.cs**. Vous pouvez créer cette classe et l'ajouter à **DataIndexer**, en utilisant le code suivant.

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **DataIndexer** | **Ajouter** | **Nouvel élément** | **Code** | **Classe**.
2. Nommez la classe **AzureSearchHelper**.
3. Remplacez le code par défaut par le code suivant.

		//Copyright 2015 Microsoft

		//Licensed under the Apache License, Version 2.0 (the "License");
		//you may not use this file except in compliance with the License.
		//You may obtain a copy of the License at

		//       http://www.apache.org/licenses/LICENSE-2.0

		//Unless required by applicable law or agreed to in writing, software
		//distributed under the License is distributed on an "AS IS" BASIS,
		//WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		//See the License for the specific language governing permissions and
		//limitations under the License.

		using System;
		using System.Net.Http;
		using System.Text;
		using Newtonsoft.Json;
		using Newtonsoft.Json.Converters;
		using Newtonsoft.Json.Serialization;

		namespace DataIndexer
		{
		    public class AzureSearchHelper
		    {
		        public const string ApiVersionString = "api-version=2015-02-28";

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



###Mise à jour de Program.cs

1. Dans l'Explorateur de solutions, ouvrez **DataIndexer** | **Program.cs**
2. Remplacez le contenu de Program.cs par le code suivant.

		using Microsoft.Azure;
		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using Microsoft.Spatial;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Timers;

		namespace DataIndexer
		{
		    class Program
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        // This Sample shows how to delete, create, upload documents and query an index
		        static void Main(string[] args)
		        {
		            string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		            string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		            // Create an HTTP reference to the catalog index
		            _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		            _indexClient = _searchClient.Indexes.GetClient("geonames");

		            Console.WriteLine("{0}", "Deleting index...\n");
		            if (DeleteIndex())
		            {
		                Console.WriteLine("{0}", "Creating index...\n");
		                CreateIndex();
		                Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
		                SyncDataFromAzureSQL();
		            }
		            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
		            Console.ReadKey();
		        }

		        private static bool DeleteIndex()
		        {
		            // Delete the index if it exists
		            try
		            {
		                _searchClient.Indexes.Delete("geonames");
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error deleting index: {0}\r\n", ex.Message.ToString());
		                Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
		                return false;
		            }

		            return true;
		        }

		        private static void CreateIndex()
		        {
		            // Create the Azure Search index based on the included schema
		            try
		            {
		                var definition = new Index()
		                {
		                    Name = "geonames",
		                    Fields = new[]
		                    {
		                        new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
		                    }
		                };

		                _searchClient.Indexes.Create(definition);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error creating index: {0}\r\n", ex.Message.ToString());
		            }

		        }

		        private static void SyncDataFromAzureSQL()
		        {
		            // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
		            Uri _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
		            HttpClient _httpClient = new HttpClient();
		            _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);

		            Console.WriteLine("{0}", "Creating Data Source...\n");
		            Uri uri = new Uri(_serviceUri, "datasources/usgs-datasource");
		            string json = "{ 'name' : 'usgs-datasource','description' : 'USGS Dataset','type' : 'azuresql','credentials' : { 'connectionString' : 'Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;' },'container' : { 'name' : 'GeoNamesRI' }} ";
		            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating data source: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Creating Indexer...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer");
		            json = "{ 'name' : 'usgs-indexer','description' : 'USGS data indexer','dataSourceName' : 'usgs-datasource','targetIndexName' : 'geonames','parameters' : { 'maxFailedItems' : 10, 'maxFailedItemsPerBatch' : 5, 'base64EncodeKeys': false }}";
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Syncing data...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer/run");
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Post, uri);
		            if (response.StatusCode != HttpStatusCode.Accepted)
		            {
		                Console.WriteLine("Error running indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            bool running = true;
		            Console.WriteLine("{0}", "Synchronization running...\n");
		            while (running)
		            {
		                uri = new Uri(_serviceUri, "indexers/usgs-indexer/status");
		                response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
		                if (response.StatusCode != HttpStatusCode.OK)
		                {
		                    Console.WriteLine("Error polling for indexer status: {0}", response.Content.ReadAsStringAsync().Result);
		                    return;
		                }

		                var result = AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
		                if (result.lastResult != null)
		                {
		                    switch ((string)result.lastResult.status)
		                    {
		                        case "inProgress":
		                            Console.WriteLine("{0}", "Synchronization running...\n");
		                            Thread.Sleep(1000);
		                            break;

		                        case "success":
		                            running = false;
		                            Console.WriteLine("Synchronized {0} rows...\n", result.lastResult.itemsProcessed.Value);
		                            break;

		                        default:
		                            running = false;
		                            Console.WriteLine("Synchronization failed: {0}\n", result.lastResult.errorMessage);
		                            break;
		                    }
		                }
		            }
		        }
		    }
		}



##Création et exécution de DataIndexer

1. Cliquez avec le bouton droit sur le projet **DataIndexer**.
2. Créez le projet.
3. Appuyez sur **F5** pour l'exécuter.

Une fenêtre de console avec ces messages doit s'afficher.

![][6]

Dans le portail, un nouvel index **geonames** doit s'afficher. La mise à jour du portail peut prendre plusieurs minutes, donc patientez un peu avant de vérifier les résultats.

![][7]

##Modification de SimpleSearchMVCApp

**SimpleSearchMVC** est une application Web qui s'exécute localement dans IIS Express. Elle fournit une zone de recherche et affiche les résultats de la recherche dans une table.

Avant de pouvoir exécuter ce programme, vous allez effectuer trois modifications :

- Remplacer **HomeController.cs**, utilisé pour accepter une entrée utilisateur. Dans cet exemple, le terme de recherche est stocké dans une variable nommée *q*.

- Remplacer **index.cshtml**, une page Web qui fournit les entrées du terme de recherche et affiche les résultats de la recherche.

- Ajouter **FeatureSearch.cs**, une classe qui crée le client de recherche et exécute la recherche.

###Mise à jour du fichier HomeController.cs

Remplacez le code par défaut par le code suivant.

	using Microsoft.Azure.Search.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;

	namespace SimpleSearchMVCApp.Controllers
	{
	    public class HomeController : Controller
	    {
	        //
	        // GET: /Home/
	        private FeaturesSearch _featuresSearch = new FeaturesSearch();

	        public ActionResult Index()
	        {
	            return View();
	        }

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


	    }
	}


###Mise à jour du fichier Index.cshtml

Remplacez le code par défaut par le code suivant.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
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

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" />
 <button onclick="Search();">Search</button>
	</div>
	<br />

	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


###Ajout de FeaturesSearch.cs

Ajoutez une classe qui fournit des fonctionnalités de recherche à votre application.

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **SimpleSearchMVCApp** | **Ajouter** | **Nouvel élément** | **Code** | **Classe**.
2. Nommez la classe **FeaturesSearch**.
3. Remplacez le code par défaut par le code suivant.

		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Linq;
		using System.Web;

		namespace SimpleSearchMVCApp
		{
		    public class FeaturesSearch
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        public static string errorMessage;

		        static FeaturesSearch()
		        {
		            try
		            {
		                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		                // Create an HTTP reference to the catalog index
		                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		                _indexClient = _searchClient.Indexes.GetClient("geonames");
		            }
		            catch (Exception e)
		            {
		                errorMessage = e.Message.ToString();
		            }
		        }

		        public DocumentSearchResponse Search(string searchText)
		        {
		            // Execute search based on query string
		            try
		            {
		                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
		                return _indexClient.Documents.Search(searchText, sp);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
		            }
		            return null;
		        }

		    }
		}

###Définition de SimpleSearchMVCApp comme projet de démarrage

Cliquez avec le bouton droit sur le projet **SimpleSearchMVCApp** pour le définir comme projet de démarrage.

##Création et exécution de SimpleSearchMVCApp

Lorsque vous créez et exécutez ce programme, une page Web doit s'afficher dans votre navigateur par défaut. Elle doit contenir une zone de recherche pour accéder aux données USGS stockées dans l'index dans votre service Azure Search.

![][8]

###Exécution d'une recherche sur les données USGS

Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Rhode Island. Si vous cliquez sur **Recherche** dans une zone de recherche vide, vous obtiendrez les 50 premières entrées, ce qui correspond à la valeur par défaut.

La saisie d'un terme de recherche donnera au moteur de recherche des critères pour limiter la recherche. Essayez d'entrer un nom régional. *Roger Williams* fut le premier gouverneur de Rhode Island. De nombreux parcs, bâtiments et écoles portent son nom.

![][9]

Vous pouvez également essayer les requêtes suivantes, en ajoutant ou en supprimant des expressions ou des opérateurs pour réorienter votre recherche :

- Pawtucket OU Pembroke
- dinde +cape -cou


##Étapes suivantes##

Ceci est le premier didacticiel Azure Search basé sur .NET et le jeu de données USGS. Au fil du temps, nous étendrons ce didacticiel pour illustrer des fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous connaissez déjà Azure Search, vous pouvez utiliser cet exemple comme tremplin pour tester des générateurs de suggestions (requêtes prédictives ou à saisie semi-automatique), des filtres et la navigation à facettes. Vous pouvez également améliorer la page des résultats de la recherche en ajoutant des décomptes et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](http://azure.microsoft.com/documentation/services/search/). Vous pouvez également cliquer sur les liens dans notre [liste de vidéos et de didacticiels](https://msdn.microsoft.com/library/azure/dn798933.aspx) pour obtenir des informations supplémentaires.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png
[5]: ./media/search-get-started-dotnet/AzSearch-DotNet-Assembly.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG
 

<!---HONumber=August15_HO6-->