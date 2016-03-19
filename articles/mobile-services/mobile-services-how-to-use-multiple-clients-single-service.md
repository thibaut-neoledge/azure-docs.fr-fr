<properties
	pageTitle="Comment utiliser plusieurs clients avec un seul backend de service mobile | Azure Mobile Services"
	description="Découvrez comment utiliser un seul backend de service mobile à partir de plusieurs applications clientes qui ciblent différentes plateformes mobiles."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>
<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="glenga"/>

# Prise en charge de plusieurs plateformes d'appareil à partir d'un seul service mobile

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


L'un des principaux avantages associés à l'utilisation d'Azure Mobile Services dans le développement de vos applications mobiles est la possibilité d'utiliser un seul service backend qui prend en charge votre application sur plusieurs plateformes clientes. Mobile Services fournit des bibliothèques clientes natives pour les principales plateformes d'appareil, ce qui facilite le développement d'applications à l'aide d'un seul service backend et des outils de développement interplateforme. Cette rubrique présente les aspects liés à l’exécution de votre application sur plusieurs plateformes clientes tout en utilisant un seul service mobile principal.

##<a id="push"></a>Notifications Push interplateforme

Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à vos applications clientes sur les principales plateformes d'appareil. Notification Hubs fournit une infrastructure cohérente et unifiée pour créer et gérer les inscriptions des appareils et pour envoyer des notifications Push interplateforme. Notification Hubs prend en charge l'envoi de notifications Push en utilisant les services suivants de notification propres aux plateformes :

+ Service de notification Push Apple (APN, Apple Push Notification) pour les applications iOS
+ Service Google Cloud Messaging (GCM) pour les applications Android
+ Services de notifications Push Windows (WNS) pour les applications Windows Store, Windows Phone 8.1 Store et Windows universelles
+ Service de notifications Push Microsoft (MPNS) pour les applications Windows Phone Silverlight

Pour plus d'informations, consultez la page [Azure Notification Hubs].

Des inscriptions de clients sont créées à l'aide de la fonction d'inscription de la bibliothèque cliente Mobile Services spécifique aux plateformes ou des API REST de Mobile Services. Notification Hubs prend en charge deux types d'inscriptions d'appareils :

+ **Inscription native**<br/>Les inscriptions natives sont adaptées au service de notifications Push propre aux plateformes. Lors de l'envoi de notifications vers des appareils inscrits à l'aide de l'inscription native, vous devez appeler des API propres aux plateformes dans votre service mobile. Pour envoyer une notification à des appareils sur plusieurs plateformes, vous devez utiliser plusieurs appels propres aux plateformes.

+ **Inscription de modèle**<br/>Notification Hubs prend également en charge les inscriptions de modèle propres aux plateformes. En utilisant des inscriptions de modèle, vous pouvez utiliser un seul appel d'API pour envoyer une notification à votre application exécutée sur n'importe quelle plateforme enregistrée. Pour plus d'informations, consultez la page [Envoi de notifications interplateforme aux utilisateurs].

Les tables des sections suivants renvoient vers les didacticiels propres aux clients, qui montrent comment implémenter des notifications Push à partir de services mobiles de backends .NET et JavaScript.

###Backend .NET

Dans un service mobile de backend .NET, vous envoyez des notifications en appelant la méthode [SendAsync] sur l'objet [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) obtenu depuis la propriété [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). La notification Push envoyée (native ou de modèle) dépend de l'objet spécifique dérivé de [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) transmis à la méthode [SendAsync], comme indiqué dans la table suivante :

