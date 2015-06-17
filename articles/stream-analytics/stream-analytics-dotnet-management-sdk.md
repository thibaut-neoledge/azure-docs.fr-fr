<properties 
	pageTitle="Utilisation du Kit de développement logiciel (SDK) .NET de gestion d'Azure Stream Analytics | Azure" 
	description="Apprenez à utiliser le Kit de développement logiciel (SDK) .NET de gestion de Stream Analytics" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015" 
	ms.author="jgao"/>


# Utilisation du Kit de développement logiciel (SDK) .NET de gestion d'Azure Stream Analytics

[Cette documentation préliminaire est susceptible d'être modifiée dans les futures versions.] 

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Dans sa version préliminaire, Stream Analytics permet aux clients de configurer des travaux de diffusion en continu pour analyser des flux de données et de mener à bien des analyses en temps réel.  

Cet article montre comment utiliser le Kit de développement logiciel (SDK) .NET de gestion Azure Stream Analytics.


## Conditions préalables
Avant de commencer cet article, vous devez effectuer les opérations suivantes :

- Installez Visual Studio 2012 ou 2013
- Téléchargez et installez le [Kit de développement logiciel (SDK) Azure .NET](http://azure.microsoft.com/downloads/) 
- Créez un groupe de ressources Azure dans votre abonnement. Voici un exemple de script Azure PowerShell : Pour plus d'informations sur Azure PowerShell, consultez [Installation et configuration d'Azure PowerShell](../install-configurer-powershell.md) ;  


		# Configuration de la session Azure PowerShell pour accéder au Gestionnaire des ressources Azure
		Switch-AzureMode AzureResourceManager

		# Connectez-vous à votre compte Azure.
		Add-AzureAccount

		# Sélectionnez l'abonnement Azure que vous souhaitez utiliser pour créer le groupe de ressources
		Select-AzureSubscription -SubscriptionName <subscription name>

		# Créez un groupe de ressources Azure	
		New-AzureResourceGroup -Name <YOUR RESORUCE GROUP NAME> -Location <LOCATION>


-	Définissez une source d'entrée et la cible de sortie à utiliser. Voir [Prise en main d'Azure Stream Analytics](../flux-analyse-get-started.md) pour configurer un exemple d'entrée et/ou de sortie à utiliser dans cet article.


## Configuration d'un projet

1. Créez une application console Visual Studio .NET C#.
2. Dans la console du Gestionnaire de package, exécutez les commandes suivantes pour installer les packages NuGet. Le premier est le Kit de développement logiciel (SDK) .NET de gestion Azure Stream Analytics. Le second est le client Azure Active Directory utilisé pour l'authentification.

		Install-Package Microsoft.Azure.Management.StreamAnalytics -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Ajoutez la section **appSettings** suivante au fichier App.config :

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION>" />
		  <add key="ActiveDirectoryTenantId" value="<YOU TENANT ID>" />
		</appSettings>


	Remplacez les valeurs de **SubscriptionId** et d'**ActiveDirectoryTenantId** par votre ID d'abonnement et votre ID de locataire Azure. Vous pouvez obtenir ces valeurs en exécutant la cmdlet Azure PowerShell suivante :

		Get-AzureAccount

5. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet :

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Ajoutez une méthode d'aide pour l'authentification :

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
		
		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
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


## Création d'un client de gestion Stream Analytics

Un objet **StreamAnalyticsManagementClient** vous permet de gérer le travail et les composants de travail, comme l'entrée, la sortie et la transformation. 

Ajoutez le code suivant au début de la méthode **Main** : 

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
	
	// Obtention du jeton d'authentification
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Création du client de gestion Stream Analytics
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

La valeur de la variable **resourceGroupName** doit être la même que le nom du groupe de ressources que vous avez créé ou sélectionné lors des étapes préalables.

Les sections suivantes de cet article supposent que ce code se trouve au début de la méthode **Main**.

## Création d'un travail Stream Analytics

