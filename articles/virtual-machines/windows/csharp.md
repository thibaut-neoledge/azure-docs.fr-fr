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
ms.date: 06/20/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: f04783896eb36e9a8c3e5cc8422a1fb054b36dd2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/23/2017

---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Créer et gérer des machines virtuelles Windows dans Azure à l’aide de C# #

Une [machine virtuelle Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a besoin de plusieurs ressources de prise en charge Azure. Cet article décrit comment créer, gérer et supprimer des ressources de machine virtuelle à l’aide de C#. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Visual Studio
> * Installation des bibliothèques
> * Créer des informations d’identification
> * Créer des ressources
> * Effectuer d’autres tâches de gestion
> * Supprimer des ressources
> * Exécution de l'application

Ces étapes prennent environ 20 minutes.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Si vous ne l’avez pas déjà fait, installez [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Sélectionnez **Développement .NET Desktop** dans la page Charges de travail, puis cliquez sur **Installer**. Dans le résumé, vous pouvez voir que les **Outils de développement .NET Framework 4 - 4.6** sont automatiquement sélectionnés. Si vous avez déjà installé Visual Studio, vous pouvez ajouter la charge de travail .NET en utilisant le lanceur Visual Studio.
2. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.
3. Dans **Modèles** > **Visual C#**, sélectionnez **Application console (.NET Framework)**, entrez le nom de projet *myDotnetProject*, sélectionnez l’emplacement du projet, puis cliquez sur **OK**.

## <a name="install-libraries"></a>Installation des bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ces étapes. Pour obtenir les bibliothèques dont vous avez besoin dans Visual Studio, suivez ces étapes :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur *myDotnetProject*, cliquez sur **Gérer les packages NuGet pour la solution...**, puis cliquez sur **Parcourir**.
2. Entrez *Microsoft.IdentityModel.Clients.ActiveDirectory* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
3. Tapez *Microsoft.Azure.Management.Compute* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
4. Tapez *Microsoft.Azure.Management.Network* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
5. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

## <a name="create-credentials"></a>Créer des informations d’identification

Avant de commencer cette étape, assurez-vous que vous avez accès à un [principal de service Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Vous devez également enregistrer l’ID d’application, la clé d’authentification et l’ID de locataire dont vous aurez besoin ultérieurement.

1. Ouvrez le fichier Program.cs créé, puis ajoutez les instructions using suivantes aux instructions au début du fichier :
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Ensuite, dans la méthode Main du fichier Program.cs, ajoutez des variables pour spécifier les valeurs communes utilisées dans le code :

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciption-id";
    var location = "westus";
    var vmName = "myVM";
    ```

    Remplacez **subscription-id** par l’identificateur de votre abonnement.

3. Pour créer les informations d’identification Active Directory dont vous avez besoin pour effectuer des requêtes, ajoutez cette méthode à la classe Program :
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("application-id", "authentication-key");
      var context = new AuthenticationContext("https://login.microsoftonline.com/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Remplacez **application-id**, **authentication-key** et **tenant-id** par les valeurs que vous avez recueillies lorsque vous avez créé votre principal de service Azure Active Directory .

4. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main dans le fichier Program.cs :
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

## <a name="create-resources"></a>Créer des ressources

### <a name="initialize-management-clients"></a>Initialiser les clients de gestion

Les clients de gestion sont nécessaires pour créer et gérer des ressources à l’aide du Kit de développement logiciel (SDK) .NET dans Azure. Pour créer les clients de gestion, ajoutez le code suivant à la méthode Main dans le fichier Program.cs :

```
var resourceManagementClient = new ResourceManagementClient(credential)
    { SubscriptionId = subscriptionId };
var networkManagementClient = new NetworkManagementClient(credential)
    { SubscriptionId = subscriptionId };
var computeManagementClient = new ComputeManagementClient(credential)
    { SubscriptionId = subscriptionId };
```

### <a name="create-the-vm-and-supporting-resources"></a>Créer la machine virtuelle et les ressources de prise en charge

Toutes les ressources doivent être contenues dans un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md).

1. Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName,
      string location)
    {   
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      resourceManagementClient,
      groupName,
      location);
    Console.WriteLine("Resource group creation: " + 
      rgResult.Result.Properties.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

Les [groupes à haute disponibilité](tutorial-availability-sets.md) facilitent la maintenance des machines virtuelles utilisées par votre application.

1. Pour créer un groupe à haute disponibilité, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        "myAVSet",
        new AvailabilitySet()
        { 
          Sku = new Microsoft.Azure.Management.Compute.Models.Sku("Aligned"),
          PlatformFaultDomainCount = 3,
          Location = location 
        });
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Availability set created. Press enter to continue...");
    Console.ReadLine();
    ```

Une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) est nécessaire pour communiquer avec la machine virtuelle.

1. Pour créer l’adresse IP publique pour la machine virtuelle, ajoutez cette méthode à la classe Program :
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync( 
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        "myIpAddress",
        new PublicIPAddress
        {
          Location = location,
          PublicIPAllocationMethod = "Dynamic"
        });
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("IP address creation: " + 
      ipResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Une machine virtuelle doit être incluse dans un sous-réseau d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

1. Pour créer un sous-réseau et un réseau virtuel, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = new Subnet
      {
        Name = "mySubnet",
        AddressPrefix = "10.0.0.0/24"
      };
      var address = new AddressSpace 
      {
        AddressPrefixes = new List<string> { "10.0.0.0/16" }
      };
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        "myVNet",
        new VirtualNetwork
        {
          Location = location,
          AddressSpace = address,
          Subnets = new List<Subnet> { subnet }
        });
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual network creation: " + 
      vnResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Une machine virtuelle a besoin d’une interface réseau pour communiquer sur le réseau virtuel.

1. Pour créer une interface réseau, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        "myVNet",
        "mySubnet");
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        "myIPaddress");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        "myNic",
        new NetworkInterface
        {
          Location = location,
          IpConfigurations = new List<NetworkInterfaceIPConfiguration>
          {
            new NetworkInterfaceIPConfiguration
            {
              Name = "myNic",
              PublicIPAddress = publicIP,
              Subnet = subnet
            }
          }
        });
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Network interface creation: " + 
      ncResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

