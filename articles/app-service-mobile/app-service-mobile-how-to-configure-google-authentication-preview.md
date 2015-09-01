<properties
	pageTitle="Comment configurer l'authentification Google pour votre application App Services"
	description="Découvrez comment configurer l'authentification Google pour votre application App Services."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
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

# Comment configurer votre application pour utiliser une connexion Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Cette rubrique montre comment configurer Mobile Apps pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Google avec une adresse de messagerie vérifiée. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Inscription de votre application avec Google


1. Connectez-vous au [portail de gestion Azure] et accédez à votre application Mobile App. Copiez votre **URL**. Vous vous en servirez ultérieurement avec votre application Google.
 
2. Cliquez sur **Paramètres**, **Authentification utilisateur**, puis cliquez sur **Google**. Copiez l’**URI de redirection**. Vous l’utiliserez pour configurer votre application Google.

3. Rendez-vous sur le site web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API Google</a>, connectez-vous avec votre compte Google, cliquez sur **Create Project**, entrez un **Project name**, puis cliquez sur **Create**.

4. Dans la barre de navigation de gauche, cliquez sur **API & Auth**. Ensuite, cliquez sur **Consent screen**. Sélectionnez votre **Email address** et entrez un **Product name**. Cliquez ensuite sur **Save**.

5. De plus, sous **API & Auth**, sélectionnez **API** et activez l’**Google+ API**. Elle se trouve sous **Social APIs**. Vous pouvez également rechercher **Google+ API**.

6. Une nouvelle fois, sous **API & Auth**, sélectionnez **Credentials**, puis **Create new client ID**.

7. Sélectionnez **Web Application**. Collez l’**URL** que vous avez copiée précédemment dans **Authorized JavaScript Origins**, puis remplacez l’URL générée dans **Authorized Redirect URI** par l’**URI de redirection** d’application Mobile App que vous avez copiée précédemment. Cet URI correspond à la passerelle d’application Mobile App suivie du chemin _/signin-google_. Par exemple, `https://contosogateway.azurewebsites.net/signin-google`. Assurez-vous d'utiliser le schéma HTTPS. Ensuite, cliquez sur **Create client ID**.

     ![][0]

8. Dans l’écran suivant, sous **Identifiant client de l’application Web**, notez les valeurs définies pour **Identifiant client** et **Code secret du client**.

    > [AZURE.IMPORTANT]La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.


## <a name="secrets"> </a>Ajout des informations Google à votre application Mobile App

7. Dans le [portail de gestion Azure], dans le panneau des paramètres Google pour votre application Mobile App, collez les valeurs d’identifiant client et de code secret du client que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

     ![][1]


Vous êtes maintenant prêt à utiliser Google pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portail de gestion Azure]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->