---
title: Utilisation d’un service web de Machine Learning | Microsoft Docs
description: Une fois qu’un service de Machine Learning est déployé, le service web RESTful rendu disponible peut être utilisé soit en tant que service de requête-réponse, soit en tant que service d’exécution de lot.
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: garye

---
# <a name="how-to-consume-an-azure-machine-learning-web-service-that-has-been-deployed-from-a-machine-learning-experiment"></a>Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning
## <a name="introduction"></a>Introduction
Lors de leur déploiement en tant que services web, les expériences Azure Machine Learning proposent une API REST et des messages au format JSON, utilisables par un vaste éventail d’appareils et plateformes. Le portail Microsoft Azure Machine Learning propose du code que vous pouvez utiliser pour appeler le service web, en R, C# et Python. 

Des services peuvent être appelés à l’aide de n’importe quel langage de programmation et à partir de tout appareil remplissant les trois critères suivants :

* Il présente une connexion réseau.
* Il présente des fonctions SSL prévues pour exécuter les demandes HTTPS.
* Il peut analyser du code JSON (manuellement ou à l’aide de bibliothèques d’assistance)

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Un service web Microsoft Azure Machine Learning peut être utilisé de deux manières : en tant que service de requête-réponse ou en tant que service d’exécution de lot. Dans chaque scénario, la fonctionnalité est fournie par le biais du service web RESTful rendu disponible pour être utilisé dès que vous déployez l’expérience.

> [!TIP]
> Pour un moyen simple de créer une application web pour accéder à votre service web prédictif, voir [Utilisation d’un service web Microsoft Azure Machine Learning avec un modèle d’application web](machine-learning-consume-web-service-with-web-app-template.md).
> 
> 

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

Pour plus d’informations sur la création et le déploiement d’un service web Azure Machine Learning, voir [Déploiement d’un service web Azure Machine Learning][publish]. Pour une procédure pas à pas de création d’une expérience Machine Learning et son déploiement, voir [Développement d’une solution prédictive avec Azure Machine Learning][walkthrough].

## <a name="requestresponse-service-rrs"></a>Service de requête-réponse (RRS, Request-Response Service)
Un service de requête-réponse (RRS) est un service web hautement évolutif, qui présente une latence faible et propose une interface pour les modèles sans état qui ont été créés et déployés à partir d’une expérience Azure Machine Learning Studio. Il permet de mettre en place des scénarios où l’application attend une réponse en temps réel.

RRS accepte une seule ligne ou plusieurs lignes de paramètres d'entrée et produit une seule ligne ou plusieurs lignes en sortie. La ou les ligne(s) obtenue(s) en sortie peuvent contenir plusieurs colonnes.

La fonction de validation de l’authenticité d’une application est un exemple de RSS. Des centaines, voire des millions d’installations d’une application sont alors attendues. Lorsque l’application démarre, elle appelle le service RRS avec l’entrée pertinente. L’application reçoit une réponse de validation du service du service, qui autorise ou bloque l’exécution de l’application.

## <a name="batch-execution-service-bes"></a>Service d'exécution de lots (BES, Batch Execution Service)
Un service d’exécution de lots (BES) est un service qui gère la notation asynchrone, portant sur des volumes élevés, d’un lot d’enregistrements de données. L’entrée du service BES contient un lot d’enregistrements provenant de diverses sources, telles que des objets blob, des tables dans Microsoft Azure, SQL Azure, HDInsight (résultats d’une requête Hive, par exemple) et des sources HTTP. La sortie du service BES contient les résultats de la notation. Les résultats sont envoyés vers un fichier dans Azure Blob Storage tandis que les données du point de terminaison de stockage sont renvoyées dans la réponse.

Un service BES peut être utile lorsque les réponses ne sont pas nécessaires immédiatement, comme dans le cas de la notation prévue à intervalles réguliers pour les individus ou les périphériques IoT (Internet of Things).

## <a name="examples"></a>Exemples
Pour savoir comment fonctionnent les services RRS et BES, nous utilisons un exemple de service web Microsoft Azure. Ce service est utilisé dans un scénario IoT (Internet of Things). Pour faire simple, notre périphérique n'envoie qu'une seule valeur, `cog_speed`, et récupère une réponse unique.

