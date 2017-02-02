---
title: "Authentification de l’accès client à un cluster | Microsoft Docs"
description: "Décrit comment authentifier l’accès client à un cluster Service Fabric et comment sécuriser les communications entre les clients et un cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 65775053918e12ef8881f417dacc0a63f080d093
ms.openlocfilehash: 6de98012e768abc7f8450e97648444a74474b5e9


---
# <a name="connect-to-a-secure-cluster"></a>Se connecter à un cluster sécurisé
Lorsqu’un client se connecte à un nœud de cluster Service Fabric, il peut être authentifié et une communication sécurisée peut être établie à l’aide de la sécurité par certificat ou d’Azure Active Directory (AAD). Cette authentification garantit que seuls les utilisateurs autorisés puissent accéder au cluster et aux applications déployées, et effectuer des tâches de gestion.  La sécurité par certificat ou AAD doit avoir été précédemment activé sur le cluster à sa création.  Pour plus d’informations sur les scénarios de sécurité des clusters, consultez [Sécurité des clusters](service-fabric-cluster-security.md). Si vous vous connectez à un cluster sécurisé avec des certificats, [configurez le certificat client](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) sur l’ordinateur qui se connecte au cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>Se connecter à un cluster sécurisé à l’aide de l’interface de ligne de commande Azure
Les commandes de l’interface de ligne de commande Azure ci-après expliquent comment se connecter à un cluster sécurisé. 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Se connecter à un cluster sécurisé à l’aide d’un certificat client
Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster. 

Si votre certificat est associé à des autorités de certification, vous devez ajouter le paramètre `--ca-cert-path` comme indiqué dans l’exemple suivant : 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si vous saisissez plusieurs autorités de certification, utilisez des virgules comme délimiteurs. 

Si le nom commun du certificat ne correspond pas au point de terminaison de connexion, vous pouvez utiliser le paramètre `--strict-ssl-false` pour ignorer la vérification. 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

Si vous souhaitez ignorer l’étape de vérification de l’autorité de certification, vous pouvez ajouter le paramètre ``--reject-unauthorized-false`` , comme l’indique la commande suivante :

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Pour vous connecter à un cluster sécurisé via un certificat auto-signé, utilisez la commande suivante, en supprimant ainsi la vérification du nom commun et de l’autorité de certification :

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

Une fois connecté, vous devez être en mesure d’[exécuter d’autres commandes d’interface de ligne de commande](service-fabric-azure-cli.md) pour interagir avec le cluster. 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-secure-cluster-using-powershell"></a>Se connecter à un cluster sécurisé avec PowerShell
Avant d’effectuer des opérations sur un cluster par le biais de PowerShell, établissez tout d’abord une connexion au cluster. La connexion au cluster est utilisée pour toutes les commandes suivantes dans la session PowerShell donnée.

### <a name="connect-to-an-unsecure-cluster"></a>Se connecter à un cluster non sécurisé

Pour vous connecter à un cluster non sécurisé, fournissez l’adresse de point de terminaison de cluster à la commande **Connect-ServiceFabricCluster** :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Se connecter à un cluster sécurisé à l’aide d’Azure Active Directory

Pour vous connecter à un cluster sécurisé qui utilise Azure Active Directory pour autoriser un accès administrateur de cluster, fournissez l’empreinte de certificat du cluster et utilisez l’indicateur *AzureActiveDirectory*.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Se connecter à un cluster sécurisé à l’aide d’un certificat client
Exécutez la commande PowerShell suivante pour vous connecter à un cluster sécurisé qui utilise des certificats clients pour autoriser l’accès administrateur. Fournissez l’empreinte de certificat du cluster, ainsi que l’empreinte numérique du certificat client qui dispose des autorisations pour la gestion de cluster. Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* est l’empreinte du certificat du serveur installé sur les nœuds du cluster. *FindValue* est l’empreinte du certificat du client administrateur.
Lorsque les paramètres sont renseignés, la commande ressemble à l’exemple suivant : 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>Se connecter à un cluster sécurisé à l’aide des API FabricClient
Le Kit de développement logiciel (SDK) Service Fabric fournit la classe [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) pour la gestion de cluster. 

### <a name="connect-to-an-unsecure-cluster"></a>Se connecter à un cluster non sécurisé

