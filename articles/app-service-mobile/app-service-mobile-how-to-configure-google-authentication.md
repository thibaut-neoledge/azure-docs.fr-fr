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
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Comment configurer votre application pour utiliser une connexion Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Mobile Apps pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Google avec une adresse de messagerie vérifiée. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Connectez-vous au [portail de gestion Azure] et accédez à votre application Mobile App. Copiez votre **URL**. Vous vous en servirez ultérieurement avec votre application Google.
 
2. Cliquez sur **Paramètres**, **Authentification utilisateur**, puis cliquez sur **Google**. Copiez l'**URI de redirection**. Vous l’utiliserez pour configurer votre application Google.

3. Rendez-vous sur le site Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, saisissez un **Nom de projet**, puis cliquez sur **Créer**.

4. Dans la barre de navigation à gauche, cliquez sur **API et authentification**, puis sous **API sociales** cliquez sur **API Google+** > **Activer l'API**.

5. Cliquez sur **API et authentification** > **Informations d'identification** > **Écran de consentement OAuth**, puis sélectionnez votre **Adresse de messagerie**, entrez un **Nom de produit** et cliquez sur **Enregistrer**.

6. Dans l'onglet **Informations d'identification**, cliquez sur **Ajouter des informations d'identification** > **ID client OAuth 2.0**, puis sélectionnez **Application Web**.

7. Collez l'**URL** Mobile Apps que vous avez copiée précédemment dans **Origines JavaScript autorisées**, puis collez l'**URI de redirection** que vous avez copiée précédemment dans **URI de redirection autorisée**. L'URI de redirection correspond à la passerelle d'application Mobile App suivie du chemin _/signin-google_. Par exemple : `https://contosogateway.azurewebsites.net/signin-google`. Assurez-vous d'utiliser le schéma HTTPS. Cliquez ensuite sur **Créer**.

8. Sur l'écran suivant, notez les valeurs de l'ID du client et de la clé secrète client.

    > [AZURE.IMPORTANT]La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.

9. Dans le [portail de gestion Azure], dans le panneau des paramètres Google pour votre application Mobile App, collez les valeurs d'identifiant client et de code secret du client que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

     ![][1]

Vous êtes maintenant prêt à utiliser Google pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portail de gestion Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO1-->