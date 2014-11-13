<properties urlDisplayName="Get Started with Media Services" pageTitle="Prise en main de Media Services - Azure" metaKeywords="Azure media services" description="Introduction &agrave; l'utilisation de Media Services avec Azure." metaCanonical="" services="media-services" documentationCenter="" title="Prise en main de Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="getting-started"></a>Prise en main de Media Services

Ce didacticiel vous montre comment commencer à développer avec Azure Media Services. Il présente le workflow Media Services de base et les objets et tâches de programmation les plus courants requis pour le développement Media Services. À la fin du didacticiel, vous pourrez lire un exemple de fichier multimédia que vous avez chargé, encodé et téléchargé. Ou vous pourrez accéder à l'élément multimédia encodé et le lire sur le serveur.

Un projet C# Visual Studio qui contient le code pour ce didacticiel est disponible ici : [Télécharger][Télécharger].

Ce didacticiel vous familiarise avec ces étapes de base :

-   [Configuration de votre projet][Configuration de votre projet]
-   [Obtention du contexte de serveur Media Services][Obtention du contexte de serveur Media Services]
-   [Création d'un élément multimédia et téléchargement des fichiers associés dans Media Services][Création d'un élément multimédia et téléchargement des fichiers associés dans Media Services]
-   [Encodage d'un élément multimédia et téléchargement d'un élément multimédia de sortie][Encodage d'un élément multimédia et téléchargement d'un élément multimédia de sortie]

## Configuration requise

Les conditions préalables suivantes sont requises pour la procédure pas à pas et pour le développement basé sur le Kit de développement logiciel (SDK) d'Azure Media Services.

-   Un compte Media Services dans un abonnement Azure nouveau ou existant. Pour plus d'informations, consultez la page [Création d'un compte Media Services][Création d'un compte Media Services].
-   Systèmes d'exploitation : Windows 7, Windows 2008 R2, Windows 8 ou une version ultérieure.
-   .NET Framework 4.5 ou .NET Framework 4.
-   Visual Studio 2012, Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou une version ultérieure.
-   Installez les bibliothèques **Kit de développement logiciel (SDK) Azure pour .NET**, **Kit de développement logiciel (SDK) d'Azure Media Services pour .NET** et **Services de données WCF 5.0 pour OData V3**, puis ajoutez les références à votre projet à l'aide du package [NuGet windowsazure.mediaservices][NuGet windowsazure.mediaservices]. La section suivante montre comment installer et ajouter ces références.

> [WACOM.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].

## <span id="Step1"></span></a>Configuration de votre projet

1.  Créez une application console C# dans Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, l'**emplacement** et le **nom de solution**, puis cliquez sur OK.

2.  Ajoutez une référence à l'assembly System.Configuration.

    Pour ajouter des références à l'aide de la boîte de dialogue **Gérer les références**, procédez comme suit. Cliquez avec le bouton droit sur le nœud **Références** dans l'**Explorateur de solutions**, puis sélectionnez **Ajouter une référence**. Dans la boîte de dialogue **Gérer les références**, sélectionnez les assemblys appropriés (dans ce cas, System.Configuration).

3.  Si ce n'est pas déjà fait, ajoutez les références aux bibliothèques **Kit de développement logiciel (SDK) Azure pour .NET** (Microsoft.WindowsAzure.StorageClient.dll), **Kit de développement logiciel (SDK) d'Azure Media Services pour .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) et **Services de données WCF 5.0 pour OData V3** (Microsoft.Data.OData.dll) à l'aide du package [NuGet windowsazure.mediaservices][NuGet windowsazure.mediaservices].

    Pour ajouter des références à l'aide de NuGet, procédez comme suit. Dans le menu principal de Visual Studio, sélectionnez OUTILS -\> Gestionnaire de package de bibliothèques -\> Console du Gestionnaire de package. Dans la fenêtre de la console, tapez *Install-Package [nom du package]*, puis appuyez sur Entrée (dans le cas présent, utilisez la commande suivante : *Install-Package windowsazure.mediaservices*.)

