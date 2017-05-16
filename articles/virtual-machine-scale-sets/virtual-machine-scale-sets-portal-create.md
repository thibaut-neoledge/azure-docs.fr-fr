---
title: "Créer un groupe de machines virtuelles identiques à l’aide du portail Azure | Microsoft Docs"
description: "Déployez des jeux de mise à l’échelle à l’aide du portail Azure."
keywords: "Jeux de mise à l’échelle de machine virtuelle"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 308355e7d66c977f5cbfc8de3b939b76c2f8f83c
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Création d’un jeu de mise à l’échelle de machine virtuelle à l’aide du portail Azure
Ce didacticiel vous montre à quel point il est facile de créer jeu de mise à l’échelle de machines virtuelles en quelques minutes à l’aide du portail Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Choisir l’image de machine virtuelle à partir de Marketplace
À partir du portail, vous pouvez facilement déployer un jeu de mise à l’échelle avec des images CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu Server ou Windows Server.

Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur web. Cliquez sur `New`, recherchez `scale set`, puis sélectionnez l’entrée `Virtual machine scale set` :

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-scale-set"></a>Création du jeu de mise à l’échelle
Vous pouvez à présent utiliser les paramètres par défaut pour créer rapidement le jeu de mise à l’échelle.

* Sur le panneau `Basics` , entrez un nom pour le jeu de mise à l’échelle. Ce nom constituant la base du nom de domaine complet (FQDN) de l’équilibreur de charge placé devant le jeu de mise à l’échelle, assurez-vous qu’il est unique dans Azure.
* Sélectionnez le type de système d’exploitation souhaité, entrez le nom d’utilisateur de votre choix, puis sélectionnez le type d’authentification que vous préférez. Si vous choisissez de définir un mot de passe, celui-ci doit compter au moins 12 caractères et remplir trois des quatre conditions suivantes : une lettre minuscule, une lettre majuscule, un chiffre et un caractère spécial. En savoir plus sur les [conditions requises pour les noms d’utilisateur et les mots de passe](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Si vous choisissez `SSH public key`, assurez-vous de coller uniquement votre clé publique, PAS votre clé privée :

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Choisissez si vous souhaitez limiter le jeu de mise à l’échelle à un seul groupe de placement ou s’il doit s’étendre sur plusieurs groupes de placement. Autoriser le jeu de mise à l’échelle à s’étendre aux groupes de placement permet d’utiliser des jeux de mise à l’échelle avec une capacité de plus de 100 machines virtuelles (jusqu'à 1 000), avec certaines limitations. Pour plus d’informations, consultez [cette documentation](./virtual-machine-scale-sets-placement-groups.md).
* Entrez le nom de groupe de ressources et l’emplacement de votre choix, puis cliquez sur `OK`.
* Sur le panneau `Virtual machine scale set service settings` : entrez l’étiquette de nom de domaine de votre choix (servant de base au nom de domaine complet pour l’équilibreur de charge placé devant le jeu de mise à l’échelle). Cette étiquette doit être unique dans Azure.
* Choisissez l’image de disque du système d’exploitation, le nombre d’instances et la taille de machine que vous souhaitez.
* Choisissez votre type de disque souhaité : géré ou non géré. Pour plus d’informations, consultez [cette documentation](./virtual-machine-scale-sets-managed-disks.md). Si vous avez configuré le jeu de mise à l’échelle pour s’étendre sur plusieurs groupes de placement, cette option ne sera pas disponible car un disque géré est requis pour que les jeux de mise à l’échelle couvrent des groupes de placement.
* Activez ou désactivez la mise à l’échelle automatique et configurez-la si elle est activée :

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Enfin, sur le panneau `Summary`, lorsque la validation est effectuée, cliquez sur `OK` pour démarrer le déploiement du jeu de mise à l’échelle.


## <a name="connect-to-a-vm-in-the-scale-set"></a>Se connecter à une machine virtuelle dans le jeu de mise à l’échelle
Si vous avez choisi de limiter votre jeu de mise à l’échelle à un seul groupe de placement, le jeu de mise à l’échelle est déployé avec des règles NAT configurées pour vous permettre de vous connecter au jeu de mise à l’échelle facilement (sinon, pour vous connecter aux machines virtuelles dans le jeu de mise à l’échelle, vous devez probablement créer une jumpbox sur le même réseau virtuel que le jeu de mise à l’échelle). Pour les voir, accédez à la l’onglet `Inbound NAT Rules` de l’équilibreur de charge pour le jeu de mise à l’échelle :

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Vous pouvez vous connecter à chaque machine virtuelle dans le jeu de mise à l’échelle en utilisant des règles NAT. Par exemple, pour un jeu de mise à l’échelle Windows, s’il existe une règle NAT sur le port entrant 50000, vous pourriez vous connecter à cette machine via protocole RDP sur `<load-balancer-ip-address>:50000`. Pour un jeu de mise à l’échelle Linux, vous vous connecteriez en utilisant la commande `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir de la documentation sur le déploiement de jeux de mise à l’échelle à partir de l’interface de ligne de commande, voir [cette documentation](virtual-machine-scale-sets-cli-quick-create.md).

Pour obtenir de la documentation sur le déploiement de jeux de mise à l’échelle à partir de PowerShell, voir [cette documentation](virtual-machine-scale-sets-windows-create.md).

Pour obtenir de la documentation sur le déploiement de jeux de mise à l’échelle à partir de Visual Studio, voir [cette documentation](virtual-machine-scale-sets-vs-create.md).

Pour obtenir une documentation générale, voir la [page de présentation de la documentation relative aux jeux de mise à l’échelle](virtual-machine-scale-sets-overview.md).

Pour des informations générales, voir la [page d’accueil principale pour les jeux de mise à l’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


