<properties 
	pageTitle="Comment configurer l'authentification Twitter pour votre application App Services"
	description="Découvrez comment configurer l'authentification Twitter pour votre application App Services." 
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

# Comment configurer votre application pour utiliser une connexion Twitter

Cette rubrique montre comment configurer Azure App Services pour utiliser Twitter comme fournisseur d'authentification. 

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse électronique vérifiée. Pour créer un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Inscription de votre application avec Twitter

1. Accédez au site web [Développeurs Twitter], connectez-vous avec vos identifiants Twitter, puis cliquez sur **Create New App**.

2. Tapez les informations de **nom**, **description** et **site web** de votre application. Ensuite, dans **Callback URL**, tapez l'URL de votre passerelle en y ajoutant le chemin d'accès _/signin-twitter_. Par exemple, `https://contosogateway.azurewebsites.net/signin-twitter`. Assurez-vous d'utiliser le schéma HTTPS.

    ![][0]

3.  Au bas de la page, lisez et acceptez les termes du contrat. Ensuite, cliquez sur **Create your Twitter application**. Cette opération inscrit l'application et affiche les détails de la demande.

4. Cliquez sur l'onglet **Settings**, activez l'option **Allow this application to be used to sign in with Twitter**, puis cliquez sur **Update Settings**.

5. Sélectionnez l'onglet **Keys and Access Tokens**. Prenez note des valeurs de **Consumer Key (API Key)** et de **Consumer secret (API Secret)**. 

    > [AZURE.NOTE] La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.


## <a name="secrets"> </a>Ajout des informations Twitter à votre application Mobile App

6. Connectez-vous au [portail de gestion Azure] et accédez à votre passerelle App Service.

7. Sous **Paramètres**, choisissez **Identité**, puis sélectionnez **Twitter**. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous êtes maintenant prêt à utiliser Twitter pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-how-to-configure-twitter-authentication/app-service-twitter-settings.png

<!-- URLs. -->

[Développeurs Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portail de gestion Azure]: https://portal.azure.com/
[xamarin]: app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md

<!--HONumber=49-->