4.  Ajoutez une section *appSettings* au fichier **app.config**, puis définissez les valeurs du nom et de la clé de votre compte Azure Media Services. Vous avez obtenu le nom et la clé du compte Media Services lors du processus de configuration du compte. Ajoutez ces valeurs à l'attribut de valeur pour chaque paramètre dans le fichier app.config du projet Visual Studio.

    > [WACOM.NOTE]
    > Dans Visual Studio 2012, le fichier App.config est ajouté par défaut. Dans Visual Studio 2010, vous devez ajouter manuellement le fichier de configuration de l'application.

        <configuration>
        . . . 
        <appSettings>
            <add key="accountName" value="Add-Media-Services-Account-Name" />
            <add key="accountKey" value="Add-Media-Services-Account-Key" />
        </appSettings>
        </configuration>

5.  Créez un dossier sur votre ordinateur local et nommez-le supportFiles (dans cet exemple, supportFiles est situé sous le répertoire du projet MediaServicesGettingStarted). Le [Projet][Télécharger] qui accompagne cette procédure pas à pas contient le répertoire supportFiles. Vous pouviez copier le contenu de ce répertoire dans votre dossier supportFiles.

6.  Remplacez les instructions using existantes au début du fichier Program.cs par le code suivant.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

7.  Ajoutez les variables de chemin d'accès de niveau classe suivantes. Le chemin d'accès \*\*\_supportFiles\*\* doit pointer sur le dossier que vous avez créé lors d'une étape précédente.

        // Base support files path.  Update this field to point to the base path  
        // for the local support files folder that you create. 
        private static readonly string _supportFiles =
                    Path.GetFullPath(@"../..\supportFiles");

        // Paths to support files (within the above base path). You can use 
        // the provided sample media files from the "supportFiles" folder, or 
        // provide paths to your own media files below to run these samples.
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
        private static readonly string _outputFilesFolder =
            Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  Ajoutez les variables de niveau classe suivantes pour récupérer les paramètres d'authentification et de connexion. Ces paramètres sont extraits du fichier App.Config et sont requis pour vous connecter à Media Services, vous authentifier et obtenir un jeton afin que vous puissiez accéder au contexte de serveur. Le code du projet référence ces variables pour créer une instance du contexte de serveur.

        private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
        private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  Ajoutez la variable de niveau classe suivante qui est utilisée comme référence statique au contexte de serveur.

        // Field for service context.
        private static CloudMediaContext _context = null;

## <span id="Step2"></span></a>Obtention du contexte Media Services

L'objet de contexte Media Services contient tous les principaux objets et collections auxquels accéder pour la programmation Media Services. Le contexte comprend des références à des collections importantes comme les travaux, les éléments multimédias, les fichiers, les stratégies d'accès, les localisateurs et d'autres objets La plupart des tâches de programmation Media Services requièrent le contexte de serveur.

Dans le fichier Program.cs, ajoutez le code suivant en tant que premier élément de votre méthode **Main**. Ce code utilise les valeurs de nom et de clé de votre compte Media Services à partir du fichier app.config pour créer une instance de contexte de serveur. L'instance est attribuée à la variable \*\*\_context\*\* que vous avez créée au niveau classe.

    // Get the service context.
    _context = new CloudMediaContext(_accountName, _accountKey);

## <span id="Step3"></span></a>Création d'un élément multimédia et chargement d'un fichier

Le code de cette section permet d'effectuer les opérations suivantes :

1.  Création d'un élément multimédia vide
     Lorsque vous créez des éléments multimédias, vous pouvez spécifier trois options différentes pour les chiffrer.

    -   **AssetCreationOptions.None** : pas de chiffrement. Si vous voulez créer un élément multimédia non chiffré, vous devez définir cette option.
    -   **AssetCreationOptions.CommonEncryptionProtected** : pour les fichiers CENC (Common Encryption Protected). Par exemple, un ensemble de fichiers pour lesquels le chiffrement PlayReady est déjà activé.
    -   **AssetCreationOptions.StorageEncrypted** : chiffrement du stockage. Chiffre un fichier d'entrée clair avant de le charger sur le stockage Azure.

        <div class="dev-callout">

        **Remarque**
        Media Services fournit un chiffrement de stockage sur disque, et non pas sur le réseau comme pour la gestion des droits numériques (DRM).

        </div>

