<properties 
	pageTitle="Application API ASP.NET de connecteur SaaS personnalisée dans Azure App Service" 
	description="Apprenez à écrire du code qui se connecte à une plateforme SaaS à partir d’une application API et à appeler l’application API à partir d’un client .NET." 
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
	ms.date="07/01/2015" 
	ms.author="tdykstra"/>

# Connexion à une plateforme SaaS à partir d’une application API ASP.NET dans Azure App Service

## Vue d'ensemble

Ce didacticiel explique comment coder et configurer une [application API](app-service-api-apps-why-best-platform.md) qui se connecte à une [plateforme SaaS (Software-as-a-Service)](../app-service/app-service-authentication-overview.md#obotosaas) à l’aide du [Kit de développement logiciel (SDK) d’application API App Service pour .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/). Il indique également comment appeler l’application API à partir d’un client .NET à l’aide du [Kit de développement d’application (SDK) App Service pour .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService). Lorsque vous l’aurez terminé, vous aurez donc un client d’application console .NET appelant une application API .NET s’exécutant dans Azure App Service. L’application API appelle l’API Dropbox et renvoie une liste de fichiers et de dossiers dans le compte Dropbox de l’utilisateur.

Si vous voulez utiliser une autre méthode que l’écriture de code appelant une API SaaS directement depuis une application API personnalisée, vous pouvez appeler une [application de connecteur API](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) préconfigurée. Pour plus d’informations sur la méthode à suivre, consultez la page [Déployer et configurer une application API de connecteur SaaS](app-service-api-connnect-your-app-to-saas-connector.md).

Ce didacticiel vous familiarise avec les procédures suivantes :

* Création d’un projet d’application API dans Visual Studio 
* Configuration du fichier *apiapp.json* pour que l’application API puisse se connecter au service Dropbox
* Ajout de code appelant Dropbox et renvoyant les résultats
* Création d’une application API dans Azure
* Déploiement du projet dans l’application API
* Configuration de l’application
* Configuration de la passerelle
* Création d’un client de test
* Exécution du client de test

## Composants requis

Ce didacticiel part des principes suivants :

* Vous avez terminé les didacticiels [Créer une application API ASP.NET dans Azure App Service](app-service-dotnet-create-api-app.md) et [Déployer une application API dans Azure App Service](app-service-dotnet-deploy-api-app.md).
  
* Vous avez une connaissance de base de l’architecture de la passerelle Azure App Service pour l’authentification, comme indiqué dans [Authentification pour les applications d’API et les applications mobiles dans Azure App Service](app-service-authentication-overview.md).

* Vous savez comment fonctionnent les applications API dans le portail Azure en version préliminaire, comme indiqué dans [Accéder aux panneaux Application API et Passerelle](app-service-api-manage-in-portal.md#navigate).

## Création du projet d’application API
 
Lorsque les instructions vous invitent à entrer un nom pour le projet, entrez *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## Configuration du fichier *apiapp.json*

La plateforme SaaS recevant les appels émis par l’application API doit être indiquée dans le fichier *apiapp.json*.

1. Ouvrez le fichier *apiapp.json*, puis ajoutez une propriété `authentication`, comme indiqué ici (vous devrez également ajouter une virgule après la propriété précédente) :

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	Une fois ceci terminé, le fichier apiapp.json doit ressembler à ceci :

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. Enregistrez le fichier .

Le fait de configurer la propriété `authentication` a deux effets :

* Le portail affiche l’interface utilisateur dans le volet d’application API. Vous pouvez donc y entrer les valeurs ID client et Clé secrète client de la plateforme SaaS.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* Ainsi, l’application API peut récupérer le jeton d’accès du fournisseur SaaS à partir de la passerelle pour l’utiliser lors de l’appel de l’API du fournisseur SaaS.

La propriété `authentication` est un tableau, mais cette version préliminaire ne prend pas en charge la spécification de plusieurs fournisseurs.

Pour obtenir la liste des plateformes prises en charge, consultez la page [Obtenir le consentement de l’utilisateur pour accéder à d’autres ressources](../app-service/app-service-authentication-overview.md#obotosaas).

Vous pouvez également spécifier des étendues, comme dans cet exemple :

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

Les étendues sont disponibles en fonction de chaque fournisseur SaaS et se trouvent dans le portail développeur du fournisseur.

## Ajout de code appelant Dropbox

1. Installez le package NuGet [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) dans le projet SimpleDropbox.

	* Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet > Console du Gestionnaire de package**.

	* Dans la fenêtre **Console du Gestionnaire de package**, entrez cette commande :
	 
			install-package DropboxRestAPI  

1. Ouvrez *Controllers\\ValuesController.cs*, puis remplacez tout le code du fichier par le code suivant.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	Avant que le client appelle cette méthode, l’utilisateur s’est connecté à Dropbox et a autorisé l’application API à accéder à son compte Dropbox. Dropbox accuse réception de cette autorisation en fournissant un jeton d’accès à la passerelle App Service. Ce code récupère le jeton à partir de la passerelle et l’inclut dans un appel vers l’API Dropbox, comme indiqué dans les étapes 6 et 7 du schéma ci-dessous.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. Créez le projet.

## Créer une application API dans Azure

Dans cette section, vous utilisez l’Assistant **Publier le site Web** de Visual Studio pour créer une application API dans Azure. Lorsque les instructions vous invitent à entrer un nom pour l’application API, entrez *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## Déploiement de votre code

Vous utilisez le même Assistant **Publier le site Web** pour déployer votre code vers la nouvelle application API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Configuration de l’authentification pour les appels entrants

Pour qu’Azure App Service autorise les appels sortants authentifiés à partir de l’application API, celle-ci doit également exiger que les appels entrants proviennent d’utilisateurs authentifiés. Ceci n’est pas obligatoire pour la procédure OAuth 2.0 globale, mais uniquement pour l’architecture de la passerelle App Service, qui est actuellement implémentée.

Les captures d’écran de cette section montrent une application API ContactsList, mais le processus est identique pour l’application API SimpleDropbox que vous créez dans ce didacticiel.

### Configuration de l’application API pour exiger l’authentification des appels entrants

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### Configuration d’un fournisseur d’identité dans la passerelle

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Configuration de l’authentification pour les appels sortants

Pour que votre application API puisse appeler l’API Dropbox, vous devez échanger les paramètres entre votre application API et une application Dropbox que vous créez sur le site de développeur Dropbox.

### Création d’une application Dropbox sur le site Dropbox.com

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Échange de paramètres entre Dropbox et votre application API

Au cous des étapes suivantes, nous partons du principe que vous utilisez une application API de connecteur Dropbox, mais les procédures à suivre et l’interface utilisateur sont les mêmes pour l’application API SimpleDropbox que vous avez créée dans ce didacticiel.

> **Remarque :** si les champs ID client et Clé secrète client pour Dropbox ne sont pas affichés dans le volet **Authentification** de l’application API SimpleDropbox contrairement à ce qui est affiché sur la capture d’écran, veuillez vérifier que vous avez bien redémarré la passerelle, comme indiqué après le déploiement du projet d’application API vers l’application API. La valeur « dropbox » située dans la propriété `authentication` du fichier *apiapp.json* que vous avez déployé précédemment déclenche l’affichage de ces champs dans le portail.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## Création d’un client de test

Cette section explique comment créer un projet d’application console qui utilise le code client généré par Visual Studio pour appeler l’application API SimpleDropbox. L’application console instancie un contrôle de navigateur Windows Forms pour gérer l’interaction utilisateur avec la passerelle et les pages web de connexion à Dropbox.

### Création du projet

1. Dans Visual Studio, créez un projet d’application console et nommez-le *SimpleDropboxTest*.

2. Définissez une référence pointant vers System.Windows.Forms.
 
	* Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis cliquez sur **Ajouter une référence**.

	* Cochez la case située à gauche de **System.Windows.Forms**, puis cliquez sur **OK**.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	L’application console utilise l’assembly Windows Forms pour instancier un contrôle de navigateur lorsqu’elle doit autoriser l’utilisateur à se connecter à la passerelle et à Dropbox.

### Ajout du code client généré

Les captures d’écran de cette section montrent un projet ContactsList et une application API, mais pour ce didacticiel, veuillez sélectionner le projet SimpleDropboxTest et l’application API SimpleDropbox.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Ajouter du code pour appeler l'application API

3. Ouvrez *Program.cs* et remplacez le code existant par le suivant :
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. Remplacez {gateway url} par l’URL de votre passerelle.
 
	Cette URL se trouve dans le volet **Passerelle** du portail :

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **Important** : assurez-vous que l’URL de la passerelle commence par `https://` et non par `http://`. **Si vous copiez http:// à partir du portail, vous devez le remplacer par https:// après l’avoir collé dans le code.**

### Explication du code

Cette application console est conçue pour utiliser une quantité minimale de code pour illustrer la procédure suivie par une application cliente. Généralement, une application de production n’est pas une application console et implémente une gestion et une journalisation des erreurs.

Voici un aperçu des effets du code :

* Il déclenche l’ouverture d’un navigateur suivant l’URL de connexion à la passerelle pour le fournisseur d’identité configuré (ici, Azure Active Directory). 
	 
* Il gère l’URL de réponse prévue après la connexion de l’utilisateur, en extrayant l’ID utilisateur et le jeton Zumo et en les fournissant à l’objet client App Service.

* Il utilise l’objet client App Service pour récupérer une URL de passerelle redirigeant le lien Dropbox pour la connexion et l’autorisation. Voir l’étape 1 du schéma.

* Il ouvre un navigateur suivant l’URL d’autorisation de la passerelle. Le navigateur est redirigé vers le lien d’autorisation et de connexion à Dropbox. Voir l’étape 2 du schéma.
	 
* Il ferme le navigateur après la connexion de l’utilisateur et autorise l’accès à Dropbox.com. Voir l’étape 3 du schéma.
 
* Il appelle l’application API. Voir l’étape 5 du schéma. (L’étape 4 se produit en arrière-plan entre Dropbox.com et la passerelle, les étapes 6 et 7 se déroulent à partir de l’application API, et non plus depuis le client.)

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

Informations complémentaires :

* L’attribut `STAThread` de la méthode `Main` est requis par le contrôle du navigateur web et n’est pas associé à la configuration ou à l’appel de l’application API.

* L’URL de connexion à la passerelle indiquée se termine dans `/aad` pour Azure Active Directory.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	Voici les valeurs à utiliser pour les autres fournisseurs :
	* "microsoftaccount"
	* "facebook"
	* "twitter"
	* "google"
<br/><br/>

* Le second paramètre de la méthode `GetConsentLinkAsync()` est l’URL de rappel vers laquelle redirige le serveur d’autorisation une fois que l’utilisateur s’est connecté à Dropbox et a autorisé l’accès à son compte.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	Pour ce paramètre, vous devez généralement spécifier la prochaine page web à laquelle l’utilisateur accède dans l’application cliente. Comme ce code de démonstration est dans une application console, il n’existe aucune page vers laquelle accéder et le code indique l’URL de passerelle simplement pour définir une page d’accueil pratique.

	L’application cliente doit vérifier qu’elle est redirigée vers cette URL et qu’il n’y a aucun message d’erreur. Si le processus de connexion ou d’autorisation échoue, la chaîne de requête de l’URL de redirection peut contenir un message d’erreur. Pour plus d’informations, consultez la section [Résolution des problèmes](#troubleshooting).

## Test

1. Exécutez l’application console SimpleDropboxTest.

2. Dans la première page d’ouverture de session, connectez-vous en utilisant vos informations d’identification Azure Active Directory (ou les informations d’identification d’un autre fournisseur d’identité comme Google ou Twitter si cela correspond à votre configuration de la passerelle).

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. Sur la page de connexion à Dropbox.com, connectez-vous à l’aide de vos informations d’identification Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. Sur la page d’autorisation Dropbox, autorisez l’application à accéder à vos données.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	L’application console appelle ensuite l’application API et renvoie une liste des fichiers dans votre compte Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## Résolution de problèmes

Cette section traite des sujets suivants :

* [Erreur HTTP 405 après la connexion à la passerelle](#405)
* [Erreur HTTP 400 au lieu de la page de connexion à Dropbox](#400)
* [Erreur HTTP 403 lors de l’appel de l’application API](#403)

### <a id="405"></a>Erreur HTTP 405 après la connexion à la passerelle

Si vous obtenez une erreur HTTP 405 lorsque le code appelle GetConsentLinkAsync, veuillez vérifier que vous avez bien utilisé https://, et non pas http:// dans l’URL de la passerelle.

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

L’erreur 405 Méthode non autorisée est reçue lorsque le client tente d’effectuer une requête POST HTTP non-SSL, que la passerelle redirige vers *https://* et que la redirection provoque une demande GET. L’URL de récupération d’un lien d’autorisation accepte uniquement les demandes POST.

### <a id="400"></a>Erreur HTTP 400 au lieu de la page de connexion à Dropbox

Veuillez vérifier que l’**ID client** entré dans le volet **Authentification** de l’application API est correct, et qu’il n’y a pas d’espace superflu au début ou à la fin.

### <a id="403"></a>Erreur HTTP 403 lors de l’appel de l’application API

* Veuillez vérifier que le **Niveau d’accès** de l’application API est défini sur **Public (authentifié)** et non sur **Interne**.

* Veuillez vérifier que la **Clé secrète client** entrée dans le volet **Authentification** de l’application API est correcte, et qu’il n’y a pas d’espace superflu au début ou à la fin.

L’URL de redirection après la connexion à Dropbox peut ressembler à cet exemple :

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

Supprimez %3d%3d à la fin de la valeur querystring `error`, il s’agit d’une chaîne encodée en base64 valide. Si vous décodez la chaîne, vous obtenez le message d’erreur suivant :

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## Étapes suivantes

Vous avez appris à coder et à configurer une application API se connectant à une plateforme SaaS. Pour consulter des liens vers d’autres didacticiels sur la gestion de l’authentification dans les applications API, consultez [la section Étapes suivantes de la page Authentification pour les applications d’API et les applications mobiles dans Azure App Service](../app-service/app-service-authentication-overview.md#next-steps).

[Azure preview portal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/

<!---HONumber=July15_HO5-->