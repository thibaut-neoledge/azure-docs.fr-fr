---
title: "Configuration de la source et de la cible pour la réplication Hyper-V sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment configurer la source et la cible lors de la réplication de machines virtuelles Hyper-V sur un site secondaire VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---
# <a name="step-6-set-up-the-replication-source-and-target"></a>Étape 6 : Configurer la source et la cible de réplication


Après avoir créé un coffre Recovery Services pour la réplication Hyper-V sur un site secondaire VMM avec [Azure Site Recovery](site-recovery-overview.md), référez-vous à cet article pour configurer les emplacements de réplication source et cible. 

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Installez le fournisseur Azure Site Recovery sur les serveurs VMM puis découvrez et inscrivez ces derniers dans le coffre.

1. Cliquez sur **Étape 1 : Préparer l’infrastructure** > **Source**.

    ![Configurer la source](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. Dans **Préparer la source**, cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans le champ **Type de serveur** et que le serveur VMM répond à la [configuration requise](#prerequisites).
4. Téléchargez le fichier d’installation du fournisseur Azure Site Recovery.
5. Téléchargez la clé d’inscription. Vous en aurez besoin lorsque vous exécuterez le programme d’installation. Une fois générée, la clé reste valide pendant 5 jours.

    ![Configurer la source](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. Installez le fournisseur Azure Site Recovery sur le serveur VMM. Vous n’avez besoin de rien installer explicitement sur les serveurs hôtes Hyper-V.


## <a name="install-the-azure-site-recovery-provider"></a>installation du fournisseur Azure Site Recovery

1. Exécutez le fichier d’installation du fournisseur sur chaque serveur VMM. Si VMM est déployé dans un cluster, procédez comme suit lors de la première installation :
    -  Installez le fournisseur sur un nœud actif et terminez l’installation pour inscrire ce serveur VMM dans le coffre.
    - Ensuite, installez le fournisseur sur les autres nœuds. Les nœuds de cluster doivent exécuter la même version du fournisseur.
2. Le programme d’installation exécute quelques vérifications préalables et demande l’autorisation d’arrêter le service VMM. Le service VMM redémarre automatiquement une fois l’installation terminée. Si vous installez sur un cluster VMM, vous êtes invité à arrêter le rôle de cluster.
3. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
4. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation par défaut et cliquez sur **Installer**.

    ![Emplacement d’installation](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. Une fois l’installation terminée, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

    ![Emplacement d’installation](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré. Cliquez sur *Next*.

    ![Enregistrement du serveur](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. Sur la page **Connexion Internet** , indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Azure.

    ![Paramètres Internet](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - Vous pouvez définir la connexion directe, ou via un proxy, du fournisseur à Internet.
   - Spécifiez les paramètres de proxy, si nécessaire.
   - Si vous utilisez un proxy, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs dans la console VMM (**Paramètres** > **Sécurité** > **Comptes d’identification**). Redémarrez le service VMM afin de mettre à jour les modifications.
8. Dans **Registration Key**, sélectionnez la clé que vous avez téléchargée depuis Azure Site Recovery et copiez-la sur le serveur VMM.
9. Le paramètre de chiffrement est uniquement utilisé quand vous répliquez des machines virtuelles Hyper-V dans des clouds VMM sur Azure. Si vous répliquez sur un site secondaire, il n’est pas utilisé.
10. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
11. Dans **Synchroniser les métadonnées du cloud** , indiquez si vous voulez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l’aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud dans les propriétés du cloud de la console VMM.
12. Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l’onglet **Serveurs VMM** de la page **Serveurs** du coffre.

    ![Serveur](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Une fois le serveur disponible dans la console Site Recovery, dans **Source** > **Préparer la source** , sélectionnez le serveur VMM, puis sélectionnez le cloud dans lequel se trouve l’hôte Hyper-V. Cliquez ensuite sur **OK**.

Vous pouvez également installer le fournisseur à partir de la ligne de commande :

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez le cloud et le serveur VMM cible :

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez le serveur VMM cible à utiliser.
2. Les clouds du serveur qui sont synchronisés avec Site Recovery s’affichent. Sélectionnez le cloud cible.

   ![Cible](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>Étapes suivantes

Allez à [Étape 7 : Configurer le mappage réseau](vmm-to-vmm-walkthrough-network-mapping.md).

