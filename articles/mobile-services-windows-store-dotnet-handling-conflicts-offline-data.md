<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Gestion des conflits liés à la synchronisation des données hors connexion dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Cette rubrique vous explique comment synchroniser les donn&eacute;es et g&eacute;rer les conflits lors de l'utilisation des fonctionnalit&eacute;s hors connexion d'Azure Mobile Services.</p>
<p>Si vous pr&eacute;f&eacute;rez regarder une vid&eacute;o, le clip sur la droite suit la m&ecirc;me proc&eacute;dure que ce didacticiel.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">14:36:00</span></div>

</div>

Dans ce didacticiel, vous allez télécharger une application qui prend en charge à la fois les données hors connexion et en ligne, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher et mettre à jour la base de données lors de l'exécution de l'application.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données hors connexion][Prise en main des données hors connexion]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données hors connexion][Prise en main des données hors connexion].

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][Téléchargement d'un projet d'application Windows Store]
2.  [Ajout d'une colonne de date d'échéance pour la base de données][Ajout d'une colonne de date d'échéance pour la base de données]

-   [Mise à jour de la base de données pour les services mobiles principaux .NET][Mise à jour de la base de données pour les services mobiles principaux .NET]
-   [Mise à jour de la base de données pour les services mobiles JavaScript][Mise à jour de la base de données pour les services mobiles JavaScript]

1.  [Test de l'application sur base d'un service mobile][Test de l'application sur base d'un service mobile]
2.  [Mise à jour manuelle des données sur le serveur principal afin de créer un conflit][Mise à jour manuelle des données sur le serveur principal afin de créer un conflit]

Ce didacticiel requiert Visual Studio 2013 s'exécutant sous Windows 8.1.

## <a name="download-app"></a>Téléchargement de l'exemple de projet

![][0]

Ce didacticiel repose sur l'[exemple de code de gestion des conflits][exemple de code de gestion des conflits], qui est un projet d'application Windows Store pour Visual Studio 2013. L'interface utilisateur pour cette application est similaire à l'application dans le didacticiel [Prise en main de données hors connexion][Prise en main des données hors connexion], mis à part qu'il y a une nouvelle colonne de date pour chaque élément TodoItem.

1.  Téléchargez la version C# de l'[exemple de code de gestion des conflits][exemple de code de gestion des conflits].

2.  Le cas échéant, installez [SQLite pour Windows 8.1][SQLite pour Windows 8.1].

3.  Dans Visual Studio 2013, ouvrez le projet téléchargé. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**. Vous pouvez également modifier la date d'échéance des éléments todo que vous ajoutez.

Notez que dans la mesure où l'application n'est pas encore connectée à un service mobile, les boutons **Push** et **Pull** généreront des exceptions.

## <a name="add-column"></a>Ajout d'une colonne au modèle de données

Dans cette section, vous allez mettre à jour la base de données pour votre service mobile de façon à inclure une table TodoItem avec une colonne de date d'échéance. L'application vous permet de modifier la date d'échéance d'un élément au moment de l'exécution de façon à pouvoir générer des conflits de synchronisation dans une section ultérieure de ce didacticiel.

La classe `TodoItem` dans l'exemple est définie dans MainPage.xaml.cs. Notez que la classe comporte l'attribut suivant qui va cibler les opérations de synchronisation sur la base de cette table.

        [DataTable("TodoWithDate")]

Mettez à jour votre base de données pour inclure cette table.

### <a name="dotnet-backend"></a>Mise à jour de la base de données pour les services mobiles principaux .NET

Si vous utilisez le serveur principal .NET pour votre service mobile, procédez comme suit pour mettre à jour le schéma de votre base de données.

1.  Ouvrez votre projet de service mobile principal .NET dans Visual Studio.
2.  Dans l'Explorateur de solutions pour Visual Studio, dans votre projet de service, développez le dossier **Modèles** et ouvrez ToDoItem.cs. Ajoutez la propriété `DueDate` comme suit.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }

