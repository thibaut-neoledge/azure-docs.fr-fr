<properties 
	pageTitle="Activation de la journalisation pour les services Web de Machine Learning | Azure" 
	description="Découvrez comment activer la journalisation pour les services Web de Machine Learning." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm=""
	ms.workload="big-data" 
	ms.date="02/12/2015"
	ms.author="raymondl,garye"/>

#Activation de la journalisation pour les services Web de Machine Learning  

Ce document fournit des informations sur la fonctionnalité de journalisation des services Web de Azure ML. L’activation de la journalisation dans les services Web fournit des informations supplémentaires qui vont au-delà d’un simple numéro d'erreur et d’un simple message pour résoudre les problèmes liés aux API.

-	Comment activer la journalisation dans les services Web :   
	-	Pour activer la journalisation, vous devez accéder au tableau de bord du service Web à partir du portail Azure. Cliquez sur le bouton « Oui » pour l'activer, puis cliquez sur « Enregistrer ».  
-	Que se passe-t-il une fois la journalisation activée :  
	-	Lorsque la journalisation est activée, tous les diagnostic et toutes les erreurs du point de terminaison par défaut sont enregistrés dans le compte de stockage Azure lié à l'espace de travail de l'utilisateur. Vous pouvez voir ce compte de stockage dans la vue Tableau de bord du portail Azure \(en bas de la section Aperçu rapide\) de son espace de travail.  
-	Disponibilité :  
	-	Nous sommes actuellement en train d’activer la journalisation du point de terminaison par défaut du service Web. Nous serons bientôt en mesure de présenter cette fonctionnalité pour le point de terminaison que l’utilisateur peut créer dans le portail Azure.  
-	Comment afficher les journaux :  
	-	Les journaux peuvent être affichés à l'aide de plusieurs outils servant à « explorer » un compte de stockage Azure. La méthode la plus simple consiste à accéder au compte de stockage dans le portail Azure, puis de cliquer sur l'onglet CONTENEURS. Vous verrez alors un conteneur nommé **ml-diagnostic**. Ce conteneur contient toutes les informations de diagnostic pour tous les points de terminaison de service Web pour tous les espaces de travail associés à ce compte de stockage.  
-	Que contient un journal d’objets blob :  
	-	Chaque objet blob dans le conteneur conserve les informations de diagnostic pour une et une seule des opérations suivantes :
		-	une exécution de la méthode de traitement par lot  
		-	une exécution de la méthode de requête-réponse  
		-	l’initialisation d'un conteneur de requête-réponse  
	-	Le nom de chaque objet blob a un préfixe qui prend la forme suivante : {ID d’espace de travail}-{ID de service Web}-{ID de point de terminaison}/{type de journal}  
-	Le type de journal prend l'une des valeurs suivantes : batch score/requests score/init  


<!--HONumber=54-->