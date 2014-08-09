<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

[regarder le didacticiel](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) [Lire la vidéo](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:42

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-android).

Eclipse et Android 4.2 ou une version ultérieure sont requis pour suivre ce didacticiel.

Inscription de votre applicationInscription de votre application pour l'authentification et configuration de Mobile Services
----------------------------------------------------------------------------------------------------------------------------

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur avec Mobile Services.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

   	![](./media/mobile-services-android-get-started-users/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **URL du site**.

   	![](./media/mobile-services-android-get-started-users/mobile-service-uri.png)

    Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3.  Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous et suivez la procédure pour inscrire votre application auprès de ce fournisseur :
	-   [Compte Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Connexion Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Connexion Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Connexion Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

	    N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

	    **Remarque relative à la sécurité**

	    La clé secrète générée par le fournisseur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-android-get-started-users/mobile-identity-tab.png)

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

Restrictions des autorisationsRestrictions des autorisations pour les utilisateurs authentifiés
-----------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png)

2.  Cliquez sur l'onglet **Autorisations**, définissez toutes les autorisations sur **Only authenticated users**, puis cliquez sur **Enregistrer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié. Cela simplifie aussi les scripts dans le didacticiel suivant, car il ne sera pas nécessaire d'autoriser la possibilité d'utilisateurs anonymes.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png)

3.  Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-android).

4.  Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentificationAjout de l'authentification à l'application
----------------------------------------------------------------------

1.  Dans l'Explorateur de package d'Eclipse, ouvrez le fichier ToDoActivity.java, puis ajoutez les instructions import suivantes.

         import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
         import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
         import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Ajoutez la méthode suivante à la classe **ToDoActivity** :

         private void authenticate() {
            
             // Connexion à l'aide du fournisseur Google.
             mClient.login(MobileServiceAuthenticationProvider.Google,
                     new UserAuthenticationCallback() {

                         @Override
                         public void onCompleted(MobileServiceUser user,
                                 Exception exception, ServiceFilterResponse response) {

                             if (exception == null) {
                                 createAndShowDialog(String.format(
                                                 "You are now logged in - %1$2s",
                                                 user.getUserId()), "Success");
                                 createTable();
                             } else {
                                 createAndShowDialog("You must log in. Login Required", "Error");
                             }
                         }
                     });
         }

    Cela crée une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'un nom d'utilisateur Google. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    **Remarque**

    Si vous utilisez un fournisseur d'identité différent de Google, remplacez la valeur transmise à la méthode **login** ci-dessus par l'une des valeurs suivantes : *MicrosoftAccount*, *Facebook* ou *Twitter*.

3.  Dans la méthode **onCreate**, ajoutez la ligne de code suivante après le code qui permet d'instancier l'objet `MobileServiceClient`.

         authenticate();

    Cet appel lance le processus d'authentification.

4.  Déplacez le code restant après `authenticate();` dans la méthode **onCreate** vers une nouvelle méthode **createTable** qui ressemble à ceci :

         private void createTable() {

             // Obtenez l'instance de table Mobile Service à utiliser
             mToDoTable = mClient.getTable(ToDoItem.class);

             mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

             // Créez un adaptateur pour lier les éléments à la vue
             mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
             ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
             listViewToDo.setAdapter(mAdapter);

             // Chargez les éléments à partir de Mobile Service
             refreshItemsFromTable();
         }

5.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android), vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

