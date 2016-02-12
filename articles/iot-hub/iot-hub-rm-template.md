<properties
	pageTitle="Créer un hub IoT à l’aide d’un modèle Resource Manager | Microsoft Azure"
	description="Suivez ce didacticiel afin d’utiliser des modèles Resource Manager pour créer un hub IoT."
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
     ms.date="11/23/2015"
     ms.author="dobett"/>

# Didacticiel : Créer un hub IoT à l'aide d'un programme C#

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introduction

Vous pouvez utiliser Azure Resource Manager pour créer et gérer des hubs Azure IoT de façon programmée. Ce didacticiel vous montre comment utiliser un modèle de gestionnaire de ressources pour créer un hub IoT à partir d'un programme C#.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement de Resource Manager.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Microsoft Visual Studio 2015.
- Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou une version ultérieure.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Préparer votre projet Visual Studio

1. Dans Visual Studio, créez un projet Windows Visual C# à l'aide du modèle de projet d'**application de console**. Nommez ce projet **CreateIoTHub**.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis cliquez sur **GÉRER LES PACKAGES NUGET**.

3. Dans le gestionnaire de packages NuGet, recherchez **Microsoft.Azure.Management.Resources**. Sélectionnez la version **2.18.11-preview**. Cliquez sur **Installer**. Sous **Réviser les modifications**, cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter les licences.

4. Dans le gestionnaire de packages NuGet, recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory**. Sélectionnez la version **2.19.208020213**. Cliquez sur **INSTALLER**, dans **RÉVISER LES MODIFICATIONS**, cliquez sur **OK**, puis cliquez sur **J'ACCEPTE** pour accepter la licence.

5. Dans le gestionnaire de packages NuGet, recherchez **Microsoft.Azure.Common**. Sélectionnez la version **2.1.0**. Cliquez sur **Installer**. Sous **Réviser les modifications**, cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter les licences.

6. Dans Program.cs, remplacez les instructions **using** existantes par les instructions suivantes :

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. Dans Program.cs, ajoutez les variables statiques suivantes en remplaçant les valeurs des espaces réservés. Vous avez noté les éléments **ApplicationId**, **SubscriptionId**, **TenantId** et **Password** précédemment dans ce didacticiel. **Nom du groupe de ressources** est le nom du groupe de ressources que vous utiliserez pour créer le hub IoT ; il peut s’agit d’un groupe de ressources existant ou nouveau. **Nom du hub IoT** est le nom du hub IoT que vous allez créer, par exemple **MonHubIoT**. **Deployment name** est le nom du déploiement, par exemple **Déploiement\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Envoyer un modèle pour créer un hub IoT

Utilisez un modèle JSON pour créer un hub IoT dans votre groupe de ressources. Vous pouvez également utiliser un modèle pour apporter des modifications à un hub IoT existant.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, cliquez sur **AJOUTER** puis sur **NOUVEL ÉLÉMENT**. Ajoutez un nouveau fichier JSON appelé **template.json** à votre projet.

2. Dans l'Explorateur de solutions, sélectionnez **template.json**, puis dans **PROPRIÉTÉS**, définissez l’option **COPIER DANS LE RÉPERTOIRE DE SORTIE** sur **TOUJOURS COPIER**.

3. Remplacez le contenu de **template.json** par la définition de ressource suivante afin d’ajouter un nouveau hub IoT standard à la région **Est des États-Unis** :

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
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
    ]
    }
    ```

4. Ajoutez la méthode suivante au fichier Program.cs :
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Ajoutez le code suivant à la méthode **CreateIoTHub** pour charger le fichier de modèle, ajoutez le nom du hub IoT, puis soumettez le modèle à Azure resource manager :

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. Ajoutez le code suivant à la méthode **CreateIoTHub**, qui attend que le déploiement soit terminé avec succès :

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## Terminer et exécuter l’application

Vous pouvez maintenant terminer l’application en appelant les méthodes **CreateIoTHub** et **ShowIoTHubKeys** avant sa génération et son exécution.

1. Ajoutez le code suivant à la fin de la méthode **Main** :

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Cliquez sur **BUILD** puis sur **BUILD SOLUTION**. Corrigez les éventuelles erreurs.

3. Cliquez sur **DÉBOGUER** puis **DÉMARRER LE DÉBOGAGE** pour exécuter l'application. Le déploiement peut prendre plusieurs minutes.

4. Vous pouvez vérifier que votre application a ajouté le nouveau IoT Hub en accédant au [portail][lnk-azure-portal] et en affichant votre liste de ressources, ou en utilisant l’applet de commande PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Cet exemple d’application ajoute un IoT Hub S1 Standard qui vous sera facturé. Lorsque vous avez terminé, vous pouvez supprimer le IoT Hub par le biais du [portail][lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Remove-AzureRmResource**.

## Étapes suivantes

- Explorez les capacités de l’[API REST du fournisseur de ressources IoT Hub][lnk-rest-api].
- Pour plus d’informations sur les capacités d’Azure Resource Manager, voir l’article [Présentation d’Azure Resource Manager][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/fr-FR/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0204_2016-->