1. Pour créer la machine virtuelle, ajoutez cette méthode à la classe Program :

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      NetworkManagementClient networkManagementClient,
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
 
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        "myNic");
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        "myAVSet");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        "myVM",
        new VirtualMachine
        {
          Location = location,
          AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
          {
            Id = avSet.Id
          },
          HardwareProfile = new HardwareProfile
          {
            VmSize = "Standard_DS1"
          },
          OsProfile = new OSProfile
          {
            AdminUsername = "azureuser",
            AdminPassword = "Azure12345678",
            ComputerName = "myVM",
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
            }
          }
        });
    }
    ```

    > [!NOTE]
    > Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      networkManagementClient,
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual machine creation: " + 
      vmResult.Result.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

## <a name="perform-management-tasks"></a>Effectuer d’autres tâches de gestion

Pendant le cycle de vie d’une machine virtuelle, vous souhaiterez exécuter des tâches de gestion telles que le démarrage, l’arrêt ou la suppression d’une machine virtuelle. En outre, vous souhaiterez peut-être créer du code pour automatiser les tâches répétitives ou complexes.

### <a name="get-information-about-the-vm"></a>Obtenir des informations sur la machine virtuelle

1. Pour obtenir des informations sur la machine virtuelle, ajoutez cette méthode à la classe Program :

    ```
    public static async void GetVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Getting information about the virtual machine...");

      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
        groupName,
        vmName,
        InstanceViewTypes.InstanceView);

      Console.WriteLine("hardwareProfile");
      Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

      Console.WriteLine("\nstorageProfile");
      Console.WriteLine("  imageReference");
      Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
      Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
      Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
      Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
      Console.WriteLine("  osDisk");
      Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
      Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
      Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
      Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
      Console.WriteLine("\nosProfile");
      Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
      Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
      Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
      Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

      Console.WriteLine("\nnetworkProfile");
      foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
      {
        Console.WriteLine("  networkInterface id: " + nic.Id);
      }

      Console.WriteLine("\nvmAgent");
      Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
      Console.WriteLine("    statuses");
      foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
      {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    message: " + stat.Message);
        Console.WriteLine("    time: " + stat.Time);
      }

      Console.WriteLine("\ndisks");
      foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
      {
        Console.WriteLine("  name: " + idisk.Name);
        Console.WriteLine("  statuses");
        foreach (InstanceViewStatus istat in idisk.Statuses)
        {
          Console.WriteLine("    code: " + istat.Code);
          Console.WriteLine("    level: " + istat.Level);
          Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
          Console.WriteLine("    time: " + istat.Time);
        }
      }

      Console.WriteLine("\nVM general status");
      Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
      Console.WriteLine("  id: " + vmResult.Id);
      Console.WriteLine("  name: " + vmResult.Name);
      Console.WriteLine("  type: " + vmResult.Type);
      Console.WriteLine("  location: " + vmResult.Location);
      Console.WriteLine("\nVM instance status");
      foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
      {
        Console.WriteLine("\n  code: " + istat.Code);
        Console.WriteLine("  level: " + istat.Level);
        Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
      }
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :

    ```
    GetVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Vous pouvez arrêter une machine virtuelle et conserver tous ses paramètres, mais continuer à être facturé, ou arrêter une machine virtuelle et la libérer. Lorsqu’une machine virtuelle est libérée, toutes les ressources qui lui sont associées sont également libérées et la facturation de la machine virtuelle prend fin.

1. Pour arrêter la machine virtuelle sans la libérer, ajoutez cette méthode à la classe Program :

    ```
    public static async void StopVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Stopping the virtual machine...");
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    Si vous souhaitez libérer la machine virtuelle, modifiez l’appel de mise hors tension avec ce code :

    ```
    await computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    StopVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="start-the-vm"></a>Démarrer la machine virtuelle

1. Pour démarrer la machine virtuelle, ajoutez cette méthode à la classe Program :

    ```
    public static async void StartVirtualMachineAsync(
      string groupName, 
      string vmName, 
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Starting the virtual machine...");
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    StartVirtualMachineAsync(
      groupName,
      vmName,
      ComputeManagementClient computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="resize-the-vm"></a>Redimensionner la machine virtuelle

Vous devez prendre en compte de nombreux aspects du déploiement lorsque vous déterminez la taille de votre machine virtuelle. Pour plus d’informations, voir [Tailles de machine virtuelle](sizes.md).  

1. Pour modifier la taille de la machine virtuelle, ajoutez cette méthode à la classe Program :

    ```
    public static async void UpdateVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Updating the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_DS3";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    UpdateVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Ajouter un disque de données à la machine virtuelle

1. Pour ajouter un disque de données à la machine virtuelle, ajoutez cette méthode à la classe Program :

    ```
    public static async void AddDataDiskAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
        {
          Lun = 0,
          Name = "myDataDisk1",
          CreateOption = DiskCreateOptionTypes.Empty,
          DiskSizeGB = 2,
          Caching = CachingTypes.ReadWrite
        });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    AddDataDiskAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

## <a name="delete-resources"></a>Supprimer des ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et l’ensemble des ressources de prise en charge, il vous suffit de supprimer le groupe de ressources.

1. Pour supprimer un groupe de ressources, ajoutez cette méthode à la classe Program :
   
    ```
    public static async void DeleteResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName)
    {
      Console.WriteLine("Deleting resource group...");
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. Pour appeler la méthode que vous avez ajoutée précédemment, ajoutez ce code à la méthode Main :
   
    ```   
    DeleteResourceGroupAsync(
      resourceManagementClient,
      groupName);
    Console.ReadLine();
    ```

3. Enregistrez le projet.

## <a name="run-the-application"></a>Exécution de l'application

1. Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2. Appuyez sur **Entrée** une fois que l’état de chaque ressource a été retourné. Dans les informations d’état, vous devez voir l’état d’approvisionnement **Réussi** . Une fois la machine virtuelle créée, vous pouvez supprimer toutes les ressources que vous avez créées. Avant d’appuyer sur **Entrée** pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier qu’elles ont bien été créées dans le Portail Azure. Si le Portail Azure est ouvert, vous devrez peut-être actualiser le panneau pour afficher les nouvelles ressources.  

    L’exécution complète de cette application console devrait durer cinq minutes environ. La suppression de l’ensemble des ressources et du groupe de ressources peut prendre quelques minutes après l’exécution de l’application.

## <a name="next-steps"></a>Étapes suivantes
* Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Déploiement d’une machine virtuelle Azure avec C# et un modèle Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Consultez [Bibliothèques Azure pour .NET](https://docs.microsoft.com/dotnet/azure/index?view=azure-dotnet) pour en savoir plus sur l’utilisation des bibliothèques Azure pour .NET.


