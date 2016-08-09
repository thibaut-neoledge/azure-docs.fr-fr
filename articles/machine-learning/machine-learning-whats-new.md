<properties
	pageTitle="Nouveautés de Machine Learning | Microsoft Azure"
	description="De nouvelles fonctionnalités sont disponibles dans Microsoft Azure Learning."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="v-donglo"/>

# Nouveautés dans Azure Machine Learning

La mise à jour de juillet 2016 de Microsoft Azure Machine Learning (ML) inclut les fonctionnalités suivantes :

* Les services web sont désormais gérés en tant que ressources Azure via les interfaces [Azure Resource Manager](../resource-group-overview.md), ce qui permet les améliorations suivantes :
	* Des nouvelles [API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) pour déployer et gérer vos services web basés sur Resource Manager.
	* Un nouveau portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/) qui fournit un emplacement pour gérer tous les aspects de votre service web.
* Intègre un nouveau modèle de déploiement de service web multi-régions et basé sur un abonnement, qui utilise des API basées sur Resource Manager afin d’exploiter le fournisseur de ressources Resource Manager pour les services web.
* Présente de nouveaux [plans de tarification](https://azure.microsoft.com/pricing/details/machine-learning/) et des capacités de gestion en utilisant le nouveau fournisseur de ressources Resource Manager pour la facturation.
	* Vous pouvez à présent [déployer votre service web dans plusieurs régions](machine-learning-how-to-deploy-to-multiple-regions.md) sans avoir à créer un nouvel abonnement dans chaque région.
* Fournit des [statistiques d’utilisation](machine-learning-manage-new-webservice.md) du service web.
* Simplifie le test de la fonction Azure ML RRS à l’aide d’exemples de données.
* Fournit une nouvelle page de test BES avec des exemples de données et un historique des travaux soumis.

En outre, Machine Learning Studio a été mis à jour afin que vous puissiez déployer vers le nouveau modèle de service web ou continuer à déployer vers le modèle de service web classique.

<!---HONumber=AcomDC_0727_2016-->