<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

Authentification unique de vos applications Windows Store avec Live Connect
===========================================================================

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>	

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows Store. Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de Live Connect. Après avoir été authentifié par Live Connect, l'utilisateur connecté est accueilli par son nom et son ID est affiché.

<div class="dev-callout"><b>Remarque</b>

<p>Ce didacticiel montre les avantages de l'authentification unique Live Connect pour les applications Windows Store. Cette fonction vous permet d'authentifier plus facilement un utilisateur déjà connecté avec votre service mobile. Pour une expérience d'authentification plus globale permettant la prise en charge de plusieurs fournisseurs de services d'authentification, consultez la rubrique <a href="/en-us/develop/mobile/tutorials/get-started-with-users-js/">Prise en main de l'authentification</a>.</p>
</div>
Ce didacticiel vous familiarise avec les étapes de base pour activer l'authentification Live Connect :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Live](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express pour Windows 8 RC ou version ultérieure

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

Inscription de votre applicationInscription de votre application pour le Windows Store
--------------------------------------------------------------------------------------

Pour pouvoir authentifier les utilisateurs, vous devez soumettre votre application à Windows Store. Vous devez ensuite inscrire la clé secrète client pour intégrer Live Connect à Mobile Services.

1.  Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application](http://go.microsoft.com/fwlink/p/?LinkID=266582) du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png)

2.  Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png)

	La nouvelle inscription au Windows Store pour votre application est créée.

3.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png)

	L’Assistant **Associer votre application au Windows Store** s’affiche.

1.  Dans l’Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

2.  Sélectionnez l’application inscrite à l’étape 2, cliquez sur **Suivant**, puis sur **Associer**.

     ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png)

	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.    

3.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png)

4.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **URL du site**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png)

	Cette valeur vous permet de définir le domaine de redirection.

5.  Accédez à la page [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) dans le Centre de développement Live Connect et cliquez sur votre application dans la liste **My applications**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png) 

6.  Cliquez sur **Modifier les paramètres**, puis sur **Paramètres de l'API** et notez les valeurs de l'**ID du client** et de la **Clé secrète client**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Remarque relative à la sécurité</b>
    <p>La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé avec personne et ne la distribuez pas avec votre application.</p>
    </div>

1.  Sous **Domaine de redirection**, entrez l'URL de votre service mobile (obtenu à l'étape 8), puis cliquez sur **Enregistrer**.

2.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez la **clé secrète client** récupérée dans Windows Store, puis cliquez sur **Enregistrer**.

    ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png)

Votre service mobile et votre application sont désormais configurés pour utiliser Live Connect.

Restriction des autorisationsRestriction des autorisations pour les utilisateurs authentifiés
---------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png)

2.  Cliquez sur l'onglet **Autorisations**, définissez toutes les autorisations sur **Seuls les utilisateurs authentifiés**, puis cliquez sur **Enregistrer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié. Cela simplifie aussi les scripts dans le didacticiel suivant, car il ne sera pas nécessaire d'autoriser la possibilité d'utilisateurs anonymes.

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png)

3.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

4.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception associée au code d'état 401 (Non autorisé) est générée.

	Cela se produit, car l'application accède à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais une authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avec Live Connect avant de demander des ressources à partir du service mobile.

Ajout de l'authentificationAjout de l'authentification à l'application
----------------------------------------------------------------------

1.  Téléchargez et installez le [Kit de développement logiciel (SDK) Live](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  Dans le menu **Projet** de Visual Studio, cliquez sur **Ajouter une référence**, puis développez **Windows**, cliquez sur **Extensions**, cochez **Kit de développement logiciel (SDK)**, puis cliquez sur **OK**.

    ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png) 
  
    Cette action permet d'ajouter une référence au Kit de développement logiciel (SDK) Live dans le projet.

1.  Ouvrez le fichier projet default.html et ajoutez l'élément &lt;script\> suivant dans l'élément &lt;head\>.

         <script src="///LiveSDKHTML/js/wl.js"></script>

     Cela permet d'activer Microsoft IntelliSense dans le fichier default.html.

2.  Ouvrez le fichier projet default.js et ajoutez le commentaire suivant en haut du fichier.

         /// <reference path="///LiveSDKHTML/js/wl.js" />

     Cela permet d'activer Microsoft IntelliSense dans le fichier default.js.

3.  Dans la surcharge de la méthode **app.OnActivated**, remplacez l'appel à la méthode **refreshTodoItems** par le code suivant :

         var session = null;   

         var logout = function () {
             return new WinJS.Promise(function (complete) {
                 WL.getLoginStatus().then(function () {
                     if (WL.canLogout()) {
                         WL.logout(complete);                            
                     }
                     else{
                         complete();
                     }
                 });
             });
         };                  

         var login = function () {
             return new WinJS.Promise(function (complete) {                    
                 WL.login({ scope: "wl.basic"}).then(function (result) {
                     session = result.session;

                     WinJS.Promise.join([
                         WL.api({ path: "me", method: "GET" }),
                         client.login(result.session.authentication_token)
                     ]).done(function (results) {
                         var profile = results[0];
                         var mobileServicesUser = results[1];
                         refreshTodoItems();
                         var title = "Welcome " + profile.first_name + "!";
                         var message = "You are now logged in as: " + mobileServicesUser.userId;
                         var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                         dialog.showAsync().done(complete);                                
                     });                       
                 }, function (error) {                        
                     session = null;
                     var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                     dialog.showAsync().done(complete);                        
                 });
             });
         }

         var authenticate = function () {
             // Procédez à une déconnexion forcée pour faciliter le test avec plusieurs comptes Microsoft.
             logout().then(login).then(function () {
                 if (session === null) {
                     // Échec de l'authentification, essayez à nouveau.
                     authenticate();
                 }
             });
         }

         WL.init({
             redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
         });           
                
         authenticate();

    Cela permet d'initialiser le client Live Connect, de procéder à une déconnexion forcée, d'envoyer une nouvelle demande de connexion à Live Connect, d'envoyer le jeton d'authentification renvoyé à Mobile Services, puis d'afficher des informations sur l'utilisateur connecté.

    <div class="dev-callout"><b>Remarque</b>

    <p>Ce code permet une déconnexion forcée, si possible, ce qui garantit ainsi que l'utilisateur est invité à entrer ses informations d'identification chaque fois que l'application s'exécute. Il facilite le test de l'application avec différents comptes Microsoft afin de s'assurer que l'authentification fonctionne correctement. Ce mécanisme n'est opérationnel que si l'utilisateur connecté n'a pas de compte Microsoft connecté.</p>
	</div>

4.  Remplacez la chaîne *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* de l'étape précédente par le domaine de redirection qui a été spécifié lors du paramétrage de l'application dans Live Connect, au format **https://*nom-service*.azure-mobile.net/**.

5.  Appuyez sur la touche F5 pour exécuter l'application et vous connecter à Live Connect avec votre compte Microsoft.

     Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js), vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur l'utilisation d'autres fournisseurs d'identité à des fins d'authentification, consultez la page [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-js).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-js
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-users-js

[Azure Management Portal]: https://manage.windowsazure.com/