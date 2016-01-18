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
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Ce didacticiel vous montre comment utiliser les bibliothèques de calcul, de stockage et réseau .NET pour créer et supprimer des ressources dans Microsoft Azure. Il vous décrit également l’authentification des requêtes sur Microsoft Azure Manager à l’aide de Microsoft Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un compte Azure Storage](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Ces étapes prennent environ 30 minutes.

## Étape 1 : ajouter une application à Azure AD et définir les autorisations

Pour pouvoir utiliser Microsoft Azure AD afin d’authentifier les demandes pour Microsoft Azure Resource Manager, une application doit être ajoutée dans le répertoire par défaut. Pour en savoir plus, consultez l’article [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../resource-group-authenticate-service-principal.md).

1. Ouvrez une invite Azure PowerShell, exécutez cette commande, puis entrez les informations d’identification pour votre abonnement lorsqu’elles vous seront demandées :

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

5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Compute* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de calcul .NET, puis suivez les instructions d’installation du package.

6. Tapez *Microsoft.Azure.Management.Network* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques réseau .NET, puis suivez les instructions d’installation du package.

7. Tapez *Microsoft.Azure.Management.Storage* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques de stockage .NET, puis suivez les instructions d’installation du package.

8. Tapez *Microsoft.Azure.Management.Resources* dans la zone de recherche, puis cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour créer votre application.

## Étape 3 : Créer les informations d’identification utilisées pour authentifier les requêtes

Maintenant que l’application Microsoft Azure Active Directory est créée et que la bibliothèque d’authentification a été installée, il vous reste à mettre en forme les informations d’application en informations d’identification qui seront utilisées pour authentifier les requêtes formulées auprès de Microsoft Azure Resource Manager.

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions using suivantes au début du fichier :

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Azure.Management.Storage;
	using Microsoft.Azure.Management.Storage.Models;
	using Microsoft.Azure.Management.Network;
	using Microsoft.Azure.Management.Network.Models;
	using Microsoft.Azure.Management.Compute;
	using Microsoft.Azure.Management.Compute.Models;
	using Microsoft.Rest;
	```

2. Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Remplacez {application-id} par l’identificateur d’application que vous avez enregistré précédemment, {password} par le mot de passe que vous avez choisi pour l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureSubscription.

3. Ajoutez le code suivant à la méthode Main dans le fichier Program.cs pour créer les informations d’identification :

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Enregistrez le fichier Program.cs.

## Étape 4 : Ajouter le code pour inscrire les fournisseurs et créer les ressources

### Inscrire les fournisseurs et créer un groupe de ressources

Toutes les ressources doivent être contenues dans un groupe de ressources. Pour que vous puissiez ajouter des ressources à un groupe, votre abonnement doit être inscrit auprès des fournisseurs de ressources.

1. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms que vous souhaitez utiliser pour les ressources, l’emplacement des ressources, par exemple « Ouest des États-Unis », les informations de compte Administrateur et votre identificateur d’abonnement :

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   Remplacez tous les espaces réservés entre crochets par les noms de votre choix. Pour trouver l’identificateur d’abonnement, exécutez Get-AzureSubscription.

2. Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Créez un compte de stockage.

Un compte de stockage est nécessaire pour stocker le fichier du disque dur virtuel généré pour la machine virtuelle.

1. Ajoutez la méthode suivante à la classe Program pour créer un compte de stockage :

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Créer une adresse IP publique

Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle.

1. Ajoutez la méthode suivante à la classe Program pour créer une adresse IP publique :

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Créer un réseau virtuel

Une machine virtuelle créée avec le modèle de déploiement de Resource Manager doit faire partie d’un réseau virtuel.

1. Ajoutez la méthode suivante à la classe Program pour créer un sous-réseau et un réseau virtuel :

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Créer un groupe à haute disponibilité

Les groupes à haute disponibilité facilitent la gestion de la maintenance des machines virtuelles utilisées par votre application.

1. Ajoutez la méthode suivante à la classe Program pour créer un groupe à haute disponibilité :

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. Ajoutez le code suivant à la méthode Main de la classe Program pour appeler la méthode que vous venez d’ajouter :

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Création d'une machine virtuelle

Maintenant que vous avez créé l’ensemble des ressources de prise en charge, vous pouvez créer une machine virtuelle.

1. Ajoutez la méthode suivante à la classe Program pour créer une machine virtuelle :

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
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
						CreateOption = "FromImage",
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
	```

	>[AZURE.NOTE]Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure](resource-groups-vm-searching.md).

2. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##Étape 5 : Ajouter du code pour supprimer les ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et l’ensemble des ressources de prise en charge, il vous suffit de supprimer le groupe de ressources.

1. Ajoutez la méthode suivante à la classe Program pour supprimer le groupe de ressources :

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## Étape 6 : Exécuter l’application console

1. Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2. Pour créer chaque ressource, appuyez sur **Entrée** après le renvoi de chaque code d’état. Une fois la machine virtuelle créée, effectuez l’étape suivante, puis appuyez sur Entrée pour supprimer toutes les ressources.

	L’exécution complète de cette application console devrait durer 5 minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.

3. Parcourez les journaux d’audit dans le portail Azure pour connaître l’état des ressources :

	![Création d'une application Active Directory](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->