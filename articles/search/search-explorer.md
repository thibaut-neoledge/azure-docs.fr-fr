<properties
	pageTitle="Interroger un index Azure Search à l’aide de l’explorateur de recherche dans le portail Azure | Microsoft Azure | Service de recherche cloud hébergé"
	description="L’Explorateur de recherche est une approche sans code permettant d’interroger un index Azure Search dans le portail Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Interrogation d’un index Azure Search à l’aide de Search Explorer dans le portail Azure
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

**Explorateur de recherche** est un outil de requête intégré du portail Azure pour des requêtes sans code pour un index Azure Search. Il se connecte à n’importe quel index de votre service et fournit une zone de recherche permettant d’entrer des expressions et des chaînes de recherche. Une syntaxe de requête valide est générée en fonction de l’entrée que vous fournissez. Les résultats s’affichent dans la page du portail.

L’Explorateur de recherche est idéal pour apprendre la syntaxe de requête, l’exécution d’une requête ad hoc occasionnelle, ou en raffinant une expression de requête avant de tenter de le traduire en code. Pour l’utiliser, vous devez déjà disposer d’un service Azure Search et un index. Consultez la page [Créer un service Azure Search dans le portail](search-create-service-portal.md) et [Importer des données vers Azure Search à l’aide du portail](search-import-data-portal.md) pour vous aider dans ces tâches.

## Ouvrir l’Explorateur de recherche

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur le tableau de bord des services de votre service Azure Search. Voici quelques façons d'afficher le tableau de bord.
	- Dans la barre de lancement, cliquez sur **Accueil**. La page d'accueil comporte des vignettes pour chaque service de votre abonnement. Cliquez sur la vignette pour ouvrir le tableau de bord des services.
	- Dans la barre de lancement, cliquez sur **Parcourir tout** >**Filtrer par** > **Rechercher des services** pour trouver votre service de recherche dans la liste.

3. Une barre de commande apparaît en haut du tableau de bord des services, avec notamment l’option **Explorateur de recherche**.

  	![][1]

4. Cliquez sur **Explorateur de recherche** pour ouvrir le panneau de l’Explorateur de recherche.
5. Définissez l’index et la version d’API. L’explorateur de recherche se connecte automatiquement au premier index dans votre liste d’index, mais vous pouvez cliquer sur **Modifier l’index** pour basculer sur une autre. **Définir la version de l’API** vous permet de spécifier les versions généralement disponibles ou en version préliminaire. Une syntaxe de requête est uniquement un aperçu.
6. Si vous avez suivi [Prise en main d’Azure Search](search-get-started-portal.md) pour créer et renseigner un index en se basant sur les données United States Geological Survey (USGS) du Rhode Island, vous pouvez utiliser ce terme de recherche pour vérifier les 3 résultats qui reviennent dans l’Explorateur de recherche : `roger williams +school -building`

Notez la syntaxe de requête qui est générée automatiquement en réponse à l’entrée d’un terme de recherche.

![][2]

## Étapes suivantes

Vous trouverez d’autres informations sur la syntaxe et des exemples de requête dans [Rechercher un document](https://msdn.microsoft.com/library/azure/dn798927.aspx) sur MSDN.

Consultez ces liens pour connaître d’autres approches sans code permettant de créer ou de gérer un service de recherche ou un index :

- [Création d'un service Azure Search dans le portail](search-create-service-portal.md)
- [Importer des données dans Azure Search à l’aide du portail](search-import-data-portal.md)
- [Gestion de votre service de recherche dans Azure](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->