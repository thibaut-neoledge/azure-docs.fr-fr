<properties 
	pageTitle="Étape 6 : accéder au service web Machine Learning | Microsoft Azure" 
	description="Étape 6 du guide pas à pas du développement d'une solution prédictive : accès à un service web actif Azure Machine Learning." 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Étape de didacticiel pas à pas 6 : accéder au service web Azure Machine Learning

Vous voici arrivé à la dernière étape du didacticiel pas à pas [Développement d'une solution de prévision avec Azure ML](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.	[Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Publier le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Accéder au service web**

----------

Pour qu'un service web soit utile, les utilisateurs doivent pouvoir lui envoyer des données et recevoir des résultats. Il s'agit d'un service web Azure qui peut recevoir et renvoyer des données de deux manières :

-	**Demande/Réponse** : l'utilisateur envoie un jeu de données de crédit au service en utilisant le protocole HTTP et le service répond avec un jeu de résultats.
-	**Exécution en lots** : l'utilisateur envoie au service l'URL d'un objet blob Azure qui contient une ou plusieurs lignes de données de crédit. Le service enregistre les résultats dans un autre objet blob et renvoie l'URL de ce conteneur.  

L'onglet **TABLEAU DE BORD** du service web contient deux liens vers des informations qui aident le développeur à écrire du code pour accéder à ce service. Cliquez sur le lien **Page d'aide sur l'API** sur la ligne **Requête/Réponse**. La page qui s'ouvre contient un exemple de code pour utiliser le protocole de requête/réponse du service. De même, le lien sur la ligne **EXÉCUTION EN LOTS** fournit un exemple de code pour effectuer une requête de lots au service.

La page d'aide sur l'API contient des exemples en langages R, C# et Python.

Pour plus d’informations sur l’accès au service web et son utilisation, consultez [Utilisation d’un service web Azure Machine Learning publié à partir d’une expérience Machine Learning](machine-learning-consume-web-services.md).

<!---HONumber=July15_HO3-->