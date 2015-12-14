<properties
	pageTitle="Inscription pour l'authentification Facebook | Azure Mobile Services"
	description="Apprenez à utiliser l'authentification Facebook dans votre application Azure Mobile Services."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/15/2015"
	ms.author="glenga"/>

# Inscription des applications pour l'authentification Facebook avec Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Cette rubrique montre comment inscrire vos applications afin d'utiliser Facebook pour l'authentification auprès d'Azure Mobile Services.

>[AZURE.NOTE]Ce didacticiel concerne [Azure Mobile Services], une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page est consacrée au didacticiel [Prise en main de l'authentification](mobile-services-ios-get-started-users.md), qui montre comment permettre aux utilisateurs de se connecter à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services](mobile-services-ios-get-started.md).

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285).

1. Accédez au site Web [Développeurs Facebook](http://go.microsoft.com/fwlink/p/?LinkId=268285) et connectez-vous à l'aide des informations d'identification de votre compte Facebook.

2. (Facultatif) Si vous n’êtes pas encore inscrit, cliquez sur **My Apps**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l’inscription.

3. Cliquez sur **My Apps** > **Add a New App** > **Advanced setup**.

4. Tapez un **nom d’affichage** unique pour votre application, choisissez **Apps for Pages** sous **Category**, puis cliquez sur **Create App ID** et effectuez l’exercice de sécurité.

	Cette action crée un identifiant d’application Facebook.

5. Cliquez sur **Settings**, tapez le domaine de votre service mobile dans **App Domains**, entrez une adresse de messagerie facultative dans **Contact Email**, puis cliquez sur **Add Platform** et sélectionnez **Website**.

   	![][3]

6. Tapez l'URL du service mobile dans **Site URL**, puis cliquez sur **Enregistrer les modifications**.

7. Cliquez sur **Afficher**, indiquez votre mot de passe si vous y êtes invité, puis notez les valeurs **Identifiant de l'application** et **Clé secrète**.

   	![][5] &nbsp;

    >[AZURE.IMPORTANT]La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application. &nbsp;

8. Cliquez sur l’onglet **Avancé** onglet, entrez l’un des formats d’URL suivants dans **URI de redirection OAuth valides**, puis cliquez sur **Enregistrer les modifications** :

	+ **Backend .NET** : `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **Backend JavaScript** : `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]Assurez-vous d’utiliser le format de chemin d’accès d’URL de redirection correct pour votre type de serveur principal Mobile Services. Si ce n’est pas le cas, l’authentification échouera.


9. Cliquez sur **Statut et révision** > **Oui** pour ouvrir l’accès à votre application au public.

	Le compte Facebook que vous avez utilisé pour inscrire la nouvelle application est un compte d’administrateur de l’application et peut y accéder en tant que tel. Cette étape accorde l’accès au public, pour que l’application puisse effectuer une authentification à l’aide d’autres comptes Facebook.


Vous pouvez désormais utiliser une connexion Facebook pour l'authentification de votre application en fournissant les valeurs de l'ID de l'application et de la clé secrète de l'application à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_1203_2015-->