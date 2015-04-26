<properties title="Create, monitor, and manage Azure data factories using Data Factory SDK" pageTitle="Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory" description="Découvrez comment utiliser le Kit de développement logiciel (SDK) de Data Factory pour créer, surveiller et gérer des fabriques de données Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="spelluru" />

# Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET
Vous pouvez créer, surveiller et gérer des fabriques de données Azure par programmation à l'aide du Kit de développement logiciel (SDK) Data Factory .NET. Cet article contient une procédure pas à pas que vous pouvez suivre pour créer un exemple d'application console .NET qui crée et surveille une fabrique de données. Pour plus d'informations sur le Kit de développement logiciel (SDK) Data Factory .NET, consultez la [référence de la bibliothèque de classes Data Factory][adf-class-library-reference]. 

## Procédure pas à pas : créer une fabrique de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET

**Conditions préalables :**

- Visual Studio 2012 ou 2013
- Téléchargez et installez le [Kit de développement logiciel (SDK) Microsoft Azure .NET][azure-developer-center]
- Téléchargez et installez les packages NuGet pour Azure Data Factory. Les instructions sont fournies dans la procédure pas à pas.

### Étape 1 : création d'une fabrique de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET
1. À l'aide de Visual Studio 2012 ou 2013, créez une application console Visual C# .NET.
	<ol type="a">
		<li>Lancez <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
		<li>Cliquez sur <b>Fichier</b>, pointez sur <b>Nouveau</b>, puis cliquez sur <b>Projet</b>.</li> 
		<li>Développez <b>Modèles</b>, puis sélectionnez <b>Visual C#</b>. Dans cette procédure pas à pas, vous utilisez C#, mais vous pouvez utiliser un autre langage .NET.</li> 
		<li>Sélectionnez <b>Application console</b> dans la liste des types de projet à droite.</li>
		<li>Entrez <b>DataFactoryAPITestApp</b> comme <b>Nom</b>.</li> 
		<li>Sélectionnez <b>C:\ADFGetStarted</b> comme <b>Emplacement</b>.</li>
		<li>Cliquez sur <b>OK</b> pour créer le projet.</li>
	</ol>
2. Cliquez sur <b>Outils</b>, pointez sur <b>gestionnaire de package NuGet</b>, puis cliquez sur <b>Console du gestionnaire de package</b>.
3.	Dans la <b>Console du gestionnaire de package</b>, exécutez les commandes suivantes une par une.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories -Pre
		Install-Package Microsoft.DataFactories.Runtime -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Ajoutez la section **appSetttings** suivante au fichier **App.config**. Ces valeurs sont utilisées par la méthode d'assistance : **GetAuthorizationHeader**. 

	Remplacez les valeurs de **SubscriptionId** et **ActiveDirectoryTenantId** par votre ID d'abonnement et votre ID de locataire Azure. Vous pouvez obtenir ces valeurs en exécutant **Get-AzureAccount** à partir d'Azure PowerShell (vous devrez peut-être commencer par vous connecter via Add-AzureAccount).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.WindowsAzure; 
6. Ajoutez le code suivant, qui crée une instance de la classe **DataPipelineManagementClient**, à la méthode **Main**. Cet objet vous permet de créer une fabrique de données, un service lié, les tables d'entrée et de sortie, ainsi qu'un pipeline. Cet objet vous permet également d'analyser les tranches d'une table au moment de l'exécution.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Add the following code that creates a **data factory** to the **Main** method.

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
8. Ajoutez le code suivant, qui crée un **service lié**, dans la méthode **Main**. 
	> [WACOM.NOTE] Utilisez le **nom de compte** et la **clé de compte** de votre compte de stockage Azure pour **ConnectionString**. 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. Ajoutez le code suivant, qui crée des **tables d'entrée et de sortie**, dans la méthode **Main**. 

	Notez que **FolderPath** de l'objet blob d'entrée a la valeur **adftutorial/**, où **adftutorial** est le nom du conteneur dans votre stockage d'objets blob. Si ce conteneur n'existe pas dans votre stockage d'objets blob Azure, créez un conteneur avec ce nom : **adftutorial**, puis téléchargez un fichier texte dans le conteneur.
	
	Notez que le FolderPath de l'objet blob de sortie a la valeur : **adftutorial/apifactoryoutput/{Slice}** où **Slice** est calculé dynamiquement en fonction de la valeur de **SliceStart** (date/heure de début de chaque tranche.)  

 
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
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
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
                        Location = new AzureBlobLocation()
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
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Ajoutez le code suivant, qui **crée et active un pipeline**, dans la méthode **Main**. Ce pipeline a un **CopyActivity** qui accepte **BlobSource** en tant que source et **BlobSink** en tant que récepteur. 

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

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. Ajoutez la méthode d'assistance suivante utilisée par la méthode **Main** pour la classe **Program**. Cette méthode affiche une boîte de dialogue qui vous permet de fournir un **nom d'utilisateur** et un **mot de passe** de connexion au portail Azure. 
 
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

14. Ajoutez le code suivant pour obtenir les détails d'exécution d'une tranche dans la méthode **Main**.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

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
    }

15. Générez l'application console. Dans le menu, cliquez sur **Générer**, puis sur **Générer la solution**.
16. Vérifiez qu'il existe au moins un fichier dans le conteneur adftutorial de votre stockage d'objets blob Azure. Si ce n'est pas le cas, créez le fichier Emp.txt dans le bloc-notes avec le contenu suivant, puis téléchargez-le vers le conteneur adftutorial.

        John, Doe
		Jane, Doe
	 
17. Exécutez l'exemple en cliquant dans le menu sur **Déboguer** -> **Démarrer le débogage**.
18. Utilisez le portail Azure en version préliminaire pour vérifier que la fabrique de données : **APITutorialFactory** est créée avec les artefacts suivants : 
	- Service lié : **LinkedService_AzureStorage** 
	- Tables : **TableBlobSource** et **TableBlobDestination**.
	- Pipeline : **PipelineBlobSample** 
18. Vérifiez qu'un fichier de sortie est créé dans le dossier **apifactoryoutput** du conteneur **adftutorial**.


## Voir aussi

Article | Description
------ | ---------------
[Présentation d'Azure Data Factory][data-factory-introduction] | Cet article présente le service Azure Data Factory, les concepts associés, sa valeur ajoutée et les scénarios qu'il prend en charge.
[Prise en main d'Azure Data Factory][adf-getstarted] | Cet article propose un didacticiel pas à pas qui permet de créer un exemple de fabrique de données Azure qui copie les données d'un objet blob Azure vers une base de données SQL Azure.
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas qui permet de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas qui permet de créer une activité personnalisée et de l'utiliser dans un pipeline.
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les applets de commande, le script JSON, les fonctions, etc. 


[data-factory-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/fr-fr/downloads/

<!--HONumber=35.2-->