3.  Dans l'Explorateur de solutions pour Visual Studio, développez le dossier **App\_Start** et ouvrez le fichier WebApiConfig.cs.

    Dans le fichier WebApiConfig.cs, notez que votre classe d'initialiseur de base de données par défaut est issue de la classe `DropCreateDatabaseIfModelChanges`. Cela signifie qu'à chaque modification apportée au modèle, la table sera supprimée et recréée pour s'adapter au nouveau modèle. Par conséquent, les données dans la table seront perdues et la table sera réamorcée. Modifiez la méthode Seed de l'initialiseur de base de données de façon à ce que la fonction d'initialisation `Seed()` fonctionne comme suit pour initialiser la nouvelle colonne DueDate. Enregistrez le fichier WebApiConfig.cs.

    > [WACOM.NOTE] Lors de l'utilisation de l'initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu'il détecte une modification du modèle de données dans la définition du modèle Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. Pour plus d'informations, consultez la rubrique [Utilisation des migrations Code First pour mettre à jour le modèle de données][Utilisation des migrations Code First pour mettre à jour le modèle de données].

        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  Dans l'Explorateur de solutions pour Visual Studio, développez le dossier **Controllers** et ouvrez ToDoItemController.cs. Renommez la classe `TodoItemController` en `TodoWithDateController`. Cette action modifie le point de terminaison REST pour les opérations de table.

        public class TodoWithDateController : TableController<TodoItem>

5.  Dans l'Explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur votre projet de service mobile principal .NET, puis sur **Publier** pour publier vos modifications.

### <a name="javascript-backend"></a>Mise à jour de la base de données pour les services mobiles principaux JavaScript

Pour les services mobiles principaux JavaScript, vous allez ajouter une nouvelle table intitulée **TodoWithDate**. Pour ajouter la table **TodoWithDate** pour les services mobiles principaux JavaScript, procédez comme suit.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Accédez à l'onglet **Données** de votre service mobile.

3.  Cliquez sur **Créer** en bas de la page et créez une nouvelle table intitulée **TodoWithDate**.

## <a name="test-app"></a>Test de l'application sur base de votre service mobile

À présent, il est temps de tester l'application sur base de Mobile Services.

1.  Dans le portail de gestion Azure, recherchez la clé d'application de votre service mobile en cliquant sur **Gérer les clés** dans la barre de commandes de l'onglet **Tableau de bord**. Copiez la **clé d'application**.

2.  Dans l'Explorateur de solutions pour Visual Studio, ouvrez le fichier App.xaml.cs dans l'exemple de projet client. Modifiez l'initialisation de **MobileServiceClient** pour utiliser l'URL et la clé d'application de votre service mobile :

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3.  Dans Visual Studio, appuyez sur **F5** pour générer et exécuter l'application.

4.  Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur **Enregistrer**. Cette action enregistre les données dans la table de synchronisation locale, mais pas sur le serveur.

    ![][0]

5.  Pour afficher l'état actuel de votre base de données, connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis cliquez sur votre service mobile.

-   Si vous utilisez le serveur principal JavaScript pour votre service mobile, cliquez sur l'onglet **Données**, puis cliquez sur la table **TodoWithDate**. Cliquez sur **Parcourir** pour voir que la table reste vide, étant donné que nous n'avons pas envoyé par Push les modifications depuis l'application vers le serveur.

        ![][1]

-   Si vous utilisez le serveur principal .NET pour votre service mobile, cliquez sur l'onglet **Configurer**, puis cliquez sur votre base de données SQL. Cliquez sur **Gérer** en bas de l'écran pour vous connecter au portail de gestion SQL Azure et afficher votre base de données en exécutant une requête SQL similaire à la suivante.

            SELECT * FROM todolist.todowithdate

        ![][2]

1.  De retour dans l'application, cliquez sur **Push**.

2.  Dans le portail de gestion, cliquez sur **Actualiser** dans la table **TodoItem**. À présent, les données que vous avez entrées dans votre application doivent normalement s'afficher.

    ![][1]

## <a name="handle-conflict"></a>Mise à jour des données sur le serveur principal afin de créer un conflit

Dans un scénario réel, un conflit de synchronisation se produit lorsqu'une application envoie par push les mises à jour vers un enregistrement de la base de données, puis qu'une autre application essaie d'envoyer par push une modification vers le même enregistrement au moyen d'une version de champ obsolète de cet enregistrement. Si une instance de l'application essaie de mettre à jour le même enregistrement, sans extraire l'enregistrement mis à jour, un conflit se produit et est intercepté en tant que `MobileServicePreconditionFailedException` dans l'application.

