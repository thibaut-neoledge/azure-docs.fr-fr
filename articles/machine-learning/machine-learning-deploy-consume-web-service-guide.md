<properties
	pageTitle="Services web Azure Machine Learning : déploiement et consommation | Microsoft Azure"
	description="Ressources pour le déploiement et la consommation de services web."
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
	ms.date="08/19/2016"
	ms.author="v-donglo"/>

# Services web Azure Machine Learning : déploiement et consommation

Vous pouvez utiliser Azure Machine Learning pour déployer des flux de travail et des modèles de Machine Learning en tant que services web. Ces services web peuvent ensuite servir à appeler les modèles Machine Learning à partir d’applications via Internet pour effectuer des prévisions en temps réel ou par lots. Les services web, RESTful, peuvent être appelés avec divers langages et plateformes de programmation, notamment Java, .NET et des applications comme Excel.

Les sections suivantes fournissent des liens vers des procédures pas à pas, du code et de la documentation pour vous aider à démarrer.

## Déployer un service web

### Avec Azure Machine Learning Studio

Machine Learning Studio et le portail des services web Microsoft Azure Machine Learning vous permettent de déployer et de gérer un service web sans avoir à écrire du code.

Les liens suivants fournissent des informations générales sur le processus de déploiement d’un nouveau service web :

* Pour une vue d’ensemble du déploiement d’un nouveau service web basé sur Azure Resource Manager, consultez [Déployer un nouveau service web](machine-learning-webservice-deploy-a-web-service.md).
* Pour une présentation du déploiement d’un service web, consultez [Déployer un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).
* Pour une présentation complète de la création et du déploiement d’un service web, consultez [Étape 1 du didacticiel pas à pas : créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md).
* Pour des exemples spécifiques de déploiement d’un service web, consultez :

	* [Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md)
	* [Comment déployer un service web dans plusieurs régions](machine-learning-how-to-deploy-to-multiple-regions.md)

### Avec les API du fournisseur de ressources des services web (API Azure Resource Manager)

Le fournisseur de ressources Azure Machine Learning pour les services web permet le déploiement et la gestion des services web au moyen d’appels d’API REST. Pour plus de détails, consultez la référence [Service web Machine Learning (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) sur MSDN.

### Avec des applets de commande PowerShell

Le fournisseur de ressources Azure Machine Learning pour les services web permet le déploiement et la gestion des services web au moyen d’applets de commande PowerShell.

Pour utiliser les applets de commande, vous devez tout d’abord vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx). Si vous ne savez pas comment appeler les commandes PowerShell basées sur Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Pour exporter votre expérience prédictive, utilisez cet [exemple de code](https://github.com/ritwik20/AzureML-WebServices). Après avoir créé le fichier .exe à partir du code, vous pouvez taper :

	C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

L’exécution de l’application crée un modèle JSON de service web. Pour utiliser le modèle afin de déployer un service web, vous devez ajouter les informations suivantes :

* Nom et clé du compte de stockage

	Vous pouvez récupérer le nom et la clé du compte de stockage à partir du [portail Azure](https://portal.azure.com/) ou du [portail Azure Classic](http://manage.windowsazure.com/).
* ID de plan d’engagement

	Vous pouvez récupérer l’ID du plan sur le portail des [services web Azure Machine Learning](https://services.azureml.net) en vous connectant et en cliquant sur le nom d’un plan.

Ajoutez-les au modèle JSON en tant qu’enfants du nœud *Propriétés* au même niveau que le nœud *MachineLearningWorkspace*.

Voici un exemple :

	"StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
	},
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consultez les articles et les exemples de code suivants pour plus de détails :

* Référence [Applets de commande Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) sur MSDN
* Exemple de [procédure pas à pas](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) sur GitHub

## Utiliser des services web

### À partir de l’interface utilisateur des services web Azure Machine Learning (test)

Vous pouvez tester votre service web sur le portail de services web Azure Machine Learning. Cela comprend le test du service de requête-réponse (RRS) et des interfaces de service d’exécution de lots (BES).

* [Déployer comme un nouveau service web](machine-learning-webservice-deploy-a-web-service.md)
* [Déploiement d’un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
* [Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md)

### À partir d’Excel

Vous pouvez télécharger un modèle Excel qui consomme le service web :

* [Utilisation d’un service web Microsoft Azure Machine Learning à partir de Microsoft Excel.](machine-learning-consuming-from-excel.md)
* [Complément Excel de services web Azure Machine Learning](machine-learning-excel-add-in-for-web-services.md)


### À partir d’un client basé sur REST

Les services web Azure Machine Learning sont des API RESTful. Vous pouvez consommer ces API à partir de différentes plateformes, notamment .NET, Python, R, Java, etc. La page **Consommer** de votre service web sur le [portail des services web Microsoft Azure Machine Learning](https://services.azureml.net) contient des exemples de code susceptibles de vous aider à démarrer. Pour plus d’informations, consultez [Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](machine-learning-consume-web-services.md).

<!---HONumber=AcomDC_0907_2016-->