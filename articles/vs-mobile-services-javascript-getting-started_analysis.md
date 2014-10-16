<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Que s'est-il passé ?

###### Références ajoutées

La bibliothèque Windows Azure Mobile Services a été ajoutée à votre projet sous la forme d'un fichier MobileServices.js.

###### Valeurs de la chaîne de connexion pour Mobile Services

Dans le dossier services\\mobileServices\\settings, un nouveau fichier JavaScript (.js) avec un objet MobileServiceClient a été généré. Il contient l'URL et la clé d'application du service mobile sélectionné.

### Prise en main de Mobile Services

###### Obtenir une référence pointant vers une table

L'objet client a été ajouté à votre projet. Son nom correspond à celui de votre service mobile, auquel la mention « Client » a été ajoutée. Le code ci-dessous permet d'obtenir une référence pointant vers une table qui contient des données destinées à un objet TodoItem. Cette référence peut ensuite être utilisée pour lire et mettre à jour la table de données.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

###### Ajouter une entrée

Insérez un nouvel élément dans une table de données. Un ID (GUID de type String) est automatiquement créé comme clé primaire de la nouvelle ligne. Ne modifiez pas le type de la colonne ID, car l'infrastructure des services mobiles y fait appel.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

###### Lire ou lancer une requête sur une table

Le code ci-dessous permet de lancer une requête sur tous les éléments d'une table, de mettre à jour une collection locale et de lier le résultat aux attributs listItem des éléments de l'interface utilisateur.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Vous pouvez utiliser la méthode where pour modifier la requête. Voici un exemple qui permet de filtrer les éléments terminés :

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Pour consulter plus d'exemples de requêtes, reportez-vous à l'objet [query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

###### Mettre une entrée à jour

Mettez une ligne à jour dans une table de données. Dans cet exemple, c'est l'élément todoItem qui a été mis à jour. Il s'agit du même élément que celui qui a été renvoyé par le service mobile. Lorsque le service mobile répond, l'élément est mis à jour dans la liste todoItems locale à l'aide de la méthode [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Appelez la méthode [done]() sur l'objet [Promise]() renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

###### Supprimer une entrée

Supprimez une ligne dans une table de données. Appelez la méthode [done]() sur l'objet [Promise]() renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }



