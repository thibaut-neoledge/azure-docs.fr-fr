<properties
	pageTitle="Migration de ressources IaaS de l’environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell | Microsoft Azure"
	description="Cet article décrit pas à pas la procédure de migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager à l’aide de PowerShell"
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

# Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell

Ces étapes vous montrent comment utiliser les commandes Azure PowerShell pour migrer des ressources d’infrastructure en tant que service (IaaS) à partir du modèle de déploiement Classic vers le modèle de déploiement Azure Resource Manager. Ces étapes appliquent une approche de commandes à remplir pour la migration de votre environnement personnalisé. Il vous suffit d’utiliser les commandes et de remplacer les variables (lignes commençant par « $ ») par vos propres valeurs.

## Étape 1 : préparation de la migration

Voici quelques bonnes pratiques recommandées lorsque vous évaluez la migration de ressources IaaS d’un environnement Classic vers Resource Manager.

- Parcourez la liste des [configurations ou fonctionnalités non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez des machines virtuelles qui utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
-	Si vous avez déjà des scripts automatisés qui déploient votre infrastructure et vos applications, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Vous pouvez également configurer des environnements de test à l’aide du portail Azure.

## Étape 2 : installation de la dernière version d’Azure PowerShell

Il existe deux options principales d’installation : [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) et [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu.

Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/).

## Étape 3 : Configurer votre abonnement et s’inscrire à la migration

Commencez par démarrer une invite de commandes PowerShell. Pour les scénarios de migration, vous devez configurer votre environnement à la fois pour l’environnement Classic et pour Resource Manager.

Connectez-vous à votre compte pour le modèle Resource Manager.

	Login-AzureRmAccount

Obtenez tous les abonnements disponibles à l’aide de la commande suivante.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure pour la session active. Remplacez tous les éléments entre guillemets, y compris les caractères < et >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

>[AZURE.NOTE] L’inscription constitue une étape unique, mais elle doit être effectuée une seule fois avant de tenter la migration. Sans vous inscrire, vous verrez le message suivant

>	*BadRequest : Subscription is not registered for migration.* 

Inscrivez-vous auprès du fournisseur de ressources de migration à l’aide de la commande suivante.
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Veuillez patienter cinq minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Assurez-vous que RegistrationState est `Registered` avant de continuer.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Connectez-vous à votre compte pour le modèle Classic.

	Add-AzureAccount

Obtenez tous les abonnements disponibles à l’aide de la commande suivante.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure pour la session active. Remplacez tous les éléments entre guillemets, y compris les caractères < et >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Étape 4 : exécution de commandes pour effectuer la migration de vos ressources IaaS

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme tentera une nouvelle fois l’opération.

### Migration de machines virtuelles dans un service cloud (ne figurant pas dans un réseau virtuel)

Obtenez la liste des services cloud à l’aide de la commande suivante, puis choisissez le service cloud que vous voulez migrer. Notez que vous obtiendrez un message d’erreur si les machines virtuelles du service cloud sont dans un réseau virtuel ou si elles ont des rôles Web/de travail.

	Get-AzureService | ft Servicename

Obtenez le nom du déploiement du service cloud à l’aide de la commande suivante.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Préparez les machines virtuelles dans le service cloud pour la migration. Vous disposez de deux options.

Si vous voulez migrer les machines virtuelles vers un réseau virtuel créé à partir d’une plateforme, utilisez la commande suivante.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Si vous voulez procéder à leur migration vers un réseau virtuel existant dans le modèle de déploiement Resource Manager, utilisez la commande suivante.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

Une fois l’opération de préparation réussie, vous pouvez interroger l’état de la migration des machines virtuelles et vous assurer qu’elles sont à l’état `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Vérifiez la configuration pour les ressources préparées à l’aide de PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Migration de machines virtuelles dans un réseau virtuel

Sélectionnez le réseau virtuel dont vous souhaitez effectuer la migration. Sachez que, si le réseau virtuel contient des rôles Web/de travail ou des machines virtuelles avec des configurations non prises en charge, vous obtiendrez un message d’erreur pour la validation.

Préparez le réseau virtuel pour la migration à l’aide de la commande suivante.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Vérifiez la configuration pour les machines virtuelles préparées à l’aide de PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Migrer un compte de stockage

Une fois que vous avez terminé la migration des machines virtuelles, nous vous recommandons de migrer le compte de stockage.

Préparer le compte de stockage pour la migration à l’aide de la commande suivante

	$storageAccountName = "storagename"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Vérifiez la configuration pour le compte de stockage préparé à l’aide de PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Étapes suivantes

- [Migration prise en charge par la plateforme de ressources IaaS de l’environnement Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0706_2016-->