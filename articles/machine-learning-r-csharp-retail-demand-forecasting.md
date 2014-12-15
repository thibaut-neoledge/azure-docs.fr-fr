<properties title="Forecasting - ETS + STL " pageTitle="Prévisions - ETS + STL  | Azure" description="Forecasting - ETS + STL " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 

#Prévisions - ETS + STL  
 
 



Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/demand_forecast) applique la décomposition des tendances saisonnières (STL) et le modèle de lissage exponentiel (ETS) pour produire des prédictions basées sur les données historiques fournies par l'utilisateur. La demande pour un produit spécifique va-t-elle augmenter cette année ? Puis-je prévoir les ventes de mes produits pour Noël afin de planifier efficacement mon inventaire ? Les modèles de prévision sont en mesure de répondre à ces questions. Ces modèles examinent les tendances cachées et de saison sur la base des données passées afin de prévoir les tendances futures.  
 
>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.  
 
##Utilisation du service web 

Ce service accepte 4 arguments et calcule les prévisions.
Les arguments d'entrée sont les suivants :

* Fréquence : indique la fréquence des données brutes (quotidiennes/hebdomadaires/mensuelles/trimestrielles/annuelles)
* Horizon : durée de validité des prévisions
* Date : ajoute les nouvelles données de série temporelle
* Valeur : ajoute les nouvelles valeurs de données de série temporelle

La sortie du service est constituée des valeurs prévisionnelles calculées.
 
Exemple d'entrée : 

* Fréquence : 12
* Horizon : 12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Valeur : 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx ) un exemple d'application).

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

>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos de présentation concernant la création d'expériences et [la publication de services web](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). La capture d'écran ci-dessous présente l'expérience qui a créé le service web et le code d'exemple de chaque module de l'expérience.

Dans Azure ML, une nouvelle expérience a été créée. Des données d'entrée d'exemple ont été téléchargées avec un schéma de données prédéfini. Un module " Exécuter le Script R ", lié au schéma de données, a généré un modèle de prévision de la décomposition des tendances saisonnières (STL) et de lissage exponentiel (ETS) à l'aide des fonctions " stl ", " ets " et " forecast " à partir de R. 

###Flux de l'expérience :

![Experiment flow][2]

####Module 1 :
 
	# Add in the CSV file with the data in the format shown below 
![Sample data][3]	

####Module 2 :

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
	fit1 <- stl(train_ts,  s.window="periodic")
	train_model <- forecast(fit1, h = data$horizon, method = 'ets')
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

##Limites 

Il s'agit d'un exemple très simple pour les prévisions ETS + STL. Comme le montre l'exemple de code ci-dessus, aucune interception des erreurs n'est implémentée et le service suppose que toutes les variables sont des valeurs continues/positives et que la fréquence doit être un nombre entier supérieur à 1. La longueur des vecteurs de la date et des valeurs doit être identique et la longueur de la série chronologique doit être supérieure à 2 fois la fréquence. La variable de la date doit respecter le format " mm/jj/aaaa ".

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png
