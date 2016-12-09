---
title: "Modes de déploiement Resource Manager et Service Management (classique) | Microsoft Docs"
description: "Découvrez les différences entre les modèles de déploiement classique et Resource Manager."
services: virtual-network
documentationcenter: 
author: telmosampaio
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 18a235d8-38ac-4886-9e56-b3855c73ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: telmos
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: b08630777e9cb66e3baae24ad9befc93a47e65db


---
# <a name="azure-deployment-models"></a>Modèles de déploiement Azure
La plateforme Azure est en transition.  Que vous débutiez avec Azure ou que vous l’utilisiez depuis plusieurs années, il est important de comprendre certains des principaux changements que nous apportons à la plateforme durant cette transition.

Toutes les ressources Azure prennent en charge au moins un des deux modèles de déploiement suivants :

* **Resource Manager :** c'est le nouveau modèle de déploiement des ressources Azure. La plupart des ressources les plus récentes prennent déjà en charge ce modèle de déploiement ; à terme, ce sera le cas de toutes les ressources.   
* **Classique :** ce modèle est pris en charge dès maintenant par la plupart des ressources Azure existantes. Les nouvelles ressources ajoutées à Azure ne prendront pas en charge ce modèle.

La documentation de chaque ressource Azure précise avec quel modèle de service elle peut être créée.

## <a name="why-does-this-matter"></a>En quoi est-ce important ?
C’est important pour les raisons suivantes :

