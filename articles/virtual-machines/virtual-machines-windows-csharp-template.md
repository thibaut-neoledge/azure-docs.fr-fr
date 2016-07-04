<properties
	pageTitle="Déployer une machine virtuelle à l’aide de C# et d’un modèle Resource Manager | Microsoft Azure"
	description="Apprenez à utiliser C# et un modèle Resource Manager pour déployer une machine virtuelle Azure."
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
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Déployer une machine virtuelle Azure à l’aide de C# et d’un modèle Resource Manager

Grâce aux modèles et aux groupes de ressources, vous pouvez gérer ensemble toutes les ressources qui prennent en charge votre application. Cet article vous montre comment configurer l’authentification et le stockage avec Azure PowerShell, puis comment générer et déployer un modèle avec C# pour créer des ressources Azure.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [Un jeton d’authentification](../resource-group-authenticate-service-principal.md)

Ces étapes prennent environ 30 minutes.

## Étape 1 : installer Azure PowerShell

Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour savoir comment installer la dernière version d’Azure PowerShell, sélectionner l’abonnement à utiliser et vous connecter à votre compte Azure.
    
## Étape 2 : Créer un groupe de ressources pour stocker le modèle

Toutes les ressources doivent être déployées dans un groupe de ressources. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md).

1. Obtenez la liste des emplacements disponibles où créer des ressources.

	    Get-AzureRmLocation | sort Location | Select Location
        
2. Remplacez la valeur de **$locName** par un emplacement de la liste, par exemple **centralus**. Créez la variable.

        $locName = "location name"
        
3. Remplacez la valeur de **$rgName** par le nom du nouveau groupe de ressources. Créez la variable et le groupe de ressources.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Le résultat suivant devrait s'afficher :
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## Étape 3 : Créer un compte de stockage et le conteneur de modèles

Un compte de stockage est nécessaire pour stocker le modèle que vous allez créer et déployer.

1. Remplacez la valeur de $stName (lettres minuscules et chiffres uniquement) par le nom du compte de stockage. Testez l’unicité du nom choisi.

        $stName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $stName

    Si cette commande renvoie **True**, le nom proposé est unique.
    
2. Maintenant, exécutez cette commande pour créer le compte de stockage.
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName
        
3. Remplacez {storage-blob-endpoint} par le point de terminaison du Blob Storage dans votre compte. Remplacez {storageaccountname} par le nom de votre compte de stockage. Remplacez {primary-storage-key} par la touche primaire d’accès rapide. Exécutez ces commandes pour créer le conteneur stockant les fichiers. Vous pouvez obtenir les valeurs de point de terminaison et de clé sur le portail Azure.

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## Étape 3 : Créer le projet Visual Studio, le fichier de modèle et le fichier de paramètres

### Créer le fichier de modèle

Avec un modèle Azure Resource Manager, vous pouvez déployer et gérer des ressources Azure en même temps à l’aide d’une description JSON des ressources et des paramètres de déploiement associés. Le modèle que vous créez dans ce didacticiel est similaire à un modèle qui se trouve dans la galerie de modèles. Pour en savoir plus, consultez [Déployer une machine virtuelle Windows simple dans l’Ouest des États-Unis](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/).

Dans Visual Studio, procédez comme suit :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.

2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** > **Nouvel élément**.

4. Cliquez sur Web, sélectionnez Fichier JSON, entrez le nom *VirtualMachineTemplate.json*, puis cliquez sur **Ajouter**.

