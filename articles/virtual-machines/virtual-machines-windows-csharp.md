---
title: "Déployer une machine virtuelle Azure à l’aide de C# | Microsoft Docs"
description: "Utiliser C# et Azure Resource Manager pour déployer une machine virtuelle et toutes ses ressources de soutien."
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
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: a03a33ca275d0d81f8968ed19b1cc2fb8907cb1c
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>Déployer une machine virtuelle Azure à l’aide de C# #

Cet article explique comment utiliser C# pour créer une machine virtuelle Azure et ses ressources de soutien.

Ces étapes prennent environ 20 minutes.

## <a name="step-1-create-a-visual-studio-project"></a>Étape 1 : création d’un projet Visual Studio

Lors de cette étape, assurez-vous que Visual Studio est installé et que vous créez une application console utilisée pour créer les ressources.

1. Si vous ne l’avez pas déjà fait, installez [Visual Studio](https://www.visualstudio.com/).
2. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.
3. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

## <a name="step-2-install-libraries"></a>Étape 2 : installation des bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ces étapes. Pour obtenir les bibliothèques dont vous avez besoin dans Visual Studio, suivez ces étapes :


1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, et cliquez sur **Gérer les packages NuGet**, puis sur **Parcourir**.
2. Entrez *Microsoft.IdentityModel.Clients.ActiveDirectory* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
3. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Compute* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
4. Tapez *Microsoft.Azure.Management.Network* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
5. Tapez *Microsoft.Azure.Management.Storage* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
6. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>Étape 3 : Créer les informations d’identification utilisées pour authentifier les requêtes

Avant de commencer cette étape, assurez-vous que vous avez accès à un [principal de service Active Directory](../azure-resource-manager/resource-group-authenticate-service-principal.md). Dans le principal de service, vous obtenez le jeton d'authentification des demandes pour Azure Resource Manager.

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :
   
    ```
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
    ```

2. Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :
   
    ```    
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
    ```

    Remplacez les valeurs suivantes :
    
    - *{client-id}* avec l'identificateur de l'application Azure Active Directory. Vous pouvez trouver cet identifiant dans le panneau Propriétés de votre application AD. Pour trouver votre application Active Directory dans le portail Azure, cliquez sur **Azure Active Directory** dans le menu de ressources, puis cliquez sur **Inscriptions d’applications**.
    - *{client-secret} * avec la clé d’accès de l’application Active Directory. Vous pouvez trouver cet identifiant dans le panneau Propriétés de votre application AD.
    - *{tenant-id} * avec l’identificateur de client de votre abonnement. Vous trouverez l’identificateur de client dans le panneau Propriétés d’Azure Active Directory dans le portail Azure. Il porte le nom *ID de répertoire*.

3. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main dans le fichier Program.cs :
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Enregistrez le fichier Program.cs.

## <a name="step-3-create-the-resources"></a>Étape 3 : création des ressources

### <a name="register-the-providers-and-create-a-resource-group"></a>Inscrire les fournisseurs et créer un groupe de ressources

Toutes les ressources doivent être contenues dans un groupe de ressources. Pour que vous puissiez ajouter des ressources à un groupe, votre abonnement doit être inscrit auprès des fournisseurs de ressources.

1. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms que vous souhaitez utiliser pour les ressources :

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    Remplacez les valeurs suivantes :

    - Remplacez tous les noms de ressource commençant par *my* par des noms appropriés à votre environnement.
    - *subscriptionId* avec l’identificateur de votre abonnement. Vous trouverez l’identificateur d’abonnement dans le panneau Abonnements du portail Azure.
    - *location* est la [région Azure](https://azure.microsoft.com/regions/) où vous souhaitez créer les ressources.
    - *adminName* est le nom du compte d’administrateur sur les machines virtuelles.
    - *adminPassword* avec le mot de passe du compte administrateur.

2. Pour créer le groupe de ressources et inscrire les fournisseurs, ajoutez cette méthode à la classe Program :
   
    ```
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
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Lorsque vous utilisez un disque non géré, un [compte de stockage](../storage/storage-create-storage-account.md) est nécessaire pour stocker le fichier du disque dur virtuel généré pour la machine virtuelle.

1. Pour créer un compte de stockage, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

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
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle.

1. Pour créer l’adresse IP publique pour la machine virtuelle, ajoutez cette méthode à la classe Program :
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

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
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Une machine virtuelle créée avec le modèle de déploiement de Resource Manager doit faire partie d’un réseau virtuel.

1. Pour créer un sous-réseau et un réseau virtuel, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
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
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>Créer une interface réseau

Une machine virtuelle a besoin d’une interface réseau pour communiquer sur le réseau virtuel.

1. Pour créer une interface réseau, ajoutez cette méthode à la classe Program :

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
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
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
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
    ```

### <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Les groupes à haute disponibilité facilitent la gestion de la maintenance des machines virtuelles utilisées par votre application.

1. Pour créer un groupe à haute disponibilité, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

1. Pour créer la machine virtuelle, ajoutez cette méthode à la classe Program :

    ```   
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
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
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
    ```

    > [!NOTE]
    > Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
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
    ```

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

2. Appuyez sur **Entrée** lorsque l’état *Réussi* s’affiche. 
   
3. Une fois la machine virtuelle créée et avant d’appuyer sur **Entrée** pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées.

## <a name="next-steps"></a>Étapes suivantes
* Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Déploiement d’une machine virtuelle Azure avec C# et un modèle Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Azure Resource Manager et de C#](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


