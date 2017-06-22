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
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b0fb6e2b86aa0a47b7250face90be8ab2d06b78e
ms.contentlocale: fr-fr
ms.lasthandoff: 03/15/2017


---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery
## <a name="overview"></a>Vue d'ensemble
Bienvenue dans Azure Site Recovery ! Utilisez cet article si vous souhaitez migrer des machines virtuelles Azure entre régions Azure. Avant de commencer, notez les points suivants :

* Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : Azure Resource Manager et classique. De plus, Azure propose deux portails : le portail Azure Classic, qui prend en charge le modèle de déploiement classique, et le portail Azure, qui gère les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes que vous configuriez Site Recovery dans le gestionnaire de ressources ou sur le portail classique. Toutefois, les instructions et captures d’écran de l’interface utilisateur de cet article sont pertinentes pour le portail Azure.
* **Actuellement vous pouvez uniquement migrer d’une région à l’autre. Vous pouvez basculer les machines virtuelles d’une région Azure à une autre, mais vous ne pourrez pas les restaurer par la suite.**
* Les instructions de migration de cet article sont basées sur les instructions pour la réplication d’une machine physique vers Azure. Il contient des liens vers les étapes de [Réplication de machines virtuelles VMware ou de serveurs physiques sur Azure](site-recovery-vmware-to-azure.md), qui décrit comment répliquer un serveur physique dans le portail Azure.
* Si vous configurez Site Recovery dans le portail classique, suivez les instructions détaillées de [cet article](site-recovery-vmware-to-azure-classic.md).

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Conditions préalables
Voici ce dont vous avez besoin pour ce déploiement :

* **Serveur de configuration**: une machine virtuelle locale exécutant Windows Server 2012 R2 qui agit en tant que serveur de configuration. Vous installez aussi les autres composants Site Recovery (y compris le serveur de traitement et le serveur maître) sur cette machine virtuelle. Pour en savoir plus, consultez [Architecture du scénario](site-recovery-components.md#vmware-to-azure) et [Conditions préalables en matière de serveur de configuration](site-recovery-vmware-to-azure.md#prerequisites).
* **Machines virtuelles IaaS**: machines virtuelles à migrer. Vous migrez ces machines virtuelles en les traitant comme des machines physiques.

## <a name="deployment-steps"></a>Étapes du déploiement
Cette section décrit les étapes de déploiement dans le nouveau portail Azure.

1. [Créer un coffre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Déployer un serveur de configuration](site-recovery-vmware-to-azure.md#prepare-the-configuration-server).
3. Après avoir déployé le serveur de configuration, vérifiez qu’il peut communiquer avec les machines virtuelles que vous souhaitez migrer.
4. [Configurer les paramètres de réplication](site-recovery-vmware-to-azure.md#set-up-replication-settings). Créez une stratégie de réplication et affectez-la au serveur de configuration.
5. [Installer le service Mobilité](site-recovery-vmware-to-azure.md#prepare-vms-for-replication). Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Les règles de pare-feu sur les machines virtuelles que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service.
6. [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). Activez la réplication pour les machines virtuelles que vous souhaitez migrer. Vous pouvez découvrir les machines virtuelles IaaS que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée des machines virtuelles. Trouvez cette adresse sur le tableau de bord de machine virtuelle dans Azure. Lorsque vous activez la réplication, vous définissez le type de machine pour les machines virtuelles comme des machines physiques.
7. [ Exécuter un basculement non planifié](site-recovery-failover.md). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’une région Azure vers une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement non planifié pour migrer plusieurs machines virtuelles d’une région à l’autre. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

## <a name="next-steps"></a>Étapes suivantes
Découvrez les autres scénarios de réplication dans [Qu’est-ce que le service Azure Site Recovery ?](site-recovery-overview.md)

