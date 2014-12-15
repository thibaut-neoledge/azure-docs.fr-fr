<properties title="Binary Classifier" pageTitle="Classifieur binaire | Azure" description="Binary Classifier" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 



#Classifieur binaire

  


Supposez que vous disposiez d'un jeu de données et que vous aimeriez prédire une variable dépendante binaire sur base de variables indépendantes. La " régression logistique " est une technique statistique très répandue qui est utilisée pour de telles prédictions. Ici, la variable dépendante est binaire ou dichotomique et p correspond à la probabilité de la présence de la caractéristique d'intérêt. 

Dans un scénario simple, un chercheur tente de prédire si un étudiant potentiel est susceptible d'accepter l'offre d'admission pour une université en fonction d'informations (moyenne générale au lycée, revenus familiaux, pays/état de résidence, sexe). Le résultat prédit correspond à la probabilité qu'un éventuel futur étudiant accepte l'offre d'admission. Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/log_regression) correspond au modèle de régression logistique pour les données et renvoie la valeur de probabilité (y) pour chacune des observations dans les données.  
  
>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.  
  

#Utilisation du service web  
Ce service web fournit les valeurs prédites de la variable dépendante sur base des variables indépendantes pour toutes les observations. Le service web attend de l'utilisateur final qu'il entre ses données sous forme de chaîne, dans laquelle les lignes sont séparées par des virgules (,) et les colonnes sont séparées par des points-virgules (;). Le service web attend 1 ligne à la fois et requiert que la première colonne soit la variable dépendante. Un exemple de jeu de données pourrait ressembler à ceci :

![Sample data][1]

Pour les observations sans variable dépendante, la mention " NA " doit être entrée dans la colonne Y. Les données entrées pour le jeu de données ci-dessus correspondraient à la chaîne suivante : " 1;5;2,1;1;6,0;5,3;2,1,0;5;5,0;3;4,1;2;1,NA;3;4 ". La sortie correspond à la valeur prédite pour chacune des lignes selon les variables indépendantes. 

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx ) un exemple d'application).

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
>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos de présentation concernant la création d'expériences et [la publication de services web](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). La capture d'écran ci-dessous présente l'expérience qui a créé le service web et le code d'exemple de chaque module de l'expérience.

À partir d'Azure ML, une nouvelle expérience vide a été créée et deux modules " Exécuter le script R " ont été importés dans l'espace de travail. Ce service web exécute une expérience Azure Machine Learning avec un script R sous-jacent.  Cette expérience comprend 2 parties : la définition du schéma et le modèle d'apprentissage + l'évaluation.  Le premier module définit la structure attendue du jeu de données en entrée, où la première variable correspond à la variable dépendante et les autres variables sont indépendantes. Le deuxième module correspond à un modèle de régression logistique générique pour les données en entrée.    

![Experiment flow][2]

####Module 1 :

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Module 2:
	#GLM Modeling   
	data <- maml.mapInputPort(1) # class: data.frame  data.split <- strsplit(data[1,1], ",")[[1]] data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) data.split <- data.frame(data.split) model <- glm(data.split$V1 ~., family='binomial', data=data.split)  out <- 
	data.frame(predict(model,data.split, type="response")) pred1 <- as.data.frame(out) group <- array(1:nrow(pred1)) for (i in 
	1:nrow(pred1))  
	{if(as.numeric(pred1[i,])>0.5) {group[i]=1} else {group[i]=0}} pred2 <- as.data.frame(group) maml.mapOutputPort("pred2");  


#Limites
Il s'agit d'un exemple très simple de service web de classification binaire. Comme illustré dans l'exemple de code ci-dessus, aucune interception des erreurs n'est implémentée et le service suppose que tout correspond à une variable binaire/continue (aucune fonctionnalité de catégorie n'est autorisée) dans la mesure où le service entre uniquement des valeurs numériques au moment de la création de ce service web. En outre, le service gère actuellement une limite de taille pour les données, en raison de la nature de demande/réponse de l'appel de service web et du fait que le modèle est adapté chaque fois que le service web est appelé. 

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png
