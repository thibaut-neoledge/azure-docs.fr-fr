<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Prise en main des données dans Mobile Services avec Visual Studio 2012
======================================================================

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services pour utiliser les données dans une application Windows Store. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

<div class="dev-callout"><b>Remarque</b>

<p>Ce didacticiel ajoute la fonctionnalité Mobile Services à une application Windows Store créée dans Visual Studio 2012. Visual Studio 2013 inclut de nouvelles fonctionnalités qui facilitent la connexion de votre application Windows Store à Mobile Services. Pour plus d'informations, consultez la page <a href="/en-us/develop/mobile/tutorials/get-started-with-data-js/">Prise en main des données dans Mobile Services</a>.</p>
</div>
Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store](#download-app)
2.  [Création du service mobile](#create-service)
3.  [Ajout d'une table de données pour le stockage](#add-table)
4.  [Mise à jour de l'application pour utiliser Mobile Services](#update-app)
5.  [Test de l'application avec Mobile Services](#test-app)

<div class="dev-callout"><b>Remarque</b>

<p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.
</p>
</div>

Téléchargement du projetTéléchargement du projet GetStartedWithData
-------------------------------------------------------------------

Ce didacticiel est basé sur l'[application GetStartedWithData](http://go.microsoft.com/fwlink/?LinkId=262308), qui est une application Windows Store. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version JavaScript de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur](http://go.microsoft.com/fwlink/?LinkId=262308).

    ![][10]

2.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet téléchargé, développez le dossier **js** et examinez le fichier default.js.

    Notez que les objets **TodoItem** ajoutés sont stockés dans un objet **List** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][0]  

    Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

Création d'un service mobileCréation d'un service mobile dans le portail de gestion
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Ajout d'une nouvelle tableAjout d'une nouvelle table au service mobile
----------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Mise à jour de l'applicationMise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données
-------------------------------------------------------------------------------------------------------------------

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, recherchez `WindowsAzure.MobileServices.WinJS`, cliquez sur **Install** au niveau du package **Azure Mobile Services pour WinJS**, puis acceptez le contrat de licence.

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png) 

	La bibliothèque cliente Mobile Services est ajoutée au projet.

3.  Dans le fichier projet default.html, ajoutez la référence de script suivante dans l'en-tête de page :

         <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

5.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][8]

	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

6.  Dans Visual Studio, ouvrez le fichier default.js, supprimez les marques de commentaire du code suivant qui définit la variable **client** et fournissez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre :

             var client = new WindowsAzure.MobileServiceClient(
                 "AppUrl",
                 "appKey"
             );

	Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile.

7.  Supprimez les marques de commentaire sur la ligne de code suivante :

         var todoTable = client.getTable('TodoItem');

    Ce code crée un objet proxy (**todoItemTable**) pour la nouvelle table de base de données. 

8.  Remplacez la fonction **InsertTodoItem** par le code suivant :

         var insertTodoItem = function (todoItem) {
             // Insérez une nouvelle ligne dans la base de données. Une fois que l'opération est terminée
             // et que Mobile Services a attribué un ID, l'élément est ajouté à la liste des liaisons.
             todoTable.insert(todoItem).done(function (item) {
                 todoItems.push(item);
             });
         };

	Ce code permet d'insérer un nouvel élément dans la table.

9.  Remplacez la fonction **RefreshTodoItem** par le code suivant :

         var refreshTodoItems = function () {
             // Ce code actualise les entrées dans la liste en interrogeant la table TodoItems. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };      

    Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les éléments terminés renvoyés depuis le service mobile. 

10.  Remplacez la fonction **UpdateCheckedTodoItem** par le code suivant :

         var updateCheckedTodoItem = function (todoItem) {
             // Ce code prend un TodoItem qui vient d'être terminé et met à jour la base de données. 
             todoTable.update(todoItem);
         };

    Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

Test de l'applicationTest de l'application avec votre nouveau service mobile
----------------------------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Comme auparavant, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][9]
          
    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5.  Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

	Notez que la valeur terminée est passée de **false** à **true**.

6.  Dans le fichier projet default.js, remplacez la fonction **RefreshTodoItems** existante par le code suivant qui filtre les éléments terminés :

         var refreshTodoItems = function () {                     
             // Requête plus avancée qui filtre les éléments terminés. 
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

Étapes suivantes
----------------

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)
    <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-js)
    <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png






[7]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
[8]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012
[C# and XAML]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/?LinkId=262308
