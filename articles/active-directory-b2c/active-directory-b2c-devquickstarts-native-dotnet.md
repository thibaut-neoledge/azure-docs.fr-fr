<properties
	pageTitle="Version d’évaluation d’Azure AD B2C | Microsoft Azure"
	description="Comment créer une application de bureau Windows avec connexion, inscription et gestion de profil à l'aide d'Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Version préliminaire d’Azure AD B2C : création d’une application de bureau Windows

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Avec Azure AD B2C, vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à vos applications de bureau en quelques étapes seulement. Cet article explique comment créer une application WPF .NET de type « liste des tâches », qui inclut l’inscription d’utilisateur, la connexion et la gestion de profil. Cette application prendra en charge l'inscription et la connexion à l’aide d’un nom d'utilisateur, d’une adresse e-mail ou d’un compte de réseau social comme Facebook et Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n’en avez pas encore, reportez-vous à [Créer un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin.

## 2\. Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Assurez-vous de

- Inclure un **client natif** dans l'application.
- Noter l’**Uri de redirection** `urn:ietf:wg:oauth:2.0:oob` ; il s’agit de l'URL par défaut pour cet exemple de code.
- Noter également l’**ID d’application** affecté à votre application. Vous en aurez besoin rapidement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [**stratégie**](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie de chaque type, comme décrit dans l'[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos trois stratégies, assurez-vous de :

- Choisir **Inscription par le biais d'un ID utilisateur** ou **Inscription par le biais d'une adresse e-mail** dans le panneau des fournisseurs d'identité.
- Choisir le **nom d’affichage** et quelques autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications **nom d’affichage** et **ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez également choisir d’autres revendications.
- Noter le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies rapidement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

## 4\. Téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Une fois l’exemple de code téléchargé, ouvrez le fichier Visual Studio `.sln` pour commencer. Vous remarquerez qu’il existe deux projets dans la solution : un projet `TaskClient` et un projet `TaskService`. Le projet `TaskClient` est l’application de bureau WPF avec laquelle l’utilisateur interagit. Le projet `TaskService` est l’API web du serveur principal de l’application qui stocke la liste des tâches de chaque utilisateur. Les projets `TaskClient` et `TaskService` sont représentées par un seul **ID d’application** dans ce cas, car ils comprennent toutes deux une application logique.

## 5\. Configuration du service de tâches

Lorsque le projet `TaskService` reçoit des demandes de `TaskClient`, il recherche un jeton d’accès valide pour authentifier la demande. Pour valider le jeton d’accès, vous devez fournir au projet `TaskService` des informations relatives à votre application. Dans le projet `TaskService`, ouvrez le fichier `web.config` qui se trouve à la racine et remplacez les valeurs de la section `<appSettings>` :

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
```
  
[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Si vous souhaitez apprendre comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, découvrez notre [article Prise en main de l’API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6\. Exécuter des stratégies
Maintenant que le `TaskService` est prêt authentifier les demandes, nous pouvons implémenter le `TaskClient`. Votre application communique avec Azure AD B2C en envoyant des demandes d’authentification HTTP, en spécifiant la stratégie qu’il souhaite exécuter dans le cadre de la demande. Pour les applications de bureau .NET, vous pouvez utiliser la **bibliothèque d’authentification Active Directory (ADAL)** pour envoyer des messages d’authentification OAuth 2.0, exécuter des stratégies et obtenir des jetons pour appeler des API web.

#### Installer la bibliothèque ADAL
Commencez par ajouter la bibliothèque ADAL au projet TaskClient à l’aide de la console du gestionnaire de package Visual Studio.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### Saisissez les informations B2C
Ouvrez le fichier `Globals.cs`, puis remplacez chacune des valeurs de propriété par vos propres valeurs. Cette classe est utilisée dans `TaskClient` pour référencer des valeurs couramment utilisées.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
``` 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


#### Création d’un AuthenticationContext
La classe principale de la bibliothèque ADAL est `AuthenticationContext`. Elle représente la connexion de votre application à votre répertoire B2C. Quand l’application démarre, créez une instance de `AuthenticationContext` dans le fichier `MainWindow.xaml.cs`, qui peut être utilisée dans la fenêtre.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### Lancement d’un flux d'inscription
Lorsque l'utilisateur clique sur le bouton d'inscription, nous souhaitons lancer un flux d'inscription à l'aide de la stratégie d'inscription que vous avez créée. Avec la bibliothèque ADAL, il vous suffit d’appeler `authContext.AcquireTokenAsync(...)`. Les paramètres que vous transmettez à `AcquireTokenAsync(...)` déterminent le jeton que vous recevez, la stratégie utilisée dans la demande d’authentification, et ainsi de suite.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
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

#### Lancement d’un flux de connexion
Il est possible de lancer un flux de connexion de la même manière que pour le flux d'inscription. Lorsque l'utilisateur clique sur le bouton de connexion, effectuez le même appel de la bibliothèque ADAL, mais cette fois à l'aide de la stratégie de connexion :

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...			
```

#### Lancement d’un flux de modification de profil
Là encore, vous pouvez exécuter votre stratégie de modification de profil de la même manière :

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

Dans tous ces cas, la bibliothèque ADAL retourne un jeton dans son `AuthenticationResult` ou lève une exception. Chaque fois que vous obtenez un jeton de la bibliothèque ADAL, vous pouvez utiliser l’objet `AuthenticationResult.UserInfo` pour mettre à jour les données utilisateur dans l’application, telles que l’interface utilisateur. La bibliothèque ADAL met également en cache le jeton, pour une utilisation dans d'autres parties de l'application.

## 7\. Appel d’API
Nous avons déjà utilisé la bibliothèque ADAL pour exécuter des stratégies et obtenir des jetons. Dans de nombreux cas, cependant, vous souhaiterez vérifier si un jeton mis en cache existe déjà, sans exécuter de stratégie. Ce genre de cas se produit lorsque l’application tente d’extraire la liste des tâches de l’utilisateur à partir du `TaskService`. Vous pouvez utiliser la même méthode `authContext.AcquireTokenAsync(...)` pour ce faire, une fois encore à l’aide du `clientId` en tant que paramètre d’étendue, mais cette fois en utilisant `PromptBehavior.Never` :

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token 
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

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
	...
```

Quand l’appel à `AcquireTokenAsync(...)` réussit et qu’un jeton est trouvé dans le cache, vous pouvez ajouter le jeton à l’en-tête `Authorization` de la requête HTTP, afin que le `TaskService` puisse authentifier la demande de lecture de la liste des tâches de l’utilisateur :

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

Vous pouvez utiliser ce même modèle chaque fois que vous souhaitez consulter le cache de jetons sans demander à l'utilisateur de se connecter. Par exemple, quand l’application démarre, nous souhaitons vérifier le `FileCache` pour déterminer si des jetons existent, afin que la session de connexion de l’utilisateur soit conservée à chaque exécution de l’application. Vous pouvez voir le même code dans l’événement `OnInitialized` de `MainWindow`, qui gère ce cas de la première exécution.

## 8\. Déconnexion de l’utilisateur
Enfin, vous pouvez utiliser la bibliothèque ADAL pour mettre fin à la session de l'utilisateur dans l'application, lorsque l'utilisateur clique sur le bouton « Se déconnecter ». Grâce à la bibliothèque ADAL, c’est aussi simple que d’effacer tous les jetons du cache de jetons :

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## 9\. Exécution de l'exemple d'application

Pour terminer, générez et exécutez `TaskClient` et `TaskService`. Inscrivez-vous à l'application avec une adresse de messagerie ou un nom d'utilisateur. Déconnectez-vous, puis reconnectez-vous en tant que même utilisateur. Modifiez le profil de cet utilisateur. Déconnectez-vous et connectez-vous avec un utilisateur différent.

## 10\. Ajout d’IDP sociaux

Actuellement, l’application prend uniquement en charge l’inscription et la connexion d’utilisateur à l’aide de ce que l’on appelle des **comptes locaux**, c’est-à-dire des comptes stockés dans votre répertoire B2C avec un nom d’utilisateur et un mot de passe. Avec Azure AD B2C, vous pouvez ajouter la prise en charge d’autres **fournisseurs d’identité**, ou IDP, sans modifier votre code.

Pour ajouter des IDP sociaux à votre application, commencez par suivre les instructions détaillées dans un ou plusieurs de ces articles. Pour chaque IDP que vous souhaitez prendre en charge, vous devez enregistrer une application dans le système et obtenir un ID client.

- [Définir Facebook en tant qu’IDP](active-directory-b2c-setup-fb-app.md)
- [Définir Google en tant qu’IDP](active-directory-b2c-setup-goog-app.md)
- [Définir Amazon en tant qu’IDP](active-directory-b2c-setup-amzn-app.md)
- [Définir LinkedIn en tant qu’IDP](active-directory-b2c-setup-li-app.md) 

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, vous devez revenir en arrière et modifier chacune de vos trois stratégies pour inclure ces nouveaux IDP, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, réexécutez simplement l’application. Vous devez voir les nouveaux IDP ajoutés comme une option d’inscription et de connexion pour chacune de vos expériences relatives à l’identité.

Vous pouvez tester librement vos stratégies et observer l'effet sur votre application d’exemple : ajoutez/supprimez des IDP, manipulez des revendications d'application, modifiez des attributs d'inscription. Faites des essais jusqu'à ce que vous commenciez à comprendre la façon dont les stratégies, les demandes d'authentification et la bibliothèque ADAL sont liées.

Pour référence, l’exemple terminé [est fourni au format .zip ici](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO3-->