<properties urlDisplayName="Get Started with Push" pageTitle="Prise en main du Push (Android) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Android .Net." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/26/2014" ms.author="ricksal" />

# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android. Dans ce didacticiel, vous ajoutez des notifications Push au projet de démarrage rapide à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré. 

Ce didacticiel vous familiarise avec les étapes suivantes :

1. [Activation de Google Cloud Messaging](#register)
2. [Configuration du service mobile pour l'envoi de demandes push](#configure)
5. [Mise à jour du serveur pour l'envoi de notifications Push](#update-server)
7. [Ajout de notifications Push à votre application](#update-app)
8. [Activation des notifications Push pour les tests en local](#local-testing)
9. [Test de l'application avec le service mobile publié]


Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données] pour connecter votre projet au service mobile. En tant que tel, ce didacticiel requiert également Visual Studio 2013. 

>[WACOM.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>. 


##<a id="register"></a>Activation de Google Cloud Messaging

[WACOM.INCLUDE [Activation de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Configuration de Mobile Services pour l'envoi de requêtes Push

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Cliquez sur l'onglet **Push**, puis entrez la valeur de **Clé API** fournie par GCM dans la procédure précédente, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Important</b>
	<p>Lorsque vous définissez vos informations d'identification GCM pour les notifications Push améliorées dans l'onglet Push du portail, celles-ci sont partagées avec Notification Hubs pour configurer le concentrateur de notification avec votre application.</p>
    </div>


Votre service mobile est désormais configuré pour utiliser GCM et Notification Hubs.


<h2><a name="download-the-service"></a>Téléchargement du service sur votre ordinateur local</h2>

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Test du service mobile</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Mise à jour du serveur pour l'envoi de notifications Push

1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs. Au début du fichier, ajoutez les instructions `using` suivantes :


		using System;
		using System.Collections.Generic;

2. Mettez à jour la définition de méthode `PostTodoItem` avec le code suivant :  

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

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Ajout de notifications Push à votre application

###Vérification de la version du Kit de développement logiciel (SDK) Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer. 

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur.

###Ajout de services Google Play au projet

[WACOM.INCLUDE [Ajout des services de lecture](../includes/mobile-services-add-Google-play-services.md)]

###Ajout de code

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

<h2><a name="test-app"></a>Test de l'application avec le service mobile publié</h2>

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

###Si vous utilisez l'émulateur à des fins de test...

Assurez-vous d'utiliser un appareil virtuel Android (AVD) prenant en charge les API Google.

1. Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, sélectionnez votre appareil, cliquez sur **Modifier** (ou **Nouveau** si vous n'avez aucun appareil).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Sélectionnez **API Google** (ou **API Google x86**) dans **Cible**, puis cliquez sur OK.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Cela permet de cibler l'AVD pour utiliser les API Google. Si vous avez installé plusieurs versions du Kit de développement logiciel (SDK) Android, vérifiez que le niveau d'API correspond à celui que vous avez défini précédemment dans les propriétés de projet.

###<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Exécution du test

1. Dans le menu **Exécuter** d'Eclipse, cliquez sur **Exécuter** pour démarrer l'application.

2. Dans l'application, tapez un texte explicite, comme _Nouvelle tâche Mobile Service_ et cliquez sur le bouton **Ajouter**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Faites glisser votre doigt du haut vers le bas de l'écran pour ouvrir le centre de notifications de l'appareil et afficher la notification.


Vous avez terminé ce didacticiel.


## <a name="next-steps"> </a>Étapes suivantes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.


+ [Send push notifications to authenticated users]
	<br/>Apprenez à utiliser des balises pour envoyer des notifications Push à partir d'un service mobile aux utilisateurs authentifiés uniquement.

+ [Envoi de notifications de diffusion aux abonnés]
	<br/>Découvrez comment les utilisateurs peuvent s'enregistrer et recevoir des notifications Push pour les catégories qui les intéressent.

+ [Envoi de notifications basées sur les abonnés]
	<br/>Apprenez à utiliser des modèles pour envoyer des notifications Push à partir d'un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.
-->
Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec différents types de comptes à l'aide des services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications de Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtention de conseils de dépannage et débogage des solutions Notification Hubs. 

* [Utilisation de la bibliothèque cliente Android pour Mobile Services]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec Android.  
  
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
[Validation et modification de données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/get-started-with-data-js
[Version principale JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) de Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Exemples de site de code de développement]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[Utilisation de la bibliothèque cliente Android pour Mobile Services]: /fr-fr/documentation/articles/mobile-services-android-how-to-use-client-library

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
[Envoi de notifications de diffusion aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Envoi de notifications basées sur les abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Portail de gestion Azure]: https://manage.windowsazure.com/
