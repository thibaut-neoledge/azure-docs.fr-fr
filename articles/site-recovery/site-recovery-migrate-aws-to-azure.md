---
title: "Migrer des machines virtuelles d’AWS vers Azure | Microsoft Docs"
description: "Cet article décrit comment migrer des machines virtuelles exécutées dans Amazon Web Services (AWS) vers Azure à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Azure avec Azure Site Recovery

Cet article décrit la procédure de migration d’instances Windows AWS vers des machines virtuelles Azure avec le service [Azure Site Recovery](site-recovery-overview.md).

La migration est en fait un basculement d’AWS vers Azure. Il n’est pas possible de restaurer automatiquement les machines et il n’y a pas de réplication continue. Cet article décrit les étapes de migration dans le Portail Azure, qui se basent sur les instructions de [réplication d’une machine physique vers Azure](site-recovery-vmware-to-azure.md).

Publiez des commentaires ou des questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Site Recovery permet de migrer des instances EC2 qui fonctionnent sous l’un des systèmes d’exploitation suivants :

- Windows (64 bits uniquement)
    - Windows Server 2008 R2 SP1+ (pilotes PV Citrix AWS uniquement. **Les instances qui exécutent des pilotes PV RedHat ne sont pas pris en charge**) Windows Server 2012 Windows Server 2012 R2
- Linux (64 bits uniquement)
    - Red Hat Enterprise Linux 6.7 (instances virtualisées HVM uniquement)

## <a name="prerequisites"></a>Composants requis

Voici ce dont vous avez besoin pour ce déploiement

* **Serveur de configuration** : une machine virtuelle locale sous Windows Server 2012 R2 est déployée en tant que serveur de configuration. Par défaut, les autres composants Site Recovery (serveur de traitement et serveur cible maître) sont installés au cours du déploiement du serveur de configuration. [En savoir plus](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **Instances EC2** : les instances de machines virtuelles EC2 que vous souhaitez migrer.

## <a name="deployment-steps"></a>Étapes du déploiement

1. création d'un coffre
2. Déployer le serveur de configuration
3. Après avoir déployé le serveur de configuration, vérifiez qu’il peut communiquer avec les machines virtuelles que vous souhaitez migrer.
4. Configurez les paramètres de réplication.
5. Installez le service Mobilité. Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Les règles de pare-feu sur les instances EC2 que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service. Le groupe de sécurité pour les instances EC2 doit avoir les règles suivantes :

    ![règles de pare-feu](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. Activez la réplication. Activez la réplication pour les machines virtuelles que vous souhaitez migrer. Vous pouvez découvrir les instances EC2 à l’aide de l’adresse IP privée, que vous pouvez obtenir à partir de la console EC2.
7. Exécutez un basculement non planifié). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’AWS vers Azure pour chaque machine virtuelle. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié pour migrer des machines virtuelles d’AWS vers Azure. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

Cliquez sur les liens pour obtenir des instructions détaillées sur les [Étapes de déploiement](site-recovery-vmware-to-azure.md) et pour exécuter un [Basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover).



<!--HONumber=Feb17_HO2-->


