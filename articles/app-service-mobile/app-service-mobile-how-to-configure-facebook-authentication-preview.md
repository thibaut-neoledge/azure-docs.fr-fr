<properties
	pageTitle="Comment configurer l'authentification Facebook pour votre application App Services"
	description="Découvrez comment configurer l'authentification Facebook pour votre application App Services."
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

# Comment configurer votre application pour utiliser une connexion Facebook

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Cette rubrique montre comment configurer Azure Mobile Apps pour utiliser Facebook comme fournisseur d’authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].

## <a name="register"> </a>Inscription de votre application sur Facebook

1. Connectez-vous au [portail de gestion Azure] et accédez à votre application Mobile App. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Facebook.
 
2. Cliquez sur **Paramètres**, **Authentification utilisateur**, puis cliquez sur **Facebook**. Copiez ensuite l’**URI de redirection** à partir du panneau Facebook. Vous l’utiliserez avec votre application Facebook.
 
3. Dans une autre fenêtre de navigateur, accédez au site web [Développeurs Facebook] et connectez-vous à l’aide des informations d’identification de votre compte Facebook.

4. (Facultatif) Si vous n’êtes pas encore inscrit, cliquez sur **Applications**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l’inscription.

5. Cliquez sur **My Apps**, puis sur **Add a New App**.

6. Sélectionnez **Website** comme plateforme. Choisissez un nom unique pour votre application, puis cliquez sur **Create New Facebook App ID**.

7. Choisissez une catégorie pour votre application dans la liste déroulante. Ensuite, cliquez sur **Create App ID**.

8. Dans la page suivante, sélectionnez **Skip Quick Start** dans le coin supérieur droit. Ceci vous dirigera vers le tableau de bord du développeur pour votre application.

9. Dans le champ **App Secret**, cliquez sur **Show**, fournissez votre mot de passe si nécessaire, puis notez les valeurs **App ID** et **App Secret**. Vous allez les définir dans le panneau de paramètres d’authentification Facebook de votre application Mobile App.

	> [AZURE.NOTE]**Remarque relative à la sécurité** : la clé secrète d’application est une information d’identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.

10. Dans la barre de navigation gauche, cliquez sur **Settings**. Tapez l’**URL** de votre application Mobile App dans **App Domains** et entrez une adresse de messagerie dans **Contact Email**.

    ![][0]

11. Si vous ne voyez pas de section de site web ci-dessous, cliquez sur **Add Platform** et sélectionnez **Website**. Tapez l’**URL** de votre application Mobile App dans le champ **Site URL**, puis cliquez sur **Save Changes**.

12. Cliquez sur l’onglet **Advanced** et ajoutez l’**URI de redirection** de votre application Mobile App dans **Valid OAuth redirect URIs**. Ensuite, cliquez sur **Save Changes**. Votre URI de redirection correspond à l’URL de la passerelle de votre application Mobile App suivie du chemin d’accès _/signin-facebook_. Par exemple : `https://contosogateway.azurewebsites.net/signin-facebook`. Assurez-vous d'utiliser le schéma HTTPS.

13. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d’autres comptes Facebook, cliquez sur **Status & Review** dans la barre de navigation de gauche. Cliquez ensuite sur **Oui** pour autoriser l'accès public général.


## <a name="secrets"> </a>Ajout des informations Facebook à votre application Mobile App


12. Dans le [portail de gestion Azure], accédez à nouveau au panneau de paramètres Facebook de votre application Mobile App. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous êtes maintenant prêt à utiliser Facebook pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/
[portail de gestion Azure]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->