2.  Création d'une instance AssetFile que nous voulons associer à l'élément multimédia.
3.  Création d'une instance AccessPolicy qui définit les autorisations et la durée de l'accès à l'élément multimédia.
4.  Création d'une instance Locator qui fournit l'accès à l'élément multimédia.
5.  Chargement d'un seul fichier multimédia dans Media Services. Le processus de création et de chargement est également appelé réception d'éléments multimédias.

Ajoutez les méthodes suivantes à la classe.

    static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
    {
        var asset = _context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);
        Console.WriteLine("Time created: " + asset.Created.Date.ToString());

        return asset;
    }

    static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                            AccessPermissions.Write | AccessPermissions.List);

        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

Ajoutez un appel à la méthode après la ligne \*\*\_context = new CloudMediaContext(\_accountName, \_accountKey);\*\* dans la méthode Main.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

## <span id="Step4"></span></a>Encodage de l'élément multimédia sur le serveur et téléchargement d'un élément multimédia de sortie

Dans Media Services, vous pouvez créer des tâches qui traitent le contenu multimédia de plusieurs manières : encodage, chiffrement, conversions de formats, etc. Un travail Media Services contient toujours une ou plusieurs tâches qui spécifient les détails du traitement. Dans cette section, vous créez une tâche de codage de base, puis vous exécutez un travail qui effectue la tâche via Azure Media Encoder. La tâche utilise une chaîne de présélection pour spécifier le type d'encodage à effectuer. Pour consulter les valeurs d'encodage de présélection disponibles, consultez la page [Présélections du système Media Services Encoder][Présélections du système Media Services Encoder]. Media Services prend en charge les mêmes formats d'entrée et de sortie de fichier multimédia que Microsoft Expression Encoder. Pour obtenir la liste des formats pris en charge, consultez la page [Types de fichiers pris en charge pour Media Services][Types de fichiers pris en charge pour Media Services].

1.  Ajoutez la définition de méthode **CreateEncodingJob** suivante à votre classe. Cette méthode montre comment accomplir plusieurs tâches requises pour un travail d'encodage :

    -   Déclarez un nouveau travail.
    -   Déclarez un processeur multimédia pour traiter le travail. Un processeur multimédia est un composant qui gère l'encodage, le chiffrement, la conversion de format et d'autres travaux de traitement connexes. Il existe plusieurs types de processeurs multimédias disponibles (vous pouvez itérer à travers ces derniers avec \_context.MediaProcessors.). La méthode GetLatestMediaProcessorByName mentionnée plus tard dans cette présentation renvoie le processeur Azure Media Encoder.
    -   Déclarez une nouvelle tâche. Chaque travail inclut une ou plusieurs tâches. Notez qu'avec la tâche, vous lui transmettez un nom convivial, une instance de processeur multimédia, une chaîne de configuration de tâche et des options de création de tâche. La chaîne de configuration spécifie les paramètres d'encodage. Cet exemple utilise le paramètre **H264 Broadband 720p**. Cette présélection génère un fichier MP4 unique. Pour plus d'informations à ce sujet et sur les autres présélections, consultez la page [Chaînes de présélection des tâches pour Azure Media Encoder][Chaînes de présélection des tâches pour Azure Media Encoder].
    -   Ajoutez un élément multimédia d'entrée à la tâche. Dans cet exemple, l'élément multimédia d'entrée est celui que vous avez créé dans la section précédente.
    -   Ajoutez un élément multimédia de sortie à la tâche. Pour un élément multimédia de sortie, spécifiez un nom convivial, une valeur booléenne pour indiquer s'il faut enregistrer la sortie sur le serveur une fois le travail terminé et une valeur **AssetCreationOptions.None** pour indiquer que la sortie n'est pas chiffrée pour le stockage et le transport.
    -   Envoyez le travail.
         Il s'agit de la dernière étape requise pour effectuer un travail d'encodage.

    La méthode montre aussi comment effectuer d'autres tâches utiles (mais facultatives) comme le suivi de la progression des travaux et l'accès à l'élément multimédia créé par votre travail d'encodage.

        static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Broadband 720p",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Optionally log job details. This displays basic job details
            // to the console and saves them to a JobDetails-{JobId}.txt file 
            // in your output folder.
            LogJobDetails(job.Id);

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // **********
            // Optional code.  Code after this point is not required for 
            // an encoding job, but shows how to access the assets that 
            // are the output of a job, either by creating URLs to the 
            // asset on the server, or by downloading. 
            // **********

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return job;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];
            IAccessPolicy policy = null;
            ILocator locator = null;

            // Declare an access policy for permissions on the asset. 
            // You can call an async or sync create method. 
            policy =
                _context.AccessPolicies.Create("My 30 days readonly policy",
                    TimeSpan.FromDays(30),
                    AccessPermissions.Read);

            // Create a SAS locator to enable direct access to the asset 
            // in blob storage. You can call a sync or async create method.  
            // You can set the optional startTime param as 5 minutes 
            // earlier than Now to compensate for differences in time  
            // between the client and server clocks. 

            locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

            // Build a list of SAS URLs to each file in the asset. 
            List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

            // Write the URL list to a local file. You can use the saved 
            // SAS URLs to browse directly to the files in the asset.
            if (sasUrlList != null)
            {
                string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
                StringBuilder fileList = new StringBuilder();
                foreach (string url in sasUrlList)
                {
                    fileList.AppendLine(url);
                    fileList.AppendLine();
                }
                WriteToFile(outFilePath, fileList.ToString());

                // Optionally download the output to the local machine.
                DownloadAssetToLocal(job.Id, outputFolder);
            }

            
            return job;
        }

