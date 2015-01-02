<properties pageTitle="Inscription pour l'authentification unique - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Inscription de vos applications Windows Store pour utiliser l'authentification unique Windows Live Connect

Cette rubrique montre comment inscrire votre application auprès du Windows Store de manière à pouvoir utiliser Live Connect pour l'authentification unique en tant que fournisseur d'identité pour Azure Mobile Services. Cette étape est également requise pour utiliser des notifications Push.

<div class="dev-callout"><b>Remarque</b>
<p>Il n'est pas nécessaire d'inscrire votre application auprès du Windows Store pour pouvoir utiliser un compte Microsoft pour l'authentification avant de publier l'application. Lorsque votre application Windows Store ne nécessite pas une authentification unique ou des notifications Push, vous pouvez simplement l'inscrire auprès de Live Connect pour utiliser une connexion de compte Microsoft.  Pour plus d'informations, consultez la rubrique <a href="/fr-fr/develop/mobile/how-to-guides/register-for-microsoft-authentication">Inscription de vos applications Windows Store pour utiliser les informations d'identification d'un compte Microsoft</a>.</p>
</div>

1. Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

   	![][0]

2. Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

   	![][1]

   	La nouvelle inscription au Windows Store pour votre application est créée.

3. Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services].

4. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**. 

  	![][2]

   	L'Assistant **Associer votre application au Windows Store** s'affiche.

5. Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6. Sélectionnez l'application inscrite à l'étape 2, cliquez sur **Suivant**, puis sur **Associer**.

   	![][3]

   	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.    

9. Accédez à la page [My Applications] dans le Centre de développement Live Connect et cliquez sur votre application dans la liste **My applications**.

   	![][6] 

10. Cliquez sur **Modifier les paramètres**, puis sur **Paramètres de l'API** et notez la valeur de la **Clé secrète client**. 

   	![][7]

    <div class="dev-callout"><b>Remarque relative à la sécurité</b>
	<p>La clé secrète client est une information d'identification de sécurité importante. Ne la partagez pas avec quiconque et ne la distribuez pas avec votre application.</p>
    </div>

11. Sous **Domaine de redirection**, entrez l'URL de votre service mobile (obtenue à l'étape 8), puis cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à intégrer l'authentification avec Live Connect dans votre application. Mobile Services propose les deux méthodes suivantes pour authentifier les utilisateurs avec Live Connect :

   - Authentification unique pour les applications Windows Store. Avec cette méthode, il suffit que les utilisateurs autorisent l'authentification dans votre application une seule fois avec Live Connect ; les informations d'identification sont ensuite gérées par Windows, sur la base des préférences utilisateur. Pour plus d'informations, consultez la page [Authentification unique pour les applications Windows Store à l'aide de Live Connect].

   - Authentification de base. Cette méthode, qui prend en charge différents fournisseurs d'authentification, nécessite que les utilisateurs se connectent à chaque démarrage de votre application. Pour plus d'informations, consultez la page [Prise en main de l'authentification].

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/get-started-with-users-js/
[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
