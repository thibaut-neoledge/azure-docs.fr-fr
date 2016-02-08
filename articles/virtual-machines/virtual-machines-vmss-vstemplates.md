<properties
	pageTitle="Déploiement des modèles de jeux de mise à l’échelle de machines virtuelles dans Visual Studio | Microsoft Azure"
	description="Comment déployer un jeu de mise à l’échelle de machines virtuelles Azure à l'aide d'un déploiement de groupe de ressources Visual Studio."
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="guybo"/>

# Déploiement des modèles de jeux de mise à l’échelle de machines virtuelles dans Visual Studio
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).

Cet article vous indique comment déployer un jeu de mise à l’échelle de machines virtuelles Azure à l'aide d'un déploiement de groupe de ressources Visual Studio.


Les [jeux de mise à l’échelle de machines virtuelles Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) représentent une ressource de calcul Azure qui permet de déployer et de gérer une collection de machines virtuelles similaires via des options de mise à l'échelle automatique et d'équilibrage de charge facilement intégrées. Vous pouvez configurer et déployer des jeux de mise à l'échelle de machines virtuelles à l'aide de [modèles Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates). Les modèles ARM peuvent être déployés à l'aide de l’interface de ligne de commande (CLI) Azure, de PowerShell, de REST et directement à partir de Visual Studio. Visual Studio fournit des exemples de modèles qui peuvent être déployés dans le cadre d'un projet de déploiement de groupe de ressources Azure.

Les déploiements de groupe de ressources Azure vous permettent de regrouper et de publier un ensemble de ressources Azure connexes dans une même opération de déploiement. Pour en savoir plus, consultez la rubrique [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/)

## Conditions préalables

Pour commencer le déploiement de jeux de mise à l'échelle de machines virtuelles dans Visual Studio, vous devez disposer des éléments suivants :

- Visual Studio 2013 ou 2015
- Kit de développement logiciel (SDK) Azure 2.7 ou 2.8

