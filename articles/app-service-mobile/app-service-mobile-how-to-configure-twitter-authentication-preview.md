<properties
	pageTitle="Comment configurer l'authentification Twitter pour votre application App Services"
	description="Découvrez comment configurer l'authentification Twitter pour votre application App Services."
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

# Comment configurer votre application pour utiliser une connexion Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Cette rubrique montre comment configurer Azure Mobile Apps pour utiliser Twitter comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse électronique vérifiée. Pour créer un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Inscription de votre application avec Twitter


1. Connectez-vous au [portail de gestion Azure] et accédez à votre application Mobile App. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Twitter.

2. Cliquez sur **Paramètres**, **Authentification utilisateur**, puis cliquez sur **Twitter**. Copie l’**URL de rappel**. Vous l’utiliserez pour configurer votre application Twitter.

3. Accédez au site web [Twitter Developers], connectez-vous avec vos identifiants Twitter, puis cliquez sur **Create New App**.

4. Tapez le **Name** et une **Description** pour votre nouvelle application. Collez l’**URL de votre application Mobile App** en guise de **Website**. Ensuite, pour l’**Callback URL**, collez l’**Callback URL** que vous avez copiée précédemment. Il s’agit de la passerelle de votre application Mobile App suivie du chemin _/signin-twitter_. Par exemple, `https://contosogateway.azurewebsites.net/signin-twitter`. Assurez-vous d'utiliser le schéma HTTPS.

    ![][0]

3.  Au bas de la page, lisez et acceptez les termes du contrat. Ensuite, cliquez sur **Create your Twitter application**. Cette opération inscrit l'application et affiche les détails de la demande.

4. Cliquez sur l’onglet **Settings**, activez l’option **Allow this application to be used to sign in with Twitter**, puis cliquez sur **Update Settings**.

5. Sélectionnez l’onglet **Keys and Access Tokens**. Prenez note des valeurs de **Consumer Key (API Key)** et de **Consumer secret (API Secret)**.

    > [AZURE.NOTE]La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.


## <a name="secrets"> </a>Ajout des informations Twitter à votre application Mobile App

1. Dans le [portail de gestion Azure], dans le panneau des paramètres Twitter pour votre application Mobile App, collez les valeurs de clé API et de clé secrète API que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous êtes maintenant prêt à utiliser Twitter pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portail de gestion Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=August15_HO8-->