Pour vous connecter à un cluster à distance non sécurisé, créez une instance de FabricClient et fournissez l’adresse du cluster :

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Pour un code qui s’exécute à partir d’un cluster, par exemple dans un Reliable Service, créez un élément FabricClient *sans* spécifier l’adresse du cluster. FabricClient se connecte à la passerelle de gestion locale sur le nœud sur lequel le code est en cours d’exécution, évitant ainsi un tronçon réseau supplémentaire.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Se connecter à un cluster sécurisé à l’aide d’un certificat client

Les nœuds du cluster doivent présenter des certificats valides, dont le nom commun ou le nom DNS dans le SAN s’affichent dans la [propriété RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) définie sur [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Ce processus permet une authentification mutuelle entre le client et les nœuds du cluster.

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Se connecter à un cluster sécurisé à l’aide d’Azure Active Directory

La procédure suivante active Azure Active Directory pour l’identité du client et le certificat de serveur pour l’identité du serveur.

Pour utiliser le mode interactif qui affiche une boîte de dialogue de connexion interactive AAD :

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

FabricClient fc = new FabricClient(
    claimsCredentials,
    connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

Pour utiliser le mode silencieux sans intervention humaine :

(Cet exemple est basé sur Microsoft.IdentityModel.Clients.ActiveDirectory, Version : 2.19.208020213.

Reportez-vous à [Microsoft.IdentityModel.Clients.ActiveDirectory Namespace](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx) pour découvrir comment obtenir un jeton et plus d’informations)

```csharp
string tenantId = "c15cfcea-02c1-40dc-8466-fbd0ee0b05d2";
string clientApplicationId = "118473c2-7619-46e3-a8e4-6da8d5f56e12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob"
    );

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";
ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

FabricClient fc = new FabricClient(
   claimsCredentials,
   connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    string token = "";
    try
    {
        var authResult = authContext.AcquireToken(
            resource,
            clientId,
            new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
        token = authResult.AccessToken;
    }
    catch (AdalException ex)
    {
        Console.WriteLine("Get AccessToken failed: {0}", ex.Message);
    }

    return token;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Se connecter à un cluster sécurisé à l’aide de Service Fabric Explorer
Pour atteindre [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pour un cluster donné, dirigez votre navigateur vers :

`http://<your-cluster-endpoint>:19080/Explorer`

L'URL complète est également disponible dans le volet des éléments essentiels du cluster dans le portail Azure.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Se connecter à un cluster sécurisé à l’aide d’Azure Active Directory

Pour vous connecter à un cluster sécurisé avec AAD, dirigez votre navigateur vers :

`https://<your-cluster-endpoint>:19080/Explorer`

Vous êtes automatiquement invité à ouvrir une session avec AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Se connecter à un cluster sécurisé à l’aide d’un certificat client

Pour vous connecter à un cluster sécurisé avec des certificats, dirigez votre navigateur vers :

`https://<your-cluster-endpoint>:19080/Explorer`

Vous êtes automatiquement invité à sélectionner un certificat client.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurer un certificat client sur l’ordinateur distant
Au moins deux certificats devraient être utilisés pour sécuriser le cluster, un pour le certificat du cluster et du serveur, et un autre pour l’accès client.  Nous vous recommandons d’utiliser également des certificats secondaires supplémentaires et des certificats d’accès client.  Pour sécuriser la communication entre un client et un nœud de cluster à l’aide de la sécurité par certificat, vous devez d’abord obtenir et installer le certificat client. Ce certificat peut être installé dans le magasin personnel de l’ordinateur local ou de l’utilisateur actuel.  Vous avez également besoin de l’empreinte numérique du certificat du serveur afin que le client puisse authentifier le cluster.

Exécutez l’applet de commande PowerShell suivante pour configurer le certificat client sur l’ordinateur à partir duquel vous accédez au cluster.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

S’il s’agit d’un certificat auto-signé, vous devez l’importer dans le magasin de « personnes autorisées » de votre ordinateur avant de pouvoir l’utiliser pour vous connecter à un cluster sécurisé.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Étapes suivantes
* [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
* [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
* [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
* [Sécurité des applications et RunAs](service-fabric-application-runas-security.md)




<!--HONumber=Dec16_HO2-->


