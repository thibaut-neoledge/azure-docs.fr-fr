<properties title="Normal Distribution Web Service Suite" pageTitle="Suite de services web de distribution normale | Azure" description="Suite de services web de distribution normale" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 

#Suite de services web de distribution normale



La suite de distribution normale correspond à un ensemble d'exemples de services web ([Générateur]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Calculatrice de quantile]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculatrice de probabilité]( https://datamarket.azure.com/dataset/aml_labs/ndp5)), spécifiquement trois services qui facilitent la génération et la gestion de distributions normales. Les services permettent de générer une séquence de distribution normale de n'importe quelle longueur, de calculer les quantiles à partir d'une probabilité donnée et de calculer la probabilité à partir d'un quantile donné.  Chacun des services émet des résultats différents selon le service sélectionné (voir la description ci-dessous). La suite de distribution normale repose sur les fonctions R qnorm, rnorm and pnorm qui sont incluses dans le package de statistiques R.

>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.  
 

##Utilisation du service web
La suite de distribution normale inclut les 3 services suivants :

###Calculatrice de quantile pour la distribution normale :
Ce service accepte 4 arguments d'une distribution normale et calcule le quantile associé.

Les arguments d'entrée sont les suivants :

* p : une probabilité unique d'un événement avec distribution normale 
* Mean : moyenne de la distribution normale
* SD : écart type de la distribution normale 
* Side : L pour la partie inférieure de la distribution et U pour la partie supérieure de la distribution

La sortie du service correspond au quantile calculé qui est associé à la probabilité donnée.

###Calculatrice de probabilité de distribution normale :
Ce service accepte 4 arguments d'une distribution normale et calcule la probabilité associée.

Les arguments d'entrée sont les suivants :

* q : quantile unique d'un événement avec une distribution normale 
* Mean : moyenne de la distribution normale
* SD : écart type de la distribution normale 
* Side : L pour la partie inférieure de la distribution et U pour la partie supérieure de la distribution

La sortie du service correspond à la probabilité calculée qui est associée au quantile donné.

###Générateur de distribution normale
Ce service accepte 3 arguments d'une distribution normale et génère une séquence aléatoire de nombres qui sont distribués normalement. 
Les arguments suivants doivent lui être fournis au sein de la demande :

* n : nombre d'observations 
* mean : moyenne de la distribution normale
* sd : écart type de la distribution normale 

La sortie du service est une séquence de longueur n avec une distribution normale basée sur les arguments mean et sd.

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs manières de consommer le service de manière automatique (exemple d'applications : [Générateur](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx),
[Calculatrice de probabilité](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx),
[Calculatrice quantile](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###Début du code C# pour l'utilisation du service web :

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##Création du service web 
>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos d'introduction sur la création d'expériences et la [publication de services web](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). 

Voici une capture d'écran de l'expérience qui a créé le service web et l'exemple de code pour chacun des modules dans l'expérience.

###Calculatrice de quantile pour la distribution normale :
Flux d'expérience
![Experiment flow][2]
 
	#data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Calculatrice de probabilité de distribution normale :
Flux d'expérience
![Experiment flow][3]
 
 	#data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Générateur de distribution normale :
Flux d'expérience
![Experiment flow][4]

	#data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##Limites 

Il s'agit d'exemples très simples en rapport avec la distribution normale. Comme illustré dans l'exemple de code ci-dessus, l'interception des erreurs est mise œuvre de façon limitée.

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
