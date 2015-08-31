<properties 
	pageTitle="Ajout de l&#39;authentification à votre application HTML/JavaScript | Microsoft Azure" 
	description="Apprenez à utiliser Mobile Services pour authentifier les utilisateurs de votre application HTML par l&#39;intermédiaire de divers fournisseurs d&#39;identité, notamment Google, Facebook, Twitter et un compte Microsoft." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Mobile Services 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Cette rubrique vous montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de vos applications HTML, y compris les applications PhoneGap ou Cordova. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

##<a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Dans le répertoire de l'application, lancez un des fichiers de commande suivants à partir du sous-dossier **server**.

	+ **launch-windows** (pour les ordinateurs Windows) 
	+ **launch-mac.command** (pour les ordinateurs Mac OS X)
	+ **launch-linux.sh** (pour les ordinateurs Linux)

	>[AZURE.NOTE]Sur un ordinateur Windows, appuyez sur la touche `R` quand PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

	Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

2. Ouvrez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur web pour démarrer l'application.

	Le chargement des données échoue. Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

3. (Facultatif) Ouvrez le débogueur de script de votre navigateur Web et rechargez la page. Vérifiez qu'une erreur d'accès refusé se produit.

Ensuite, vous allez mettre à jour l'application pour autoriser l'authentification avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

>[AZURE.NOTE]La connexion étant effectuée dans une fenêtre contextuelle, vous devez appeler la méthode **login** à partir de l'événement Click du bouton. Sinon, certains navigateurs suppriment la fenêtre de connexion.

1. Ouvrez le fichier projet index.html, recherchez l'élément H1, puis ajoutez juste en dessous l'extrait de code suivant :

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	Cela vous permet de vous connecter à Mobile Services à partir de la page.

2. Dans le fichier app.js, recherchez la ligne de code tout en bas du fichier qui appelle la fonction refreshTodoItems, puis remplacez-la par le code suivant :
	
		function refreshAuthDisplay() {
			var isLoggedIn = client.currentUser !== null;
			$("#logged-in").toggle(isLoggedIn);
			$("#logged-out").toggle(!isLoggedIn);

			if (isLoggedIn) {
				$("#login-name").text(client.currentUser.userId);
				refreshTodoItems();
			}
		}

		function logIn() {
			client.login("facebook").then(refreshAuthDisplay, function(error){
				alert(error);
			});
		}

		function logOut() {
			client.logout();
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');
		}

		// On page init, fetch the data and set up event handlers
		$(function () {
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');		    
			$("#logged-out button").click(logIn);
			$("#logged-in button").click(logOut);
		});

    Cela crée un ensemble de fonctions pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'une connexion Facebook. Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur transmise à la méthode **login** ci-dessus par l'une des valeurs suivantes : *microsoftaccount*, *facebook*, *twitter*, *google* ou *aad*.

	>[AZURE.IMPORTANT]Dans une application PhoneGap, vous devez également ajouter les plug-ins suivants au projet : <ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Revenez au navigateur où votre application s'exécute, puis actualisez la page.

	   Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

	>[AZURE.NOTE]Quand vous utilisez Internet Explorer, vous pouvez recevoir cette erreur après la connexion : <code>window opener inaccessible. Il peut être dans une zone Internet Explorer différente</code>. Cela survient lorsque la fenêtre contextuelle s'exécute dans une zone de sécurité différente (Internet) de localhost (Intranet). Cela affecte les applications uniquement lors du développement sur localhost. Pour contourner ce problème, ouvrez l'onglet **Sécurité** dans les **Options Internet**, cliquez sur **Intranet local**, puis sur **Sites** et désactivez **Détecter automatiquement le réseau Intranet**. N'oubliez pas d'activer à nouveau ce paramètre lorsque vous avez fini de tester

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur l'utilisation de Mobile Services avec HTML/JavaScript dans le [Guide de fonctionnement Mobile Services HTML/JavaScript]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-html-get-started.md
[Get started with data]: mobile-services-html-get-started-data.md
[Autorisation des utilisateurs avec des scripts]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services HTML/JavaScript]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=August15_HO8-->