<properties 
	pageTitle="Gestion des conflits liés aux données hors connexion dans Mobile Services (Windows Store) | Centre de développement mobile" 
	description="Apprenez à gérer les conflits à l'aide d'Azure Mobile Services lors de la synchronisation des données hors connexion dans votre application Windows Store" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>


# Gestion des conflits liés à la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##Vue d'ensemble

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Cette rubrique vous explique comment synchroniser les données et gérer les conflits lors de l'utilisation des fonctionnalités hors connexion d'Azure Mobile Services.</p>
<p>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">14:36:00</span></div>
</div>

Dans ce didacticiel, vous allez télécharger une solution Windows Universal c# pour une application prenant en charge la gestion des conflits de synchronisation hors connexion. Vous allez intégrer un service mobile à l'application, puis exécuter les clients Windows Store 8.1 et Windows Phone 8.1 pour générer un conflit de synchronisation et le résoudre.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données hors connexion]. Avant de commencer, nous vous conseillons de suivre d'abord le didacticiel [Prise en main des données hors connexion].


##Configuration requise

Ce didacticiel requiert Visual Studio 2013 s'exécutant sous Windows 8.1.


##Téléchargement de l'exemple de projet

![][0]

Ce didacticiel est une procédure pas à pas expliquant comment l'[Exemple TODO hors connexion Mobile Services] gère les conflits de synchronisation entre le magasin hors connexion local et la base de données Mobile Services dans Azure.

