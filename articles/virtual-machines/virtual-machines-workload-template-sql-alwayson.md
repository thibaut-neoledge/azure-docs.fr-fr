<properties
	pageTitle="SQL Server AlwaysOn à l’aide d’un modèle Azure Resource Manager | Microsoft Azure"
	description="Déployez facilement cinq serveurs qui prennent en charge SQL Server AlwaysOn à l’aide d’un modèle Resource Manager et du portail Azure, d’Azure PowerShell et de l’interface de ligne de commande Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Déployer SQL Server AlwaysOn à l’aide d’un modèle Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique Vous ne pouvez pas créer cette ressource avec le modèle de déploiement classique.

Suivez les instructions de cet article pour déployer SQL Server AlwaysOn à l’aide d’un modèle Resource Manager Azure. Ce modèle crée cinq machines virtuelles dans un nouveau réseau virtuel, sur deux sous-réseaux différents.

![](./media/virtual-machines-workload-template-sql-alwayson/five-server-sqlao.png)

Vous pouvez exécuter le modèle avec le portail Azure, Azure PowerShell ou l’interface de ligne de commande Azure.

## Portail Azure

Pour déployer cette charge de travail à l’aide d’un modèle Azure Resource Manager et du portail Azure, cliquez [ici](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsql-server-2014-alwayson-dsc%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sql-alwayson/azure-portal-template.png)

1.	Pour le panneau **Modèle**, cliquez sur **Enregistrer**.
2.	Cliquez sur **Paramètres**. Dans le panneau **Paramètres**, tapez les nouvelles valeurs, effectuez votre sélection parmi les valeurs autorisées ou acceptez les valeurs par défaut, puis cliquez sur **OK**.
3.	Si nécessaire, cliquez sur **Abonnement**, puis sélectionnez l’abonnement Azure approprié.
4.	Cliquez sur **Groupe de ressources**, puis sélectionnez un groupe de ressources existant. Sinon, cliquez sur **Ou créer nouveau** pour créer un nouveau groupe de ressources pour cette charge de travail.
5.	Si nécessaire, cliquez sur **Emplacement du groupe de ressources**, puis sélectionnez l’emplacement Azure approprié.
6.	Si nécessaire, cliquez sur **Mentions légales** afin de passer en revue les conditions d’utilisation de ce modèle.
7.	Cliquez sur **Create**.

Selon le modèle, la génération de la charge de travail par Azure peut prendre un certain temps. Lorsque l’exécution du modèle est terminée, vous disposez d’une nouvelle configuration SQL Server à cinq serveurs dans votre groupe de ressources nouveau ou existant.

## Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Saisissez un nom de déploiement Azure, un nouveau nom de groupe de ressources et un emplacement de centre de données Azure dans l’ensemble de commandes suivant. Supprimez les éléments entre guillemets, notamment les caractères < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Voici un exemple.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Ensuite, exécutez votre bloc de commande dans l’invite Azure PowerShell.

Lorsque vous exécutez la commande **New-AzureRmResourceGroupDeployment**, vous êtes invité à fournir les valeurs d’une série de paramètres. Une fois que vous avez spécifié l’ensemble des valeurs de paramètres, la commande **New-AzureRmResourceGroupDeployment** crée et configure les machines virtuelles.

Lorsque l’exécution du modèle est terminée, vous disposez d’une nouvelle configuration SQL Server à cinq serveurs dans votre nouveau groupe de ressources.

## Interface de ligne de commande Azure

Avant de commencer, assurez-vous de disposer de la version appropriée de l’interface de ligne de commande Azure, d’être connecté et d’être passé sur le nouveau mode Gestionnaire des ressources. Pour plus d’informations, cliquez [ici](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Tout d’abord, vous créez un nouveau groupe de ressources. Exécutez la commande suivante et définissez le nom du groupe et l’emplacement du centre de données Azure dans lequel vous souhaitez effectuer le déploiement.

	azure group create <group name> <location>

Ensuite, exécutez la commande suivante et définissez le nom de votre nouveau groupe de ressources et le nom d’un déploiement Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json <group name> <deployment name>

Voici un exemple.

	azure group create sqlao eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json sqlao sqldevtest

Lors de l’exécution de la commande **azure group deployment create**, vous êtes invité à renseigner les valeurs d’une série de paramètres. Une fois que vous avez défini l’ensemble des valeurs de paramètres, Azure crée et configure les machines virtuelles.

Lorsque l’exécution du modèle est terminée, vous disposez d’une nouvelle configuration SQL Server à cinq serveurs dans votre nouveau groupe de ressources.


## Ressources supplémentaires

[Déployer et gérer des machines virtuelles à l’aide de modèles Azure Resource Manager et d’Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d’Azure Resource Manager](../resource-group-overview.md)

[Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=AcomDC_1203_2015-->