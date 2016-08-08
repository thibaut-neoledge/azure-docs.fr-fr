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
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Inscription des applications pour l'authentification Facebook avec Mobile Services

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)] &nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

Cette rubrique montre comment inscrire vos applications afin d'utiliser Facebook pour l'authentification auprès d'Azure Mobile Services. Cette page est consacrée au didacticiel [Prise en main de l'authentification](mobile-services-ios-get-started-users.md), qui montre comment permettre aux utilisateurs de se connecter à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services](mobile-services-ios-get-started.md).

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285).

1. Accédez au site Web [Développeurs Facebook](http://go.microsoft.com/fwlink/p/?LinkId=268285) et connectez-vous à l'aide des informations d'identification de votre compte Facebook.

2. (Facultatif) Si vous n’êtes pas encore inscrit, cliquez sur **My Apps**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l’inscription.

3. Cliquez sur **Mes applications** > **Ajouter une nouvelle application** > **Site Web** > **Ignorer et créer un ID d’application**.

4. Dans **Nom d’affichage**, saisissez un nom unique pour votre application, choisissez une **Catégorie** pour votre application, puis cliquez sur **Create App ID** et effectuez la vérification de sécurité. Vous serez alors redirigé vers le tableau de bord du développeur pour votre nouvelle application Facebook.

5. Dans le champ **App Secret**, cliquez sur **Show**, fournissez votre mot de passe si nécessaire, puis notez les valeurs **App ID** et **App Secret**. Vous les utiliserez plus tard pour configurer l’application dans Azure.

	> [AZURE.NOTE] **Remarque relative à la sécurité** : la clé secrète d’application est une information d’identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.

5. Dans la barre de navigation gauche, cliquez sur **Settings**, tapez le domaine de votre service mobile dans **App Domains**, entrez une adresse de messagerie facultative dans **Contact Email**, puis cliquez sur **Add Platform** et sélectionnez **Website**.

   	![][3]

6. Tapez l'URL du service mobile dans **Site URL**, puis cliquez sur **Enregistrer les modifications**.

7. Cliquez sur **Afficher**, indiquez votre mot de passe si vous y êtes invité, puis notez les valeurs **Identifiant de l'application** et **Clé secrète**.

   	![][5] &nbsp;

    >[AZURE.IMPORTANT] La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application. &nbsp;

8. Cliquez sur l’onglet **Avancé** onglet, entrez l’un des formats d’URL suivants dans **URI de redirection OAuth valides**, puis cliquez sur **Enregistrer les modifications** :

	+ **Service principal .NET** : `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **Service principal JavaScript** : `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]Assurez-vous d’utiliser le format de chemin d’accès d’URL de redirection correct pour votre type de serveur principal Mobile Services, à l’aide du schéma *HTTPS*. Si ce n’est pas le cas, l’authentification échouera.


12. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d’autres comptes Facebook, cliquez sur **Révision des applications** et **rendre public todolist-complete-nodejs** pour activer l’accès public général à l’aide de l’authentification Facebook.

Vous pouvez désormais utiliser une connexion Facebook pour l'authentification de votre application en fournissant les valeurs de l'ID de l'application et de la clé secrète de l'application à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0727_2016-->