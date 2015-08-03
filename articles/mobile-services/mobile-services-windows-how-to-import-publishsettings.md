<properties 
	pageTitle="Importation du fichier de paramètres de publication dans Visual Studio 2013 | Mobile Services" 
	description="Découvrez comment importer un fichier de paramètres de publication d'abonnement pour votre application Azure Mobile Services dans Visual Studio 2013." 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Importation du fichier de paramètres de publication de votre abonnement dans Visual Studio 2013

Avant de créer le service mobile, vous devez importer le fichier de paramètres de publication de votre abonnement Azure dans Visual Studio. Cela permet à Visual Studio de se connecter à Azure en votre nom.

>[AZURE.NOTE]Depuis Visual Studio 2013 Update 2, aucun fichier de paramètres de publication n'est requis. Visual Studio est en mesure de se connecter directement à Azure à l'aide des informations d'identification que vous indiquez.

1. Dans Visual Studio 2013, ouvrez l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Ajouter**, puis sur **Service connecté...**. 

	![ajouter un service connecté](./media/mobile-services-windows-how-to-import-publishsettings/mobile-add-connected-service.png)

2. Dans la boîte de dialogue Gestionnaire des services, cliquez sur **Créer un service...**, puis sélectionnez **&lt;Importer...&gt;>** sous **Abonnement** dans la boîte de dialogue Créer un service mobile.

	![créer un service mobile à partir de VS 2013](./media/mobile-services-windows-how-to-import-publishsettings/mobile-create-service-from-vs2013.png)

3. Dans Import Azure Subscriptions, cliquez sur **Télécharger le fichier d’abonnement**, connectez-vous à votre compte Azure (si nécessaire), puis cliquez sur **Enregistrer** lorsque le navigateur vous invite à enregistrer le fichier.

	![télécharger le fichier d’abonnement dans VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]La fenêtre de connexion s’affiche dans le navigateur qui peut se trouver derrière la fenêtre Visual Studio. N’oubliez pas de noter l’emplacement où vous avez enregistré le fichier .publishsettings téléchargé. Vous pouvez ignorer cette étape si votre projet est déjà connecté à votre abonnement Azure.

4. Cliquez sur **Parcourir**, recherchez l’emplacement où vous avez enregistré le fichier .publishsettings, sélectionnez ce dernier, puis cliquez sur **Ouvrir** et sur **Importer**.

	![importer l’abonnement dans VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription-2.png)

	Visual Studio importe les données nécessaires pour se connecter à votre abonnement Azure. Si votre abonnement dispose déjà d'un ou plusieurs services mobiles, leur nom s'affiche.

	> [AZURE.NOTE]Après l’importation des paramètres de publication, pensez à supprimer le fichier .publishsettings téléchargé, car il contient des informations pouvant être utilisées par d’autres personnes pour accéder à votre compte. Mettez le fichier en lieu sûr si vous souhaitez le conserver afin de l’utiliser pour d’autres projets d’applications connectées.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->