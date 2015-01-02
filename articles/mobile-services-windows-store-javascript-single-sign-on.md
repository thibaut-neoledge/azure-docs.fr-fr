<properties urlDisplayName="Authenticate with single sign-on" pageTitle="Authentification de vos applications avec Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure JavaScript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Authentification unique de vos applications Windows Store avec Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	


Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows Store.  Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de Live Connect. Après avoir été authentifié par Live Connect, l'utilisateur connecté est accueilli par son nom et son ID est affiché.  

>[WACOM.NOTE]Ce didacticiel montre les avantages de l'authentification unique Live Connect pour les applications Windows Store. Cette fonction vous permet d'authentifier plus facilement un utilisateur déjà connecté avec votre service mobile. Pour une expérience d'authentification plus globale permettant la prise en charge de plusieurs fournisseurs de services d'authentification, consultez la rubrique <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/">Prise en main de l'authentification</a>.

Ce didacticiel vous familiarise avec les étapes de base pour activer l'authentification Live Connect :

1. [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2. [Restriction des autorisations de table pour les utilisateurs authentifiés]
3. [Ajout de l'authentification à l'application]

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Live]
+ Microsoft Visual Studio 2012 Express pour Windows 8 RC ou version ultérieure
+ Vous devez aussi d'abord suivre le didacticiel [Ajout de Mobile Services à une application existante].

##<a name="register"></a>Inscription de votre application pour le Windows Store

Pour pouvoir authentifier les utilisateurs, vous devez soumettre votre application à Windows Store. Vous devez ensuite inscrire la clé secrète client pour intégrer Live Connect à Mobile Services.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel <a href="/fr-fr/documentation/articles/mobile-services-windows-store-get-started">Prise en main de Mobile Services</a>.</p></li> 
<li><p>Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.</p>
   
   	<p>Cela se produit car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, alors que la table <em>TodoItem</em> requiert désormais l'authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

1. Téléchargez et installez le [Kit de développement logiciel (SDK) Live].

2. Dans le menu **Projet** de Visual Studio, cliquez sur **Ajouter une référence**, puis développez **Windows**, cliquez sur **Extensions**, sélectionnez **Kit de développement logiciel (SDK) Live**, puis cliquez sur **OK**. 

  	![][16]

  	Cette action permet d'ajouter une référence au Kit de développement logiciel (SDK) Live dans le projet.

3. Ouvrez le fichier projet default.html et ajoutez l'élément <script> suivant dans l'élément <head>. 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	Cela permet d'activer Microsoft IntelliSense dans le fichier default.html.


4. Ouvrez le fichier projet default.js et ajoutez le commentaire suivant en haut du fichier. 

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	Cela permet d'activer Microsoft IntelliSense dans le fichier default.js.

5. Dans la surcharge de la méthode **app.OnActivated**, remplacez l'appel à la méthode **refreshTodoItems** par le code suivant : 
	
        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
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
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           
            
        authenticate();

    Cela permet d'initialiser le client Live Connect, de procéder à une déconnexion forcée, d'envoyer une nouvelle demande de connexion à Live Connect, d'envoyer le jeton d'authentification renvoyé à Mobile Services, puis d'afficher des informations sur l'utilisateur connecté. Ce code permet une déconnexion forcée, si possible, ce qui garantit ainsi que l'utilisateur est invité à entrer ses informations d'identification chaque fois que l'application s'exécute. Il facilite le test de l'application avec différents comptes Microsoft afin de s'assurer que l'authentification fonctionne correctement. Ce mécanisme n'est opérationnel que si l'utilisateur connecté n'a pas de compte Microsoft connecté.

	>[WACOM.NOTE]Vous ne devez pas effectuer de demande de jetons d'authentification Live Connect ou Mobile Services chaque fois que votre application s'exécute. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Il est préférable de mettre les jetons en cache et d'essayer tout d'abord d'utiliser le jeton Mobile Services en cache avant d'appeler **LoginWithMicrosoftAccountAsync**. Pour voir un exemple de mise en cache de ce jeton, consultez la page [Prise en main de l'authentification](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens)
	
7. Remplacez la chaîne _<< INSERT REDIRECT DOMAIN HERE >>_ de l'étape précédente par le domaine de redirection qui a été spécifié lors de la configuration de l'application dans Live Connect, au format **https://_service-name_.azure-mobile.net/**.
		
8. Appuyez sur la touche F5 pour exécuter l'application et vous connecter à Live Connect avec votre compte Microsoft. 

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et vous en servir pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur l'utilisation d'autres fournisseurs d'identité à des fins d'authentification, consultez la page [Prise en main de l'authentification].

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Étapes suivantes]:#next-steps

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
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Ajout de Mobile Services à une application existante]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Autorisation des utilisateurs avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/

[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
