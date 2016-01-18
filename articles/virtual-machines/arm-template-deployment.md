<properties
	pageTitle="Déploiement de ressources Azure à l’aide d’un modèle | Microsoft Azure"
	description="Découvrez comment utiliser certains des clients disponibles dans la bibliothèque Azure Resource Management pour déployer une machine virtuelle, un réseau virtuel et un compte de stockage."
	services="virtual-machines,virtual-networks,storage"
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
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Grâce aux modèles et aux groupes de ressources, vous pouvez gérer en même temps toutes les ressources qui prennent en charge votre application. Ce didacticiel vous montre comment utiliser certains des clients disponibles dans la bibliothèque Azure Resource Management et comment créer un modèle pour déployer une machine virtuelle, un réseau virtuel et un compte de stockage.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un compte Azure Storage](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE]Le compte de stockage que vous créez à ce stade est utilisé pour stocker le modèle. Un autre compte de stockage est créé lorsque vous déployez le modèle qui est utilisé pour stocker le disque de la machine virtuelle. Créez un conteneur dans ce compte de stockage nommé Modèles.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Ces étapes prennent environ 30 minutes.

## Étape 1 : ajouter une application à Azure AD et définir les autorisations

Pour pouvoir utiliser Microsoft Azure AD afin d’authentifier les demandes pour Microsoft Azure Resource Manager, une application doit être ajoutée dans le répertoire par défaut. Pour ajouter une application, procédez comme suit :

1. Ouvrez une invite de commandes Azure PowerShell, exécutez cette commande et entrez les informations d’identification pour votre abonnement lorsqu’elles vous seront demandées :

			Login-AzureRmAccount

2. Remplacez {password} dans la commande suivante avec celui que vous souhaitez utiliser et exécutez-le pour créer l’application :

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Prenez note de l’identificateur d’application fourni une fois l’application créée. Vous en aurez besoin pour l’étape suivante. Vous trouverez également l’identificateur d’application dans le champ d’ID client de la section Active Directory du portail Azure.

3. Remplacez {application-id} par l’identificateur que vous venez d’enregistrer, puis créez le principal du service pour l’application :

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Définissez les autorisations relatives à l’utilisation de l’application :

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Étape 2 : créer le projet Visual Studio, le fichier de modèle et le fichier de paramètres

### Créer le fichier de modèle

