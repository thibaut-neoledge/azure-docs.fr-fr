---
title: "Déploiement de ressources Azure avec C# | Microsoft Docs"
description: "Découvrez comment utiliser C# et Azure Resource Manager pour créer des ressources Microsoft Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 37ab64cb40def50342eb31c240aa301e9d751055
ms.openlocfilehash: 6fa33715484a4bb371148410b43c5ac606744160


---
# <a name="deploy-azure-resources-using-c"></a>Déploiement de ressources Azure en C# #
Cet article vous montre comment créer des ressources Azure avec C#.

Vous devez d’abord vous assurer que vous avez effectué ces tâches :

* Installez [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Vérifier l’installation de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
* Obtenir un [jeton d’authentification](../azure-resource-manager/resource-group-authenticate-service-principal.md)

Ces étapes prennent environ 30 minutes.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Étape 1 : Créer un projet Visual Studio et installez les bibliothèques
Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Pour obtenir les bibliothèques dont vous avez besoin dans Visual Studio, suivez ces étapes :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.
2. Dans **Modèles** > **Visual C#**, sélectionnez **Application Console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
4. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.
5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Compute* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de calcul .NET, puis suivez les instructions d’installation du package.
6. Tapez *Microsoft.Azure.Management.Network* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques réseau .NET, puis suivez les instructions d’installation du package.
7. Tapez *Microsoft.Azure.Management.Storage* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de stockage .NET, puis suivez les instructions d’installation du package.
8. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, puis cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Étape 2 : Créer les informations d’identification utilisées pour authentifier les requêtes
Maintenant, formatez les informations d’application que vous avez crées précédemment en informations d’identification utilisées pour authentifier les demandes à Azure Resource Manager.

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :
   
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
2. Pour créer le jeton nécessaire, ajoutez cette méthode à la classe Program :
   
        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }
   
    Remplacez {client-id} par l’identificateur d’application Azure Active Directory, {client-secret} par la clé d’accès de l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureRmSubscription. Vous pouvez trouver la clé d’accès à l’aide du portail Azure.
3. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main dans le fichier Program.cs :
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Enregistrez le fichier Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Étape 3 : Inscrire les fournisseurs de ressources et créer les ressources
### <a name="register-the-providers-and-create-a-resource-group"></a>Inscrire les fournisseurs et créer un groupe de ressources
Toutes les ressources doivent être contenues dans un groupe de ressources. Pour que vous puissiez ajouter des ressources à un groupe, votre abonnement doit être inscrit auprès des fournisseurs de ressources.

1. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms que vous souhaitez utiliser pour les ressources :
   
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
2. Pour créer le groupe de ressources et inscrire les fournisseurs, ajoutez cette méthode à la classe Program :
   
        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
   
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
   
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }
3. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Créez un compte de stockage.
Un [compte de stockage](../storage/storage-create-storage-account.md) est nécessaire pour stocker le fichier du disque dur virtuel généré pour la machine virtuelle.

1. Pour créer un compte de stockage, ajoutez cette méthode à la classe Program :
   
        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
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
2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main de la classe Program :
   
        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Créer une adresse IP publique
Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle.

1. Pour créer l’adresse IP publique de la machine virtuelle, ajoutez cette méthode à la classe Program :
   
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
2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main de la classe Program :
   
        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel
Une machine virtuelle créée avec le modèle de déploiement de Resource Manager doit faire partie d’un réseau virtuel.

1. Pour créer un sous-réseau et un réseau virtuel, ajoutez cette méthode à la classe Program :
   
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
2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main de la classe Program :
   
        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-network-interface"></a>Créer l’interface réseau
Une machine virtuelle a besoin d’une interface réseau pour communiquer sur le réseau virtuel.

1. Pour créer une interface réseau, ajoutez cette méthode à la classe Program :
   
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
2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main de la classe Program :
   
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

### <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité
Les groupes à haute disponibilité facilitent la gestion de la maintenance des machines virtuelles utilisées par votre application.

1. Pour créer un groupe à haute disponibilité, ajoutez cette méthode à la classe Program :
   
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
2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main de la classe Program :
   
        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

1. Pour créer la machine virtuelle, ajoutez cette méthode à la classe Program :
   
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
          var networkManagementClient = new NetworkManagementClient(credential)
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
        }
   
   > [!NOTE]
   > Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   > 
   > 
2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

## <a name="step-4-delete-the-resources"></a>Étape 4 : supprimer les ressources
Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et l’ensemble des ressources de prise en charge, il vous suffit de supprimer le groupe de ressources.

1. Pour supprimer un groupe de ressources, ajoutez cette méthode à la classe Program :
   
   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
   ```   
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-5-run-the-console-application"></a>Étape 5 : Exécuter l’application console
1. Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.
2. Pour créer chaque ressource, appuyez sur **Entrée** après le renvoi de chaque code d’état. Une fois la machine virtuelle créée, effectuez l’étape suivante, puis appuyez sur Entrée pour supprimer toutes les ressources.
   
    L’exécution complète de cette application console devrait durer cinq minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.
3. Pour connaître l’état des ressources, parcourez les journaux d’audit dans le portail Azure :
   
    ![Parcourir les journaux d’audit dans le portail Azure](./media/virtual-machines-windows-csharp/crpportal.png)

## <a name="next-steps"></a>Étapes suivantes
* Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Déploiement d’une machine virtuelle Azure avec C# et un modèle Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO1-->