2.  Ajoutez un appel à la méthode **CreateEncodingJob** dans votre méthode **Main** après les lignes ajoutées précédemment.

        CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);

3.  Ajoutez les méthodes d'assistance suivantes à la classe. Elles sont nécessaires pour prendre en charge la méthode **CreateEncodingJob**. Ce qui suit est un résumé des méthodes d'assistance.
    -   La méthode **GetLatestMediaProcessorByName** renvoie un processeur multimédia approprié pour gérer l'encodage, le chiffrement ou d'autres tâches de traitement connexes. Vous créez un processeur multimédia à l'aide du nom de chaîne approprié pour le processeur à créer. Les chaînes pouvant être transmises à la méthode pour le paramètre mediaProcessor sont : **Azure Media Encoder**, **Microsoft Azure Media Packager**, **Microsoft Media Encryptor** et **Storage Decryption**.

            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                // The possible strings that can be passed into the 
                // method for the mediaProcessor parameter:
                //   Azure Media Encoder
                //   Windows Azure Media Packager
                //   Windows Azure Media Encryptor
                //   Storage Decryption

                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

    -   Lorsque vous exécutez des travaux, vous avez généralement besoin de faire appel à une méthode de suivi de la progression du travail. L'exemple de code suivant définit un gestionnaire d'événements StateChanged. Ce dernier suit la progression du travail et fournit l'état mis à jour, selon l'état. Le code définit également la méthode LogJobStop. Cette méthode d'assistance journalise les détails de l'erreur.

            private static void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("********************");
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine("Please wait while local tasks or downloads complete...");
                        Console.WriteLine("********************");
                        Console.WriteLine();
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

            private static void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobStop-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }

            private static void LogJobDetails(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nJob ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);

                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobDetails-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }
                    
            private static string JobIdAsFileName(string jobID)
            {
                return jobID.Replace(":", "_");
            }

    -   La méthode WriteToFile écrit un fichier dans le dossier de sortie spécifié.

            static void WriteToFile(string outFilePath, string fileContent)
            {
                StreamWriter sr = File.CreateText(outFilePath);
                sr.Write(fileContent);
                sr.Close();
            }

    -   Après avoir encodé des éléments multimédias dans Media Services, vous pouvez accéder aux éléments multimédias de sortie qui résultent d'un travail d'encodage. Cette procédure pas à pas vous montre deux façons d'accéder à la sortie d'un travail d'encodage :

        -   Création d'une URL SAS vers un élément multimédia sur le serveur.
        -   Téléchargement d'un élément multimédia de sortie à partir du serveur.

        La méthode GetAssetSasUrlList crée une liste des URL SAS de tous les fichiers dans un élément multimédia.

            static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
            {
                // Declare a list to contain all the SAS URLs.
                List<String> fileSasUrlList = new List<String>();

                // If the asset has files, build a list of URLs to 
                // each file in the asset and return. 
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    string sasUrl = BuildFileSasUrl(file, locator);
                    fileSasUrlList.Add(sasUrl);
                }

                // Return the list of SAS URLs.
                return fileSasUrlList;
            }

            // Create and return a SAS URL to a single file in an asset. 
            static string BuildFileSasUrl(IAssetFile file, ILocator locator)
            {
                // Take the locator path, add the file name, and build 
                // a full SAS URL to access this file. This is the only 
                // code required to build the full URL.
                var uriBuilder = new UriBuilder(locator.Path);
                uriBuilder.Path += "/" + file.Name;

                // Optional:  print the locator.Path to the asset, and 
                // the full SAS URL to the file
                Console.WriteLine("Locator path: ");
                Console.WriteLine(locator.Path);
                Console.WriteLine();
                Console.WriteLine("Full URL to file: ");
                Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
                Console.WriteLine();


                //Return the SAS URL.
                return uriBuilder.Uri.AbsoluteUri;
            }

    -   La méthode **DownloadAssetToLocal** télécharge chaque fichier de l'élément multimédia dans un dossier local. Dans cet exemple, comme l'élément multimédia a été créé avec un fichier multimédia d'entrée, la collection de fichiers d'éléments multimédias de sortie contient deux fichiers : le fichier multimédia encodé (un fichier .mp4) et un fichier .xml avec les métadonnées sur l'élément multimédia. La méthode télécharge les deux fichiers.

            static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
            {
                // This method illustrates how to download a single asset. 
                // However, you can iterate through the OutputAssets
                // collection, and download all assets if there are many. 

                // Get a reference to the job. 
                IJob job = GetJob(jobId);
                // Get a reference to the first output asset. If there were multiple 
                // output media assets you could iterate and handle each one.
                IAsset outputAsset = job.OutputMediaAssets[0];

                IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
                ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
                BlobTransferClient blobTransfer = new BlobTransferClient
                {
                    NumberOfConcurrentTransfers = 10,
                    ParallelTransferThreadCount = 10
                };

                var downloadTasks = new List<Task>();
                foreach (IAssetFile outputFile in outputAsset.AssetFiles)
                {
                    // Use the following event handler to check download progress.
                    outputFile.DownloadProgressChanged += DownloadProgress;

                    string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

                    Console.WriteLine("File download path:  " + localDownloadPath);

                    downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

                    outputFile.DownloadProgressChanged -= DownloadProgress;
                }

                Task.WaitAll(downloadTasks.ToArray());

                return outputAsset;
            }

            static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
            {
                Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
            }

    -   Les méthodes d'assistance GetJob et GetAsset demandent et renvoient une référence à un objet travail et à un objet élément multimédia avec les ID spécifiés. Vous devez utiliser un type de requête LINQ similaire pour renvoyer des références aux autres objets Media Services sur le serveur.

            static IJob GetJob(string jobId)
            {
                // Use a Linq select query to get an updated 
                // reference by Id. 
                var jobInstance =
                    from j in _context.Jobs
                    where j.Id == jobId
                    select j;
                // Return the job reference as an Ijob. 
                IJob job = jobInstance.FirstOrDefault();

                return job;
            }
            static IAsset GetAsset(string assetId)
            {
                // Use a LINQ Select query to get an asset.
                var assetInstance =
                    from a in _context.Assets
                    where a.Id == assetId
                    select a;
                // Reference the asset as an IAsset.
                IAsset asset = assetInstance.FirstOrDefault();

                return asset;
            }

