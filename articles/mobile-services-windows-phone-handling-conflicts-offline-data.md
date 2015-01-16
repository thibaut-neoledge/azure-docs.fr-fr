<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Gestion des conflits liés aux données hors connexion dans Mobile Services (Windows Phone) | Centre de développement mobile" metaKeywords="" description="Apprenez à gérer les conflits à l'aide d'Azure Mobile Services lors de la synchronisation des données hors connexion dans votre application Windows Phone" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />


# Gestion des conflits liés à la synchronisation des données hors connexion dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone" class="current">Windows Phone</a>
<a href="/fr-fr/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS">iOS</a>
</div>


<p>Cette rubrique vous explique comment synchroniser les données et gérer les conflits lors de l'utilisation des fonctionnalités hors connexion d'Azure Mobile Services. Dans ce didacticiel, vous allez télécharger une application qui prend en charge à la fois les données hors connexion et en ligne, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher et mettre à jour la base de données lors de l'exécution de l'application.
</p>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main de données hors connexion]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données hors connexion].


Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet Windows Phone] 
2. [Ajout d'une colonne de date d'échéance pour la base de données]
  * [Mise à jour de la base de données pour les services mobiles principaux .NET]  
  * [Mise à jour de la base de données pour les services mobiles JavaScript]  
3. [Test de l'application sur base d'un service mobile]
4. [Mise à jour manuelle des données sur le serveur principal afin de créer un conflit]

Ce didacticiel nécessite Visual Studio 2012 et le [Kit de développement logiciel (SDK) Windows Phone 8].


## <a name="download-app"></a>Téléchargement de l'exemple de projet



Ce didacticiel repose sur l'[exemple de code de gestion des conflits], qui est un projet Windows Phone 8 pour Visual Studio 2012.  
L'interface utilisateur de cette application est similaire à celle de l'application présentée dans le didacticiel [Prise en main de données hors connexion], si ce n'est qu'il y a une nouvelle colonne de date pour chaque élément TodoItem.

![][0]


1. Téléchargez la version Windows Phone de l'[exemple de code de gestion des conflits]. 

2. Le cas échéant, installez [SQLite pour Windows Phone 8].

3. Dans Visual Studio 2012, ouvrez le projet téléchargé. Ajoutez une référence à **SQLite pour Windows Phone** sous **Windows Phone** > **Extensions**.

4. Dans Visual Studio 2012, appuyez sur la touche **F5** pour générer et exécuter l'application dans le débogueur.
 
5. Dans l'application, tapez du texte pour quelques nouveaux éléments todo, puis cliquez sur **Enregistrer** pour enregistrer chacun d'eux. Vous pouvez également modifier la date d'échéance des éléments todo que vous ajoutez.


Notez que dans la mesure où l'application n'est pas encore connectée à un service mobile, les boutons **Push** et **Pull** généreront des exceptions.


## <a name="add-column"></a>Ajout d'une colonne au modèle de données

Dans cette section, vous allez mettre à jour la base de données pour votre service mobile de façon à inclure une table TodoItem avec une colonne de date d'échéance. L'application vous permet de modifier la date d'échéance d'un élément au moment de l'exécution de façon à pouvoir générer des conflits de synchronisation dans une section ultérieure de ce didacticiel. 

La classe `TodoItem` dans l'exemple est définie dans MainPage.xaml.cs. Notez que la classe comporte l'attribut suivant qui va cibler les opérations de synchronisation sur la base de cette table.

        [DataTable("TodoWithDate")]

Mettez à jour votre base de données pour inclure cette table.

### <a name="dotnet-backend"></a>Mise à jour de la base de données pour les services mobiles principaux .NET 

Si vous utilisez le serveur principal .NET pour votre service mobile, procédez comme suit pour mettre à jour le schéma de votre base de données.

