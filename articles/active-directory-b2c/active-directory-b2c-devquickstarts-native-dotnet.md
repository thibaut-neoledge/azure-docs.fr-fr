<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C | Microsoft Azure"
	description="Comment créer une application de bureau Windows comprenant connexion, inscription et gestion de profil à l’aide d’Azure Active Directory B2C."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Version préliminaire d’Azure AD B2C : création d’une application de bureau Windows


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Avec Azure Active Directory (Azure AD) B2C, vous pouvez ajouter de puissantes fonctionnalités de gestion des identités en libre-service à vos applications de bureau, en seulement quelques étapes. Cet article explique comment créer une application WPF (Windows Presentation Foundation) .NET de type « liste des tâches », qui inclut l’inscription d’utilisateur, la connexion et la gestion de profil. L’application prendra en charge l’inscription et la connexion à l’aide d’un nom d’utilisateur ou d’une adresse e-mail. Elle prendra également en charge l’inscription et la connexion à l’aide d’un compte de réseau social comme Facebook et Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Incluez un **client natif** dans l’application.
- Copiez l’**URI de redirection** `urn:ietf:wg:oauth:2.0:oob`. Il s’agit de l’URL par défaut pour cet exemple de code.
- Copiez l’**ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie pour chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création des trois stratégies, assurez-vous de :

- Choisir **Inscription par le biais d’un ID utilisateur** ou **Inscription par le biais d’une adresse e-mail** dans le panneau des fournisseurs d’identité.
- Choisir **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications **Nom d’affichage** et **ID d’objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Copier le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

## Téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou sur la branche `complete` du même dépôt.

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. La solution contient deux projets : un projet `TaskClient` et un projet `TaskService`. `TaskClient` est l’application de bureau WPF avec laquelle l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste des tâches de chaque utilisateur. Dans ce cas, `TaskClient` et `TaskService` sont toutes les deux représentées par un seul ID d’application, car elles constituent une application logique.

## Configuration du service de tâches

Quand `TaskService` reçoit une demande de `TaskClient`, il recherche un jeton d’accès valide pour authentifier la demande. Pour valider le jeton d’accès, vous devez fournir à `TaskService` des informations relatives à votre application. Dans le projet `TaskService`, ouvrez le fichier `web.config` qui se trouve à la racine du projet et remplacez les valeurs de la section `<appSettings>` :

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

