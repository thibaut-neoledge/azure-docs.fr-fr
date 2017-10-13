---
title: "Créer et gérer une machine virtuelle Azure à l’aide de C# | Microsoft Docs"
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
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 5d9021c2f65b70e36d5ea82992c9fb9d2d6d394a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Créer et gérer des machines virtuelles Windows dans Azure à l’aide de C# #

Une [machine virtuelle Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a besoin de plusieurs ressources de prise en charge Azure. Cet article décrit comment créer, gérer et supprimer des ressources de machine virtuelle à l’aide de C#. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Visual Studio
> * Installer le package
> * Créer des informations d’identification
> * Créer des ressources
> * Effectuer les tâches de gestion
> * Supprimer des ressources
> * Exécution de l'application

Ces étapes prennent environ 20 minutes.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Si vous ne l’avez pas déjà fait, installez [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Sélectionnez **Développement .NET Desktop** dans la page Charges de travail, puis cliquez sur **Installer**. Dans le résumé, vous pouvez voir que les **Outils de développement .NET Framework 4 - 4.6** sont automatiquement sélectionnés. Si vous avez déjà installé Visual Studio, vous pouvez ajouter la charge de travail .NET en utilisant le lanceur Visual Studio.
2. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.
3. Dans **Modèles** > **Visual C#**, sélectionnez **Application console (.NET Framework)**, entrez le nom de projet *myDotnetProject*, sélectionnez l’emplacement du projet, puis cliquez sur **OK**.

## <a name="install-the-package"></a>Installer le package

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ces étapes. Pour obtenir les bibliothèques dont vous avez besoin dans Visual Studio, suivez ces étapes :

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet**, puis sur **Console du gestionnaire de package**.
2. Dans la console, tapez la commande suivante :

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Créer des informations d’identification

Avant de commencer cette étape, assurez-vous que vous avez accès à un [principal de service Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Vous devez également enregistrer l’ID d’application, la clé d’authentification et l’ID de client dont vous aurez besoin dans une étape ultérieure.

### <a name="create-the-authorization-file"></a>Créer le fichier d’autorisation

1. Dans l’Explorateur de solutions, cliquez sur *myDotnetProject* > **Ajouter** > **Nouvel élément**, puis sélectionnez **Fichier texte** dans *Éléments Visual C#*. Nommez le fichier *azureauth.properties*, puis cliquez sur **Ajouter**.
2. Ajoutez ces propriétés d’autorisation :

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Remplacez **&lt;subscription-id&gt;** par votre identificateur d’abonnement, **&lt;application-id&gt;** par l’identificateur d’application Active Directory, **&lt;authentication-key&gt;** par la clé d’application et **&lt;tenant-id&gt;** par l’identificateur du locataire.

3. Enregistrez le fichier azureauth.properties. 
4. Définissez une variable d’environnement dans Windows nommée AZURE_AUTH_LOCATION avec le chemin complet au fichier d’autorisation que vous avez créé. Vous pouvez par exemple utiliser la commande PowerShell suivante :

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Créer le client de gestion

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes à celles qui existent au début du fichier :

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Pour créer le client de gestion, ajoutez ce code à la méthode Main :

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Créer des ressources

### <a name="create-the-resource-group"></a>Créer le groupe de ressources

Toutes les ressources doivent être contenues dans un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md).

Pour spécifier des valeurs pour l’application et créer le groupe de ressources, ajoutez ce code à la méthode Main :

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Créer le groupe à haute disponibilité

Les [groupes à haute disponibilité](tutorial-availability-sets.md) facilitent la maintenance des machines virtuelles utilisées par votre application.

Pour créer le groupe à haute disponibilité, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Créer une adresse IP publique

Une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) est nécessaire pour communiquer avec la machine virtuelle.

Pour créer l’adresse IP publique pour la machine virtuelle, ajoutez ce code à la méthode Main :
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Une machine virtuelle doit être incluse dans un sous-réseau d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

Pour créer un sous-réseau et un réseau virtuel, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Créer l’interface réseau

Une machine virtuelle a besoin d’une interface réseau pour communiquer sur le réseau virtuel.

Pour créer une interface réseau, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

Pour créer la machine virtuelle, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Si vous souhaitez utiliser un disque existant au lieu d’une image marketplace, utilisez ce code :

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Effectuer les tâches de gestion

Pendant le cycle de vie d’une machine virtuelle, vous souhaiterez exécuter des tâches de gestion telles que le démarrage, l’arrêt ou la suppression d’une machine virtuelle. En outre, vous souhaiterez peut-être créer du code pour automatiser les tâches répétitives ou complexes.

Quand vous avez besoin d’effectuer une action avec la machine virtuelle, vous devez en obtenir une instance :

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Obtenir des informations sur la machine virtuelle

Pour obtenir des informations sur la machine virtuelle, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Vous pouvez arrêter une machine virtuelle et conserver tous ses paramètres, mais continuer à être facturé, ou arrêter une machine virtuelle et la libérer. Lorsqu’une machine virtuelle est libérée, toutes les ressources qui lui sont associées sont également libérées et la facturation de la machine virtuelle prend fin.

Pour arrêter la machine virtuelle sans la libérer, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Si vous souhaitez libérer la machine virtuelle, modifiez l’appel de mise hors tension avec ce code :

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Démarrer la machine virtuelle

Pour démarrer la machine virtuelle, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Redimensionner la machine virtuelle

De nombreux aspects du déploiement doivent être pris en considération lors du choix d’une taille pour votre machine virtuelle. Pour plus d’informations, voir [Tailles des machines virtuelles](sizes.md).  

Pour changer la taille de la machine virtuelle, ajoutez ce code à la méthode Main :

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Ajouter un disque de données à la machine virtuelle

Pour ajouter un disque de données à la machine virtuelle, ajoutez ce code à la méthode Main pour ajouter un disque de données d’une taille de 2 Go, avec un numéro d’unité logique de 0 et un type de mise en cache Lecture/écriture :

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Supprimer des ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et l’ensemble des ressources de prise en charge, il vous suffit de supprimer le groupe de ressources.

Pour supprimer le groupe de ressources, ajoutez ce code à la méthode Main :

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Exécution de l'application

L’exécution complète de cette application console devrait durer cinq minutes environ. 

1. Pour exécuter l’application console, cliquez sur **Démarrer**.

2. Avant d’appuyer sur **Entrée** pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées. Cliquez sur l’état du déploiement pour afficher des informations sur le déploiement.

## <a name="next-steps"></a>Étapes suivantes
* Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Déploiement d’une machine virtuelle Azure avec C# et un modèle Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Consultez [Bibliothèques Azure pour .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet) pour en savoir plus sur l’utilisation des bibliothèques Azure pour .NET.

