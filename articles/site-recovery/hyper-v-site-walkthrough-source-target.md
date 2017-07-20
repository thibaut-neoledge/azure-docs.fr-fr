---
title: "Configurer la source et la cible pour la réplication de Hyper-V vers Azure (sans System Center VMM) avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer les paramètres de source et de cible pour la réplication de machines virtuelles Hyper-V vers le stockage Azure avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.contentlocale: fr-fr
ms.lasthandoff: 06/23/2017

---

# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>Étape 8 : configurer la source et la cible pour la réplication de Hyper-V vers Azure

Cet article explique comment configurer les paramètres de source et de cible pour la réplication de machines virtuelles Hyper-V (sans System Center VMM) vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Configurez le site Hyper-V, installez le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur les hôtes Hyper-V et inscrivez le site dans le coffre.

1. Dans **Préparer l’infrastructure**, cliquez sur **Source**. Pour ajouter un nouveau site Hyper-V en tant que conteneur pour vos hôtes ou clusters Hyper-V, cliquez sur **+Site Hyper-V**.

    ![Configurer la source](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. Dans **Créer un site Hyper-V**, spécifiez un nom pour le site. Cliquez ensuite sur **OK**. Sélectionnez le site que vous avez créé, puis cliquez sur **+Serveur Hyper-V** pour ajouter un serveur au site.

    ![Configurer la source](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. Dans **Ajouter un serveur** > **Type de serveur**, vérifiez que **Serveur Hyper-V** est affiché.

    - Assurez-vous que le serveur Hyper-V à ajouter est conforme à la [configuration requise](#on-premises-prerequisites) et est en mesure d’accéder aux URL spécifiées.
    - Téléchargez le fichier d’installation du fournisseur Azure Site Recovery. Vous allez exécuter ce fichier pour installer le fournisseur et l’agent Recovery Services sur chaque hôte Hyper-V.

    ![Configurer la source](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Installer le fournisseur et l’agent

1. Exécutez le fichier d’installation du fournisseur sur chaque hôte que vous avez ajouté au site Hyper-V. Si vous effectuez l’installation sur un cluster Hyper-V, exécutez le programme d’installation sur chaque nœud du cluster. L’installation et l’inscription de chaque nœud de cluster Hyper-V permettent de s’assurer que les machines virtuelles sont protégées, même si elles migrent entre les nœuds.
2. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
3. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation du fournisseur par défaut et cliquez sur **Installer**.
4. Dans **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé du coffre que vous avez téléchargé. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre et le site Hyper-V auquel appartient le serveur Hyper-V.

    ![Enregistrement du serveur](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. Dans **Paramètres du proxy**, indiquez de quelle manière le fournisseur qui s’exécute sur les hôtes Hyper-V doit se connecter à Azure Site Recovery par le biais d’Internet.

    * Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**.
    * Si votre proxy nécessite une authentification ou si vous voulez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **Se connecter directement à Azure Site Recovery avec un serveur proxy**.
    * Si vous utilisez un proxy :
        - Spécifiez l’adresse, le port et les informations d’identification.
        - Vérifiez que les URL décrites dans la [configuration requise](#prerequisites) sont autorisées via le proxy.

    ![Internet](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. Une fois l’installation terminée, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

    ![Emplacement d’installation](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. Une fois l’inscription terminée, les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s’affiche dans **Infrastructure Site Recovery** > **Hôtes Hyper-V**.


## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Spécifiez le compte Azure Storage à utiliser pour la réplication, ainsi que le réseau Azure auquel les machines virtuelles Azure se connecteront après le basculement.

1. Cliquez sur **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles Azure après le basculement. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou gestion des ressources) pour les machines virtuelles.

3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

    - Si vous n’avez pas de compte de stockage, cliquez sur **+Stockage** pour créer un compte basé sur Resource Manager en ligne. 
    - Si vous n’avez pas de réseau Azure, cliquez sur **+Réseau** pour créer un compte basé sur Resource Manager en ligne.






## <a name="next-steps"></a>Étapes suivantes

Accédez à [Étape 9 : configurer une stratégie de réplication](hyper-v-site-walkthrough-replication.md).

