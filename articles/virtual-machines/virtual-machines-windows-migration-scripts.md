<properties
	pageTitle="Outils de la communauté pour Azure Service Management pour la migration d’Azure Resource Manager"
	description="Cet article répertorie les outils qui ont été fournis par la Communauté afin de faciliter la migration de ressources IaaS d’Azure Service Management vers la pile Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="singhkay"/>

# Outils de la communauté pour Azure Service Management pour la migration d’Azure Resource Manager

Cet article répertorie les outils qui ont été fournis par la Communauté afin de faciliter la migration de ressources IaaS d’Azure Service Management vers la pile Azure Resource Manager.

>[AZURE.NOTE]Ces outils ne sont pas pris en charge officiellement par le Support Microsoft. Ils sont donc disponibles en open source sur Github, et nous acceptons volontiers les demandes de publication de correctifs ou de scénarios supplémentaires. Pour signaler un problème, utilisez la fonctionnalité de problèmes Github.
>
> Une migration avec ces outils entraînera un temps d’arrêt pour votre machine virtuelle Classic. Si vous souhaitez effectuer une migration prise en charge par la plateforme, consultez les articles suivants :
>
>- [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## ASM2ARM

Il s’agit d’un module de script PowerShell pour migrer votre machine virtuelle **unique** de la pile Azure Service Management à la pile Azure Resource Manager.

[Lien vers la documentation des outils](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## migAz

migAz est une option supplémentaire pour migrer un ensemble complet de ressources Azure Service Management IaaS vers des ressources Azure Resource Manager IaaS. La migration peut être exécutée dans le même abonnement ou entre différents abonnements et types d’abonnements (par ex. : les abonnements CSP).

[Lien vers la documentation des outils](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)

<!---HONumber=AcomDC_0831_2016-->