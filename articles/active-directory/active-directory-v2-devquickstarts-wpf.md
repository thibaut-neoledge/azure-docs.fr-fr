<properties
	pageTitle="Application native .NET Azure AD v2.0 | Microsoft Azure"
	description="Comment créer une application native .NET qui se connecte avec les comptes Microsoft personnels, ainsi qu’avec les comptes professionnels ou scolaires."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Ajouter une connexion à une application de bureau Windows

Le point de terminaison v2.0 vous permet de rapidement ajouter une authentification à vos applications de bureau avec la prise en charge des comptes Microsoft personnels, ainsi que les comptes professionnels ou scolaires. Il permet également à votre application de communiquer de manière sécurisée avec une API web principale, ainsi qu’avec [Microsoft Graph](https://graph.microsoft.io) et un nombre limité d’[API unifiées Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).

Pour les [applications natives .NET qui s’exécutent sur un appareil](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d’accès pour votre application, qui les utilisent pour appeler les services Web. Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste des tâches .NET WPF qui exécute les activités suivantes :

-	connexion de l’utilisateur et obtention des jetons d’accès à l’aide du [protocole d’authentification OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) ;
-	appel sécurisé d’un service Web principal de liste de tâches, qui est également sécurisé par OAuth 2.0 ;
-	déconnexion des utilisateurs.

## Télécharger l’exemple de code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

L'application terminée est également fournie à la fin de ce didacticiel.

## Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- ajouter la plateforme **Mobile** pour votre application ;
- copier l'**URI de redirection** à partir du portail. Vous devez utiliser la valeur par défaut de `urn:ietf:wg:oauth:2.0:oob`.

## Installez et configurez ADAL
Maintenant que vous disposez d'une application enregistrée auprès de Microsoft, vous pouvez installer ADAL et écrire votre code associé aux identités. Pour permettre à ADAL de communiquer avec le point de terminaison v2.0, vous devez lui fournir des informations sur l'enregistrement de votre application.

-	Commencez par ajouter ADAL au projet TodoListClient à l'aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease
```

-	Dans le projet TodoListClient, ouvrez `app.config`. Remplacez les valeurs des éléments de la section `<appSettings>` afin qu’elles reflètent les valeurs saisies dans le portail d’inscription d’applications. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	L’élément `ida:ClientId` est l’**ID d’application** de l’application copiée à partir du portail.
    -	L’élément `ida:RedirectUri` est l’**URI de redirection** provenant du portail.
- Dans le projet TodoList-Service, ouvrez l’élément `web.config` dans la racine du projet.  
    - Remplacez la valeur `ida:Audience` par l’**ID d’application** du portail.

## Utilisation d’ADAL pour obtenir des jetons
Le principe de base de la bibliothèque ADAL consiste simplement à appeler `authContext.AcquireToken(...)` chaque fois que votre application a besoin d’un jeton d’accès, et la bibliothèque ADAL s’occupe du reste.

-	Dans le projet `TodoListClient`, ouvrez `MainWindow.xaml.cs` et recherchez la méthode `OnInitialized(...)`. La première étape est d’initialiser `AuthenticationContext` pour votre application (classe principale de la bibliothèque ADAL). C’est à ce moment-là que vous fournissez à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD et lui indiquer comment mettre en cache des jetons.

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- Lorsque l’application démarre, nous souhaitons vérifier si l’utilisateur est déjà connecté. Toutefois, nous ne voulons pas encore invoquer d’interface utilisateur de connexion. Nous demanderons à l’utilisateur de cliquer sur l’option de connexion. Également dans la méthode `OnInitialized(...)` :

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- Si l’utilisateur, non connecté, clique sur le bouton de connexion, nous souhaitons invoquer une interface utilisateur de connexion et lui demander de saisir ses informations d’identification. Implémentez le gestionnaire du bouton de connexion :

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
				// If the user canceled the login, it will result in the
				// error code 'authentication_canceled'.

				if (ex.ErrorCode == "authentication_canceled")
				{
						MessageBox.Show("Sign in was canceled by the user");
				}
				else
				{
						// An unexpected error occurred.
						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}

						MessageBox.Show(message);
				}

				return;
		}

}
```

- Si l’utilisateur parvient à se connecter, ADAL reçoit et met en cache un jeton pour vous. Vous pouvez exécuter la méthode `GetTodoList()` en toute confiance. Pour récupérer les tâches d’un utilisateur, il ne vous reste plus qu’à implémenter la méthode `GetTodoList()`.

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
				// and let them click the Sign-In button.

				if (ex.ErrorCode == "user_interaction_required")
				{
						MessageBox.Show("Please sign in first");
						SignInButton.Content = "Sign In";
				}
				else
				{
						// In any other case, an unexpected error occurred.

						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}
						MessageBox.Show(message);
				}

				return;
		}

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null) { // Si l’utilisateur a cliqué sur le bouton d’effacement du cache, // effacez le cache de jeton ADAL et représentez l’utilisateur comme déconnecté. // Il est également nécessaire d’effacer les cookies du contrôle // du navigateur afin que l’utilisateur suivant puisse se connecter.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

## Exécuter

Félicitations ! You now have a working .NET WPF app that has the ability to authenticate users & securely call Web APIs using OAuth 2.0. Run your both projects, and sign in with either a personal Microsoft account or a work or school account. Add tasks to that user's To-Do list.   Sign out, and sign back in as another user to view their To-Do list. Fermez l’application et exécutez-la de nouveau. Remarquez à quel point la session de l'utilisateur reste intacte - car l'application cache des jetons dans un fichier local.

ADAL makes it easy to incorporate common identity features into your app, using both personal and work accounts. Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore. La seule chose que vous devez vraiment connaître est un appel unique d’API : `authContext.AcquireTokenAsync(...)`.

Pour référence, l'exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Étapes suivantes

Vous pouvez maintenant aborder des rubriques plus sophistiquées. Par exemple :

- [Sécurisation de l’API web TodoListService avec le point de terminaison v2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Pour obtenir des ressources supplémentaires, consultez : - [Guide de développement v2.0 >>](active-directory-appmodel-v2-overview.md) - [StackOverflow - Balise « adal » >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=AcomDC_0224_2016-->