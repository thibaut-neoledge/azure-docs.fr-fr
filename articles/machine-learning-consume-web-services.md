<properties 
	pageTitle="Utilisation d'un service web Machine Learning publié à partir d'une expérience Machine Learning | Azure" 
	description="Lorsqu'un service Machine Learning (ML) est publié, le service web RESTful qui est mis à disposition peut être exploité en temps que service requête-réponse ou d'exécution de lot." 
	services="machine-learning" 
	solutions="big-data" 
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
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# Utilisation d'un service web Microsoft Azure Machine Learning publié

## Introduction

Lors de leur publication en tant que services web, les expériences Microsoft Azure Machine Learning proposent une API REST, qui peut être exploitée par divers périphériques et plates-formes. En effet, l'API REST, très simple, accepte et répond au moyen de messages présentant le format JSON. Le portail Microsoft Azure Machine Learning propose du code que vous pouvez utiliser pour appeler le service web, en R, C# et Python. Cependant, ces services peuvent être appelés via n'importe quel langage de programmation et depuis n'importe quel périphérique répondant aux exigences suivantes :

* Il présente une connexion réseau.
* Il présente des fonctions SSL prévues pour exécuter les demandes HTTPS.
* Il présente une fonctionnalité d'analyse des données JSON (à la main ou via des bibliothèques de prise en charge).

Cela signifie que les services peuvent être utilisés à partir d'applications web, mobiles ou d'Ordinateur Client, voire à partir de Microsoft Excel !  

Un service web Microsoft Azure Machine Learning peut être utilisé de deux manières différentes : en tant que service de requête-réponse ou comme service d'exécution de lot. Dans chaque scénario, la fonctionnalité est fournie par le biais du service web RESTful, qui peut être utilisé une fois l'expérience publiée. En déployant un service web ML dans Microsoft Azure avec un point de terminaison de service web Microsoft Azure, où le service est automatiquement mis à l'échelle en fonction de l'utilisation, vous pouvez éviter les coûts initiaux et permanents liés aux ressources matérielles.

<!-- Lorsque cet article est publié, corrigez le lien et supprimer les commentaires.
Pour en savoir plus sur la gestion des points de terminaison de service web Microsoft Azure Machine Learning à l'aide de l'API REST, consultez la section **Points de terminaison de service web Microsoft Azure Machine Learning**. 
-->

Pour en savoir plus sur la création et la publication d'un service web Microsoft Azure Machine Learning, 
consultez la section [Publier un service web Microsoft Azure Machine Learning][publish]. Pour obtenir une procédure pas à pas portant sur la création d'une expérience ML et sa publication, consultez la section [Développement d'une solution prédictive avec Azure Machine Learning][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Service de requête-réponse (RRS, Request-Response Service)

Un service de requête-réponse (RRS) est un service web hautement évolutif, qui présente une latence faible et propose une interface pour les modèles sans état qui ont été créés et publiés à partir d'une expérience Microsoft Azure Machine Learning Studio.

RRS accepte une seule ligne de paramètres en entrée et génère une seule ligne en sortie. La ligne obtenue en sortie peut contenir plusieurs colonnes.

La fonction de validation de l'authenticité d'une application est un exemple de RSS. Des centaines, voire des millions d'installations d'une application sont alors attendues. Lorsque l'application démarre, elle effectue un appel au service RRS avec l'entrée pertinente. L'application reçoit une réponse de validation du service du service, qui autorise ou bloque l'exécution de l'application.


## Service d'exécution de lots (BES, Batch Execution Service)
 
Un service d'exécution de lots (BES) est un service qui gère la notation asynchrone, portant sur des volumes élevés, d'un lot d'enregistrements de données. L'entrée du service BES contient un lot d'enregistrements provenant de diverses sources, telles que les objets blob, les tables dans Microsoft Azure, SQL Azure, HDInsight (résultats d'une requête Hive, par exemple) et les sources HTTP. La sortie du service BES contient les résultats de la notation. Les résultats sont envoyés vers un fichier dans le stockage d'objets blob Microsoft Azure ; les données du point de terminaison de stockage sont renvoyées dans la réponse.

Un service BES peut être utile lorsque les réponses ne sont pas nécessaires immédiatement, comme dans le cas de la notation prévue à intervalles réguliers pour les individus ou les périphériques IoT (Internet of Things).

## Exemples
Pour savoir comment les services RRS et BES, nous utilisons un exemple de service web Microsoft Azure. Ce service est utilisé dans un scénario IoT (Internet of Things). Pour faire simple, notre périphérique n'envoie qu'une seule valeur `cog_speed` et récupère une réponse unique. 

