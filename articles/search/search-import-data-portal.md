<properties
	pageTitle="Importer des données dans Azure Search à l’aide du portail | Microsoft Azure | Service de recherche cloud hébergé"
	description="Comment charger des données sur un index dans Azure Search à l’aide du portail"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importer des données dans Azure Search à l’aide du portail
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Le portail Azure comprend, sur le tableau de bord Azure Search, une commande **Importer des données** qui vous guide tout au long du processus d’ingestion des données dans Azure Search. La commande s’appuie sur une fonctionnalité d’indexeurs intégrée qui analyse une source de données et utilise l’ensemble de lignes contenues dans cette source de données pour créer et charger des documents.

L’Assistant d’importation des données propose une construction en trois phases :

- une connexion à la source de données
- un index cible dans lequel les données sont chargées (l’Assistant peut généralement le générer automatiquement)
- une planification qui s’exécute immédiatement ou à intervalles réguliers

Pour utiliser un indexeur ou la commande **Importer des données**, votre source de données principale doit compter parmi les sources prises en charge : base de données SQL Azure, bases de données relationnelles SQL Server sur une machine virtuelle Azure ou Azure DocumentDB.

Vous pouvez uniquement importer à partir d’une seule table, d’une vue ou d’une structure de données équivalente. Vous devrez peut-être commencer par créer cette structure de données dans votre source de données d’application pour obtenir les métadonnées et les entrées de données pertinentes dans votre index de recherche.

Vous pouvez tester ce workflow à l’aide d’exemples de données. Pour commencer, consultez la page [Prise en main d’Azure Search dans le portail Azure](search-get-started-portal.md).

##Configurer l’importation des données

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur le tableau de bord des services de votre service Azure Search. Voici quelques façons d'afficher le tableau de bord.
	- Dans la barre de lancement, cliquez sur **Accueil**. La page d'accueil comporte des vignettes pour chaque service de votre abonnement. Cliquez sur la vignette pour ouvrir le tableau de bord des services.
	- Dans la barre de lancement, cliquez sur **Parcourir tout** >**Filtrer par** > **Rechercher des services** pour trouver votre service de recherche dans la liste.

3. Une barre de commande apparaît en haut du tableau de bord des services, avec notamment l’option **Importer des données**. Cliquez sur **Importer des données** pour ouvrir le panneau d’importation des données.

4. Cliquez sur **Se connecter à vos données** pour spécifier une définition de source de données utilisée par un indexeur. Les options incluent :
	- 	La source de données existante fait référence à une définition de source de données précédemment créée pour un indexeur. Si des indexeurs sont déjà définis dans votre service de recherche, vous pouvez réaffecter une définition de source de données à une importation.
	- 	SQL Azure permet de spécifier une connexion de source de données vers une base de données SQL Azure ou une base de données SQL Server sur une machine virtuelle Azure. 
	- 	DocumentDB est utilisé pour spécifier une connexion de source de données pour ce type de source de données. 

   Pour SQL Azure et DocumentDB, la base de données doit être liée à votre abonnement. Si vous la connaissez, vous pouvez coller une chaîne de connexion ; sinon, vous pouvez choisir une source de données créée précédemment par une personne disposant d’autorisations d’écriture pour votre abonnement.

5. Cliquez sur **Personnaliser l’index cible** pour terminer l’index par défaut.
	- Le champ **Clé** est obligatoire. Le champ que vous sélectionnez pour la clé doit être un champ de chaîne contenant des valeurs uniques.
	- Le nom et le type de champ sont généralement renseignés automatiquement. Vous pouvez cependant modifier le type de données.
	- Sélectionnez les attributs de chaque champ :
		- « Récupérable » renvoie le champ dans les résultats de la recherche.
		- « Filtrable » permet de référencer le champ dans les expressions de filtre.
		- « Triable » permet d’utiliser le champ dans un tri.
		- « À choix multiples » active le champ pour la navigation à choix multiples.
		- « Possibilité de recherche » permet une recherche en texte intégral.
	- Cliquez sur l’onglet **Analyseur** si vous souhaitez spécifier un analyseur de langue au niveau du champ. Voir [Création d’une définition d’index de document dans plusieurs langues dans Azure Search](search-language-support.md) pour plus d’informations.
	- Cliquez sur le **Générateur de suggestions** si vous souhaitez activer les suggestions de requête en saisie automatique ou semi-automatique.

6. Cliquez **Importer vos données** pour exécuter l’opération d’importation à l’aide de l’option Exécuter maintenant, ou pour définir une planification périodique.

L’opération d’importation des données que vous venez de terminer crée un indexeur en arrière-plan. Vous pouvez maintenant modifier directement l’indexeur pour modifier l’un de ses composants.
	
##Modification d’un indexeur existant

Dans le tableau de bord des services, double-cliquez sur la vignette Indexeur pour extraire une liste de tous les indexeurs créés pour votre abonnement. Double-cliquez sur l’un des indexeurs pour l’exécuter, le modifier ou le supprimer.

<!---HONumber=AcomDC_0224_2016-->