---
title: "Maintenance planifiée des machines virtuelles Linux | Microsoft Docs"
description: "Découvrez en quoi consiste la maintenance planifiée Azure et son incidence sur la disponibilité de vos machines virtuelles Linux s’exécutant dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e65e614c-e969-40dc-a271-fe074069daf1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d0e7d00dd8e6ab53897340e13a3170519cbdb135
ms.lasthandoff: 11/17/2016


---
# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Maintenance planifiée des machines virtuelles Linux dans Azure
Découvrez en quoi consiste la maintenance planifiée Azure et son incidence possible sur la disponibilité de vos machines virtuelles Linux. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Cet article fournit des informations de base sur le processus de maintenance planifiée. Si vous souhaitez résoudre les problèmes qui ont entraîné le redémarrage de votre machine virtuelle, vous pouvez [lire ce billet de blog détaillant l’affichage des journaux de redémarrage de machine virtuelle](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Pourquoi Azure exécute une maintenance planifiée
Microsoft Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte qui supporte les machines virtuelles. Nombre de ces mises à jour sont exécutées sans impact sur les machines virtuelles ou les services cloud, y compris les mises à jour de préservation de la mémoire.

Cependant, certaines d’entre elles requièrent un redémarrage de vos machines virtuelles pour appliquer les mises à jour requises sur l’infrastructure. Les machines virtuelles sont éteintes lorsqu’on applique le correctif sur l’infrastructure, puis elles sont redémarrées.

Il existe deux sortes de maintenance qui peuvent avoir un impact sur la disponibilité de votre machine virtuelle : maintenance planifiée et non planifiée. Cette page décrit comment Microsoft Azure exécute une maintenance planifiée. Pour plus d’informations sur la maintenance non planifiée, consultez la page [Présentation de la maintenance planifiée et non planifiée](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

