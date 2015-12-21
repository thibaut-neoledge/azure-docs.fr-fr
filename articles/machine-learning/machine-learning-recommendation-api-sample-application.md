<properties 
	pageTitle="Opérations courantes dans l'API Machine Learning Recommendations | Microsoft Azure" 
	description="Exemple d'application Azure ML Recommendation" 
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
	ms.date="12/07/2015" 
	ms.author="luisca"/>


# Opérations courantes dans l'API de Machine Learning Recommendations

##Objectif

Ce document illustre l’utilisation de l’API d’Azure Machine Learning Recommendations, via une [version d’évaluation de l’application](http://1drv.ms/1xeO2F3).

Cette application n’est pas conçue pour inclure toutes les fonctionnalités ou pour utiliser toutes les API. Elle présente quelques unes des opérations courantes à effectuer lorsque vous souhaitez vous familiariser avec le service de recommandations de Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Introduction au service de recommandation de Machine Learning

Recommandations via le service de recommandations d’Azure Machine Learning est activé lorsque vous créez un modèle de recommandation basé sur les données suivantes :

* Un référentiel des éléments que vous souhaitez recommander, autrement dit, un catalogue
* Les données représentant l’utilisation des éléments par session ou utilisateur (vous pouvez progressivement acquérir ces données via l’acquisition de données ; ne fait pas partie de la version d’évaluation de l’application.)

Une fois qu’un modèle de recommandation a été généré, vous pouvez l’utiliser pour prédire des éléments qui pourraient intéresser un utilisateur, selon un ensemble d’éléments (ou un élément unique) qu’il a sélectionné.

Afin de permettre le scénario ci-dessus, procédez comme suit dans le service Machine Learning Recommandation :

* Créez un modèle : il s’agit d’un conteneur logique qui contient les données (de catalogue et d’utilisation) et le ou les modèles de prévision. Chaque conteneur de modèle est identifié par un identifiant unique attribué lors de sa création. Cet identifiant, appelé identifiant de modèle, est utilisé par la plupart des API. 
* Téléchargez un catalogue : une fois qu’un conteneur de modèle est créé, vous pouvez lui associer un catalogue.

**Remarque** : les étapes de création d’un modèle et de téléchargement dans un catalogue sont généralement effectuées une fois pendant le cycle de vie du modèle.

* Téléchargez l’utilisation : pour ajouter des données d’utilisation au conteneur du modèle.
* Générez le modèle de recommandation : une fois que vous avez suffisamment de données, vous pouvez générer un modèle de recommandation. Cette opération utilise des algorithmes d’apprentissage automatique très performants pour créer un modèle de recommandation. Chaque génération est associée à un identifiant unique. Vous devez conserver un enregistrement de cet identifiant, car il est nécessaire au fonctionnement de certaines API.
* Surveillez le processus de génération : la génération d’un modèle de recommandation est une opération asynchrone et peut prendre de quelques minutes à plusieurs heures selon la quantité de données (de catalogue et d’utilisation) et les paramètres de génération. Vous devez donc surveiller la génération. Un modèle de recommandation est créé uniquement si sa génération associée est réalisée avec succès.
* (Facultatif) Choisissez la génération active d’un modèle de recommandation. Cette étape est nécessaire uniquement si vous avez associé plusieurs modèles de recommandation à votre conteneur de modèle. Toute demande d’obtention de recommandation sans indiquer le modèle de recommandation actif sera automatiquement redirigée par le système vers la génération active par défaut. 

**Remarque**: un modèle de recommandation actif est prêt pour la production et est conçu pour une charge de production. Il diffère d’un modèle de recommandation non actif, qui reste dans un environnement similaire à celui d’un test (parfois appelé intermédiaire).

* Obtenez la recommandation : une fois que vous disposez d’un modèle de recommandation, vous pouvez déclencher la recommandation pour un seul élément ou pour une liste d’éléments que vous avez sélectionnés. 

En général, l'appel de la fonction Obtenez une recommandation dure un certain temps. En attendant, vous pouvez rediriger les données d’utilisation vers le système de recommandation de Machine Learning qui les ajoutera au conteneur de modèle spécifié. Lorsque vous avez suffisamment de données d’utilisation, vous pouvez déclencher une nouvelle génération de modèle de recommandation qui intègre les données d’utilisation supplémentaires.

##Composants requis

* Visual Studio 2013
* Accès à Internet 
* Abonnement aux API Recommendations (https://datamarket.azure.com/dataset/amla/recommendations).

##Solution de la version d’évaluation de l’application Azure Machine Learning

Cette solution contient le code source, les fichiers d’utilisation et de catalogue en version d’évaluation, ainsi que des directives pour télécharger les packages requis pour la compilation.

##API utilisées

L’application utilise la fonctionnalité de recommandation de Machine Learning via un sous-ensemble des API disponibles. Les API suivantes sont illustrées dans l'application :

* Créez un modèle : créez le conteneur logique pour les modèles de recommandation et les données. Un modèle est identifié par un nom. Vous ne pouvez pas créer deux modèles portant le même nom.
* Téléchargez le fichier de catalogue : pour télécharger les données de catalogue.
* Téléchargez le fichier d’utilisation : pour télécharger les données d’utilisation.
* Déclenchez la génération : pour créer un modèle de recommandation.
* Surveillez l’exécution de la génération : surveillez l’état d’une génération de modèle de recommandation.
* Choisissez un modèle de recommandation généré : pour indiquer quel modèle de recommandation utiliser par défaut pour un certain conteneur de modèle. Cette étape est nécessaire uniquement si vous avez plusieurs modèles de recommandation et si vous souhaitez configurer une génération non active comme modèle de recommandation actif.
* Obtenez la recommandation : pour récupérer un élément recommandé en fonction d’un élément unique ou d’un ensemble d’éléments. 

Pour une description complète de l’API, consultez la documentation de Microsoft Azure Marketplace.

**Remarque**: un modèle peut posséder plusieurs générations au fil du temps (pas simultanément). Chaque génération a été créée avec le même catalogue ou un catalogue mis à jour et des données d'utilisation supplémentaires.

## Pièges courants

* Vous devez fournir votre nom d’utilisateur et votre clé de compte principal Microsoft Azure Marketplace pour exécuter la version d’évaluation.
* La version d'évaluation de l'application ne peut pas exécuter des étapes consécutives. Le flux d’application comprend la création, le téléchargement, la surveillance de la génération et l’obtention de recommandations à partir d’un modèle prédéfini. Par conséquent, elle échouera à exécuter des étapes consécutives si vous ne modifiez pas le nom du modèle entre les appels.
* Recommandation peut ne pas renvoyer de données. La version d’évaluation de l’application utilise un très petit catalogue et fichier d’utilisation. Certains éléments du catalogue n’ont donc aucun élément recommandé.

## Clause d'exclusion de responsabilité
La version d’évaluation n’est pas conçue pour être exécutée dans un environnement de production. Les données fournies dans le catalogue ne sont pas nombreuses et ne fournissent pas un modèle de recommandation éloquent. Elles sont seulement fournies à titre de démonstration.
 

<!---HONumber=AcomDC_1210_2015-->