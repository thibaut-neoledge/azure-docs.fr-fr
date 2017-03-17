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
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: 9b8d800e39a5a659b5c9ebce7066b56c5b543db6
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Déployer une machine virtuelle Azure à l’aide de C# et d’un modèle Resource Manager
Cet article explique la procédure de déploiement d’un modèle Azure Resource Manager à l’aide de C#. Le [modèle](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) déploie une machine virtuelle unique exécutant Windows Server dans un nouveau réseau virtuel avec un seul sous-réseau.

Pour obtenir une description détaillée de la ressource de machine virtuelle, consultez [Virtual machines in an Azure Resource Manager template (Machines virtuelles dans un modèle Azure Resource Manager)](virtual-machines-windows-template-description.md). Pour plus d’informations sur toutes les ressources d’un modèle, consultez [Guide de création d’un modèle Resource Manager](../resource-manager-template-walkthrough.md).

Ces étapes prennent environ 10 minutes.

## <a name="step-1-create-a-visual-studio-project"></a>Étape 1 : création d’un projet Visual Studio

Lors de cette étape, assurez-vous que Visual Studio est installé et que vous créez une application console utilisée pour déployer le modèle.

1. Si vous ne l’avez pas déjà fait, installez [Visual Studio](https://www.visualstudio.com/).
2. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.
3. Dans **Modèles** > **Visual C#**, sélectionnez **Application console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

## <a name="step-2-install-libraries"></a>Étape 2 : installation des bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ces étapes. Vous avez besoin de la bibliothèque Azure Resource Manager et de la bibliothèque d’authentification pour créer les ressources. Pour obtenir ces bibliothèques dans Visual Studio, suivez ces étapes :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, et cliquez sur **Gérer les packages NuGet**, puis sur **Parcourir**.
2. Entrez *Microsoft.IdentityModel.Clients.ActiveDirectory* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.
3. En haut de la page, sélectionnez **Inclure la version préliminaire**. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, cliquez sur **Installer**, puis suivez les instructions d’installation du package.

Vous êtes maintenant prêt à utiliser les bibliothèques permettant de créer votre application.

## <a name="step-3-create-credentials-used-to-authenticate-requests"></a>Étape 3 : création des informations d’identification utilisées pour authentifier les requêtes

Avant de commencer cette étape, assurez-vous que vous avez accès à un [principal de service Active Directory](../azure-resource-manager/resource-group-authenticate-service-principal.md). Dans le principal de service, vous obtenez le jeton d'authentification des demandes pour Azure Resource Manager.

1. Ouvrez le fichier Program.cs du projet que vous avez créé, puis ajoutez les instructions suivantes au début du fichier :

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
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
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    Remplacez les valeurs suivantes :
    
    - *{client-id}* avec l'identificateur de l'application Azure Active Directory. Vous pouvez trouver cet identifiant dans le panneau Propriétés de votre application AD. Pour trouver votre application Active Directory dans le portail Azure, cliquez sur **Azure Active Directory** dans le menu de ressources, puis cliquez sur **Inscriptions d’applications**.
    - *{client-secret} * avec la clé d’accès de l’application Active Directory. Vous pouvez trouver cet identifiant dans le panneau Propriétés de votre application AD.
    - *{tenant-id} * avec l’identificateur de client de votre abonnement. Vous trouverez l’identificateur de client dans le panneau Propriétés d’Azure Active Directory dans le portail Azure. Il porte le nom *ID de répertoire*.

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Enregistrez le fichier Program.cs.

## <a name="step-4-create-a-resource-group"></a>Étape 4 : création d’un groupe de ressources

Même s’il est possible de créer un groupe de ressources à partir d’un modèle, le modèle que vous utilisez à partir de la galerie n’en crée pas. Lors de cette étape, vous ajoutez le code pour créer un groupe de ressources.

1. Pour spécifier les valeurs pour l’application, ajoutez les variables à la méthode Main de la classe Program :

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName;
    var location = "location";
    ```

    Remplacez les valeurs suivantes :
    
    - *myResourceGroup* avec le nom du groupe de ressources en cours de création.
    - *subscriptionId* avec l’identificateur de votre abonnement. Vous trouverez l’identificateur d’abonnement dans le panneau Abonnements du portail Azure.
    - *deploymentName* avec le nom du déploiement.
    - *location* est la [région Azure](https://azure.microsoft.com/regions/) où vous souhaitez créer les ressources.

2. Ajoutez la méthode suivante à la classe Program pour créer un groupe de ressources :

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-5-create-a-parameters-file"></a>Étape 5 : création d’un fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs. Le fichier de paramètres est utilisé lorsque vous déployez le modèle. Le modèle que vous utilisez dans la galerie attend des valeurs pour les paramètres *adminUserName*, *adminPassword*, et *dnsLabelPrefix*.

Dans Visual Studio, suivez ces étapes :

1. Cliquez avec le bouton droit sur Explorateur de solutions, puis cliquez sur **Ajouter** > **Nouvel élément**.
2. Cliquez sur **Web**, sélectionnez **Fichier JSON**, entrez le nom *Parameters.json*, puis cliquez sur **Ajouter**.
3. Ouvrez le fichier parameters.json et ajoutez le contenu JSON suivant :

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    Remplacez les valeurs des paramètres par des valeurs qui fonctionnent dans votre environnement.

4. Enregistrez le fichier Parameters.json.

## <a name="step-6-deploy-a-template"></a>Étape 6 : déploiement d’un modèle

Dans cet exemple, vous déployez un modèle à partir de la galerie de modèles Azure et lui fournissez les valeurs de paramètre à partir du fichier local que vous avez créé. 

1. Pour déployer le modèle, ajoutez cette méthode à la classe Program :

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    Vous pouvez également déployer un modèle à partir d’un dossier local à l’aide de la propriété Template au lieu de la propriété TemplateLink.

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez le code suivant à la méthode Main :

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-7-delete-the-resources"></a>Étape 7 : suppression des ressources

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

## <a name="step-8-run-the-console-application"></a>Étape 8 : Exécuter l’application console

L’exécution complète de cette application console devrait durer cinq minutes environ. 

1. Pour exécuter l’application de console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2. Appuyez sur **Entrée** lorsque l’état *Réussi* s’affiche. 

    Vous devriez également voir **1 Réussite** sous Déploiements dans le panneau Vue d’ensemble de votre groupe de ressources dans le portail Azure.

3. Avant d’appuyer sur **Entrée** pour démarrer la suppression des ressources, prenez quelques minutes pour vérifier que les ressources dans le portail Azure ont bien été créées. Cliquez sur l’état du déploiement pour afficher des informations sur le déploiement.

## <a name="next-steps"></a>Étapes suivantes
* Si vous rencontrez des problèmes lors du déploiement, nous vous conseillons de consulter la section [Résolution des erreurs courantes dans un déploiement Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
* Découvrez comment déployer une machine virtuelle et ses ressources de soutien en consultant [Déployer une machine virtuelle Azure à l’aide de C#](virtual-machines-windows-csharp.md).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Azure Resource Manager et de C#](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

