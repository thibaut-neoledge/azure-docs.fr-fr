<properties title="Sample Web Services Built with R on Azure ML and Published to Marketplace" pageTitle="Exemples de services web créés avec R sur Azure ML et publiés sur Marketplace | Azure" description="Sample Web Services Built with R on Azure ML and Published to Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 


#Exemples de services web créés avec R sur Azure ML et publiés sur Marketplace




Avec Azure Machine Learning Studio, les utilisateurs peuvent écrire du code R et le publier en quelques clics sous la forme d'un service web afin que d'autres personnes et périphériques l'utilise dans le monde entier. De la création de simples calculatrices qui fournissent des fonctionnalités de statistiques à la création d'un facteur personnalisé de prédiction d'analyse de sentiment dans l'exploration de textes, par exemple, les utilisateurs R nouveaux et expérimentés peuvent bénéficier de la facilité avec laquelle les utilisateurs d'Azure ML exploitent le code R. Bien que ces services web puissent être utilisés par les utilisateurs, via une application mobile ou un site web, leur objectif est également de servir d'exemple pour expliquer comment Azure ML peut exploiter les scripts R à des fins analytiques et peut être utilisé pour créer des services web sur le code R.

Dans cette page, vous trouverez de nombreux exemples de services web qui ont été créés à l'aide d'Azure ML et publiés sur Microsoft Azure Marketplace. À chaque service web est joint un document complet intégrant des exemples de jeux de données pour tester les services et expliquant comment l'utilisateur peut lui-même créer un service similaire. 

![Workflow][1]

Examinez les scénarios suivants :

####Scenario 1 : modèle générique 
Un utilisateur travaille avec un modèle générique qui peut être appliqué aux données d'un nouvel utilisateur, par exemple une prévision de données de série temporelles ou une méthode R personnalisée avec des analyses avancées. Cet utilisateur publie le modèle sous forme de service web afin que d'autres utilisent ses données.

* [Classifieur binaire](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [Modèle de cluster](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [Régression linéaire multivariable](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [Prévisions - Lissage exponentiel](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [Prévisions ETS + STL](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [Prévisions : modèle autorégressif à moyenne mobile intégrée (ARIMA)](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-arima/)
* [Analyse de survie](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####Scenario 2 : modèle formé - données spécifiques 
Un utilisateur dispose de données qui fournissent des prédictions utiles dans le code R. Par exemple : un échantillon volumineux de questionnaires de personnalité regroupés via un algorithme à k moyennes pour prédire le type de personnalité de l'utilisateur ou des données d'enquête de santé qui peuvent être utilisées pour prédire le risque qu'un individu contracte un cancer du poumon, à l'aide d'un package R d'analyse de survie. L'utilisateur publie les données via un service web qui prédit les résultats d'un nouvel utilisateur.

####Scenario 3 : modèle formé - données génériques 
Un utilisateur possède des données génériques (par exemple, un corpus de texte) qui permettent la création d'un service web pouvant être appliqué de manière générique dans différents types de scénarios et cas d'usage.

* [Analyse de sentiments basée sur un lexique](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####Scenario 4 : calculatrice avancée 
Un utilisateur fournit des calculs complexes ou des simulations ne nécessitant aucun modèle formé ni ajustement d'un modèle pour les données de l'utilisateur.

* [Test de différence des proportions](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [Normal Distribution Suite](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [Suite de distribution binomiale](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l'utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png