* Les fonctionnalités de la plateforme Azure que vous utilisez sont différentes entre ces deux modèles.  Par exemple, les ressources créées à l’aide du modèle de déploiement Resource Manager (ou simplement de Resource Manager) peuvent être créées avec des [modèles Azure Resource Manager](azure-resource-manager/resource-group-overview.md#template-deployment), ce qui n’est pas possible pour les ressources créées avec le modèle de déploiement classique.
* Les fonctionnalités ou les comportements de chaque ressource Azure peuvent être différents d’un modèle à l’autre ou n’exister que dans un des deux modèles.  Par exemple, l’équilibrage de charge du trafic sur l’ensemble des machines virtuelles créées avec le modèle de déploiement classique est *implicite* car les machines virtuelles sont membres d'un service cloud Azure et la charge est équilibrée automatiquement entre les machines virtuelles dans un service cloud. Les machines virtuelles créées à l'aide de Resource Manager ne sont pas membres d'un service cloud et une ressource d'équilibrage de charge Azure distincte doit être *explicitement* créée pour équilibrer le trafic entre plusieurs machines virtuelles.  
* La façon de créer, de configurer et de gérer vos ressources Azure diffère entre ces deux modèles.
* Les ressources créées à l'aide d'un des modèles de déploiement ne peuvent pas nécessairement interagir avec les ressources créées à l'aide d'un autre modèle de déploiement. Par exemple, les machines virtuelles Azure créées à l'aide d'un des modèles de déploiement peuvent uniquement se connecter à des réseaux Azure Virtual Networks créés à l'aide du même modèle de déploiement.    

Chacun des modèles de déploiement sous-jacents est une interface de programmation (API) pour chaque ressource.  Il existe une [API Ressource Manager](https://msdn.microsoft.com/library/azure/dn948464.aspx) pour le modèle de déploiement Resource Manager et une [API Service Management](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour le modèle de déploiement classique. Les développeurs peuvent écrire du code pour interagir avec ces API *directement*.  

Les professionnels de l’informatique, cependant, interagissent en général avec ces API *indirectement* à l’aide d’un portail graphique dans un navigateur web, grâce à des applets de commande Azure PowerShell sur un ordinateur Windows, ou grâce à l’interface de ligne de commande Azure sur un ordinateur Windows, OS X ou Linux. Ces trois méthodes indirectes utilisées par le professionnel de l'informatique interagissent directement avec les API. Cela signifie que lorsqu’une nouvelle fonctionnalité est introduite dans la plateforme Azure ou dans les ressources, elle est toujours directement disponible au moyen de l'API en premier lieu. Les méthodes indirectes prennent en charge les nouvelles ressources et fonctionnalités une fois l'API disponible.  

Les sections ci-dessous expliquent comment les ressources Azure sont configurées à l’aide des différents modèles de déploiement grâce aux trois méthodes indirectes.

## <a name="portals"></a>Portails
Azure dispose de deux portails :

* **[Portail Azure](https://manage.windowsazure.com) :** si vous utilisez Azure depuis un certain temps, vous avez utilisé ce portail. Il est utilisé pour créer et configurer d’anciennes ressources Azure qui prennent en charge le modèle de déploiement classique. Vous ne pouvez pas l'utiliser pour créer ou configurer les ressources qui prennent uniquement en charge Resource Manager. 
* **[Portail Azure en version préliminaire](https://azure.microsoft.com/overview/preview-portal/) :** si vous utilisez une ressource Azure plus récente, vous avez certainement utilisé ce portail. Il peut être utilisé pour créer et configurer certaines ressources Azure. Vous pourrez à terme créer et configurer toutes les ressources Azure avec celui-ci. Pour les quelques ressources qui prennent en charge les deux modèles de déploiement, ce portail peut être utilisé pour créer et configurer une ressource à l'aide de n’importe quel modèle de déploiement. 

Certaines fonctionnalités et ressources peuvent uniquement être créées et configurées dans un portail ou dans l'autre. Certaines ressources ou fonctionnalités ne peuvent pas (encore) être créées ou configurées dans les deux portails, et peuvent uniquement être configurées avec PowerShell, l'interface de ligne de commande ou les deux. La documentation de chaque ressource Azure précise avec quelle méthode elle peut être créée. 

## <a name="powershell"></a>PowerShell
Avec [PowerShell](powershell-install-configure.md) vous pouvez utiliser une ligne de commande ou créer des scripts pour créer et configurer des ressources Azure à partir d'un ordinateur Windows.  Les ressources Azure individuelles ont des [applets de commande Resource Manager](https://msdn.microsoft.com/library/azure/mt125356.aspx), des [applets de commande Service Management](https://msdn.microsoft.com/library/azure/dn708504.aspx), ou les deux.  Certaines fonctionnalités et ressources peuvent uniquement être créées et/ou configurées à l’aide de PowerShell ou de l’interface de ligne de commande. Selon la ressource, lors de l'utilisation des applets de commande PowerShell Resource Manager, vous avez deux options pour créer et configurer des ressources Azure :

* **Applets de commande PowerShell uniquement :** vous pouvez créer et configurer chaque ressource Azure individuellement à l'aide des applets de commande de chaque ressource. Vous pouvez le faire à partir d'une ligne de commande ou en incluant plusieurs commandes dans un script PowerShell que vous pouvez stocker et versionner.
* **Applets de commande PowerShell avec un modèle Azure Resource Manager :** vous pouvez utiliser PowerShell pour créer des ressources Azure à l’aide d’un modèle Azure Resource Manager. Les modèles peuvent être enregistrés et gérés. Pour en savoir plus, lisez l’article [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md). Plusieurs [modèles Azure Quickstart](https://azure.microsoft.com/documentation/templates/) existent pour les solutions courantes. Ils peuvent également être téléchargés et modifiés.

## <a name="cli"></a>Interface de ligne de commande
Vous pouvez créer et configurer des ressources Azure à partir d'ordinateurs Windows, OS X ou Linux à l'aide de l'interface de ligne de commande.  Lisez l’article [Installer l’interface de ligne de commande Azure](xplat-cli-install.md) pour installer l'interface de ligne de commande sur le système d'exploitation de votre choix. Comme pour PowerShell, il existe différentes commandes à utiliser selon que vous créez des ressources à l’aide des modèles de déploiement [Resource Manager](xplat-cli-azure-resource-manager.md) ou [classique (Service Management)](virtual-machines/virtual-machines-linux-classic-manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes
* Voici quelques informations supplémentaires sur [Resource Manager](azure-resource-manager/resource-group-overview.md).
* Découvrez comment [concevoir des modèles](best-practices-resource-manager-design-templates.md).




<!--HONumber=Nov16_HO3-->


