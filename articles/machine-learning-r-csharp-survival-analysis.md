<properties 
	pageTitle="Analyse de survie | Azure" 
	description="Probabilité d'occurrence d'un événement d'analyse de survie" 
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


#Analyse de survie 





Dans de nombreux scénarios, le principal résultat évalué est la durée avant qu'un événement qui vous intéresse ne se produise. En d'autres termes, vous posez la question " Quand cet événement va-t-il se produire ? ". Par exemple, considérez les situations dans lesquelles les données décrivent le temps écoulé (jours, années, kilométrage, etc.) jusqu'à ce que l'événement se produise (rechute (maladie), obtention d'un doctorat, défaillance des plaquettes de frein). Dans les données, chaque instance représente un objet spécifique (un patient, une personne, une voiture, etc.).

Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) répond à la question " Quelle est la probabilité que l'événement se produise en n jours/heures/etc. (temps) pour l'objet x ? " Grâce au modèle d'analyse de survie, ce service web permet aux utilisateurs de fournir des données pour entraîner le modèle et le tester. Le principal objectif de l'expérience est de modéliser la durée écoulée jusqu'à ce que l'événement se produise. 

>Bien que ce service web puisse être employé par les utilisateurs, via une application mobile, un site web ou même sur un ordinateur local, par exemple, son objectif est également de constituer un exemple d'utilisation d'Azure ML pour créer des services web sur le code R. Avec seulement quelques lignes de code R et quelques clics sur un bouton d'Azure ML Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les périphériques du monde entier l'utilisent sans que l'auteur du service web n'ait à configurer l'infrastructure.  

##Utilisation du service web

Le schéma de données d'entrée du service web est présenté dans le tableau suivant. Six types d'information d'entrée sont nécessaires : les données d'apprentissage, le données de test, la durée d'intérêt, l'index de dimension " temporelle ", l'index de dimension de l'" évènement " et les types de variables (continues ou facteur). Les données d'apprentissage sont représentées par une chaîne, dans laquelle les lignes sont séparées par des virgules et les colonnes sont séparées par des points-virgules. Le nombre de fonctionnalités des données est flexible. Tous les éléments de la chaîne d'entrée doivent être numériques. Dans les données d'apprentissage, la dimension " temporelle " indique le nombre d'unités de temps (jours, années, kilométrage, etc.) qui s'est écoulé depuis le point de départ de l'étude (un patient suivant un programme de désintoxication, un étudiant commençant son doctorat, le début de la conduite d'une voiture, etc.) jusqu'à ce que l'événement se produise (le patient consomme de nouveau de la drogue, l'étudiant obtient son doctorat, les plaquettes de frein de la voiture sont usées, etc.). La dimension de l'" évènement " indique si l'évènement se produit à la fin de l'étude. " event=1 " signifie que l'évènement se produit au moment indiqué par la dimension " temporelle " ; tandis que " event=0 " signifie que l'évènement n'a pas encore eu lieu au moment indiqué par la dimension " temporelle ".

- trainingdata : une chaîne de caractères. Les lignes sont séparées par des virgules et les colonnes sont séparées par des points-virgules. Chaque ligne inclut la dimension " temporelle ", la dimension de l'" évènement " et les variables de prédiction.
- testingdata : une ligne de données qui contient les variables de prédiction pour un objet particulier.
- time_of_interest : le temps n écoulé
- index_time : index de colonne de la dimension " temporelle " (à partir de 1)
- index_event : index de colonne de la dimension de l'" évènement " (à partir de 1)
- variable_types : une chaîne de caractères séparés par des points-virgules. 0 représente les variables continues et 1 représente les variables facteur.


La sortie est la probabilité qu'un événement se produise à un moment précis. 

>Étant hébergé sur Microsoft Azure Marketplace, ce service est un service OData. Il peut être appelé à l'aide des méthodes POST ou GET. 

Il existe plusieurs façons d'utiliser le service de manière automatique ([voici](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx) un exemple d'application). 

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


L'interprétation de ce test est la suivante. Supposons que l'objectif des données est de modéliser le temps écoulé jusqu'à la rechute des patients ayant suivi un des deux programmes de désintoxication. La sortie du service web est la suivante : pour les patients de 35 ans, ayant déjà suivi 2 traitements et suivant actuellement le programme de traitement résidentiel à long terme pour utilisation d'héroïne et de cocaïne, la probabilité de consommer de nouveau de la drogue est de 95,64 % au 500ème jour.

##Création du service web

>Ce service web a été créé à l'aide d'Azure ML. Pour un essai gratuit, ainsi que des vidéos de présentation concernant la création d'expériences et [la publication de services web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consultez [azure.com/ml](http://azure.com/ml). La capture d'écran ci-dessous présente l'expérience qui a créé le service web et le code d'exemple de chaque module de l'expérience.

À partir d'Azure ML, une nouvelle expérience a été créée et deux modules " Exécuter le script R " ont été créés sur l'espace de travail. Le schéma de données a été créé avec un simple module " Exécuter le script R ", qui définit le schéma de données d'entrée pour le service web. Ce module est ensuite lié au deuxième module " Exécuter le script R " qui effectue la majeure partie du travail. Ce module réalise le prétraitement des données, la création du modèle et les prédictions. Dans l'étape de prétraitement des données, les données d'entrée représentées par une chaîne longue sont transformées et converties en une trame de données. Dans l'étape de création du modèle, un package R externe " survival_2.37-7.zip " est tout d'abord installé pour effectuer l'analyse de survie. La fonction " coxph " est ensuite exécutée après la tâche de traitement des données de série. Pour connaître les détails de la fonction " coxph " pour l'analyse de survie, consultez la documentation R. Dans l'étape de prédiction, une instance de test est fournie dans le modèle d'apprentissage avec la fonction " surfit " et la courbe de survie de cette instance de test est générée en tant que variable " curve ". Enfin, vous obtenez la probabilité de la durée d'intérêt. 

###Flux de l'expérience :

![experiment flow][1]

####Module 1 :

    #data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1"
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)
    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Module 2 :

    #read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")
    # preprocessing trainingdata
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)
    # preprocessing testingdata
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))
    # preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types
    # necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)
    # prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # based on user input, find the event occurance probablity
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }
    #pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Limites

Ce service web accepte uniquement les valeurs numériques sous forme de variables de fonctionnalité (colonnes). La colonne " évènement " peut uniquement prendre la valeur 0 ou 1. La colonne " temps " doit contenir un entier positif.

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png

<!--HONumber=46--> 
