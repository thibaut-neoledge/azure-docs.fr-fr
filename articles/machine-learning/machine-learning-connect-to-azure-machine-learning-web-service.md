<properties 
	pageTitle="Se connecter à un service web Microsoft Azure Machine Learning | Azure" 
	description="Via C# ou Python, vous pouvez vous connecter à un service web Microsoft Azure Machine Learning, au moyen d'une clé d'autorisation." 
	services="machine-learning" 
	documentationCenter="" 
	authors="derrickv" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="derrickv" />


# Se connecter à un service web Microsoft Azure Machine Learning | Azure 
L'expérience de développeur de Microsoft Azure Machine Learning correspond à une API de service web destinée à effectuer des prévisions à partir des données d'entrée, en temps réel ou en mode de lot. Vous allez utiliser Microsoft Azure Machine Learning Studio (ML Studio) pour créer des prédictions et publier un service web Microsoft Azure ML. 

Pour savoir comment créer et publier un service web Microsoft Azure Machine Learning à l'aide de Studio ML, consultez les documents suivants :

- [Vue d'ensemble du processus Microsoft Azure ML](../machine-learning-overview-of-azure-ml-process.md)
- [Prise en main de Machine Learning Studio](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Vue d'ensemble de Microsoft Azure Machine Learning](https://studio.azureml.net/)
- [Centre de documentation Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

## Service web Microsoft Azure Machine Learning ##

Grâce au service web Microsoft Azure Machine Learning, une application externe peut communiquer avec le modèle de notation de workflow ML et ce, en temps réel. Un appel du service web ML renvoie les résultats d'une prédiction à une application externe. Pour créer cet appel, vous transmettez une clé d'API créée lorsque vous publiez une prédiction. Le service web ML est basé sur l'architecture REST, souvent choisie pour les projets de programmation web.

Microsoft Azure Machine Learning propose deux types de service :

- Service de requête-réponse (Request-Response Service, RSS) : service hautement évolutif, à faible latence, qui constitue une interface pour les modèles sans état créés et publiés à partir de ML Studio.
- Service d'exécution de lot (Batch Execution Service, BES) : service asynchrone qui effectue la notation d'un lot pour les enregistrements de données.

Pour en savoir plus sur les services web Microsoft Azure Machine Learning, consultez la section [Vue d'ensemble du processus Microsoft Azure ML](../machine-learning-overview-of-azure-ml-process.md).

## Obtenir une clé d'autorisation Microsoft Azure Machine Learning ##
Vous pouvez obtenir une clé d'API de service web auprès d'un service web ML. Vous pouvez la demander à Microsoft Azure Machine Learning Studio ou au portail de gestion Microsoft Azure.
### Microsoft Azure Machine Learning Studio ###
1. Dans ML Studio, cliquez sur l'option **SERVICES WEB** figurant sur la gauche.
2. Cliquez sur un service web. La " clé API " figure sur l'onglet **TABLEAU DE BORD**.

### Portail de gestion Microsoft Azure ###

1. Cliquez sur l'option **MACHINE LEARNING** figurant sur la gauche.
2. Cliquez sur un espace de travail.
3. Cliquez sur **SERVICES WEB**.
4. Cliquez sur un service web.
5. Cliquez sur un point de terminaison. La " CLÉ API " se trouve sur la partie inférieure droite de la fenêtre.

## <a id="connect"></a>Se connecter à un service web Microsoft Azure Machine Learning

Vous pouvez vous connecter à un service web Microsoft Azure Machine Learning à l'aide de n'importe quel langage de programmation qui prend en charge les requêtes et réponses HTTP. Vous pouvez consulter des exemples en C#, Python et R sur l'une des pages d'aide relatives aux services web Microsoft Azure ML.

### Pour afficher une page d'aide sur une API de service web Microsoft Azure ML ###
Vous créez ce type de page lorsque vous publiez un service web. Consultez la section [Étape 5 : publication du service web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).


**Pour afficher une page d'aide Microsoft sur une API Microsoft Azure ML API**
Dans Microsoft Azure Machine Learning Studio :

1. Sélectionnez **SERVICES WEB**.
2. Choisissez un service web.
3. Sélectionnez **Page d'aide sur l'API** - **REQUÊTE-RÉPONSE** ou **EXÉCUTION DE LOT**.


**Page d'aide sur l'API Microsoft Azure ML**
La page d'aide sur l'API Microsoft Azure ML contient des détails sur un service web de prédiction, notamment :


<table>
	<tr>
		<td>&nbsp;</td>
		<td>Exemple </td>
	</tr>
	<tr>
		<td>URI de requête POST </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{ID_espace_travail}/services/{ID_service}/score
		</td>
	</tr>
	<tr>
		<td>Exemple de requête </td>
		<td>{ <br/> 
			&nbsp;&nbsp; "Id": "score00001",   <br/>
			&nbsp;&nbsp; "Instance": <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; {  <br/>  
 			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; "FeatureVector": { <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col1": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col2": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col3": "0", <br/>  
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  ...     },   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp;   "GlobalParameters": {}   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; } <br/>
		}</td>
	</tr>
	<tr>
		<td>Corps de réponse </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Nom</B></td>
				<td><B>Type de données</B></td>
			</tr>
	
			<tr>
				<td>Fonctionnalité</td>
				<td>String</td>
			</tr>
			<tr>
				<td>Nombre</td>
				<td>Chiffre</td>
			</tr>
			<tr>
				<td>Valeur unique </td>
				<td>Chiffre </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>Exemple de réponse </td>
		<td>[&quot;Col1&quot;,&quot;1&quot;,&quot;1&quot;,...] </td>
	</tr>
	<tr>
		<td>Exemple de code </td>
		<td>(exemple de code en C#, Python et R) </td>
	</tr>
</table>

**REMARQUE** Les exemples de code proviennent de l'exemple 1 : " Téléchargement d'un jeu de données depuis l'UCI : jeu de données de classe Adult 2 " dans la collection d'exemples Microsoft Azure ML.

### Exemple de code C# ###

Pour vous connecter à un service web Microsoft Azure ML, utilisez un élément **HttpClient** de transmission de ScoreData. ScoreData contient un élément FeatureVector, vecteur à n dimensions  correspondant à des fonctions numériques qui représente l'élément ScoreData. Vous vous authentifiez auprès du service Microsoft Azure ML au moyen d'une clé API.

Pour que vous puissiez vous connecter à un service web ML, le package NuGet **Microsoft.AspNet.WebApi.Client** doit être installé.

**Installer le package NuGet Microsoft.AspNet.WebApi.Client dans Microsoft Visual Studio**

1. Publiez le service web " Téléchargement d'un jeu de données depuis l'UCI : jeu de données de classe Adult 2 ".
2. Click **Tools** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.
2. Choisissez l'élément **Install-Package Microsoft.AspNet.WebApi.Client**.

**Pour exécuter l'exemple de code**

1. Publiez l'expérience " Téléchargement d'un jeu de données depuis l'UCI : jeu de données de classe Adult 2 ", qui fait partie de la collection d'exemple de Microsoft Azure ML.
2. Attribuez l'élément apiKey avec la clé à partir d'un service web. Consultez la section Obtention d'une clé d'autorisation Microsoft Azure ML.
3. Affectez l'élément serviceUri avec l'URI de requête. Consultez la section Obtention d'un URI de requête.

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Exemple de code Python ###

Pour vous connecter à un service web Microsoft Azure ML, utilisez la bibliothèque **urllib2** de transmission de l'élément ScoreData. ScoreData contient un élément FeatureVector, vecteur à n dimensions  correspondant à des fonctions numériques qui représente l'élément ScoreData. Vous vous authentifiez auprès du service Microsoft Azure ML au moyen d'une clé API.


**Pour exécuter l'exemple de code**

1. Publishez l'expérience " Téléchargement d'un jeu de données depuis l'UCI : jeu de données de classe Adult 2 ", qui fait partie de la collection d'exemple de Microsoft Azure ML.
2. Attribuez l'élément apiKey avec la clé à partir d'un service web. Consultez la section Obtention d'une clé d'autorisation Microsoft Azure ML.
3. Affectez l'élément serviceUri avec l'URI de requête. Consultez la section Obtention d'un URI de requête.

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 

<!--HONumber=49--> 