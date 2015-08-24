<properties 
	pageTitle="Authentification de vos applications Windows Store avec Live Connect" 
	description="Découvrez comment utiliser l'authentification unique Live Connect dans Azure Mobile Services à partir d'une application Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

# Authentifier votre application Windows Phone avec l'authentification gérée par le client à l'aide du compte Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]

##Vue d'ensemble
Cette rubrique montre comment obtenir un jeton d'authentification de compte Microsoft à l'aide du Kit de développement logiciel (SDK) Live à partir d'une application Windows universelle. Vous utilisez ensuite ce jeton pour authentifier les utilisateurs auprès d'Azure Mobile Services. Dans ce didacticiel, vous ajoutez l'authentification de compte Microsoft à un projet existant à l'aide du Kit de développement logiciel (SDK) Live. Après avoir été correctement authentifié, l'utilisateur connecté est accueilli par son nom et la valeur de son ID s'affiche.

>[AZURE.NOTE]Ce didacticiel présente les avantages de l'utilisation de l'authentification gérée par le client et du Kit de développement logiciel (SDK) Live. Cette fonction vous permet d'authentifier plus facilement un utilisateur déjà connecté avec votre service mobile. Vous pouvez également demander des étendues supplémentaires pour permettre à votre application d'accéder à des ressources telles que OneDrive. L'authentification gérée par le service offre une expérience plus générale et prend en charge plusieurs fournisseurs d'authentification. Pour plus d'informations sur l'authentification gérée par le service, consultez la rubrique [Ajouter l'authentification à votre application](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md).

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Live]
+ Microsoft Visual Studio 2013 Update 3, ou version ultérieure
+ Vous devez suivre l'un des deux didacticiels [Prise en main de Mobile Services](mobile-services-javascript-backend-windows-store-dotnet-get-started.md) ou [Ajout de Mobile Services à une application existante].

##Inscrire votre application pour utiliser un compte Microsoft pour l'authentification

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application auprès du Centre des développeurs de compte Microsoft. Vous devez ensuite connecter cette inscription à votre service mobile. Effectuez les étapes décrites dans la rubrique suivante pour créer une inscription de compte Microsoft et la connecter à votre service mobile.

+ [Inscrire votre application pour utiliser les informations de connexion d'un compte Microsoft](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

Vous devez ensuite restreindre l'accès à une ressource, en l'occurrence la table *TodoItems* pour vous assurer qu'elle ne soit accessible que par un utilisateur inscrit.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>Ajouter l'authentification à l'application

Enfin, ajoutez le Kit de développement logiciel (SDK) Live et utilisez-le pour authentifier les utilisateurs de votre application.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez **LiveSDK**, cliquez sur **Installer** dans le package **Live SDK**, puis acceptez les contrats de licence.

  	Le Kit de développement logiciel (SDK) Live vient s'ajouter à la solution.

3. Ouvrez le fichier de projet partagé MainPage.cs et ajoutez l'instruction using suivante :

        using Microsoft.Live;        

4. Ajoutez l'extrait de code suivant à la classe **MainPage** :
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    Cela crée une variable membre pour le stockage de la session Live Connect actuelle et une méthode pour gérer le processus d'authentification.

	>[AZURE.NOTE]Vous devez essayer d'utiliser les jetons d'authentification Live mis en cache ou les jetons d'autorisation Mobile Services avant de demander de nouveaux jetons à partir des services. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Pour voir un exemple de mise en cache de ce jeton, consultez [Prise en main de l'authentification](../mobile-services-windows-store-dotnet-get-started-users.md#tokens)

5. Commentez ou supprimer l'appel de la méthode **RefreshTodoItems** dans la méthode à substituer **OnNavigatedTo** existante.

	Cela empêche les données d'être chargées avant que l'utilisateur ne soit authentifié. Ensuite, vous allez ajouter un bouton pour démarrer le processus de connexion.

6. Ajoutez l'extrait de code suivant à la classe MainPage :

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. Dans le projet d'application Windows Store, ouvrez le fichier de projet MainPage.xaml, puis ajoutez l'élément **Bouton** suivant juste avant l'élément définissant le bouton **Enregistrer** :

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Répétez l'étape précédente pour le projet d'application Windows Phone Store, mais cette fois, ajoutez le **Bouton** dans **TitlePanel**, après l'élément **TextBlock**.
		
9. Appuyez sur la touche F5 pour exécuter l'application et vous connecter à Live Connect avec votre compte Microsoft.

	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

10. Cliquez avec le bouton droit sur le projet d'application Windows Phone Store, cliquez sur **Définir comme projet de démarrage**, puis répétez l'étape précédente pour vérifier que l'application Windows Phone Store s'exécute correctement.

À présent, tout utilisateur authentifié par l'un de vos fournisseurs d'identité enregistrés peut accéder à la table *TodoItem*. Pour mieux sécuriser les données spécifiques à l'utilisateur, vous devez également implémenter l'autorisation. Pour ce faire, obtenez l'ID d'un utilisateur donné, qui permettra de déterminer le niveau d'accès dont il doit disposer pour une ressource donnée.

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur l'utilisation d'autres fournisseurs d'identité à des fins d'authentification, consultez la page [Prise en main de l'authentification]. Obtenez plus d'informations sur Mobile Services avec .NET dans [le guide de fonctionnement Mobile Services .NET]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Ajout de Mobile Services à une application existante]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Prise en main de l'authentification]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Autorisation des utilisateurs avec des scripts]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[le guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
 

<!---HONumber=August15_HO7-->