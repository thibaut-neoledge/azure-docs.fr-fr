<properties
	pageTitle="Différentes façons de créer une machine virtuelle Linux | Microsoft Azure"
	description="Répertorie les différentes façons de créer une machine virtuelle Linux sur Azure et fournit des liens vers d’autres instructions."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Différentes méthodes de création de machine virtuelle Linux avec Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

Microsoft Azure propose différents moyens de créer une machine virtuelle, suivant les utilisateurs et les applications. Cet article résume ces différences et les options dont vous disposez pour créer vos machines virtuelles de Linux.

Les modèles Azure Resource Manager permettent de créer et de gérer une machine virtuelle et ses différentes ressources en tant qu’unité logique unique de déploiement. Pour plus d’informations sur Microsoft Azure Resource Manager et sur la gestion des ressources en tant qu’unité unique, consultez la [vue d’ensemble](../resource-group-overview.md).

## Choix des outils

### Interface de commande : interface de ligne de commande Azure 

Utilisez l’interface de ligne de commande (CLI). Consultez ces didacticiels sur l'utilisation de l'interface de ligne de commande (CLI) Azure :

* [Create a Linux VM from the CLI for dev and test (Créer une machine virtuelle Linux réservée au développement et aux tests depuis l’interface de ligne de commande)](virtual-machines-linux-quick-create-cli.md) 

* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

### Interface utilisateur graphique (GUI) : portail Azure

L’interface utilisateur graphique du [portail Azure](https://portal.azure.com) constitue un moyen simple de tester une machine virtuelle, en particulier si vous découvrez Microsoft Azure. Utilisez le portail Azure pour créer la machine virtuelle :

* [Création d’une machine virtuelle exécutant Linux dans le portail Azure](virtual-machines-linux-portal-create.md) 

## Système d'exploitation et choix d'images

Choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils. Sinon, utilisez l’une de vos propres images.

### Images Microsoft Azure

Dans tous les articles ci-dessus, vous pouvez facilement utiliser une image Azure existante pour créer une machine virtuelle et la personnaliser en fonction de la mise en réseau, de l’équilibrage de charge, entre autres. Le portail fournit Azure Marketplace pour les images fournies par Azure. Vous pouvez obtenir des listes similaires à l’aide de la ligne de commande. Par exemple, dans l’interface de ligne de commande Azure, exécutez `azure vm image list` pour obtenir la liste de toutes les images disponibles, par emplacement et serveur de publication. Consultez [Navigate and select Azure virtual machine images with the Azure CLI](virtual-machines-linux-cli-ps-findimage.md) (Rechercher par navigation et sélectionner des images de machines virtuelles avec l’interface de ligne de commande Azure).

### Utiliser votre propre image

Utilisez une image basée sur une machine virtuelle Microsoft Azure existante, en *capturant* cette machine virtuelle ou en chargeant l’une de vos images stockées sur un disque dur virtuel. Pour plus d'informations, consultez les pages suivantes :

* [Distributions prises en charge par Azure](virtual-machines-linux-endorsed-distros.md)

* [Informations concernant les distributions non approuvées](virtual-machines-linux-create-upload-generic.md)

* [Capture d’une machine virtuelle Linux en tant que modèle Resource Manager](virtual-machines-linux-capture-image.md).

## Étapes suivantes

* Essayez l’un des didacticiels pour créer une machine virtuelle Linux à partir du [portail](virtual-machines-linux-portal-create.md), avec le [CLI](virtual-machines-linux-quick-create-cli.md) ou à l’aide d’un [modèle](virtual-machines-linux-cli-deploy-templates.md) Azure Resource Manager.

* Après avoir créé une machine virtuelle Linux, vous pouvez facilement [ajouter un disque de données](virtual-machines-linux-add-disk.md).

<!---HONumber=AcomDC_0420_2016-->