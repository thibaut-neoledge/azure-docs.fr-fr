<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend" class="current">Serveur principal .NET</a> | 
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend">JavaScript backend</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services en tant que source de données principale pour une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel est un service mobile principal .NET. Le service principal .NET vous permettra d'utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile. Il vous permettra également d'exécuter et de déboguer votre service mobile sur votre ordinateur local. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version principale JavaScript de cette rubrique.

> [WACOM.NOTE]Cette rubrique vous montre comment ajouter Azure Mobile Services à un projet Windows Store. Vous pouvez utiliser les outils de Visual Studio 2013 pour ajouter le même service mobile principal .NET à un projet d'application Windows universelle. Pour plus d'informations, consultez la page [version de l'application universelle Windows][version de l'application universelle Windows] de ce didacticiel.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][Téléchargement d'un projet d'application Windows Store]
2.  [Création d'un service mobile][Création d'un service mobile]
3.  [Téléchargement du service mobile en local][Téléchargement du service mobile en local]
4.  [Mise à jour de l'application Windows Store pour utiliser le service mobile][Mise à jour de l'application Windows Store pour utiliser le service mobile]
5.  [Test de l'application Windows Store sur le service hébergé localement][Test de l'application Windows Store sur le service hébergé localement]
6.  [Publication du service mobile sur Azure][Publication du service mobile sur Azure]
7.  [Test de l'application Windows Store sur le service hébergé sous Azure][Test de l'application Windows Store sur le service hébergé sous Azure]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Une version d'évaluation gratuite est disponible.

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithMobileServices][application GetStartedWithMobileServices], qui est un projet d'application Windows Store dans Visual Studio 2013. L'interface utilisateur de cette application est similaire à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version JavaScript de l'exemple d'application GetStartedWithMobileServices sur le [site d'exemples de code développeur][application GetStartedWithMobileServices].

2.  Exécutez Visual Studio 2013 avec des privilèges d'administration : cliquez avec le bouton droit sur Visual Studio, puis cliquez sur **Exécuter en tant qu'administrateur**.

3.  Dans Visual Studio 2013, ouvrez le projet téléchargé. Développez le dossier js dans l'Explorateur de solutions et examinez le fichier default.js.

    Notez que les objets **TodoItem** ajoutés sont stockés dans un `WinJS.Binding.List` en mémoire.

4.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

5.  Dans l'application, tapez un texte dans la zone située sous **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][0]

    Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

## <a name="create-service"></a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Téléchargement du projet de service mobile et ajout de celui-ci à la solution

1.  Dans le [portail de gestion Azure][portail de gestion Azure], cliquez sur votre nouveau service mobile ou sur son onglet d'icône cloud pour accéder à la page de présentation.

    ![][1]

2.  Cliquez sur la plateforme **Windows Store**. Sous la section **Prise en main**, développez **Connecter une application Windows Store existante**, puis cliquez sur le bouton **Télécharger** afin de télécharger un projet de démarrage personnalisé pour le service mobile.

    ![][2]

3.  En bas de cette section **Prise en main**, accédez à l'étape intitulée **Publier votre service dans le cloud**. Cliquez sur le lien présenté dans la capture d'écran ci-dessous afin de télécharger un fichier de profil de publication pour le service mobile que vous venez de télécharger.

    > [WACOM.NOTE] Enregistrez le fichier en lieu sûr, car il contient des informations sensibles sur votre compte Azure. Vous supprimerez ce fichier après la publication du service mobile, comme expliqué plus loin dans ce didacticiel.

    ![][3]

4.  Décompressez le projet de démarrage de service personnalisé que vous avez téléchargé. Copiez les dossiers que contenait le fichier .zip dans le même répertoire **JavaScript** que le fichier solution Prise en main des données (.sln). Cela permet au gestionnaire de package NuGet d'assurer plus facilement la synchronisation de tous les packages.

    ![][4]

5.  Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur la solution de votre application Windows Store Prise en main des données. Cliquez sur **Ajouter**, puis sur **Projet existant**.

    ![][5]

6.  Dans la boîte de dialogue Ajouter un projet existant, accédez au dossier du projet de service mobile que vous avez déplacé vers le répertoire **JavaScript**. Sélectionnez le fichier du projet C# (.csproj) dans le sous-répertoire du service. Cliquez sur **Ouvrir** pour ajouter le projet à votre solution.

    ![][6]

7.  Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet de service que vous venez d'ajouter, puis cliquez sur **Générer** pour vérifier qu'il est généré sans erreur. Pendant la génération, il se peut que le gestionnaire de package NuGet ait besoin de restaurer des packages NuGet référencés dans le projet.

    ![][7]

8.  Cliquez de nouveau sur le projet de service avec le bouton droit. Cette fois, cliquez sur **Démarrer une nouvelle instance** sous le menu contextuel **Déboguer**.

    ![][8]

    Visual Studio ouvre la page Web par défaut de votre service. Vous pouvez cliquer sur **try it now** pour tester les méthodes dans votre service mobile à partir de la page Web par défaut.

    ![][9]

    Par défaut, Visual Studio a hébergé votre service mobile localement dans IIS Express. Pour vous en assurer, cliquez avec le bouton droit sur l'icône de barre d'état système d'IIS Express sur votre barre des tâches.

    ![][10]

# <a name="update-app"></a>Mise à jour de l'application Windows Store pour utiliser le service mobile

Dans cette section, vous allez mettre à jour l'application Windows Store pour utiliser le service mobile en tant que service principal de l'application.

1.  Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d'application Windows Store, puis cliquez sur **Gérer les packages NuGet**.

    ![][11]

2.  À partir de la boîte de dialogue Gérer les packages NuGet, recherchez **WindowsAzure.MobileServices.WinJS** dans la collection de packages en ligne et cliquez pour installer le package NuGet Azure Mobile Services. Fermez ensuite la boîte de dialogue.

    ![][12]

3.  À partir du portail de gestion Azure, accédez à la page d'aperçu de votre service mobile, puis recherchez l'étape intitulée **Connecter votre application et stocker les données dans votre service**. Cliquez sur **JavaScript** pour sélectionner ce langage et copiez l'extrait de code pour créer le client `MobileServiceClient`.

    ![][13]

4.  Dans l'explorateur de solutions de Visual Studio, développez le dossier **js** et ouvrez le fichier default.js. Collez l'extrait de code que vous avez copié dans le gestionnaire d'événements `app.onactivated` juste avant la définition de la variable `todoItems`. Notez que l'extrait comprend un constructeur placé en commentaire pour la connexion au service mobile sous Azure via la clé de l'application. Vous annulerez ce commentaire lors d'une étape ultérieure.

    ![][14]

5.  Dans le fichier default.js, remplacez le reste du code du gestionnaire d'événements `app.onactiviated` par le code suivant qui définit les éléments `todoItems` et les opérations que nous allons tester avec le service mobile. Puis enregistrez le fichier.

    Ce code utilise le Kit de développement logiciel (SDK) Mobile Services pour que JavaScript permette à l'application de stocker ses données dans une table fournie par le service au lieu de les stocker localement en mémoire. Les trois principales méthodes sont les suivantes : `insertTodoItem`, `refreshTodoItems` et `updateCheckedTodoItem`. Elles vous permettent d'insérer, d'interroger et de mettre à jour de manière asynchrone votre collection de données avec une table dans Azure.

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

6.  Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier default.html. En haut du fichier, ajoutez une nouvelle référence au script WinJS pour MobileServices.js, puis enregistrez le fichier.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][15]

