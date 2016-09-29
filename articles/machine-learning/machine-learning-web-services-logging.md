<properties 
	pageTitle="Journalisation pour les services web Machine Learning | Microsoft Azure" 
	description="Découvrez comment activer la journalisation pour les services Web de Machine Learning. La journalisation fournit des informations supplémentaires pour vous aider à résoudre les problèmes des API." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="08/09/2016"
	ms.author="raymondl;garye"/>

#Activation de la journalisation pour les services Web de Machine Learning  

Ce document fournit des informations sur la fonctionnalité de journalisation des services Web de Azure ML. L’activation de la journalisation dans les services Web fournit des informations supplémentaires qui vont au-delà d’un simple numéro d'erreur et d’un simple message pour résoudre les problèmes liés aux API.

-	Comment activer la journalisation dans les services Web :
	-	Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/)
	-	Cliquez sur Machine Learning, sur votre espace de travail, puis choisissez l’option de menu Service web.
	-	Dans la liste de Services web, cliquez sur le nom du service web.
	-	Dans la liste des points de terminaison, cliquez sur le nom du point de terminaison.
	-	Cliquez sur l’option de menu Configurer.
	-	Définissez Niveau de suivi des diagnostics sur Erreur ou Tous, puis cliquez sur Enregistrer dans la barre de menus du bas.
-	Que se passe-t-il une fois la journalisation activée :
	-	Quand la journalisation est activée, tous les diagnostics et toutes les erreurs du point de terminaison sélectionné sont enregistrés dans le compte de stockage Azure lié à l’espace de travail de l’utilisateur. Vous pouvez voir ce compte de stockage dans la vue Tableau de bord du portail Azure Classic (en bas de la section Aperçu rapide) de son espace de travail.
-	Comment afficher les journaux :
	-	Les journaux peuvent être affichés à l'aide de plusieurs outils servant à « explorer » un compte de stockage Azure. La méthode la plus simple consiste à accéder au compte de stockage dans le portail Azure Classic, puis de cliquer sur l'onglet CONTENEURS. Vous verrez alors un conteneur nommé **ml-diagnostic**. Ce conteneur contient toutes les informations de diagnostic pour tous les points de terminaison de service Web pour tous les espaces de travail associés à ce compte de stockage.
-	Que contient un journal d’objets blob :
	-	Chaque objet blob dans le conteneur conserve les informations de diagnostic pour une et une seule des opérations suivantes :
		-	une exécution de la méthode de traitement par lot
		-	une exécution de la méthode de requête-réponse
		-	l’initialisation d'un conteneur de requête-réponse
	-	Le nom de chaque objet blob a un préfixe qui prend la forme suivante : {ID d’espace de travail}-{ID de service Web}-{ID de point de terminaison}/{type de journal}
-	Le type de journal peut avoir une des valeurs suivantes :
	- batch
	- score/requests
	- score/init

 

<!---HONumber=AcomDC_0914_2016-->