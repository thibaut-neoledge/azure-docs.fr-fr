<properties pageTitle="Utilisation d'Azure Key Vault à partir d'une application web | Présentation" description="Utilisez ce didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d'une application web." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Utilisation d'Azure Key Vault à partir d'une application web #

## Introduction  
Utilisez ce didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d'une application web. Celui-ci vous guide dans le processus d'obtention d'une clé secrète à partir d'Azure Key Vault afin de pouvoir être utilisé dans votre application web.

**Durée estimée :** 15 minutes


Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)

## Configuration requise 

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- un URI pour une clé secrète dans Azure Key Vault,
- un ID client et une clé secrète client pour une application web enregistrés avec Azure Active Directory qui a accès à votre Key Vault,
- une application web. Nous afficherons les étapes d'une application ASP.NET MVC déployée dans Azure en tant qu'application web. 

> [AZURE.NOTE]Il est essentiel que vous ayez effectué les étapes répertoriées dans [Prise en main d'Azure Key Vault](key-vault-get-started.md) pour ce didacticiel afin que vous ayez l'URI pour une clé secrète et un ID client ainsi qu'une clé secrète client pour une application web.

L'application web qui accédera à Key Vault est celle qui est enregistrée dans Azure Active Directory et est autorisée à accéder à votre Key Vault. Si cela n'est pas le cas, revenez à Inscrire une Application dans le didacticiel de prise en main et répétez les étapes répertoriées.

Ce didacticiel est conçu pour les développeurs web qui comprennent les principes fondamentaux de création d'applications web sur Azure. Pour plus d'informations sur Azure Web Apps, consultez [Vue d'ensemble de Web Apps](../app-service-web-overview.md).



## <a id="packages"></a>Ajout de packages NuGet ##
Il existe trois packages que votre application web doit avoir installés.

- Bibliothèque d'authentification Active Directory : contient des méthodes pour interagir avec Azure Active Directory et gérer l'identité de l'utilisateur
- Bibliothèque Azure Key Vault : contient des méthodes pour interagir avec Azure Key Vault


Trois de ces packages peuvent être installés à l'aide de la Console du Gestionnaire de Package à l'aide de la commande Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>Modification du fichier Web.Config ##
Il existe trois paramètres d'application qui doivent être ajoutés au fichier web.config comme suit.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Si vous ne souhaitez pas héberger votre application comme une application web d'Azure, vous devriez ajouter les valeurs réelles ID client, Clé secrète client et une clé secrèteURI au fichier Web.config. Sinon, laissez ces valeurs factices ; nous ajouterons les valeurs réelles dans le portail Azure pour un niveau de sécurité supplémentaire.


## <a id="gettoken"></a>Ajout d'une méthode pour obtenir un jeton d'accès ##
Pour utiliser l'API Key Vault, vous avez besoin d'un jeton d'accès. Le client Key Vault gère les appels de l'API Key Vault, mais vous devez lui fournir une fonction qui lui fait obtenir le jeton d'accès.

Voici le code pour obtenir un jeton d'accès avec Azure Active Directory. Ce code peut être placé n'importe où dans votre application. Vous pouvez par exemple ajouter une classe Utils ou EncryptionHelper.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>Récupération de la clé secrète dans Application Start ##
Nous avons maintenant besoin du code pour appeler l'API Key Vault et récupérer la clé secrète. Le code suivant peut être placé n'importe où tant qu'il est appelé avant que vous ne deviez l'utiliser. Ce code a été créé dans l'événement Application Start dans Global.asax afin qu'il s'exécute au démarrage et rende la clé secrète disponible à l'application.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Ajout de paramètres d'application dans le portail Azure (facultatif) ##
Si vous avez une application web d'Azure, vous pouvez maintenant ajouter les valeurs réelles pour AppSettings dans le portail Azure. Ce faisant, les valeurs réelles ne seront pas dans le fichier Web.config, mais protégés via le portail où vous avez des fonctionnalités de contrôle d'accès distinctes. Ces valeurs seront remplacées par les valeurs que vous avez entrées dans votre fichier Web.config. Assurez-vous que les noms sont identiques.

![Paramètres de l'application affichés dans le portail Azure][1]



## <a id="next"></a>Étapes suivantes ##


Pour les références de programmation, consultez la page [Référence de l'API cliente C# du coffre de clés](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=58_postMigration-->