<properties
	pageTitle="Création d’une machine virtuelle Linux | Microsoft Azure"
	description="Découvrez comment créer une machine virtuelle personnalisée avec le modèle de déploiement classique exécutant le système d’exploitation Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="iainfou"/>

# Comment créer une machine virtuelle Linux personnalisée

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

Cette rubrique décrit comment créer une machine virtuelle personnalisée avec l’interface de ligne de commande Azure, à l’aide du modèle de déploiement classique. Nous allons utiliser une image Linux provenant des **IMAGES** disponibles sur Azure. L’interface de ligne de commande Azure propose notamment les choix de configuration suivants :

- Connexion de la machine virtuelle à un réseau virtuel
- Ajout de la machine virtuelle à un service cloud existant
- Ajout de la machine virtuelle à un compte de stockage existant
- Ajout de la machine virtuelle à un groupe à haute disponibilité

> [AZURE.IMPORTANT] si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d’hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu’elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


## Création d’une machine virtuelle Linux à l'aide du modèle de déploiement classique

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=AcomDC_0608_2016-->