## Test du code

Exécutez le programme (appuyez sur F5). La console affiche une sortie semblable à ce qui suit :

    Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
    Time created: 11/14/2012 12:00:00 AM
    Created assetFile interview2.wmv
    Upload interview2.wmv
    Done uploading of interview2.wmv using Upload()

    Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
    Job Name: My encoding job
    Job submitted (client UTC time): 11/14/2012 10:09:39 PM
    Media Services account name: Add-Media-Services-Account-Name
    Media Services account location: Add-Media-Services-account-location-name

    Job(My encoding job) state: Queued.
    Please wait...

    Job(My encoding job) state: Processing.
    Please wait...

    ********************
    Job(My encoding job) is finished.
    Please wait while local tasks or downloads complete...
    ********************

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
    A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
    BxERnav8Jb6hL7fxylq3oESc%3D

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
    1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
    od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

    Downloads are in progress, please wait.

    File download path:  C:\supportFiles\outputfiles\interview2.mp4
    1.70952185308162 % download progress.
    3.68508804454907 % download progress.
    6.48870388360293 % download progress.
    6.83808741232649 % download progress.
    . . . 
    99.0763740574049 % download progress.
    99.1522674787341 % download progress.
    100 % download progress.
    File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
    100 % download progress.

1.  Suite à l'exécution de cette application, ce qui suit se produit :

