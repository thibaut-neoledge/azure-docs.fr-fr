<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universel">Windows Universel</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS.
Le didacticiel vous apprend à créer une application iOS vide qui reçoit des notifications Push à l'aide du service de notification Push Apple (APN, Apple Push Notification). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat][Génération de la demande de signature de certificat]
2.  [Inscription de votre application et activation des notifications Push][Inscription de votre application et activation des notifications Push]
3.  [Création d'un profil de mise en service pour l'application][Création d'un profil de mise en service pour l'application]
4.  [Configuration de votre concentrateur de notification][Configuration de votre concentrateur de notification]
5.  [Connexion de votre application au concentrateur de notification][Connexion de votre application au concentrateur de notification]
6.  [Envoi de notifications à partir de votre serveur principal][Envoi de notifications à partir de votre serveur principal]

Ce didacticiel présente un scénario de diffusion simple utilisant les concentrateurs de notification. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les composants requis suivants :

-   [Kit de développement logiciel (SDK) Mobile Services iOS][Kit de développement logiciel (SDK) Mobile Services iOS]
-   [XCode 4,5][XCode 4,5]
-   Un appareil compatible iOS 5.0 (ou version ultérieure)
-   Un abonnement au programme pour développeurs iOS

   <div class="dev-callout"><b>Remarque</b>
   <p>En raison de la configuration requise pour les notifications Push, vous devez d&eacute;ployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un &eacute;mulateur.</p>
   </div>

Vous devez accomplir ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications iOS.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

[WACOM.INCLUDE [Activer les notifications Push Apple](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>Configuration de votre concentrateur de notification

1.  Dans Trousseaux d'accès, cliquez avec le bouton droit sur le nouveau certificat de l'application de démarrage rapide et cliquez sur **Mes certificats**. Cliquez sur **Exporter**, nommez le fichier, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

    ![][0]

  Notez le nom du fichier et l'emplacement du certificat exporté.

> [WACOM.NOTE] Ce didacticiel crée un fichier QuickStart.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure] et cliquez sur **+NOUVEAU** en bas de l'écran.

2.  Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

    ![][1]

3.  Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

    ![][2]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

    ![][3]

5.  Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur celui que vous venez de créer.

    ![][4]

6.  Cliquez sur l'onglet **Configurer** en haut de la page, puis cliquez sur **Télécharger** dans les paramètres de notification d'Apple. Sélectionnez ensuite le certificat **.p12** exporté précédemment ainsi que le mot de passe du certificat. Assurez-vous de choisir si vous souhaitez utiliser la **Production** (si vous souhaitez envoyer des notifications Push aux utilisateurs qui ont acheté votre application depuis le store) ou le service Push **Bac à sable (sandbox)** (au cours du développement).

    ![][5]

7.  Cliquez sur l'onglet **Tableau de bord** dans la partie supérieure, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

    ![][6]

Votre Notification Hub est configuré pour APNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

## <a name="connecting-app"></a>Connexion de votre application au concentrateur de notification

1.  Dans XCode, créez un projet iOS et sélectionnez le modèle **Single View Application**.

    ![][7]

2.  Sous **Targets**, cliquez sur le nom de votre projet, développez **Code Signing Identity**, puis sous **Debug**, sélectionnez le profil d'identité de signature du code. En outre, si vous utilisez une version plus récente de XCode, basculez les **Niveaux** de **Basic** à **All** et définissez la ligne **Provisioning Profile** sur le profil de mise à niveau.

    ![][8]

3.  Télécharger le Kit de développement logiciel (SDK) Azure Mobile pour iOS. Ouvrez le fichier. zip et faites glisser le dossier WindowsAzureMessaging.Framework dans le dossier Structure de votre projet XCode. Sélectionnez **Copy items in destination group's folder**, puis cliquez sur **OK**.

    ![][9]

4.  Dans le fichier AppDelegate.h, ajoutez l'instruction d'importation suivante :

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  Dans le fichier AppDelegate.m, ajoutez le code suivant dans la méthode `didFinishLaunchingWithOptions` :

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  Dans le même fichier, ajoutez la méthode suivante :

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

7.  *(facultatif)* À nouveau, dans le même fichier, ajoutez la méthode suivante pour afficher une **UIAlert** si la notification est reçue alors que l'application est active :

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

8.  Exécutez l'application sur votre appareil.

## <a name="send"></a>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications en utilisant les Notification Hubs à partir des serveurs principaux en utilisant l'[interface REST][interface REST]. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour obtenir un exemple expliquant comment envoyer des notifications à partir d'un serveur principal Azure Mobile Services intégré à Notification Hubs, voir **Prise en main des notifications Push dans Mobile Services** ([Service principal .NET][Service principal .NET] | [Service principal JavaScript][Service principal .NET]). Pour obtenir un exemple expliquant comment envoyer des notifications à l'aide d'API REST, voir **Utilisation de Notification Hubs depuis Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** puis **Projet...** puis, sous **Visual C#**, cliquez sur **Windows** et sur **Application console**, puis cliquez sur **OK**.

   	![][20]

	Un projet d'application console est créé.

2.  Dans le menu **Outils**, cliquez sur **Gestionnaire de package de bibliothèques**, puis sur **Console du Gestionnaire de package**.

    La Console du Gestionnaire de package apparaît.

3.  Dans la fenêtre de console, exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus

    Cela permet d'ajouter une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus][package NuGet WindowsAzure.ServiceBus].

4.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

5.  Dans la classe**Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs**. Remplacez aussi l'espace réservé de chaîne de connexion par la chaîne de connexion nommée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section « Configuration de votre concentrateur de notification ».

    > [WACOM.NOTE]Assurez-vous d'utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès Écouter n'est pas autorisée à envoyer des notifications.

6.  Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

7.  Appuyez sur la touche F5 pour exécuter l'application console.

    Vous devez recevoir une alerte sur votre appareil. Si vous utilisez le Wi-Fi, assurez-vous que votre connexion fonctionne.

Vous trouverez toutes les charges utiles possibles dans le [Guide de programmation des notifications locales et Push][Guide de programmation des notifications locales et Push] d'Apple.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils iOS. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs][Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation de Notification Hubs pour envoyer les dernières nouvelles][Utilisation de Notification Hubs pour envoyer les dernières nouvelles]. Découvrez plus en détail comment utiliser Notification Hubs dans [Recommandations relatives à Notification Hubs][Recommandations relatives à Notification Hubs].

 
 


  [Génération de la demande de signature de certificat]: #certificates
  [Inscription de votre application et activation des notifications Push]: #register
  [Création d'un profil de mise en service pour l'application]: #profile
  [Configuration de votre concentrateur de notification]: #configure-hub
  [Connexion de votre application au concentrateur de notification]: #connecting-app
  [Envoi de notifications à partir de votre serveur principal]: #send
  [XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [interface REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx
  [Service principal .NET]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /fr-fr/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /fr-fr/documentation/articles/notification-hubs-php-backend-how-to/
  [package NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Guide de programmation des notifications locales et Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Utilisation de Notification Hubs pour envoyer les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
