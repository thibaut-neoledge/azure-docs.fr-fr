<properties urlDisplayName="Get Started with Data" pageTitle="Prise en main des données (Windows Store) | Centre de développement mobile" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />

# Ajout de Mobile Services à une application existante

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

Cette rubrique explique comment utiliser Azure Mobile Services en tant que source de données principale pour une application du Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données pendant l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel est un service mobile principal .NET. Vous pouvez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile, et vous pouvez exécuter et déboguer votre service mobile sur votre ordinateur local. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version principale JavaScript de cette rubrique.

>[WACOM.NOTE]Cette rubrique montre comment ajouter Azure Mobile Services à un projet Windows Store. Vous pouvez utiliser les outils de Visual Studio 2013 pour ajouter le même service mobile principal .NET à un projet d'application Windows universelle. Pour plus d'informations, consultez la [version application Windows universelle](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data) de ce didacticiel.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement d'un projet d'application Windows Store]
2. [Création d'un service mobile]
3. [Téléchargement du service mobile en local]
4. [Mise à jour de l'application Windows Store pour utiliser le service mobile]
5. [Test de l'application Windows Store sur le service hébergé localement]
6. [Publication du service mobile sur Azure]
7. [Test de l'application Windows Store sur le service hébergé sous Azure]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Une version d'évaluation gratuite est disponible.

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithMobileServices][Site d'exemples de code développeur], qui est un projet d'application du Windows Store dans Visual Studio 2013. L'interface utilisateur de cette application est similaire à l'application générée par le démarrage rapide de Mobile Services, excepté que les éléments ajoutés sont stockés en local dans la mémoire. 

1. Téléchargez la version JavaScript de l'exemple d'application GetStartedWithMobileServices sur le [site d'exemples de code développeur]. 

2. Exécutez Visual Studio 2013 avec des privilèges d'administration : cliquez avec le bouton droit sur Visual Studio, puis cliquez sur **Exécuter en tant qu'administrateur**.

3. Dans Visual Studio 2013, ouvrez le projet téléchargé. Développez le dossier js dans l'Explorateur de solutions et examinez le fichier default.js.

   	Notez que les objets **TodoItem** ajoutés sont stockés dans un `WinJS.Binding.List`.

4. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

5. Dans l'application, entrez du texte dans la zone sous **Insérer un TodoItem**, puis cliquez sur **Enregistrer**.

   	![][0]  

   	Notez que le texte enregistré est affiché dans la deuxième colonne sous **Interroger et mettre à jour des données**.

##<a name="create-service"></a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Téléchargement du projet de service mobile et ajout de celui-ci à la solution

1. Dans le [portail de gestion Azure], cliquez sur votre nouveau service mobile ou sur son onglet d'icône cloud pour accéder à la page de présentation.

    ![][2]

2. Cliquez sur la plateforme **Windows Store**. Sous la section **Prise en main**, développez **Connecter une application Windows Store existante**, puis cliquez sur le bouton **Télécharger** pour télécharger un projet de démarrage personnalisé pour le service mobile. 

    ![][3]

3. En bas de cette section **Prise en main**, accédez à l'étape intitulée **Publier votre service dans le cloud**. Cliquez sur le lien présenté dans la capture d'écran ci-dessous pour télécharger un fichier de profil de publication pour le service mobile que vous venez de télécharger.

    > [WACOM.NOTE] Enregistrez le fichier en lieu sûr, car il contient des informations sensibles sur votre compte Azure. Vous supprimerez ce fichier après la publication du service mobile, comme expliqué plus loin dans ce didacticiel. 

    ![][5]


4. Décompressez le projet de démarrage de service personnalisé que vous avez téléchargé. Copiez les dossiers que contenait le fichier .zip dans le même répertoire **JavaScript** que le fichier solution Prise en main des données (.sln). Cela permet au gestionnaire de package NuGet d'assurer plus facilement la synchronisation de tous les packages. 

    ![][26]

5. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur la solution de votre application du Windows Store Prise en main des données. Cliquez sur **Ajouter**, puis sur **Projet existant**.

    ![][4]

6. Dans la boîte de dialogue Ajouter un projet existant, accédez au dossier du projet de service mobile que vous avez déplacé vers le répertoire **JavaScript**. Sélectionnez le fichier du projet C# (.csproj) dans le sous-répertoire du service. Cliquez sur **Ouvrir** pour ajouter le projet à votre solution.

    ![][6]

7. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet de service que vous venez d'ajouter, puis cliquez sur **Générer** pour vérifier qu'il est généré sans erreur. Pendant la génération, le gestionnaire de package NuGet doit peut-être restaurer des packages NuGet référencés dans le projet.

    ![][20]

8. Cliquez avec le bouton droit à nouveau sur le projet de service. Cette fois, cliquez sur **Démarrer une nouvelle instance** sous le menu contextuel **Déboguer**.

    ![][21]

    Visual Studio ouvre la page web par défaut de votre service. Vous pouvez cliquer sur **Essayer** pour tester les méthodes dans votre service mobile à partir de la page web par défaut.

    ![][22]

    Par défaut, Visual Studio a hébergé votre service mobile localement dans IIS Express. Pour vous en assurer, cliquez avec le bouton droit sur l'icône de barre d'état système d'IIS Express sur votre barre des tâches.

    ![][23]


#<a name="update-app"></a>Mise à jour de l'application Windows Store pour utiliser le service mobile

Dans cette section, vous allez mettre à jour l'application Windows Store pour utiliser le service mobile en tant que service principal de l'application.


1. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d'application du Windows Store, puis cliquez sur **Gérer les packages NuGet**.

    ![][7]

2. Dans la boîte de dialogue Gérer les packages NuGet, recherchez **WindowsAzure.MobileServices.WinJS** dans la collection de packages en ligne et cliquez pour installer le package NuGet Azure Mobile Services. Fermez ensuite la boîte de dialogue.

    ![][8]

3. À partir du portail de gestion Azure, accédez à la page d'aperçu de votre service mobile, puis recherchez l'étape intitulée **Connecter votre application et stocker les données dans votre service**. Cliquez sur **JavaScript** pour sélectionner ce langage et copiez l'extrait de code pour créer le client `MobileServiceClient`.

    ![][9]

4. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **js** et ouvrez le fichier default.js. Collez l'extrait de code que vous avez copié dans le gestionnaire d'événements `app.onactivated` juste avant la définition de la variable `todoItems`. Notez que l'extrait comprend un constructeur placé en commentaire pour la connexion au service mobile sous Azure via la clé de l'application. Vous annulerez ce commentaire dans une étape ultérieure.

    ![][10]


5. Dans le fichier default.js, remplacez le reste du code du gestionnaire d'événements `app.onactiviated` par le code suivant qui définit les éléments `todoItems` et les opérations que nous allons tester avec le service mobile. Puis enregistrez le fichier.

    Ce code utilise le Kit de développement logiciel (SDK) Mobile Services pour que JavaScript permette à l'application de stocker ses données dans une table fournie par le service au lieu de les stocker localement en mémoire. Les trois principales méthodes sont les suivantes : `insertTodoItem`, `refreshTodoItems` et `updateCheckedTodoItem`. Elles vous permettent d'insérer, d'interroger et de mettre à jour de manière asynchrone votre collection de données avec une table dans Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6. Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier default.html. En haut du fichier, ajoutez une nouvelle référence au script WinJS pour MobileServices.js, puis enregistrez le fichier.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]

##<a name="test-locally-hosted"></a>Test de l'application du Windows Store sur le service hébergé localement

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Publication du service mobile sur Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Test du service mobile publié sur Azure

1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **js** et ouvrez le fichier default.js.  Placez en commentaire le code permettant de créer le client `MobileServiceClient` qui se connecte au service mobile hébergé localement. Annulez le commentaire du code permettant de créer le client `MobileServiceClient` qui se connecte à votre service sous Azure. Enregistrez vos modifications dans le fichier.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. Dans Visual Studio, appuyez sur la touche F5 ou cliquez sur **Démarrer le débogage** dans le menu Déboguer. L'application du Windows Store est alors régénérée conformément à la précédente modification avant d'être exécutée pour se connecter au service mobile hébergé à distance sous Azure. 

    ![][12]


3. Entrez quelques nouveaux éléments todoitem et cliquez sur **Enregistrer** pour chacun d'eux. Activez la case à cocher pour valider certains de ces nouveaux éléments. Chaque nouvel élément todoItem est stocké et mis à jour dans la base de données SQL que vous avez configurée précédemment pour votre service mobile dans le portail de gestion Azure. 

    ![][16]

    Vous pouvez redémarrer l'application pour vous assurer que les modifications ont été conservées dans la base de données sous Azure. Vous pouvez également examiner la base de données à l'aide du portail de gestion Azure ou de l'Explorateur d'objets SQL Server de Visual Studio. Au cours des deux prochaines étapes, vous allez utiliser le portail de gestion Azure pour consulter les modifications dans votre base de données.

4. Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![][17]

5. Dans le portail de gestion, exécutez une requête pour afficher les modifications effectuées par l'application du Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application du Windows Store pour utiliser des données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données avec des scripts]
  <br/>Découvrez plus en détail comment utiliser des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Découvrez comment utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une demande unique.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Obtenez plus d'informations sur l'utilisation de Mobile Services avec HTML et JavaScript.
  
<!-- Anchors. -->

[Téléchargement d'un projet d'application Windows Store]: #download-app
[Création d'un service mobile]: #create-service
[Téléchargement du service mobile en local]: #download-the-service-locally
[Mise à jour de l'application du Windows Store pour utiliser le service mobile]: #update-app
[Test de l'application Windows Store sur le service hébergé localement]: #test-locally-hosted
[Publication du service mobile sur Azure]: #publish-mobile-service
[Test de l'application Windows Store sur le service hébergé sous Azure]: #test-azure-hosted
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Validation et modification de données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-js
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) de Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site d'exemples de code développeur]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library/
[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  
