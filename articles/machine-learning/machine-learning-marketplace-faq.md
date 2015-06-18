<properties 
	pageTitle="Forum aux questions pour la publication et l'utilisation d'applications Machine Learning sur Azure Marketplace | Azure" 
	description="Forum Aux Questions (FAQ)" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="luisca"/> 

#Forum aux questions pour la publication et l'utilisation d'applications Machine Learning sur Azure Marketplace

##Pour une consommation à partir de Marketplace


###Question 1 : Je vois l'erreur suivante après avoir saisi l'entrée pour le service web. Pourquoi ai-je cette erreur ?
La demande a provoqué une erreur de temporisation ou une erreur sur le serveur principal. Notre équipe étudie en ce moment ce problème. Nous sommes désolés pour ce désagrément. (500)
Réponse : Vos paramètres d'entrée peuvent ne pas être conformes au format requis pour le service web spécifique. Consultez le lien correspondant dans la documentation afin de rechercher le format correct pour les paramètres d'entrée et les limitations de ce service web.

###Question 2 : Si je copie le lien de l'API du service web que je vois dans " Explorer ce jeu de données " et que je le colle dans une autre fenêtre (ou onglet) du navigateur, quelles sont les informations d'identification que je dois utiliser pour accéder aux résultats et comment puis-je les afficher ?
Réponse : Vous devez utiliser votre compte Marketplace en tant que nom d'utilisateur et la clé de compte principal comme mot de passe. La clé de compte principal se trouve sur la page " Explorer ce jeu de données " sous la description du service web (cliquez sur le bouton " Afficher "). Le résultat peut s'afficher dans le navigateur ou être disponible en téléchargement en fonction du navigateur que vous utilisez.

###Question 3 : Je vois l'erreur suivante après avoir saisi l'entrée pour le service web dans " Explorer ce jeu de données ". Pourquoi ai-je cette erreur ?
Une erreur inattendue s'est produite pendant le traitement de votre demande. Réessayez.
Réponse : Un ou plusieurs paramètres d'entrée de votre service web peuvent avoir dépassé la limite de longueur lors de la consommation du service web sur la page " Explorer ce jeu de données " de Marketplace. Les services peuvent être appelés avec une longueur d'entrée supérieure à l'aide de méthodes HTTP POST ; l'exemple de code est publié dans les exemples de services décrits ici : http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-web-service-examples/.

###

##Pour une publication à partir d'Azure ML sur Marketplace

###Question 1 : Pourquoi mes logos/images/nombres de transactions ne s'actualisent-ils pas pour mon service web ? 
Réponse : Sur le portail de publication, les logos/images sont mis en cache et cela peut prendre jusqu'à 10 jours pour que le nouveau logo ou la nouvelle image soit mis(e) à jour sur le portail.

###Question 2 : Pourquoi l'" onglet Détail " de mon service web sur Marketplace affiche-t-il une erreur ?
Réponse : Un problème connu se produit sur Marketplace lorsque l'utilisateur se connecte à Azure ML pour obtenir des détails sur un service. Notre équipe travaille en ce moment à la résolution de ce problème.

###Question 3 : Pourquoi l'exemple de code R dans les services web d'Azure ML ne permet-il pas de consommer les services web dans Marketplace ?
Réponse : Les systèmes d'authentification sont différents lorsque vous vous connectez aux services web Azure ML directement par rapport à la connexion à ces services web via le Marketplace. Les services du Marketplace sont des services OData et ils peuvent être appelés avec les méthodes GET ou POST. 

###Question 4 : Pourquoi les liens de support de mes offres de service web ne sont-ils pas correctement mis à jour pour certaines de mes offres ?
Réponse : Les liens de support sont globaux par éditeur, et non par offre. 

###Question 5 : Comment puis-je publier un service web avec le mode de saisie de lot sur Marketplace ?
Réponse : Le mode de saisie de lot n'est actuellement pas pris en charge dans les services web Marketplace.

###Question 6 : Qui dois-je contacter pour obtenir de l'aide si j'ai des questions sur la tâche d'éditeur de données ou si je rencontre des problèmes lors de la publication ?
Réponse : Veuillez contacter l'équipe Marketplace à l'adresse datamarketbd@microsoft.com pour plus d'informations.






<!--HONumber=46--> 
 