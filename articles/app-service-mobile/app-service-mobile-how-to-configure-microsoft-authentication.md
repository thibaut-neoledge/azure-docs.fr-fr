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
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser un compte Microsoft comme fournisseur d’authentification.


> [AZURE.NOTE]
Cette rubrique décrit l'utilisation de la fonctionnalité Authentification/autorisation d'App Service. Elle remplace la passerelle App Service pour la plupart des applications. Les différences qui s'appliquent à l'utilisation de la passerelle sont signalées dans des notes tout au long de cette section.


## <a name="register"> </a>Inscrivez votre application avec un compte Microsoft

1. Connectez-vous au [portail Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application de compte Microsoft.

2. Accédez à la page [Mes applications] dans le Centre des développeurs de compte Microsoft, puis connectez-vous avec votre compte Microsoft si nécessaire.

4. Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

5. Cliquez sur **Paramètres de l’API**. Sélectionnez **Oui** pour **Application cliente mobile ou de bureau**. Dans le champ **URL de redirection** saisissez l’**URL de redirection** de votre application, puis cliquez sur **Enregistrer**. Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/microsoftaccount/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assurez-vous d'utiliser le schéma HTTPS.

	![][0]


	> [AZURE.NOTE]
	Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d’App Service, votre URL de redirection utilise à la place l’URL de la passerelle avec le chemin d’accès _/signin-microsoft_.


6. Cliquez sur **Paramètres d'application** et notez les valeurs de l'**ID du client** et de la **Clé secrète client**.


    > [AZURE.NOTE] La clé secrète client est une information d'identification de sécurité importante. Ne partagez la clé secrète client avec personne et ne la distribuez pas dans une application cliente.


## <a name="secrets"> </a>Ajout des informations de compte Microsoft à votre application

> [AZURE.NOTE]
Si vous utilisez la passerelle App Service, ignorez cette section et accédez à votre passerelle dans le portail. Sélectionnez **Paramètres**, **Identité**, puis **Compte Microsoft**. Collez les valeurs obtenues précédemment et cliquez sur **Enregistrer**.


7. Revenez au [portail Azure] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification / Autorisation**.

8. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.

9. Cliquez sur **Compte Microsoft**. Collez les valeurs d’ID de l’application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]

	Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

17. (Facultatif) Pour restreindre l'accès à votre site aux seuls utilisateurs authentifiés par votre compte Microsoft, définissez **Action à exécuter lorsque la demande n'est pas authentifiée** sur **Compte Microsoft**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers le compte Micrososft pour être authentifiées.

11. Cliquez sur **Save**.


Vous êtes maintenant prêt à utiliser un compte Microsoft pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->