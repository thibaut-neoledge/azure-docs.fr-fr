<properties
	pageTitle="Déploiement de ressources Azure avec C# | Microsoft Azure"
	description="Découvrez comment utiliser C# et Azure Resource Manager pour créer des ressources Microsoft Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="davidmu"/>

# Déploiement de ressources Azure en C# 

Cet article vous montre comment créer des ressources Azure avec C#.

Vous devez d’abord vous assurer que vous avez fait ceci :

- Installez [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Vérifiez l’installation de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obtenir un [jeton d’authentification](../resource-group-authenticate-service-principal.md)

Ces étapes prennent environ 30 minutes.

## Étape 1 : Créer un projet Visual Studio et installez les bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous devez installer la bibliothèque Microsoft Azure Resource Management, la bibliothèque d’authentification Microsoft Azure Active Directory et la bibliothèque des fournisseurs de ressources informatiques. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.

2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.

4. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.

5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Compute* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de calcul .NET, puis suivez les instructions d’installation du package.

6. Tapez *Microsoft.Azure.Management.Network* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques réseau .NET, puis suivez les instructions d’installation du package.

7. Tapez *Microsoft.Azure.Management.Storage* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de stockage .NET, puis suivez les instructions d’installation du package.

8. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, puis cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

## Étape 2 : Créer les informations d’identification utilisées pour authentifier les requêtes

L’application Azure Active Directory est créée et la bibliothèque d’authentification est installée. Il vous reste à formater les informations d’application en informations d’identification qui serviront à authentifier les requêtes envoyées à Azure Resource Manager. Procédez comme suit :

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

	Remplacez {client-id} par l’identificateur d’application Azure Active Directory, {client-secret} par la clé d’accès de l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureRmSubscription. Vous pouvez trouver la clé d’accès à l’aide du portail Azure.

3. Ajoutez le code suivant à la méthode Main dans le fichier Program.cs pour créer les informations d’identification :

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Enregistrez le fichier Program.cs.

## Étape 3 : Ajouter le code pour inscrire les fournisseurs et créer les ressources

### Inscrire les fournisseurs et créer un groupe de ressources

Toutes les ressources doivent être contenues dans un groupe de ressources. Pour que vous puissiez ajouter des ressources à un groupe, votre abonnement doit être inscrit auprès des fournisseurs de ressources.

1. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms que vous souhaitez utiliser pour les ressources, l’emplacement des ressources, par exemple « États-Unis du Centre », les informations de compte Administrateur et votre identificateur d’abonnement :

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Remplacez les valeurs de toutes les variables par le nom et l’identificateur que vous souhaitez utiliser. Pour trouver l’identificateur d’abonnement, exécutez Get-AzureRmSubscription.

2. Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources et enregistrer les fournisseurs :

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### Créer un compte de stockage

Un [compte de stockage](../storage/storage-create-storage-account.md) est nécessaire pour stocker le fichier du disque dur virtuel généré pour la machine virtuelle.

1. Ajoutez la méthode suivante à la classe Program pour créer un compte de stockage :

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential);
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### Créer une adresse IP publique

Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle.

1. Ajoutez la méthode suivante à la classe Program pour créer une adresse IP publique de la machine virtuelle :

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### Créer un réseau virtuel

Une machine virtuelle créée avec le modèle de déploiement de Resource Manager doit faire partie d’un réseau virtuel.

1. Ajoutez la méthode suivante à la classe Program pour créer un sous-réseau et un réseau virtuel :

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### Créer l’interface réseau

Une machine virtuelle a besoin d’une interface réseau pour communiquer sur le réseau virtuel que vous venez de créer.

1. Ajoutez la méthode suivante à la classe Program pour créer une interface réseau :

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### Créer un groupe à haute disponibilité

Les groupes à haute disponibilité facilitent la gestion de la maintenance des machines virtuelles utilisées par votre application.

1. Ajoutez la méthode suivante à la classe Program pour créer un groupe à haute disponibilité :

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### Création d'une machine virtuelle

Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

1. Ajoutez la méthode suivante à la classe Program pour créer une machine virtuelle :

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential);
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
          Console.WriteLine(vm.ProvisioningState);
        }

	>[AZURE.NOTE] Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avsetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##Étape 4 : Ajouter le code pour supprimer les ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et l’ensemble des ressources de prise en charge, il vous suffit de supprimer le groupe de ressources.

1.	Ajoutez la méthode suivante à la classe Program pour supprimer un groupe de ressources :

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## Étape 5 : Exécuter l’application console

1. Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2. Pour créer chaque ressource, appuyez sur **Entrée** après le renvoi de chaque code d’état. Une fois la machine virtuelle créée, effectuez l’étape suivante, puis appuyez sur Entrée pour supprimer toutes les ressources.

	L’exécution complète de cette application console devrait durer 5 minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.

3. Parcourez les journaux d’audit dans le portail Azure pour connaître l’état des ressources :

	![Parcourir les journaux d’audit dans le portail Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## Étapes suivantes

- Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Déploiement d’une machine virtuelle Azure avec C# et un modèle Resource Manager](virtual-machines-windows-csharp-template.md).
- Pour apprendre à gérer la machine virtuelle que vous venez de créer, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-csharp-manage.md).

<!---HONumber=AcomDC_0629_2016-->