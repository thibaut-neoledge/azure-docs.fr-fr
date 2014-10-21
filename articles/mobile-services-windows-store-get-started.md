<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de Mobile Services
================================

<div class="dev-center-tutorial-selector sublanding">
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store" class="current">Windows Store</a>
	<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/fr-fr/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/fr-fr/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/fr-fr/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/fr-fr/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET backend">.NET backend</a> | 
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel présente l'ajout d'un service principal cloud à une application Windows Store à l'aide d'Azure Mobile Services.</p>

<p>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel. Dans la vidéo, après avoir présenté Mobile Services, Scott Guthrie décrit la création de votre premier service mobile et explique comment s'y connecter à partir d'une application Windows Store.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">10:08</span></div>
</div>

Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la version principale .NET de cette rubrique.

Voici une capture d'écran de l'application terminée :

![](./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png)

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Services pour les applications Windows Store.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

> Ce didacticiel requiert Visual Studio 2013. Pour vous connecter à une application Windows Store à l'aide de Visual Studio 2012, suivez la procédure de la rubrique [Prise en main des données dans Mobile Services à l'aide de Visual Studio 2012](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

Création d'un service mobile
----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Création d'une application Windows Store
----------------------------------------

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows Store connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

   	![][6]

   	Cette opération affiche les trois étapes faciles pour créer une application Windows Store connectée à votre service mobile.

   	![](./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png)

3.  Si ce n'est pas déjà fait, téléchargez et installez [Visual Studio 2013 Express pour Windows](http://go.microsoft.com/fwlink/?LinkId=257546) sur votre ordinateur local ou sur votre machine virtuelle.

4.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run your app**, sélectionnez une langue pour votre application, puis cliquez sur **Télécharger**.

   	Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

Exécution de votre application Windows
--------------------------------------

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier solution dans Visual Studio 2013 Express pour Windows.

2.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	![][10]

   	Cette opération envoie une requête POST au nouveau service mobile hébergé sur Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la deuxième colonne de l'application.

	> [WACOM.NOTE]Vous pouvez vérifier le code (se trouvant dans le fichier MainPage.xaml.cs (projet C\#/XAML) ou dans le fichier default.js (projet JavaScript/HTML)) permettant d'accéder au service mobile pour exécuter une requête et insérer des données.

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![][11]

   	Cette opération vous permet de parcourir les données insérées par l'application dans la table.

   	![][12]

Étapes suivantes
----------------

Maintenant que vous avez effectué le démarrage rapide, découvrez comment effectuer d'autres tâches importantes dans Mobile Services :

-   **Prise en main des données** ([C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet) / [JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js))
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   **Prise en main de l'authentification** ([C#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet) / [JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js))
    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   **Prise en main des notifications Push** ([C#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet) / [JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js))
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-windows-store-get-started/mobile-vs2013-project.png

[10]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[Get started with data JS]: /en-us/develop/mobile/tutorials/get-started-with-data-js
[Get started with authentication JS]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications JS]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[.NET backend version]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Get started with data in Mobile Services using Visual Studio 2012]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

