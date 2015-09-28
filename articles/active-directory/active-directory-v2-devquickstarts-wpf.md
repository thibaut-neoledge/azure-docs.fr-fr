<properties
	pageTitle="Modèle d’application v2.0 - Application native .NET | Microsoft Azure"
	description="Comment créer une application native .NET qui se connecte avec les comptes Microsoft personnels, ainsi qu’avec les comptes professionnels ou scolaires."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Version d’évaluation du modèle d’application v2.0 : Ajouter une connexion à une application de bureau Windows

Avec le modèle d’application v2.0, vous pouvez rapidement ajouter une authentification à vos applications de bureau, avec prise en charge pour les comptes Microsoft personnels, ainsi que pour les comptes professionnels ou scolaires. Il permet également à votre application de communiquer de manière sécurisée avec une API Web principale, ainsi qu’avec un nombre limité des [API unifiées Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour bénéficier d’instructions sur l’intégration avec le service Azure AD généralement disponible, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

Pour les [applications natives .NET qui s’exécutent sur un appareil](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d’accès pour votre application, qui les utilisent pour appeler les services Web. Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste des tâches .NET WPF qui exécute les activités suivantes :

-	connexion de l’utilisateur et obtention des jetons d’accès à l’aide du [protocole d’authentification OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) ;
-	appel sécurisé d’un service Web principal de liste de tâches, qui est également sécurisé par OAuth 2.0 ;
-	déconnexion des utilisateurs.

Pour générer l’application fonctionnelle complète, vous devez :

2. inscrire votre application ;
3. installer et configurer la bibliothèque ADAL ;
5. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

L'application terminée est également fournie à la fin de ce didacticiel.

## 1. Enregistrez une application
Créez une nouvelle application sur [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez la [procédure indiquée ici](active-directory-v2-app-registration.md).  Assurez-vous de :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- ajouter la plateforme **Mobile** pour votre application ;
- copier l'**URI de redirection** à partir du portail. Vous devez utiliser la valeur par défaut de « urn:ietf:wg:oauth:2.0:oob ».

## 2. Installez et configurez ADAL
Une fois l'application enregistrée auprès de Microsoft, vous pouvez installer ADAL et saisir votre code associé aux identités.  Pour qu'ADAL puisse communiquer le point de terminaison v2.0, vous devez lui fournir des informations sur l'enregistrement de votre application.

-    Commencez par ajouter ADAL au projet TodoListClient à l'aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease 
```

-	Dans le projet TodoListClient, ouvrez `app.config`. Remplacez les valeurs des éléments de la section `<appSettings>` afin qu’elles reflètent les valeurs saisies dans le portail d’inscription d’applications. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	L’élément `ida:ClientId` est l’**ID d’application** de l’application copiée à partir du portail.
    -	L’élément `ida:RedirectUri` est l’**URI de redirection** à partir du portail.
- Dans le projet TodoList-Service, ouvrez l’élément `web.config` dans la racine du projet.  
    - Remplacez la valeur `ida:Audience` par l’**ID d’application** du portail.

## 3\. Utilisation d’ADAL pour obtenir des jetons
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

- Lorsque l’application démarre, nous souhaitons vérifier si l’utilisateur est déjà connecté. Toutefois, nous ne voulons pas encore invoquer d’interface utilisateur de connexion. Nous demanderons à l’utilisateur de cliquer sur l’option de connexion. Également dans la méthode `OnInitialized(...)` :

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

- Si l’utilisateur, non connecté, clique sur le bouton de connexion, nous souhaitons invoquer une interface utilisateur de connexion et lui demander de saisir ses informations d’identification. Implémentez le gestionnaire du bouton de connexion :

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

- Si l’utilisateur parvient à se connecter, ADAL reçoit et met en cache un jeton pour vous ; vous pouvez exécuter la méthode `GetTodoList()` en toute confiance. Pour récupérer les tâches d’un utilisateur, il ne vous reste plus qu’à implémenter la méthode `GetTodoList()`.

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

Félicitations ! Vous disposez maintenant d'une application fonctionnelle WPF .NET qui a la capacité d'authentifier les utilisateurs et d'appeler en toute sécurité les API Web à l'aide d'OAuth 2.0.  Exécutez vos projets et connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire. Ajoutez des tâches à la liste de tâches de cet utilisateur.  Déconnectez-vous et reconnectez-vous à l'aide du compte d'un autre utilisateur pour afficher sa liste de tâches.  Fermez l'application et réexécutez-la.  Remarquez à quel point la session de l'utilisateur reste intacte - car l'application cache des jetons dans un fichier local.

ADAL permet d'intégrer très facilement des fonctionnalités d'identités courantes dans votre application, à l'aide de comptes professionnels ou personnels. Il accomplit à votre place toutes les tâches fastidieuses, telles que la gestion du cache, la prise en charge du protocole OAuth, la présentation de l'utilisateur à l'aide d'une interface utilisateur de connexion, l'actualisation des jetons expirés, etc.  Il vous suffit simplement de connaître un appel d'API unique, « authContext.AcquireTokenAsync (...) ».

À titre de référence, l'exemple terminé (sans vos valeurs de configuration) [est disponible ici, sous la forme d'un fichier .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), mais vous pouvez également le cloner à partir de GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Étapes suivantes

Vous pouvez maintenant aborder des rubriques plus sophistiquées. Par exemple :

- [Sécurisation de l’API Web TodoListService avec le modèle d’application v2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Pour obtenir des ressources supplémentaires, consultez : - [La version d’évaluation du modèle d’application v2.0 >>](active-directory-appmodel-v2-overview.md) - [Balise adal StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=Sept15_HO3-->