Une fois l’expérience déployée, nous avons besoin de quatre éléments d’informations pour appeler le service BES ou RRS.

* la **clé API** ou **clé primaire** du service ;
* l’**URI de demande** du service ;
* le **corps** et les **en-têtes de demande** d’API attendus ;
* le **corps** et les **en-têtes de réponse** d’API attendus.

La manière dont vous trouvez ces informations dépend du type de service que vous avez déployé : un nouveau service web ou un service web classique.

### <a name="information-location-in-the-azure-machine-learning-web-services-portal"></a>Emplacement des informations dans le portail des services web Azure Machine Learning
Pour trouver les informations nécessaires :

1. Connectez-vous au portail des [services web Azure Machine Learning][webservicesportal].
2. Cliquez sur **Services web** ou **Services web classiques**.
3. Cliquez sur le service web que vous utilisez. 
4. Si vous utilisez un service web classique, cliquez sur le point de terminaison que vous utilisez.

Les informations figurent sur les pages suivantes :

* La **clé primaire** figure dans la page **Utiliser**.
* L’**URI de demande** figure dans la page **Utiliser**. 
* Les **en-têtes de demande**, **en-têtes de réponse** et **corps** d’API attendus sont disponibles dans la page **API Swagger**.

### <a name="information-locations-in-machine-learning-studio-classic-web-service-only"></a>Emplacements des informations dans Machine Learning Studio (service web classique uniquement)
Vous pouvez trouver les informations nécessaires à partir de deux emplacements : Machine Learning Studio ou le portail des services web Azure Machine Learning.

Pour trouver les informations nécessaires dans Machine Learning Studio :

1. Connectez-vous à [Machine Learning Studio][mlstudio].
2. À gauche de l’écran, cliquez sur **SERVICES WEB**.
3. Cliquez sur le service web que vous utilisez. 

Les informations figurent sur les pages suivantes :

* La **Clé API** est disponible sur le **Tableau de bord** du service. 
* L’**URI de demande** est disponible dans la page d’aide de l’API.
* Les **en-têtes de demande**, **en-têtes de réponse** et **corps** d’API attendus sont disponibles dans la page d’aide de l’API.

Pour accéder à la page d’aide de l’API, cliquez sur le lien **DEMANDE-RÉPONSE** ou **EXÉCUTION DE LOT** en fonction de ce qui convient pour votre tâche.

Pour trouver les informations nécessaires sur le portail des services web Azure Machine Learning :

1. Connectez-vous au portail des [services web Azure Machine Learning][webservicesportal].
2. Cliquez sur **Services web classiques**.
3. Cliquez sur le service web que vous utilisez. 
4. Cliquez sur le point de terminaison que vous utilisez.

Les informations figurent sur les pages suivantes :

* La **clé primaire** figure dans la page **Utiliser**.
* L’**URI de demande** figure dans la page **Utiliser**. 
* Les **en-têtes de demande**, **en-têtes de réponse** et **corps** d’API attendus sont disponibles dans la page **API Swagger**.

Dans les deux exemples ci-dessous, nous utilisons le langage C# pour illustrer le code requis.

### <a name="rrs-example"></a>Exemple de service RRS
L’exemple de demande suivant montre l’API entrer la charge utile pour l’appel d’API de notre exemple de service. Pour un service web classique, vous pouvez trouver des exemples de charge utile dans la **page d’aide de l’API** ou dans la page **API Swagger** du portail des services web Machine Learning. Pour un nouveau service web, vous pouvez trouver des exemples de charge utile dans la **API Swagger** du portail des services web Machine Learning.

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


De même, l’exemple suivant montre la réponse de l’API pour le service.

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

Voici l’exemple de code pour l’implémentation en C#. Pour un service web classique, vous pouvez trouver des exemples de code en bas de la **page d’aide de l’API** ou en bas de la page **Utiliser**. Pour un nouveau service web, vous pouvez trouver des exemples de code en bas de la **Utiliser**.

**Exemple de code en C#**

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

                    const string apiKey = "abc123"; // Replace this with the API key for the Web service
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

**Exemple de code en Java**