Le code suivant crée un travail Stream Analytics sous le groupe de ressources que vous avez défini. Vous ajouterez ultérieurement une entrée, une sortie et une transformation au travail.

	// Création d'un travail Stream Analytics
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Création d'une source d'entrée Stream Analytics

Le code suivant crée une source d'entrée Stream Analytics avec objet blob comme type de source d'entrée et sérialisation CSV. Pour créer une source d'entrée de concentrateur d'événements, utilisez **EventHubStreamInputDataSource** au lieu de **BlobStreamInputDataSource**. De même, vous pouvez personnaliser le type de sérialisation de la source d'entrée.

	// Création d'une source d'entrée Stream Analytics
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Les sources d'entrée sont liées à un travail spécifique. Pour utiliser la même source d'entrée pour différents travaux, vous devez appeler la méthode à nouveau et spécifier un nom de travail différent.


## Test d'une source d'entrée Stream Analytics

La méthode **TestConnection** teste si le travail Stream Analytics est en mesure de se connecter à la source d'entrée, ainsi que d'autres aspects spécifiques au type de source d'entrée. Par exemple, pour la source d'entrée d'objet blob que vous avez créée à l'étape précédente, la méthode vérifie que le nom et la paire de clés du compte de stockage peuvent être utilisés pour se connecter au compte de stockage et que le conteneur spécifié existe.

	// Test de la connexion à la source d'entrée
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Création d'une cible de sortie Stream Analytics

La création d'une cible de sortie est très similaire à la création d'une source d'entrée Stream Analytics. Tout comme les sources d'entrée, les cibles de sortie sont liées à une tâche spécifique. Pour utiliser la même cible de sortie pour différents travaux, vous devez appeler la méthode à nouveau et spécifier un nom de travail différent.

Le code suivant crée une cible de sortie (base de données SQL Azure). Vous pouvez personnaliser le type de données de la cible de sortie et/ou le type de sérialisation.

	// Création d'une cible de sortie Stream Analytics
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Test d'une cible de sortie Stream Analytics

Une cible de sortie Stream Analytics dispose également de la méthode **TestConnection** pour tester les connexions.

	// Test de la connexion de cible de sortie
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Création d'une transformation Stream Analytics

Le code suivant crée une transformation Stream Analytics avec la requête " select * from Input " et spécifie l'allocation d'une unité de diffusion en continu pour le travail Stream Analytics. Pour plus d'informations sur le réglage des unités de diffusion en continu, consultez [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md).


	// Création d'une transformation Stream Analytics
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Tout comme l'entrée et la sortie, une transformation est liée au travail Stream Analytics spécifique où elle a été créée.

## Démarrage d'un travail Stream Analytics
Après avoir créé un travail Stream Analytics et ses entrées, sorties et transformations, vous pouvez démarrer le travail en appelant la méthode **Start**. 

L'exemple de code suivant démarre un travail Stream Analytics avec une heure de début de sortie personnalisée, le 12 décembre 2012, 12:12:12 UTC :

	// Démarrage d'un travail Stream Analytics
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Arrêt d'un travail Stream Analytics
Vous pouvez arrêter un travail Stream Analytics en cours d'exécution en appelant la méthode **Stop**.

	// Arrêt d'un travail Stream Analytics
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Suppression d'un travail Stream Analytics
La méthode **Delete** supprime le travail, ainsi que les ressources sous-jacentes, y compris les entrées, les sorties et la transformation du travail.

	// Suppression d'un travail Stream Analytics
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Étapes suivantes

- [Présentation d'Azure Stream Analytics][stream.analytics.introduction]
- [Prise en main d'Azure Stream Analytics][stream.analytics.get.started]
- [Mise à l'échelle des travaux Azure Stream Analytics][stream.analytics.scale.jobs]
- [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations]
- [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference]
- [Références sur l'API REST de gestion d'Azure Stream Analytics][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


<!--HONumber=52--> 