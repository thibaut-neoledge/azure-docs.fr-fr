<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Que s'est-il passé ?

###### Références ajoutées

Le package NuGet Azure Mobile Services a été ajouté à votre projet. Suite à cette opération, les références .NET suivantes ont été ajoutées à votre projet : Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### Valeurs de la chaîne de connexion pour Mobile Services

Dans votre fichier App.xaml.cs, un objet MobileServiceClient a été créé avec l'URL et la clé d'application du service mobile sélectionné.

### Prise en main de Mobile Services

###### Obtenir une référence pointant vers une table

Le code ci-dessous permet d'obtenir une référence pointant vers une table qui contient des données destinées à un objet TodoItem. Cette référence peut ensuite être utilisée pour lire et mettre à jour la table de données. Les attributs de la classe TodoItem doivent être configurés pour interpréter le JSON que le service mobile envoie en réponse à vos requêtes.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Ce code fonctionne si les autorisations de votre table sont définies sur **Toute personne avec la clé d'application**. Si vous modifiez les autorisations pour sécuriser votre service mobile, vous devez ajouter la prise en charge de l'authentification des utilisateurs. Consultez la page [Prise en main de l'authentification][Prise en main de l'authentification].

###### Ajouter une entrée

Insérez un nouvel élément dans une table de données.

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### Lire ou lancer une requête sur une table

Le code ci-dessous permet de lancer une requête sur tous les éléments d'une table. Notez qu'il ne renvoie que la première page de données, qui contient par défaut 50 éléments. Vous pouvez transmettre n'importe quelle taille de page, puisqu'il s'agit d'un paramètre facultatif.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### Mettre une entrée à jour

Mettez une ligne à jour dans une table de données. L'élément de paramètre correspond à l'objet TodoItem à mettre à jour.

    await todoTable.UpdateAsync(item);

###### Supprimer une entrée

Supprimez une ligne de la base de données. L'élément de paramètre correspond à l'objet TodoItem à supprimer.

    await todoTable.DeleteAsync(item);

  [Prise en main de l'authentification]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
