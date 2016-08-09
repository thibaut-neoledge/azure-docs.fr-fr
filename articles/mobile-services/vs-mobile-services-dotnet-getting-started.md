<properties
	pageTitle="Prise en main d’un projet de services mobiles Visual Studio .NET (services connectés) | Microsoft Azure"
	description="Prise en main d’Azure Mobile Services dans un projet .NET Visual Studio"
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="mlearned"/>

# Prise en main de Mobile Services (Projets .NET)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

La première étape à effectuer pour suivre le code figurant dans ces exemples dépend du type de service mobile auquel vous êtes connecté.

- Dans le cas d'un service mobile principal JavaScript, créez une table nommée TodoItem. Pour créer une table, recherchez le service mobile sous le nœud Azure dans l'Explorateur de serveurs, cliquez avec le bouton droit sur le nœud du service mobile pour ouvrir le menu contextuel, puis choisissez **Créer une table**. Entrez « TodoItem » comme nom de table.

- Si vous disposez d’un service mobile principal .NET, une table TodoItem existe déjà dans le modèle de projet par défaut créé par Visual Studio, mais vous devez la publier sur Azure. Pour cela, ouvrez le menu contextuel du projet de service mobile dans l'Explorateur de solutions, puis choisissez **Publier le site web**. Acceptez les valeurs par défaut, puis choisissez le bouton **Publier**.

##Obtenir une référence à une table

Le code ci-dessous permet de créer une référence à une table (`todoTable`) qui contient des données destinées à un objet TodoItem. Cette référence peut ensuite être utilisée pour lire et mettre à jour la table de données. Les attributs de la classe TodoItem doivent être configurés pour interpréter le JSON que le service mobile envoie en réponse à vos requêtes.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Ce code fonctionne si les autorisations de votre table sont définies sur **Toute personne avec la clé d'application**. Si vous modifiez les autorisations pour sécuriser votre service mobile, vous devez ajouter la prise en charge de l'authentification des utilisateurs. Consultez la page [Prise en main de l'authentification](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

##Ajouter un élément de table

Insérez un nouvel élément dans une table de données.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

##Lire une table ou exécuter des requêtes sur une table

Le code ci-dessous permet de lancer une requête sur tous les éléments d'une table. Notez qu'il ne renvoie que la première page de données, qui contient par défaut 50 éléments. Vous pouvez transmettre n'importe quelle taille de page, puisqu'il s'agit d'un paramètre facultatif.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.
        items = await todoTable.ToListAsync();
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


##Mettre à jour un élément de table

Mettez une ligne à jour dans une table de données. L'élément de paramètre correspond à l'objet TodoItem à mettre à jour.

	await todoTable.UpdateAsync(item);

##Supprimer un élément de table

Supprimez une ligne de la base de données. L'élément de paramètre correspond à l'objet TodoItem à supprimer.

	await todoTable.DeleteAsync(item);


[En savoir plus sur Mobile Services](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->