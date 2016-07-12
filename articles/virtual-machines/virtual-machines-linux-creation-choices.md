<properties
	pageTitle="Différentes façons de créer une machine virtuelle Linux | Microsoft Azure"
	description="Répertorie les différentes façons de créer une machine virtuelle Linux sur Azure et fournit des liens vers des outils et des didacticiels pour chaque méthode."
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
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Différentes méthodes de création de machine virtuelle Linux avec Resource Manager

Microsoft Azure propose différents moyens de créer une machine virtuelle à l’aide du modèle de déploiement Azure Resource Manager, suivant les utilisateurs et les applications. Cet article résume ces différences et les options dont vous disposez pour créer vos machines virtuelles Linux.

## Interface de ligne de commande Azure 

L’interface de ligne de commande Azure est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Vous pouvez en savoir plus sur [la manière d’installer et de configurer l’interface de ligne de commande Azure](../xplat-cli-install.md). Les didacticiels suivants fournissent des exemples d’utilisation de l’interface de commande Azure. Lisez chaque article pour en savoir plus sur les commandes de démarrage rapide de l’interface de ligne de commande indiquées :

* [Créer une machine virtuelle Linux à partir de l’interface CLI Azure pour le développement et le test](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Créer une machine virtuelle Linux de A à Z à l’aide de l’interface de ligne de commande (CLI) Azure](virtual-machines-linux-create-cli-complete.md)

* [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Portail Azure

L’interface utilisateur graphique du [portail Azure](https://portal.azure.com) constitue un moyen simple de tester une machine virtuelle, en particulier si vous découvrez Microsoft Azure, étant donné qu’aucune installation n’est à effectuer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md)
* [Attacher un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md)

## Système d'exploitation et choix d'images
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, vous pouvez charger une de vos propres images (voir ci-dessous).

### Images Microsoft Azure
Vous pouvez utiliser les commandes de l’interface de ligne de commande `azure vm image` pour voir ce qui est disponible par éditeur, version de distributeur et build.

Répertorier les éditeurs disponibles :

```bash
azure vm image list-publishers --location WestUS
```

Répertorier les produits disponibles (offres) pour un éditeur donné :

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Répertorier les références disponibles (versions distributeur) d’une offre donnée :

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Répertorier toutes les images disponibles pour une version donnée :

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Consultez la page [Parcourir et sélectionner des images de machines virtuelles Azure avec l’interface de ligne de commande Azure](virtual-machines-linux-cli-ps-findimage.md) pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles.

Les commandes `azure vm quick-create` et `azure vm create` disposent également d’alias que vous pouvez utiliser pour accéder rapidement aux distributeurs les plus courants et à leurs versions les plus récentes. Cela est plus simple que de devoir spécifier l’éditeur, l’offre, la référence et la version à chaque fois que vous créez une machine virtuelle :

| Alias | Éditeur | Offer | SKU | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | le plus récent |
| CoreOS | CoreOS | CoreOS | Stable | le plus récent |
| Debian | credativ | Debian | 8 | le plus récent |
| openSUSE | SUSE | openSUSE | 13\.2 | le plus récent |
| RHEL | Redhat | RHEL | 7,2 | le plus récent |
| SLES | SLES | SLES | 12-SP1 | le plus récent |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | le plus récent |

### Utiliser votre propre image

Si vous avez besoin de personnalisations spécifiques, vous pouvez utiliser une image basée sur une machine virtuelle Azure existante, en *capturant* cette machine virtuelle ou en chargeant l’une de vos images stockées sur un disque dur virtuel. Pour plus d’informations sur les versions prises en charge et comment utiliser vos propres images, consultez les articles suivants :

* [Distributions prises en charge par Azure](virtual-machines-linux-endorsed-distros.md)

* [Informations concernant les distributions non approuvées](virtual-machines-linux-create-upload-generic.md)

* [Capture d’une machine virtuelle Linux en tant que modèle Resource Manager](virtual-machines-linux-capture-image.md). Commandes de démarrage rapide :

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Étapes suivantes

* Essayez l’un des didacticiels pour créer une machine virtuelle Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md), avec l’[interface de ligne de commande](virtual-machines-linux-quick-create-cli.md) ou à l’aide d’un [modèle](virtual-machines-linux-cli-deploy-templates.md) Azure Resource Manager.

* Après avoir créé une machine virtuelle Linux, vous pouvez facilement [ajouter un disque de données](virtual-machines-linux-add-disk.md).

* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0706_2016-->