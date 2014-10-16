<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Que s'est-il passé ?

### Références ajoutées

Le plug-in du client Azure Mobile Services (fourni avec toutes les applications hybrides multi-appareils) a été activé.

### Valeurs de la chaîne de connexion pour Mobile Services

Sous services\\mobileServices\\settings, un nouveau fichier JavaScript (.js) avec un objet MobileServiceClient a été généré. Il contient l'URL et la clé d'application du service mobile sélectionné. Ce fichier permet l'initialisation d'un objet client du service mobile, de la même manière que le code ci-dessous.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### Prise en main de Mobile Services

###### Obtenir une référence pointant vers une table

Le code ci-dessous permet d'obtenir une référence pointant vers une table qui contient des données destinées à un objet TodoItem. Cette référence peut ensuite être utilisée pour lire et mettre à jour la table de données. La table TodoItem est automatiquement créée lorsque vous créez un service mobile.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Pour que ces exemples fonctionnent, les autorisations de la table doivent être définies sur **Toute personne avec la clé d'application**. Vous pourrez ensuite configurer l'authentification. Consultez la page [Prise en main de l'authentification][Prise en main de l'authentification].

###### Ajouter une entrée

Insérez un nouvel élément dans une table de données. Un ID (GUID de type String) est automatiquement créé comme clé primaire de la nouvelle ligne. Appelez la méthode [done]() sur l'objet [Promise]() renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### Lire ou lancer une requête sur une table

Le code ci-dessous permet de lancer une requête sur tous les éléments d'une table, dans l'ordre des champs de texte. Vous pouvez ajouter un code pour traiter les résultats de la requête dans le gestionnaire success. Dans ce cas, un tableau local des éléments est mis à jour.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

Vous pouvez utiliser la méthode where pour modifier la requête. Voici un exemple qui permet de filtrer les éléments terminés :

    todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

Pour consulter plus d'exemples de requêtes, reportez-vous à l'objet [query]().

###### Mettre une entrée à jour

Mettez une ligne à jour dans une table de données. Avec ce code, l'élément est retiré de la liste lorsque le service mobile répond. Appelez la méthode [done]() sur l'objet [Promise]() renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### Supprimer une entrée

Supprimez une ligne d'une table de données à l'aide de la méthode del. Appelez la méthode [done]() sur l'objet [Promise]() renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [Prise en main de l'authentification]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-html-get-started-users/
  [done]: 
  [query]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
