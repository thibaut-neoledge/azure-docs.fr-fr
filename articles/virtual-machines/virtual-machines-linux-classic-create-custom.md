---
title: "Création d’une machine virtuelle Linux Classic à l’aide de l’interface de ligne de commande | Microsoft Docs"
description: "Apprenez à créer une machine virtuelle Linux avec l’interface de ligne de commande Azure, à l’aide du modèle de déploiement Classic."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: cfb86b803e2c74dfff567b0a9920735ddb9e69f4


---
# <a name="how-to-create-a-linux-vm-with-the-azure-cli"></a>Création d’une machine virtuelle Linux avec l’interface de ligne de commande Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Pour la version de Resource Manager, suivi [ce lien](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cette rubrique décrit comment créer une machine virtuelle Linux avec l’interface de ligne de commande Azure, à l’aide du modèle de déploiement Classic. Nous utilisons une image Linux provenant des **IMAGES** disponibles sur Azure. L’interface de ligne de commande Azure propose notamment les choix de configuration suivants :

* Connexion de la machine virtuelle à un réseau virtuel
* Ajout de la machine virtuelle à un service cloud existant
* Ajout de la machine virtuelle à un compte de stockage existant
* Ajout de la machine virtuelle à un groupe à haute disponibilité

> [!IMPORTANT]
> Si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d’hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Création d’une machine virtuelle Linux à l’aide du modèle de déploiement Classic
[!INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]




<!--HONumber=Nov16_HO3-->


