<properties 
	pageTitle="Forum aux questions : publication et utilisation d’applications Machine Learning sur Azure Marketplace | Microsoft Azure" 
	description="Forum Aux Questions (FAQ)" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bharaths" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2016" 
	ms.author="bharaths"/>

#Publication et utilisation d’applications Machine Learning sur Azure Marketplace : forum aux questions

##Questions relatives à la consommation à partir de Marketplace


**1. Le message d’erreur suivant apparaît une fois que j’ai saisi l’entrée pour le service web. Pourquoi ai-je cette erreur ?**

**La demande a provoqué une erreur de temporisation ou une erreur sur le serveur principal. Notre équipe étudie en ce moment ce problème. Nous sommes désolés pour ce désagrément. (500)**

Vos paramètres d'entrée peuvent ne pas être conformes au format requis pour le service web spécifique. Consultez le lien correspondant dans la documentation afin de rechercher le format correct pour les paramètres d'entrée et les limitations de ce service web.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2. Si je copie le lien de l’API du service web que je vois sur la page « Explorer ce jeu de données » et que je le colle dans une autre fenêtre du navigateur, quelles informations d’identification dois-je utiliser pour accéder aux résultats et comment puis-je les afficher ?**

Vous devez utiliser votre compte Marketplace en tant que nom d'utilisateur et la clé de compte principal comme mot de passe. La clé de compte principal se trouve sur la page **Explorer ce jeu de données** sous la description du service web (cliquez sur le bouton **Afficher**). Le résultat peut s’afficher dans le navigateur ou être disponible en téléchargement, en fonction du navigateur que vous utilisez.

**3. Le message d’erreur suivant apparaît une fois que j’ai saisi l’entrée pour le service web sur la page « Explorer ce jeu de données ». Pourquoi ai-je cette erreur ?**

**Une erreur inattendue s’est produite pendant le traitement de votre demande. Réessayez.**

Un ou plusieurs paramètres d’entrée de votre service web peuvent avoir dépassé la limite de longueur lors de la consommation du service web sur la page **Explorer ce jeu de données** de Marketplace. Les services peuvent être appelés avec une longueur d’entrée supérieure à l’aide de méthodes HTTP POST. Pour obtenir des exemples, consultez la page [Exemples de solutions utilisant R sur Machine Learning et publiées sur Marketplace](machine-learning-r-csharp-web-service-examples.md).

**4. Pourquoi l’onglet « EXPLORATEUR D’API » du magasin du portail Azure Classic est-il vide ?**

Il s’agit d’un problème connu sur Marketplace du portail Azure Classic. Notre équipe travaille en ce moment à la résolution de ce problème.


##Questions relatives à la publication à partir d’Azure Machine Learning sur Marketplace

**1. Pourquoi mes transactions de logos ou d’images ne s’actualisent-elles pas pour mon service web ?**

Les logos et images sont mis en cache sur le portail de publication et cela peut prendre jusqu’à 10 jours avant que le nouveau logo ou la nouvelle image ne soit mis(e) à jour sur le portail.

**2. Pourquoi l’onglet « Détail » de mon service web sur Marketplace affiche-t-il un message d’erreur ?**

Un problème connu se produit sur Marketplace lorsque l’utilisateur se connecte à Azure Machine Learning pour obtenir des détails sur un service. Notre équipe travaille en ce moment à la résolution de ce problème.

**3. Pourquoi l’exemple de code R dans les services web d’Azure Machine Learning ne permet-il pas de consommer les services web dans Marketplace ?**

Les systèmes d’authentification sont différents lorsque vous vous connectez aux services web Azure Machine Learning directement ou lorsque vous vous y connectez via le Marketplace. Les services du Marketplace sont des services OData qui peuvent être appelés avec les méthodes GET ou POST.

**4. Pourquoi les liens de support de mes offres de service web ne sont-ils pas correctement mis à jour pour certaines de mes offres ?**

Les liens de support sont globaux par éditeur, non par offre.

**5. Comment puis-je publier un service web avec le mode de saisie de lot sur Marketplace ?**

Le mode de saisie de lot n'est actuellement pas pris en charge dans les services web Marketplace.

**6. Qui dois-je contacter pour obtenir de l’aide si j’ai des questions sur la tâche d’éditeur de données ou si je rencontre des problèmes lors de la publication ?**

Veuillez contacter l’équipe Azure Marketplace à l’adresse <datamarketbd@microsoft.com> pour plus d’informations.





 

<!---HONumber=AcomDC_0914_2016-->