Quatre éléments d'information sont nécessaires pour appeler le service BES ou RRS. Ces informations sont accessibles sur les [pages de service Microsoft Azure Machine Learning](https://studio.azureml.net), une fois que l'expérience a été publiée. Cliquez sur le lien des SERVICES WEB, sur la gauche de l'écran, pour faire apparaître les services publiés. Pour trouver des informations sur un service spécifique, vous pouvez accéder à des liens vers des pages d'aide sur les API, pour les services RSS et BES.

1.	La **clé d'API de service** est disponible sur la page principale de services.
2.	L'**URI de service** est disponible sur la page d'aide sur les API pour le service voulu.
3.	Le **corps de demande d'API** attendu est disponible sur la page d'aide de l'API pour le service voulu.
4.	Le **corps de la demande d'API** attendu est disponible sur la page d'aide de l'API pour le service voulu.

Dans les deux exemples ci-dessous, nous utilisons le langage C# pour illustrer le code requis et la plate-forme ciblée est une version de Windows 8 pour ordinateur de bureau. 

### Exemple de service RRS
Sur la page d'aide de l'API, à part l'URI, vous allez saisir en entrée et créer des sorties incluant des définitions et des exemples de code. L'entrée de l'API est appelée pour ce service en particulier et constitue la charge utile de l'appel d'API. 

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


De même, la réponse de l'API est également appelée, pour ce service en particulier.

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

La partie inférieure de la page inclut des exemples de code. Vous trouverez ci-dessous un exemple de code illustrant l'implémentation de C#. 
                   
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

### Exemple de service BES
Sur la page d'aide de l'API, en plus de l'URI, vous trouverez des informations sur plusieurs appels disponibles. Contrairement au service RRS, le service BES est asynchrone. Cela signifie que l'API du service BES met simplement en file d'attente la tâche à exécuter. Elle ne l'exécute pas avant la réception de la réponse de l'API, en réalité. Le service BES permet au développeur d'exécuter trois actions, décrites ci-dessous.

1. Envoi d'une tâche d'exécution de lot
1. Obtention de l'état ou du résultat d'une tâche d'exécution de lot
1. Suppression d'une tâche d'exécution de lot  

**1. Envoi d'une tâche d'exécution de lot**

Vous envoyez une tâche d'exécution de lot en fournissant des informations sur l'emplacement de stockage des données du lot. Pour les besoins de cet exemple, nous supposerons que les enregistrements pour lesquels nous voulons générer une notation par lot se trouvent dans un fichier blob, dans un compte de stockage.

La réponse d'un programme de traitement par lots correspond à un ID de tâche, car, nous l'avons dit, cette tâche est exécutée de manière asynchrone. Nous allons utiliser l'ID de tâche pour obtenir l'état et les résultats de cette tâche, un peu plus tard.

**Exemple de demande**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**Exemple de réponse**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**Exemple de code**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2. Obtention de l'état ou du résultat d'une tâche d'exécution de lot**

Pour obtenir le résultat d'une tâche, vous devez disposer de l'ID de la tâche, qui figurait dans la réponse à l'envoi de cette tâche. Il n'existe aucune entrée réelle sur cet appel d'API. Il utilise un léger changement apporté à l'URI du service BES et la méthode de demande. Au lieu d'une demande POST, il utilise une demande GET suivant l'URI définie sur la page d'aide de l'API.
 
La réponse, cependant, est disposée en couches.

**Charge utile de réponse**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` peut avoir la valeur 0, 1, 2, 3 ou 4 avec la sémantique suivante :

* 0	Non démarrée
* 1	En cours d'exécution
* 2	A échoué
* 3	Annulée
* 4	Terminée

Si la tâche n'est pas terminée, `Result` a la valeur **null**. Si elle est terminée, `Result` a le format suivant : 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

La zone de détails fournit des informations sur l'erreur, le cas échéant.

**Exemple de code**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3. Supprimer une tâche d'exécution de lot**              
Une tâche peut également être supprimée une fois qu'elle a été lancée. Vous pouvez la supprimer pour différentes raisons, par exemple une exécution trop longue. Pour ce faire, vous devez d'abord vous procurer l'ID de la tâche, qui est inclus dans la réponse à l'envoi de la tâche.

Il n'existe aucune entrée réelle sur cet appel d'API. Un léger changement est apporté à l'URI du service BES et à la méthode de demande. Au lieu d'une demande POST, elle utilise une demande DELETE suivant l'URI défini sur la page d'aide de l'API. L'exemple de code associé à cette opération est très simple.


<!--HONumber=49-->