L’exemple de code suivant montre comment construire une demande d’API REST dans Java. Il suppose que les variables (apikey et apiurl) comprennent les détails nécessaires sur l’API, et que la variable jsonBody comprend un objet JSON correct tel qu’attendu par l’API REST. Vous pouvez télécharger le code complet à partir de github à l’adresse [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp). Cet exemple Java requiert la [bibliothèque du client http apache](https://hc.apache.org/downloads.cgi).

    /**
     * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
     */
        /**
          * Call REST API for retrieving prediction from Azure ML 
          * @return response from the REST API
          */    
        public static String rrsHttpPost() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                    // create HttpPost and HttpClient object
                    post = new HttpPost(apiurl);
                    client = HttpClientBuilder.create().build();

                    // setup output message by copying JSON body into 
                    // apache StringEntity object along with content type
                    entity = new StringEntity(jsonBody, HTTP.UTF_8);
                    entity.setContentEncoding(HTTP.UTF_8);
                    entity.setContentType("text/json");

                    // add HTTP headers
                    post.setHeader("Accept", "text/json");
                    post.setHeader("Accept-Charset", "UTF-8");

                    // set Authorization header based on the API key
                    post.setHeader("Authorization", ("Bearer "+apikey));
                    post.setEntity(entity);

                    // Call REST API and retrieve response content
                    HttpResponse authResponse = client.execute(post);

                    return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                    return e.toString();
            }

        }




### <a name="bes-example"></a>Exemple de service BES
Contrairement au service RRS, le service BES est asynchrone. Cela signifie que l’API BES met simplement en file d'attente un travail à exécuter, et l’appelant interroge l’état du travail pour voir quand il s’est terminé. Voici les opérations actuellement pris en charge pour les traitements par lots :

1. Créer (envoyer) un traitement par lots
2. Lancer ce traitement par lots
3. Obtenir l’état ou le résultat du traitement par lots
4. Annuler un traitement par lots en cours d'exécution

**1. Créer un travail d’exécution de lots**

Quand vous créez un traitement par lots pour votre service Microsoft Azure Machine Learning, vous pouvez spécifier plusieurs paramètres qui définissent l’exécution de lot :

* **Input** : représente une référence d’objet blob où l’entrée du traitement par lots est stockée.
* **GlobalParameters** : représente l’ensemble des paramètres globaux que vous pouvez définir pour l’expérience. Une expérience Microsoft Azure Machine Learning peut avoir des paramètres obligatoires et facultatifs qui personnalisent l’exécution du service, et l’appelant est censé fournir tous les paramètres requis, le cas échéant. Ces paramètres sont spécifiés comme une collection de paires clé-valeur.
* **Sorties** : si le service a défini une ou plusieurs sorties, l’appelant peut les rediriger vers un emplacement d’objet blob Azure. En définissant ce paramètre, vous pouvez enregistrer la sortie du service dans un emplacement préféré et sous un nom prévisible. Autrement, le nom d’objet blob de sortie est généré de façon aléatoire. 
  
    Le service s’attend à ce que le contenu de la sortie, selon son type, soit enregistré dans les formats pris en charge :
  
  * Sorties de jeu de données : peuvent être enregistrées en tant que **.csv, .tsv, .arff**
  * Sorties de modèle formé : doivent être enregistrées en tant que **.ilearner**
    
    Vous spécifiez les remplacements d’emplacement de sortie en tant que collection de paires de noms de sortie ou de références d’objets blob. Le *nom de sortie* est le nom défini par l’utilisateur pour un nœud de sortie spécifique, et la *référence d’objet blob* est une référence à un emplacement d’objet blob Azure vers lequel la sortie est redirigée. Le *nom de sortie* est affiché sur la page d’aide de l’API du service.

Tous les paramètres de création de travail sont facultatifs en fonction de la nature de votre service. Par exemple, les services sans nœud d’entrée défini n’exigent pas la transmission d’un paramètre *Input* . De même, la fonctionnalité de remplacement d’emplacement de sortie est facultative car, autrement, les sorties sont stockées dans le compte de stockage par défaut qui a été configuré pour votre espace de travail Microsoft Azure Machine Learning. L’exemple de demande suivant présente les charges utiles pour un service où seules les informations d’entrée sont fournies :

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

