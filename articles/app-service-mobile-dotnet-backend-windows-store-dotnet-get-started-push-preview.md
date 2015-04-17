<properties 
	pageTitle="Ajout de notifications push à votre application universelle Windows à l'aide d'Azure App Service" 
	description="Découvrez comment utiliser Azure App Service pour envoyer des notifications push à votre application universelle Windows." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="yuaxu"/>

# Ajout de notifications push à votre application du Windows Store

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Cette rubrique montre comment envoyer des notifications push à une application universelle Windows à partir d'un backend .NET à l'aide d'Azure App Service. Une fois terminé, vous envoyez des notifications push à partir de votre backend .NET à toutes les applications universelles Windows inscrites lors de l'insertion d'un enregistrement.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications push :

1. [Inscription de votre application pour les notifications push](#register)
2. [Configuration](#configure)
3. [Mise à jour du service pour l'envoi de notifications push](#update-service)
4. [Ajout des notifications push à votre application](#add-push)
5. [Test des notifications push dans votre application](#test)

Ce didacticiel est basé sur le démarrage rapide d'App Service Mobile App. Avant de commencer, vous devez suivre le didacticiel [Prise en main des applications mobiles App Service].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* un [compte Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) actif.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Inscription de votre application pour les notifications push

Pour envoyer des notifications push vers des applications universelles Windows à l'aide d'Azure App Service, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer les informations d'identification du service de notifications push de votre application mobile pour les intégrer à WNS.

1. Si vous n'avez pas encore inscrit votre application, accédez à la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Soumettre une application</a> du Centre de développement pour les applications du Windows Store, connectez-vous avec votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

    ![][0]

2. Tapez un nom pour l'application dans **Nom de l'application**, puis cliquez sur **Réserver le nom d'application** et sur **Enregistrer**.

    ![][1]

    La nouvelle inscription au Windows Store pour votre application est créée.

4. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application du Windows Store, puis cliquez sur **Store** et sur **Associer l'application au Windows Store...**. 

    ![][3]

    L'Assistant **Associer votre application au Windows Store** s'affiche.

5. Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6. Cliquez sur l'application inscrite à l'étape 2, sur **Suivant** puis sur **Associer**.

    ![][4]

    Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application. 

7. (Facultatif) Répétez les étapes 4 à 6 pour le projet d'application Windows Phone Store.  

7. De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**. 

    ![][5] 

8. Dans la page **Services**, cliquez sur **Live Services site** sous **Microsoft Azure Mobile Services**.

    ![][17]

9. Sous l'onglet **Paramètres de l'application**, notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**. 

    ![][6]

    > [AZURE.NOTE] **Remarque relative à la sécurité**
    La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.

##<a id="configure"></a>Configuration de l'application mobile pour l'envoi de demandes push

1. Connectez-vous à la [version préliminaire du portail Azure], sélectionnez **Parcourir**, **Mobile App**, puis cliquez sur votre application. Cliquez sur les services de notification push.

2. Dans le service de notification Windows, entrez votre **Clé secrète client** et votre **Identificateur de sécurité (SID) du package**, puis enregistrez.

Votre application App Service Mobile App est maintenant configurée pour fonctionner avec WNS.

<!-- URLs. -->
[Version préliminaire du portail Azure]: https://portal.azure.com/

##<a id="update-service"></a>Mise à jour du service pour l'envoi de notifications push

Maintenant que les notifications push sont activées dans l'application, vous devez mettre à jour votre backend d'application pour envoyer des notifications push. 

1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**.

2. Recherchez **Microsoft.Azure.NotificationHubs** et cliquez sur **Installer** pour tous les projets dans la solution.

3. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de backend mobile. Ouvrez TodoItemController.cs. Ajoutez les instructions  `using` suivantes au début du fichier :

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Ajoutez l'extrait de code suivant à la méthode `PostTodoItem` après l'appel à **InsertAsync** :  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        try
        {
            await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);
        }
        catch (System.Exception ex)
        {
            throw ex;
        }

    Ce code indique au hub de notifications associé à cette application mobile d'envoyer une notification push après l'insertion d'une tâche.


<h2><a name="publish-the-service"></a>Publication du backend mobile sur Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Ajout des notifications push à votre application

1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**. 

    La boîte de dialogue Gérer les packages NuGet s'affiche.

2. Recherchez le Kit de développement logiciel (SDK) de client App Service Mobile App et cliquez sur **Installer**, sélectionnez tous les projets de la solution et acceptez les conditions d'utilisation. 

    Cette opération lance le téléchargement, l'installation et l'ajout d'une référence dans tous les projets à la bibliothèque Azure Mobile Push pour Windows. 

3. Ouvrez le fichier de projet **App.xaml.cs** et ajoutez les instructions `using` suivantes :

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    Dans un projet universel, ce fichier se trouve dans le dossier `<project_name>.Shared`.

4. Dans le même fichier, ajoutez la définition de la méthode **InitNotificationsAsync** à la classe **App** :
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            var result = MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Ce code récupère l'URI de canal ChannelURI pour l'application dans WNS et l'inscrit avec votre application App Service Mobile App.
    
5. En haut du gestionnaire d'événements **OnLaunched** dans **App.xaml.cs**, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

        InitNotificationsAsync();

    Cela garantit l'inscription de l'URI de canal ChannelURI de courte durée chaque fois que l'application est lancée.

6. Dans l'Explorateur de solutions, double-cliquez sur **Package.appxmanifest** de l'application du Windows Store et, dans **Notifications**, définissez **Compatible toast** sur **Oui**.

    Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

7. (Facultatif) Répétez l'étape précédente dans le projet d'application Windows Phone Store.

8. Appuyez sur la touche **F5** pour exécuter les applications.

Votre application est maintenant prête à recevoir des notifications toast.

##<a id="test"></a>Test des notifications push dans votre application

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!--HONumber=49-->