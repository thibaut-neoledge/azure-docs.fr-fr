<properties 
	pageTitle="Prévisions - Lissage exponentiel | Azure" 
	description="Service web : Prévisions - Lissage exponentiel" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#Prévisions - Lissage exponentiel 
  



Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/ets) met en œuvre un modèle de lissage exponentiel (ETS) pour produire des prédictions basées sur les données d'historique fournies par l'utilisateur. La demande pour un produit spécifique va-t-elle augmenter cette année ? Puis-je prévoir les ventes de mes produits pour Noël afin de planifier efficacement mon inventaire ? Les modèles de prévision sont en mesure de répondre à ces questions. Ces modèles examinent les tendances cachées et de saison sur la base des données passées afin de prévoir les tendances futures.  
 
>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.
 
##Utilisation du service web 
 
Ce service accepte 4 arguments et calcule les prévisions ETS.
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

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx) un exemple d'application).

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

>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos de présentation concernant la création d'expériences et [la publication de services web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). La capture d'écran ci-dessous présente l'expérience qui a créé le service web et le code d'exemple de chaque module de l'expérience.

Dans Azure ML, une nouvelle expérience a été créée. Des données d'entrée d'exemple ont été téléchargées avec un schéma de données prédéfini. Le module " Exécuter le script R ", qui est lié au schéma de données, génère le modèle de prévision ARIMA à l'aide des fonctions " ets " et " forecast " de R. 


![Experiment flow][2]

####Module 1 :
 
	# Add in the CSV file with the data in the format shown below 
![Data sample][3]	

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
	fit1 <- ets(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

 
##Limites 

Il s'agit d'un exemple très simple destiné aux prévisions ETS. Comme illustré dans l'exemple de code ci-dessus, aucune interception des erreurs n'est implémentée, et le service suppose que toutes les variables sont des valeurs continues/positives et que la fréquence doit être un entier supérieur à 1. La longueur des vecteurs de date et de valeur doit être identique. La variable de la date doit respecter le format " mm/jj/aaaa ".

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png

<!--HONumber=46--> 
 