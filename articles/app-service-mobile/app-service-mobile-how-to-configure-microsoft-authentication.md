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
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser un compte Microsoft comme fournisseur d’authentification.

## <a name="register-windows-dev-center"> </a>Inscription de votre application Windows auprès du Centre de développement Windows

Les applications Windows universelles et les applications du Windows Store doivent être enregistrées via le Centre de développement Windows. Ceci vous permettra de configurer plus facilement les notifications Push pour votre application à l’avenir.

>[AZURE.NOTE]Ignorez cette section pour les applications Windows Phone 8, Windows Phone 8.1 Silverlight et toutes les applications non-Windows. Si vous avez déjà configuré des notifications Push pour votre application Windows, vous pouvez également ignorer cette section.

1. Connectez-vous au [portail Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application avec votre compte Microsoft.

2. Si vous n’avez pas déjà inscrit votre application, accédez à la page [Centre de développement Windows](https://dev.windows.com/dashboard/Application/New), connectez-vous à votre compte Microsoft, tapez un nom d’application, vérifiez sa disponibilité, puis cliquez sur **Réserver un nom d’application**.

3. Ouvrez votre projet d’application Windows dans Visual Studio, puis, dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet d’application Windows Store, cliquez sur **Store**, puis sur **Associer l’application au Windows Store...**.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. Dans l’Assistant, cliquez sur **Se connecter**, connectez-vous avec votre compte Microsoft, sélectionnez le nom d’application que vous avez réservé, puis cliquez sur **Suivant** > **Associer**. Pour une application Windows 8.1 universelle, vous devez répéter les étapes 4 et 5 pour le projet Windows Phone Store.

6. De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services** > **Notifications Push**.

7. Sur la page **Notifications Push**, cliquez sur **Site des services Microsoft Live** sous **Windows Push Notification Services (WNS) et Microsoft Azure Mobile Services**.

Ensuite, vous configurerez l'authentification par compte Microsoft pour votre application Windows.


## <a name="register-microsoft-account"> </a>Inscription de votre application avec un compte Microsoft

Si vous avez déjà inscrit votre application Windows dans la section précédente, vous pouvez passer à l'étape 4.

1. Connectez-vous au [portail Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application avec votre compte Microsoft.

2. Accédez à la page [Mes applications] dans le Centre des développeurs de compte Microsoft, puis connectez-vous avec votre compte Microsoft si nécessaire.

3. Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

4. Cliquez sur **Paramètres API**, sélectionnez **Oui** pour **Application cliente de bureau ou mobile**, indiquez l’**URL de redirection** de votre application, puis cliquez sur **Enregistrer**.
 
	![][0]

	>[AZURE.NOTE]Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/microsoftaccount/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assurez-vous d'utiliser le schéma HTTPS.

6. Cliquez sur **Paramètres d'application** et notez les valeurs de l'**ID du client** et de la **Clé secrète client**.

    > [AZURE.IMPORTANT] La clé secrète client est une information d'identification de sécurité importante. Ne partagez la clé secrète client avec personne et ne la distribuez pas dans une application cliente.

## <a name="secrets"> </a>Ajout des informations de compte Microsoft à votre application App Service

1. Dans le [portail Azure], accédez à votre application et cliquez sur **Paramètres** > **Authentification / Autorisation**.

2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, définissez-la sur **Activé**.

3. Cliquez sur **Compte Microsoft**. Collez les valeurs d’ID de l’application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]

	Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

4. (Facultatif) Pour restreindre l'accès à votre site aux seuls utilisateurs authentifiés par votre compte Microsoft, définissez **Action à exécuter lorsque la demande n'est pas authentifiée** sur **Compte Microsoft**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers le compte Micrososft pour être authentifiées.

5. Cliquez sur **Save**.

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

<!---HONumber=AcomDC_0302_2016-->