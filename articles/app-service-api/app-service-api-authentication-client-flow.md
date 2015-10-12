<properties 
	pageTitle="Appeler une application API à partir d'un client authentifié" 
	description="Découvrez comment appeler une application API Azure à partir d'un client d'application web authentifié par Azure Active Directory." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="tdykstra"/>

# Appeler une application API Azure à partir d'un client d'application web authentifié par Azure Active Directory

## Vue d’ensemble

Ce didacticiel montre comment appeler une application API protégée par Azure Active Directory (AAD) à partir d'une application web également protégée par AAD. Á l’issue du didacticiel, vous aurez une application web et une application API en cours d'exécution dans votre abonnement Azure. L'application web affichera les données qu'elle obtient en appelant l'application API, comme illustré dans la capture d'écran suivante.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

Vous allez apprendre à transmettre les informations d'identification AAD de l'application web à l'application API afin que l'utilisateur ne soit plus invité à se connecter par l'application API.

Vous allez effectuer les étapes suivantes :

- Créer une application API et la configurer pour utiliser l'authentification AAD.
- Créer une application web et la configurer pour utiliser l'authentification AAD.
- Ajouter du code à l'application web pour appeler l'application API protégée.
- Déployer l'application web et l'application API sur Azure.
- Vérifier par des tests que l'application web peut appeler l'application API.

### Authentification du flux client

Le code que vous ajoutez à l'application web implémente un processus appelé authentification du [flux client](../app-service/app-service-authentication-overview.md#client-flow). Le diagramme suivant illustre le processus d'obtention du jeton d'accès AAD et d’échange contre un jeton d'application API (Zumo).

![](./media/app-service-api-authentication-client-flow/clientflow.png)

Si vous n'êtes pas familiarisé avec le rôle de la passerelle d'application API dans l'authentification pour les applications API, consultez la section [Authentification pour les applications API et les applications mobiles](../app-service/app-service-authentication-overview.md).

## Composants requis

