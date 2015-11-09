<properties
	pageTitle="Différentes méthodes de création d’une machine virtuelle Windows | Microsoft Azure"
	description="Répertorie différentes méthodes de création d’une machine virtuelle Windows avec Resource Manager."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# Différentes méthodes de création de machine virtuelle Windows avec Resource Manager

Microsoft Azure propose différents moyens de créer des machines virtuelles adaptées à tous les utilisateurs et toutes les applications. Pour cette raison, vous devez effectuer certains choix relatifs à la machine virtuelle et à sa création. Cet article vous propose un résumé de ces options et des liens pour obtenir des instructions.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Les modèles Microsoft Azure Resource Manager ont été récemment introduits comme un moyen de créer et de gérer une machine virtuelle et ses différentes ressources en tant qu’unité logique unique de déploiement. Des instructions relatives à cette approche sont fournies ci-dessous, le cas échéant. Pour plus d’informations sur Microsoft Azure Resource Manager et sur la gestion des ressources en tant qu’unité unique, consultez la [vue d’ensemble][].

## Choix des outils

### Interface graphique utilisateur (GUI) : portail Azure en version préliminaire

L’interface utilisateur graphique du portail Microsoft Azure constitue un moyen simple de tester une machine virtuelle, en particulier si vous venez de démarrer avec Microsoft Azure. Utiliser le portail Azure en version préliminaire pour créer une machine virtuelle :

[Créer une machine virtuelle exécutant Windows][]

### Interface de commande : interface de ligne de commande Azure ou Azure PowerShell

Si vous préférez travailler dans une interface de commande, choisissez l’interface de ligne de commande Microsoft Azure ou Microsoft Azure PowerShell, qui comporte des applets de commande pour Microsoft Azure et une console personnalisée.

Pour plus d’informations sur l’interface de ligne de commande Azure, consultez :

- [Commandes équivalentes de Resource Manager et de la gestion des services pour les tâches opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows][]
- [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure][].

Pour plus d’informations sur Azure PowerShell, consultez :

- [Créer une machine virtuelle Windows avec Resource Manager et PowerShell][]
- [Création et préconfiguration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell][]
- [Déploiement et gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell][]
- [Création d’une machine virtuelle Windows avec un modèle Resource Manager et PowerShell][]

### Environnement de développement : Visual Studio

[Déployer les ressources Microsoft Azure à l'aide des bibliothèques de traitement, réseau et de stockage .NET][]

## Système d'exploitation et choix d'images

Choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils. Sinon, utilisez l’une de vos propres images. Trouvez l’image de plateforme dont vous avez besoin pour votre application en utilisant les informations dans : [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure][].

<!-- LINKS -->
[vue d’ensemble]: ../resource-group-overview.md

[Créer une machine virtuelle exécutant Windows]: virtual-machines-windows-tutorial.md

[Commandes équivalentes de Resource Manager et de la gestion des services pour les tâches opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Création et préconfiguration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Déploiement et gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Créer une machine virtuelle Windows avec Resource Manager et PowerShell]: virtual-machines-create-windows-powershell-resource-manager.md
[Création d’une machine virtuelle Windows avec un modèle Resource Manager et PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template-simple.md


[Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure]: resource-groups-vm-searching.md
[Déployer les ressources Microsoft Azure à l'aide des bibliothèques de traitement, réseau et de stockage .NET]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Nov15_HO1-->