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
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Azure avec Azure Site Recovery

Cet article décrit la procédure de migration d’instances Windows AWS vers des machines virtuelles Azure avec le service [Azure Site Recovery](site-recovery-overview.md).

La migration est en fait un basculement d’AWS vers Azure. Vous ne pouvez pas restaurer automatiquement les machines et il n’y a pas de réplication continue. Cet article décrit les étapes de migration dans le Portail Azure, qui se basent sur les instructions de [réplication d’une machine physique vers Azure](site-recovery-vmware-to-azure.md).

Publiez des commentaires ou des questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Site Recovery permet de migrer des instances EC2 qui fonctionnent sous l’un des systèmes d’exploitation suivants :

- Windows (64 bits uniquement)
    - Windows Server 2008 R2 SP1+ (pilotes PV Citrix AWS uniquement. **Les instances qui exécutent des pilotes PV RedHat ne sont pas pris en charge**) Windows Server 2012 Windows Server 2012 R2
- Linux (64 bits uniquement)
    - Red Hat Enterprise Linux 6.7 (instances virtualisées HVM uniquement)

## <a name="prerequisites"></a>Composants requis

Voici ce dont vous avez besoin pour ce déploiement :

* **Serveur de configuration** : une machine virtuelle Amazon EC2 sous Windows Server 2012 R2 est déployée en tant que serveur de configuration. Par défaut, les autres composants Azure Site Recovery (serveur de traitement et serveur cible maître) sont installés pendant le déploiement du serveur de configuration. Cet article décrit les étapes de migration dans le portail Azure. Celles-ci s’appuient sur les instructions de la section [En savoir plus](site-recovery-components.md).

* **Instances EC2** : les instances de machines virtuelles Amazon EC2 que vous souhaitez migrer.

## <a name="deployment-steps"></a>Étapes du déploiement

1. Créez un coffre Recovery Services.
2. Le groupe de sécurité de vos instances EC2 doit avoir des règles configurées pour permettre la communication entre l’instance EC2 que vous souhaitez migrer et l’instance sur laquelle vous envisagez de déployer le Serveur de configuration.

3. Sur le même cloud privé virtuel Amazon que vos instances EC2, déployez un serveur de configuration ASR. Consultez les conditions préalables pour un déploiement de machines virtuelles VMware et de serveurs physiques sur Azure et ainsi connaître les composants nécessaires à la configuration d’un déploiement serveur.

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  Une fois votre serveur de configuration déployé dans AWS et inscrit avec votre coffre Recovery Services, vous devriez voir le serveur de configuration et le serveur de traitement dans l’infrastructure Site Recovery comme indiqué ci-dessous :

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. Après avoir déployé le serveur de configuration (son affichage peut prendre jusqu’à 15 minutes), vérifiez qu’il peut communiquer avec les machines virtuelles que vous souhaitez migrer.

6. [Configurer les paramètres de réplication](site-recovery-setup-replication-settings-vmware.md).

7. Activer la réplication : activez la réplication pour les machines virtuelles que vous souhaitez migrer. Vous pouvez découvrir les instances EC2 à l’aide de l’adresse IP privée, que vous pouvez obtenir à partir de la console EC2.

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. Une fois que les instances EC2 ont été protégées et que la réplication vers Azure est terminée, [exécutez un test de basculement](site-recovery-test-failover-to-azure.md) pour valider les performances de votre application dans Azure.

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Effectuer un basculement de AWS vers Azure pour chaque machine virtuelle. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement pour migrer des machines virtuelles d’AWS vers Azure. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

10. Pour la migration, vous n’avez pas besoin de valider de basculement. Au lieu de cela, sélectionnez l’option Terminer la migration pour chacune des machines que vous souhaitez migrer. L’action Terminer la migration met un terme au processus de migration, supprime la réplication de la machine, puis arrête la facturation de Site Recovery pour celle-ci.

    ![Migrer](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>Étapes suivantes

- [Replicate Azure VMs to another region after migration to Azure using Azure Site Recovery](site-recovery-azure-to-azure-after-migration.md) (Répliquer des machines virtuelles Azure vers une autre région après la migration vers Azure à l’aide d’Azure Site Recovery).
- Commencez à protéger vos charges de travail en [répliquant des machines virtuelles Azure.](site-recovery-azure-to-azure.md)
