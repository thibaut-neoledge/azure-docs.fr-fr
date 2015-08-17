<properties 
	pageTitle="Modèle de cluster | Microsoft Azure" 
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
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>


#Modèle de cluster    

Comment pouvons-nous prédire le comportement de groupes de détenteurs de cartes de crédit afin de limiter le risque de non-paiement pour les émetteurs de carte de crédit ? Comment pouvons-nous définir des groupes de traits de personnalité chez des employés afin d'améliorer leurs performances au travail ? Comment les médecins peuvent-ils classer leurs patients en groupes selon les caractéristiques de leurs maladies ? En principe, l’outil d’analyse des clusters permet d’apporter une réponse à toutes ces questions.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
   
L’analyse des clusters classe un ensemble d’observations en au moins deux groupes inconnus s’excluant mutuellement, en fonction de combinaisons de variables. L'objectif de l'analyse des clusters est de découvrir un système d'organisation des observations, généralement sur base de personnes ou de leurs caractéristiques, en groupes, dont les membres ont des propriétés en commun. Ce [service](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utilise la méthodologie des k-moyennes, une technique de clustering couramment utilisée, afin d’organiser les données arbitraires en groupes. Ce service web prend les données et le nombre de clusters k en entrée, et produit des prédictions sur les groupes k auxquels appartient chaque observation.

>Les utilisateurs peuvent potentiellement accéder à ce service web par le biais d’une application mobile, d’un site web ou même d’un ordinateur local, par exemple. Mais l’objectif du service web est également de servir d’exemple d’utilisation d’Azure Machine Learning pour créer des services web avec le code R. Avec seulement quelques lignes de code R et quelques clics dans Azure Machine Learning Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les appareils du monde entier l’utilisent sans que l’auteur du service web n’ait à configurer l’infrastructure.

##Utilisation du service web   
Ce service web regroupe les données en un ensemble de groupes k et génère l'affectation de groupe pour chaque ligne. Le service web attend de l’utilisateur final qu’il entre ses données sous forme de chaîne, dans laquelle les lignes sont séparées par des virgules (,) et les colonnes sont séparées par des points-virgules (;). Le service web attend 1 ligne à la fois. Un exemple de jeu de données pourrait ressembler à ceci :

![Exemples de données][1]

Supposons que l’utilisateur souhaite séparer ces données en 3 groupes s’excluant mutuellement. Les données d’entrée pour le jeu de données ci-dessus seraient les suivantes : valeur = « 10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4 »; k=« 3 ». Le résultat correspond à l'appartenance au groupe prédite pour chacune des lignes.

>Étant hébergé sur Azure Marketplace, ce service est un service OData. Il peut être appelé à l’aide des méthodes POST ou GET.

Il existe plusieurs façons d’utiliser le service de manière automatique (un exemple d’application est disponible [ici](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx)).

###Début du code C# pour l'utilisation du service web :

	public class Input
	{
	        public string value;
	        public string k;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

À partir d’Azure Machine Learning, une nouvelle expérience vide a été créée et deux modules [Exécuter le script R][execute-r-script] ont été importés dans l’espace de travail. Le schéma de données a été créé avec un simple module [Exécuter le script R][execute-r-script]. Il a ensuite été lié à la section du modèle de cluster, à nouveau créée avec un module [Exécuter le script R][execute-r-script]. Dans le module [Exécuter le script R][execute-r-script] utilisé pour le modèle de cluster, le service web utilise ensuite la fonction « k moyenne », qui est prédéfinie dans le module [Exécuter le script R][execute-r-script] d’Azure Machine Learning.
   

     
![Flux de l’expérience][3]

####Module 1 : 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	
	maml.mapOutputPort("mydata");     
	

####Module 2 :
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means cluster analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# Get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# Append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
##Limitations
Il s'agit d'un exemple très simple de service web de clustering. Comme illustré dans l’exemple de code ci-dessus, aucune interception des erreurs n’est implémentée et le service suppose que tout correspond à une variable continue (aucune fonctionnalité de catégorie n’est autorisée), dans la mesure où le service entre uniquement des valeurs numériques au moment de la création de ce service web. En outre, le service gère actuellement une limite de taille pour les données, en raison de la nature de la demande/réponse de l’appel de service web et du fait que le modèle est adapté chaque fois que le service web est appelé.

##Forum Aux Questions
Pour les questions fréquemment posées relatives à l’utilisation du service web ou à la publication sur Azure Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=August15_HO6-->