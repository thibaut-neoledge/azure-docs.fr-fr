---
title: Didacticiel sur DotNet Core pour les machines virtuelles Microsoft Azure 1 | Microsoft Docs
description: Didacticiel sur DotNet Core pour les machines virtuelles Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 14d5f250-1f76-49d4-898f-07b58fd39e7c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b6d945fcb4ed3a3bd49f522b5b7a355aa1a9b1b2
ms.lasthandoff: 03/31/2017


---
# <a name="automating-application-deployments-to-windows-virtual-machines"></a>Automatisation des déploiements d’applications sur Machines virtuelles Windows

Cette série en quatre parties détaille le déploiement et la configuration des ressources et applications Azure à l’aide de modèles Azure Resource Manager. Dans cette série, un exemple de modèle est déployé et le modèle de déploiement examiné. L’objectif de cette série est d’informer concernant la relation entre les ressources Azure et de fournir une expérience pratique concernant le déploiement de modèles Azure Resource Manager entièrement intégrés. Ce document suppose que le lecteur dispose d’un niveau de connaissance élémentaire concernant Azure Resource Manager. Avant de commencer ce didacticiel, nous vous recommandons de vous familiariser avec les concepts de base d’Azure Resource Manager.

## <a name="music-store-application"></a>Application du Store musique
L’exemple utilisé dans cette série est une application .NET Core simulant une expérience d’achat au Store musique. Cette application peut être déployée sur un système virtuel Linux ou Windows. Des exemples de déploiement ont été créés pour les deux. L’application inclut une application web et une base de données SQL. Avant de lire les articles de cette série, déployez l’application à l’aide du bouton de déploiement figurant sur cette page. Une fois le déploiement achevé, l’architecture Azure/d’application ressemble au diagramme suivant. 

Le modèle Resource Manager du Store musique se trouve ici : [Modèle Windows du Store musique](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Application du Store musique](./media/dotnet-core-1-landing/music-store.png)

Chacun de ces composants, dont le modèle JSON associé, est examiné dans les quatre articles suivants.

* [**Architecture d’applications**](dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) : les composants d’application, tels que les sites web et bases de données, doivent être hébergés sur des ressources informatiques Azure telles que des machines virtuelles et des bases de données SQL Azure. Ce document vous guide dans le mappage de vos besoins de calcul à des ressources Azure, et dans le déploiement de celles-ci avec un modèle Azure Resource Manager. 
* [**Accès et sécurité**](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) : lors de l’hébergement des applications dans Azure, il est nécessaire de prendre en compte le mode d’accès à l’application et la manière dont les différents composants de celle-ci accèdent les uns aux autres. Ce document détaille la fourniture et la sécurisation de l’accès Internet à une application, ainsi que l’accès entre composants de l’application.
* [**Disponibilité et mise à l’échelle**](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) : la disponibilité et la mise à l’échelle font référence à la capacité des applications à rester opérationnelles pendant un arrêt de l’infrastructure, et à la capacité d’adapter l’échelle des ressources de calcul à la demande de l’application. Ce document détaille les composants nécessaires au déploiement d’une application à charge équilibrée et hautement disponible.
* [**Déploiement d’application**](dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) : lors du déploiement d’applications dans Machines Virtuelles Azure, la méthode d’installation des fichiers binaires des applications sur la machine virtuelle doit être prise en compte. Ce document détaille l’automatisation de l’installation d’applications à l’aide d’extensions de script personnalisé pour machine virtuelle Azure.

Lorsque vous développez des modèles Azure Resource Manager, l’objectif est d’automatiser le déploiement d’une infrastructure Azure, ainsi que l’installation et la configuration de toutes les applications hébergées sur celle-ci. Ces articles fournissent un exemple de cette expérience.

## <a name="deploy-the-music-store-application"></a>Déploiement de l’application du Store musique
Vous pouvez déployer l’application du Store musique à l’aide de ce bouton.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Le modèle Azure Resource Manager requiert les valeurs de paramètre suivantes.

| Nom du paramètre | Description |
| --- | --- |
| ADMINUSERNAME |Nom d’utilisateur administrateur utilisé sur la machine virtuelle et la base de données Azure SQL Database. |
| ADMINPASSWORD |Mot de passe utilisé sur la machine virtuelle Azure et la base de données Azure SQL Database. |
| NUMBEROFINSTANCES |Nombre de machines virtuelles à créer. Chacune de ces machines virtuelles héberge l’application web du Store musique, et la charge de tout le trafic est équilibrée sur celles-ci. |
| PUBLICIPADDRESSDNSNAME |Nom DNS global unique associé à l’adresse IP publique. |

Une fois le déploiement du modèle terminé, accédez à l’adresse IP publique à l’aide d’un navigateur Internet. Le site .Net du Store musique s’affiche.

## <a name="next-steps"></a>Étapes suivantes
<hr>

[Étape 1 : architecture d’application avec les modèles Azure Resource Manager](dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Étape 2 : accès et sécurité dans les modèles Azure Resource Manager](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Étape 3 : disponibilité et mise à l’échelle dans les modèles Azure Resource Manager](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Étape 4 : déploiement d’application avec des modèles Azure Resource Manager](dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


