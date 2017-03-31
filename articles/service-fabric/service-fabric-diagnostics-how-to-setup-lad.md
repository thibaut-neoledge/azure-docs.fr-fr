---
title: Collecte des journaux avec Linux Azure Diagnostics | Microsoft Docs
description: "Cet article décrit la procédure de configuration d’Azure Diagnostics pour la collecte de journaux d’un cluster Linux Service Fabric exécuté dans Azure."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a160d469-8b7d-4560-82dd-8500db34a44a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 46b62b0ebc5b81241815e3b5b4fa3fe275b88af1
ms.lasthandoff: 03/27/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Collecte des journaux avec Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux facilite l’analyse et la résolution des problèmes concernant vos services, votre application, voire le cluster lui-même. L’une des façons de charger et collecter les journaux consiste à utiliser l’extension Azure Diagnostics, qui charge les journaux dans Azure Storage, Azure Application Insights ou Azure Event Hubs. Vous pouvez également lire les événements du stockage ou d’Event Hubs et les placer dans un produit comme [Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou une autre solution d’analyse de journaux. [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) est fourni avec un service complet de recherche dans les journaux et un service d’analyse intégré.

## <a name="log-sources-that-you-might-want-to-collect"></a>Sources de journaux que vous pourriez vouloir collecter
* **Journaux Service Fabric** : émis par la plateforme via [LTTng](http://lttng.org) et chargés dans votre compte de stockage. Les journaux peuvent porter sur les événements opérationnels ou d’exécution émis par la plate-forme. Ils sont stockés dans l’emplacement spécifié par le manifeste de cluster. (Pour obtenir les informations sur le compte de stockage, recherchez la balise **AzureTableWinFabETWQueryable**, puis **StoreConnectionString**.)
* **Événements d’application** : émis par le code de votre service. Vous pouvez utiliser une solution de journalisation qui écrit des fichiers journaux textuels, par exemple LTTng. Pour plus d’informations, consultez la documentation de LTTng sur le traçage de votre application.  

## <a name="deploy-the-diagnostics-extension"></a>Déployer l’extension Diagnostics
La première étape de la collecte de journaux consiste à déployer l’extension Diagnostics sur chaque machine virtuelle du cluster Service Fabric. Cette extension collecte les journaux sur chaque machine virtuelle et les charge dans le compte de stockage que vous spécifiez. Les étapes varient selon que vous utilisez le portail Azure ou Azure Resource Manager.

Pour déployer l’extension Diagnostics sur les machines virtuelles du cluster lors de la création de ce dernier, définissez **Diagnostics** sur **Activé**. Une fois le cluster créé, vous ne pouvez plus modifier ce paramètre à l’aide du portail.

Ensuite, configurez Linux Azure Diagnostics (LAD) pour collecter les fichiers et les placer dans votre compte de stockage. Ce processus est détaillé dans le scénario 3 (Charger vos propres fichiers journaux) de l’article [Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Cette procédure vous fournit un accès aux suivis. Vous pouvez charger les suivis dans le visualiseur de votre choix.

Vous pouvez également déployer l’extension Diagnostics à l’aide d’Azure Resource Manager. Le processus, identique pour Windows et Linux, est documenté pour les clusters Windows, dans la section [Collecte des journaux avec Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md).

Vous pouvez également utiliser Operations Management Suite, comme indiqué dans [Operations Management Suite Log Analytics with Linux (Analyse des journaux Operations Management Suite avec Linux)](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Une fois cette configuration effectuée, l’agent LAD analyse les fichiers journaux spécifiés. Dès qu’une nouvelle ligne est ajoutée au fichier, elle crée une entrée dans le journal syslog, qui est envoyée au stockage que vous avez spécifié.

## <a name="next-steps"></a>Étapes suivantes
Pour identifier plus précisément les événements à consulter lors de la résolution des problèmes, consultez la [documentation de LTTng](http://lttng.org/docs) et la section [Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


