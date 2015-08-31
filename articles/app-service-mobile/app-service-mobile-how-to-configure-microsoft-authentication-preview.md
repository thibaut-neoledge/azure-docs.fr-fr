<properties
	pageTitle="Comment configurer l'authentification par compte Microsoft pour votre application App Services"
	description="Découvrez comment configurer l'authentification par compte Microsoft pour votre application App Services."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Comment configurer votre application pour utiliser une connexion par compte Microsoft

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Cette rubrique montre comment configurer Azure Mobile Apps pour utiliser un compte Microsoft comme fournisseur d’authentification.

## <a name="register"> </a>Inscrivez votre application avec un compte Microsoft

1. Connectez-vous au [portail de gestion Azure] et accédez à votre application Mobile App.

2. Cliquez sur **Paramètres**, **Authentification utilisateur**, puis cliquez sur **Compte Microsoft**. Copiez l’**URL de redirection**. Vous l’utiliserez pour configurer une nouvelle application pour votre compte Microsoft.

3. Accédez à la page [Mes applications] dans le Centre des développeurs de compte Microsoft, puis connectez-vous avec votre compte Microsoft si nécessaire.

4. Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

5. Cliquez sur **Paramètres de l’API**. Sélectionnez **Oui** pour **Application cliente mobile ou de bureau**. Dans le champ **URL de redirection**, entrez l’**URL de redirection** que vous avez copiée précédemment. Il s’agit de la passerelle d’application Mobile App suivie de _/signin-microsoft_. Par exemple, `https://contosogateway.azurewebsites.net/signin-microsoft`. Assurez-vous d'utiliser le schéma HTTPS. Après avoir entré l’URL de redirection, cliquez sur **Enregistrer**.

	![][0]

	>[AZURE.NOTE]Pour l’inscription d’une application de compte Microsoft existante, vous devrez peut-être d’abord activer **Sécurité de redirection améliorée**.

4. Cliquez sur **Paramètres d'application** et notez les valeurs de l'**ID du client** et de la **Clé secrète client**.

    > [AZURE.NOTE]La clé secrète client est une information d'identification de sécurité importante. Ne partagez la clé secrète client avec personne et ne la distribuez pas dans une application cliente.


## <a name="secrets"> </a>Ajout des informations de compte Microsoft à votre application Mobile App

1. Dans le [portail de gestion Azure], dans le panneau des paramètres du compte Microsoft pour votre application Mobile App, collez les valeurs d’identifiant client et de code secret du client que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous êtes maintenant prêt à utiliser un compte Microsoft pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portail de gestion Azure]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->