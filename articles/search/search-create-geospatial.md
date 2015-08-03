<properties 
	pageTitle="Création d'une application de recherche géospatiale à l'aide de Azure Search" 
	description="Création d'une application de recherche géospatiale à l'aide de Bing et de Azure Search" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Création d'une application de recherche géospatiale à l'aide de Azure Search

Ce didacticiel explique comment ajouter une recherche géospatiale à des applications web à l'aide d'Azure Search et de Bing Maps. La recherche géospatiale vous permet d'identifier des cibles de recherche à une certaine distance d'un point (par ex., trouver tous les restaurants dans un rayon de 5 km de mon emplacement actuel). La fonctionnalité géospatiale dans Azure Search prend en charge les techniques de mappage les plus courantes. Par exemple, si vous souhaitez utiliser des polygones dans une application de gestion de biens immobiliers pour afficher les maisons à vendre dans une zone polygonale, vous pouvez le faire facilement en utilisant OData ou notre syntaxe de recherche simple.

Pour une vue d'ensemble plus détaillée, visionnez cette vidéo Channel 9 relative à [Azure Search et aux données géospatiales](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

![][7]

Pour créer l'application, nous allons utiliser le service de cartographie Bing pour géocoder les adresses chargées à partir d'un fichier CSV, puis stocker les données obtenues dans un index de recherche.

