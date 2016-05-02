<properties
	pageTitle="Utilisation d'Azure Key Vault à partir d'une application web | Microsoft Azure"
	description="Utilisez ce didacticiel pour vous aider à apprendre comment utiliser Azure Key Vault à partir d'une application web."
	services="key-vault"
	documentationCenter=""
	authors="adhurwit"
	manager=""
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="adhurwit"/>

# Utilisation d'Azure Key Vault à partir d'une application web #

## Introduction  
Utilisez ce didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d'une application web. Celui-ci vous guide dans le processus d'obtention d'une clé secrète à partir d'Azure Key Vault afin de pouvoir être utilisé dans votre application web.

**Durée estimée :** 15 minutes


Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)

## Configuration requise

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- un URI pour une clé secrète dans Azure Key Vault,
- un ID client et une clé secrète client pour une application Web enregistrés avec Azure Active Directory qui a accès à votre Key Vault,
- une application web. Nous afficherons les étapes d'une application ASP.NET MVC déployée dans Azure en tant qu'application web.

> [AZURE.NOTE]  Il est essentiel que vous ayez effectué les étapes répertoriées dans [Prise en main d'Azure Key Vault](key-vault-get-started.md) pour ce didacticiel afin que vous ayez l'URI pour une clé secrète et un ID client ainsi qu'une clé secrète client pour une application web.

L'application web qui accédera à Key Vault est celle qui est enregistrée dans Azure Active Directory et est autorisée à accéder à votre Key Vault. Si cela n'est pas le cas, revenez à Inscrire une Application dans le didacticiel de prise en main et répétez les étapes répertoriées.

Ce didacticiel est conçu pour les développeurs web qui comprennent les principes fondamentaux de création d'applications web sur Azure. Pour plus d'informations sur Azure Web Apps, consultez [Vue d'ensemble de Web Apps](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Ajout de packages NuGet ##
Deux packages doivent être installés pour votre application web.

- Bibliothèque d'authentification Active Directory : contient des méthodes pour interagir avec Azure Active Directory et gérer l'identité de l'utilisateur
- Bibliothèque Azure Key Vault : contient des méthodes pour interagir avec Azure Key Vault


Ces deux packages peuvent être installés à l’aide de la console du Gestionnaire de Package en utilisant la commande Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault


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

> [AZURE.NOTE] Le moyen le plus simple d'authentifier une application Azure AD est d’utiliser une clé secrète client et un ID client. L'utiliser dans votre application Web permet une séparation des tâches et davantage de contrôle sur la gestion de clés. Toutefois, vous devez placer la clé secrète client dans vos paramètres de configuration, ce qui, dans certains cas, peut s’avérer aussi dangereux que placer le secret que vous souhaitez protéger dans vos paramètres de configuration. Consultez la section ci-dessous pour plus d'informations sur l'utilisation d'un ID client et d’un certificat au lieu de l’ID client et de la clé secrète client pour authentifier l'application Azure AD.



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


## Authentification avec un certificat et non une clé secrète client
Il est également possible d'authentifier une application Azure AD à l'aide d'un ID client et d’un certificat, plutôt qu'un ID client et une clé secrète client. Voici les étapes pour utiliser un certificat dans une application Web Azure :

1. obtenir ou créer un certificat ;
2. associer le certificat à une application Azure AD ;
3. ajouter du code à votre application Web pour utiliser le certificat ;
4. ajouter un certificat à votre application Web.


**Obtenir ou créer un certificat** Dans notre cas, nous utiliserons un certificat de test. Voici quelques exemples de commandes que vous pouvez utiliser dans une invite de commandes de développeur pour créer un certificat. Accédez au répertoire où vous souhaitez créer les fichiers de certificat.

	makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
	pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Prenez note de la date de fin et du mot de passe pour le fichier .pfx (dans cet exemple : 31/07/2016 et test123). Vous en aurez besoin ultérieurement.

Pour plus d’informations sur la création d’un certificat de test, consultez [How to: Create Your Own Test Certificate](https://msdn.microsoft.com/library/ff699202.aspx)


**Associer le certificat à une application Azure AD** Maintenant que vous disposez d'un certificat, vous devez l'associer à une application Azure AD. Mais le portail de gestion Azure ne prend pas encore cela en charge. Vous devez utiliser Powershell. Voici les commandes à exécuter :

	$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

	PS C:\> $x509.Import("C:\data\KVWebApp.cer")

	PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

	PS C:\> $now = [System.DateTime]::Now

	# this is where the end date from the cert above is used
	PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

	PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

	PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

	PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToKeys all -ResourceGroupName 'contosorg'

	# get the thumbprint to use in your app settings
	PS C:\>$x509.Thumbprint

Après avoir exécuté ces commandes, vous pouvez voir l'application dans Azure AD. Si vous ne voyez pas l'application, recherchez « Applications my company owns » au lieu de « Applications my company uses ».

Pour plus d'informations sur les objets d'application AD Azure et ServicePrincipal, consultez [Objets principal du service et application](../active-directory/active-directory-application-objects.md).



**Ajouter du code à votre application Web pour utiliser le certificat** Nous allons maintenant ajouter du code à votre application Web pour accéder au certificat et l'utiliser pour l'authentification.

Tout d'abord vient le code d'accès au certificat.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Notez que l'emplacement StoreLocation est CurrentUser, et non LocalMachine. Et que nous choisissons « false » pour la méthode Find, car nous utilisons un certificat de test.


Vient ensuite le code qui utilise le CertificateHelper et crée un ClientAssertionCertificate, nécessaire pour l'authentification.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Voici le nouveau code pour obtenir le jeton d'accès. Cela remplace la méthode GetToken ci-dessus. Elle a un nom différent pour des raisons pratiques.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

J'ai inséré tout ce code dans la classe Utils de mon projet d’application Web pour plus de facilité d'utilisation.

La dernière modification de code a lieu dans la méthode Application\_Start. Nous devons tout d'abord appeler la méthode GetCert() pour charger le ClientAssertionCertificate. Nous modifions ensuite la méthode de rappel que nous transmettons lors de la création d'un nouveau KeyVaultClient. Notez que cela remplace le code que nous avions ci-dessus.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Ajouter un certificat à votre application Web** L’ajout d'un certificat à votre application Web est un processus simple en deux étapes. Connectez-vous au portail Azure et accédez à votre application Web. Sur le volet Paramètres de votre application Web, cliquez sur l'entrée « Custom domains and SSL ». Sur le nouveau panneau, vous pourrez télécharger le certificat que vous avez créé plus tôt, KVWebApp.pfx. Assurez-vous de vous souvenir du mot de passe pour le pfx.

![Ajout d'un certificat à une application Web dans le portail Azure][2]


La dernière chose que vous devez faire consiste à ajouter un paramètre d'application à votre application Web, nommé WEBSITE\_LOAD\_CERTIFICATES et avec la valeur *. Cela garantit que tous les certificats sont chargés. Si vous souhaitez charger uniquement les certificats que vous avez téléchargés, vous pouvez entrer une liste séparée par des virgules de leurs empreintes numériques.

Pour en savoir plus sur l'ajout d'un certificat à une application Web, consultez [Using Certificates in Azure Websites Applications](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)



## <a id="next"></a>Étapes suivantes ##


Pour les références de programmation, consultez la page [Référence de l'API cliente C# du coffre de clés](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png

<!---HONumber=AcomDC_0420_2016-->