<properties
	pageTitle="Prise en main de l&#39;authentification (Xamarin.Android) - Mobile Services"
	description="Découvrez comment utiliser l&#39;authentification dans votre application Azure Mobile Services pour Xamarin.Android."
	services="mobile-services"
	documentationCenter="xamarin"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="donnam"/>

# Ajout de l'authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

<p>Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application Xamarin.Android. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.</p>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscrire votre application pour l'authentification et configurer Mobile Services]
2. [Restreindre les autorisations de table aux utilisateurs authentifiés]
3. [Ajouter l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

Xamarin.Android et le Kit de développement logiciel (SDK) Android 4.2 ou une version ultérieure sont requis pour suivre ce didacticiel.

##<a name="register"></a>Inscrire votre application pour l'authentification et configurer Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services].

4. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

	 Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

1. Ajoutez la propriété suivante à la classe **ToDoActivity** :

		private MobileServiceUser user;

2. Ajoutez la méthode suivante à la classe **ToDoActivity** :

        private async Task Authenticate()
        {
            try
            {
                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex, "Authentication failed");
            }
        }

    Cela crée une méthode pour gérer le processus d’authentification. L'utilisateur est authentifié à l'aide d'une connexion Microsoft Account. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    > [AZURE.NOTE]Si vous utilisez un autre fournisseur d'identité que Microsoft, remplacez la valeur passée à la méthode **login** ci-dessus par l'une des valeurs suivantes : _Facebook_, _Google_, _Twitter_ ou _WindowsAzureActiveDirectory_.

3. Dans la méthode **onCreate**, ajoutez la ligne de code suivante après le code qui permet d'instancier l'objet `MobileServiceClient`.

		await Authenticate();

	Ceci démarre le processus d'authentification et l'attend de façon asynchrone.

4. Déplacez le code restant après `await Authenticate();` dans la méthode **OnCreate** vers une nouvelle méthode **CreateTable** qui ressemble à ceci :

        private async Task CreateTable()
        {

            await InitLocalStoreAsync();

            // Get the Mobile Service Table instance to use
            toDoTable = client.GetTable<ToDoItem>();

            textNewToDo = FindViewById<EditText>(Resource.Id.textNewToDo);

            // Create an adapter to bind the items with the view
            adapter = new ToDoItemAdapter(this, Resource.Layout.Row_List_To_Do);
            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewToDo);
            listViewTodo.Adapter = adapter;

            // Load the items from the Mobile Service
            await RefreshItemsFromTableAsync();
        }

5. Appelez ensuite la nouvelle méthode **CreateTable** dans **OnCreate** après l'appel **Authenticate** ajouté à l'étape 2 :

		await CreateTable();


6. À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

## Téléchargement de l'exemple terminé
Téléchargez le [projet d'exemple terminé]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

<!-- Anchors. -->
[Inscrire votre application pour l'authentification et configurer Mobile Services]: #register
[Restreindre les autorisations de table aux utilisateurs authentifiés]: #permissions
[Ajouter l'authentification à l'application]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Autorisation des utilisateurs avec des scripts]: mobile-services-javascript-backend-service-side-authorization.md
[Azure Management Portal]: https://manage.windowsazure.com/
[projet d'exemple terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331328

<!---HONumber=August15_HO8-->