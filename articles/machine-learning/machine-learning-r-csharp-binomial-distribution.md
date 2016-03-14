<properties 
	pageTitle="Suite de distribution binomiale | Microsoft Azure" 
	description="Suite de distribution binomiale" 
	services="machine-learning" 
	documentationCenter="" 
	authors="ireiter" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2016" 
	ms.author="ireiter"/>


#Suite de distribution binomiale




La suite de distribution binomiale correspond à un ensemble d’exemples de services web ([Générateur binomial](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Calculatrice de probabilité](https://datamarket.azure.com/dataset/aml_labs/bdp4), [Calculatrice de quantile](https://datamarket.azure.com/dataset/aml_labs/bdq5)) qui facilite la génération et la gestion des distributions binomiales. Les services permettent de générer une séquence de distribution binomiale de n'importe quelle longueur, de calculer les quantiles à partir d'une probabilité donnée et de calculer la probabilité à partir d'un quantile donné. Chacun des services émet des résultats différents selon le service sélectionné (voir la description ci-dessous). La suite de distribution binomiale repose sur les fonctions R qbinom, rbinom et pbinom qui sont incluses dans le package de statistiques R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Les utilisateurs peuvent potentiellement accéder à ces services web directement via Marketplace ou par le biais d’une application mobile, d’un site web ou même d’un ordinateur local, par exemple. Mais l’objectif du service web est également de servir d’exemple d’utilisation d’Azure Machine Learning pour créer des services web avec le code R. Avec seulement quelques lignes de code R et quelques clics dans Azure Machine Learning Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que des utilisateurs et des appareils du monde entier puissent y accéder (aucune configuration de l’infrastructure par le créateur du service web n’est requise).

##Utilisation du service web
La suite de distribution binomiale inclut les 3 services suivants.

###Calculatrice de quantile pour la distribution binomiale
Ce service accepte 4 arguments d'une distribution normale et calcule le quantile associé. Les arguments d'entrée sont les suivants :

- p : probabilité unique agrégée de plusieurs essais  
- size : nombre d’essais
- prob : probabilité de réussite d’un essai
- side : L pour la partie inférieure de la distribution, U pour la partie supérieure de la distribution 

La sortie du service correspond au quantile calculé qui est associé à la probabilité donnée.

###Calculatrice de probabilité de distribution binomiale
Ce service accepte 4 arguments d'une distribution binomiale et calcule la probabilité associée. Les arguments d'entrée sont les suivants :

- q : quantile unique d’un événement avec une distribution binomiale 
- size : nombre d’essais
- prob : probabilité de réussite d’un essai
- side : L pour la partie inférieure de la distribution, U pour la partie supérieure de la distribution ou E qui est égal à un nombre unique de succès

La sortie du service correspond à la probabilité calculée qui est associée au quantile donné.

###Générateur de distribution binomiale
Ce service accepte 3 arguments d'une distribution binomiale et génère une séquence aléatoire de nombres qui sont distribués selon une loi binomiale. Les arguments suivants doivent lui être fournis au sein de la demande :

- n : nombre d’observations 
- size : nombre d’essais
- prob : probabilité de réussite

La sortie du service est une séquence de longueur n avec une distribution binomiale basée sur les arguments size et prob.

>Étant hébergé sur Azure Marketplace, ce service est un service OData. Il peut être appelé à l’aide des méthodes POST ou GET.

Il existe plusieurs façons d’utiliser le service de manière automatique (exemple d’applications : [Générateur](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Calculatrice de probabilité](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Calculatrice de quantile](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)).

###Début du code C# pour l'utilisation du service web :

###Calculatrice de quantile pour la distribution binomiale
	public class Input
	{
	        public string p;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
    void main()
	{
	        var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###Calculatrice de probabilité de distribution binomiale
	public class Input
	{
	        public string q;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}


###Générateur de distribution binomiale
	public class Input
	{
	        public string n;
	        public string size;
	        public string p;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}





##Création du service web 

>Ce service web a été créé à l’aide d’Azure Machine Learning. Pour un essai gratuit, ainsi que des vidéos de présentation relatives à la création d’expériences et à la [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez la page [azure.com/ml](http://azure.com/ml). Voici une capture d'écran de l'expérience qui a créé le service web et l'exemple de code pour chacun des modules dans l'expérience.

###Calculatrice de quantile pour la distribution binomiale

![Create workspace][4]

####Module 1 :
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####Module 2 :

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###Calculatrice de probabilité de distribution binomiale

![Create workspace][5]

####Module 1 :

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####Module 2 :
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###Générateur de distribution binomiale

![Create workspace][6]

####Module 1 :

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####Module 2 :
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##Limitations 
Il s’agit d’exemples très simples en rapport avec la distribution binomiale. Comme illustré dans l'exemple de code ci-dessus, l'interception des erreurs est mise œuvre de façon limitée.

##Forum Aux Questions
Pour les questions fréquemment posées relatives à l’utilisation du service web ou à la publication sur Azure Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 

<!---HONumber=AcomDC_0302_2016-->