1. Ouvrez votre projet de service mobile principal .NET dans Visual Studio.
2. Dans l'Explorateur de solutions de Visual Studio, dans votre projet de service, développez le dossier **Models** et ouvrez ToDoItem.cs. Ajoutez ensuite la propriété `DueDate` comme suit.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **App_Start** et ouvrez le fichier WebApiConfig.cs. 

    Dans le fichier WebApiConfig.cs, notez que votre classe d'initialiseur de base de données par défaut est issue de la classe `DropCreateDatabaseIfModelChanges`. Cela signifie qu'à chaque modification apportée au modèle, la table sera supprimée et recréée pour s'adapter au nouveau modèle. Par conséquent, les données dans la table seront perdues et la table sera réamorcée. Modifiez la méthode Seed de l'initialiseur de base de données de façon à ce que la fonction d'initialisation `Seed()` fonctionne comme suit pour initialiser la nouvelle colonne DueDate. Enregistrez le fichier WebApiConfig.cs.

    >[WACOM.NOTE] Lors de l'utilisation de l'initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu'il détecte une modification du modèle de données dans la définition du modèle Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. Pour plus d'informations, consultez la rubrique [Utilisation des migrations Code First pour mettre à jour le modèle de données](/fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Controllers** et ouvrez ToDoItemController.cs. Renommez la classe `TodoItemController` en `TodoWithDateController`. Cette action modifie le point de terminaison REST pour les opérations de table. 

        public class TodoWithDateController : TableController<TodoItem>
    

5. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur votre projet de service mobile principal .NET, puis sélectionnez **Publier** pour publier vos modifications.


### <a name="javascript-backend"></a>Mise à jour de la base de données pour les services mobiles principaux JavaScript

Pour les services mobiles principaux JavaScript, vous allez ajouter une nouvelle table intitulée **TodoWithDate**. Pour ajouter la table **TodoWithDate** pour les services mobiles principaux JavaScript, procédez comme suit.

  1. Connectez-vous au [portail de gestion Azure]. 

  2. Accédez à l'onglet **Données** de votre service mobile. 

  3. Cliquez sur **Créer** en bas de la page et créez ensuite une table intitulée **TodoWithDate**. 


## <a name="test-app"></a>Test de l'application sur votre service mobile

À présent, il est temps de tester l'application sur base de Mobile Services.

1. Dans le portail de gestion Azure, recherchez la clé d'application de votre service mobile en cliquant sur **Gérer les clés** dans la barre de commandes de l'onglet **Tableau de bord**. Copiez la **clé d'application**.

2. Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier App.xaml.cs dans l'exemple de projet client. Modifiez l'initialisation de **MobileServiceClient** pour utiliser l'URL et la clé d'application de votre service mobile :

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Dans Visual Studio, appuyez sur la touche **F5** pour générer et exécuter l'application.

4. Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur **Enregistrer** pour enregistrer quelques nouveaux éléments todo. Cette action enregistre les données dans la table de synchronisation locale, mais pas sur le serveur.

    ![][0]

5. Pour afficher l'état actuel de votre base de données, connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis cliquez sur votre service mobile.

  * Si vous utilisez le serveur principal JavaScript pour votre service mobile, cliquez sur l'onglet **Données**, puis cliquez sur la table **TodoWithDate**. Cliquez sur **Parcourir** pour voir que la table reste vide, étant donné que nous n'avons pas envoyé par Push les modifications depuis l'application vers le serveur.

        ![][1]

  *  Si vous utilisez le serveur principal .NET pour votre service mobile, cliquez sur l'onglet **Configurer**, puis cliquez sur votre base de données SQL. Cliquez sur **Gérer** en bas de l'écran pour vous connecter au portail de gestion SQL Azure et afficher votre base de données en exécutant une requête SQL similaire à la suivante.
    
            SELECT * FROM todolist.todowithdate

        ![][2]

   	 

7. De retour dans l'application, cliquez sur **Push**.

8. Dans le portail de gestion, cliquez sur **Actualiser** dans la table **TodoItem**. À présent, les données que vous avez entrées dans votre application doivent normalement s'afficher.

   	![][3]

9. Laissez **Emulator WVGA 512MB(FR)** en fonction pour la section suivante dans laquelle vous exécuterez l'application dans deux émulateurs pour générer un conflit.

## <a name="handle-conflict"></a>Mise à jour des données sur le serveur principal afin de créer un conflit

Dans un scénario réel, un conflit de synchronisation se produit lorsqu'une application envoie par push les mises à jour vers un enregistrement de la base de données, puis qu'une autre application essaie d'envoyer par push une modification vers le même enregistrement au moyen d'une version de champ obsolète de cet enregistrement. Si une instance de l'application essaie de mettre à jour le même enregistrement, sans extraire l'enregistrement mis à jour, un conflit se produit et est intercepté en tant que `MobileServicePreconditionFailedException` dans l'application.  

Dans cette section, vous allez exécuter simultanément deux instances de l'application afin de générer un conflit. 


1. Si **Emulator WVGA 512MB(FR)** n'est pas en fonction, appuyez sur **Ctrl+F5** pour le relancer.

2. Dans Visual Studio, indiquez le périphérique de sortie **Emulator WVGA(FR)** et exécutez une deuxième instance de l'application dans le nouvel émulateur en appuyant sur **F5**.
 
    ![][5]
 
   
3. Dans la deuxième instance de l'application, cliquez sur **Pull** pour synchroniser le magasin local avec la base de données du service mobile. Les deux instances de l'application doivent avoir les mêmes données.
 
    ![][6]

4. Dans la deuxième instance de l'application, activez la case à cocher pour terminer l'un des éléments, puis cliquez sur **Push** pour transmettre par push vos modifications à la base de données distante. Dans la capture d'écran suivante, la tâche **Pick up James** a été effectuée, ce qui indique qu'on a déjà été chercher James. La première instance de l'application comporte à présent un enregistrement obsolète.

    ![][9]

5. Dans la première instance de l'application, essayez de modifier la date de l'enregistrement obsolète, puis cliquez sur **Push** pour essayer de mettre à jour la base de données distante avec l'enregistrement obsolète. Dans la capture d'écran ci-dessous, nous essayons de planifier que James sera pris en voiture le **5/10/2014**.

    ![][7]

6. Lorsque vous cliquez sur le bouton **Push** pour valider le changement de date, une boîte de dialogue s'affiche pour indiquer que le conflit a été détecté. Vous êtes invité à préciser la façon dont vous voulez résoudre le conflit. Choisissez l'une des options de résolution de conflit.

    Dans le scénario ci-dessous, James a déjà été pris en voiture. Par conséquent, il est inutile de planifier sa prise en charge le **5/10/2014**. L'option **Utiliser la version serveur** serait sélectionnée de sorte que la première instance de l'application aurait mis à jour cet enregistrement avec l'enregistrement du serveur. 

    ![][8]

## Examen du code permettant de gérer les conflits de synchronisation

Pour pouvoir configurer la fonctionnalité de détection des conflits, vous devez inclure une colonne de version à la fois dans votre base de données locale et dans votre objet de transfert de données. La classe " TodoItem " contient le membre suivant :

        [Version]
        public string Version { get; set; }

La colonne `__version` est également spécifiée dans la base de données locale configurée dans la méthode `OnNavigatedTo()`.

Pour gérer les conflits de synchronisation hors connexion dans votre code, vous créez une classe qui implémente `IMobileServiceSyncHandler`. Transmettez un objet de ce type dans l'appel à `InitializeAsync` :

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La classe `SyncHandler` dans **MainPage.xaml.cs** implémente `IMobileServiceSyncHandler`. La méthode `ExecuteTableOperationAsync` est appelée lorsque chaque opération Push est envoyée vers le serveur. Si une exception de type `MobileServicePreconditionFailedException` est générée, cela signifie qu'il y a un conflit entre la version locale et la version distante d'un élément.

Pour résoudre les conflits au profit de l'élément local, essayez simplement à nouveau l'opération. Une fois qu'un conflit a eu lieu, la version locale de l'élément est mise à jour de façon à correspondre à la version serveur. Par conséquent, une nouvelle exécution de l'opération remplacera les modifications du serveur par les modifications locales :

    await operation.ExecuteAsync(); 

Pour résoudre les conflits au profit de l'élément serveur, exécutez simplement la méthode `ExecuteTableOperationAsync`. La version locale de l'objet sera ignorée et remplacée par la valeur provenant du serveur.

Pour interrompre l'opération Push (mais conserver les modifications en file d'attente), utilisez la méthode `AbortPush()` :

    operation.AbortPush();

