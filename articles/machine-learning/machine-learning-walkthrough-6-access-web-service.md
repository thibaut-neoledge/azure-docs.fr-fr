<properties 
	pageTitle="Étape 6 : accès au service web Azure Machine Learning | Azure" 
	description="Étape de procédure pas à pas de solution 6 : accès à un service web Azure Machine Learning actif" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="garye"/>


Vous voici arrivé à la dernière étape du didacticiel pas à pas, [Développement d'une solution de prévision avec Azure ML][develop] :

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Créer un espace de travail ML][create-workspace]
2.	[Télécharger les données existantes][upload-data]
3.	[Créer une nouvelle expérience][create-new]
4.	[Former et évaluer les modèles][train-models]
5.	[Publier le service web][publish]
6.	**Accès au service web**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Étape 6 : accès au service web Azure Machine Learning

Pour que le service web soit utile, les utilisateurs doivent pouvoir lui envoyer des données et recevoir des résultats. Il s'agit d'un service web Azure qui peut recevoir et renvoyer des données de deux manières :  

-	**Demande/Réponse** : l'utilisateur envoie un jeu de données de crédit au service en utilisant le protocole HTTP et le service répond avec un jeu de résultats.
-	**Exécution en lots** : l'utilisateur envoie au service l'URL d'un objet blob Azure qui contient une ou plusieurs lignes de données de crédit. Le service enregistre les résultats dans un autre objet blob et renvoie l'URL de ce conteneur.  

L'onglet **TABLEAU DE BORD** du service web contient deux liens vers des informations qui aident le développeur à écrire du code pour accéder à ce service. Cliquez sur le lien **Page d'aide sur l'API** sur la ligne **Requête/Réponse**. La page qui s'ouvre contient un exemple de code pour utiliser le protocole de requête/réponse du service. De même, le lien sur la ligne **EXÉCUTION EN LOTS** fournit un exemple de code pour effectuer une requête de lots au service.  

La page d'aide sur l'API contient des exemples en langages R, C# et Python. Exemple : voici le code R que vous pouvez utiliser pour accéder au service web que nous avons publié (l'URL réelle du service est affichée dans votre exemple de code) :  

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()



<!--HONumber=46--> 
 