Remarque : ces instructions partent du principe que vous utilisez Visual Studio 2015 avec le [Kit de développement logiciel (SDK) Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## Création d’un projet

1. Créez un projet dans Visual Studio 2015 en sélectionnant **Fichier | Nouveau | Projet**.

	![Fichier Nouveau][file_new]

2. Sous **Visual C# | Cloud**, sélectionnez **Azure Resource Manager** pour créer un projet de déploiement d'un modèle ARM.

	![Créer un projet][create_project]

3.  Depuis la liste des modèles, sélectionnez le modèle de jeux de mise à l'échelle de machines virtuelles Windows ou Linux.

	![Sélectionner un modèle][select_Template]

4. Une fois votre projet créé, des scripts de déploiement PowerShell, un modèle Azure Resource Manager et un fichier de paramètres pour le jeu de mise à l'échelle de machines virtuelles apparaîtront.

	![Explorateur de solutions][solution_explorer]

## Personnalisation de votre projet

Vous pouvez modifier dès à présent le modèle pour le personnaliser en fonction des besoins de votre application, en ajoutant par exemple des propriétés d'extension de machines virtuelles ou en modifiant les règles d'équilibrage de charge. Par défaut, les modèles de jeu de mise à l'échelle de machines virtuelles sont configurés pour déployer l'extension AzureDiagnostics qui permet d’ajouter très facilement des règles de mise à l'échelle automatique. Ceci déploie également un équilibreur de charge via une adresse IP publique, configuré avec des règles NAT entrantes qui permettent de vous connecter aux instances de machine virtuelle avec SSH (Linux) ou RDP (Windows). La plage de ports frontaux commence à 50 000, ce qui signifie, dans le cas de Linux, que si vous utilisez SSH sur le port 50 000 de l'adresse IP publique (ou du nom de domaine), vous êtes redirigé vers le port 22 de la première machine virtuelle dans le jeu de mise à l'échelle. La connexion au port 50 001 sera acheminée vers le port 22 de la deuxième machine virtuelle et ainsi de suite.

 Un bon moyen de modifier vos modèles avec Visual Studio consiste à utiliser le plan JSON pour organiser des paramètres, des variables et des ressources. En comprenant le schéma, Visual Studio peut signaler les erreurs figurant dans votre modèle avant de le déployer.

![JSON Explorer][json_explorer]

## Déployer le projet

6. Déployer le modèle ARM vers Azure pour créer la ressource de jeu de mise à l’échelle de machines virtuelles. Cliquez avec le bouton droit de la souris sur le nœud du projet et sélectionnez **Déployer | Nouveau déploiement**.

	![Déployer un modèle][5deploy_Template]

7. Sélectionnez votre abonnement dans la boîte de dialogue « Déploiement vers un groupe de ressources ».

	![Déployer un modèle][6deploy_Template]

8. À ce stade, vous pouvez également créer un groupe de ressources Azure sur lequel déployer votre modèle.

	![Nouveau groupe de ressources][new_resource]

9. Sélectionnez ensuite le bouton **Modifier les paramètres** pour saisir des paramètres qui seront transmis à votre modèle. Certaines valeurs, telles que le nom d'utilisateur et le mot de passe du système d'exploitation, sont requises pour créer le déploiement.

	![Modifier les paramètres][edit_parameters]

10. Cliquez maintenant sur **Déployer**. La fenêtre **Sortie** affiche la progression du déploiement. Notez que l'action exécute le script **Deploy-AzureResourceGroup.ps1**.

	![Fenêtre Sortie][output_window]

## Exploration de votre jeu de mise à l’échelle de machines virtuelles

Une fois le déploiement terminé, vous pouvez afficher le nouveau jeu de mise à l'échelle de machines virtuelles dans le **Cloud Explorer** de Visual Studio (actualisez la liste). Cloud Explorer vous permet de gérer des ressources Azure dans Visual Studio lors du développement d'applications. Vous pouvez également afficher votre jeu de mise à l’échelle de machines virtuelles dans le portail Azure et Azure Resource Explorer.

![Cloud Explorer][cloud_explorer]

 Le portail vous explique comment gérer visuellement votre infrastructure Azure via un navigateur Web, tandis qu’Azure Resource Explorer vous permet d’explorer et de déboguer très facilement des ressources Azure, en vous donnant un aperçu de la « vue d’instance » et en vous indiquant également les commandes PowerShell pour les ressources que vous recherchez. Bien que les jeux de mise à l'échelle de machines virtuelles soient en version préliminaire, Resource Explorer fournit des informations très détaillées sur vos jeux de mise à l'échelle de machines virtuelles.

## Étapes suivantes

Une fois les jeux de mise à l'échelle de machines virtuelles déployés avec succès via Visual Studio, vous pouvez personnaliser davantage votre projet en fonction des besoins de votre application. Vous pouvez, par exemple, configurer la mise à l'échelle automatique via l’ajout d’une ressource Insights, l’ajout d'une infrastructure à votre modèle, telles que des machines virtuelles autonomes, ou le déploiement d’applications à l'aide de l'extension de script personnalisé. Vous trouverez ici de bons exemples de modèles parmi le référentiel GitHub [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates) (recherchez le terme « vmss »).

[file_new]: ./media/virtual-machines-vmss-vstemplates/1-FileNew.png
[create_project]: ./media/virtual-machines-vmss-vstemplates/2-CreateProject.png
[select_Template]: ./media/virtual-machines-vmss-vstemplates/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-vmss-vstemplates/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-vmss-vstemplates/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-vmss-vstemplates/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-vmss-vstemplates/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-vmss-vstemplates/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-vmss-vstemplates/8-EditParameter.png
[output_window]: ./media/virtual-machines-vmss-vstemplates/9-Output.png
[cloud_explorer]: ./media/virtual-machines-vmss-vstemplates/12-CloudExplorer.png

<!---HONumber=AcomDC_0128_2016-->