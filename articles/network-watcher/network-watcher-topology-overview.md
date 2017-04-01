---
title: "Présentation de la topologie dans Azure Network Watcher | Microsoft Docs"
description: "Cette page fournit une vue d’ensemble des fonctionnalités de topologie de Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.lasthandoff: 03/31/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Présentation de la topologie dans Azure Network Watcher

La topologie renvoie un graphique des ressources réseau figurant dans un réseau virtuel. Ce graphique illustre l’interconnexion entre les ressources afin de représenter la connectivité réseau de bout en bout.

![Vue d’ensemble de la topologie][1]

Dans le portail, la fonction Topologie renvoie les objets de ressource par réseau virtuel. Les relations sont représentées par des lignes entre les ressources. Les ressources situées à l’extérieur de la région Network Watcher, même si elles figurent dans le groupe de ressources, ne sont pas affichées. Les ressources renvoyées dans la vue du portail sont un sous-ensemble des composants réseau qui sont représentés graphiquement. Pour visualiser la liste complète des ressources réseau, vous pouvez utiliser [PowerShell](network-watcher-topology-powershell.md) ou [REST](network-watcher-topology-rest.md).

> [!NOTE]
> Une instance de Network Watcher est requise dans chaque région sur laquelle vous souhaitez exécuter la fonction Topologie.

Lorsque les ressources sont renvoyées, la connexion entre ces dernières est modélisée sous deux relations.

- **Imbrication** - Exemple : un réseau virtuel contient un sous-ensemble contenant une carte réseau.
- **Associé** - Exemple : une carte réseau est associée à une machine virtuelle.

### <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser PowerShell pour récupérer la vue Topologie en consultant l’article [Network Watcher topology with PowerShell](network-watcher-topology-powershell.md) (Visualiser la topologie Network Watcher avec PowerShell).

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

