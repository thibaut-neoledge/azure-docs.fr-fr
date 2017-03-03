---
title: "Déployer une application sur des groupes identiques de machines virtuelles | Microsoft Docs"
description: "Déployer une application sur des groupes identiques de machines virtuelles"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: f13545d753690534e0e645af67efcf1b524837eb
ms.openlocfilehash: dad27b11b5f02ed41826b82882cc5089eb69cb04
ms.lasthandoff: 02/09/2017


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Déployer une application sur des groupes identiques de machines virtuelles
Une application exécutée sur un groupe identique de machines virtuelles est généralement déployée d’une des façons suivantes :

* Installer de nouveaux logiciels sur une image de plateforme au moment du déploiement. Une image de plateforme dans ce contexte est une image du système d’exploitation d’Azure Marketplace, comme Ubuntu 16.04, Windows Server 2012 R2, etc.

Vous pouvez installer de nouveaux logiciels sur une image de plateforme à l’aide d’une [extension de machine virtuelle](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Une extension de machine virtuelle est un logiciel qui s’exécute lorsqu’une machine virtuelle est déployée. Vous pouvez exécuter le code que vous souhaitez lors du déploiement à l’aide d’une extension de script personnalisé. [Voici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) un exemple de modèle Azure Resource Manager avec deux extensions de machines virtuelles : une extension de script personnalisé Linux pour installer Apache et PHP et une extension de diagnostic pour émettre des données de performances utilisées par la mise à l’échelle automatique Azure.

L’avantage de cette approche est que vous disposez d’un niveau de séparation entre votre code d’application et le système d’exploitation et que vous pouvez mettre à jour votre application séparément. Bien sûr, cela signifie également qu’il y a un plus grand nombre d’éléments mobiles et que le déploiement des machines virtuelles peut être plus long si le script doit télécharger et configurer de nombreux éléments.

**Si vous transmettez des informations sensibles dans votre commande d’extension de script personnalisé (par exemple, un mot de passe), veillez à spécifier `commandToExecute` dans l’attribut `protectedSettings` de l’extension de script personnalisé à la place de l’attribut `settings`.**

* Créer une image de machine virtuelle personnalisée qui inclut le système d’exploitation et l’application dans un même VHD. Ici, le groupe identique se compose d’un ensemble de machines virtuelles copiées à partir d’une image créée par vos soins, que vous devez tenir à jour. Cette approche ne nécessite aucune configuration supplémentaire lors du déploiement des machines virtuelles. Toutefois, dans la version `2016-03-30` des groupes identiques de machines virtuelles (et les versions antérieures), les disques de système d’exploitation des machines virtuelles du groupe sont limités à un seul compte de stockage. Par conséquent, vous pouvez avoir au maximum 40 machines virtuelles dans un groupe identique, par opposition à la limite de 100 machines virtuelles par groupe avec les images de plateforme. Consultez [Vue d’ensemble de la conception de groupes identiques](virtual-machine-scale-sets-design-overview.md) pour plus d’informations.

    >[!NOTE]
    >La version d’API de VM Scale Sets `2016-04-30-preview` prend en charge l’utilisation d’Azure Managed Disks pour le disque du système d’exploitation et tous les disques de données supplémentaires. Pour plus d’informations, consultez les sections relatives à la [vue d’ensemble de Managed Disks](../storage/storage-managed-disks-overview.md) et à [l’utilisation de disques de données associés](virtual-machine-scale-sets-attached-disks.md). 

* Déployer une plateforme ou une image personnalisée qui consiste essentiellement en un conteneur hôte et installer votre application en tant qu’un ou plusieurs conteneurs que vous gérez avec un orchestrateur ou un outil de gestion de configuration. L’avantage de cette approche est que vous avez extrait votre infrastructure cloud de la couche d’application et que vous pouvez la gérer séparément.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>Que se passe-t-il lorsqu’un groupe identique de machines virtuelles monte en charge ?
Lorsque vous ajoutez une ou plusieurs machines virtuelles à un groupe identique en augmentant la capacité, manuellement ou automatiquement, l’application est automatiquement installée. Par exemple, si des extensions sont définies pour le groupe identique, elles s’exécutent sur une machine virtuelle différente à chaque création. Si le groupe identique est basé sur une image personnalisée, toute nouvelle machine virtuelle est une copie de l’image personnalisée source. Si les machines virtuelles du groupe identique sont des hôtes de conteneurs, il est possible que vous ayez un code de démarrage pour charger les conteneurs dans une extension de script personnalisé, ou qu’une extension installe un agent qui s’enregistre avec un orchestrateur de cluster (par exemple, Azure Container Service).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Comment gérer la mise à jour des applications dans les groupes identiques de machines virtuelles ?
Pour la mise à jour des applications dans les groupes identiques de machines virtuelles, trois approches principales découlent des trois méthodes précédentes de déploiement des applications :

* Mise à jour avec des extensions de machines virtuelles. Les extensions de machines virtuelles définies pour un groupe identique de machines virtuelles sont exécutées chaque fois qu’une nouvelle machine virtuelle est déployée, qu’une machine virtuelle existante est réinitialisée, ou qu’une extension de machine virtuelle est mise à jour. Si vous avez besoin de mettre à jour votre application, la mise à jour directe d’une application au moyen des extensions est une approche viable : il vous suffit de mettre à jour la définition de l’extension. Pour ce faire, une méthode simple consiste à modifier les URI de fichiers pour qu’ils pointent vers le nouveau logiciel.

* Approche de l’image personnalisée immuable. Vous pouvez vous concentrer sur la création d’un pipeline fiable afin d’automatiser la génération, le test et le déploiement des images lorsque vous intégrez l’application (ou les composants de l’application) dans une image de machine virtuelle. Vous pouvez concevoir votre architecture de façon à faciliter le passage rapide en production d’un groupe identique intermédiaire. Un bon exemple de cette approche est le [fonctionnement du pilote Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Packer et Terraform prennent également en charge Azure Resource Manager ; par conséquent, vous pouvez aussi définir vos images « en tant que code » et les générer dans Azure, puis utiliser le disque dur virtuel dans votre groupe identique. Toutefois, cette opération devient problématique pour les images Marketplace, pour lesquelles les extensions / scripts personnalisés deviennent plus importants du fait que vous ne manipulez pas directement les éléments de Marketplace.

* Mettre à jour les conteneurs. Extrayez la gestion du cycle de vie des applications à un niveau supérieur à l’infrastructure cloud, par exemple par encapsulation des applications et composants des applications dans des conteneurs, et gérez ces conteneurs avec des orchestrateurs de conteneurs et des gestionnaires de configuration comme Chef/Puppet.

Les machines virtuelles du groupe identique deviennent un support stable pour les conteneurs et nécessitent uniquement des mises à jour occasionnelles du système d’exploitation et de la sécurité. Comme indiqué précédemment, Azure Container Service est un bon exemple de création d’un service autour de cette approche.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Comment déployer une mise à jour du système d’exploitation sur plusieurs domaines de mise à jour ?
Supposons que vous souhaitez mettre à jour votre image de système d’exploitation tout en maintenant le fonctionnement du groupe identique de machines virtuelles. Pour cela, il est possible de mettre à jour les images des machines virtuelles, une machine virtuelle à la fois. Vous pouvez le faire avec PowerShell ou l’interface de ligne de commande Azure. Il existe des commandes distinctes pour mettre à jour le modèle du groupe identique de machines virtuelles (la façon dont sa configuration est définie) et pour émettre des appels de « mise à niveau manuelle » sur des machines virtuelles données. Le document Azure [Mettre à niveau un jeu de mise à l’échelle de machines virtuelles](./virtual-machine-scale-sets-upgrade-scale-set.md) fournit également plus d’informations sur les options disponibles pour effectuer des mises à niveau du système d’exploitation sur un groupe de machines virtuelles identiques.


