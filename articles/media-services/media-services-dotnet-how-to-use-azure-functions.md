---
title: "Développement de fonctions Azure Functions avec Media Services"
description: "Cette rubrique montre comment développer des fonctions Azure Functions avec Media Services à l’aide du portail Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/27/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 95379ed04c47a1e62822ae44b9a1f13b234c6282
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="develop-azure-functions-with-media-services"></a>Développement de fonctions Azure Functions avec Media Services

Cette rubrique vous montre comment vous familiariser avec la création de fonctions Azure Functions qui utilisent Media Services. La fonction Azure Function définie dans cette rubrique surveille un conteneur de compte de stockage nommé **input** pour les nouveaux fichiers MP4. Une fois qu’un fichier est déplacé dans le conteneur de stockage, le déclencheur d’objet blob exécute la fonction.

Si vous souhaitez explorer et déployer des fonctions Azure existantes qui utilisent Azure Media Services, consultez [Fonctions Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Ce référentiel contient des exemples qui utilisent Media Services pour afficher les flux de travail liés à l’ingestion de contenu directement à partir du Stockage Blob, à l’encodage et à l’écriture de contenu dans le Stockage Blob. Il inclut également des exemples décrivant la manière de contrôler les notifications de travail via les WebHooks et les files d’attente Azure. Vous pouvez également développer vos fonctions en fonction des exemples dans le référentiel [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Pour déployer les fonctions, appuyez sur le bouton **Déployer dans Azure**.

## <a name="prerequisites"></a>Composants requis

- Pour créer votre première fonction, vous devez avoir un compte Azure actif. Si tel n’est pas le cas, des [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).
- Si vous souhaitez créer des fonctions Azure Functions qui effectuent des actions sur votre compte Azure Media Services (AMS) ou qui écoutent des événements envoyés par Media Services, vous devez créer un compte AMS, comme décrit [ici](media-services-portal-create-account.md).
- Comprendre [l’utilisation des fonctions Azure](../azure-functions/functions-overview.md). Consultez également :
    - [Liaisons HTTP et webhook Azure Functions](../azure-functions/functions-triggers-bindings.md)
    - [Guide pratique pour configurer les paramètres d’application Azure Functions](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
    
## <a name="considerations"></a>Considérations

-  Azure Functions s’exécutant sur le plan Consommation a une limite de délai d’expiration de 5 minutes.

## <a name="create-a-function-app"></a>Créer une application de fonction

1. Accédez au [Portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Suivez [cette procédure](../azure-functions/functions-create-function-app-portal.md) pour créer une application de fonction.

>[!NOTE]
> Un compte de stockage que vous spécifiez dans la variable d’environnement **StorageConnection** (voir l’étape suivante) doit être dans la même région que votre application.

## <a name="configure-function-app-settings"></a>Configuration des paramètres de l’application de fonction

Lorsque vous développez des fonctions Media Services, il est utile d’ajouter des variables d’environnement qui seront utilisées dans toutes vos fonctions. Pour configurer les paramètres d’application, cliquez sur le lien Configurer les paramètres de l’application. Pour en savoir plus, consultez [Guide pratique pour configurer des paramètres d’application de fonction](../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Par exemple :

![Paramètres](./media/media-services-azure-functions/media-services-azure-functions001.png)

La fonction, définie dans cet article, suppose que les variables d’environnement suivantes se trouvent dans vos paramètres d’application :

**AMSAADTenantDomain** : point de terminaison de locataire Azure AD. Pour plus d’informations sur la connexion à l’API AMS, consultez [cet](media-services-use-aad-auth-to-access-ams-api.md) article.

**AMSRESTAPIEndpoint** : URI qui représente le point de terminaison d’API REST. 

**AMSClientId** : ID client d’application Azure AD.

**AMSClientId** : clé secrète client d’application Azure AD.

**StorageConnection** : connexion au stockage du compte associé au compte Media Services. Cette valeur est utilisée dans le fichier **function.json** et le fichier **run.csx** (décrits ci-après).

## <a name="create-a-function"></a>Créer une fonction

Une fois votre application de fonction déployée, vous pouvez la retrouver parmi les fonctions Azure Functions **App Services**.

1. Sélectionnez votre application de fonction et cliquez sur **Nouvelle fonction**.
2. Choisissez le langage **C#** et le scénario **Traitement des données**.
3. Choisissez le modèle **BlobTrigger**. Cette fonction sera déclenchée à chaque fois qu’un objet blob est chargé dans le conteneur **input**. Le nom **input** est spécifié dans le **chemin d’accès**, à l’étape suivante.

    ![fichiers d'entrée](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Une fois que vous sélectionnez **BlobTrigger**, certaines commandes s’affichent sur la page.

    ![fichiers d'entrée](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Cliquez sur **Créer**. 


## <a name="files"></a>Fichiers

Votre fonction Azure est associée à des fichiers de code et à d’autres fichiers décrits dans cette section. Par défaut, une fonction est associée à des fichiers **function.json** et **run.csx** (C#). Vous devez ajouter un fichier **project.json**. Le reste de cette section présente les définitions de ces fichiers.

![fichiers d'entrée](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Le fichier function.json définit les liaisons de fonction et d’autres paramètres de configuration. Le runtime utilise ce fichier pour déterminer les événements à surveiller et comment passer des données et renvoyer des données à partir de l’exécution de la fonction. Pour plus d’informations, consultez [Liaisons HTTP et webhook d’Azure Functions](../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Définissez la propriété **disabled** sur **true** pour empêcher l’exécution de la fonction. 

Voici un exemple de fichier **function.json**.

```
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Le fichier project.json contient des dépendances. Voici un exemple de fichier **project.json** qui inclut les packages .NET Azure Media Services requis à partir de Nuget. Notez que les numéros de version changeront avec les dernières mises à jour pour les packages, donc vous devez vérifier les versions les plus récentes. 

```
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

Il s’agit du code C# de votre fonction.  La fonction définie ci-dessous surveille un conteneur de compte de stockage nommé **input** (c’est ce qui a été spécifié dans le chemin d’accès) pour les nouveaux fichiers MP4. Une fois qu’un fichier est déplacé dans le conteneur de stockage, le déclencheur d’objet blob exécute la fonction.
    
L’exemple défini dans cette section montre 

1. comment ingérer une ressource dans un compte Media Services (par copie d’un objet blob dans une ressource AMS) et 
2. comment soumettre un travail d’encodage qui utilise la valeur prédéfinie « Diffusion en continu adaptative » de Media Encoder Standard.

Dans le scénario réel, vous voulez probablement effectuer le suivi de la progression du travail, puis publier votre ressource encodée. Pour plus d’informations, consultez [Utiliser Azure WebHooks pour surveiller les notifications de travaux Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Pour plus d’exemples, consultez [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Une fois que vous avez fini de définir votre fonction, cliquez sur **Enregistrer et exécuter**.

```
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

##<a name="test-your-function"></a>Tester votre fonction

Pour tester votre fonction, vous devez charger un fichier MP4 dans le conteneur **input** du compte de stockage que vous avez spécifié dans la chaîne de connexion.  

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous êtes prêt à commencer le développement d’une application Media Services. 
 
Pour plus d’informations, ainsi que des exemples et des solutions de l’utilisation d’Azure Functions et de Logic Apps avec Azure Media Services pour créer des flux de travail de création de contenu personnalisé, consultez [l’exemple Media Services .NET Functions Integration sur GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).

Consultez également [Utiliser Azure WebHooks pour surveiller les notifications de travaux Media Services avec .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


