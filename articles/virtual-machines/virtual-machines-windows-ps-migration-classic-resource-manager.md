<properties
	pageTitle="Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell"
	description="Cet article décrit pas à pas les fonctionnalités du service de migration de ressources prise en charge par la plateforme vers Azure Resource Manager à l’aide de scripts PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell

Cette procédure vous indique comment utiliser les commandes Azure PowerShell pour effectuer la migration de ressources IaaS Classic vers Resource Manager. Ces étapes appliquent une approche de commandes à remplir pour la migration de votre environnement personnalisé. Il vous suffit d’utiliser les commandes et de remplacer les variables (lignes commençant par « $ ») par vos propres valeurs.

## Étape 1 : préparation de la migration

Voici quelques recommandations à prendre en compte lorsque vous évaluez la migration des ressources IaaS Classic vers Resource Manager.

- Parcourez la liste des configurations ou fonctionnalités non prises en charge [ici](virtual-machines-windows-migration-classic-resource-manager.md). Si certaines de vos machines virtuelles utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
-	Si vous utilisez des scripts automatisés qui déploient votre infrastructure et vos applications dès aujourd’hui, essayez de créer une configuration de test semblable en utilisant ces scripts pour la migration. Vous pouvez également configurer des exemples d’environnements à l’aide du Portail Azure.
- Étant donné que le service est en version préliminaire publique, assurez-vous que votre environnement de test pour la migration est isolé de votre environnement de production. Ne mélangez pas les comptes de stockage, les réseaux virtuels ou les autres ressources entre l’environnement de test et celui de production.

## Étape 2 : installer la dernière version d’Azure PowerShell

Il existe deux options principales pour l’installation : [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) et [WebPI](http://aka.ms/webpi-azps). WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu.

Pour plus d’informations, voir [Azure Powershell 1.0](https://azure.microsoft.com//blog/azps-1-0/).

## Étape 3 : configurer votre abonnement et s’inscrire à la version préliminaire publique de migration

Commencez par démarrer une invite de commandes PowerShell. Pour les scénarios de migration, vous devez configurer votre environnement à la fois pour Classic et pour Resource Manager.

Connectez-vous à votre compte pour le modèle Resource Manager.

	Login-AzureRmAccount

Obtenez tous les abonnements disponibles à l’aide de la commande suivante.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure pour la session active. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Inscrivez-vous pour la version préliminaire publique à l’aide de la commande suivante.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Veuillez patienter 5 minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Assurez-vous que RegistrationState est défini sur « Inscrit » avant de continuer.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Connectez-vous à votre compte pour le modèle Classic.

	Add-AzureAccount

Obtenez tous les abonnements disponibles à l’aide de la commande suivante.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure pour la session active. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Étape 4 : commandes pour effectuer la migration de vos ressources IaaS

>[AZURE.NOTE] Notez que toutes les opérations décrites ci-dessous sont idempotent. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation et d’abandon ou de validation pour que la plateforme tente une nouvelle fois l’opération.

### Migration de machines virtuelles dans un service cloud (ne figurant pas dans un réseau virtuel)

Obtenez la liste des services cloud à l’aide de la commande ci-après, puis choisissez le service cloud dont vous souhaitez effectuer la migration. Notez que si les machines virtuelles du service cloud se trouvent dans un réseau virtuel ou qu’elles ont des rôles Web/de travail, vous obtiendrez un message d’erreur.

	Get-AzureService | ft Servicename

Obtenez le nom du déploiement du service cloud à l’aide de la commande suivante.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Préparez les machines virtuelles dans le service cloud pour la migration. Vous disposez de deux options.

Si vous voulez effectuer la migration des machines virtuelles vers un réseau virtuel créé par la plateforme, utilisez la commande ci-dessous.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Si vous voulez procéder à leur migration vers un réseau virtuel existant dans le modèle de déploiement Resource Manager, utilisez la commande ci-dessous.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

Une fois l’opération de préparation réussie, vous pouvez exécuter une requête pour connaître l’état de la migration de la machine virtuelle et vous assurer que la machine virtuelle présente l’état « Préparé ».

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Vérifiez la configuration des ressources préparées soit à l’aide de PowerShell, soit à l’aide du Portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande ci-dessous.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande ci-dessous.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Migration de machines virtuelles dans un réseau virtuel

Sélectionnez le réseau virtuel dont vous souhaitez effectuer la migration. Notez que si le réseau virtuel contient des rôles Web/de travail ou des machines virtuelles avec des configurations non prises en charge, vous obtiendrez un message d’erreur pour la validation.

Préparez le réseau virtuel pour la migration à l’aide de la commande ci-dessous.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Vérifiez la configuration des machines virtuelles préparées soit à l’aide de PowerShell, soit à l’aide du Portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande ci-dessous.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande ci-dessous.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## Références

- [Migration prise en charge par la plateforme de ressources IaaS Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->