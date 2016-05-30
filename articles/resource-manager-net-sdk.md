<properties
   pageTitle="Kit de développement logiciel (SDK) Resource Manager pour .Net| Microsoft Azure"
   description="Une vue d’ensemble des exemples d’authentification et de cas d’utilisation du Kit de développement logiciel (SDK) Resource Manager pour .Net"
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
   ms.date="05/17/2016"
   ms.author="navale;tomfitz;"/>

# Kit de développement logiciel (SDK) Azure Resource Manager pour .Net  
Des kits de développement logiciel (SDK) Azure Resource Manager en version préliminaire sont disponibles en plusieurs langues sur plusieurs plates-formes. Chacune de ces langue est disponible à travers leurs gestionnaires de package d’écosystème et GitHub.

Le code dans chaque kit de développement logiciel (SDK) est généré à partir de [spécifications de l’API Azure RESTful](https://github.com/azure/azure-rest-api-specs). Ces spécifications sont open source et basées sur la spécification Swagger v2. Le code de kit de développement logiciel (SDK) est généré par le biais d’un projet open source appelé [AutoRest](https://github.com/azure/autorest). AutoRest transforme ces spécifications d’API RESTful en bibliothèques clientes dans plusieurs langues. Si vous souhaitez améliorer des aspects du code généré dans les kits de développement logiciel, l’ensemble des outils permettant de créer les kits de développement logiciels sont ouverts, disponibles gratuitement et basés sur le format de spécification API largement adopté.

Le kit de développement logiciel (SDK) pour .Net est fourni comme un ensemble de packages NuGet qui vous permet d’appeler la plupart des API exposées par l’azure Resource Manager. Si le kit de développement logiciel (SDK) n’expose pas les fonctionnalités nécessaires, vous pouvez facilement le combiner à des appels habituels de l’API REST ARM en arrière-plan.

Cette documentation n’est pas destinée à décrire tous les aspects du Kit de développement logiciel (SDK) Azure .NET, les API Azure ARM ou Visual Studio. Son objectif est plutôt de permettre une familiarisation rapide.

Vous pouvez trouver [ici](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) un exemple de projet complet téléchargeable à partir de l’endroit d’où proviennent tous les extraits de code figurant ci-dessous.

## Authentification
L’authentification pour ARM est gérée par Azure Active Directory (AD). Pour vous connecter à une API quelconque, vous devez tout d’abord vous authentifier auprès d’Azure AD pour recevoir un jeton d’authentification que vous pouvez transmettre à chaque requête. Pour obtenir ce jeton, vous devez d’abord créer ce que l’on appelle une application Azure AD et un principal du service qui seront utilisés pour la connexion. Consultez [Création de l’application Azure AD et du principal du service](resource-group-create-service-principal-portal.md) pour obtenir des instructions détaillées.

Une fois le principal du service créé, vous devez avoir les éléments suivants :
* ID de client (GUID)
* Clé secrète client (chaîne de caractères)
* ID client (GUID) ou nom de domaine (chaîne de caractères)

### Réception du jeton d’accès (AccessToken) à partir du code
Le jeton d’authentification peut facilement être acquis avec les lignes de code ci-dessous, en transmettant uniquement votre ID client Azure AD, votre ID de client de l’application Azure AD et la clé secrète client de l’application Azure AD. Enregistrez le jeton pour plusieurs requêtes, car il est valide pendant 1 heure par défaut.

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Interrogation des abonnements Azure associés à l’application authentifiée
L’une des premières étapes que vous devriez accomplir est l’interrogation des abonnements Azure associés à l’application venant d’être authentifiée. L’ID d’abonnement pour votre abonnement cible devra être obligatoirement transmis à chaque appel d’API que vous effectuerez par la suite.

L’exemple de code ci-dessous interroge les API Azure directement à l’aide de l’API REST, et non à l’aide de fonctionnalités dans le Kit de développement logiciel (SDK) Azure pour .NET.

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

Notez que JSON envoie une réponse depuis Azure à partir de laquelle les ID d’abonnement sont extraits afin de retourner une liste d’ID. Tous les appels ultérieurs des API Azure ARM dans cette documentation utilisent un seul ID d’abonnement Azure. Ainsi, si votre application est associée à plusieurs abonnements, choisissez simplement l’ID adapté à utiliser comme paramètre à l’avenir.

À partir de là, chaque appel des API Azure utiliseront le Kit de développement logiciel (SDK) Azure pour .NET. Le code sera donc légèrement différent.

### Encapsulage du jeton en tant qu’objet TokenCredentials
Tous les appels d’API suivants nécessiteront le jeton que vous avez reçu d’Azure AD au format d’un objet « TokenCredentials ». Un tel objet est créé facilement en transmettant simplement le jeton brut en tant que paramètre au constructeur de la classe.

```csharp
var credentials = new TokenCredentials(token);
```

## Création d’un groupe de ressources
Tous les éléments dans Azure se concentrent sur les groupes de ressources. Nous allons donc commencer par en créer un. Les ressources générales et des groupes de ressources sont gérées par le *ResourceManagementClient* et, comme pour tout client de gestion plus spécialisé que nous utiliserons par la suite, vous devez fournir vos informations d’identification, ainsi qu’un ID d’abonnement pour identifier l’abonnement avec lequel vous souhaitez travailler.

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

## Création de ressources manuellement ou à l’aide de modèles
Il existe différentes manières d’interagir avec les API de l’Azure Resource Manager, mais voici les deux méthodes principales :

* Manuellement, en appelant des fournisseurs de ressources spécifiques manuellement ou
* en utilisant un modèle Azure Resource Manager (c’est-à-dire un modèle ARM)

L’utilisation de modèles ARM présente les avantages suivants :

* vous spécifiez de façon déclarative le résultat final que vous souhaitez obtenir plutôt que la manière dont il doit être obtenu ;
* vous n’êtes pas obligé de traiter manuellement l’exécution parallèle de vos déploiements. ARM exécute cette opération pour vous ;
* vous n’êtes pas obligé d’apprendre C# ou tout autre langage afin de déployer un modèle ARM, même si vous pouvez utiliser n’importe quel langage pour démarrer un déploiement basé sur un modèle ;
* Le langage de domaine spécifique, DSL, qui est utilisé dans les modèles, est conçu à l’aide de JSON et suffisamment facile à comprendre par toute personne ayant travaillé avec JSON.

Même si les modèles permettent de profiter de tous ces avantages, nous allons commencer par vous montrer comment appeler les API manuellement.

### Création d’une machine virtuelle élément par élément
Nous disposons maintenant de notre abonnement et de notre groupe de ressources. Si nous souhaitons déployer une machine virtuelle, nous devons déterminer les éléments faisant réellement partie d’une machine virtuelle. Et ils sont nombreux :

* 1 ou plusieurs comptes de stockage, pour le stockage de disques persistants
* 1 ou plusieurs adresses IP publiques (PIP) pour l’accessibilité depuis Internet (comprenant un nom DNS)
* 1 ou plusieurs réseaux virtuels (VNET) pour la communication interne entre vos ressources
* 1 ou plusieurs cartes d’interface réseau (NIC) pour permettre à la machine virtuelle de communiquer
* 1 ou plusieurs machines virtuelles (VM) pour exécuter nos logiciels

Il est également intéressant de noter que certaines de ces ressources peuvent être créées en parallèle alors que d’autres ne le peuvent pas. Par exemple :

* les cartes d’interface réseau dépendent des adresses IP publiques et des réseaux virtuels ;
* les machines virtuelles dépendent des cartes d’interface réseau et des comptes de stockage.

Vous devez vous assurer que vous n’essayez pas d’instancier des ressources avant que les dépendances nécessaires n’aient été créées. L’[exemple](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) complet fourni avec cette documentation montre comment vous pouvez créer efficacement vos ressources en parallèle tout en suivant ce que vous avez déjà créé.

#### Création du compte de stockage
Vous avez besoin d’un compte de stockage pour stocker les disques durs virtuels pour votre machine virtuelle. Si vous avez un compte de stockage existant, vous pouvez l’utiliser pour plusieurs machines virtuelles. Mais n’oubliez pas de répartir la charge sur plusieurs comptes de stockage afin de ne pas atteindre les limites. N’oubliez pas que le type de compte de stockage et son emplacement peuvent entraîner une limitation de la taille de la machine virtuelle que vous pouvez choisir dans la mesure où toutes les tailles de machines virtuelles ne sont pas disponibles dans toutes les régions et/ou pour tous les types de compte de stockage.

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

#### Création d’une adresse IP publique (PIP)
L’adresse IP publique est ce qui rend vos ressources dans Azure accessibles depuis Internet. Un nom de domaine qualifié complet (FQDN) vous sera affecté en même temps que l’adresse IP. Vous pouvez l’utiliser pour faciliter l’accès.

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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Création du réseau virtuel (VNet)
Chaque machine virtuelle créée avec les API ARM doit faire partie d’un réseau virtuel, même si elle est l’unique élément qu’il contient. Le réseau virtuel doit contenir au moins un sous-réseau, mais vous pouvez en avoir un plus grand nombre pour diviser et protéger vos ressources dans plusieurs sous-réseaux.

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

#### Création de la carte d’interface réseau (NIC)
La carte d’Interface réseau (NIC) permet de connecter votre machine virtuelle au réseau virtuel dans lequel elle réside. Une machine virtuelle peut disposer de nombreuses cartes d’interface réseau, et donc être associée à plusieurs réseaux virtuels. Cet exemple suppose que vous associez vos machines virtuelles à un seul réseau virtuel.

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

#### Création de la machine virtuelle
Enfin, il est temps de créer la machine virtuelle elle-même. La machine virtuelle dépend directement ou indirectement de toutes les ressources créées aux étapes précédentes. Par conséquent, vous devez attendre que tous les éléments traités précédemment soient prêts avant d’essayer d’approvisionner une machine virtuelle. L’approvisionnement d’une machine virtuelle est ce qui prend le plus de temps par rapport aux autres ressources. Vous devez donc vous attendre à ce que votre application ait besoin d’un certain temps pour cette opération.

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

### Utilisation d’un déploiement basé sur un modèle
Veuillez lire et suivre les indications du didacticiel [Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle](./virtual-machines/virtual-machines-windows-csharp-template.md) pour obtenir des instructions détaillées sur la manière de déployer un modèle.

En bref, le déploiement d’un modèle est beaucoup plus facile que l’approvisionnement manuel des ressources. Et le code ci-dessous montre comment le faire en pointant sur les URI dans lesquelles se trouvent le modèle et un fichier de paramètres.

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


 
   

<!---HONumber=AcomDC_0518_2016-->