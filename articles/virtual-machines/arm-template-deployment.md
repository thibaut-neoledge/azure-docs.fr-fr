<properties 
	pageTitle="Déploiement de ressources Azure à l’aide d’un modèle" 
	description="Découvrez comment utiliser certains des clients disponibles dans la bibliothèque Azure Resource Management pour déployer une machine virtuelle, un réseau virtuel et un compte de stockage." 
	services="virtual-machines,virtual-networks,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="davidmu"/>

# Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle

Grâce aux modèles et aux groupes de ressources, vous pouvez gérer en même temps toutes les ressources qui prennent en charge votre application. Ce didacticiel vous montre comment utiliser certains des clients disponibles dans la bibliothèque Azure Resource Management et comment créer un modèle pour déployer une machine virtuelle, un réseau virtuel et un compte de stockage.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un compte Azure Storage](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/fr-fr/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/fr-fr/download/details.aspx?id=40855)
- [Azure PowerShell](../powershell-install-configure.md)

Ces étapes prennent environ 30 minutes.

## Étape 1 : ajouter une application à Azure AD et définir les autorisations

Pour pouvoir utiliser Microsoft Azure AD afin d’authentifier les demandes pour Microsoft Azure Resource Manager, une application doit être ajoutée dans le répertoire par défaut. Pour ajouter une application, procédez comme suit :

1. Ouvrez une invite de commandes Azure PowerShell puis exécutez cette commande :

        Switch-AzureMode –Name AzureResourceManager

2. Définissez le compte Azure que vous souhaitez utiliser pour ce didacticiel. Exécutez cette commande et entrez les informations d’identification pour votre abonnement lorsqu’elles vous seront demandées :

	    Add-AzureAccount

