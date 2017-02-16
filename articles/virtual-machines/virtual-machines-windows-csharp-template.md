---
title: "Déployer une machine virtuelle à l’aide de C# et d’un modèle Resource Manager | Microsoft Docs"
description: "Apprenez à utiliser C# et un modèle Resource Manager pour déployer une machine virtuelle Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: 8424fb5d107935833e9652ef86e03933ea14c26e


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Déployer une machine virtuelle Azure à l’aide de C# et d’un modèle Resource Manager
Grâce aux modèles et aux groupes de ressources, vous pouvez gérer ensemble toutes les ressources qui prennent en charge votre application. Cet article vous montre comment utiliser Visual Studio et C# pour définir l’authentification, créer un modèle et ensuite déployer les ressources Azure à l’aide du modèle que vous avez créé.

Vous devez d’abord vous assurer que vous avez effectué ces étapes de configuration :

* Installez [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Vérifier l’installation de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
* Obtenir un [jeton d’authentification](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* Créer un groupe de ressources à l’aide [d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md), de [l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) ou du [portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)

Ces étapes prennent environ 30 minutes.

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Étape 1 : Créer le projet Visual Studio, le fichier de modèle et le fichier de paramètres
### <a name="create-the-template-file"></a>Créer le fichier de modèle
Un modèle Azure Resource Manager vous permet de déployer et de gérer les ressources Azure ensemble. Le modèle est une description JSON des ressources et des paramètres de déploiement associés.

Dans Visual Studio, suivez ces étapes :

1. Cliquez sur **Fichier** > **Nouveau** > **Projet**.
2. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.
3. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** > **Nouvel élément**.
4. Cliquez sur Web, sélectionnez Fichier JSON, entrez le nom *VirtualMachineTemplate.json* , puis cliquez sur **Ajouter**.
5. Entre les parenthèses ouvrantes et fermantes du fichier VirtualMachineTemplate.json, ajoutez l’élément de schéma requis et l’élément contentVersion requis :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. Les [paramètres](../azure-resource-manager/resource-group-authoring-templates.md#parameters) ne sont pas toujours nécessaires, mais ils offrent un moyen d’entrer des valeurs lors du déploiement du modèle. Ajoutez l’élément parameters et ses éléments enfants après l’élément contentVersion :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres. Ajoutez l’élément variables après la section parameters :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [ressources](../azure-resource-manager/resource-group-authoring-templates.md#resources) telles que la machine virtuelle, le réseau virtuel et le compte de stockage sont ensuite définis dans le modèle. Ajoutez la section resources après la section variables :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. Enregistrez le fichier de modèle que vous avez créé.

### <a name="create-the-parameters-file"></a>Créer le fichier de paramètres
Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs utilisées lors du déploiement du modèle. Dans Visual Studio, suivez ces étapes :

1. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** > **Nouvel élément**.
2. Cliquez sur Web, sélectionnez Fichier JSON, entrez le nom *Parameters.json* , puis cliquez sur **Ajouter**.
3. Ouvrez le fichier parameters.json et ajoutez le contenu JSON suivant :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

   > [!NOTE]
   > Cet article crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   >
   >
4. Enregistrez le fichier de paramètres que vous avez créé.

## <a name="step-2-install-the-libraries"></a>Étape 2 : Installer les bibliothèques
Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous avez besoin de la bibliothèque Azure Resource Management et de la bibliothèque d’authentification Azure Active Directory pour créer les ressources. Pour obtenir ces bibliothèques dans Visual Studio, suivez ces étapes :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, et cliquez sur **Gérer les packages NuGet**, puis sur Parcourir.
2. Entrez *Active Directory* dans la zone de recherche, cliquez sur **Installer** pour le package de la bibliothèque d’authentification Active Directory, puis suivez les instructions pour installer le package.
3. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, cliquez sur **Installer** pour les bibliothèques Microsoft Azure Resource Management, puis suivez les instructions pour installer le package.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Étape 3 : Créer les informations d’identification utilisées pour authentifier les requêtes
L’application Azure Active Directory est créée et la bibliothèque d’authentification est installée. Maintenant formatez les informations d’application en informations d’identification utilisées pour authentifier les demandes à Azure Resource Manager.

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. Ajoutez la méthode suivante à la classe Program pour obtenir le jeton nécessaire à la création des informations d’identification :

   ```
   private static async Task<AuthenticationResult> GetAccessTokenAsync()
   {
     var cc = new ClientCredential("{client-id}", "{client-secret}");
     var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
     var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
     if (token == null)
     {
       throw new InvalidOperationException("Could not get the token.");
     }
     return token;
   }
   ```

   Remplacez {client-id} par l’identificateur d’application Azure Active Directory, {client-secret} par la clé d’accès de l’application AD et {tenant-id} par l’identificateur de client de votre abonnement. Pour trouver l’ID de client, exécutez Get-AzureRmSubscription. Vous pouvez trouver la clé d’accès à l’aide du portail Azure.
3. Pour créer les informations d’identification, ajoutez le code suivant à la méthode Main dans le fichier Program.cs :

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Enregistrez le fichier Program.cs.

## <a name="step-4-deploy-the-template"></a>Étape 4 : déployer le modèle
Dans cette étape, utilisez le groupe de ressources que vous avez créé, sinon vous pouvez également créer un groupe de ressources à l’aide des classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) et [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Ajoutez des variables à la méthode Main de la classe Program pour spécifier les noms des ressources que vous avez créées, le nom du déploiement et votre identificateur d’abonnement :

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Remplacez la valeur de groupName par le nom de votre groupe de ressources. Remplacez la valeur de deploymentName par le nom que vous souhaitez utiliser pour le déploiement. Pour trouver l’identificateur d’abonnement, exécutez Get-AzureRmSubscription.
2. Ajoutez la méthode suivante à la classe Program pour déployer les ressources dans le groupe de ressources en utilisant le modèle que vous avez défini :

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Si vous souhaitez déployer le modèle à partir d’un compte de stockage, vous pouvez remplacer la propriété Template par la propriété TemplateLink.
3. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Étape 5 : supprimer les ressources
Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Vous n’avez pas besoin de supprimer séparément les ressources d’un groupe de ressources. Supprimez le groupe de ressources pour supprimer automatiquement toutes ses ressources.

1. Pour supprimer un groupe de ressources, ajoutez la méthode suivante à la classe Program :

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

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-6-run-the-console-application"></a>Étape 6 : Exécuter l’application console
1. Pour exécuter l’application de console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.
2. Appuyez sur **Entrée** lorsque l’état Accepté s’affiche.

   L’exécution complète de cette application console devrait durer cinq minutes environ. Avant d’appuyer sur Entrée pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées avant de les supprimer.
3. Pour connaître l’état des ressources, parcourez les journaux d’audit dans le portail Azure :

    ![Parcourir les journaux d’audit dans le portail Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Étapes suivantes
* Si vous rencontrez des problèmes lors du déploiement, nous vous conseillons de consulter la section [Résolution des erreurs courantes dans un déploiement Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Jan17_HO2-->


