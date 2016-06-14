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

Microsoft Azure propose différents moyens de créer une machine virtuelle à l’aide du modèle de déploiement Azure Resource Manager, suivant les utilisateurs et les applications. Cet article résume ces différences et les options dont vous disposez pour créer vos machines virtuelles Linux.

## Choix des outils

### Interface de commande : interface de ligne de commande Azure 

Utilisez l’interface de ligne de commande (CLI). Consultez l’article [Installer l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md) pour en savoir plus sur l’installation de l’interface de ligne de commande Azure par le biais de npm, un conteneur Docker, ou un script d’installation. Les didacticiels suivants fournissent des exemples d’utilisation de l’interface de commande Azure :

* [Créer une machine virtuelle Linux à partir de l’interface CLI Azure pour le développement et le test](virtual-machines-linux-quick-create-cli.md) 

* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Créer une machine virtuelle Linux de A à Z à l’aide de l’interface de ligne de commande (CLI) Azure](virtual-machines-linux-create-cli-complete.md)

### Interface utilisateur graphique (GUI) : portail Azure

L’interface utilisateur graphique du [portail Azure](https://portal.azure.com) constitue un moyen simple de tester une machine virtuelle, en particulier si vous découvrez Microsoft Azure, étant donné qu’aucune installation n’est à effectuer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Création d’une machine virtuelle exécutant Linux dans le portail Azure](virtual-machines-linux-quick-create-portal.md) 

## Système d'exploitation et choix d'images

Avec ces deux méthodes, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, vous pouvez télécharger une de vos propres images.

### Images Microsoft Azure

Dans tous les articles ci-dessus, vous pouvez facilement utiliser une image Azure existante pour créer une machine virtuelle et la personnaliser en fonction de la mise en réseau et de l’équilibrage de charge, entre autres. Le portail fournit Azure Marketplace pour les images fournies par Azure. Vous pouvez obtenir des listes similaires à l’aide de la ligne de commande. Par exemple, dans l’interface de ligne de commande Azure, exécutez `azure vm image list` pour obtenir la liste de toutes les images disponibles, par emplacement et serveur de publication. Consultez la page [Parcourir et sélectionner des images de machines virtuelles Linux dans Azure avec l’interface CLI ou PowerShell](virtual-machines-linux-cli-ps-findimage.md) pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles.

### Utiliser votre propre image

Si vous avez besoin de personnalisations spécifiques, vous pouvez utiliser une image basée sur une machine virtuelle Microsoft Azure existante, en *capturant* cette machine virtuelle ou en chargeant l’une de vos images stockées sur un disque dur virtuel. Pour plus d’informations sur les versions prises en charge et comment utiliser vos propres images, consultez les articles suivants :

* [Distributions prises en charge par Azure](virtual-machines-linux-endorsed-distros.md)

* [Informations concernant les distributions non approuvées](virtual-machines-linux-create-upload-generic.md)

* [Capture d’une machine virtuelle Linux en tant que modèle Resource Manager](virtual-machines-linux-capture-image.md).

## Étapes suivantes

* Essayez l’un des didacticiels pour créer une machine virtuelle Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md), avec le [CLI](virtual-machines-linux-quick-create-cli.md) ou à l’aide d’un [modèle](virtual-machines-linux-cli-deploy-templates.md) Azure Resource Manager.

* Après avoir créé une machine virtuelle Linux, vous pouvez facilement [ajouter un disque de données](virtual-machines-linux-add-disk.md).

* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0608_2016-->