3. Remplacez {password} dans la commande suivante par celui que vous souhaitez utiliser, puis exécutez-le pour créer l’application :

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Enregistrez la valeur de ApplicationId donnée dans la réponse de l’étape précédente. Vous en aurez besoin plus loin dans ce didacticiel :

	![Création d'une application Active Directory](./media/arm-template-deployment/azureapplicationid.png)

	>[AZURE.NOTE]Vous trouverez également l’identificateur d’application dans le champ d’ID client de l’application dans le portail de gestion.

5. Remplacez {application-id} par l’identificateur que vous venez d’enregistrer, puis créez le principal du service pour l’application :

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. Définissez les autorisations relatives à l’utilisation de l’application :

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Étape 2 : créer le projet Visual Studio, le fichier de modèle et le fichier de paramètres

###Créer le fichier de modèle

Avec un modèle Azure Resource Manager, vous pouvez déployer et gérer des ressources Azure en même temps à l’aide d’une description JSON des ressources et des paramètres de déploiement associés. Dans Visual Studio, procédez comme suit :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.

2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** > **Nouvel élément**.

4.	Dans la fenêtre Ajouter un nouvel élément, sélectionnez **Fichier texte**, entrez *VirtualMachineTemplate.json* pour le nom, puis cliquez sur **Ajouter**.

5.	Ouvrez le fichier VirtualMachineTemplate.json et ajoutez les parenthèses ouvrante et fermante, l’élément de schéma requis et l’élément contentVersion requis :

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. Les [paramètres](https://msdn.microsoft.com/library/azure/dn835138.aspx#parameters) ne sont pas toujours requis, mais ils facilitent la gestion de modèle. Ils décrivent le type de la valeur, la valeur par défaut le cas échéant, et éventuellement les valeurs autorisées du paramètre. Pour ce didacticiel, les paramètres utilisés pour créer une machine virtuelle, un compte de stockage et un réseau virtuel sont ajoutés au modèle.

    Ajoutez l’élément parameters et ses éléments enfants après l’élément contentVersion :

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	Les [variables](https://msdn.microsoft.com/library/azure/dn835138.aspx#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres.

    Ajoutez l’élément variables après la section parameters :

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	Les [ressources](https://msdn.microsoft.com/library/azure/dn835138.aspx#resources) telles que la machine virtuelle, le réseau virtuel et le compte de stockage sont ensuite définis dans le modèle.

    Ajoutez la section resources après la section variables :

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",       
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": { 
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": { 
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	Enregistrez le fichier de modèle que vous avez créé.

###Créer le fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs et envoyez-le au Gestionnaire de ressources avec le modèle. Dans Visual Studio, procédez comme suit :

1.	Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** et **Nouvel élément**.

2.	Dans la fenêtre Ajouter un nouvel élément, sélectionnez **Fichier texte**, entrez *Parameters.json* pour le nom, puis cliquez sur **Ajouter**.

3.	Ouvrez le fichier parameters.json et ajoutez le contenu JSON suivant :

        {
          "contentVersion": "1.0.0.0",
          "parameters": { 
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" }, 
            "nicName": { "value": "mytestnic1" } 
          }
        }

    >[AZURE.NOTE]Les noms d’images de disque dur virtuel changent régulièrement dans la galerie d’images. Vous devez donc obtenir le nom d’une image actuelle pour déployer la machine virtuelle. Pour ce faire, consultez la page [Gérer les images Windows à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx), puis remplacez {source-image-name} par le nom du fichier de disque dur virtuel que vous souhaitez utiliser. Par exemple, « a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd ». Remplacez {subscription-id} par l’identificateur de votre abonnement.


4.	Enregistrez le fichier de paramètres que vous avez créé.

Le fichier de modèle et le fichier de paramètres sont accessibles via le Gestionnaire de ressources Azure à partir d’un compte de stockage Azure. Pour placer les fichiers dans un stockage, procédez comme suit :

1.	Ouvrez l’Explorateur de serveurs et accédez au conteneur de votre compte de stockage dans lequel vous souhaitez placer le fichier. Pour ce didacticiel, le conteneur dans lequel se trouve le modèle est nommé « templates ».

2.	Dans le coin supérieur droit du volet du conteneur de modèles, cliquez sur l’icône Télécharger un objet Blob, accédez au fichier VirtualMachineTemplate.json que vous avez créé, puis cliquez sur **Ouvrir**.

3. Cliquez de nouveau sur l’icône Télécharger un objet Blob, accédez au fichier Parameters.json que vous avez créé, puis cliquez sur **Ouvrir**.

##Étape 3 : installer les bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous devez installer la bibliothèque Azure Resource Management et la bibliothèque d’authentification Azure Active Directory. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1.	Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.

2.	Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.

3.	En haut de la page, sélectionnez **Inclure la version préliminaire**. Entrez *Azure Resource Manager* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management, puis suivez les instructions pour installer le package.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour créer votre application.

##Étape 4 : créer les informations d’identification utilisées pour authentifier les requêtes

Maintenant que l’application Azure Active Directory est créée et que la bibliothèque d’authentification a été installée, il vous reste à mettre en forme les informations d’application en informations d’identification qui seront utilisées pour authentifier les requêtes formulées auprès d’Azure Resource Manager. Effectuez les actions suivantes :

1.	Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions using suivantes au début du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

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

4.	Enregistrez le fichier Program.cs.


##Étape 5 : ajouter le code pour déployer le modèle

Les ressources sont toujours déployées à partir d’un modèle dans un groupe de ressources. Utilisez les classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) et [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) pour créer le groupe de ressources dans lequel seront déployées les ressources.

1.	Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Ajoutez la méthode suivante à la classe Program pour déployer les ressources dans le groupe de ressources en utilisant le modèle que vous avez défini :

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	Remplacez {storage-account-name} par le nom du compte dans lequel vous avez précédemment placé vos fichiers.

4.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##Étape 6 : ajouter du code pour supprimer les ressources

Étant donné que les ressources utilisées dans Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Vous n’avez pas besoin de supprimer séparément les ressources d’un groupe de ressources. Vous pouvez supprimer le groupe de ressources et toutes ses ressources seront automatiquement supprimées.

1.	Ajoutez la méthode suivante à la classe Program pour effacer un groupe de ressource :

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Ajoutez le code suivant à la méthode Main pour appeler la méthode que vous venez d’ajouter :

		DeleteResourceGroup(credential);
		Console.ReadLine();

##Étape 7 : exécuter l’application console

1.	Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2.	Pour créer chaque ressource, appuyez sur **Entrée** après le renvoi de chaque code d’état. Une fois la machine virtuelle créée, effectuez l’étape suivante, puis appuyez sur Entrée pour supprimer toutes les ressources.

	L’exécution complète de cette application console devrait durer 5 minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression de ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Microsoft Azure en version préliminaire ont bien été créées avant de les supprimer.

3. Parcourez les journaux d’audit dans le portail Microsoft Azure en version préliminaire pour connaître l’état des ressources :

	![Création d'une application Active Directory](./media/arm-template-deployment/crpportal.png)

<!---HONumber=58_postMigration-->