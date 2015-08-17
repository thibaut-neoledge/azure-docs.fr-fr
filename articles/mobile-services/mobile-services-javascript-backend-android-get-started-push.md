
<properties 
	pageTitle="Prise en main des notifications push (Android JavaScript) | Centre de développement mobile" 
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Android JavaScript." 
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
	ms.date="06/01/2015" 
	ms.author="ricksal"/>


# Ajout de notifications push à votre application Android Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## Résumé

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android à l'aide de Google Cloud Messaging (« GCM »). Vous allez ajouter des notifications Push au projet de démarrage rapide nécessaire pour ce didacticiel. Les notifications Push sont activées à l'aide du concentrateur de notification Azure qui est inclus dans votre service mobile. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

<!-- [AZURE.NOTE] If you would like to see the source code of the completed app, go <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStartedWithPush/AndroidStudio" target="_blank">here</a>.
-->


## Composants requis

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Activation de Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configuration de Mobile Services pour l'envoi de requêtes Push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>Ajout de notifications Push à votre application



L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur.

###Ajout de services Google Play au projet

[AZURE.INCLUDE [Ajout de services Google Play](../../includes/mobile-services-add-Google-play-services.md)]

###Ajout de code

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Mise à jour du script d'insertion inscrit dans le portail de gestion

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="test"></a>Test des notifications Push dans votre application

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

###Configuration de l'émulateur Android pour le test

Quand vous exécutez cette application dans l’émulateur, veillez à utiliser un appareil virtuel Android (AVD) qui prend en charge les API Google.

1. À l'extrémité droite de la barre d'outils, sélectionnez le gestionnaire de périphériques virtuels Android, sélectionnez votre appareil, puis cliquez sur l'icône de modification à droite.

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Sélectionnez **Modifier** dans la ligne de description de l'appareil, sélectionnez **API Google**, puis cliquez sur OK.

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Cela permet de cibler l'AVD pour utiliser les API Google.

###Exécution du test

1. Depuis l'élément de menu **Exécuter**, cliquez sur **Exécuter l'application** pour démarrer le projet.

2. Dans l'application, tapez un texte explicite, comme _Nouvelle tâche Mobile Services_ puis cliquez sur le bouton **Ajouter**.

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. Balayez l'écran de haut en bas pour ouvrir le tiroir de notification de l'appareil et afficher la notification.


Vous avez terminé ce didacticiel.

## Résolution de problèmes

### Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [Vérification du Kit de développement logiciel (SDK)](../../includes/mobile-services-verify-android-sdk-version.md)]


## Versions de code antérieures

Si vous souhaitez consulter la version Eclipse de ce didacticiel, allez à [Prise en main des notifications Push (Eclipse)].


<!--
To see a completed version of the source code in an Eclipse project, go <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/Android">here</a>.
-->


## <a name="next-steps"> </a>Étapes suivantes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données] <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs] <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Instructions de dépannage et de débogage des solutions Notification Hubs.

* [Utilisation de la bibliothèque cliente Android pour Mobile Services] <br/>En savoir plus sur l'utilisation de Mobile Services avec Android.

* [Référence de script serveur Mobile Services] <br/>En savoir plus sur l'implémentation de logique métier dans votre service mobile.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Prise en main des notifications Push (Eclipse)]: mobile-services-javascript-backend-android-get-started-push-EC.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[Prise en main des données]: mobile-services-android-get-started-data.md
[Prise en main de l'authentification]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Configuration du Kit de développement logiciel (SDK) des services Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[Utilisation de la bibliothèque cliente Android pour Mobile Services]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[Présentation de Notification Hubs]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=August15_HO6-->