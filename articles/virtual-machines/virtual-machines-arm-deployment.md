<properties
	pageTitle="Déployer des ressources à l’aide de bibliothèques .NET | Microsoft Azure"
	description="Ce didacticiel vous montre comment utiliser les bibliothèques de calcul, de stockage et réseau .NET pour créer et supprimer des ressources dans Microsoft Azure à l’aide de Resource Manager."
	services="virtual-machines,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Ce didacticiel vous montre comment utiliser certains des clients disponibles dans les bibliothèques de traitement, de stockage et réseau .NET afin de créer et de supprimer des ressources dans Microsoft Azure. Il vous décrit également l’authentification des requêtes sur Microsoft Azure Manager à l’aide de Microsoft Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un compte Azure Storage](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/fr-FR/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/fr-FR/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Ces étapes prennent environ 30 minutes.

## Étape 1 : ajouter une application à Azure AD et définir les autorisations

Pour pouvoir utiliser Microsoft Azure AD afin d’authentifier les demandes pour Microsoft Azure Resource Manager, une application doit être ajoutée dans le répertoire par défaut. Pour ajouter une application, procédez comme suit :

1. Ouvrez une invite Azure PowerShell, exécutez cette commande et entrez les informations d’identification pour votre abonnement lorsqu’elles vous seront demandées :

	    Login-AzureRmAccount

2. Remplacez {password} dans la commande suivante avec celui que vous souhaitez utiliser et exécutez-le pour créer l’application :

	    New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Prenez note de l’identificateur d’application fourni une fois l’application créée. Vous en aurez besoin pour l’étape suivante. Vous trouverez également l’identificateur d’application dans le champ d’ID client de la section Active Directory du portail Azure.

3. Remplacez {application-id} par l’identificateur que vous venez d’enregistrer, puis créez le principal du service pour l’application :

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Établissez les autorisations relatives à l’utilisation de l’application :

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Étape 2 : Créer un projet Visual Studio et installez les bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous devez installer la bibliothèque Microsoft Azure Resource Management, la bibliothèque d’authentification Microsoft Azure Active Directory et la bibliothèque des fournisseurs de ressources informatiques. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.

2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.

4. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.

5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Entrez *Calcul Microsoft Azure* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de calcul .NET, puis suivez les instructions d’installations du package.

6. Entrez *Réseau Microsoft Azure* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques réseau .NET, puis suivez les instructions d’installation du package.

7. Entrez *Microsoft Azure Storage* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de réseau .NET, puis suivez les instructions d’installation du package.

8. Entrez *Microsoft Azure Resource Management* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour créer votre application.

## Étape 3 : Créer les informations d’identification utilisées pour authentifier les requêtes

Maintenant que l’application Microsoft Azure Active Directory est créée et que la bibliothèque d’authentification a été installée, il vous reste à mettre en forme les informations d’application en informations d’identification qui seront utilisées pour authentifier les requêtes formulées auprès de Microsoft Azure Resource Manager. Effectuez les actions suivantes :

1.	Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions using suivantes au début du fichier :

    using Microsoft.Azure; using Microsoft.IdentityModel.Clients.ActiveDirectory; using Microsoft.Azure.Management.Resources; using Microsoft.Azure.Management.Resources.Models; using Microsoft.Azure.Management.Storage; using Microsoft.Azure.Management.Storage.Models; using Microsoft.Azure.Management.Network; using Microsoft.Azure.Management.Network.Models; using Microsoft.Azure.Management.Compute; using Microsoft.Azure.Management.Compute.Models;


2. Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);

          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Remplacez {application-id} par l’identificateur d’application que vous avez enregistré précédemment, {password} par le mot de passe que vous avez choisi pour l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureSubscription.

3.	Ajoutez le code suivant à la méthode Main dans le fichier Program.cs pour créer les informations d’identification :

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	Remplacez {subscription-id} par l’identificateur de votre abonnement.

4.	Enregistrez le fichier Program.cs.

## Étape 4 : Ajouter le code pour inscrire les fournisseurs et créer les ressources

### Inscrire les fournisseurs et créer un groupe de ressources

Les ressources sont toujours déployées dans un groupe de ressources. Utilisez les classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) et [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) pour créer le groupe de ressources dans lequel seront déployées les ressources.

1.	Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");

          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		CreateResourceGroup(credential);
		Console.ReadLine();

###Créez un compte de stockage.

Un compte de stockage est nécessaire pour stocker le fichier du disque dur virtuel généré pour la machine virtuelle.

1.	Ajoutez la méthode suivante à la classe Program pour créer un compte de stockage :

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");

          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		CreateStorageAccount(credential);
		Console.ReadLine();

###Créer la configuration de la mise en réseau

Une machine virtuelle est plus productive lorsqu’elle est ajoutée à un réseau virtuel.

1.	Ajoutez la méthode suivante à la classe Program pour créer un sous-réseau, une adresse IP publique et un réseau virtuel :

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		CreateNetwork(credential);
		Console.ReadLine();

###Création d'une machine virtuelle

Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

1.	Ajoutez la méthode suivante à la classe Program pour créer la machine virtuelle :

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);

            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]Les noms d’images de disque dur virtuel changent régulièrement dans la galerie d’images. Vous devez donc obtenir le nom d’une image actuelle pour déployer la machine virtuelle. Pour ce faire, consultez la page [Gérer les images Windows à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx), puis remplacez {source-image-name} par le nom du fichier de disque dur virtuel que vous souhaitez utiliser. Par exemple, « a699494373c04fc0bc8f2bb1389d6106\_\_Windows-Server-2012-R2-201411.01-en.us-127GB.vhd ».

	Remplacez {subscription-id} par l’identificateur de votre abonnement.

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		CreateVirtualMachine(credential);
    Console.ReadLine();

##Étape 5 : Ajouter du code pour supprimer les ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et l’ensemble des ressources de prise en charge, il vous suffit de supprimer le groupe de ressources.

1.	Ajoutez la méthode suivante à la classe Program pour supprimer le groupe de ressources :

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		DeleteResourceGroup(credential);
        Console.ReadLine();

##Étape 6 : Exécuter l’application console

1.	Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2.	Pour créer chaque ressource, appuyez sur **Entrée** après le renvoi de chaque code d’état. Une fois la machine virtuelle créée, effectuez l’étape suivante, puis appuyez sur Entrée pour supprimer toutes les ressources.

	L’exécution complète de cette application console devrait durer 5 minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression de ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.

3. Parcourez les journaux d’audit dans le portail Azure pour connaître l’état des ressources :

	![Création d'une application Active Directory](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_1203_2015-->