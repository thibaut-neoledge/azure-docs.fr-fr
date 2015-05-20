<properties 
	pageTitle="Création de votre première application Azure Search dans .NET" 
	description="Didacticiel sur la création d'une solution à l'aide de la bibliothèque cliente .NET à partir du kit de développement logiciel (SDK) .NET Azure Search." 
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
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#Création de votre première application Azure Search dans .NET#

Ce didacticiel permet de créer une application de recherche Web personnalisée dans Visual Studio 2013 ou version ultérieure qui utilise Azure Search pour son expérience de recherche. Le didacticiel utilise le [kit de développement logiciel \(SDK\) .NET Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx) pour générer des classes pour les objets et les opérations utilisés dans cet exemple.

Vous pouvez télécharger l'exemple à partir de codeplex à la rubrique [Démo Azure Search à l'aide de données USGS](https://azsearchdemos.codeplex.com/SourceControl/latest) pour suivre les étapes de ce didacticiel. Cet exemple d'application utilise des données de l'[USGS \(United States Geological Services\)](http://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Washington. Nous utiliserons ces informations pour créer une application de recherche basée sur des données de bâtiments repères, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Pour exécuter cet exemple, vous devez disposer d'un service Azure Search, auquel vous pouvez vous connecter dans le [portail Azure](https://portal.azure.com).

Vous pouvez commencer par l'étape [Création d'un service dans le portail](../search-create-service-portal/) si vous avez besoin d'aide pour configurer et vérifier la disponibilité du service. L'article explique également comment rechercher le nom du service et les clés administrateur utilisés dans chaque didacticiel et solution incluant d'Azure Search.

> [AZURE.TIP]Nous vous recommandons de mettre à jour les packages NuGet avant de créer des projets afin d'éviter les erreurs de build. Cliquez avec le bouton droit sur la solution et choisissez **Gérer les packages NuGet**. Mettez à jour tous les packages utilisés dans cette solution.

##Création de l'index##

1. Copiez le nom du service et la clé administrateur à partir du [portail Azure](https://portal.azure.com) et collez-les dans **DataIndexer** \| **App.config**.
1. Cliquez avec le bouton droit sur le projet **DataIndexer** pour le définir comme projet de démarrage.
1. Créez et exécutez le projet.

Une fenêtre de console avec ces messages doit s'afficher.

![][1]

Le portail devrait afficher un index des nouvelles fonctionnalités xx et xx. La mise à jour du portail peut prendre plusieurs minutes, alors veillez à actualiser l'affichage après quelques minutes pour voir les résultats.

![][2]

##Création de l'application##


1. Copiez le nom du service et la clé administrateur à partir du [portail Azure](https://portal.azure.com) et collez-les dans **SimpleSearchMVCApp** \| **Web.config**.
1. Cliquez avec le bouton droit sur le projet **SimpleSearchMVCApp** pour le définir comme projet de démarrage.
1. Créez et exécutez le projet.

Une page Web doit s'afficher dans votre navigateur par défaut, contenant une zone de recherche pour accéder aux données USGS stockées dans l'index de votre service Azure Search.

![][3]

##Lancement d'une recherche sur les données USGS##

Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Washington. Si vous cliquez sur **Search** et que le champ de recherche est vide, vous obtiendrez les 50 premières entrées, ce qui correspond au paramétrage par défaut.

Saisissez un terme pour que le moteur de recherche puisse travailler. Essayez d'entrer un nom régional. « Snoqualmie » est une localité de l'État de Washington. C'est également le nom d'une rivière, d'une cascade spectaculaire, d'un col et d'un parc naturel.

![][4]

Vous pouvez également essayer les termes suivants :

- Seattle
- Rainier
- Seattle and Rainier
- Seattle +Rainier -Mount \(permet d'obtenir les résultats incluant Rainier avenue et Rainier club, dans les limites de la ville de Seattle\).

##Exploration du code##

Pour apprendre les bases du SDK .NET, consultez la rubrique [Utilisation d'Azure Search dans .NET](../search-howto-dotnet-sdk/) pour obtenir des détails sur les classes les plus utilisées dans la bibliothèque cliente.

La suite de cette section aborde quelques points sur chaque projet. Le cas échéant, nous vous indiquerons d'autres méthodes qui utilisent des fonctionnalités plus avancées.

**Projet DataIndexer**

Pour simplifier les choses, les données sont incorporées à la solution, dans un fichier texte généré à partir des données du site Web [United States Geological Services \(USGS\)](http://geonames.usgs.gov/domestic/download_data.htm).

Les alternatives à l'incorporation de données incluent les [indexeurs pour DocumentDB](documentdb-search-indexer.md) ou les [indexeurs pour base de données SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). Les indexeurs injectent les données dans votre index Azure Search, simplifiant considérablement l'écriture et la mise à jour du code.

Vous pouvez également charger des données à partir d'une base de données SQL Server sur site. [Ce didacticiel](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/) vous explique comment procéder.

Le programme **DataIndexer** inclut une méthode **SearchDocuments** pour la recherche et le filtrage des données. Cette méthode existe sous la forme d'une étape de vérification, prenant en charge les messages d'état transmis à la fenêtre de console, c'est-à-dire ceux qui affichent les résultats de la recherche et les comportements du filtre. Très probablement, vous n'aurez pas besoin d'une telle méthode dans le code que vous écrivez pour la création et le chargement d'un index.

**Projet SimpleSearchMVCApp**

Le projet MVC utilise une vue et un contrôleur pour acheminer les entrées et les sorties vers la couche de présentation. **Index.cshtml** fournit le code HTML utilisé pour restituer les résultats de la recherche. Actuellement, il s'agit seulement d'un simple tableau qui classe les informations du jeu de données. Ce tableau est utile pour la création d'un prototype et les tests, et vous pouvez facilement en améliorer la présentation. Pour obtenir des conseils sur le traitement des résultats par lots et l'ajout d'un décompte sur une page, consultez [Page de résultats et pagination dans Azure Search](search-pagination-page-layout.md).

Les connexions à Azure Search ainsi que l'exécution d'une requête de recherche sont définies dans le fichier **FeatureSearch.cs**.

Pour finir, si vous n'êtes pas encore convaincu de la valeur et de la simplicité du kit de développement logiciel \(SDK\) .NET, comparez les fichiers source de cet exemple avec ceux de l'API REST : [Azure Search Adventure Works Demo](https://azuresearchadventureworksdemo.codeplex.com/). La version du kit de développement .NET décrite dans ce didacticiel est beaucoup plus simple, avec moins de lignes de code.

##Étapes suivantes##

Il s'agit du premier didacticiel Azure Search basé sur le jeu de données USGS. Au fil du temps, nous compléterons ce didacticiel et en préparerons de nouveaux présentant des fonctionnalités de recherche que vous pourrez utiliser dans vos solutions personnalisées.

Si vous connaissez déjà Azure Search, vous pouvez utiliser cet exemple comme tremplin pour tester des générateurs de suggestions \(requêtes prédictives ou à saisie semi-automatique\), des filtres et la navigation à facettes. Vous pouvez également améliorer la page des résultats de la recherche en ajoutant des décomptes et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](http://azure.microsoft.com/documentation/services/search/). Vous pouvez également cliquer sur les liens dans notre [liste de vidéos et de didacticiels](https://msdn.microsoft.com/library/azure/dn798933.aspx) pour obtenir des informations supplémentaires.

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->