<properties linkid="" urlDisplayName="" pageTitle="How to use multiple clients with a single mobile service backend | Azure Mobile" metaKeywords="mobile services single backend" description="Learn how to use a single mobile service back-end from multiple client apps that target different mobile platforms, including Windows Store and Windows Phone." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="" manager="" editor="mollybos" />

# Prise en charge de plusieurs plateformes d'appareil à partir d'un seul service mobile

L'un des principaux avantages associés à l'utilisation d'Azure Mobile Services dans le développement de vos applications mobiles est la possibilité d'utiliser un seul service principal qui prend en charge votre application sur plusieurs plateformes clientes. Mobile Services fournit des bibliothèques clientes natives pour les principales plateformes d'appareil, ce qui facilite le développement d’applications à l'aide d'un seul service principal et des outils de développement interplateforme. Cette rubrique présente les aspects suivants liés à l'exécution de votre application sur plusieurs plateformes clientes tout en utilisant un seul service mobile principal :

-   [Notifications Push interplateforme][]
-   [Développement d'applications interplateforme][]
-   [Partage de code dans les projets Visual Studio][]

Pour plus d'informations sur Mobile Services, consultez la rubrique [Centre de développement Mobile Services][].

## <span id="push"></span></a>Notifications Push interplateforme

Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à vos applications clientes sur les principales plateformes d'appareil. Notification Hubs fournit une infrastructure cohérente et unifiée pour créer et gérer les inscriptions des appareils et pour envoyer des notifications Push interplateforme. Notification Hubs prend en charge l'envoi de notifications Push en utilisant les services suivants de notification propres aux plateformes :

-   Service de notification Push Apple (APN, Apple Push Notification) pour les applications iOS
-   Service Google Cloud Messaging (GCM) pour les applications Android
-   Services de notifications Push Windows (WNS) pour les applications Windows Store, Windows Phone 8.1 Store et Windows universelles
-   Service de notifications Push Microsoft (MPNS) pour les applications Windows Phone Silverlight

> [WACOM.NOTE]Notification Hubs ne prend pas en charge actuellement l'utilisation du service de notification Windows (WNS) pour envoyer des notifications Push vers des applications Windows Phone Silverlight 8.1. Vous devez utiliser les services de notifications Push Microsoft (MPNS) pour envoyer des notifications vers les applications Silverlight et Windows Phone 7.0 et 8.0.

Pour plus d'informations, consultez la page [Azure Notification Hubs][].

Des inscriptions de clients sont créées à l'aide de la fonction d'inscription de la bibliothèque cliente Mobile Services spécifique aux plateformes ou des API REST de Mobile Services. Notification Hubs prend en charge deux types d'inscriptions d'appareils :

-   **Inscription native**
    Les inscriptions natives sont adaptées au service de notifications Push propre aux plateformes. Lors de l'envoi de notifications vers des appareils inscrits à l'aide de l'inscription native, vous devez appeler des API propres aux plateformes dans votre service mobile. Pour envoyer une notification à des appareils sur plusieurs plateformes, vous devez utiliser plusieurs appels propres aux plateformes.

-   **Inscription de modèle**
    Notification Hubs prend également en charge les inscriptions de modèle propres aux plateformes. En utilisant des inscriptions de modèle, vous pouvez utiliser un seul appel d'API pour envoyer une notification à votre application exécutée sur n'importe quelle plateforme enregistrée. Pour plus d'informations, consultez la page [Envoi de notifications interplateforme aux utilisateurs][].

> [WACOM.NOTE]Une erreur se produit lors de la tentative d'envoi d'un message à une plateforme d'appareil native pour laquelle il n'existe aucune inscription d'appareil. Cette erreur ne se produit pas lorsque vous envoyez des notifications de modèle.

Les tables des sections suivants renvoient vers les didacticiels propres aux clients, qui montrent comment implémenter des notifications Push à partir de services mobiles principaux .NET et JavaScript.

### Service principal .NET

Dans un service mobile principal .NET, vous envoyez des notifications en appelant la méthode [SendAsync][] sur l'objet [PushClient][] obtenu depuis la propriété [ApiServices.Push][]. La notification Push envoyée (native ou de modèle) dépend de l'objet spécifique dérivé de [IPushMessage][] transmis à la méthode [SendAsync][], comme indiqué dans la table suivante :

| Plateforme | [APNS][]               | [GCM][]                 | [WNS][]                  | [MPNS][]              |
|------------|------------------------|-------------------------|--------------------------|-----------------------|
| Native     | [Message Push Apple][] | [Message Push Google][] | [Message Push Windows][] | [Message Push MPNS][] |

Le code suivant envoie une notification Push depuis un service principal .NET vers toutes les inscriptions d'appareils iOS et Windows Store :

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

Lorsque vous utilisez des inscriptions clientes de modèle plutôt que des inscriptions clientes natives, vous pouvez envoyer la même notification par un appel unique vers[SendAsync][], en fournissant un objet [TemplatePushMessage][] comme suit :

    // Create a new template message and add the 'message' parameter.    
    var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

    // Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload); 

### Service principal JavaScript

Dans un service mobile principal JavaScript, vous pouvez envoyer des notifications en envoyant la méthode **send** sur l'objet propre à la plateforme obtenu à partir de l'[objet push][] global, comme le montre la table suivante :

| Plateforme | [APNS][1]      | [GCM][2]      | [WNS][3]      | [MPNS][4]      |
|------------|----------------|---------------|---------------|----------------|
| Native     | [objet apns][] | [objet gcm][] | [objet wns][] | [objet mpns][] |

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

Lorsque vous utilisez des inscriptions clientes de modèle plutôt que des inscriptions clientes natives, vous pouvez envoyer la même notification par un appel unique vers la fonction **envoyer** de l'[objet push][] global, en fournissant une charge utile de messages de modèle, comme suit :

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

## <span id="xplat-app-dev"></span></a>Développement d'applications interplateforme

Le développement d'applications natives destinées à toutes les principales plateformes d'appareil mobile nécessite que vous (ou votre organisation) soyez spécialisés au moins dans les langages de programmation Objective-C, Java, C# ou JavaScript. En raison de l'étendue du développement de ces plateformes disparates, certains développeurs choisissent une expérience entièrement basée sur les navigateurs Web pour leurs applications. Toutefois, ces expériences basées sur le Web ne peuvent accéder à la plupart des ressources natives permettant de fournir une expérience riche à laquelle les utilisateurs s'attendent sur leurs appareils mobiles.

Des outils interplateforme sont disponibles et fournissent une expérience native plus riche sur un appareil mobile, tout en partageant une base de code unique, en général JavaScript. Mobile Services vous permet de créer et de gérer facilement un service principal pour des plateformes de développement d'applications interplateforme, en fournissant des didacticiels de démarrage rapide pour les plateformes de développement suivantes :

-   [**Appcelerator**][]
    Appcelerator vous permet d'utiliser JavaScript pour développer une application unique compilée pour fonctionner facilement sur toutes les plateformes d'appareil mobile. Cet outil permet de fournir à l'utilisateur une expérience riche via l'interface utilisateur, d'accéder à toutes les ressources natives d'appareil et d'optimiser les performances de l'application native. Pour plus d'informations, consultez la page [Didacticiel d'Appcelerator][].

