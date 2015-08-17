<properties 
	pageTitle="Utilisation d’un service web Microsoft Azure Machine Learning | Microsoft Azure" 
	description="Lorsqu’un service Machine Learning (ML) est publié, le service web RESTful qui est mis à disposition peut être exploité en temps que service requête-réponse ou d’exécution de lot." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="06/29/2015" 
	ms.author="bradsev" />


# Utilisation d’un service web Microsoft Azure Machine Learning publié à partir d’une expérience Machine Learning

## Introduction

Lors de leur publication en tant que services web, les expériences Microsoft Azure Machine Learning proposent une API REST, qui peut être exploitée par divers périphériques et plates-formes. En effet, l’API REST, très simple, accepte et répond au moyen de messages présentant le format JSON. Le portail Microsoft Azure Machine Learning propose du code que vous pouvez utiliser pour appeler le service web, en R, C# et Python. Cependant, ces services peuvent être appelés via n’importe quel langage de programmation et depuis n’importe quel périphérique répondant aux exigences suivantes :

* Il présente une connexion réseau.
* Il présente des fonctions SSL prévues pour exécuter les demandes HTTPS.
* Il présente une fonctionnalité d’analyse des données JSON (à la main ou via des bibliothèques de prise en charge).

Cela signifie que les services peuvent être utilisés à partir d’applications web, d’applications mobiles ou d’applications d’ordinateur client, voire à partir de Microsoft Excel.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Un service web Microsoft Azure Machine Learning peut être utilisé de deux manières différentes : en tant que service de requête-réponse ou comme service d’exécution de lot. Dans chaque scénario, la fonctionnalité est fournie par le biais du service web RESTful, qui peut être utilisé une fois l’expérience publiée. En déployant un service web ML dans Microsoft Azure avec un point de terminaison de service web Microsoft Azure, où le service est automatiquement mis à l’échelle en fonction de l’utilisation, vous pouvez éviter les coûts initiaux et permanents liés aux ressources matérielles.

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**. 
-->

Pour plus d'informations sur la création et la publication d’un service web Microsoft Azure Machine Learning, consultez la page [Publication d’un service web Microsoft Azure Machine Learning][publish]. Pour obtenir une procédure pas à pas portant sur la création d’une expérience Machine Learning et sa publication, consultez la page [Développement d’une solution prédictive avec Azure Machine Learning][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Service de requête-réponse (RRS, Request-Response Service)

Un service de requête-réponse (RRS) est un service web hautement évolutif, qui présente une latence faible et propose une interface pour les modèles sans état qui ont été créés et publiés à partir d’une expérience Microsoft Azure Machine Learning Studio.

RRS accepte une seule ligne de paramètres en entrée et génère une seule ligne en sortie. La ligne obtenue en sortie peut contenir plusieurs colonnes.

La fonction de validation de l’authenticité d’une application est un exemple de RSS. Des centaines, voire des millions d’installations d’une application sont alors attendues. Lorsque l’application démarre, elle effectue un appel au service RRS avec l’entrée pertinente. L’application reçoit une réponse de validation du service du service, qui autorise ou bloque l’exécution de l’application.


## Service d’exécution de lots (BES, Batch Execution Service)
 
Un service d’exécution de lots (BES) est un service qui gère la notation asynchrone, portant sur des volumes élevés, d’un lot d’enregistrements de données. L’entrée du service BES contient un lot d’enregistrements provenant de diverses sources, telles que les objets blob, les tables dans Microsoft Azure, SQL Azure, HDInsight (résultats d’une requête Hive, par exemple) et les sources HTTP. La sortie du service BES contient les résultats de la notation. Les résultats sont envoyés vers un fichier dans le stockage d’objets blob Microsoft Azure ; les données du point de terminaison de stockage sont renvoyées dans la réponse.

Un service BES peut être utile lorsque les réponses ne sont pas nécessaires immédiatement, comme dans le cas de la notation prévue à intervalles réguliers pour les individus ou les périphériques IoT (Internet of Things).

## Exemples
Pour savoir comment les services RRS et BES, nous utilisons un exemple de service web Microsoft Azure. Ce service est utilisé dans un scénario IoT (Internet of Things). Pour faire simple, notre périphérique n’envoie qu’une seule valeur, `cog_speed`, et récupère une réponse unique.