La réponse à l’API de création de traitement par lots est l’ID de travail unique associé à votre travail. Cet ID est important, car il constitue pour vous le seul moyen de référencer ce travail dans le système pour d’autres opérations.  

**Exemple de réponse**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Démarrer une tâche d’exécution de lots**

Quand vous créez un traitement par lots, celui-ci est enregistré dans le système et placé dans un état *Not started* (Non démarré). Pour planifier réellement l’exécution du travail, vous appelez l’API **start** décrite dans la page d’aide de l’API du point de terminaison de service ou la page de l’API Swagger du service web, et fournissez l’ID du travail obtenu à la création de celui-ci.

**3. Obtenir l’état d’une tâche d’exécution de lots**

Vous pouvez interroger l’état de votre traitement par lots asynchrone à tout moment en transmettant l’ID du travail à l’API *GetJobStatus*. La réponse de l’API contient un indicateur de l’état actuel du travail, ainsi que les résultats du traitement par lots s’il s’est terminé correctement. En cas d’erreur, d’autres d’informations sur les raisons réelles de l’échec sont retournées dans la propriété *Details*, comme illustré ici :

**Charge utile de réponse**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*StatusCode* peut avoir l'une des valeurs suivantes :

* Not started
* Exécution
* Échec
* Canceled
* Finished

La propriété *Results* est spécifiée uniquement si le travail s’est terminé correctement (sinon, elle a la valeur **null**). Une fois le travail terminé, si le service a au moins un nœud de sortie défini, les résultats sont retournés en tant que une collection de paires *[nom de sortie, référence d’objet blob]*, où la référence d’objet blob est une référence SAP en lecture seule à l’objet blob contenant le résultat.

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

Vous pouvez annuler à tout moment un traitement par lots en cours d’exécution en appelant l’API *CancelJob* désignée et en transmettant l’ID du travail. Vous pouvez annuler pour diverses raisons, par exemple, si l’exécution du travail prend trop de temps.

#### <a name="using-the-bes-sdk"></a>Utilisation du Kit de développement logiciel (SDK) BES
Le [package NuGet du Kit de développement logiciel BES](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) fournit des fonctions qui permettent d’appeler facilement BES pour la notation en mode par lot. Pour installer le package NuGet, dans Visual Studio, dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis cliquez sur **Console du gestionnaire de package**.

Les expériences Azure Machine Learning déployées en tant que services web peuvent inclure des modules d’entrée de service web. Cela signifie que l’entrée dans le service web est fournie par le biais d’un appel de service web sous la forme d’une référence à un emplacement d’objet blob. Il est également possible de ne pas utiliser de module d’entrée de service web et d’utiliser plutôt un module **Importer des données**. Dans ce cas, le module **Importer des données** lit à partir d’une source de données telle qu’une base de données SQL en utilisant une requête los de l’exécution. Les paramètres de service web peuvent être utilisés pour pointer dynamiquement vers d’autres serveurs ou tables, etc. Le Kit de développement logiciel (SDK) prend en charge ces deux modèles.

L’exemple de code suivant montre comment soumettre et surveiller un traitement par lots sur un service Microsoft Azure Machine Learning à l’aide du Kit de développement logiciel BES. Les commentaires contiennent des détails sur les paramètres et les appels.

#### <a name="sample-code"></a>**Exemple de Code**
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
                // First collect and fill in the URI and access key for your Web service endpoint.
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

#### <a name="sample-code-in-java-for-bes"></a>Exemple de code en Java pour BES
L’API REST BES (Batch Execution Service) traite le code JSON composé d’une référence à un exemple de fichier csv d’entrée et d’une référence à un exemple de fichier csv de sortie comme indiqué dans l’exemple suivant, et crée un travail dans Azure ML pour effectuer les prédictions par lot. Vous pouvez consulter le code complet dans [GitHub](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp). Cet exemple Java requiert la [bibliothèque du client http apache](https://hc.apache.org/downloads.cgi). 

    { "GlobalParameters": {}, 
        "Inputs": { "input1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=Q8kkieg==", 
            "RelativeLocation": "myContainer/sampleinput.csv" } }, 
        "Outputs": { "output1": { "ConnectionString":   "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
            "RelativeLocation": "myContainer/sampleoutput.csv" } } 
    } 


