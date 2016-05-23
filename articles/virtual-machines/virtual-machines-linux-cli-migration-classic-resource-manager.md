<properties
	pageTitle="Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure"
	description="Cet article décrit pas à pas la procédure de migration de ressources prise en charge par la plateforme vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure."
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

# Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure

Ces étapes vous montrent comment utiliser les commandes de l’interface de ligne de commande Azure pour faire migrer les ressources IaaS de Classic à Resource Manager. Cet article nécessite une connexion à [l’interface de ligne de commande Azure](../xplat-cli-install.md) (`azure login`).

## Étape 1 : Préparation de la migration

Voici quelques recommandations à prendre en compte lorsque vous évaluez la migration des ressources IaaS migration de Classic à Resource Manager.

- Parcourez la liste des configurations ou fonctionnalités non prises en charge [ici](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez des machines virtuelles qui utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
-	Si vous avez des scripts automatisés qui déploient votre infrastructure et vos applications dès aujourd’hui. Essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Vous pouvez également configurer des environnements de test à l’aide du portail Azure.
- Étant donné que le service est en version préliminaire publique, assurez-vous que votre environnement de test pour la migration est isolé de votre environnement de production. Ne mélangez pas les comptes de stockage, les réseaux virtuels ou les autres ressources entre l’environnement de test et celui de production.

## Étape 2 : Configurer votre abonnement et s’inscrire à la version préliminaire publique de migration

Pour les scénarios de migration, vous devez définir votre environnement à la fois pour Classic et pour Resource Manager. [Installez l’interface de ligne de commande Azure](../xplat-cli-install.md) et [sélectionnez votre abonnement](../xplat-cli-connect.md).

Sélectionnez votre abonnement Azure, à l’aide de la commande suivante :

	azure account set "azure-subscription-name"

Inscrivez-vous pour la version préliminaire publique à l’aide de la commande suivante. Sachez que même si dans certains cas cette commande arrive à expiration, l’inscription se fait. Suivez l’étape suivante pour vérifier l’état de l’inscription.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Veuillez patienter 5 minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Assurez-vous que RegistrationState est défini sur « Inscrit » avant de continuer.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Passez ensuite l’interface de ligne de commande en mode asm.

	azure config mode asm

## Étape 3 : Commandes pour migrer vos ressources IaaS

>[AZURE.NOTE] Notez que toutes les opérations décrites ci-dessous sont idempotent. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation et d’abandon ou de validation pour que la plateforme tente une nouvelle fois l’opération.

### Migration des machines virtuelles dans un service Cloud (pas sur un réseau virtuel)

Obtenez la liste des services Cloud à l’aide de la commande suivante, puis choisissez le service Cloud que vous voulez faire migrer. Sachez que si les machines virtuelles du service Cloud sont dans un réseau virtuel ou si elles ont des rôles Web/de travail, vous obtiendrez un message d’erreur.

	azure service list

Exécutez la commande suivante pour obtenir le nom du déploiement du service Cloud à partir d’une sortie détaillée. Dans la plupart des cas, le nom du déploiement est le même que le nom de service Cloud.

	azure service show servicename -vv

Préparez les machines virtuelles dans le service Cloud pour la migration. Vous avez deux options.

Si vous voulez faire migrer les machines virtuelles vers un réseau virtuel créé à partir d’une plateforme, utilisez la commande ci-dessous.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Si vous voulez les faire migrer vers un réseau virtuel existant dans le modèle de déploiement Resource Manager, utilisez la commande ci-dessous.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

Une fois l’opération de préparation réussie, vous pouvez consulter la sortie détaillée pour voir l’état de la migration de la machine virtuelle et vous assurer que son état est « Préparé ».

	azure vm show vmName -vv

Vérifiez la configuration des ressources préparées soit à l’aide de l’interface de ligne de commande, soit à l’aide du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande ci-dessous.

	azure service deployment abort-migration serviceName deploymentName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande ci-dessous.

	azure service deployment commit-migration serviceName deploymentName

### Migration des machines virtuelles dans un réseau virtuel

Sélectionnez le réseau virtuel que vous voulez faire migrer. Sachez que si le réseau virtuel contient des rôles Web/de travail ou des machines virtuelles avec des configurations non prises en charge, vous obtiendrez un message d’erreur pour la validation.

Obtenez tous les réseaux virtuels de l’abonnement à l’aide de la commande suivante.

	azure network vnet list

Préparez le réseau virtuel de votre choix pour la migration à l’aide de la commande ci-dessous.

	azure network vnet prepare-migration virtualnetworkname

Vérifiez la configuration des machines virtuelles préparées soit à l’aide de l’interface de ligne de commande, soit à l’aide du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande ci-dessous.

	azure network vnet abort-migration virtualnetworkname

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande ci-dessous.

	azure network vnet commit-migration virtualnetworkname

## Références

- [Migration prise en charge par la plateforme de ressources IaaS Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0511_2016-->