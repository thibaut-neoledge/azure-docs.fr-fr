<properties
	pageTitle="Prise en main d’Azure AD Xamarin | Microsoft Azure"
	description="Création d’une application Xamarin qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="dastrock"/>


# Intégration d’Azure AD dans une application Xamarin

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin permet d’écrire des applications en C#, qui peuvent être exécutées sur différentes plateformes, y compris sur les appareils mobiles et les PC. Si vous créez une application à l’aide de Xamarin, Azure AD facilite l’authentification de vos utilisateurs avec leurs comptes Active Directory. Il permet également à votre application d’utiliser en toute sécurité une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les applications Xamarin qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d’accès pour votre application. Pour illustrer cette simplicité, nous créerons une application « Directory Searcher » effectuant les actions suivantes :

-	exécution sur iOS, Android, Bureau Windows, Windows Phone et Windows Store ;
- utilisation d’une bibliothèque de classes portable (PCL) unique pour authentifier les utilisateurs et obtenir des jetons pour l’API Graph Azure AD ;
-	recherche, dans un répertoire, d’utilisateurs correspondant à un UPN (nom d’utilisateur principal) donné.

Pour générer l’application fonctionnelle complète, vous devez :

2. configurer votre environnement de développement Xamarin ;
2. inscrire votre application auprès d’Azure AD ;
3. installer et configurer la bibliothèque ADAL ;
5. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Pour commencer, téléchargez [la structure du projet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Chaque option est une solution Visual Studio 2013. Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## *0. Configuration de votre environnement de développement Xamarin*
Il existe différentes façons de configurer Xamarin en fonction des plateformes spécifiques que vous souhaitez cibler. Dans la mesure où ce didacticiel inclut des projets pour iOS, Android et Windows, nous avons choisi d’utiliser Visual Studio 2013 et l’[hôte de génération Xamarin.iOS](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/), qui requièrent les éléments suivants : - Un ordinateur Windows pour exécuter Visual Studio et les applications Windows - Un ordinateur OSX (si vous souhaitez être en mesure d’exécuter l’application iOS) - Un abonnement professionnel à Xamarin (une [version d’évaluation gratuite](http://developer.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial/) est suffisante) - [Xamarin pour Windows](https://xamarin.com/download), qui inclut l’intégration Visual Studio (recommandé pour cet exemple), Xamarin.iOS et Xamarin.Android. - [Xamarin pour OS X](https://xamarin.com/download), qui inclut Xamarin.iOS (et l’hôte de génération Xamarin.iOS), Xamarin.Android, Xamarin.Mac et Xamarin Studio.

Nous vous recommandons de commencer par la [page de téléchargement de Xamarin](https://xamarin.com/download) et d’installer Xamarin sur votre Mac et votre PC. Si vous ne disposez pas de ces deux ordinateurs, vous pouvez toujours exécuter l’exemple, mais certains projets devront être omis. Suivez les [guides d’installation détaillée](http://developer.xamarin.com/guides/cross-platform/getting_started/installation/) pour iOS et Android et, si vous souhaitez en savoir plus sur les options disponibles pour le développement, consultez le guide [Création d’applications multiplateformes](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/). Pour le moment, vous n’avez pas besoin de configurer un appareil pour le développement, ni de disposer d’un abonnement au programme pour développeurs Apple (à moins, bien sûr, que vous souhaitiez exécuter l’application iOS sur un appareil).

Une fois vous avez terminé la configuration nécessaire, ouvrez la solution dans Visual Studio pour commencer. Vous trouverez six projets : cinq projets propres à la plateforme et une bibliothèque de classes portable partagée par toutes les plateformes, `DirectorySearcher.cs`.


## *1. Inscription de l’application Directory Searcher*
Pour autoriser votre application à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD :

-	Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
-	Cliquez sur **Active Directory** dans la partie de gauche.
-	Sélectionnez un client dans lequel inscrire l’application.
-	Cliquez sur l’onglet **Applications**, puis sur **Ajouter** dans le menu déroulant inférieur.
-	Suivez les invites et créez une **application cliente native**.
    -	Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	L’**URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons. Entrez une valeur, par exemple `http://DirectorySearcher`.
-	Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet **Configurer**, car vous en aurez besoin dans les sections suivantes.
- Toujours sous l’onglet **Configurer**, cherchez la section Autorisations pour d’autres applications. Pour l’application Azure Active Directory, ajoutez l’autorisation **Accéder au répertoire de l’organisation** sous **Autorisations déléguées**. Cela permet à votre application d’interroger l’API Graph concernant les utilisateurs.

## *2. Installation et configuration de la bibliothèque ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité. Pour que la bibliothèque ADAL puisse communiquer avec Azure AD, vous devez lui fournir des informations concernant l’inscription de votre application. - Commencez par ajouter la bibliothèque ADAL à chacun des projets dans la solution, à l’aide de la console du gestionnaire de package.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.Windows -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.WindowsPhone -IncludePrerelease
`

- Notez que deux références de bibliothèque sont ajoutées à chaque projet : la partie de la bibliothèque PCL de la bibliothèque ADAL et une partie spécifique de la plateforme.

-	Dans le projet DirectorySearcher Lib, ouvrez `DirectorySearcher.cs`. Modifiez les valeurs de membre de classe afin de refléter les valeurs que vous avez saisies dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	`tenant` est le domaine de votre client Azure AD, par exemple, contoso.onmicrosoft.com.
    -	`clientId` est l’ID client de votre application que vous avez copié à partir du portail.
    - `returnUri` est l’URI de redirection que vous avez entré dans le portail, par exemple, `http://DirectorySearcher`.

## *3. Utilisation de la bibliothèque ADAL pour obtenir des jetons à partir d’AAD*
*Presque* toute la logique d’authentification de l’application réside dans `DirectorySearcher.SearchByAlias(...)`. Tout ce qui est nécessaire dans les projets propres à la plateforme est de transmettre un paramètre contextuel à la PCL `DirectorySearcher`.

- Tout d’abord, ouvrez `DirectorySearcher.cs` et ajoutez un nouveau paramètre à la méthode `SearchByAlias(...)`. `IPlatformParameters` est le paramètre contextuel qui encapsule les objets spécifiques de la plateforme que la bibliothèque ADAL doit authentifier.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	Ensuite, initialisez `AuthenticationContext`, classe principale de la bibliothèque ADAL. C’est à ce moment que vous fournissez à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD. Appelez ensuite `AcquireTokenAsync(...)`, qui accepte l’objet `IPlatformParameters` et appelle le flux d’authentification nécessaire pour renvoyer un jeton à l’application.

```C#
...
AuthenticationResult authResult = null;

try
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
}
...
```
- `AcquireTokenAsync(...)` tente tout d’abord de renvoyer un jeton pour la ressource demandée (l’API Graph dans ce cas) sans inviter l’utilisateur à entrer ses informations d’identification (via la mise en cache ou l’actualisation des anciens jetons). Le cas échéant seulement, il affiche la page de connexion d’Azure AD à l’utilisateur avant d’acquérir le jeton demandé.


- Vous pouvez ensuite joindre le jeton d’accès à la demande de l’API Graph, dans l’en-tête d’autorisation :

```C#
...
request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Le rôle de la PCL `DirectorySearcher` et du code lié à l’identité de votre application s’arrête là. Il ne reste qu’à appeler la méthode `SearchByAlias(...)` dans les affichages de chaque plateforme et, le cas échéant, d’ajouter le code permettant de gérer correctement le cycle de vie de l’interface utilisateur.

####Android :
- Dans `MainActivity.cs`, ajoutez un appel à `SearchByAlias(...)` dans le gestionnaire de clic de bouton :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Vous devez également substituer la méthode du cycle de vie `OnActivityResult` pour rediriger toute authentification vers la méthode appropriée. La bibliothèque ADAL fournit une méthode d’assistance pour cela dans Android :

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Bureau Windows :
- Dans `MainWindow.xaml.cs`, appelez simplement `SearchByAlias(...)` en passant un `WindowInteropHelper` dans l’objet `PlatformParameters` du bureau :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS :
- Dans `DirSearchClient_iOSViewController.cs`, l’objet iOS `PlatformParameters` se réfère simplement au contrôleur d’affichage :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows Store
- Dans Windows Store, ouvrez `MainPage.xaml.cs` et implémentez la méthode `Search`, qui utilise une méthode d’assistance dans un projet partagé afin de mettre à jour l’interface utilisateur en fonction des besoins.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters(PromptBehavior.Auto, false));
```

####Windows Phone
- Dans Windows Phone, ouvrez `MainPage.xaml.cs` et implémentez la méthode `Search`, qui utilise la même méthode d’assistance dans un projet partagé afin de mettre à jour l’interface utilisateur.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters());
```

Félicitations ! Vous disposez maintenant d’une application Xamarin fonctionnelle capable d’authentifier les utilisateurs et d’appeler en toute sécurité les API web à l’aide d’OAuth 2.0 sur cinq plateformes différentes. Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs. Exécutez votre application DirectorySearcher et connectez-vous avec l’un de ces utilisateurs. Recherchez d’autres utilisateurs en fonction de leur UPN.

La bibliothèque ADAL facilite l’intégration des fonctionnalités d’identité communes dans votre application. Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore. La seule chose que vous devez vraiment connaître est un appel unique d’API : `authContext.AcquireToken*(…)`.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Vous pouvez à présent aborder d’autres scénarios d’identité. Par exemple :

[Sécurisation d’une API web .NET avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez : - [AzureADSamples sur GitHub >>](https://github.com/AzureAdSamples) - [CloudIdentity.com >>](https://cloudidentity.com) - Documentation Azure AD sur [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=62-->