Ce didacticiel permet de créer la [Démo Azure Search – Adventure Works](http://azuresearchadventureworksdemo.codeplex.com). Si vous n'avez pas encore parcouru cette démo, commencez par le faire afin de vous familiariser avec la création d'un index et l'appel de l'API Azure Search à partir d'une application Web.

<a id="sub-1"></a>
## Conditions préalables

+	Visual Studio 2012 ou une version ultérieure avec ASP.NET MVC 4 et SQL Server doivent être installés. Vous pouvez télécharger les éditions Express gratuites si vous n'avez pas encore installé le logiciel : [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) et [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Un service Azure Search. Vous aurez besoin du nom du service Azure Search et de la clé d’administration. Pour obtenir de l'aide, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).
+	Un service Bing Maps et une clé pour y accéder. Les instructions sont fournies dans la section suivante
+	[Exemple de recherche géospatiale Azure Search sur CodePlex](https://azuresearchgeospatial.codeplex.com/). Dans l'onglet Source, cliquez sur **Télécharger** pour obtenir un fichier .zip de la solution. 

    ![][12]

Cette solution inclut deux projets :

+	**StoreIndexer** permet de créer un index Azure Search et de charger les données.
+	**AdventureWorksWebGeo** est une application MVC4 qui effectue des recherches dans l'index Azure Search et affiche les emplacements des magasins sur une carte Bing.

[AZURE.INCLUDE [Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Bing Maps

Nous allons utiliser l'API Bing Maps pour réaliser deux opérations.

+ **Géocodage des adresses** : les données incluent des adresses (ville, province, code postal), mais nous avons également besoin des coordonnées de longitude et de latitude d'une adresse pour pouvoir effectuer des recherches géospatiales. Pour obtenir les coordonnées, nous utiliserons l'API de flux de données Bing Maps afin d'envoyer un lot d'adresses à géocoder. Bien que l'utilisation du compte d'essai Bing nous limite à 50 adresses à la fois, ce sera suffisant pour ce didacticiel.

+ **Bing Maps** : lors de l'exécution de l'application, nous utiliserons Bing Maps pour afficher les emplacements des magasins, superposés sur une carte Bing.

### Création d'un compte Bing Maps

1. Accédez au [Portail Bing Maps](https://www.bingmapsportal.com/) et créez un compte. Entrez les informations requises pour la création du compte.

2. Une fois le compte créé, sélectionnez **Créer ou afficher les clés** et entrez les informations requises pour créer une clé. Pour cette démo, vous pouvez sélectionner **Clé de version d'essai**.

3. Cliquez sur **Soumettre**. Les informations relatives à la clé de votre application Bing Maps doivent s'afficher. Prenez note de cette clé, car nous en aurons besoin plus tard.

<a id="sub-3"></a>
## Géocodage des adresses en C# à l'aide de l'API de flux de données Bing Maps

Lors de cette étape, nous utiliserons l'API de flux de données Bing Maps pour géocoder certaines des adresses de divers magasins de vélos aux quatre coins du monde.

Ces données proviennent d'un fichier CSV appelé store_locations.csv et situé dans la source que vous avez téléchargée précédemment. Si vous ouvrez ce fichier dans un éditeur de texte ou Excel, vous verrez qu'il inclut une colonne avec l'ID de chaque magasin, son nom et son adresse.

Examinons le code afin de comprendre comment cela fonctionne.

1. Ouvrez la solution AdventureWorksGeo dans Visual Studio, développez le projet **StoreIndexer** dans l'Explorateur de solutions et ouvrez Program.cs. Comme nous avons déjà abordé la création d'index dans la [Démonstration Azure Search – Adventure Works](http://azuresearchadventureworksdemo.codeplex.com/), nous ne nous attarderons pas sur son fonctionnement dans Program.cs.

2. Accédez à la fonction **Main** et notez qu'elle appelle **ApplyStoreData**. Accédez à cette fonction et examinez le code.

3. **ApplyStoreData** charge les données d'un fichier CSV appelé « store_locations.csv » dans une System.Data.DataTable.

    Ce fichier inclut tous les magasins, avec leurs adresses que nous souhaitons charger dans Azure Search. En parcourant chaque ligne de ce fichier, nous pouvons créer un ensemble de **indexOperations**, qui sont ensuite insérées dans l'index Azure Search (précédemment créé dans la fonction **CreateStoresIndex()**).

    Si vous examinez alors l'index avec attention, vous noterez que le champ **GeoPt**, qui contiendra la longitude et la latitude de chaque magasin, est vide. Cela nous amène à l'étape suivante de la fonction **Main**.

5. Accédez à la fonction **ExtractAddressInfoToXML()**. Cette fonction extrait les informations d'adresses du fichier store_locations.csv et les charge dans un fichier XML au format compatible avec Bing Maps pour le géocodage. Une fois le fichier créé, il est envoyé pour traitement au flux de données Bing Maps en appelant la fonction **GeoCoding.CreateJob**.

6. Le processus de géocodage pouvant être assez long, une boucle appelle **GeoCoding.CheckStatus** toutes les 10 secondes afin de vérifier si l'opération est terminée. Une fois l'opération terminée, les résultats sont téléchargés en appelant **GeoCoding.DownloadResults** dans la classe d'adresses.

7. La dernière étape consiste à prendre ces adresses géocodées et à les envoyer à Azure Search. Examinons d'un peu plus près comment cela fonctionne en ouvrant la fonction **UpdateStoreData**.

  **UpdateStoreData** utilise l'action **@search.action: merge** pour mettre à jour le champ d'emplacement de type Edm.GeographyPoint avec les coordonnées de longitude et de latitude géocodées, qui viennent d'être téléchargées à partir de Bing Maps. Pour ce faire, la fonction effectue une recherche dans le storeId, qui est la clé unique pour le document dans l'index « stores », et fusionne ces nouvelles données dans le document existant.

8. Avant d'exécuter l'application, ajoutez les informations de votre service Azure Search et de votre API Bing Maps en ouvrant App.config et en mettant à jour les valeurs de « SearchServiceName », « SearchServiceApiKey » et « BingMapsAPI » en les remplaçant par celles de votre service Azure Search et de votre API Bing Maps. Pour le nom du service Azure Search, si votre service est « mysearch.search.windows.net », vous entrerez « mysearch ».

9. Cliquez avec le bouton droit de la souris sur le projet **StoreIndexer** et sélectionnez **Déboguer** | **Démarrer une nouvelle instance** pour l'exécuter.

<a id="sub-4"></a>
## Ajout d'un mappage à une application MVC4 à l'aide de Azure Search et Bing Maps

Lors de cette étape, vous allez créer et exécuter l'application de recherche dans un navigateur Web, qui chargera une recherche de magasins et les affichera graphiquement en haut d'une carte Bing.

1.	Dans Visual Studio, ouvrez la solution de démonstration Azure Search appelée AdventureWorksGeo.sln. 
	
2.	Dans l'Explorateur de solutions, cliquez avec le bouton droit de la souris sur **AdventureWorksWebGeo**, puis sélectionnez **Définir comme projet de démarrage**.
	
3.	Ouvrez Web.config dans ce projet et mettez à jour les valeurs de « SearchServiceName », « SearchServiceApiKey » et « BingMapsAPI » en les remplaçant par celles de votre service Azure Search et de votre API Bing Maps. Pour le nom du service Azure Search, si votre service est « mysearch.search.windows.net », vous entrerez « mysearch ».

4.	Enregistrez Web.config.
	
5.	Appuyez sur la touche **F5** pour lancer le projet. En cas d'erreur de génération, suivez cette procédure de [Résolution des problèmes](#err-mvc).

Notez la superposition des magasins sur les points de la carte. Cliquez sur un de ces magasins, et vous verrez s'afficher une fenêtre contextuelle avec les informations détaillées du magasin. Toutes ces informations proviennent de l'index Azure Search appelé « stores » et créé au cours des étapes précédentes.

<a id="sub-5"></a>
## Exploration de AdventureWorksWebGeo

Le projet **AdventureWorksWebGeo** nous montre comment ASP.NET MVC 4 peut être utilisé pour interagir avec Azure Search et créer une application de mappage qui exploite la recherche géospatiale. Dans cette section, nous allons examiner certaines parties du code de l'application et ce qu'elles font.

1.	Dans l'Explorateur de solutions, développez **AdventureWorksWebGeo** | **Contrôleur** et ouvrez HomeController.cs. La fonction **Index()** est appelée lorsque l'application démarre et la page Index se charge. Dans cette fonction, l'API Bing Maps est chargée à partir de Web.config et envoyée à la vue Index en tant que ViewBag.BingAPI.

2.	Ouvrez Index.cshtml à partir de **Vues** | **Accueil**.

3.	Ce fichier suit le processus d'ajout classique de Bing Maps à une application web, avec toutefois quelques différences :

+	Le ViewBag du contrôleur est utilisé pour charger les informations d'identification de la carte en utilisant : informations d'identification : '@ViewBag.BingAPI' 

+	Dès que la carte est chargée, un JQuery $.post est envoyé à la fonction HomeController **Search** en se référant à : /home/search

+	La fonction **Search** récupère les emplacements des magasins, qui sont ensuite reçus et ajoutés en tant que repères sur la carte Bing.

4.	Ouvrez HomeController.cs sous **Contrôleurs** et observez la fonction **Search**. Notez l'appel de _storeSearch.Search(lat, lon, 10000). Cela génère l'exécution d'une requête afin de trouver tous les magasins dans un rayon de 10 000 km de la latitude (lat) et la longitude (lon) spécifiées. Les résultats de cette requête sont traités, puis renvoyés à la vue Index afin d'être traités en tant que repères superposés sur la carte Bing. 
La démo est terminée. Nous venons de parcourir l'ensemble des principales opérations qu'il vous faut connaître avant de créer une application ASP.NET MVC 4 basée sur une carte à l'aide d'Azure Search.


<a id="err-mvc"></a>
## Résolution de « Impossible de charger le fichier ou l'assembly 'System.Web.Mvc' »

Lors de la création de AdventureWorksWeb, si le message d'erreur « Could not load file or assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies » (« Impossible de charger le fichier ou l'assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ou une de ses dépendances ») apparaît, essayez la procédure de résolution suivante.

1. Ouvrez la console du gestionnaire de package en sélectionnant **Outils** | **Gestionnaire de package NuGet** | **Console du gestionnaire de package**
2. Lorsque l'invite PM> s'affiche, entrez « Update-package -reinstall Microsoft.AspNet.Mvc »
3. Lorsqu'il vous est demandé de recharger le fichier, sélectionnez **Oui pour tout**.
4. Régénérez la solution et essayez à nouveau **F5**.

<a id="next-steps"></a>
## Étapes suivantes

Pour votre apprentissage personnel, essayez d'ajouter d'autres fonctionnalités à l'application de mappage. Par exemple :

+ Une zone de recherche permettant aux utilisateurs de rechercher certains magasins spécifiques.

+ Des facettes permettant aux utilisateurs d'appliquer un filtre par pays ou région.

+ Des zones de sélection dessinées par les utilisateurs et leur permettant de spécifier des régions à rechercher en dessinant une zone sur la carte. La zone est ensuite filtrée par l'API d'intersection géospatiale Azure et représentée graphiquement sur une carte.


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!---HONumber=July15_HO4-->