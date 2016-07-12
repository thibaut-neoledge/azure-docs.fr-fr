<properties
   pageTitle="Kit de développement logiciel (SDK) Resource Manager pour .NET | Microsoft Azure"
   description="Vue d’ensemble de l’authentification du Kit de développement logiciel (SDK) Resource Manager pour .NET et exemples d’utilisation"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# Kit de développement logiciel (SDK) Azure Resource Manager pour .NET  
Des Kits de développement logiciel (SDK) Azure Resource Manager en version préliminaire sont disponibles en plusieurs langues sur plusieurs plates-formes. Chacune de ces langues est disponible via le gestionnaire de package d’écosystème correspondant et GitHub.

Le code dans chaque kit de développement logiciel (SDK) est généré à partir de [spécifications de l’API Azure RESTful](https://github.com/azure/azure-rest-api-specs). Disponibles en open source, ces spécifications sont basées sur la spécification Swagger 2.0. Le code des Kits de développement logiciel (SDK) est généré par le biais d’un projet open source appelé [AutoRest](https://github.com/azure/autorest). AutoRest transforme ces spécifications d’API RESTful en bibliothèques clientes dans plusieurs langues. Si vous souhaitez améliorer des aspects du code généré dans les Kits de développement logiciel (SDK), l’ensemble des outils permettant de créer les Kits de développement logiciel (SDK) sont ouverts, disponibles gratuitement et basés sur un format de spécification d’API largement répandu.

Le [Kit de développement logiciel (SDK) pour .NET](https://azure.microsoft.com/downloads/) est un ensemble de packages NuGet qui vous permet d’appeler la plupart des API Azure Resource Manager. Si leKkit de développement logiciel (SDK) ne fournit pas les fonctionnalités nécessaires, vous pouvez facilement le combiner à des appels habituels de l’API REST Resource Manager en arrière-plan.

Cet article n’a pas pour objet de décrire tous les aspects du Kit de développement logiciel (SDK) Azure pour .NET, les API Azure Resource Manager ou Visual Studio. Il vise à permettre une familiarisation rapide.

Tous les extraits de code ci-après proviennent d’un [exemple de projet téléchargeable](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Installer les packages NuGet

Les exemples de cet article requièrent deux packages NuGet (outre le Kit de développement logiciel (SDK) Azure pour .NET). Pour les installer :

1. Dans Visual Studio, cliquez avec le bouton droit sur votre projet et sélectionnez **Gérer les packages NuGet**.
2. Recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory** et installez la dernière version stable du package.
3. Recherchez **Microsoft.Azure.Management.ResourceManager** et sélectionnez **Inclure la version préliminaire**. Installez la version préliminaire la plus récente (par exemple, la version 1.1.2).

## Authentifier
Azure Active Directory (Azure AD) gère l’authentification pour Resource Manager. Pour vous connecter à une API quelconque, vous devez tout d’abord vous authentifier auprès d’Azure AD pour recevoir un jeton d’accès que vous pouvez transmettre à chaque requête. Pour obtenir ce jeton, vous devez créer une application Azure AD et un principal du service qui seront utilisés pour la connexion. Pour obtenir des instructions détaillées, consultez l’un de ces articles :

- [Utiliser Azure PowerShell pour créer une application Active Directory pour accéder aux ressources](resource-group-authenticate-service-principal.md)
- [Utiliser l’interface de ligne de commande Azure pour créer une application Active Directory pour accéder aux ressources](resource-group-authenticate-service-principal-cli.md)
- [Utiliser le portail Azure pour créer une application Active Directory pouvant accéder aux ressources](resource-group-create-service-principal-portal.md)

Une fois le principal du service créé, vous devez disposer des éléments suivants :

- ID client ou ID d’application (GUID)
- Clé secrète client ou mot de passe (chaîne)
- ID client (GUID) ou nom de domaine (chaîne)

### Recevoir le jeton d’accès à partir du code
Vous pouvez acquérir le jeton d’authentification à l’aide des lignes de code suivantes, en transmettant uniquement l’ID client Azure AD, l’ID client de l’application Azure AD et la clé secrète client de l’application Azure AD. Enregistrez le jeton pour plusieurs requêtes, car il est valide pendant une heure par défaut.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

Au lieu d’utiliser l’ID client pour la connexion, vous pouvez utiliser le domaine Azure AD, comme indiqué dans le code suivant. Cette approche nécessite de modifier la signature de méthode de manière qu’elle inclue le nom de domaine au lieu de l’ID client.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

Vous pouvez obtenir le jeton d’accès pour une application Azure AD utilisant un certificat pour l’authentification à l’aide du code suivant :

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Interroger les abonnements Azure associés à l’application authentifiée
Il peut être judicieux d’interroger les abonnements Azure associés à l’application venant d’être authentifiée. L’ID d’abonnement pour votre abonnement cible devra être obligatoirement transmis à chaque appel d’API que vous effectuerez par la suite.

L’exemple de code suivant interroge directement les API Azure à l’aide de l’API REST. Autrement dit, il n’utilise aucune fonctionnalité du Kit de développement logiciel (SDK) Azure pour .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Notez que vous obtenez une réponse JSON d’Azure. Vous extrayez ensuite l’ID d’abonnement de cette réponse afin de renvoyer une liste d’ID. Tous les appels vers des API Azure Resource Manager dans la suite de cet article utilisent un seul ID d’abonnement Azure. Si votre application est associée à plusieurs abonnements, choisissez simplement celui qui vous convient, puis utilisez-le en tant que paramètre à l’avenir.

À partir de maintenant, tous les appels des API Azure utiliseront le Kit de développement logiciel (SDK) Azure pour .NET. Par conséquent, le code sera un peu différent.

### Encapsuler le jeton en tant qu’objet TokenCredentials
Tous les appels d’API suivants nécessiteront le jeton que vous avez reçu d’Azure AD au format d’un objet TokenCredentials. Vous pouvez créer un tel objet en transmettant le jeton brut en tant que paramètre au constructeur de la classe.

```csharp
var credentials = new TokenCredentials(token);
```

Si vous disposez d’une version antérieure du package NuGet de Resource Manager (nommé Microsoft.Azure.Management.Resources), vous devez utiliser le code suivant :

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## Créer un groupe de ressources
Tous les éléments dans Azure se concentrent sur les groupes de ressources. Nous allons donc en créer un. *ResourceManagementClient* gère les ressources générales et les groupes de ressources. Pour tout client de gestion plus spécialisé que vous utiliserez par la suite, vous devez fournir vos informations d’identification et un ID d’abonnement afin d’identifier l’abonnement avec lequel vous souhaitez travailler.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Créer des ressources manuellement ou à l’aide de modèles
Il existe plusieurs manières d’interagir avec les API Azure Resource Manager, mais voici les deux méthodes principales :

* Manuellement, en appelant des fournisseurs de ressources spécifiques
* En utilisant un modèle Azure Resource Manager

L’utilisation d’un modèle Resource Manager présente les avantages suivants :

* Vous spécifiez de façon déclarative le résultat que vous souhaitez obtenir plutôt que la manière dont il doit être obtenu.
* Vous n’êtes pas obligé de traiter manuellement l’exécution parallèle de vos déploiements. Resource Manager exécute cette opération pour vous.
* Vous n’êtes pas obligé d’apprendre C# ou tout autre langage afin de déployer un modèle Resource Manager, même si vous pouvez utiliser n’importe quel langage pour démarrer un déploiement basé sur un modèle.
* Le langage spécifique au domaine (DSL) qui est utilisé dans les modèles est créé via JSON. Si vous avez déjà travaillé avec JSON, vous ne devriez pas rencontrer de difficultés.

Même si les modèles permettent de profiter de tous ces avantages, nous allons commencer par vous montrer comment appeler les API manuellement.

### Créer une machine virtuelle élément par élément
Vous disposez de votre abonnement et de votre groupe de ressources. Si vous souhaitez déployer une machine virtuelle, vous devez savoir de quels éléments elle est constituée :

* Un ou plusieurs comptes de stockage, pour le stockage de disques persistants
* Une ou plusieurs adresses IP publiques, pour rendre vos ressources dans Azure accessibles depuis Internet (inclut un nom DNS)
* Un ou plusieurs réseaux virtuels, pour la communication interne entre vos ressources
* Une ou plusieurs cartes réseau, pour permettre aux machines virtuelles de communiquer
* Une ou plusieurs machines virtuelles, pour exécuter les logiciels

Certaines de ces ressources peuvent être créées en parallèle, mais d’autres non. Par exemple :

* Les cartes réseau dépendent des adresses IP publiques et des réseaux virtuels.
* Les machines virtuelles dépendent des cartes réseau et des comptes de stockage.

N’essayez pas d’instancier des ressources avant que les dépendances nécessaires aient été créées. [L’exemple](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) complet montre comment vous pouvez créer efficacement vos ressources en parallèle tout en suivant ce que vous avez déjà créé.

#### Créer le compte de stockage
Vous avez besoin d’un compte de stockage pour stocker les disques durs virtuels destinés à votre machine virtuelle. Si vous possédez déjà un compte de stockage, vous pouvez l’utiliser pour plusieurs machines virtuelles. Cependant, pensez à répartir la charge entre plusieurs comptes de stockage afin de ne pas atteindre les limites. Ayez également à l’esprit que le type de compte de stockage et son emplacement peuvent entraîner une limitation de la taille de la machine virtuelle sélectionnable, car toutes les tailles de machines virtuelles ne sont pas disponibles dans toutes les régions ou pour tous les types de compte de stockage.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Créer une adresse IP publique
L’adresse IP publique permet de rendre vos ressources dans Azure accessibles depuis Internet. Un nom de domaine qualifié complet (FQDN) vous sera affecté en même temps que l’adresse IP. Vous pouvez l’utiliser pour faciliter l’accès.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### Créer un réseau virtuel
Chaque machine virtuelle créée avec les API Azure Resource Manager doit faire partie d’un réseau virtuel, même si elle est le seul élément de ce réseau. Le réseau virtuel doit contenir au moins un sous-réseau, mais vous pouvez utiliser plusieurs sous-réseaux pour diviser et protéger vos ressources.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Créer la carte réseau
La carte réseau permet de connecter votre machine virtuelle au réseau virtuel dans lequel elle réside. Une machine virtuelle peut disposer de nombreuses cartes réseau, et donc être associée à plusieurs réseaux virtuels. Cet exemple suppose que vous associez vos machines virtuelles à un seul réseau virtuel.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Créer la machine virtuelle
Enfin, il est temps de créer la machine virtuelle. La machine virtuelle dépend (directement ou indirectement) de toutes les ressources créées aux étapes précédentes. Par conséquent, vous devez attendre que toutes les ressources soient prêtes avant d’essayer d’approvisionner une machine virtuelle. L’approvisionnement d’une machine virtuelle prend plus de temps que la création des autres ressources. Vous devez donc vous attendre à ce que votre application ait besoin d’un certain temps pour cette opération.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Déploiement d’un modèle
Pour obtenir des instructions détaillées sur le déploiement d’un modèle, consultez l’article [Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle](./virtual-machines/virtual-machines-windows-csharp-template.md).

Pour résumer, il est beaucoup plus facile de déployer un modèle que d’approvisionner manuellement les ressources. Le code suivant montre comment le faire en indiquant les URI où se trouvent le modèle et un fichier de paramètres.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```

<!---HONumber=AcomDC_0629_2016-->