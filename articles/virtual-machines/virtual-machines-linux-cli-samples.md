---
title: "Exemples d’interface de ligne de commande Azure | Microsoft Docs"
description: "Exemples d’interface de ligne de commande Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: ea2124e01753b914ebd1691e5d89091ac5cd5859
ms.lasthandoff: 03/04/2017


---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Exemples d’interface de ligne de commande Azure pour machines virtuelles Linux

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle](./scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle Linux avec une configuration minimale. |
| [Créer une machine virtuelle entièrement configurée](./scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Créer une machine virtuelle prenant en charge Docker](./scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle, configure cette machine virtuelle en tant qu’hôte Docker et exécute un conteneur NGINX. |
| [Créer une machine virtuelle et exécuter le script de configuration](./scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer NGINX. |
| [Créer une machine virtuelle avec WordPress](./scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer WordPress. |
|**Mettre en réseau des machines virtuelles**||
| [Sécuriser le trafic réseau entre les machines virtuelles](./scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée deux machines virtuelles, toutes les ressources associées, ainsi qu’un groupe de sécurité réseau interne et un groupe de sécurité réseau externe. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Operations Management Suite](./scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle, installe l’agent Operations Management Suite et inscrit la machine virtuelle dans un espace de travail OMS.  |
|**Redémarrer les machines virtuelles**||
| [Redémarrer les machines virtuelles](./scripts/virtual-machines-linux-cli-sample-restart-by-tag.md) | Crée des machines virtuelles et les redémarre. |
|**Résoudre les problèmes liés aux machines virtuelles**||
| [Résoudre les problèmes liés au disque du système d’exploitation de machines virtuelles](./scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monte le disque du système d’exploitation d’une machine virtuelle en tant que disque de données sur une seconde machine virtuelle. |
| | |

