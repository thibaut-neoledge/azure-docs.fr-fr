---
title: "Migrer des machines virtuelles IaaS Azure entre des régions Azure | Microsoft Docs"
description: "Utilisez Site Recovery pour migrer des machines virtuelles IaaS Azure d’une région Azure vers une autre."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery
## <a name="overview"></a>Vue d'ensemble
Bienvenue dans Azure Site Recovery ! Utilisez cet article si vous souhaitez migrer des machines virtuelles Azure entre régions Azure. Avant de commencer, notez les points suivants :

* Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : Azure Resource Manager et classique. De plus, Azure propose deux portails : le portail Azure Classic, qui prend en charge le modèle de déploiement classique, et le portail Azure, qui gère les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes que vous configuriez Site Recovery dans le gestionnaire de ressources ou sur le portail classique. Toutefois, les instructions et captures d’écran de l’interface utilisateur de cet article sont pertinentes pour le portail Azure.
* **Actuellement vous pouvez uniquement migrer d’une région à l’autre. Vous pouvez basculer les machines virtuelles d’une région Azure à une autre, mais vous ne pourrez pas les restaurer par la suite.**

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Conditions préalables
Voici ce dont vous avez besoin pour ce déploiement :

* **Machines virtuelles IaaS**: machines virtuelles à migrer. Vous migrez ces machines virtuelles en les traitant comme des machines physiques.

## <a name="deployment-steps"></a>Étapes du déploiement
Cette section décrit les étapes de déploiement dans le nouveau portail Azure.

1. [Créer un coffre](site-recovery-vmware-to-azure.md).
2. [Activer la réplication](site-recovery-vmware-to-azure.md). Activez la réplication pour les machines virtuelles que vous souhaitez migrer et choisissez Azure comme source. 
3. [ Exécuter un basculement non planifié](site-recovery-failover.md). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’une région Azure vers une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement non planifié pour migrer plusieurs machines virtuelles d’une région à l’autre. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

## <a name="next-steps"></a>Étapes suivantes
Découvrez les autres scénarios de réplication dans [Qu’est-ce que le service Azure Site Recovery ?](site-recovery-overview.md)

