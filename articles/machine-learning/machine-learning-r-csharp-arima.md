<properties 
	pageTitle="Prévisions - Modèle ARIMA (Autoregressive Integrated Moving Average, moyenne mobile intégrée auto-régressive) | Microsoft Azure" 
	description="Prévisions - Modèle ARIMA (Autoregressive Integrated Moving Average, moyenne mobile intégrée auto-régressive)" 
	services="machine-learning" 
	documentationCenter="" 
	authors="yijichen" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/02/2015" 
	ms.author="yijichen"/>

 
#Prévisions - Modèle ARIMA (Autoregressive Integrated Moving Average, moyenne mobile intégrée auto-régressive)

Ce [service](https://datamarket.azure.com/dataset/aml_labs/arima) met en œuvre le modèle ARIMA (Autoregressive Integrated Moving Average, moyenne mobile intégrée auto-régressive) pour produire des prédictions basées sur les données d'historique fournies par l'utilisateur. La demande pour un produit spécifique va-t-elle augmenter cette année ? Puis-je prévoir les ventes de mes produits pour Noël afin de planifier efficacement mon inventaire ? Les modèles de prévision sont en mesure de répondre à ces questions. Ces modèles examinent les tendances cachées et de saison sur la base des données passées afin de prévoir les tendances futures.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Les utilisateurs peuvent potentiellement accéder à ce service web par le biais d’une application mobile, d’un site web ou même d’un ordinateur local, par exemple. Mais l’objectif du service web est également de servir d’exemple d’utilisation d’Azure Machine Learning pour créer des services web avec le code R. Avec seulement quelques lignes de code R et quelques clics dans Azure Machine Learning Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les appareils du monde entier l’utilisent sans que l’auteur du service web n’ait à configurer l’infrastructure.

##Utilisation du service web 

Ce service accepte 4 arguments et calcule les prévisions ARIMA. Les arguments d'entrée sont les suivants :

* Frequency : indique la fréquence des données brutes (quotidiennes/hebdomadaires/mensuelles/trimestrielles/annuelles)
* Horizon : durée de validité des prévisions
* Date : ajoute les nouvelles données de série temporelle
* Value : ajoute les nouvelles valeurs de données de série temporelle

La sortie du service est constituée des valeurs prévisionnelles calculées.

Exemple d'entrée :

* Frequency : 12
* Horizon : 12
* Date : 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value : 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>Étant hébergé sur Azure Marketplace, ce service est un service OData. Il peut être appelé à l’aide des méthodes POST ou GET.

Il existe plusieurs façons d’utiliser le service de manière automatique (un exemple d’application est disponible [ici](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx)).

###Début du code C# pour l'utilisation du service web :

	public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

       
	void Main()
	{
  		var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
		var json = JsonConvert.SerializeObject(input);
		var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	       
	  	var httpClient = new HttpClient();
	   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
	   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
	  	var result = query.Result.Content;
	  	var scoreResult = result.ReadAsStringAsync().Result;
  	}

##Création du service web 

>Ce service web a été créé à l’aide d’Azure Machine Learning. Pour un essai gratuit, ainsi que des vidéos de présentation relatives à la création d’expériences et à la [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez la page [azure.com/ml](http://azure.com/ml). Voici une capture d'écran de l'expérience qui a créé le service web et l'exemple de code pour chacun des modules dans l'expérience.

Dans Azure Machine Learning, une nouvelle expérience vide a été créée. Des exemples de données d’entrée ont été téléchargés avec un schéma de données prédéfini. Le module [Exécuter le script R][execute-r-script], qui est lié au schéma de données, génère le modèle de prévision ARIMA à l'aide des fonctions « auto.arima » et « forecast » de R.

###Flux de l’expérience :

![Create workspace][2]

####Module 1 :
 
	# Add in the CSV file with the data in the format shown below 
![Create workspace][3]

####Module 2 :
	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- auto.arima(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forecasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");


##Limitations 

Il s'agit d'un exemple très simple destiné aux prévisions ARIMA. Comme illustré dans l’exemple de code ci-dessus, aucune interception des erreurs n’est implémentée et le service suppose que toutes les variables sont des valeurs continues/positives et que la fréquence doit être un entier supérieur à 1. La longueur des vecteurs de date et de valeur doit être identique. La variable de la date doit respecter le format « mm/jj/aaaa ».

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=Sept15_HO2-->