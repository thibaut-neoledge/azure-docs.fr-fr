<properties title="Difference in Proportions Test" pageTitle="Test de différence des proportions | Azure" description="Test de différence des proportions" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Test de différence des proportions




Deux proportions sont-elles différentes d'un point de vue statistique ? Supposons qu'un utilisateur souhaite comparer deux films pour déterminer si l'un présente une proportion nettement supérieure de " J'aime " par rapport à l'autre. Avec un échantillon volumineux, il peut exister une différence statistiquement significative entre les proportions 0,50 et 0,51, tandis qu'avec un petit échantillon, il n'y a peut-être pas suffisamment de données pour déterminer si ces proportions sont réellement différentes. 

Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) effectue un test d'hypothèse de la différence entre deux proportions sur base du nombre de réussites entré par l'utilisateur et du nombre total d'essais pour les 2 groupes de comparaison. Exemple de scénario : ce service web pourrait être appelé à partir d'une application de comparaison de films, indiquant à l'utilisateur en fonction de la classification des films si un film est vraiment " aimé " plus souvent que les autres.

>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.


##Utilisation du service web

Ce service accepte 4 arguments et effectue un test d'hypothèse des proportions.

Les arguments d'entrée sont les suivants :

* Successes1 : nombre d'événements de succès dans l'échantillon 1
* Successes2 : nombre d'événements de succès dans l'échantillon 2
* Total1 : taille de l'échantillon 1
* Total2 : taille de l'échantillon 2

La sortie du service correspond au résultat du test d'hypothèse de pair avec la statistique de khi carré, la fraction décimale, la valeur p, la proportion dans l'échantillon 1/2 et les limites d'intervalle de confiance.

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx ) un exemple d'application).

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

À partir d'Azure ML, une nouvelle expérience vierge a été créée avec deux modules " Exécuter le script R ". Dans le premier module, le schéma de données est défini alors que le second module utilise la commande prop.test dans R pour effectuer le test d'hypothèse pour 2 proportions. 


###Flux de l'expérience :

![Experiment flow][2]


####Module 1 :
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Module 2 :

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Limites 

Il s'agit d'un exemple très simple permettant de tester la différence dans 2 proportions. Comme illustré dans l'exemple de code ci-dessus, aucune interception des erreurs n'est implémentée et le service suppose que toutes les variables sont continues.

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png
