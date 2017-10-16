---
title: "Configurer la source et la cible pour la réplication de serveurs physiques vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer les paramètres sources et cibles liés à la réplication de serveurs physiques vers un stockage Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>Étape 7 : Configurer la source et la cible pour la réplication de serveurs physiques vers Azure

Cet article explique comment configurer des paramètres sources et cibles lors de la réplication de serveurs physiques locaux vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Configurez le serveur de configuration, inscrivez-le dans le coffre et découvrez les machines.

1. Cliquez sur **Site Recovery** > **Étape 1 : Préparer l’infrastructure** > **Source**.
2. Si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
4. Téléchargez le fichier d’installation unifiée de Site Recovery.
5. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lors de l’exécution du programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

   ![Configurer la source](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>inscrire le serveur de configuration dans le coffre

Suivez les étapes suivantes avant de commencer, puis exécutez le programme d’installation unifiée afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître. La vidéo décrit comment configurer les composants pour une réplication de machine virtuelle VMware vers Azure. Toutefois, le même processus vaut pour la réplication de serveurs physiques vers Azure.

- Sur la machine virtuelle du serveur de configuration, assurez-vous que l’horloge système est synchronisée avec un [Serveur de temps](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Elles doivent correspondre. S’il y a 15 minutes d’avance ou de retard, le programme d’installation peut échouer.
- Exécutez le programme d’installation en tant qu’administrateur local sur la machine du serveur de configuration.
- Assurez-vous que TLS 1.0 est activé sur la machine virtuelle.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Le serveur de configuration peut également être installé [en ligne de commande](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Avant de configurer l’environnement cible, assurez-vous de disposer d’un compte de stockage Azure et d’un réseau virtuel déjà configuré.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez si votre modèle de déploiement cible est basé sur Resource Manager ou est de type classique.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

   ![Cible](./media/physical-walkthrough-source-target/gs-target.png)

4. Si vous n’avez pas créé de compte de stockage ou de réseau, cliquez sur **+Compte de stockage** ou **+Réseau** pour créer un compte Resource Manager ou un réseau en ligne.

## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 8 : Configurer une stratégie de réplication](physical-walkthrough-replication.md)
