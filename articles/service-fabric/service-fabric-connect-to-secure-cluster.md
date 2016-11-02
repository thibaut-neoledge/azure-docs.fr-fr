<properties
   pageTitle="Authentifier l’accès client à un cluster | Microsoft Azure"
   description="Décrit comment authentifier l’accès client à un cluster Service Fabric à l’aide de certificats et comment sécuriser les communications entre les clients et un cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="connect-to-a-secure-cluster-without-aad"></a>Connexion à un cluster sécurisé sans AAD
Lorsqu’un client se connecte à un nœud de cluster Service Fabric, il peut être authentifié et une communication sécurisée peut être établie à l’aide de la sécurité par certificat. Cette authentification garantit que seuls les utilisateurs autorisés puissent accéder au cluster et aux applications déployées, et effectuer des tâches de gestion.  La sécurité par certificat doit avoir été précédemment activée sur le cluster à sa création.  Au moins deux certificats devraient être utilisés pour sécuriser le cluster, un pour le certificat du cluster et du serveur, et un autre pour l’accès client.  Nous vous recommandons d’utiliser également des certificats secondaires supplémentaires et des certificats d’accès client.  Pour plus d’informations sur les scénarios de sécurité des clusters, consultez [Sécurité des clusters](service-fabric-cluster-security.md).

Pour sécuriser la communication entre un client et un nœud de cluster à l’aide de la sécurité par certificat, vous devez d’abord obtenir et installer le certificat client. Ce certificat peut être installé dans le magasin personnel de l’ordinateur local ou de l’utilisateur actuel.  Vous avez également besoin de l’empreinte numérique du certificat du serveur afin que le client puisse authentifier le cluster.


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
<a id="connectsecureclustercli"></a> 
## <a name="connect-to-a-secure-cluster-using-azure-cli-without-aad"></a>Se connecter à un cluster sécurisé avec l’interface de ligne de commande Azure sans AAD

Les commandes de l’interface de ligne de commande Azure ci-après expliquent comment se connecter à un cluster sécurisé. Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster. 
 
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

Une fois connecté, vous devez être en mesure d’exécuter d’autres commandes d’interface de ligne de commande pour interagir avec le cluster. 

<a id="connectsecurecluster"></a>
## <a name="connect-to-a-secure-cluster-using-powershell-without-aad"></a>Se connecter à un cluster sécurisé via PowerShell, sans AAD

Exécutez la commande PowerShell suivante pour vous connecter à un cluster sécurisé. Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster.

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




## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>Se connecter à un cluster sécurisé à l’aide des API FabricClient

Pour en savoir plus sur les API FabricClient, voir [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Les nœuds du cluster doivent présenter des certificats valides, dont le nom commun ou le nom DNS dans le SAN s’affichent dans la [propriété RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) définie sur [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Ce processus permet une authentification mutuelle entre le client et les nœuds du cluster.

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


## <a name="next-steps"></a>Étapes suivantes

- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
- [Sécurité des applications et RunAs](service-fabric-application-runas-security.md)



<!--HONumber=Oct16_HO2-->


