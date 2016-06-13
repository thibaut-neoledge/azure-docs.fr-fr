<properties
	pageTitle="Créer un IoT Hub à l’aide d’un modèle ARM et C# | Microsoft Azure"
	description="Suivez ce didacticiel afin d’utiliser des modèles Resource Manager pour créer un IoT Hub à l'aide d'un programme C#"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/31/2016"
     ms.author="dobett"/>

# Créer un IoT Hub à l'aide d'un programme C# avec un modèle ARM

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introduction

Vous pouvez utiliser Azure Resource Manager (ARM) pour créer et gérer des Azure IoT Hubs de façon programmée. Ce didacticiel vous montre comment utiliser un modèle de gestionnaire de ressources pour créer un hub IoT à partir d'un programme C#.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement de Resource Manager.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Microsoft Visual Studio 2015.
- Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].
- Un [compte de stockage Azure][lnk-storage-account] où vous pouvez stocker vos fichiers de modèle.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou une version ultérieure.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Préparer votre projet Visual Studio

1. Dans Visual Studio, créez un projet Windows Visual C# à l'aide du modèle de projet d'**application de console**. Nommez ce projet **CreateIoTHub**.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis cliquez sur **GÉRER LES PACKAGES NUGET**.

3. Dans le gestionnaire de packages NuGet, activez **Inclure la version préliminaire** et recherchez **Microsoft.Azure.Management.ResourceManager**. Cliquez sur **Installer**. Sous **Réviser les modifications**, cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter les licences.

4. Dans le gestionnaire de packages NuGet, recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory**. Cliquez sur **INSTALLER**, dans **RÉVISER LES MODIFICATIONS**, cliquez sur **OK**, puis cliquez sur **J'ACCEPTE** pour accepter la licence.

5. Dans le gestionnaire de packages NuGet, recherchez **Microsoft.Azure.Common**. Cliquez sur **Installer**. Sous **Réviser les modifications**, cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter les licences.

6. Dans Program.cs, remplacez les instructions **using** existantes par les instructions suivantes :

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. Dans Program.cs, ajoutez les variables statiques suivantes en remplaçant les valeurs des espaces réservés. Vous avez noté les éléments **ApplicationId**, **SubscriptionId**, **TenantId** et **Password** précédemment dans ce didacticiel. Le **nom de votre compte de stockage** est le nom du compte de stockage Azure où vous allez stocker vos fichiers de modèle. **Nom du groupe de ressources** est le nom du groupe de ressources que vous utiliserez pour créer le hub IoT ; il peut s’agit d’un groupe de ressources existant ou nouveau. **Deployment name** est le nom du déploiement, par exemple **Déploiement\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Envoyer un modèle pour créer un hub IoT

Utilisez un fichier de paramètres et un modèle JSON pour créer un IoT Hub dans votre groupe de ressources. Vous pouvez également utiliser un modèle pour apporter des modifications à un hub IoT existant.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, cliquez sur **Ajouter**, puis sur **Nouvel élément**. Ajoutez un nouveau fichier JSON appelé **template.json** à votre projet.

2. Remplacez le contenu de **template.json** par la définition de ressource suivante afin d’ajouter un nouveau hub IoT standard à la région **Est des États-Unis** :

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, cliquez sur **Ajouter**, puis sur **Nouvel élément**. Ajoutez un nouveau fichier JSON appelé **parameters.json** à votre projet.

4. Remplacez le contenu de **parameters.json** avec les informations des paramètres ci-dessous qui définissent le nom du nouvel IoT Hub, par exemple **{vos initiales}mynewiothub** (notez que ce nom doit être globalement unique et inclure votre nom ou vos initiales) :

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. Dans l’**Explorateur de serveurs**, connectez-vous à votre abonnement Azure et, dans votre compte de stockage, créez un conteneur appelé **modèles**. Dans le panneau **Propriétés**, définissez les autorisations **Accès public en lecture** pour le conteneur **modèles** sur **Blob**.

6. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur le conteneur **modèles**, puis cliquez sur Afficher le conteneur d'objets blob. Cliquez sur le bouton Télécharger l’objet Blob, sélectionnez les deux fichiers, **parameters.json** et **templates.json**, puis cliquez sur Ouvrir pour télécharger les fichiers JSON dans le conteneur **modèles**. Les URL des objets blob contenant les données JSON sont :

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. Ajoutez la méthode suivante au fichier Program.cs :
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Ajoutez le code suivant à la méthode **CreateIoTHub** pour envoyer le fichier de modèle et le fichier de paramètres à Azure Resource Manager :

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Ajoutez le code suivant à la méthode **CreateIoTHub** qui affiche l’état et les clés du nouvel IoT Hub :

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## Terminer et exécuter l’application

Vous pouvez maintenant terminer l’application en appelant la méthode **CreateIoTHub** avant sa génération et son exécution.

1. Ajoutez le code suivant à la fin de la méthode **Main** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Cliquez sur **BUILD** puis sur **BUILD SOLUTION**. Corrigez les éventuelles erreurs.

3. Cliquez sur **DÉBOGUER** puis **DÉMARRER LE DÉBOGAGE** pour exécuter l'application. Le déploiement peut prendre plusieurs minutes.

4. Vous pouvez vérifier que votre application a ajouté le nouveau IoT Hub en accédant au [portail][lnk-azure-portal] et en affichant votre liste de ressources, ou en utilisant l’applet de commande PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Cet exemple d’application ajoute un IoT Hub S1 Standard qui vous sera facturé. Lorsque vous avez terminé, vous pouvez supprimer le IoT Hub par le biais du [portail][lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Remove-AzureRmResource**.

## Étapes suivantes

Maintenant que vous avez déployé un IoT Hub à l'aide d’un modèle ARM avec un programme C#, vous pouvez aller encore plus loin :

- Découvrez les capacités de l’[API REST du fournisseur de ressources IoT Hub][lnk-rest-api].
- Pour plus d’informations sur les capacités d’Azure Resource Manager, voir l’article [Présentation d’Azure Resource Manager][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0601_2016-->