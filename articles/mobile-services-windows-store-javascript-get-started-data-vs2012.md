<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Prise en main des données dans Mobile Services avec Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services pour utiliser les données dans une application Windows Store. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel ajoute la fonctionnalit&eacute; Mobile Services &agrave; une application Windows Store cr&eacute;&eacute;e dans Visual&nbsp;Studio&nbsp;2012. Visual&nbsp;Studio&nbsp;2013 inclut de nouvelles fonctionnalit&eacute;s qui facilitent la connexion de votre application Windows&nbsp;Store &agrave; Mobile Services. Pour plus d'informations, consultez la page <a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-js/">Prise en main des donn&eacute;es dans Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][]
2.  [Création du service mobile][]
3.  [Ajout d'une table de données pour le stockage][]
4.  [Mise à jour de l'application pour utiliser Mobile Services][]
5.  [Test de l'application avec Mobile Services][]

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][], qui est une application Windows Store. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version JavaScript de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur][application GetStartedWithData].

    ![][]

2.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet téléchargé, développez le dossier **js** et examinez le fichier default.js.

    Notez que les objets **TodoItem** ajoutés sont stockés dans un objet **List** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][1]

    Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][]]

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, recherchez `WindowsAzure.MobileServices.WinJS`, cliquez sur **Install** au niveau du package **Azure Mobile Services pour WinJS**, puis acceptez le contrat de licence.

    ![][2]

    La bibliothèque cliente Mobile Services est ajoutée au projet.

3.  Dans le fichier projet default.html, ajoutez la référence de script suivante dans l'en-tête de page :

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

5.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][3]

    Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

6.  Dans Visual Studio, ouvrez le fichier default.js, supprimez les marques de commentaire du code suivant qui définit la variable **client** et fournissez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre :

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile.

7.  Supprimez les marques de commentaire sur la ligne de code suivante :

        var todoTable = client.getTable('TodoItem');

    Ce code crée un objet proxy (**todoTable**) pour la nouvelle table de base de données.

8.  Remplacez la fonction **InsertTodoItem** par le code suivant :

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    Ce code permet d'insérer un nouvel élément dans la table.

9.  Remplacez la fonction **RefreshTodoItem** par le code suivant :

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les éléments terminés renvoyés depuis le service mobile.

10. Remplacez la fonction **UpdateCheckedTodoItem** par le code suivant :

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec votre nouveau service mobile

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Comme auparavant, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][4]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5.  Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

    Notez que la valeur terminée est passée de **false** à **true**.

6.  Dans le fichier projet default.js, remplacez la fonction **RefreshTodoItems** existante par le code suivant qui filtre les éléments terminés :

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  Dans l'application, cochez un autre élément dans la liste, puis cliquez sur le bouton **Actualiser**.

    Notez que l'élément coché n'est plus affiché dans la liste. Chaque actualisation provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Prise en main des données dans Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-with-data-js/
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F
  [application GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  []: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [portail de gestion]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-js
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-js
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js-vs2012
