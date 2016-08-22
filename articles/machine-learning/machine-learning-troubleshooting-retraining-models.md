<properties
	pageTitle="Dépannage de la reformation d’un service web classique Azure Machine Learning | Microsoft Azure"
	description="Identifiez et corrigez les problèmes courants rencontrés lorsque vous reformez le modèle d’un service web Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="VDonGlover"
	manager=""
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="v-donglo"/>

#Dépannage de la reformation d’un service web classique Azure Machine Learning

## Vue d’ensemble de la reformation

Lorsque vous déployez une expérience prédictive en tant que service web d’évaluation, il s’agit d’un modèle statique. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, le modèle doit être reformé.

Pour une procédure pas à pas complète du processus de reformation d’un service web classique, consultez [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md).

## Processus de reformation

Lorsque vous devez reformer le service web, vous devez ajouter quelques éléments supplémentaires :

* Un service web déployé à partir de l’expérience de formation. L’expérience doit avoir un module de **sortie de service web** attaché à la sortie du module du **modèle de formation**.

	![Attachez la sortie de service web au modèle de formation.][image1]

* Un nouveau point de terminaison ajouté à votre service web d’évaluation. Vous pouvez ajouter le point de terminaison par programme à l’aide de l’exemple de code référencé dans la rubrique Reformation des modèles Machine Learning par programme ou via le portail Azure Classic.

Vous pouvez utiliser l’exemple de code C# de la page d’aide API du service web de formation pour reformer le modèle. Après avoir évalué les résultats et que vous en êtes satisfait, vous mettez à jour le service web d’évaluation du modèle formé à l’aide du nouveau point de terminaison que vous avez ajouté.

Une fois tous les éléments en place, les principales étapes à suivre pour reformer le modèle sont les suivantes :

1.	Appelez le service web de formation : l’appel est destiné au service d’exécution de lots (BES, Batch Execution Service), et non au service de requête-réponse (RRS, Request-Response Service). Vous pouvez utiliser l’exemple de code C# sur la page d’aide API pour effectuer l’appel.
2.	Rechercher les valeurs pour *BaseLocation*, *RelativeLocation* et *SasBlobToken* : ces valeurs sont retournées dans la sortie à partir de votre appel au service web de formation. ![affichage de la sortie de l’exemple de reformation et des valeurs BaseLocation, RelativeLocation et SasBlobToken.][image6]
3.	Mettez à jour le point de terminaison ajouté à partir du service web d’évaluation avec le nouveau modèle formé : à l’aide de l’exemple de code fourni dans la rubrique Reformation des modèles Machine Learning par programme, mettez à jour le nouveau point de terminaison que vous avez ajouté au modèle d’évaluation avec le modèle nouvellement formé à partir du service web de formation.

## Obstacles courants
### Vérifiez que vous disposez de l’URL d’application de correctifs appropriée

L’URL d’application de correctifs que vous utilisez doit être celle associée au nouveau point de terminaison d’évaluation que vous avez ajouté au service web d’évaluation. Il existe deux méthodes pour obtenir l’URL d’application de correctifs :

Option 1 : à l’aide de C#

Pour obtenir l’URL d’application de correctifs appropriée :

1.	Exécutez l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).
2.	À partir de la sortie d’AddEndpoint, recherchez la valeur *HelpLocation* et copiez l’URL.

	![HelpLocation dans la sortie de l’exemple addEndpoint.][image2]

3.	Collez l’URL dans un navigateur pour accéder à une page qui fournit des liens d’aide pour le service web.
4.	Cliquez sur le lien de **mise à jour des ressources** pour ouvrir la page d’aide d’application de correctifs.

Option 2 : à l’aide du portail Azure

1.	Connectez-vous au [portail Azure](https://manage.windowsazure.com) Classic.
2.	Ouvrez l’onglet Machine Learning. ![Onglet Machine Learning.][image4]
3.	Cliquez sur le nom de votre espace de travail, puis sur **Services Web**.
4.	Cliquez sur le service web d’évaluation avec lequel vous travaillez. (Si vous n’avez pas modifié le nom par défaut du service web, il se terminera généralement par [Scoring Exp.]).
5.	Cliquez sur Ajouter un point de terminaison.
6.	Après l’ajout du point de terminaison, cliquez sur le nom du point de terminaison. Cliquez ensuite sur **Mettre à jour la ressource** pour ouvrir la page d’aide d’application de correctifs.

![Nouveau tableau de bord de point de terminaison.][image3]

La page d’aide d’application de correctifs contient l’URL d’application de correctifs que vous devez utiliser et fournit un exemple de code que vous pouvez utiliser pour l’appeler.

![URL d’application de correctifs.][image5]


### Vérifiez que vous mettez à jour le point de terminaison d’évaluation approprié
* N’appliquez pas de correctifs au service web de formation : l’opération d’application de correctifs doit être effectuée sur le service web d’évaluation.
* N’appliquez pas de correctifs au point de terminaison par défaut du service web : l’opération d’application de correctifs doit être effectuée sur le nouveau point de terminaison du service web d’évaluation que vous avez ajouté.

Vérifiez que vous avez ajouté le point de terminaison au service web approprié

Le point de terminaison que vous utilisez pour reformer le modèle doit se trouver sur le service web d’évaluation et non sur le service web de formation. Vous pouvez vérifier sur quel service web se trouve le point de terminaison en visitant le portail Azure Classic.

1.	Connectez-vous au [portail Azure](https://manage.windowsazure.com) Classic.
2.	Ouvrez l’onglet Machine Learning. ![Interface utilisateur de l’espace de travail Machine Learning.][image4]
3.	Sélectionnez votre espace de travail.
4.	Cliquez sur **Services web**.
5.	Sélectionnez votre service web prédictif.
6.	Vérifiez que votre nouveau point de terminaison a été ajouté au service web.

### Vérifiez l’espace de travail dans lequel votre service web se trouve afin de vous assurer qu’il est dans la région appropriée.

1.	Connectez-vous au [portail Azure](https://manage.windowsazure.com) Classic.
2.	Sélectionnez Machine Learning dans le menu. ![Interface utilisateur de la région Machine Learning.][image4]
3.	Vérifiez l’emplacement de votre espace de travail.


<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png

<!---HONumber=AcomDC_0810_2016-->