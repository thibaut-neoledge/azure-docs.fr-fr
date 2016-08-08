<properties
	pageTitle="Prise en main d'un projet de services mobiles Cordova (services connectés Visual Studio) | Microsoft Azure"
	description="Décrit les premières étapes que vous pouvez suivre une fois votre projet Cordova connecté à Azure Mobile Services à l'aide de services connectés de Visual Studio."
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="mlearned"/>

# Prise en main de Mobile Services (Projets Cordova)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

##Premières étapes
La première étape à effectuer pour suivre le code figurant dans ces exemples dépend du type de service mobile auquel vous êtes connecté.

- Dans le cas d'un service mobile principal JavaScript, créez une table nommée TodoItem. Pour créer une table, recherchez le service mobile sous le nœud Azure dans l'Explorateur de serveurs, cliquez avec le bouton droit sur le nœud du service mobile pour ouvrir le menu contextuel, puis choisissez **Créer une table**. Entrez « TodoItem » comme nom de table.

- Si vous disposez d’un service mobile principal .NET, une table TodoItem existe déjà dans le modèle de projet par défaut créé par Visual Studio, mais vous devez la publier sur Azure. Pour cela, ouvrez le menu contextuel du projet de service mobile dans l'Explorateur de solutions, puis choisissez **Publier le site web**. Acceptez les valeurs par défaut, puis choisissez le bouton **Publier**.

##Créer une référence à une table

Le code ci-dessous permet d'obtenir une référence pointant vers une table qui contient des données destinées à un objet TodoItem. Cette référence peut ensuite être utilisée pour lire et mettre à jour la table de données. La table TodoItem est automatiquement créée lorsque vous créez un service mobile.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Pour que ces exemples fonctionnent, les autorisations de la table doivent être définies sur **Toute personne avec la clé d'application**. Vous pourrez ensuite configurer l'authentification. Consultez la page [Prise en main de l'authentification](mobile-services-html-get-started-users.md).

##Ajouter un élément à une table

Insérez un nouvel élément dans une table de données. Un ID (GUID de type String) est automatiquement créé comme clé primaire de la nouvelle ligne. Appelez la méthode **done** sur l'objet [Promise](https://msdn.microsoft.com/library/dn802826.aspx) renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

##Lire une table ou exécuter des requêtes sur une table

Le code ci-dessous permet de lancer une requête sur tous les éléments d'une table, dans l'ordre des champs de texte. Vous pouvez ajouter un code pour traiter les résultats de la requête dans le gestionnaire success. Dans ce cas, un tableau local des éléments est mis à jour.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

Vous pouvez utiliser la méthode where pour modifier la requête. Voici un exemple qui permet de filtrer les éléments terminés :

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

Pour consulter plus d'exemples de requêtes, reportez-vous à l'objet [query](https://msdn.microsoft.com/library/azure/jj613353.aspx).

##Mettre à jour un élément de table

Mettez une ligne à jour dans une table de données. Avec ce code, l'élément est retiré de la liste lorsque le service mobile répond. Appelez la méthode **done** sur l'objet [Promise](https://msdn.microsoft.com/library/dn802826.aspx) renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##Supprimer un élément de table

Supprimez une ligne d'une table de données à l'aide de la méthode **del**. Appelez la méthode **done** sur l'objet [Promise](https://msdn.microsoft.com/library/dn802826.aspx) renvoyé pour obtenir une copie de l'objet inséré et gérer les éventuelles erreurs.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

<!---HONumber=AcomDC_0727_2016-->