Pour apprendre comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, découvrez l’[article sur la prise en main de l’API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Exécuter des stratégies
Quand `TaskService` est prêt à authentifier les demandes, vous pouvez implémenter `TaskClient`. Votre application communique avec Azure AD B2C en envoyant des demandes d’authentification HTTP. Celles-ci spécifient la stratégie qu’elles souhaitent exécuter dans le cadre de la demande. Pour les applications de bureau .NET, vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour envoyer des messages d’authentification OAuth 2.0, exécuter des stratégies et obtenir des jetons qui appellent des API web.

### Installer la bibliothèque ADAL
Ajoutez la bibliothèque ADAL au projet `TaskClient` à l’aide de la console du gestionnaire de package Visual Studio.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### saisissez les informations B2C
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


### Création d’AuthenticationContext
La classe principale de la bibliothèque ADAL est `AuthenticationContext`, qui représente la connexion de votre application à votre répertoire B2C. Quand l’application démarre, créez une instance d’`AuthenticationContext` dans `MainWindow.xaml.cs`. Celle-ci peut être utilisée dans l’ensemble de la fenêtre.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### Lancement d’un flux d'inscription
Quand un utilisateur choisit de s’inscrire, vous devez lancer un flux d’inscription qui utilise la stratégie d’inscription que vous avez créée. À l’aide de la bibliothèque ADAL, il vous suffit d’appeler `authContext.AcquireTokenAsync(...)`. Les paramètres que vous transmettez à `AcquireTokenAsync(...)` déterminent, entre autres, le jeton que vous recevez et la stratégie utilisée dans la demande d’authentification.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
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

### Lancement d’un flux de connexion
Vous pouvez lancer un flux de connexion de la même façon que vous lancez un flux d’inscription. Quand un utilisateur se connecte, effectuez le même appel à la bibliothèque ADAL, en utilisant cette fois votre stratégie de connexion :

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

### Lancement d’un flux de modification de profil
Là encore, vous pouvez exécuter une stratégie de modification de profil de la même manière :

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

Dans tous ces cas, la bibliothèque ADAL retourne un jeton dans `AuthenticationResult` ou lève une exception. Chaque fois que vous obtenez un jeton de la bibliothèque ADAL, vous pouvez utiliser l’objet `AuthenticationResult.UserInfo` pour mettre à jour les données utilisateur dans l’application, telles que l’interface utilisateur. La bibliothèque ADAL met également en cache le jeton, pour une utilisation dans d’autres parties de l’application.

## Appel d’API
Vous avez maintenant utilisé la bibliothèque ADAL pour exécuter des stratégies et obtenir des jetons. Dans de nombreux cas, cependant, vous souhaitez vérifier si un jeton mis en cache existe déjà, sans exécuter de stratégie. Ce genre de cas se produit quand l’application tente d’extraire la liste des tâches d’une utilisateur à partir de `TaskService`. Vous pouvez utiliser la même méthode `authContext.AcquireTokenAsync(...)` pour ce faire, de nouveau à l’aide de `clientId` en tant que paramètre d’étendue, mais cette fois en utilisant également `PromptBehavior.Never` :

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
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
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

Quand l’appel à `AcquireTokenAsync(...)` réussit et qu’un jeton est trouvé dans le cache, vous pouvez ajouter le jeton à l’en-tête `Authorization` de la requête HTTP. Ainsi, `TaskService` peut authentifier la demande de lecture de la liste des tâches de l’utilisateur :

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

Vous pouvez utiliser ce modèle chaque fois que vous souhaitez consulter le cache de jetons sans demander à un utilisateur de se connecter. Par exemple, quand l’application démarre, vous pouvez souhaiter vérifier `FileCache` pour déterminer si des jetons existent. De cette façon, la session de connexion de l’utilisateur est conservée à chaque exécution de l’application. Vous pouvez voir le même code dans l’événement `OnInitialized` de `MainWindow`. `OnInitialized` gère ce cas de la première exécution.

## Déconnexion de l’utilisateur
Vous pouvez utiliser la bibliothèque ADAL pour mettre fin à la session d’un utilisateur dans l’application quand l’utilisateur sélectionne **Se déconnecter**. À l’aide de la bibliothèque ADAL, vous pouvez effectuer cette opération en effaçant tous les jetons du cache de jetons :

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
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

## Exécution de l'exemple d'application

Pour terminer, générez et exécutez `TaskClient` et `TaskService`. Inscrivez-vous à l’application à l’aide d’une adresse de messagerie ou d’un nom d’utilisateur. Déconnectez-vous, puis reconnectez-vous en tant que même utilisateur. Modifiez le profil de cet utilisateur. Déconnectez-vous et connectez-vous en utilisant un utilisateur différent.

## Ajout d’IDP sociaux

Actuellement, l’application prend uniquement en charge l’inscription et la connexion d’utilisateurs qui recourent à des **comptes locaux**. Il s’agit de comptes stockés dans votre répertoire B2C qui utilisent un nom d’utilisateur et un mot de passe. Avec Azure AD B2C, vous pouvez ajouter la prise en charge d’autres fournisseurs d’identité sans modifier votre code.

Pour ajouter des fournisseurs d’identité sociaux à votre application, commencez par suivre les instructions détaillées figurant dans ces articles. Pour chaque fournisseur d’identité que vous souhaitez prendre en charge, vous devez inscrire une application dans ce système et obtenir un ID client.

- [Définir Facebook en tant qu’IDP](active-directory-b2c-setup-fb-app.md)
- [Définir Google en tant qu’IDP](active-directory-b2c-setup-goog-app.md)
- [Définir Amazon en tant qu’IDP](active-directory-b2c-setup-amzn-app.md)
- [Définir LinkedIn en tant qu’IDP](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, vous devez modifier chacune de vos trois stratégies pour inclure ces nouveaux fournisseurs, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, exécutez à nouveau l’application. Les nouveaux fournisseurs d’identité doivent être ajoutés comme options d’inscription et de connexion dans chacune de vos expériences relatives à l’identité.

Vous pouvez tester vos stratégies et observer les résultats sur votre exemple d’application. Ajoutez ou supprimez des fournisseurs d’identité, manipulez des revendications d’application ou modifiez des attributs d’inscription. Faites des essais jusqu’à ce que vous compreniez la façon dont les stratégies, les requêtes d’authentification et la bibliothèque ADAL sont liées.

Pour référence, l’exemple terminé [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->