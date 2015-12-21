<properties 
	pageTitle="Exemple d’application Machine Learning : Frequently Bought Together | Microsoft Azure" 
	description="Service web Machine Learning qui effectue une analyse du panier d’achat en ligne afin de proposer des recommandations sur des produits fréquemment achetés ensemble, sur la base de l’historique des transactions fourni par l’utilisateur." 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="luisca"/>

# Exemple d’application Machine Learning : Frequently Bought Together


## IMPORTANT : CE SERVICE EST OBSOLÈTE

La fonctionnalité Frequently Bought Together fournie par ce service a maintenant été intégrée dans notre [API Recommendations](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c), plus importante. Nous vous incitons à utiliser Recommendations au lieu de ce service.

##Vue d'ensemble

Le [service web Frequently Bought Together de Machine Learning](https://datamarket.azure.com/dataset/amla/mba) effectue une analyse du panier d’achats en ligne, afin de proposer des recommandations basées sur les produits récemment achetés ensemble, à partir de l’historique des transactions. Les recommandations du service Frequently Bought Together permettent aux utilisateurs d’identifier les produits d’un catalogue regroupant les éléments les plus pertinents lorsqu’ils recherchent un produit spécifique. La mise en évidence de ces recommandations s’est avérée utile, améliorant les ventes des détaillants en ligne.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
  
##Prise en main 

Une fois que vous vous êtes abonné au service web Frequently Bought Together, vous pouvez utiliser [l’exemple d’application web Market Basket Analysis Service](https://marketbasket.cloudapp.net/) pour charger vos données dans un modèle et découvrir les ensembles de produits fréquemment achetés. Pour utiliser l’application ou l’API, vous devez posséder une clé API, que vous pouvez obtenir sur la [page de votre compte Microsoft Azure Data Market](https://datamarket.azure.com/account).

##Utilisation du service web 

Ce service contient les API permettant de créer des modèles Frequently Bought Together, de charger l’historique des transactions et de récupérer les ensembles de produits souvent achetés ensemble les mieux classés, pour un produit donné. Vous trouverez des exemples expliquant comment utiliser ces API dans le référentiel [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether), sur GitHub.

 

<!---HONumber=AcomDC_1210_2015-->