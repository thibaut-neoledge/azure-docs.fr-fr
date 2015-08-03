<properties 
	pageTitle="Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory" 
	description="Learn how to programmatically create, monitor, and manage Azure data factories by using Data Factory SDK." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="spelluru"/>

# Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET
## Vue d'ensemble
Vous pouvez créer, surveiller et gérer des fabriques de données Azure par programmation à l'aide du Kit de développement logiciel (SDK) Data Factory .NET. Cet article contient une procédure pas à pas que vous pouvez suivre pour créer un exemple d'application console .NET qui crée et surveille une fabrique de données. Pour plus d'informations sur le Kit de développement logiciel (SDK) Data Factory .NET, consultez [Informations de référence sur la bibliothèque de classes Data Factory][adf-class-library-reference].



## Composants requis

- Visual Studio 2012 ou 2013
- Téléchargez et installez le [Kit de développement logiciel (SDK) Azure .NET][azure-developer-center].
- Téléchargez et installez les packages NuGet pour Azure Data Factory. Les instructions sont fournies dans la procédure pas à pas.

## Procédure pas à pas
1. À l'aide de Visual Studio 2012 ou 2013, créez une application console Visual C# .NET.
	<ol type="a">
	<li>Lancez <b>Visual Studio&#160;2012</b> ou <b>Visual Studio&#160;2013</b>.</li>
	<li>Cliquez sur <b>Fichier</b>, pointez le curseur de la souris sur <b>Nouveau</b>, puis cliquez sur <b>Projet</b>.</li> 
	<li>Développez <b>Modèles</b>, puis sélectionnez <b>Visual&#160;C#</b>. Dans cette procédure pas à pas, vous utilisez&#160;C#, mais vous pouvez utiliser un autre langage&#160;.NET.</li> 
	<li>Sélectionnez <b>Application console</b> dans la liste des types de projet située sur la droite.</li>
	<li>Entrez <b>DataFactoryAPITestApp</b> dans le champ <b>Nom</b>.</li> 
	<li>Sélectionnez <b>C:\ADFGetStarted</b> comme <b>Emplacement</b>.</li>
	<li>Cliquez sur <b>OK</b> pour créer le projet.</li>
</ol>
2. Cliquez sur <b>Outils</b>, pointez sur <b>Gestionnaire de package NuGet</b>, puis cliquez sur <b>Console du gestionnaire de package</b>.
3.	Dans la fenêtre <b>Console du gestionnaire de package</b>, exécutez les commandes suivantes une par une.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Ajoutez la section **appSettings** suivante au fichier **App.config**. Ces valeurs sont utilisées par la méthode d'assistance **GetAuthorizationHeader**. 

	Remplacez les valeurs de **SubscriptionId** et **ActiveDirectoryTenantId** par votre ID d'abonnement et votre ID de locataire Azure. Vous pouvez obtenir ces valeurs en exécutant **Get-AzureAccount** à partir d'Azure PowerShell (vous devrez peut-être commencer par vous connecter via Add-AzureAccount).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="<subscription ID>" />
    		<add key="ActiveDirectoryTenantId" value="<tenant ID" />
		</appSettings>
6. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. Ajoutez à la méthode **Main** le code suivant, qui crée une instance de la classe **DataPipelineManagementClient**. Cet objet vous permet de créer une fabrique de données, un service lié, les tables d'entrée et de sortie, ainsi qu'un pipeline. Cet objet vous permet également d'analyser les tranches d'une table au moment de l'exécution.    

        // create data factory management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

7. Ajoutez à la méthode **Main** le code suivant, qui crée une **fabrique de données**.

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

8. Ajoutez à la méthode **Main** le code suivant, qui crée un **service lié**.
	> [AZURE.NOTE]Utilisez le **nom de compte** et la **clé de compte** de votre compte de stockage Azure pour **ConnectionString**.

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
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=spestore;AccountKey=4VwviDOId32nYKABQy9NHsMG0vC/CXx9iuR02HJdGL+0kieqHqbT3ap+bM/c+aGnGoA7SqkwNFq90hqV1bmV0w==")
                    )
                }
            }
        );
9. Ajoutez à la méthode **Main** le code suivant, qui crée des **tables d'entrée et de sortie**. 

	Notez que le paramètre **FolderPath** de l'objet blob d'entrée a la valeur **adftutorial/**, où **adftutorial** est le nom du conteneur dans votre stockage d'objets blob. Si ce conteneur n'existe pas dans votre stockage d'objets blob Azure, créez un conteneur nommé **adftutorial** et chargez un fichier texte sur celui-ci.
	
	Notez que le paramètre FolderPath de l'objet blob de sortie est défini sur **adftutorial/apifactoryoutput/{Slice}** où la valeur **Slice** est calculée dynamiquement en fonction de la valeur de **SliceStart** (date/heure de début de chaque tranche).

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
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

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
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

11. Ajoutez à la méthode **Main** le code suivant, qui **crée et active un pipeline**. Ce pipeline dispose d'une fonction **CopyActivity** qui accepte **BlobSource** en tant que source et **BlobSink** en tant que récepteur.

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
                                        Name = Table_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                        
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

	

12. Ajoutez à la classe **Program** la méthode d'assistance suivante utilisée par la méthode **Main**. Cette méthode affiche une boîte de dialogue qui vous permet de fournir un **nom d'utilisateur** et un **mot de passe** de connexion au portail Azure.
 
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
 
13. Ajoutez le code suivant à la méthode **Main** pour obtenir l'état d'une tranche de données de la table de sortie. Une seule tranche est attendue dans cet exemple.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. **(Facultatif)** Ajoutez à la méthode **Main** le code suivant pour obtenir les détails d’exécution d’une tranche de données.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Table_Destination,
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

15. Générez l'application console. Dans le menu, cliquez sur **Générer**, puis sur **Générer la solution**. Si vous obtenez une erreur sur la classe **ConfigurationManager**, ajoutez une référence à l’assembly **System.Configuration** et réessayez de générer.
16. Vérifiez qu'il existe au moins un fichier dans le conteneur adftutorial de votre stockage d'objets blob Azure. Si ce n'est pas le cas, créez le fichier Emp.txt dans le bloc-notes avec le contenu suivant, puis chargez-le sur le conteneur adftutorial.

        John, Doe
		Jane, Doe
	 
17. Exécutez l'exemple en cliquant dans le menu sur **Déboguer** -> **Démarrer le débogage**. Si **Obtention des détails d’exécution d’une tranche de données** s’affiche, patientez quelques minutes, puis appuyez sur **ENTRÉE**.
18. Utilisez le portail Azure en version préliminaire pour vérifier que la fabrique de données **APITutorialFactory** est créée avec les artefacts suivants : 
	- Service lié : **LinkedService_AzureStorage** 
	- Tables : **TableBlobSource** et **TableBlobDestination**
	- Pipeline : **PipelineBlobSample** 
18. Vérifiez qu'un fichier de sortie est créé dans le dossier **apifactoryoutput** du conteneur **adftutorial**.


## Voir aussi

Article | Description
------ | ---------------
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence pour les développeurs incluent des références complètes sur la bibliothèque de classes .NET, les applets de commande, le script JSON, les fonctions, etc. 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=July15_HO4-->