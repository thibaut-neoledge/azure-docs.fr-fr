---
title: Gérer les machines virtuelles à l’aide d’Azure Resource Manager et de C# | Microsoft Docs
description: Gérez les machines virtuelles à l’aide de modèles Azure Resource Manager et C#.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/24/2016
ms.author: davidmu

---
# Gestion des machines virtuelles Azure à l’aide de modèles Azure Resource Manager et de C
Les tâches présentées dans cet article vous montrent comment gérer des machines virtuelles, avec notamment le démarrage, l’arrêt et la mise à jour.

Pour effectuer les tâches décrites dans cet article, vous avez besoin de ce qui suit :

* [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [Un jeton d’authentification](../resource-group-authenticate-service-principal.md)

## Création d’un projet Visual Studio et installation des packages
Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer les tâches de cet article. Vous devez installer la bibliothèque d’authentification Azure Active Directory et la bibliothèque des fournisseurs de ressources informatiques. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.
2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
4. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.
5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Compute* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de calcul .NET, puis suivez les instructions d’installation du package.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour gérer vos machines virtuelles.

## Configuration du projet
Maintenant que l’application Microsoft Azure Active Directory est créée et que la bibliothèque d’authentification a été installée, il vous reste à mettre en forme les informations d’application en informations d’identification qui seront utilisées pour authentifier les requêtes formulées auprès de Microsoft Azure Resource Manager.

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
2. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms des ressources que vous souhaitez utiliser, l’emplacement des ressources, par exemple « États-Unis du Centre » et votre identificateur d’abonnement :
   
        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";
   
    Remplacez toutes les valeurs des variables avec le nom et l'identificateur que vous souhaitez utiliser. Pour trouver l’identificateur d’abonnement, exécutez Get-AzureRmSubscription.
3. Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :
   
        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return result;
        }
   
    Remplacez {client-id} par l’identificateur d’application Azure Active Directory, {client-secret} par la clé d’accès de l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureRmSubscription. Vous pouvez trouver la clé d’accès à l’aide du portail Azure.
4. Ajoutez le code suivant à la méthode Main dans Program.cs pour créer les informations d’identification.
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
5. Enregistrez le fichier Program.cs.

## Affichage des informations relatives à une machine virtuelle
1. Ajoutez cette méthode à la classe Program dans le projet que vous avez créé précédemment :
   
        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");
   
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
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
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
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
2. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :
   
        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
3. Enregistrez le fichier Program.cs.
4. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.
   
    Lorsque vous exécutez cette méthode, vous devriez voir quelque chose comme ceci :
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0
   
        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite
   
          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True
   
          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1
   
          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM
   
          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM
   
          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus
   
          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## Démarrage d'une machine virtuelle
1. Mettez le code précédemment ajouté à la méthode Main en commentaire, sauf le code des informations d’identification.
2. Ajoutez cette méthode à la classe Program :
   
        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }
3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :
   
        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Enregistrez le fichier Program.cs.
5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.
   
    Vous devriez voir l'état de la machine virtuelle changer sur En cours d'exécution.

## Arrêt d’une machine virtuelle
Vous pouvez arrêter une machine virtuelle de deux manières. Vous pouvez arrêter une machine virtuelle et conserver tous ses paramètres, mais continuer à être facturé, ou arrêter une machine virtuelle et la libérer, ce qui libère également toutes les ressources associées et met fin à la facturation de la machine virtuelle.

1. Mettez le code précédemment ajouté à la méthode Main en commentaire, sauf le code des informations d’identification.
2. Ajoutez cette méthode à la classe Program :
   
        public static void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }
   
    Si vous souhaitez libérer la machine virtuelle, modifiez l'appel de mise hors tension avec ce qui suit :
   
        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :
   
        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Enregistrez le fichier Program.cs.
5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.
   
    Vous devriez voir l'état de la machine virtuelle changer sur Arrêté Si vous avez exécuté la méthode qui appelle la fonction Libérer, l'état est Arrêté (libéré).

## Redémarrage d’une machine virtuelle en cours d’exécution
1. Mettez le code précédemment ajouté à la méthode Main en commentaire, sauf le code des informations d’identification.
2. Ajoutez cette méthode à la classe Program :
   
        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }
3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :
   
        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Enregistrez le fichier Program.cs.
5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

## Suppression d'une machine virtuelle
1. Mettez le code précédemment ajouté à la méthode Main en commentaire, sauf le code des informations d’identification.
2. Ajoutez cette méthode à la classe Program :
   
        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }
3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :
   
        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Enregistrez le fichier Program.cs.
5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

## Mise à jour d’une machine virtuelle
Cet exemple montre comment modifier la taille d'une machine virtuelle en cours d'exécution.

1. Mettez le code précédemment ajouté à la méthode Main en commentaire, sauf le code des informations d’identification.
2. Ajoutez cette méthode à la classe Program :
   
        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }
3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :
   
        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Enregistrez le fichier Program.cs.
5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.
   
    Vous devriez voir la taille de la machine virtuelle changer sur Standard\_A1

## Étapes suivantes
Si vous rencontrez des problèmes avec un déploiement, vous pouvez consulter [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md)

<!---HONumber=AcomDC_0720_2016-->