Cette procédure interrompt l'opération Push, mais conserve toutes les modifications en attente, y compris l'opération en cours si `AbortPush` est appelé à partir de `ExecuteTableOperationAsync`. La prochaine fois que `PushAsync()` est appelé, ces modifications sont envoyées vers le serveur. 

Lorsqu'une opération Push est annulée, `PushAsync` génère une exception `MobileServicePushFailedException` et la propriété d'exception `PushResult.Status` a la valeur `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Téléchargement du projet Windows Phone]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une colonne de date d'échéance pour la base de données]: #add-column
[Mise à jour de la base de données pour les services mobiles principaux .NET]: #dotnet-backend  
[Mise à jour de la base de données pour les services mobiles JavaScript]: #javascript-backend
[Test de l'application sur base d'un service mobile]: #test-app
[Mise à jour manuelle des données sur le serveur principal afin de créer un conflit]: #handle-conflict
[Étapes suivantes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[7]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[8]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
[9]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png



<!-- URLs -->
[Exemple de code de gestion des conflits]: http://go.microsoft.com/fwlink/?LinkId=398257
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started/
[Prise en main de données hors connexion]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-offline-data
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Gestion des conflits de base de données]: /fr-fr/documentation/articles/mobile-services-windows-phone-handle-database-conflicts/#test-app
[Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[SQLite pour Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/