1. Téléchargez le fichier .zip du [Référentiel GitHub d'exemples Mobile Services] pour l'extraire dans un répertoire de travail. 

2. Si vous n'avez pas déjà installé SQLite pour Windows 8.1 et Windows Phone 8.1 comme indiqué dans le didacticiel [Prise en main des données hors connexion], installez les deux runtimes.

3. Dans Visual Studio 2013, ouvrez le fichier de solution *mobile-services-samples\\TodoOffline\\WindowsUniversal\\TodoOffline-Universal.sln*. Appuyez sur **F5** pour régénérer et exécuter le projet. Assurez-vous que les packages NuGet sont restaurés et que les références sont correctement définies.

    >[AZURE.NOTE]Vous devrez peut-être supprimer les anciennes références pointant vers le runtime SQLite et les remplacer par la référence mise à jour, comme indiqué dans le didacticiel [Prise en main des données hors connexion].

4. Dans l'application, entrez du texte dans la zone **Insérer un TodoItem**, puis cliquez sur **Enregistrer** pour ajouter des éléments todo au magasin local. Fermez ensuite l'application.

Notez que dans la mesure où l'application n'est pas encore connectée à un service mobile, les boutons **Push** et **Pull** généreront des exceptions.




##Test de l'application sur votre service mobile

À présent, il est temps de tester l'application sur base de Mobile Services.

1. Dans le portail de gestion Azure, recherchez la clé d'application de votre service mobile en cliquant sur **Gérer les clés** dans la barre de commandes de l'onglet **Tableau de bord**. Copiez la **clé d'application**.

2. Dans l'Explorateur de solutions pour Visual Studio, ouvrez le fichier App.xaml.cs dans l'exemple de projet client. Modifiez l'initialisation de **MobileServiceClient** pour utiliser l'URL et la clé d'application de votre service mobile :

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application de nouveau.

    ![][0]


##Mise à jour des données sur le backend afin de créer un conflit

Dans un scénario réel, un conflit de synchronisation se produit lorsqu'une application envoie par push les mises à jour vers un enregistrement de la base de données, puis qu'une autre application essaie d'envoyer par push une mise à jour vers le même enregistrement au moyen d'une version de champ obsolète de cet enregistrement. Si vous vous rappelez le contenu du didacticiel [Prise en main des données hors connexion], vous savez que la propriété « version » du système est requise pour la prise en charge des fonctionnalités de synchronisation hors connexion. Ces informations de version sont examinées lors de chaque mise à jour de la base de données. Si une instance de l'application tente de mettre à jour un enregistrement en utilisant une version obsolète, cela va générer un conflit qui se présentera sous la forme d'une exception `MobileServicePreconditionFailedException` dans l'application. Si l'application n'intercepte pas l'exception `MobileServicePreconditionFailedException`, une exception `MobileServicePushFailedException` est alors levée. Elle décrit le nombre d'erreurs de synchronisation rencontrées.

>[AZURE.NOTE]Pour prendre en charge la synchronisation des enregistrements supprimés avec la synchronisation des données hors connexion, vous devez activer la [Suppression réversible](mobile-services-using-soft-delete.md). Sinon, vous devez supprimer manuellement les enregistrements dans le magasin local, ou appeler `IMobileServiceSyncTable::PurgeAsync()` pour purger le magasin local.


La procédure suivante indique comment exécuter les clients Windows Phone 8.1 et Windows Store 8.1 en même temps pour provoquer et résoudre un conflit à l'aide de l'exemple.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet Windows Phone 8.1 et cliquez sur **Définir comme projet de démarrage**. Puis, appuyez sur **Ctrl+F5** pour exécuter le client Windows Phone 8.1 sans débogage. Une fois le client Windows Phone 8.1 opérationnel dans l'émulateur, cliquez sur **Pull** pour synchroniser le magasin local avec l'état actuel de la base de données.
 
    ![][3]
 
   
2. Dans Visual Studio, cliquez avec le bouton droit sur le projet de runtime Windows 8.1, puis cliquez sur **Définir comme projet de démarrage**. Ensuite, appuyez sur **F5** pour l'exécuter. Une fois le client Windows Store 8.1 opérationnel, cliquez sur **Pull** pour synchroniser le magasin local avec l'état actuel de la base de données.

    ![][4]
 
3. À ce stade, les deux clients sont synchronisés avec la base de données. Le code de ces deux clients utilise également la synchronisation des valeurs incrémentielles, de sorte qu'ils synchroniseront uniquement des éléments todo incomplets. Les éléments todo complets seront ignorés. Choisissez l'un des éléments et définissez le texte d'un même élément sur une valeur différente pour chaque client. Cliquez sur le bouton **Push** pour synchroniser les modifications avec la base de données sur le serveur.

    ![][5]

    ![][6]


4. Le client dont la notification push a été exécutée en dernier se heurte au conflit et demande à l'utilisateur de choisir la valeur à valider dans la base de données. L'exception fournit la valeur de la version correcte utilisée pour résoudre le conflit.

    ![][7]



##Examen du code permettant de gérer les conflits de synchronisation

Pour utiliser les fonctionnalités hors connexion pour Mobile Services, vous devez inclure la colonne de version dans votre base de données locale et votre objet de transfert de données. Pour cela, vous devez mettre à jour la classe `TodoItem` du membre suivant :

        [Version]
        public string Version { get; set; }

La colonne `__version` est incluse dans la base de données locale de la méthode `OnNavigatedTo()` lorsque la classe `TodoItem` est utilisée pour définir le magasin local.

Pour gérer les conflits de synchronisation hors connexion dans votre code, vous créez une classe qui implémente `IMobileServiceSyncHandler`. Transmettez un objet de ce type dans l'appel à `MobileServiceClient.SyncContext.InitializeAsync()`. Cela se produit également dans la méthode `OnNavigatedTo()` de l'exemple.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La classe `SyncHandler` de **SyncHandler.cs** implémente `IMobileServiceSyncHandler`. La méthode `ExecuteTableOperationAsync` est appelée lorsque chaque opération push est envoyée vers le serveur. Si une exception de type `MobileServicePreconditionFailedException` est levée, cela signifie qu'il y a un conflit entre la version locale et la version distante d'un élément.

Pour résoudre les conflits au profit de l'élément local, essayez simplement à nouveau l'opération. Une fois qu'un conflit a eu lieu, la version locale de l'élément est mise à jour de façon à correspondre à la version serveur. Par conséquent, une nouvelle exécution de l'opération remplacera les modifications du serveur par les modifications locales :

    await operation.ExecuteAsync(); 

Pour résoudre les conflits au profit de l'élément serveur, exécutez simplement la méthode `ExecuteTableOperationAsync`. La version locale de l'objet sera ignorée et remplacée par la valeur provenant du serveur.

Pour interrompre l'opération push (mais conserver les modifications en file d'attente), utilisez la méthode `AbortPush()` :

    operation.AbortPush();

Cette procédure interrompt l'opération push, mais conserve toutes les modifications en attente, y compris l'opération en cours si `AbortPush` est appelé à partir de `ExecuteTableOperationAsync`. La prochaine fois que `PushAsync()` est appelé, ces modifications sont envoyées vers le serveur.

Lorsqu'une opération Push est annulée, `PushAsync` génère une `MobileServicePushFailedException`, et la propriété d'exception `PushResult.Status` a la valeur `MobileServicePushStatus.CancelledByOperation`.



<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: ../mobile-services-windows-store-get-started.md
[Prise en main des données hors connexion]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure Management Portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: mobile-services-windows-store-dotnet-handle-database-conflicts.md#test-app
[Référentiel GitHub d'exemples Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=512865
[Exemple TODO hors connexion Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=512866
 

<!---HONumber=July15_HO3-->