## <a name="test-locally-hosted"></a>Test de l'application Windows Store sur le service hébergé localement

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

## <a name="publish-mobile-service"></a>Publication du service mobile sur Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Test du service mobile publié sur Azure

1.  Dans l'explorateur de solutions de Visual Studio, développez le dossier **js** et ouvrez le fichier default.js. Placez en commentaire le code permettant de créer le client `MobileServiceClient` qui se connecte au service mobile hébergé localement. Annulez le commentaire du code permettant de créer le client `MobileServiceClient` qui se connecte à votre service sous Azure. Enregistrez vos modifications dans le fichier.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

2.  Dans Visual Studio, appuyez sur la touche F5 ou cliquez sur **Démarrer le débogage** dans le menu Déboguer. L'application Windows Store est alors régénérée conformément à la précédente modification avant d'être exécutée pour se connecter au service mobile hébergé à distance sous Azure.

    ![][16]

3.  Entrez quelques nouveaux éléments todoitem et cliquez sur **Enregistrer** pour chacun d'eux. Activez la case à cocher pour valider certains de ces nouveaux éléments. Chaque nouvel élément todoItem est stocké et mis à jour dans la base de données SQL que vous avez configurée précédemment pour votre service mobile dans le portail de gestion Azure.

    ![][17]

    Vous pouvez redémarrer l'application pour vous assurer que les modifications ont été conservées dans la base de données sous Azure. Vous pouvez également examiner la base de données à l'aide du portail de gestion Azure ou de l'Explorateur d'objets SQL Server de Visual Studio. Au cours des deux prochaines étapes, vous allez utiliser le portail de gestion Azure pour consulter les modifications dans votre base de données.

4.  Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![][18]

5.  Dans le portail de gestion, exécutez une requête pour afficher les modifications effectuées par l'application Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][19]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]

    En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript.

  [version de l'application universelle Windows]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création d'un service mobile]: #create-service
  [Téléchargement du service mobile en local]: #download-the-service-locally
  [Mise à jour de l'application Windows Store pour utiliser le service mobile]: #update-app
  [Test de l'application Windows Store sur le service hébergé localement]: #test-locally-hosted
  [Publication du service mobile sur Azure]: #publish-mobile-service
  [Test de l'application Windows Store sur le service hébergé sous Azure]: #test-azure-hosted
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [application GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
  [4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png
  [5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
  [6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
  [8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
  [9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
  [10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png
  [11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
  [12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
  [13]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
  [14]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
  [15]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
  [16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png
  [17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
  [18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
  [19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-js
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library/
