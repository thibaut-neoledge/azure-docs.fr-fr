<properties 
	pageTitle="Prise en main du Push (Android) | Centre de développement mobile" 
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Android .Net." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="ricksal"/>

# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push-EC.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android. Dans ce didacticiel, vous ajoutez des notifications Push au projet de démarrage rapide à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré. 

Ce didacticiel vous familiarise avec les étapes suivantes :

1. [Activation de Google Cloud Messaging](#register)
2. [Configuration du service mobile pour l'envoi de demandes Push](#configure)
5. [Mise à jour du serveur pour l'envoi de notifications Push](#update-server)
7. [Ajout de notifications Push à votre application](#update-app)
8. [Activation des notifications Push pour les tests en local](#local-testing)
9. [Test de l'application avec le service mobile publié](#test-app)


Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, suivez le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données] pour connecter votre projet au service mobile. En tant que tel, ce didacticiel requiert également Visual Studio 2013. 

>[AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>. 


##<a id="register"></a>Activation de Google Cloud Messaging

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Configuration de Mobile Services pour l'envoi de demandes Push

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Cliquez sur l'onglet **Push**, puis entrez la valeur **Clé API** fournie par GCM dans la procédure précédente, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

> [AZURE.IMPORTANT] Lorsque vous définissez vos informations d'identification GCM pour les notifications Push améliorées dans l'onglet Push du portail, celles-ci sont partagées avec Notification Hubs pour configurer le concentrateur de notification avec votre application.


Votre service mobile est désormais configuré pour utiliser GCM et Notification Hubs.


<h2><a name="download-the-service"></a>Téléchargement du service sur votre ordinateur local</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Test du service mobile</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Mise à jour du serveur pour l'envoi de notifications Push

1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs. Ajoutez les instructions  `using` suivantes au début du fichier :


		using System;
		using System.Collections.Generic;

2. Mettez à jour la définition de méthode  `PostTodoItem` avec le code suivant :  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Ce code envoie une notification Push (avec le texte de l'élément inséré) après l'insertion d'un élément ToDo. En cas d'erreur, le code ajoute une entrée dans le journal des erreurs consultable à partir de l'onglet **Journaux** du service mobile sur le portail de gestion.


<h2><a name="publish-the-service"></a>Publication du service mobile sur Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Ajout de notifications Push à votre application

###Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version-EC.md)]


L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging requiert un niveau d'API minimal pour le développement et les tests, auquel la propriété **minSdkVersion** du manifeste doit correspondre. 

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour savoir comment définir cette valeur, puis définissez-la correctement.

###Ajout de services Google Play au projet

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services-EC.md)]

###Ajout de code

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push-EC.md)]

<h2><a name="test-app"></a>Test de l'application avec le service mobile publié</h2>

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

###Si vous utilisez l'émulateur pour les tests...

Assurez-vous d'utiliser un appareil virtuel Android (AVD) prenant en charge les API Google.

1. Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, sélectionnez votre appareil, cliquez sur **Modifier** (ou sur **Nouveau** si vous n'avez aucun appareil).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Sélectionnez **API Google** (ou **API Google x86**) dans **cible**, puis cliquez sur OK.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Cela permet de cibler l'AVD pour utiliser les API Google. Si vous avez installé plusieurs versions du Kit de développement logiciel (SDK) Android, vérifiez que le niveau d'API correspond à celui que vous avez défini précédemment dans les propriétés du projet.

###<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Exécution du test

1. Dans le menu **Exécuter** d'Eclipse, cliquez sur **Exécuter** pour démarrer l'application.

2. Dans l'application, tapez un texte explicite, comme _Nouvelle tâche Mobile Services_ et cliquez sur le bouton **Ajouter**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Balayez l'écran de haut en bas pour ouvrir le centre de notifications de l'appareil et afficher la notification.


Vous avez terminé ce didacticiel.


## <a name="next-steps"></a>Étapes suivantes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.


+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>Découvrez comment stocker et interroger des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec différents types de comptes à l'aide de Mobile Services.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenez des conseils sur le dépannage et le débogage des solutions Notification Hubs. 

* [Utilisation de la bibliothèque cliente Android pour Mobile Services]
  <br/>Découvrez comment utiliser Mobile Services avec Android.  
  
<!-- Anchors. -->

[Création d'un service mobile]: #create-service
[Téléchargement du service en local]: #download-the-service-locally
[Test du service mobile]: #test-the-service
[Téléchargement du projet GetStartedWithData]: #download-app
[Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données]: #update-app
[Test de l'application Android sur le service hébergé localement]: #test-locally-hosted
[Publication du service mobile sur Azure]: #publish-mobile-service
[Test de l'application Android sur le service hébergé dans Azure]: #test-azure-hosted
[Test de l'application avec le service mobile publié]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Validation et modification de données à l'aide de scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Prise en main de Mobile Services]: /documentation/articles/mobile-services-dotnet-backend-android-get-started
[Prise en main des données]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Prise en main de l'authentification]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript et HTML]: /develop/mobile/tutorials/get-started-with-data-js
[Version du service principal JavaScript]: /develop/mobile/tutorials/get-started-with-data-android
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site d'exemples de code développeur]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Guide de fonctionnement Mobile Services .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[Utilisation de la bibliothèque cliente Android pour Mobile Services]: /documentation/articles/mobile-services-android-how-to-use-client-library

[Envoi de notifications Push aux utilisateurs authentifiés]: /documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[Présentation de Notification Hubs]: /documentation/articles/notification-hubs-overview/
[Envoi de notifications diffusées aux abonnés]: /documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Envoi de notifications basées sur un modèle aux abonnés]: /documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