-   [**PhoneGap**][]**/**[**Cordova**][]
    PhoneGap (créée dans le cadre du projet Apache Cordova) est une infrastructure open source gratuite qui vous permet d'utiliser des modèles Web d'API, HTML et JavaScript normalisés pour développer une application unique fonctionnant sur les appareils Android, iOS et Windows. PhoneGap fournit une interface utilisateur dotée d'un affichage Web, mais dont l'expérience utilisateur est améliorée grâce à l'accès à des ressources natives sur l'appareil, comme les notifications Push, l'accéléromètre, l'appareil photo, la géolocalisation et le navigateur intégré à l'application. Pour plus d'informations, consultez la page [Didacticiel de démarrage rapide de PhoneGap][].

    Visual Studio vous permet désormais de créer des applications Cordova interplateforme en utilisant l'extension Multi-Device Hybrid Apps (applications hybrides multi-appareils) pour Visual Studio, un logiciel préliminaire. Pour plus d'informations, consultez la page [Prise en main des applications hybrides multi-appareils utilisant HTML et JavaScript][].

-   [**Sencha Touch**][]
    Sencha Touch fournit un ensemble de commandes optimisées pour les écrans tactiles, qui permet de créer une expérience presque native sur un large éventail d'appareils à partir d'une base de code HTML et JavaScript unique. Sencha Touch peut être utilisé avec des bibliothèques PhoneGap ou Cordova pour permettre aux utilisateurs d'accéder à des ressources natives d'appareil. Pour plus d'informations, consultez la page [Didacticiel de démarrage rapide de Sencha Touch][].

-   [**Xamarin**][]
    Xamarin vous permet de créer des applications complètement natives pour les appareils iOS et Android, dotées d'une interface utilisateur entièrement native et d'un accès à toutes les ressources d'appareil. Les applications Xamarin sont codées à l'aide du langage C#, et non des langages Objective-C et Java. Ceci permet aux développeurs .NET de publier des applications vers iOS et Android et de partager le code depuis des projets Windows. Xamarin fournit une expérience utilisateur entièrement native sur les appareils iOS et Android à l'aide du code C#. Ceci vous permet de réutiliser certains de vos codes Mobile Services provenant d'applications Windows sur des appareils iOS et Android. Pour plus d’informations, consultez la page [Développement Xamarin][] ci-dessous.

    Vous pouvez créer des applications Xamarin dans Xamarin Studio ou Visual Studio 2013. Pour plus d'informations, consultez la page [Développement interplateforme dans Visual Studio][].

## <span id="shared-vs"></span></a>Partage et réutilisation de code dans des projets Visual Studio

Mobile Services intègre une bibliothèque cliente .NET. Il s'agit d'une bibliothèque de classes portable .NET Framework qui prend en charge le développement sur toutes les plateformes Windows. Pour plus d'informations, consultez la rubrique [Prise en main d'un client .NET avec Mobile Services][]. Cette méthode permet de réutiliser facilement le même code Mobile Services, comme pour l'accès aux données ou l'authentification, dans plusieurs projets C#.

Grâce au partage et à la réutilisation de votre code C# dans vos différents projets, vous pouvez implémenter le modèle MVVM (Model-View-View Model) et partager les assemblys entre plusieurs plateformes. Vous pouvez implémenter le modèle et afficher les classes du modèle dans un projet de bibliothèque de classes portable dans Visual Studio, puis créer des vues personnalisées pour différentes plateformes. Le code du modèle, commun aux différentes plateformes, peut (par exemple) récupérer les données d'une source telle que votre service mobile d'une manière indépendante de la plateforme. La bibliothèque MSDN fournit une [vue d'ensemble et un exemple][], une discussion sur les [différences entre les API][], un exemple d'[utilisation de bibliothèques de classes portables pour implémenter le modèle MVVM][], d'autres [orientations normatives][] et des informations sur la [gestion des ressources][] dans les projets de bibliothèque de classes portable.

En plus de ces conseils généraux, Visual Studio met également à votre disposition des fonctions spécifiques pour réutiliser votre code Mobile Services dans plusieurs projets d'application cliente. Ces fonctions sont détaillées dans les sections suivantes. Pour plus d'informations générales sur l'utilisation de Visual Studio 2013 pour créer des applications interplateforme, consultez la page [Développement interplateforme dans Visual Studio][].

### Applications Windows universelles

Visual Studio 2013 Update 2 ajoute la prise en charge des projets d'applications Windows universelles. Les applications universelles sont des solutions qui incluent des projets pour Windows Store 8.1 et Windows Phone Store 8,1, et un projet de code partagé. Dans le cadre de ce type de projet, le code partagé est traité comme s'il faisait partie à la fois du projet Windows Store et du projet Windows Phone. Pour plus d'informations, consultez la page [Créer des applications Windows universelles pour tous les appareils Windows][]. Les applications Windows universelles peuvent être écrites en C#/XAML et JavaScript/HTML.

Par défaut, l'onglet de démarrage rapide du service mobile du [portail de gestion Azure][] génère une version d'application Windows universelle de l'exemple d'application TodoList pour commencer. Vous pouvez choisir de télécharger une version C#/XAML ou JavaScript/HTML du projet. Pour plus d'informations, consultez la page [Prise en main de Mobile Services][].

> [WACOM.NOTE]La version C# du projet d'application quickstart du portail partage la page MainPage.xaml.cs code-behind, mais n'utilise pas un modèle de vue. Pour obtenir un exemple de l'application TodoList en tant que projet d'application Windows universelle en C# qui utilise le modèle MVVM, consultez la page [Projet d'application Windows universelle pour Azure Mobile Services à l'aide du modèle MVVM][].

### <span id="xamarin"></span></a>Développement Xamarin

Vous pouvez tirer profit de votre expérience de développement Visual Studio et C# en utilisant Xamarin et Visual Studio ou Xamarin Studio pour développer des applications pour iOS et Android. Xamarin utilise une implémentation interplateforme de .NET Framework qui vous permet d'utiliser le code C# code pour développer des applications iOS et Android. En utilisant Xamarin, vous pouvez tirer profit du code existant de vos projets Windows qui utilise la bibliothèque cliente .NET de Mobile Services pour accéder à votre service mobile. Pour plus d'informations, consultez la page [Développement interplateforme dans Visual Studio][].

Pour commencer à créer des applications Xamarin qui utilisent Mobile Services, consultez les didacticiels de démarrage rapide de Xamarin ([iOS][] / [Android][]).

### Applications Windows Store et Windows Phone Silverlight

Dans Windows Phone 8.1, vous pouvez choisir d'utiliser le XAML basé sur Silverlight précédent pour développer des applications ou utiliser le XAML basé sur Windows Runtime, qui permet de développer des applications Windows universelles. Pour plus d'informations sur les applications Windows Phone 8.1 Silverlight et Windows Phone Store 8.1, consultez la section [L'avenir des développeurs Windows Phone 8][].

La bibliothèque cliente .NET de Mobile Services prend en charge les applications Windows Phone Store 8.1 et Windows Phone Silverlight 8.1. Du fait que les applications Windows Runtime et Windows Phone Silverlight ne peuvent être créées à partir d'un même projet, vous devez envisager la réutilisation du code comme expliquée dans les méthodes énoncées ci-dessus relatives à la bibliothèque de classes portable et au modèle MVVM.

> [WACOM.NOTE]Pour utiliser l'authentification unique du client avec un compte Microsoft dans des applications Windows Runtime et Windows Phone Silverlight, vous devez d'abord inscrire l'application Windows Runtime dans le tableau de bord Windows Store. En effet, une fois que vous créez une inscription Live Connect pour Windows Phone, vous ne pouvez pas en créer une pour Windows Store. Pour plus d'informations sur la marche à suivre, lisez la rubrique **Authentification unique de votre application Windows Store avec Live Connect** ([Windows Store][]/[Windows Phone][]).

<!-- URLs -->

  [Notifications Push interplateforme]: #push
  [Développement d'applications interplateforme]: #xplat-app-dev
  [Partage de code dans les projets Visual Studio]: #shared-vs
  [Centre de développement Mobile Services]: /fr-fr/documentation/services/mobile-services/
  [Azure Notification Hubs]: /fr-fr/develop/net/how-to-guides/service-bus-notification-hubs/
  [Envoi de notifications interplateforme aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [SendAsync]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
  [PushClient]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx
  [ApiServices.Push]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx
  [IPushMessage]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx
  [APNS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [GCM]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [WNS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [MPNS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Message Push Apple]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx
  [Message Push Google]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx
  [Message Push Windows]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx
  [Message Push MPNS]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx
  [TemplatePushMessage]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
  [objet push]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554217.aspx
  [1]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push
  [2]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push
  [3]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [4]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [objet apns]: http://msdn.microsoft.com/fr-fr/library/azure/jj839711.aspx
  [objet gcm]: http://msdn.microsoft.com/fr-fr/library/azure/dn126137.aspx
  [objet wns]: http://msdn.microsoft.com/fr-fr/library/azure/jj860484.aspx
  [objet mpns]: http://msdn.microsoft.com/fr-fr/library/azure/jj871025.aspx
  [**Appcelerator**]: http://go.microsoft.com/fwlink/p/?LinkId=509987
  [Didacticiel d'Appcelerator]: /fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [**PhoneGap**]: https://go.microsoft.com/fwLink/p/?LinkID=390707
  [**Cordova**]: http://cordova.apache.org/
  [Didacticiel de démarrage rapide de PhoneGap]: /fr-fr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/
  [Prise en main des applications hybrides multi-appareils utilisant HTML et JavaScript]: http://msdn.microsoft.com/fr-fr/library/dn771545.aspx
  [**Sencha Touch**]: http://go.microsoft.com/fwlink/p/?LinkId=509988
  [Didacticiel de démarrage rapide de Sencha Touch]: /fr-fr/documentation/articles/partner-sencha-mobile-services-get-started/
  [**Xamarin**]: https://go.microsoft.com/fwLink/p/?LinkID=330242
  [Développement Xamarin]: #xamarin
  [Développement interplateforme dans Visual Studio]: http://msdn.microsoft.com/fr-fr/library/dn771552.aspx
  [Prise en main d'un client .NET avec Mobile Services]: fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
  [vue d'ensemble et un exemple]: http://msdn.microsoft.com/fr-fr/library/gg597391(v=vs.110)
  [différences entre les API]: http://msdn.microsoft.com/fr-fr/library/gg597392(v=vs.110)
  [utilisation de bibliothèques de classes portables pour implémenter le modèle MVVM]: http://msdn.microsoft.com/fr-fr/library/hh563947(v=vs.110)
  [orientations normatives]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/jj714086(v=vs.105).aspx
  [gestion des ressources]: http://msdn.microsoft.com/fr-fr/library/hh871422(v=vs.110)
  [Créer des applications Windows universelles pour tous les appareils Windows]: http://go.microsoft.com/fwlink/p/?LinkId=509905
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
  [Projet d'application Windows universelle pour Azure Mobile Services à l'aide du modèle MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de
  [iOS]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started
  [Android]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started
  [L'avenir des développeurs Windows Phone 8]: http://msdn.microsoft.com/fr-fr/library/windows/apps/dn655121(v=vs.105).aspx
  [Windows Store]: /fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/single-sign-on-wp8/
