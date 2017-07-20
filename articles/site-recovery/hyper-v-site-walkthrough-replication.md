---
title: "Configurer une stratégie de réplication pour la réplication de machines virtuelles Hyper-V (sans System Center VMM) vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer une stratégie de réplication pour la réplication de machines virtuelles Hyper-V vers le stockage Azure."
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 42a0421b4589b95c0da806be0ec8875b7d62c91d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/23/2017

---

# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>Étape 9 : configurer une stratégie de réplication pour la réplication de machines virtuelles Hyper-V vers Azure

Cet article explique comment configurer une stratégie de réplication pour la réplication de machines virtuelles Hyper-V vers Azure (sans System Center VMM) à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="about-snapshots"></a>Informations concernant les instantanés

Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle.
    - Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés.
    - Si vous activez les instantanés cohérents avec l'application, cela affecte les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.

## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.

    ![Réseau](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie.
3. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).

    > [!NOTE]
    > Une fréquence de 30 secondes n’est pas prise en charge lors de la réplication sur un Stockage Premium. La limitation est déterminée par le nombre de captures instantanées par objet blob (100) pris en charge par le Stockage Premium. [Plus d’informations](../storage/storage-premium-storage.md#snapshots-and-copy-blob)

4. Dans **Rétention des points de récupération**, spécifiez la durée (en heures) de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
5. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre 1 et 12 heures).
6. Dans **Heure de début de la réplication initiale**, indiquez à quel moment démarrer la réplication initiale. La réplication se produit via votre bande passante Internet. Il est donc préférable de prévoir son exécution en dehors des heures de bureau. Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

Lorsque vous créez une stratégie, elle est automatiquement associée au site Hyper-V. Vous pouvez associer un site Hyper-V (et les machines virtuelles qu’il contient) à plusieurs stratégies de réplication dans **Réplication** > nom de la stratégie > **Associer le site Hyper-V**.



## <a name="next-steps"></a>Étapes suivantes

Accédez à [Étape 10 : activer la réplication](hyper-v-site-walkthrough-enable-replication.md).