Avec un modèle Azure Resource Manager, vous pouvez déployer et gérer des ressources Azure en même temps à l’aide d’une description JSON des ressources et des paramètres de déploiement associés. Le modèle que vous créez dans ce didacticiel est similaire à un modèle qui se trouve dans la galerie de modèles. Pour en savoir plus, consultez [Déployer une machine virtuelle Windows simple dans l’Ouest des États-Unis](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

Dans Visual Studio, procédez comme suit :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.

2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** > **Nouvel élément**.

4. Dans la fenêtre Ajouter un nouvel élément, sélectionnez **Fichier texte**, entrez *VirtualMachineTemplate.json* pour le nom, puis cliquez sur **Ajouter**.

5. Ouvrez le fichier VirtualMachineTemplate.json et ajoutez les parenthèses ouvrante et fermante, l’élément de schéma requis et l’élément contentVersion requis :

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. Les [paramètres](../resource-group-authoring-templates.md#parameters) ne sont pas toujours requis, mais ils facilitent la gestion de modèle. Ils décrivent le type de la valeur, la valeur par défaut le cas échéant, et éventuellement les valeurs autorisées du paramètre. Pour ce didacticiel, les paramètres utilisés pour créer une machine virtuelle, un compte de stockage et un réseau virtuel sont ajoutés au modèle.

    Ajoutez l’élément parameters et ses éléments enfants après l’élément contentVersion :

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. Les [variables](../resource-group-authoring-templates.md#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres.

    Ajoutez l’élément variables après la section parameters :

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
	}
	```

8. Les [ressources](../resource-group-authoring-templates.md#resources) telles que la machine virtuelle, le réseau virtuel et le compte de stockage sont ensuite définis dans le modèle.

    Ajoutez la section resources après la section variables :

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/publicIPAddresses",
				"name": "[variables('publicIPAddressName')]",
				"location": "[variables('location')]",
				"properties": {
					"publicIPAllocationMethod": "[variables('publicIPAddressType')]",
					"dnsSettings": {
						"domainNameLabel": "[parameters('dnsNameForPublicIP')]"
					}
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/virtualNetworks",
				"name": "[variables('virtualNetworkName')]",
				"location": "[variables('location')]",
				"properties": {
					"addressSpace": {
						"addressPrefixes": [ "[variables('addressPrefix')]" ]
					},
					"subnets": [ {
						"name": "[variables('subnetName')]",
						"properties": {
							"addressPrefix": "[variables('subnetPrefix')]"
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/networkInterfaces",
				"name": "[variables('nicName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
					"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
				],
				"properties": {
					"ipConfigurations": [ {
						"name": "ipconfig1",
						"properties": {
							"privateIPAllocationMethod": "Dynamic",
							"publicIPAddress": {
								"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
							},
							"subnet": {
								"id": "[variables('subnetRef')]"
							}
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-06-15",
				"type": "Microsoft.Compute/virtualMachines",
				"name": "[variables('vmName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
					"[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
				],
				"properties": {
					"hardwareProfile": {
						"vmSize": "[variables('vmSize')]"
					},
					"osProfile": {
						"computerName": "[variables('vmName')]",
						"adminUsername": "[parameters('adminUsername')]",
						"adminPassword": "[parameters('adminPassword')]",
					},
					"storageProfile": {
						"imageReference": {
							"publisher": "[variables('imagePublisher')]",
							"offer": "[variables('imageOffer')]",
							"sku": "[parameters('windowsOSVersion')]",
							"version" : "latest"
						},
						"osDisk": {
							"name": "osdisk",
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
							},
							"caching": "ReadWrite",
							"createOption": "FromImage"
						}
					},
					"networkProfile": {
						"networkInterfaces" : [ {
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
						} ]
					}
				}
			} ]
		}
		```

9. Enregistrez le fichier de modèle que vous avez créé.

### Créer le fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs et envoyez-le au Gestionnaire de ressources avec le modèle. Dans Visual Studio, procédez comme suit :

1. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** et **Nouvel élément**.

2. Dans la fenêtre Ajouter un nouvel élément, sélectionnez **Fichier texte**, entrez *Parameters.json* pour le nom, puis cliquez sur **Ajouter**.

3. Ouvrez le fichier parameters.json et ajoutez le contenu JSON suivant :

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "value": "mytestsa1" },
			"adminUserName": { "value": "mytestacct1" },
			"adminPassword": { "value": "mytestpass1" },
			"dnsNameForPublicIP": { "value": "mytestdns1" }
		}
	}
	```

    >[AZURE.NOTE]Ce didacticiel crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure](resource-groups-vm-searching.md).


4. Enregistrez le fichier de paramètres que vous avez créé.

### Téléchargement des fichiers

Le fichier de modèle et le fichier de paramètres sont accessibles via le Gestionnaire de ressources Azure à partir d’un compte de stockage Azure. Pour placer les fichiers dans le premier espace de stockage que vous avez créé, procédez comme suit :

1. Ouvrez l’Explorateur de serveurs et accédez au conteneur de votre compte de stockage dans lequel vous souhaitez placer le fichier. Pour ce didacticiel, le conteneur dans lequel se trouve le modèle est nommé « templates ».

2. Dans le coin supérieur droit du volet du conteneur de modèles, cliquez sur l’icône Télécharger un objet Blob, accédez au fichier VirtualMachineTemplate.json que vous avez créé, puis cliquez sur **Ouvrir**.

3. Cliquez de nouveau sur l’icône Télécharger un objet Blob, accédez au fichier Parameters.json que vous avez créé, puis cliquez sur **Ouvrir**.

## Étape 3 : installer les bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous devez installer la bibliothèque Azure Resource Management et la bibliothèque d’authentification Azure Active Directory. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.

2. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.

3. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Resources* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management, puis suivez les instructions pour installer le package.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour créer votre application.

##Étape 4 : créer les informations d’identification utilisées pour authentifier les requêtes

Maintenant que l’application Azure Active Directory est créée et que la bibliothèque d’authentification a été installée, il vous reste à mettre en forme les informations d’application en informations d’identification qui seront utilisées pour authentifier les requêtes formulées auprès d’Azure Resource Manager. Effectuez les actions suivantes :

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions using suivantes au début du fichier :

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

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

4. Enregistrez le fichier Program.cs.


## Étape 5 : ajouter le code pour déployer le modèle

Les ressources sont toujours déployées à partir d’un modèle dans un groupe de ressources. Utilisez les classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) et [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) pour créer le groupe de ressources dans lequel seront déployées les ressources.

1. Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :

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
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	Remplacez {group-name} par le nom que vous souhaitez utiliser pour le groupe de ressources. Remplacez {subscription-id} par votre identificateur d’abonnement que vous pouvez obtenir en exécutant Get-AzureSubscription. Remplacez l’emplacement par la région dans laquelle vous souhaitez créer les ressources, par exemple « Ouest des États-Unis ».

3. Ajoutez la méthode suivante à la classe Program pour déployer les ressources dans le groupe de ressources en utilisant le modèle que vous avez défini :

	```
	public static void CreateTemplateDeployment(
		TokenCredentials credential,
		string groupName,
		string storageName,
		string deploymentName,
		string subscriptionId)
	{
		Console.WriteLine("Creating the template deployment...");
		var deployment = new Deployment();
		deployment.Properties = new DeploymentProperties
		{
			Mode = DeploymentMode.Incremental,
			TemplateLink = new TemplateLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
			}
		};
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
			groupName,
			deploymentName,
			deployment);
		Console.WriteLine(dpResult.Properties.ProvisioningState);
	}
	```

	Remplacez {storage-name} par le nom du compte dans lequel vous avez précédemment placé les fichiers.

4. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    Remplacez {group-name} par le nom du groupe de ressources que vous avez créé. Remplacez {storage-name} par le nom du compte de stockage dans lequel vous avez placé les fichiers de modèles. Remplacez {deployment-name} par le nom de votre choix pour identifier l’ensemble des ressources de déploiement. Remplacez {subscription-id} par votre identificateur d’abonnement que vous pouvez obtenir en exécutant Get-AzureSubscription.

##Étape 6 : ajouter du code pour supprimer les ressources

Étant donné que les ressources utilisées dans Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Vous n’avez pas besoin de supprimer séparément les ressources d’un groupe de ressources. Vous pouvez supprimer le groupe de ressources et toutes ses ressources seront automatiquement supprimées.

1.	Ajoutez la méthode suivante à la classe Program pour effacer un groupe de ressource :

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

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    Remplacez {group-name} par le nom du groupe de ressources que vous avez créé. Remplacez {subscription-id} par votre identificateur d’abonnement que vous pouvez obtenir en exécutant Get-AzureSubscription.

##Étape 7 : exécuter l’application console

1.	Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2.	Appuyez sur **Entrée** lorsque l’état Accepté s’affiche.

	L’exécution complète de cette application console devrait durer 5 minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.

3. Parcourez les journaux d’audit dans le portail Azure pour connaître l’état des ressources :

	![Création d'une application Active Directory](./media/arm-template-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->