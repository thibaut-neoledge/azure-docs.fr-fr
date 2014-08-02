<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application HTML. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-html).

Inscription de votre applicationInscription de votre application pour l'authentification et configuration de Mobile Services
----------------------------------------------------------------------------------------------------------------------------

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur avec Mobile Services.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

   	![](./media/mobile-services-html-get-started-users/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **Mobile Service URL**.

   	![](./media/mobile-services-html-get-started-users/mobile-service-uri.png)

    Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3.  Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous et suivez la procédure pour inscrire votre application auprès de ce fournisseur :
	-   [Compte Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Connexion Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Connexion Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Connexion Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

    	**Remarque relative à la sécurité**

    	La clé secrète générée par le fournisseur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**.

      ![](./media/mobile-services-html-get-started-users/mobile-identity-tab.png)

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

Restrictions des autorisationsRestrictions des autorisations pour les utilisateurs authentifiés
-----------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png)

2.  Cliquez sur l'onglet **Autorisations**, définissez toutes les autorisations sur **Only authenticated users**, puis cliquez sur **Enregistrer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié. Cela simplifie aussi les scripts dans le didacticiel suivant, car il ne sera pas nécessaire d'autoriser la possibilité d'utilisateurs anonymes.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png)

3.  Dans le répertoire de l'application, lancez un des fichiers de commande suivants à partir du sous-dossier **server**.
    -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    **Remarque**

    Sur un ordinateur Windows, appuyez sur la touche « R » lorsque PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

    Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

4.  Ouvrez l'URL <http://localhost:8000/> dans un navigateur Web pour démarrer l'application.

    Le chargement des données échoue. Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

5.  (Facultatif) Ouvrez le débogueur de script de votre navigateur Web et rechargez la page. Vérifiez qu'une erreur d'accès refusé se produit.

Ensuite, vous allez mettre à jour l'application pour autoriser l'authentification avant de demander des ressources à partir du service mobile.

Ajout de l'authentificationAjout de l'authentification à l'application
----------------------------------------------------------------------

**Remarque**

La connexion étant effectuée dans une fenêtre contextuelle, vous devez appeler la méthode **login** à partir de l'événement Click du bouton. Sinon, certains navigateurs suppriment la fenêtre de connexion.

1.  Ouvrez le fichier projet index.html, recherchez l'élément H1, puis ajoutez juste en dessous l'extrait de code suivant :

         <div id="logged-in">
             You are logged in as <span id="login-name"></span>.
             <button id="log-out">Log out</button>
         </div>
         <div id="logged-out">
             You are not logged in.
             <button>Log in</button>
         </div>

    Cela vous permet de vous connecter à Mobile Services à partir de la page.

2.  Dans le fichier app.js, recherchez la ligne de code tout en bas du fichier qui appelle la fonction refreshTodoItems, puis remplacez-la par le code suivant :

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

         // Lors de l'initialisation de la page, récupérez les données et configurez les gestionnaires d'événements
         $(function () {
             refreshAuthDisplay();
             $('#summary').html('<strong>You must login to access data.</strong>');           
             $("#logged-out button").click(logIn);
             $("#logged-in button").click(logOut);
         });

    Cela crée un ensemble de fonctions pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'une connexion Facebook.

    **Remarque**

    Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur transmise à la méthode **login** ci-dessus par l'une des valeurs suivantes : *microsoftaccount*, *facebook*, *twitter* ou *google*.

3.  Revenez au navigateur où votre application s'exécute, puis actualisez la page.

    Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

    **Remarque**

    Lorsque vous utilisez Internet Explorer, vous pouvez recevoir cette erreur après la connexion : `window opener inaccessible. Il peut être dans une zone Internet Explorer différente`. Cela survient lorsque la fenêtre contextuelle s'exécute dans une zone de sécurité différente (Internet) de localhost (Intranet). Cela affecte les applications uniquement lors du développement sur localhost. Pour contourner ce problème, ouvrez l'onglet **Sécurité** dans les **Options Internet**, cliquez sur **Intranet local**, puis sur **Sites** et désactivez **Détecter automatiquement le réseau Intranet**. N'oubliez pas d'activer à nouveau ce paramètre lorsque vous avez fini de tester

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html), vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur l'utilisation de Mobile Services avec HTML/JavaScript dans le [Guide de fonctionnement Mobile Services HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

