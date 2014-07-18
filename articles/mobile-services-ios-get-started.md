<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de Mobile Services
================================

[Windows Store](/en-us/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-android-get-started "Android") [HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[.NET](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ ".NET") | [JavaScript](/en-us/documentation/articles/mobile-services-ios-get-started/ "JavaScript")

Ce didacticiel présente l'ajout d'un service principal cloud à une application iOS à l'aide d'Azure Mobile Services.

Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel.

[regarder le didacticiel](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) [Lire la vidéo](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) 09:38

Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la [version principale .NET] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][0]

XCode 4.5 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F).

Création d'un service mobile
----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Création d'une applicationCréation d'une application iOS
--------------------------------------------------------

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application iOS connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **iOS** sous **Choisissez une plateforme** et développez **Créer une application iOS**.

	![][6]

	Cela affiche les trois étapes faciles pour créer une application iOS connectée à votre service mobile.

	![][7]

3.  Si vous ne l'avez pas encore fait, téléchargez et installez [Xcode] v4.4 ou une version ultérieure.

4.  Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run your app**, cliquez sur **Télécharger**.

	Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services iOS. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

Exécution de votre nouvelle application iOS
-------------------------------------------

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![](./media/mobile-services-ios-get-started/mobile-data-browse.png)

## Étapes suivantes 
Maintenant que vous avez effectué le démarrage rapide, découvrez comment effectuer d'autres tâches importantes dans Mobile Services : 
*	[Prise en main des données] 

	En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services. 
*	[Prise en main de l'authentification] 
	
	En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité. 
*	[Prise en main des notifications Push] 
	
	En savoir plus sur l'envoi d'une notification Push très basique sur votre application. 

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Prise en main des données]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Prise en main de l'authentification]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Prise en main des notifications Push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET backend version]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
