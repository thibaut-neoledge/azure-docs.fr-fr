---
title: "Exemples d’interface de ligne de commande Azure sur Windows | Microsoft Docs"
description: "Exemples d’interface de ligne de commande Azure sur Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 35ddd5c90dfba7e2e368a5f69e33616998581ff7
ms.contentlocale: fr-fr
ms.lasthandoff: 03/31/2017


---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Exemples d’interface de ligne de commande Azure pour machines virtuelles Windows

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure pour déployer des machines virtuelles Windows.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle Windows avec une configuration minimale. |
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer IIS. |
| [Créer une machine virtuelle et exécuter la configuration DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle et utilise l’extension DSC Azure pour installer IIS. |
|**Mettre en réseau des machines virtuelles**||
| [Sécuriser le trafic réseau entre les machines virtuelles](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Crée deux machines virtuelles, toutes les ressources associées, ainsi qu’un groupe de sécurité réseau interne et un groupe de sécurité réseau externe. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Operations Management Suite](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle, installe l’agent Operations Management Suite et inscrit la machine virtuelle dans un espace de travail OMS.  |
| | |

