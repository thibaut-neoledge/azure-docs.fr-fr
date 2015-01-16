<properties title="Lexicon Based Sentiment Analysis" pageTitle="Étape 1 : Analyse de sentiments basée sur un lexique | Azure" description="Analyse de sentiments basée sur un lexique" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 



#Analyse de sentiments basée sur un lexique 

 


Comment pouvez-vous évaluer les avis et les attitudes des utilisateurs envers des marques ou des rubriques sur les réseaux sociaux en ligne, tels que des billets Facebook, des Tweets et des critiques ? L'analyse de sentiments fournit une méthode pour l'analyse de telles questions.

En particulier, il y a en général deux méthodes d'analyse des sentiments : l'une d'elles utilise un algorithme d'apprentissage supervisé et l'autre peut être considérée comme étant un algorithme d'apprentissage non supervisé. L'algorithme d'apprentissage supervisé crée généralement un modèle de classification pour les corpus volumineux annotés. Sa précision est principalement basée sur la qualité de l'annotation et la phase d'apprentissage prend généralement beaucoup de temps. En outre, lorsque nous appliquons l'algorithme à un autre domaine, le résultat n'est généralement pas bon. Par rapport à l'apprentissage supervisé, l'apprentissage non supervisé basé sur un lexique utilise un dictionnaire de sentiments, qui ne nécessite pas le stockage d'un corpus de données volumineux et de formation, ce qui permet d'accélérer considérablement le processus. 

Notre [service](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) repose sur le lexique MPQA(http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), qui est l'un des lexiques de subjectivité les plus fréquemment utilisés. Il existe 5 097 mots négatifs et 2 533 mots positifs dans le MPQA. Par ailleurs, tous ces mots sont annotés avec une polarité forte ou faible. Le corpus entier fait l'objet d'une licence GPL GNU. Le service web peut être utilisé pour toutes les phrases courtes telles que les Tweets et les billets Facebook. 

>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.

##Utilisation du service web

Les données en entrée peuvent correspondre à n'importe quel texte, mais le service web fonctionne mieux avec des phrases courtes. Le résultat est une valeur numérique comprise entre -1 et 1. Toute valeur inférieure à 0 indique que le sentiment à l'égard du texte est négatif, il est positif si la valeur est supérieure à 0. La valeur absolue du résultat indique la puissance du sentiment associé. 

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/) un exemple d'application).

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


L'entrée est " C'est une belle journée ", la sortie est " 1 ", ce qui indique un sentiment positif associé à la phrase en entrée. 

##Création du service web
>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos de présentation concernant la création d'expériences et [la publication de services web](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). La capture d'écran ci-dessous présente l'expérience qui a créé le service web et le code d'exemple de chaque module de l'expérience.


Dans Azure ML, une nouvelle expérience a été créée. La figure ci-dessous illustre le flux d'expérience pour l'analyse de sentiments basée sur un lexique. Le fichier " sent_dict.csv " correspond au lexique de subjectivité MPQA ; il est défini en tant que l'une des entrées du module " Exécuter le script R ". Une autre entrée correspond à une critique partielle provenant d'un jeu de données de critiques Amazon à des fins de test, où nous avons effectué des opérations de sélection, de modification des noms de colonne et de fractionnement.  Nous utilisons le package de hachage pour stocker le lexique de subjectivité en mémoire et accélérer le processus de calcul du score. L'ensemble du texte sera converti en jetons par le package " tm " et comparé au mot dans le dictionnaire de sentiments. Enfin, un score sera calculé en ajoutant le poids de chaque mot subjectif dans le texte. 

###Flux de l'expérience :

![experimenmt flow][2]


####Module 1 :
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
    # install hash package
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)
    #  compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }
    # Sample operation
    data.set <- data.frame(result)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Limites

Du point de vue de l'algorithme, l'analyse de sentiments basée sur un lexique est un outil général d'analyse de sentiments, qui peut ne pas être plus performant que la méthode de classification pour des champs spécifiques. Le problème de la négation n'est pas correctement pris en charge. Nous codons en dur plusieurs négations dans notre programme, mais une meilleure méthode consiste à utiliser un dictionnaire de négations et à créer certaines règles. Le service web fonctionne mieux avec des phrases courtes et simples, telles que les Tweets et les billets Facebook, qu'avec les phrases longues et complexes, telles que les critiques Amazon. 

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png