2.  Un fichier .wmv est chargé dans Media Services.

3.  Le fichier est ensuite encodé à l'aide de la présélection **H264 Broadband 720p** d'**Azure Media Encoder**.

4.  Le fichier FileSasUrlList.txt est créé dans le dossier \\supportFiles\\outputFiles. Le fichier contient l'URL de l'élément multimédia encodé.

    Pour lire le fichier multimédia, copiez l'URL de l'élément multimédia à partir du fichier texte et copiez l'URL dans un navigateur.

5.  Le fichier multimédia .mp4 et le fichier \_metadata.xml sont téléchargés dans le dossier outputFiles.

> [WACOM.NOTE]
> Dans le modèle objet Media Services, un élément multimédia est un objet de collection de contenu Media Services représentant un ou plusieurs fichiers. Le chemin d'accès au localisateur fournit une URL d'objet blob Azure qui est le chemin d'accès de base à cet élément multimédia dans Azure Storage. Pour accéder à des fichiers spécifiques dans l'élément multimédia, ajoutez un nom de fichier au chemin d'accès au localisateur de base.

## Étapes suivantes

Cette procédure pas à pas a décrit une séquence de tâches de programmation pour créer une application Media Services simple. Vous avez découvert les tâches de programmation Media Services fondamentales, notamment l'obtention du contexte de serveur, ainsi que la création, l'encodage et le téléchargement des éléments multimédias ou leur accès sur le serveur. Pour les étapes suivantes et des tâches de développement plus avancées, consultez les pages suivantes :

-   [Utilisation de Media Services][Utilisation de Media Services]
-   [Création d'applications avec l'API REST de Media Services][Création d'applications avec l'API REST de Media Services]

<!-- Anchors. -->

  [Télécharger]: http://go.microsoft.com/fwlink/?linkid=253275
  [Configuration de votre projet]: #Step1
  [Obtention du contexte de serveur Media Services]: #Step2
  [Création d'un élément multimédia et téléchargement des fichiers associés dans Media Services]: #Step3
  [Encodage d'un élément multimédia et téléchargement d'un élément multimédia de sortie]: #Step4
  [Création d'un compte Media Services]: http://go.microsoft.com/fwlink/?LinkId=256662
  [NuGet windowsazure.mediaservices]: http://nuget.org/packages/windowsazure.mediaservices
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A8A8397B5
  [Présélections du système Media Services Encoder]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj129582.aspx
  [Types de fichiers pris en charge pour Media Services]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh973634.aspx
  [Chaînes de présélection des tâches pour Azure Media Encoder]: http://msdn.microsoft.com/library/windowsazure/jj129582.aspx
  [Utilisation de Media Services]: http://azure.microsoft.com/fr-fr/develop/media-services/resources/
  [Création d'applications avec l'API REST de Media Services]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh973618.aspx
