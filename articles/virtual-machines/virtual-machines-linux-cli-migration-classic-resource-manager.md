<properties
	pageTitle="Migration de ressources IaaS de l’environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
	description="Cet article décrit pas à pas la procédure de migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure

Ces étapes vous montrent comment utiliser les commandes de l’interface de ligne de commande Azure (CLI) pour migrer des ressources d’infrastructure en tant que service (IaaS) à partir du modèle de déploiement Classic vers le modèle de déploiement Azure Resource Manager. Cet article nécessite une connexion à [l’interface de ligne de commande Azure](../xplat-cli-install.md) (`azure login`).

## Étape 1 : préparation de la migration

Voici quelques bonnes pratiques recommandées lorsque vous évaluez la migration de ressources IaaS d’un environnement Classic vers Resource Manager.

- Parcourez la liste des [configurations ou fonctionnalités non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez des machines virtuelles qui utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
-	Si vous avez déjà des scripts automatisés qui déploient votre infrastructure et vos applications, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Vous pouvez également configurer des environnements de test à l’aide du portail Azure.

## Étape 2 : Configurer votre abonnement et s’inscrire à la migration

Pour les scénarios de migration, vous devez configurer votre environnement à la fois pour l’environnement Classic et pour Resource Manager. [Installez l’interface de ligne de commande Azure](../xplat-cli-install.md) et [sélectionnez votre abonnement](../xplat-cli-connect.md).

Sélectionnez votre abonnement Azure à l’aide de la commande suivante :

	azure account set "azure-subscription-name"

>[AZURE.NOTE] L’inscription constitue une étape unique, mais elle doit être effectuée une seule fois avant de tenter la migration. Sans vous inscrire, vous verrez le message suivant

>	*BadRequest : Subscription is not registered for migration.* 

Inscrivez-vous auprès du fournisseur de ressources de migration à l’aide de la commande suivante. Notez que cette commande expire dans certains cas. Toutefois, l’inscription sera réussie.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Veuillez patienter cinq minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Assurez-vous que RegistrationState est `Registered` avant de continuer.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Passez ensuite l’interface de ligne de commande en mode `asm`.

	azure config mode asm

## Étape 3 : exécution de commandes pour effectuer la migration de vos ressources IaaS

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme tentera une nouvelle fois l’opération.

### Migration de machines virtuelles dans un service cloud (ne figurant pas dans un réseau virtuel)

Obtenez la liste des services cloud à l’aide de la commande suivante, puis choisissez le service cloud que vous voulez migrer. Notez que vous obtiendrez un message d’erreur si les machines virtuelles du service cloud sont dans un réseau virtuel ou si elles ont des rôles Web/de travail.

	azure service list

Exécutez la commande suivante pour obtenir le nom du déploiement du service cloud à partir d’une sortie détaillée. Dans la plupart des cas, le nom du déploiement est le même que le nom de service Cloud.

	azure service show servicename -vv

Préparez les machines virtuelles dans le service cloud pour la migration. Vous disposez de deux options.

Si vous voulez migrer les machines virtuelles vers un réseau virtuel créé à partir d’une plateforme, utilisez la commande suivante.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Si vous voulez procéder à leur migration vers un réseau virtuel existant dans le modèle de déploiement Resource Manager, utilisez la commande suivante.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

Une fois l’opération de préparation réussie, vous pouvez consulter la sortie détaillée pour voir l’état de la migration des machines virtuelles et vous assurer que leur état est `Prepared`.

	azure vm show vmName -vv

Vérifiez la configuration pour les ressources préparées à l’aide de l’interface de ligne de commande ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

	azure service deployment abort-migration serviceName deploymentName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

	azure service deployment commit-migration serviceName deploymentName

### Migration de machines virtuelles dans un réseau virtuel

Sélectionnez le réseau virtuel dont vous souhaitez effectuer la migration. Sachez que, si le réseau virtuel contient des rôles Web/de travail ou des machines virtuelles avec des configurations non prises en charge, vous obtiendrez un message d’erreur pour la validation.

Obtenez tous les réseaux virtuels de l’abonnement à l’aide de la commande suivante.

	azure network vnet list

Préparez le réseau virtuel de votre choix pour la migration à l’aide de la commande suivante.

	azure network vnet prepare-migration virtualnetworkname

Vérifiez la configuration pour les machines virtuelles préparées à l’aide de l’interface de ligne de commande ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

	azure network vnet abort-migration virtualnetworkname

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

	azure network vnet commit-migration virtualnetworkname

### Migrer un compte de stockage

Une fois que vous avez terminé la migration des machines virtuelles, nous vous recommandons de migrer le compte de stockage.

Préparer le compte de stockage pour la migration à l’aide de la commande suivante

	azure storage account prepare-migration storageaccountname

Vérifiez la configuration pour le compte de stockage préparé à l’aide de l’interface de ligne de commande ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

	azure storage account abort-migration storageaccountname

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

	azure storage account commit-migration storageaccountname

## Étapes suivantes

- [Migration prise en charge par la plateforme de ressources IaaS de l’environnement Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0706_2016-->