<properties
	pageTitle="Comment configurer l'authentification par compte Microsoft pour votre application App Services"
	description="Découvrez comment configurer l'authentification par compte Microsoft pour votre application App Services."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahender"/>

# Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser un compte Microsoft comme fournisseur d’authentification.

## <a name="register-microsoft-account"> </a>Inscription de votre application avec un compte Microsoft

1. Connectez-vous au [portail Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application avec votre compte Microsoft.

2. Accédez à la page [Mes applications] dans le Centre des développeurs de compte Microsoft, puis connectez-vous avec votre compte Microsoft si nécessaire.

3. Cliquez sur **Ajouter une application**, puis tapez le nom de l’application et cliquez sur **Créer une application**.

4. Prenez note de l’**ID d’application**, car vous en aurez besoin ultérieurement.

5. Sous « Plateformes », cliquez sur **Ajouter une plateforme** et sélectionnez « Web ».

6. Sous « URI de redirection », entrez le point de terminaison de votre application, puis cliquez sur **Enregistrer**.
 
	>[AZURE.NOTE]Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/microsoftaccount/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assurez-vous d'utiliser le schéma HTTPS.

7. Sous « Secrets de l’application », cliquez sur **Générer un nouveau mot de passe**. Prenez note de la valeur qui s’affiche. Une fois que vous quittez cette page, le mot de passe ne s’affiche plus.


    > [AZURE.IMPORTANT] Le mot de passe est une information d’identification de sécurité importante. Ne partagez le mot de passe avec personne et ne le distribuez pas dans une application cliente.

## <a name="secrets"> </a>Ajout des informations de compte Microsoft à votre application App Service

1. Dans le [portail Azure], accédez à votre application et cliquez sur **Paramètres** > **Authentification / Autorisation**.

2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, définissez-la sur **Activé**.

3. Cliquez sur **Compte Microsoft**. Collez les valeurs d’ID et de mot de passe de l’application que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]

	Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

4. (Facultatif) Pour restreindre l'accès à votre site aux seuls utilisateurs authentifiés par votre compte Microsoft, définissez **Action à exécuter lorsque la demande n'est pas authentifiée** sur **Compte Microsoft**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers le compte Micrososft pour être authentifiées.

5. Cliquez sur **Save**.

Vous êtes maintenant prêt à utiliser un compte Microsoft pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->