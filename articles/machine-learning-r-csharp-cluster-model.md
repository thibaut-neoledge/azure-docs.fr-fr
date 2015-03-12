<properties 
	pageTitle="Modèle de cluster | Azure" 
	description="Modèle de cluster" 
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
	ms.date="02/12/2015" 
	ms.author="jaymathe"/> 


#Modèle de cluster    



Comment pouvons-nous prédire le comportement de groupes de détenteurs de cartes de crédit afin de limiter le risque de non paiement pour les émetteurs de carte de crédit ?  Comment pouvons-nous définir des groupes de traits de personnalité chez des employés afin d'améliorer leurs performances au travail ? Comment les médecins peuvent-ils classer leurs patients en groupes selon les caractéristiques de leurs maladies ? En principe, l'outil d'analyse des clusters permet d'apporter à une réponse à toutes ces questions.    
   
Dans la pratique, l'analyse des clusters classe un ensemble d'observations en minimum deux groupes inconnus s'excluant mutuellement en fonction de combinaisons de variables. L'objectif de l'analyse des clusters est de découvrir un système d'organisation des observations, généralement sur base de personnes ou de leurs caractéristiques, en groupes, dont les membres ont des propriétés en commun. Ce [service](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utilise la méthodologie des k-moyennes, une technique de clustering couramment utilisée, afin d'organiser les données arbitraires en groupes. Ce service web prend les données et le nombre de clusters k en entrée, et produit des prédictions sur les groupes k auxquels appartient chaque observation. 

>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.  

#Utilisation du service web   
Ce service web regroupe les données en un ensemble de groupes k et génère l'affectation de groupe pour chaque ligne. Le service web attend de l'utilisateur final qu'il entre ses données sous forme de chaîne, dans laquelle les lignes sont séparées par des virgules (,) et les colonnes sont séparées par des points-virgules (;). Le service web attend 1 ligne à la fois. Un exemple de jeu de données pourrait ressembler à ceci :

![Sample data][1]

Supposons que l'utilisateur souhaite séparer ces données en trois groupes s'excluant mutuellement. Les données d'entrée pour le jeu de données ci-dessus seraient les suivantes : value = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". Le résultat correspond à l'appartenance au groupe prédite pour chacune des lignes.

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx ) un exemple d'application).

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


#Création du service web  
>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos de présentation concernant la création d'expériences et [la publication de services web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). La capture d'écran ci-dessous présente l'expérience qui a créé le service web et le code d'exemple de chaque module de l'expérience.

À partir d'Azure ML, une nouvelle expérience vide a été créée et deux modules " Exécuter le script R " ont été importés dans l'espace de travail. Le schéma de données a été créé avec un simple module " Exécuter le script R " ; ensuite, le schéma de données a été lié à la section du modèle de cluster, à nouveau créée avec un module " Exécuter le script R ". Dans le module " Exécuter le script R " utilisé pour le modèle de cluster, le service web utilise ensuite la fonction " moyenne k ", qui est prédéfinie dans le module " Exécuter le script R " d'Azure ML.    
   

     
![Experiment flow][3]

####Module 1 : 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	maml.mapOutputPort("mydata");     
	

####Module 2 :
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#Limites
Il s'agit d'un exemple très simple de service web de clustering. Comme illustré dans l'exemple de code ci-dessus, aucune interception des erreurs n'est implémentée et le service suppose que tout correspond à une variable continue (aucune fonctionnalité de catégorie n'est autorisée) dans la mesure où le service entre uniquement des valeurs numériques au moment de la création de ce service web. En outre, le service gère actuellement une limite de taille pour les données, en raison de la nature de demande/réponse de l'appel de service web et du fait que le modèle est adapté chaque fois que le service web est appelé. 

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png

<!--HONumber=46--> 
