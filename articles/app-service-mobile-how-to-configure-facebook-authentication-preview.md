<properties 
	pageTitle="Comment configurer l'authentification Facebook pour votre application App Services"
	description="Découvrez comment configurer l'authentification Facebook pour votre application App Services." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Comment configurer votre application pour utiliser une connexion Facebook

Cette rubrique montre comment configurer Azure App Services pour utiliser Facebook comme fournisseur d'authentification. 
	
Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, accédez à [facebook.com].

## <a name="register"> </a>Inscription de votre application sur Facebook

1. Accédez au site web [Développeurs Facebook] et connectez-vous à l'aide des informations d'identification de votre compte Facebook.

2. (Facultatif) Si vous n'êtes pas encore inscrit, cliquez sur **My Apps**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l'inscription. 

3. Cliquez sur **Applications**, puis sur **Create a New App**.

4. Sélectionnez **Website** comme plateforme. Choisissez un nom unique pour votre application, puis cliquez sur **Create New Facebook App ID**.

5. Choisissez une catégorie pour votre application dans la liste déroulante. Ensuite, cliquez sur **Create App ID**.

6. Dans la page suivante, sélectionnez **Skip Quick Start** dans le coin supérieur droit. Ceci vous dirigera vers le tableau de bord du développeur pour votre application.

7. Dans le champ **App Secret**, cliquez sur **Show**, fournissez votre mot de passe si nécessaire, puis notez les valeurs **App ID** et **App Secret**. 

	> [AZURE.NOTE] **Remarque relative à la sécurité**
	La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.

8. Dans la barre de navigation gauche, cliquez sur **Settings**. Tapez l'URL de la passerelle dans **App Domains** et entrez une adresse de messagerie dans **Contact Email**.

    ![][0]

9. Si vous ne voyez pas de section de site web ci-dessous, cliquez sur **Add Platform** et sélectionnez **Website**. Tapez l'URL de votre passerelle App Service dans **Site URL**, puis cliquez sur **Save Changes**.

10. Cliquez sur l'onglet **Advanced** et ajoutez votre URI de redirection dans **Valid OAuth redirect URIs**. Ensuite, cliquez sur **Save Changes**. Votre URI de redirection correspond à l'URL de votre passerelle à laquelle s'ajoute le chemin d'accès _/signin-facebook_. Par exemple, `https://contosogateway.azurewebsites.net/signin-facebook`. Assurez-vous d'utiliser le schéma HTTPS.

11. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d'autres comptes Facebook, cliquez sur **Status & Review** dans la barre de navigation de gauche. Cliquez ensuite sur **Oui** pour autoriser l'accès public général.


## <a name="secrets"> </a>Ajout des informations Facebook à votre application Mobile App


12. Connectez-vous au [portail de gestion Azure] et accédez à votre passerelle App Services.

13. Sous **Paramètres**, choisissez **Identité**, puis sélectionnez **Facebook**. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous êtes maintenant prêt à utiliser Facebook pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-how-to-configure-facebook-authentication/app-service-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portail de gestion Azure]: https://portal.azure.com/

<!--HONumber=49-->