Quatre éléments d’information sont nécessaires pour appeler le service BES ou RRS. Ces informations sont accessibles sur les pages de service dans [Microsoft Azure Machine Learning](https://studio.azureml.net) une fois que l’expérience a été publiée. Cliquez sur le lien des SERVICES WEB, sur la gauche de l’écran, pour faire apparaître les services publiés. Pour trouver des informations sur un service spécifique, vous pouvez accéder à des liens vers des pages d’aide sur les API, pour les services RSS et BES.

1.	La **clé d’API de service** est disponible sur la page principale des services.
2.	L’**URI de service** est disponible sur la page d’aide de l’API pour le service voulu.
3.	Le **corps de demande d’API** attendu est disponible sur la page d’aide de l’API pour le service voulu.
4.	Le **corps de réponse d’API** attendu est disponible sur la page d’aide de l’API pour le service voulu.

Dans les deux exemples ci-dessous, nous utilisons le langage C# pour illustrer le code requis et la plate-forme ciblée est une version de Windows 8 pour ordinateur de bureau.

### Exemple de service RRS
Sur la page d’aide de l’API, à part l’URI, vous allez saisir en entrée et créer des sorties incluant des définitions et des exemples de code. L’entrée de l’API est appelée pour ce service en particulier et constitue la charge utile de l’appel d’API.

**Exemple de demande**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


De même, la réponse de l’API est également appelée, pour ce service en particulier.

**Exemple de réponse**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

La partie inférieure de la page inclut des exemples de code. Vous trouverez ci-dessous un exemple de code illustrant l’implémentation de C#.
                   
**Exemple de code**

	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### Exemple de service BES
Sur la page d’aide de l’API, en plus de l’URI, vous trouverez des informations sur plusieurs appels disponibles. Contrairement au service RRS, le service BES est asynchrone. Cela signifie que l’API BES met simplement en file d'attente un travail à exécuter, et l’appelant interroge l’état du travail pour voir quand il s’est terminé. Voici les opérations actuellement pris en charge pour les traitements par lots :

1. Créer (envoyer) un traitement par lots
1. Lancer ce traitement par lots
1. Obtenir l’état ou le résultat du traitement par lots
1. Annuler un traitement par lots en cours d'exécution

**1. Créer un travail d’exécution de lots**

Lorsque vous créez un traitement par lots pour votre point de terminaison de service Microsoft Azure Machine Learning, vous pouvez spécifier plusieurs paramètres qui définissent l’exécution du traitement par lots :

* **Input** : représente une référence d’objet blob où l’entrée du traitement par lots est stockée.
* **GlobalParameters** : représente l’ensemble des paramètres qu’il est possible de définir pour l’expérience. Une expérience Microsoft Azure Machine Learning peut avoir des paramètres obligatoires et facultatifs qui personnalisent l’exécution du service, et l’appelant est censé fournir tous les paramètres requis, le cas échéant. Ces paramètres sont spécifiés comme une collection de paires clé-valeur.
* **Outputs** : si le service a défini une ou plusieurs sorties, nous autorisons l’appelant à les rediriger vers un emplacement d’objet blob Azure de son choix. Cela vous permettra d’enregistrer la ou les sorties du service dans un nouvel emplacement et sous un nom prévisible ; sinon, le nom d’objet blob de sortie est généré de façon aléatoire. **REMARQUE :** le service s’attend à ce que le contenu de la sortie, selon son type, soit enregistré dans les formats pris en charge :
  - sorties de jeu de données : peuvent être enregistrées en tant que **.csv, .tsv, .arff**
  - sorties de modèle formé : peuvent être enregistrées en tant que **.ilearner**
  
  Les remplacements d’emplacement de sortie sont spécifiés comme une collection de paires *<output name  blob reference>*, où le *nom de sortie* est le nom défini par l'utilisateur pour un nœud de sortie spécifique (également indiqué sur la page d’aide de l’API du service), et la *référence d’objet blob* est une référence à un emplacement d’objet blob Azure vers lequel la sortie doit être redirigée.
  
Tous ces paramètres de création de travail peuvent être facultatifs en fonction de la nature de votre service. Par exemple, les services avec aucun nœud d’entrée défini ne nécessitent pas la transmission d’un paramètre *Input* et la fonctionnalité de remplacement d’emplacement de sortie est entièrement facultative ; sinon, les sorties sont stockées dans le compte de stockage par défaut qui a été configuré pour votre espace de travail Microsoft Azure Machine Learning. Le code ci-dessus présente un exemple de demande de charge utile transmise à l’API REST pour un service où seules les informations d’entrée sont transmises :

**Exemple de demande**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

La réponse à l’API de création de traitement par lots est l'ID de travail unique qui a été associé à votre travail. Ce code est très important, car il fournit le seul moyen pour référencer ce travail dans le système pour d’autres opérations.
  
**Exemple de réponse**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Démarrer une tâche d’exécution de lots**

Lorsque vous créez un traitement par lots, il est simplement enregistré dans le système et placé dans un état *Not started*. Pour planifier réellement le travail pour l’exécution, vous devez appeler l’API **start** décrite sur la page d’aide de l’API du point de terminaison et fournir l’ID de travail obtenu à la création du travail.
  
**3. Obtenir l’état d’une tâche d’exécution de lots**

Vous pouvez interroger l’état de votre traitement par lots asynchrone à tout moment, en transmettant l’ID du travail à l’API GetJobStatus. La réponse de l’API contient un indicateur de l’état actuel du travail, ainsi que les résultats réels du traitement par lots s’il s’est terminé correctement. En cas d’erreur, plus d’informations sur les raisons réelles de l’échec sont retournées dans la propriété *Details*.
 
**Charge utile de réponse**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

*StatusCode* peut avoir l’une des valeurs suivantes :

* Not started
* Exécution
* Échec
* Annulé
* Finished

La propriété *Results* est remplie uniquement si le travail s’est terminé correctement (sinon, elle a la valeur **null**). À l’achèvement du travail et si le service a au moins un nœud de sortie défini, le résultat est retourné comme une collection de paires *[nom de sortie, référence d'objet blob]*, où la référence d’objet blob est une référence SAS en lecture seule à l’objet blob contenant le résultat réel.

**Exemple de réponse**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4. Annuler une tâche d’exécution de lots**

Il est possible d’annuler à tout moment un traitement par lots en cours d'exécution en appelant l’API CancelJob désignée et en transmettant l’ID du travail. Vous pouvez la supprimer pour différentes raisons, par exemple une exécution trop longue.



#### Utilisation du [Kit de développement logiciel BES](machine-learning-consume-web-services.md#batch-execution-service-sdk)

Le [package Nugget du Kit de développement logiciel BES](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) fournit des fonctions qui permettent d’appeler facilement BES pour la notation en mode par lot. Pour installer le package Nuget, dans Visual Studio, accédez à Outils, puis sélectionnez Gestionnaire de package Nuget et cliquez sur Console du gestionnaire de package.

Les expériences AzureML publiées comme services web peuvent inclure des modules d’entrée de service web ; cela signifie qu’elles s’attendent à ce que l’entrée soit fournie via l’appel du service web sous la forme d’une référence à un emplacement d’objet blob. Il est également possible de ne pas utiliser de module d’entrée de service web et d’utiliser plutôt un module Reader. Dans ce cas, le module Reader lit généralement à partir d’une base de données SQL avec une requête au moment de l’exécution pour obtenir les données. Les paramètres de service web peuvent être utilisés pour pointer dynamiquement vers d’autres serveurs ou tables, etc. Le Kit de développement logiciel (SDK) prend en charge ces deux modèles.

L'exemple de code ci-dessous montre comment vous pouvez soumettre et surveiller un traitement par lots sur un point de terminaison de service Microsoft Azure Machine Learning à l’aide du Kit de développement logiciel BES. Notez les commentaires pour plus d’informations sur les paramètres et les appels.

#### **Exemple de Code**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning 
	
	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;
	  
	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }
	
	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";
	
	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);
	
	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results
	
	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might 
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.
	            
	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references 
                    // based on a blob SAS URI
                    
                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");
                              
                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.
                    
                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME", 
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };
	            
	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };
	                
	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };
	
	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);
	
	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();
	
	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

 

<!---HONumber=August15_HO6-->