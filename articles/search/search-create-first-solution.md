<properties 
	pageTitle="Création de votre première solution de recherche à l'aide du service Azure Search" 
	description="Création de votre première solution de recherche à l'aide du service Azure Search" 
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
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Création de votre première solution de recherche à l'aide du service Azure Search

+ [Configuration requise](#sub-1)
+ [Création d'un index Azure Search](#sub-2)
+ [Exploration de CatalogIndexer](#sub-3)
+ [Création d'une application MVC4 avec Azure Search](#sub-4)
+ [Exploration d'AdventureWorksWeb](#sub-5)
+ [Étapes suivantes](#next-steps)

<h2>Vue d'ensemble</h2>

<p>Cet exemple présente une application de recherche pour l'entreprise de fabrication de vélos Adventure Works. À la fin de ce didacticiel, vous disposerez d'un catalogue de produits en ligne incluant une interface de recherche enrichie comprenant une navigation par facette, plusieurs options de tri pour les résultats de recherche et des suggestions de requête de type ahead.

   ![][7]

Cette démonstration a pour but la prise en main d'Azure Search via les exercices suivants :

+	Création d'un index Azure Search
+	Chargement de documents (données) dans l'index Azure Search à partir d'une base de données SQL Server
+	Création d'une application ASP.NET MVC4 qui utilise Azure Search aux fins suivantes :
	+	Rechercher et afficher les résultats d'un index Azure Search
	+	Afficher les suggestions de type ahead dans une zone de recherche lors de la saisie d'un terme de recherche
	+	Filtrer les résultats de recherche à l'aide de facettes


<h2 id="sub-1">Configuration requise</h2>

+	Visual Studio 2012 ou une version ultérieure avec ASP.NET MVC 4 et SQL Server doivent être installés. Vous pouvez télécharger les éditions Express gratuites si vous n'avez pas encore installé le logiciel : [Visual Studio 2013 Express](http://www.visualstudio.com/fr-fr/products/visual-studio-express-vs.aspx) et [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Un service Azure Search. Vous aurez besoin du nom du service Azure Search et de la clé d'administration. Voir [Prise en main d'Azure Search](search-get-started.md) pour plus d'informations.
+	[Adventure Works Azure Search Demo project sur CodePlex](http://go.microsoft.com/fwlink/p/?LinkID=510972). Sous l'onglet Source, cliquez sur **Télécharger** pour obtenir un fichier zip de la solution. 

    ![][12]


Cette solution inclut deux projets :

+	**CatalogIndex** crée un index Azure Search et charge les données d'une base de données SQL Server incluse dans le projet.
+	**AdventureWorksWeb** est une application MVC4 qui interroge l'index Azure Search. Ce didacticiel part du principe que vous disposez d'une bonne connaissance d'ASP.NET MVC.

[WACOM.INCLUDE [You need an Azure account to complete this tutorial:](../../includes/free-trial-note.md)]


<h2 id="sub-2">Création d'un index Azure Search</h2>

Dans cette étape, vous allez utiliser **CatalogIndex** pour créer un index Azure Search appelé " catalog " basé sur les données de la base de données SQL Server AdventureWorks.

1.	Dans Visual Studio, ouvrez la solution de démonstration Azure Search nommée **AdventureWorksCatalog.sln**.  
2.	Cliquez avec le bouton droit sur **CatalogIndexer** dans l'Explorateur de solutions et sélectionnez **Définir comme projet de démarrage** afin que cette application s'exécute à la place du projet **AdventureWorksWeb** quand vous appuyez sur **F5**.
3.	Ouvrez **App.config** dans ce projet et remplacez les valeurs de " SearchServiceName " et " SearchServiceApiKey " par celles de votre service Azure Search. Pour le nom du service Azure Search, si votre service est " mysearch.search.windows.net ", vous entrerez " mysearch ".
4.	App.config peut éventuellement inclure une entrée pour SourceSqlConnectionString qui suppose la présence de SQL Server 2014 Express LocalDB (Server=(LocalDB)\v11.0). Si vous utilisez une autre édition de SQL Server, mettez à jour le nom du serveur en conséquence. Par exemple, si vous avez une instance par défaut locale, vous pouvez utiliser (local) ou localhost.
5.	Enregistrez **App.config**.
6.	Appuyez sur **F5** pour lancer le projet.

Une invite de commandes doit s'afficher avec le texte suivant : " Création de l'index... "

Au bout de quelques secondes, le texte suivant doit s'afficher : " Terminé.  Appuyez sur <Entrée> pour continuer : "

   ![][8]

Appuyez sur **Entrée** pour fermer l'application. À ce stade, vous avez correctement créé l'index Azure Search. 

> [WACOM.NOTE] Si vous recevez des erreurs incluant " Valeur non valide pour la clé attachdbfilename " ou une autre erreur liée à l'attachement de la base de données, il s'agit peut-être d'un conflit de contrôle de compte d'utilisateur. Pour les besoins de cette démonstration, contournez ces erreurs en procédant comme suit : 
> Copiez la solution dans un dossier nouveau ou existant (par exemple, Temp) qui fournit l'accès aux utilisateurs authentifiés. 
> Utilisez **Exécuter en tant qu'administrateur** pour démarrer Visual Studio. 
> Ouvrez la solution, générez-la, puis appuyez sur **F5** pour créer l'index.

Pour vérifier la création de l'index et le téléchargement des documents, accédez à votre tableau de bord du service de recherche dans la [version préliminaire du portail Azure](https://portal.azure.com). Dans Utilisation, le nombre d'index doit être augmenté de 1, et vous devez avoir 294 documents, un pour chaque produit de la base de données.

Cliquez sur la vignette **Index** pour afficher la liste d'index. Celle-ci change pour afficher le nouveau nombre d'index et de documents.

   ![][9]


<h2 id="sub-3">Exploration de CatalogIndexer</h2>

Examinons de plus près le projet **CatalogIndexer** pour en comprendre le fonctionnement.

1.	Ouvrez **Program.cs** à partir de l'Explorateur de solutions et accédez à la fonction Main(string[] args).

    Cette fonction crée un URI nommé " _serviceURI " basé sur votre service Azure Search spécifique, puis crée un HttpClient appelé " _httpClient " qui utilise votre clé d'API pour l'authentification auprès de ce service de recherche.

2.	ChangeEnumeratorSql et ChangeSet servent à énumérer les données de la table Products dans la base de données SQL Server AdventureWorks. 

3.	En fonction des données collectées dans cette table, ApplyChanges est ensuite appelé pour appliquer ces données à l'index Azure Search.

4.	Accédez à ApplyChanges dans le même fichier. Cette fonction supprime l'index s'il existe déjà (DeleteCatalogIndex), puis crée un index appelé " catalog " (CreateCatalogIndex).  

5.	Passez à la fonction CreateCatalogIndex. L'index est créé avec un schéma qui correspond aux colonnes de la table Products dans SQL Server. Chaque champ contient un type (Edm.String ou Edm.Double) et des attributs qui définissent l'utilité de ces champs. Consultez la [documentation sur l'API REST Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) pour plus d'informations sur ces attributs.

6.	Revenez à la fonction ApplyChanges. Cette fonction s'exécute en boucle dans l'ensemble des données des modifications énumérées ChangeSet. Plutôt que d'appliquer les modifications une à une, elles sont regroupées par lot de 1 000, puis appliquées au service de recherche. Cette procédure est plus efficace que d'appliquer les documents un à un.

Maintenant que vous savez comment créer et remplir un index à l'aide de données relationnelles dans SQL Server, voyons comment créer un catalogue de produits qui utilise nos données de recherche.


<h2 id="sub-4">Création d'une application MVC4 avec Azure Search</h2>

Au cours de cette étape, vous allez créer et exécuter l'application de recherche dans un navigateur web.

1.	Dans Visual Studio, ouvrez la solution de démonstration Azure Search nommée **AdventureWorksCatalog.sln**.  

2.	Cliquez avec le bouton droit sur **AdventureWorksWeb** dans l'Explorateur de solutions et sélectionnez **Définir comme projet de démarrage**.

3.	Ouvrez **Web.config** dans ce projet et remplacez les valeurs de SearchServiceName et SearchServiceApiKey par celles de votre service Azure Search. Pour le nom du service Azure Search, si votre service est " mysearch.search.windows.net ", vous entrerez " mysearch ".

4.	Enregistrez **Web.config**.

5.	Appuyez sur **F5** pour lancer le projet. Suivez ces étapes de [Résolution des problèmes](#err-mvc) si vous obtenez une erreur de génération. 

    ![][10]

6.	N'indiquez aucune valeur dans la zone de recherche, puis cliquez sur **Rechercher** pour renvoyer l'ensemble des 294 produits.

7.	Notez la liste des facettes à gauche. Cliquez sur l'une d'elles. La recherche est réexécutée, mais cette fois, la facette sélectionnée est utilisée pour filtrer les résultats. Vous pouvez ajouter un point d'arrêt à la première ligne de la fonction Search de **HomeController.cs** pour parcourir (via F11) chaque ligne.

7.	Entrez un terme de recherche, comme " VTT ". Au cours de la frappe, une liste déroulante de requêtes suggérées s'affiche.

    ![][11]

La capture d'écran du début de cette démonstration réapparaît en dessous pour vous rappeler ce qui a été traité jusque-là. Dans les sections précédentes, nous avons présenté la navigation par facettes (à gauche), le nombre de documents (visible en haut de la page), les suggestions, ainsi que les options de tri par pertinence, liste ou prix.

   ![][7]


<h2 id="sub-5">Exploration de AdventureWorksWeb</h2>

Le projet AdventureWorksWeb montre comment utiliser ASP.NET MVC 4 pour interagir avec Azure Search à partir d'une application web. Dans cette section, nous allons examiner certaines parties du code de l'application et ce qu'elles font.

1.	Dans l'Explorateur de solutions, développez **AdventureWorksWeb** | **Controller** et ouvrez **HomeController.cs**

    Voici le contrôleur MVC qui gère l'interaction à partir de la vue Index.  En haut du contrôleur, une référence à CatalogSearch _catalogSearch crée un objet de connexion HttpClient au service Azure Search. Le code de ce CatalogSearch se trouve dans **CatalogSearch.cs**

2. **HomeController.cs** inclut deux fonctions principales :

	**Search** : lorsque l'utilisateur clique sur le bouton de recherche ou choisit une facette, cette fonction est appelée pour récupérer les résultats et les renvoyer pour affichage dans la vue Index.

	**Suggest** : lorsque l'utilisateur tape dans la zone de recherche, la fonction est appelée pour renvoyer une liste de suggestions basée sur le contenu de l'index Azure Search.

Découvrons ces deux fonctions plus en détail.  

3.	Dans la fonction Search de **HomeController.cs**, un appel à _catalogSearch.Search inclut l'objet de connexion à votre service Azure Search. Quand vous appelez la fonction Search située dans **CatalogSearch.cs**, vous constatez qu'elle utilise ces paramètres pour générer une requête Azure Search. Les résultats de la requête sont stockés dans objet JSON appelé result, puis transmis à la vue Index, où ils sont analysés et affichés dans la page web.

4.	Ouvrez **Index.cshtml** sous Vues | Accueil. C'est ici que se trouve le code utilisé pour analyser ces résultats.

5.	Arrêtez l'application si elle est toujours en cours d'exécution et ouvrez le fichier **Index.cshtml** sous Vues | Accueil.  À la fin de ce fichier, une fonction JavaScript utilise " JQuery $(function ()) ". Cette fonction est appelée lors du chargement de la page. Elle utilise la fonction de remplissage automatique qu'elle lie en tant que rappel à partir de la zone de texte de recherche identifiée comme " q ". Chaque fois qu'un utilisateur tape dans la zone de texte, cette fonction de suggestion automatique est appelée et appelle à son tour /home/suggest avec les données saisies.  /home/suggest est une référence à la fonction Suggest de **HomeController.cs**.

6.	Ouvrez **HomeController.cs** et accédez à la fonction Suggest. Ce code est très similaire à la fonction Search qui utilise l'objet _catalogSearch pour appeler la fonction Suggest de **CatalogSearch.cs**. Au lieu d'exécuter une requête de recherche, la fonction Suggest appelle l'[API Suggestions](http://msdn.microsoft.com/library/azure/dn798936.aspx). Elle utilise les termes entrés dans la zone de texte et génère une liste de suggestions possibles. Les valeurs sont renvoyées dans le fichier **Index.cshtml** et automatiquement affichées dans la zone de recherche en tant qu'options de saisie semi-automatique.

Vous vous demandez peut-être comment Azure Search peut savoir les champs sur lesquels baser les suggestions. Pour cela, il faut revenir au moment où vous avez créé l'index. Dans la fonction CreateCatalogIndex du fichier Program.cs du projet **CatalogIndexer**, il existe un attribut appelé Suggestions.  Chaque fois que cet attribut est défini sur True, Azure Search peut l'utiliser comme champ pour la récupération des suggestions

À nous de jouer.  

7.	Une fois encore, générez l'application, puis appuyez sur **F5** pour l'exécuter.

8.	Dans la zone de recherche, tapez " Route ".  Au bout d'une seconde, une liste d'éléments doit s'afficher sous la zone de texte avec des suggestions parmi lesquelles l'utilisateur peut opérer un choix.  

Vous pouvez ajouter un point d'arrêt à la fonction Suggest de **HomeController.cs** et parcourir (via F11) chaque appel effectué pour générer la liste de suggestions.

La démo est terminée. Nous venons de parcourir l'ensemble des principales opérations qu'il vous faut connaître avant de créer une application ASP.NET MVC 4 utilisant Azure Search.


<h2 id="err-mvc">Résolution de " Impossible de charger le fichier ou l'assembly 'System.Web.Mvc' "</h2>

Lors de la création de AdventureWorksWeb, si le message d'erreur " Could not load file or assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies " (" Impossible de charger le fichier ou l'assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ou une de ses dépendances ") apparaît, essayez la procédure de résolution suivante.

1. Ouvrez la console du gestionnaire de package : **Outils** | **Gestionnaire de package NuGet** | **Console du Gestionnaire de package**
2. Lorsque l'invite PM> s'affiche, entrez " Update-package -reinstall Microsoft.AspNet.Mvc "
3. Lorsque vous êtes invité à recharger le fichier, choisissez **Oui pour tout**.
4. Régénérez la solution et appuyez sur **F5** à nouveau.


<h2 id="next-steps">Étapes suivantes</h2>

Pour continuer sur votre lancée, vous pouvez ajouter une page Détails qui s'ouvre lorsque l'utilisateur clique sur l'un des résultats de recherche. En guise de préparation, procédez comme suit :

+	Consultez la documentation sur l'[API Recherche](http://msdn.microsoft.com/library/azure/dn798929.aspx) qui vous permet d'exécuter une requête dans Azure Search pour trouver un document spécifique (par exemple, vous pouvez transmettre l'ID de produit).
+	Essayez d'ajouter une nouvelle fonction Details dans le fichier **HomeController.cs**. Ajoutez la vue **Details.cshtml** correspondante, qui reçoit les résultats de cette recherche et les affiche.
+	Consultez cet autre exemple de code et la vidéo sur la recherche géospatiale : [Channel 9 : Azure Search et données géospatiales](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) et [CodePlex : exemple de recherche géographique Azure Search](http://azuresearchgeospatial.codeplex.com)

Vous pouvez également consulter l'[API REST Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) sur MSDN.


<!--Anchors-->
[Configuration requise]: #sub-1
[Création d'un index Azure Search]: #sub-2
[Exploration de CatalogIndexer]: #sub-3
[Création d'une application MVC4 avec Azure Search]: #sub-4
[Exploration d'AdventureWorksWeb]: #sub-5
[Étapes suivantes]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!--HONumber=35.2-->

<!--HONumber=46--> 
 