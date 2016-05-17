<properties
	pageTitle="Ajout de notifications Push à votre application Xamarin.Android | Azure App Service"
	description="Découvrez comment utiliser Azure App Service et Azure Notification Hubs pour envoyer des notifications Push à votre application Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="glenga"/>

# Ajouter des notifications push à votre application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Vue d'ensemble

Dans ce didacticiel, vous ajoutez des notifications Push au projet [Créer une application Xamarin.Android] afin qu’une notification Push soit envoyée chaque fois qu’un enregistrement est inséré. Ce didacticiel est basé sur le didacticiel [Créer une application Xamarin.Android], que vous devez effectuer en premier. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Composants requis

Ce didacticiel requiert les éléments suivants :

+ Un compte Google actif. Vous pouvez vous connecter à un compte Google sur [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

+ Le composant client [Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/). Vous ajouterez ce composant au cours du didacticiel.

##<a name="create-hub"></a>Créer un hub de notification

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>Activation de Google Cloud Messaging

[AZURE.INCLUDE [Activation de GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Configurer le backend d’application mobile pour l’envoi de demandes push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>Mettre à jour le projet de serveur pour l’envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurer le projet client pour les notifications push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Ajouter le code des notifications push à votre application

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Tester les notifications push dans votre application

Vous pouvez tester l’application à l’aide d’un appareil virtuel dans l’émulateur. Des étapes de configuration supplémentaires sont requises lors de l’exécution sur un émulateur.

1. Assurez-vous de procéder au déploiement ou au débogage sur un périphérique virtuel sur lequel les API Google sont définis comme cible, comme indiqué dans le Gestionnaire d’appareil virtuel Android (AVD).

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Ajoutez un compte Google à l’appareil Android en cliquant sur **Applications** > **Paramètres** > **Ajouter un compte**, puis suivez les invites pour utiliser Ajouter un compte Google existant à l’appareil pour en créer un nouveau.

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Exécutez normalement l’application todolist et insérez un nouvel élément todo. Cette fois, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le tiroir de notification pour afficher le texte intégral de la notification.

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quickstart]: app-service-mobile-xamarin-android-get-started.md
[Créer une application Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0504_2016-->