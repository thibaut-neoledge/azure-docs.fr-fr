<properties
	pageTitle="Différentes façons de créer une machine virtuelle Linux | Microsoft Azure"
	description="Répertorie les différentes façons de créer une machine virtuelle Linux sur Azure et fournit des liens vers d’autres instructions."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Différentes façons de créer une machine virtuelle Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft Azure propose différents moyens de créer une machine virtuelle, suivant les utilisateurs et les applications. Cet article résume ces différences et les options dont vous disposez pour créer vos machines virtuelles de Linux.

Les modèles Azure Resource Manager peuvent également être utilisés pour créer et gérer une machine virtuelle et ses différentes ressources en tant qu’unité logique unique de déploiement. Des instructions relatives à cette approche sont fournies le cas échéant. Pour plus d’informations sur Microsoft Azure Resource Manager et sur la gestion des ressources en tant qu’unité unique, consultez la [vue d’ensemble][].

## Choix des outils

### Interface utilisateur graphique : le portail Azure Classic ou le portail Azure

L'interface utilisateur graphique du portail constitue un moyen simple de tester une machine virtuelle, en particulier si vous venez de démarrer avec Microsoft Azure. Pour créer la machine virtuelle, utilisez le [portail Azure Classic](https://manage.windowsazure.com) ou le [portail Azure](https://portal.azure.com). Pour obtenir des instructions générales, consultez [Création d’une machine virtuelle personnalisée][] et sélectionnez une image Linux à partir de la **galerie**. Notez que le [portail Azure Classic](https://manage.windowsazure.com) crée des machines virtuelles en utilisant uniquement le modèle de déploiement classique.

### Interface de commande : interface de ligne de commande Microsoft Azure ou Microsoft Azure PowerShell

Si vous préférez travailler dans une interface de commande, choisissez l’interface de ligne de commande Azure Mac, Linux, Windows, ou bien Azure PowerShell, qui comporte des applets de commande Windows PowerShell pour Azure et une console personnalisée.

Pour créer des machines virtuelles dans le modèle de déploiement Resource Manager à l’aide de l’interface de ligne de commande Azure, consultez [Créer une machine virtuelle exécutant Linux][]. Suivez les onglets ou les sélecteurs d’article dans cet article pour obtenir des instructions sur l’utilisation d’Azure PowerShell et des modèles.

Pour le modèle de déploiement classique, consultez [Créer une machine virtuelle Linux personnalisée à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-create-custom.md) et [Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Linux][].


### Environnement de développement : Visual Studio

Visual Studio prend également en charge la création de machines virtuelles Azure. Pour le modèle de déploiement classique, consultez [Création d’une machine virtuelle pour un site web avec Visual Studio][]. Pour le modèle de déploiement Resource Manager, consultez [Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET][].


## Système d’exploitation et choix d’images

Choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils. Sinon, utilisez l’une de vos propres images.


### Images Microsoft Azure

Dans tous les articles ci-dessus, vous pouvez facilement utiliser une image Azure existante pour créer une machine virtuelle et la personnaliser en fonction de la mise en réseau, de l’équilibrage de charge, entre autres. Les portails fournissent une liste de galeries ou d’images pour les images fournies par Azure. Vous pouvez obtenir des listes similaires à l’aide de la ligne de commande. Par exemple, dans l’interface de ligne de commande Azure, exécutez `azure vm image list` pour obtenir la liste de toutes les images disponibles, par emplacement et serveur de publication.


### Utiliser votre propre image

Utilisez une image basée sur une machine virtuelle Microsoft Azure existante, en *capturant* cette machine virtuelle ou en chargeant l’une de vos propres images stockée sur un disque dur virtuel. Pour le modèle de déploiement classique, consultez [Capture d’une machine virtuelle Linux à utiliser comme modèle à l’aide de l’interface de ligne de commande][] et [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux][]. Pour le modèle de déploiement Resource Manager, consultez [Comment capturer une machine virtuelle Linux en tant que modèle Resource Manager](virtual-machines-linux-capture-image-resource-manager.md).

## Étapes suivantes

[Connexion à la machine virtuelle][]

[Association d’un disque de données][]

## Ressources supplémentaires

[Environnement de test de la configuration de base][]

[Environnements de test de cloud hybride Azure][]

[Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l'interface de ligne de commande Azure pour Mac, Linux et Windows][]

<!-- LINKS -->
[vue d’ensemble]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l'interface de ligne de commande Azure pour Mac, Linux et Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[Capture d’une machine virtuelle Linux à utiliser comme modèle à l’aide de l’interface de ligne de commande]: virtual-machines-linux-capture-image.md

[Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux]: virtual-machines-linux-create-upload-vhd.md

[Création d’une machine virtuelle pour un site web avec Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET]: virtual-machines-arm-deployment.md

[Connexion à la machine virtuelle]: virtual-machines-linux-how-to-log-on.md

[Association d’un disque de données]: virtual-machines-linux-how-to-attach-disk.md

[Environnement de test de la configuration de base]: virtual-machines-base-configuration-test-environment.md
[Environnements de test de cloud hybride Azure]: virtual-machines-hybrid-cloud-test-environments.md

[Créer une machine virtuelle exécutant Linux]: virtual-machines-linux-tutorial.md
[Création d’une machine virtuelle personnalisée]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->