|Plateforme |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) | MPNS
|-----|-----|----|----|-----|
|Native|[Message Push Apple](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[Message Push Google](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[Message Push Windows](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [Message Push MPNS](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

Le code suivant envoie une notification Push depuis un backend .NET vers toutes les inscriptions d'appareils iOS et Windows Store :

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

	// Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Pour obtenir des exemples d'envoi de notifications Push vers d'autres plateformes clientes natives, cliquez sur les liens des plateformes dans l'en-tête de la table ci-dessus.

Lorsque vous utilisez des inscriptions clientes de modèle plutôt que des inscriptions clientes natives, vous pouvez envoyer la même notification par un appel unique vers[SendAsync], en fournissant un objet [TemplatePushMessage] comme suit :

	// Create a new template message and add the 'message' parameter.
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###Backend JavaScript

Dans un backend JavaScript, vous pouvez envoyer des notifications en envoyant la méthode **send** sur l'objet propre à la plateforme obtenu à partir de l'[objet push] global, comme le montre la table suivante :

|Plateforme |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Native|[objet apns](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[objet gcm](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[objet wns](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [objet mpns](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

Le code suivant envoie une notification Push à toutes les inscriptions Android et Windows Phone :

	// Define a push notification for GCM.
	var gcmPayload =
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices.
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

Pour obtenir des exemples d'envoi de notifications Push vers d'autres plateformes clientes natives, cliquez sur les liens des plateformes dans l'en-tête de la table ci-dessus.

Lorsque vous utilisez des inscriptions clientes de modèle plutôt que des inscriptions clientes natives, vous pouvez envoyer la même notification par un appel unique vers la fonction **envoyer** de l'[objet push] global, en fournissant une charge utile de messages de modèle, comme suit :

	// Create a new template message with the 'message' parameter.
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>Développement d'applications interplateforme
Le développement d'applications natives destinées à toutes les principales plateformes d'appareil mobile nécessite que vous (ou votre organisation) soyez spécialisés au moins dans les langages de programmation Objective-C, Java, C# ou JavaScript. En raison de l'étendue du développement de ces plateformes disparates, certains développeurs choisissent une expérience entièrement basée sur les navigateurs Web pour leurs applications. Toutefois, ces expériences basées sur le Web ne peuvent accéder à la plupart des ressources natives permettant de fournir une expérience riche à laquelle les utilisateurs s'attendent sur leurs appareils mobiles.

Des outils interplateforme sont disponibles et fournissent une expérience native plus riche sur un appareil mobile, tout en partageant une base de code unique, en général JavaScript. Mobile Services vous permet de créer et de gérer facilement un service backend pour des plateformes de développement d'applications interplateforme, en fournissant des didacticiels de démarrage rapide pour les plateformes de développement suivantes :

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator vous permet d'utiliser JavaScript pour développer une application unique compilée pour fonctionner facilement sur toutes les plateformes d'appareil mobile. Cet outil permet de fournir à l'utilisateur une expérience riche via l'interface utilisateur, d'accéder à toutes les ressources natives d'appareil et d'optimiser les performances de l'application native. Pour plus d'informations, consultez la page [Didacticiel d'Appcelerator][Appcelerator].

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (créée dans le cadre du projet Apache Cordova) est une infrastructure open source gratuite qui vous permet d'utiliser des modèles web d'API, HTML et JavaScript normalisés pour développer une application unique fonctionnant sur les appareils Android, iOS et Windows. PhoneGap fournit une interface utilisateur dotée d'un affichage Web, mais dont l'expérience utilisateur est améliorée grâce à l'accès à des ressources natives sur l'appareil, comme les notifications Push, l'accéléromètre, l'appareil photo, la géolocalisation et le navigateur intégré à l'application. Pour plus d'informations, consultez la page [Didacticiel de démarrage rapide de PhoneGap][PhoneGap].

	Visual Studio vous permet désormais de créer des applications Cordova interplateforme en utilisant l'extension Multi-Device Hybrid Apps (applications hybrides multi-appareils) pour Visual Studio, un logiciel préliminaire. Pour plus d'informations, consultez la page [Prise en main des applications hybrides multi-appareils utilisant HTML et JavaScript](http://msdn.microsoft.com/library/dn771545.aspx).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch fournit un ensemble de commandes optimisées pour les écrans tactiles, qui permet de créer une expérience presque native sur un large éventail d'appareils à partir d'une base de code HTML et JavaScript unique. Sencha Touch peut être utilisé avec des bibliothèques PhoneGap ou Cordova pour permettre aux utilisateurs d'accéder à des ressources natives d'appareil. Pour plus d'informations, consultez la page [Didacticiel de démarrage rapide de Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin vous permet de créer des applications complètement natives pour les appareils iOS et Android, dotées d'une interface utilisateur entièrement native et d'un accès à toutes les ressources d'appareil. Les applications Xamarin sont codées à l'aide du langage C#, et non des langages Objective-C et Java. Ceci permet aux développeurs .NET de publier des applications vers iOS et Android et de partager le code depuis des projets Windows. Xamarin fournit une expérience utilisateur entièrement native sur les appareils iOS et Android à l'aide du code C#. Ceci vous permet de réutiliser certains de vos codes Mobile Services provenant d'applications Windows sur des appareils iOS et Android. Pour plus d’informations, consultez la page [Développement Xamarin](#xamarin) ci-dessous.


<!-- URLs -->
[Azure Notification Hubs]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Envoi de notifications interplateforme aux utilisateurs]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[objet push]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!---HONumber=AcomDC_1210_2015-->