Si vous voulez déployer l'application sur une autre machine pour exécuter deux instances de l'application et générer un conflit, vous pouvez suivre les instructions de déploiement dans le didacticiel [Gestion des conflits de base de données][Gestion des conflits de base de données].

La procédure suivante explique comment mettre à jour la base de données dans Visual Studio afin de générer un conflit.

1.  Dans Visual Studio, exécutez l'Explorateur de serveurs et connectez-vous à votre compte Azure. Développez vos **bases de données SQL** pour votre compte Azure.

    ![][2]

2.  Cliquez avec le bouton droit sur votre base de données SQL dans la liste et cliquez sur **Ouvrir dans Explorateur d'objets SQL Server**.
3.  Dans l'Explorateur d'objets SQL Server, développez votre base de données et développez **Tables**. Cliquez avec votre bouton droit sur votre table **TodoWithDate** et cliquez sur **Afficher les données**.

4.  Modifiez le champ **complete** de l'un des éléments sur True.

    ![][3]

5.  De retour dans votre application Todo, modifiez le même élément que vous avez modifié dans la base de données directement.

    ![][4]

6.  Cliquez sur le bouton **Push**. Une boîte de dialogue vous invite à préciser la façon dont vous voulez résoudre le conflit. Choisissez l'une des options de résolution de conflit.

    ![][5]

## Examen du code permettant de gérer les conflits de synchronisation

Pour pouvoir configurer la fonctionnalité de détection des conflits, vous devez inclure une colonne de version à la fois dans votre base de données locale et votre objet de transfert de données. La classe `TodoItem` contient le membre suivant :

        [Version]
        public string Version { get; set; }

La colonne `__version` est également spécifiée dans la base de données locale configurée dans la méthode `OnNavigatedTo()`.

Pour gérer les conflits de synchronisation hors connexion dans votre code, vous créez une classe qui implémente `IMobileServiceSyncHandler`. Transmettez un objet de ce type dans l'appel à `InitializeAsync` :

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La classe `SyncHandler` dans **MainPage.xaml.cs** implémente `IMobileServiceSyncHandler`. La méthode `ExecuteTableOperationAsync` est appelée lorsque chaque opération Push est envoyée vers le serveur. Si une exception de type `MobileServicePreconditionFailedException` est générée, cela signifie qu'il y a un conflit entre la version locale et la version distante d'un élément.

Pour résoudre les conflits au profit de l'élément local, essayez simplement à nouveau l'opération. Une fois qu'un conflit a eu lieu, la version locale de l'élément est mise à jour de façon à correspondre à la version serveur. Par conséquent, une nouvelle exécution de l'opération remplacera les modifications du serveur par les modifications locales :

    await operation.ExecuteAsync(); 

Pour résoudre les conflits au profit de l'élément serveur, exécutez simplement la méthode `ExecuteTableOperationAsync`. La version locale de l'objet sera ignorée et remplacée par la valeur provenant du serveur.

Pour interrompre l'opération Push (mais conserver les modifications en file d'attente), utilisez la méthode `AbortPush()` :

    operation.AbortPush();

Cette procédure interrompt l'opération Push, mais conserve toutes les modifications en attente, y compris l'opération en cours si `AbortPush` est appelé à partir de `ExecuteTableOperationAsync`. La prochaine fois que `PushAsync()` est appelé, ces modifications sont envoyées vers le serveur.

Lorsqu'une opération Push est annulée, `PushAsync` génère une `MobileServicePushFailedException`, et la propriété d'exception `PushResult.Status` a la valeur `MobileServicePushStatus.CancelledByOperation`.

 <!-- Images --> <!-- URLs -->

  [Prise en main des données hors connexion]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Ajout d'une colonne de date d'échéance pour la base de données]: #add-column
  [Mise à jour de la base de données pour les services mobiles principaux .NET]: #dotnet-backend
  [Mise à jour de la base de données pour les services mobiles JavaScript]: #javascript-backend
  [Test de l'application sur base d'un service mobile]: #test-app
  [Mise à jour manuelle des données sur le serveur principal afin de créer un conflit]: #handle-conflict
  [0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
  [exemple de code de gestion des conflits]: http://go.microsoft.com/fwlink/?LinkId=394787
  [SQLite pour Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
  [Utilisation des migrations Code First pour mettre à jour le modèle de données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
  [Gestion des conflits de base de données]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
  [2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
  [3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
  [4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
  [5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png
