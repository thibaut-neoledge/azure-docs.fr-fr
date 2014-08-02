<properties linkid="develop-mobile-how-to-guides-register-for-single-sign-on" urlDisplayName="Register for single sign on" pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

Inscription de vos applications Windows Store pour utiliser l'authentification unique Windows Live Connect
==========================================================================================================

Cette rubrique montre comment inscrire votre application auprès du Windows Store de manière à pouvoir utiliser Live Connect pour l'authentification unique en tant que fournisseur d'identité pour Azure Mobile Services. Cette étape est également requise pour utiliser des notifications Push.

**Remarque**

Il n'est pas nécessaire d'inscrire votre application auprès du Windows Store pour pouvoir utiliser un compte Microsoft pour l'authentification avant de publier l'application. Lorsque votre application Windows Store ne nécessite pas une authentification unique ou des notifications Push, vous pouvez simplement l'inscrire auprès de Live Connect pour utiliser une connexion de compte Microsoft. Pour plus d'informations, consultez la page [Inscription de vos applications Windows Store pour utiliser les informations d'identification d'un compte Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication).

1.  Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application](http://go.microsoft.com/fwlink/p/?LinkID=266582) du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

      ![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png)

2.  Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

      ![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png)

      La nouvelle inscription au Windows Store pour votre application est créée.

3.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

   ![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png)

 L’Assistant **Associer votre application au Windows Store** s’affiche.

1.  Dans l’Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

2.  Sélectionnez l’application inscrite à l’étape 2, cliquez sur **Suivant**, puis sur **Associer**.

      ![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png)

      Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.    

3.  Accédez à la page [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) dans le Centre de développement Live Connect et cliquez sur votre application dans la liste **My applications**.

      ![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png)

4.  Cliquez sur **Modifier les paramètres**, puis sur **Paramètres de l'API** et notez la valeur de la **Clé secrète client**.

      ![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Remarque relative à la sécurité</b>
    <p>La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé avec personne et ne la distribuez pas avec votre application.</p>
    </div>

1.  Sous **Domaine de redirection**, entrez l'URL de votre service mobile (obtenu à l'étape 8), puis cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à intégrer l'authentification avec Live Connect dans votre application. Mobile Services propose les deux méthodes suivantes pour authentifier les utilisateurs avec Live Connect :

-   Authentification unique pour les applications Windows Store. Avec cette méthode, il suffit que les utilisateurs autorisent l'authentification dans votre application une seule fois avec Live Connect ; les informations d'identification sont ensuite gérées par Windows, sur la base des préférences utilisateur. Pour plus d'informations, consultez la page [Authentification unique pour les applications Windows Store à l'aide de Live Connect](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

-   Authentification de base. Cette méthode, qui prend en charge différents fournisseurs d'authentification, nécessite que les utilisateurs se connectent à chaque démarrage de votre application. Pour plus d'informations, consultez la page [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet).