Avant de commencer ce didacticiel, assurez-vous que [Visual Studio 2015](https://www.visualstudio.com/) et le [kit de développement logiciel Azure pour .NET](http://go.microsoft.com/fwlink/?linkid=518003) sont installés. Les mêmes instructions sont également valables pour Visual Studio 2013.

Ce didacticiel suppose que vous savez utiliser les projets web dans Visual Studio.

## Créer et sécuriser une application API avec AAD

1. Créer ou télécharger un projet d'application API.
 
	* Pour créer un projet, suivez les instructions dans la section [Créer une application API](app-service-dotnet-create-api-app.md).

	* Pour télécharger un projet, allez sur le [référentiel GitHub ContactsList](https://github.com/tdykstra/ContactsList).

	Vous disposez maintenant d'un projet d'API web qui renvoie les données de contacts.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Déployer le projet d’application API vers une nouvelle application API dans Azure.

	Suivez les instructions dans la section [Déployer une application API](app-service-dotnet-deploy-api-app.md).

	Dans le [portail Azure en version préliminaire], le panneau **Définition d'API** de l'application API affiche à présent les méthodes Get et Post du projet d'API web que vous avez déployé.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Sécuriser l'application API à l'aide d'Azure Active Directory (AAD) en tant que fournisseur d'identité.
 
	Suivez les instructions pour AAD dans la section [Protéger une application API](app-service-api-dotnet-add-authentication.md). Vous n'avez pas besoin de faire la section **Utiliser Postman pour envoyer une demande Post** du didacticiel.

	Lorsque vous avez terminé, le panneau **Azure Active Directory** de l’application API dans le [portail Azure en version préliminaire] possède l’ID client de l'application AAD et votre locataire AAD.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	Et l'onglet **Configurer** de l’application AAD dans le [portail Azure] possède l’URL ID de l’application API et l’URL de réponse.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## Créer et sécuriser une application web avec AAD

Dans cette section, vous téléchargez et configurez un projet web configuré pour l'authentification AAD. Le projet a une page **À propos** qui affiche des informations sur les revendications pour l'utilisateur connecté.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

Plus tard, vous modifierez cette page en ajoutant une section pour afficher les informations de contact extraites de l'application API.

1. Téléchargez le projet web à partir du [référentiel WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)
 
2. Suivez les instructions sur l’**Exécution de l'exemple** dans le [fichier Lisez-moi](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), avec les exceptions suivantes :
 
	* Vous pouvez utiliser Visual Studio 2015 bien que le fichier Lisez-moi indique d'utiliser Visual Studio 2013. 

	* Utilisez l'application AAD déjà créée au lieu d’en créer une nouvelle.
 
	* Conservez l’**URI ID d'application** que vous avez déjà pour l'application AAD. (Ne pas modifier au format spécifié dans le fichier Lisez-moi.)
	
	* Modifiez les autres paramètres d'application AAD comme indiqué ; plus précisément, définissez les URL d'authentification et de réponse vers l’URL de base pour l’application exemple.

Vous conservez l’URI ID d'application que vous avez créé pour l'application API afin de pouvoir utiliser le même jeton d’accès AAD pour l'application web et pour l'application API. Si vous avez modifié l'URI ID d'application au format indiqué par le fichier Lisez-moi, cela fonctionnerait pour l'accès à l'application web, mais pas pour l'application API. Vous ne pourriez pas transmettre le jeton AAD à la passerelle d'application API pour obtenir un jeton Zumo, car la passerelle attend un jeton pour un URI ID d'application composé de l'URL de la passerelle suivie de « /login/aad ».

## Ajout du code client généré pour l’application API

Dans cette section, vous ajoutez du code généré automatiquement pour une interface typée pour l’appel de l'application API.

8.	Dans l’**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet WebApp-GroupClaims-DotNet, puis cliquez sur **Ajouter > Client d’application API Azure**.

9.	Dans la boîte de dialogue **Ajouter un client d’application API Microsoft Azure**, sélectionnez l'application API sécurisée avec AAD.

	Une fois la génération de code terminée, un nouveau dossier s'affiche dans l'**Explorateur de solutions**, avec le nom de l'application API. Ce dossier contient le code qui implémente les classes et les modèles de données du client.

10. Résolvez les références ambiguës provoquées par le code généré dans *ContactsList/ContactsExtensions.cs* : modifiez les deux instances de `Task.Factory.StartNew` à `System.Threading.Tasks.Task.Factory.StartNew`.
 
## Ajoutez du code pour échanger le jeton AAD contre un jeton Zumo

1.	Dans HomeController.cs, ajoutez `using` instructions pour le kit de développement logiciel App Service et le sérialiseur JSON.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. Ajoutez des constantes pour l'URL de la passerelle et l'URI ID de l'application AAD. Assurez-vous que l'URL de la passerelle est https, pas http.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	Ajoutez une méthode qui obtient un objet client pour appeler l'application API.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	Dans ce code, `ConfigHelper.Authority` correspond à « https://login.microsoftonline.com/{votre locataire} », par exemple : « https://login.microsoftonline.com/contoso.onmicrosoft.com ».

2.	Ajoutez le code juste avant l’instruction `return View()` à la fin de la méthode `About` pour appeler l'application API. (Dans l'étape suivante, vous ajouterez du code à l’affichage `About` pour afficher les données renvoyées.)

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. Dans *Views/Home/About.cshtml*, ajoutez le code juste après le titre `h2` pour afficher les informations de contact.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. Dans le fichier Web.config de l'application, ajoutez la redirection de liaison suivante après la balise `<assemblyBinding>` d'ouverture.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	Sans cette redirection de liaison, l'application échouera car le kit de développement logiciel App Service inclut une dépendance par rapport à System.Net.Http.Primitives version 1.5.0.0, alors que la version utilisée par le projet est 4.2.28.0.
 
3. Suivez les instructions dans la section **Déploiement de cet exemple sur Azure** dans le [fichier Lisez-moi](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).

5. Exécutez l'application.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. Cliquez sur **Se connecter**, puis entrez les informations d'identification d'un utilisateur dans le domaine AAD que vous utilisez pour cette application.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. Cliquez sur **À propos**.

	Le code que vous avez ajouté au contrôleur À propos et à l’affichage s'exécute et montre que vous vous êtes correctement authentifié auprès de l'application API et que vous avez correctement appelé sa méthode Get.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## Résolution de problèmes

### Erreurs HTTP 400 

Assurez-vous que l'URL de la passerelle est https, pas http. Si vous la copiez directement à partir du portail Azure en version préliminaire, elle peut spécifier http.
 
### Erreurs SQL Server

L'application nécessite l'accès à une base de données SQL Server identifiée par la chaîne de connexion GroupClaimContext. Assurez-vous que la chaîne de connexion dans le site déployé pointe vers une instance de base de données SQL. Vous pouvez placer la chaîne de connexion correcte dans le fichier Web.config déployé ou dans les paramètres de configuration d'exécution Azure.

## Remerciements

Merci à Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) pour son aide dans le développement de ce didacticiel.

## Étapes suivantes

Vous avez vu comment procéder à l'authentification du flux client pour les applications API Service App. Pour plus d’informations sur d’autres façons de gérer l’authentification dans les applications API, consultez la page [Authentification pour les applications API et les applications mobiles](../app-service/app-service-authentication-overview.md).

[portail Azure]: https://manage.windowsazure.com/
[portail Azure en version préliminaire]: https://portal.azure.com/

<!---HONumber=Oct15_HO1-->