##### <a name="create-a-bes-job"></a>Créer un travail BES
        /**
         * Call REST API to create a job to Azure ML 
         * for batch predictions
         * @return response from the REST API
         */ 
        public static String besCreateJob() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(apiurl);
                client = HttpClientBuilder.create().build();

                // setup output message by copying JSON body into 
                // apache StringEntity object along with content type
                entity = new StringEntity(jsonBody, HTTP.UTF_8);
                entity.setContentEncoding(HTTP.UTF_8);
                entity.setContentType("text/json");

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                // note a space after the word "Bearer " - don't miss that
                post.setHeader("Authorization", ("Bearer "+apikey));
                post.setEntity(entity);

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll("\"", "");


                return jobId;

            }
            catch (Exception e) {

                return e.toString();
            }

        }

##### <a name="start-a-previously-created-bes-job"></a>Démarrer un travail BES créé précédemment
        /**
         * Call REST API for starting prediction job previously submitted 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */ 
        public static String besStartJob(String job){
            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }

                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }
##### <a name="cancel-a-previously-created-bes-job"></a>Annuler un travail BES créé précédemment
        /**
         * Call REST API for canceling the batch job 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */ 
        public static String besCancelJob(String job) {
            HttpDelete post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpDelete(startJobUrl+job);
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }
                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }

### <a name="other-programming-environments"></a>Autres environnements de programmation
Vous pouvez également générer le code dans de nombreux autres langages en suivant les instructions du site [swagger.io](http://swagger.io/) . Pour un service web classique, vous pouvez obtenir le document Swagger :

* a partir de la page d’aide de l’API ; 
* en appelant la commande Obtenir le document d’API pour le point de terminaison, disponible sur la page de l’API Swagger du portail des services web Machine Learning. 

Accédez à [swagger.io](http://swagger.io/swagger-codegen/), puis suivez les instructions pour télécharger le code Swagger, Java et Apache mvn. 

La liste suivante résume les instructions de configuration de Swagger pour d’autres environnements de programmation.

* Vérifiez que Java 7 ou version ultérieure est installé.
* Installez Apache mvn (sous Ubuntu, vous pouvez utiliser *apt-get install mvn*).
* Accédez à la page Swagger sur Github et téléchargez le projet Swagger en tant que fichier zip.
* Décompressez Swagger.
* Générez les outils Swagger en exécutant *mvn package* à partir du répertoire source de Swagger.

Vous pouvez à présent utiliser les outils Swagger. Voici les instructions pour générer le code client Java. 

* Accédez à la page d’aide de l’API Azure ML (exemple [ici](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs)).
* Recherchez l’URL du code swagger.json pour les API REST d’Azure ML (avant dernier point au début de la page d’aide de l’API)
* Cliquez sur le lien du document Swagger (exemple [ici](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)).
* Utilisez la commande suivante, comme l’indique le [fichier lisezmoi de Swagger](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) , pour générer le code client.

**Exemple de ligne de commande pour générer le code client**

    java -jar swagger-codegen-cli.jar generate\
     -i https://ussouthcentral.services.azureml.net:443/workspaces/\
    fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
     -l java -o /home/username/sample

* Combinez les valeurs des champs host, basePath et /swagger.json de l’exemple de [page d’aide d’API](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument) Swagger ci-dessous pour construire l’URL Swagger utilisée dans la ligne de commande précédente.

**Exemple de page d’aide de l’API**

    {
      "swagger": "2.0",
      "info": {
        "version": "2.0",
        "title": "Sample 5: Binary Classification with Web service: Adult Dataset [Predictive Exp.]",
        "description": "No description provided for this Web service.",
        "x-endpoint-name": "default"
      },
      "host": "ussouthcentral.services.azureml.net:443",
      "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
      "schemes": [
        "https"
      ],
      "consumes": [
        "application/json"
      ],
      "produces": [
        "application/json"
      ],
      "paths": {
        "/swagger.json": {
          "get": {
            "summary": "Get swagger API document for the Web service",
            "operationId": "getSwaggerDocument",

<!-- Relative Links -->

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- External Links -->
[webservicesportal]: https://services.azureml.net/
[mlstudio]: https://studio.azureml.net


<!--HONumber=Oct16_HO2-->