5. Entre les parenthèses ouvrantes et fermantes du fichier VirtualMachineTemplate.json, ajoutez l’élément de schéma requis et l’élément contentVersion requis :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. Les [paramètres](../resource-group-authoring-templates.md#parameters) ne sont pas toujours requis, mais ils facilitent la gestion de modèle. Ils décrivent le type de la valeur, la valeur par défaut le cas échéant, et éventuellement les valeurs autorisées du paramètre. Dans ce didacticiel, les paramètres utilisés pour créer une machine virtuelle, un compte de stockage et un réseau virtuel sont ajoutés au modèle. Ajoutez l’élément parameters et ses éléments enfants après l’élément contentVersion :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. Les [variables](../resource-group-authoring-templates.md#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres. Ajoutez l’élément variables après la section parameters :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
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

8. Les [ressources](../resource-group-authoring-templates.md#resources) telles que la machine virtuelle, le réseau virtuel et le compte de stockage sont ensuite définis dans le modèle. Ajoutez la section resources après la section variables :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
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
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
                    "sku": "[variables('windowsOSVersion')]",
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
      
9. Enregistrez le fichier de modèle que vous avez créé.

### Créer le fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs et envoyez-le au Gestionnaire de ressources avec le modèle. Dans Visual Studio, procédez comme suit :

1. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** et **Nouvel élément**.

2. Cliquez sur Web, sélectionnez Fichier JSON, entrez le nom *Parameters.json*, puis cliquez sur **Ajouter**.

3. Ouvrez le fichier parameters.json et ajoutez le contenu JSON suivant :

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

    >[AZURE.NOTE] Cet article crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Enregistrez le fichier de paramètres que vous avez créé.

### Télécharger les fichiers et définir l’autorisation permettant de les utiliser 

Le fichier de modèle et le fichier de paramètres sont accessibles via le Gestionnaire de ressources Azure à partir d’un compte de stockage Azure. Pour placer les fichiers dans le premier espace de stockage créé, procédez comme suit :

1. Ouvrez Cloud Explorer, puis accédez au conteneur de modèles dans votre compte de stockage créé précédemment.

2. Dans la fenêtre du conteneur de modèles, cliquez sur l’icône Charger l’objet blob dans l’angle supérieur droit, accédez au fichier VirtualMachineTemplate.json que vous avez créé, puis cliquez sur **Ouvrir**.

3. Cliquez de nouveau sur l’icône Télécharger un objet Blob, accédez au fichier Parameters.json que vous avez créé, puis cliquez sur **Ouvrir**.

## Étape 4 : Installer les bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous devez installer la bibliothèque Azure Resource Management et la bibliothèque d’authentification Azure Active Directory. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.

2. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.

4. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.Resources* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management, puis suivez les instructions pour installer le package.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

##Étape 5 : Créer les informations d’identification utilisées pour authentifier les requêtes

L’application Azure Active Directory est créée et la bibliothèque d’authentification est installée. Il vous reste à formater les informations d’application en informations d’identification qui serviront à authentifier les requêtes envoyées à Azure Resource Manager. Procédez comme suit :

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    Remplacez {application-id} par l’identificateur d’application que vous avez enregistré précédemment, {password} par le mot de passe que vous avez choisi pour l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureRmSubscription.

3. Ajoutez le code suivant à la méthode Main dans le fichier Program.cs pour créer les informations d’identification :

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Enregistrez le fichier Program.cs.

## Étape 6 : Ajouter le code pour déployer le modèle

Dans cette étape, utilisez les classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) et [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) pour créer le groupe de ressources dans lequel seront déployées les ressources.

1. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms que vous souhaitez utiliser pour les ressources, l’emplacement de celles-ci (par exemple, « États-Unis du Centre »), les informations de compte Administrateur et l’identificateur de votre abonnement :

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    Remplacez les valeurs de toutes les variables par le nom et l’identificateur que vous souhaitez utiliser. Pour trouver l’identificateur d’abonnement, exécutez Get-AzureRmSubscription. La valeur de la variable storageName est le nom du compte de stockage hébergeant le modèle.
    
2. Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :

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
        }

2. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. Ajoutez la méthode suivante à la classe Program pour déployer les ressources dans le groupe de ressources en utilisant le modèle que vous avez défini :

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
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
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

4. Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName,
          subscriptionId);
        Console.ReadLine();

##Étape 7 : Ajouter le code pour supprimer les ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Vous n’avez pas besoin de supprimer séparément les ressources d’un groupe de ressources. Supprimez le groupe de ressources pour supprimer automatiquement toutes ses ressources.

1.	Ajoutez la méthode suivante à la classe Program pour supprimer le groupe de ressources :

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##Étape 8 : Exécuter l’application console

1.	Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Microsoft Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2.	Appuyez sur **Entrée** lorsque l’état Accepté s’affiche.

	L’exécution complète de cette application console devrait durer 5 minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.

3. Parcourez les journaux d’audit dans le portail Azure pour connaître l’état des ressources :

	![Parcourir les journaux d’audit dans le portail Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## Étapes suivantes

- Si vous rencontrez des problèmes de déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Pour apprendre à gérer la machine virtuelle que vous venez de créer, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0622_2016-->