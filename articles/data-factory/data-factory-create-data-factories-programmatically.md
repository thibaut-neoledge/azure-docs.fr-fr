---
title: "Créer des pipelines de données à l’aide du Kit de développement logiciel (SDK) .NET Azure | Microsoft Docs"
description: "Découvrez comment créer, analyser et gérer par programmation des fabriques de données Azure à l&quot;aide du Kit de développement logiciel (SDK) Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 6a76c399e626ea85581d5f8fb863da878bdbf50b
ms.lasthandoff: 03/09/2017


---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Créer, surveiller et gérer des fabriques de données Azure à l’aide du Kit de développement logiciel (SDK) Azure Data Factory .NET
## <a name="overview"></a>Vue d'ensemble
Vous pouvez créer, surveiller et gérer des fabriques de données Azure par programmation à l'aide du Kit SDK Data Factory .NET. Cet article contient une procédure pas à pas que vous pouvez suivre pour créer un exemple d'application console .NET qui crée et surveille une fabrique de données. Pour plus d'informations sur le Kit SDK Data Factory .NET, consultez [Informations de référence sur la bibliothèque de classes Data Factory](https://msdn.microsoft.com/library/mt415893.aspx) .

## <a name="prerequisites"></a>Configuration requise
* Visual Studio 2012, 2013 ou 2015
* Téléchargez et installez le [Kit SDK Azure .NET](http://azure.microsoft.com/downloads/).
* Ajoutez une application client native à Azure Active Directory. Pour connaître les étapes permettant d’ajouter l’application, consultez [Intégration d’applications dans Azure Active Directory](../active-directory/active-directory-integrating-applications.md) . Notez les valeurs **CLIENT ID** et **REDIRECT URI** dans la page **CONFIGURE**.
* Obtenez votre **ID d’abonnement** et votre **ID de locataire** Azure. Consultez [Obtenir les ID d’abonnement et client Azure](#get-azure-subscription-and-tenant-ids) pour obtenir des instructions.
* Téléchargez et installez les packages NuGet pour Azure Data Factory. Les instructions sont fournies dans la procédure pas à pas.

## <a name="walkthrough"></a>Procédure pas à pas
1. À l'aide de Visual Studio 2012 ou 2013, créez une application console Visual C# .NET.
   1. Lancez **Visual Studio 2012/2013/2015**.
   2. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.
   3. Développez **Modèles**, puis sélectionnez **Visual C#**. Dans cette procédure pas à pas, vous utilisez C#, mais vous pouvez utiliser un autre langage .NET.
   4. Sélectionnez **Application console** dans la liste des types de projet située sur la droite.
   5. Entrez **DataFactoryAPITestApp** dans le champ **Nom**.
   6. Sélectionnez **C:\ADFGetStarted** comme **Emplacement**.
   7. Cliquez sur **OK** pour créer le projet.
2. Cliquez sur **Outils**, pointez le curseur de la souris sur **Gestionnaire de package NuGet**, puis cliquez sur **Console du gestionnaire de package**.
3. Dans la fenêtre **Console du Gestionnaire de package**, exécutez les commandes suivantes une par une.

    ```
    Install-Package Microsoft.Azure.Management.DataFactories
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    ```
4. Ajoutez la section **appSettings** suivante au fichier **App.config**. Ces valeurs de configuration sont utilisées par la méthode **GetAuthorizationHeader** .

   > [!IMPORTANT]
   > Remplacez les valeurs de **AdfClientId**, **RedirectUri**, **SubscriptionId** et **ActiveDirectoryTenantId** par vos propres valeurs.

    ```XML
    <appSettings>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    
        <!-- Replace the following values with your own -->
        <add key="AdfClientId" value="Your AAD application ID" />
        <add key="RedirectUri" value="Your AAD application's redirect URI" />
        <add key="SubscriptionId" value="your subscription ID" />
        <add key="ActiveDirectoryTenantId" value="your tenant ID" />
    </appSettings>
    ```
5. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet.

    ```csharp
    using System.Threading;
    using System.Configuration;
    using System.Collections.ObjectModel;

    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure;
    ```
6. Ajoutez à la méthode **Main** le code suivant, qui crée une instance de la classe **DataPipelineManagementClient**. Cet objet vous permet de créer une fabrique de données, un service lié, des jeux de données d’entrée et de sortie, ainsi qu’un pipeline. Il vous permet également d’analyser les tranches d’un jeu de données lors de l’exécution.

    ```csharp
    // create data factory management client
    string resourceGroupName = "resourcegroupname";
    string dataFactoryName = "APITutorialFactorySP";
    
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());
    
    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
    
    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!NOTE]
   > Remplacez **resourcegroupname** par le nom de votre groupe de ressources Azure. Vous pouvez créer un groupe de ressources à l’aide du cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) .
7. Ajoutez à la méthode **Main** le code suivant, qui crée une **fabrique de données**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties() { }
            }
        }
    );
    ```
8. Ajoutez à la méthode **Main** le code suivant, qui crée un **service lié**.

   > [!NOTE]
   > Utilisez le **nom de compte**et la **clé de compte** de votre compte de stockage Azure pour la **ConnectionString**.

    ```csharp
    // create a linked service
    Console.WriteLine("Creating a linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "LinkedService-AzureStorage",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                )
            }
        }
    );
    ```
9. Ajoutez à la méthode **Main** le code suivant, qui crée des **jeux de données d’entrée et de sortie**.

    Le paramètre **FolderPath** de l’objet blob d’entrée a la valeur **adftutorial/**, où **adftutorial** est le nom du conteneur dans votre stockage des objets blob. Si ce conteneur n'existe pas dans votre stockage d'objets blob Azure, créez un conteneur nommé **adftutorial** et chargez un fichier texte sur celui-ci.

    Le paramètre FolderPath de l’objet blob de sortie est défini sur **adftutorial/apifactoryoutput/{Slice}** où la valeur **Slice** est calculée dynamiquement en fonction de la valeur de **SliceStart** (date/heure de début de chaque tranche).

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "LinkedService-AzureStorage",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "LinkedService-AzureStorage",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. Ajoutez à la méthode **Main** le code suivant, qui **crée et active un pipeline**. Ce pipeline dispose d’une fonction **CopyActivity** qui accepte **BlobSource** en tant que source et **BlobSink** en tant que récepteur.

    L’activité de copie effectue le déplacement des données dans Azure Data Factory. Elle est mise en œuvre par un service disponible dans le monde entier, capable de copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) .

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput() {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
11. Ajoutez à la classe **Program** la méthode d’assistance suivante utilisée par la méthode **Main**. Cette méthode affiche une boîte de dialogue qui vous permet de fournir un **nom d’utilisateur** et un **mot de passe** de connexion au portail Azure.

    ```csharp
    public static string GetAuthorizationHeader()
    {
        AuthenticationResult result = null;
        var thread = new Thread(() =>
        {
            try
            {
                var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                result = context.AcquireToken(
                    resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                    clientId: ConfigurationManager.AppSettings["AdfClientId"],
                    redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                    promptBehavior: PromptBehavior.Always);
            }
            catch (Exception threadEx)
            {
                Console.WriteLine(threadEx.Message);
            }
        });

        thread.SetApartmentState(ApartmentState.STA);
        thread.Name = "AcquireTokenThread";
        thread.Start();
        thread.Join();

        if (result != null)
        {
            return result.AccessToken;
        }

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```
12. Ajoutez le code suivant à la méthode **Main** pour obtenir l’état d’une tranche de données du jeu de données de sortie. Une seule tranche est attendue dans cet exemple.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(facultatif)** Ajoutez à la méthode **Main** le code suivant pour obtenir les détails d’exécution d’une tranche de données.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. Dans l’Explorateur de solutions, développez le projet (**DataFactoryAPITestApp**), cliquez avec le bouton droit sur **Références**, puis cliquez sur **Ajouter une référence**. Cochez la case pour l’assembly `System.Configuration` et cliquez sur **OK**.
15. Générez l'application console. Dans le menu, cliquez sur **Générer**, puis sur **Générer la solution**.
16. Vérifiez qu'il existe au moins un fichier dans le conteneur adftutorial de votre stockage d'objets blob Azure. Si ce n'est pas le cas, créez le fichier Emp.txt dans le bloc-notes avec le contenu suivant, puis chargez-le sur le conteneur adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
17. Exécutez l’exemple en cliquant dans le menu sur **Déboguer** -> **Démarrer le débogage**. Si **Obtention des détails d’exécution d’une tranche de données** s’affiche, patientez quelques minutes, puis appuyez sur **Entrée**.
18. Utilisez le portail Azure pour vérifier que la fabrique de données **APITutorialFactory** est créée avec les artefacts suivants :
    * Service lié : **LinkedService_AzureStorage**
    * Jeu de données : **DatasetBlobSource** et **DatasetBlobDestination**.
    * Pipeline : **PipelineBlobSample**
19. Vérifiez qu’un fichier de sortie est créé dans le dossier **apifactoryoutput** du conteneur **adftutorial**.

## <a name="log-in-without-popup-dialog-box"></a>Connexion sans boîte de dialogue contextuelle
L’exemple de code dans la procédure lance une boîte de dialogue dans laquelle vous pouvez entrer des informations d’identification Azure. Si vous devez vous connecter par programmation sans utiliser de boîte de dialogue, consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

> [!IMPORTANT]
> Ajoutez une application web à Azure Active Directory et notez l’ID client et la clé secrète du client de l’application.
>
>

### <a name="example"></a>Exemple
Créez la méthode GetAuthorizationHeaderNoPopup.

```csharp
public static string GetAuthorizationHeaderNoPopup()
{
    var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
    var context = new AuthenticationContext(authority.AbsoluteUri);
    var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
    AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
    if (result != null)
        return result.AccessToken;

    throw new InvalidOperationException("Failed to acquire token");
}
```

Remplacez l’appel **GetAuthorizationHeader** par un appel à **GetAuthorizationHeaderNoPopup** dans la fonction **Main** :

```csharp
TokenCloudCredentials aadTokenCredentials =
    new TokenCloudCredentials(
    ConfigurationManager.AppSettings["SubscriptionId"],
    GetAuthorizationHeaderNoPopup());
```

Voici comment créer l’application Active Directory (le principal du service) et l’affecter dans un second temps au rôle de contributeur Data Factory :

1. Créez l’application Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
    ```
2. Créez le principal du service AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
3. Ajoutez le principal du service au rôle de contributeur Data Factory.

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
4. Récupérez l’ID de l’application.

    ```PowerShell
    $azureAdApplication
    ```

Notez l’ID d’application et le mot de passe (clé secrète client) et utilisez-les dans la procédure.

## <a name="get-azure-subscription-and-tenant-ids"></a>Obtenir les ID d’abonnement et client Azure
Si vous n’avez pas la dernière version d’Azure PowerShell sur votre ordinateur, suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .

1. Démarrez Azure PowerShell et exécutez la commande suivante
2. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```

    Si vous n'avez qu’un seul abonnement Azure associé à ce compte, il est inutile d’effectuer les deux étapes suivantes.
3. Exécutez la commande suivante pour afficher tous les abonnements de ce compte.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **NameOfAzureSubscription** par le nom de votre abonnement Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext
    ```PowerShell

Note down the **SubscriptionId** and **TenantId** values.

