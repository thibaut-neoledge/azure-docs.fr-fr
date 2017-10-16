---
title: "Configurer la source et la cible pour la réplication VMware sur Azure à l’aide de Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer les paramètres source et cible pour la réplication de machines virtuelles VMware sur un stockage Azure à l’aide de Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>Étape 8 : configurer la source et la cible pour la réplication VMware sur Azure

Cet article explique comment configurer des paramètres source et cible lors de la réplication de machines virtuelles VMware locales sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Configurez le serveur de configuration, inscrivez-le dans le coffre et découvrez les machines virtuelles.

1. Cliquez sur **Site Recovery** > **Étape 1 : Préparer l’infrastructure** > **Source**.
2. Si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
4. Téléchargez le fichier d’installation unifiée de Site Recovery.
5. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lors de l’exécution du programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

   ![Configurer la source](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>inscrire le serveur de configuration dans le coffre

Suivez les étapes suivantes avant de commencer, puis exécutez le programme d’installation unifiée afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître.
    - Visionnez une courte vidéo de présentation :

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - Sur la machine virtuelle du serveur de configuration, assurez-vous que l’horloge système est synchronisée avec un [Serveur de temps](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Elles doivent correspondre. S’il y a 15 minutes d’avance ou de retard, le programme d’installation peut échouer.
    - Exécutez le programme d’installation en tant qu’administrateur local sur la machine virtuelle du serveur de configuration.
    - Assurez-vous que TLS 1.0 est activé sur la machine virtuelle.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Le serveur de configuration peut également être installé [en ligne de commande](http://aka.ms/installconfigsrv).



## <a name="connect-to-vmware-servers"></a>Se connecter à des serveurs VMware

Pour permettre à Azure Site Recovery de détecter les machines virtuelles en cours d’exécution dans votre environnement local, vous devez connecter votre serveur VMware vCenter ou vos hôtes ESXi vSphere à Site Recovery. Avant de commencer, prenez note que :

- Si vous ajoutez le serveur vCenter ou des hôtes vSphere à Site Recovery avec un compte ne disposant pas de droits d’administrateur sur le serveur, ces privilèges doivent être activés pour le compte :
    - Centre de données, magasin de données, dossier, hôte, réseau, ressource, machine virtuelle, vSphere Distributed Switch.
    - Le serveur vCenter a besoin des autorisations d’affichage de stockage.
- Lorsque vous ajoutez des serveurs VMware à Site Recovery, il peut s’écouler 15 minutes ou plus avant leur affichage dans le portail.

### <a name="add-the-account-for-automatic-discovery"></a>Ajouter le compte pour la découverte automatique

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>Configurer une connexion

Connectez-vous aux serveurs comme suit :

1. Sélectionnez **+vCenter** pour connecter un serveur VMware vCenter ou un ordinateur hôte VMware vSphere ESXi.
2. Dans **Ajouter un serveur vCenter** , spécifiez un nom convivial pour l’hôte vSphere ou le serveur vCenter, puis indiquez l’adresse IP ou le nom de domaine complet public du serveur.
3. Conservez le port 443, sauf si vos serveurs VMware sont configurés pour écouter les demandes sur un port différent. Sélectionnez le compte à connecter au serveur VMware vCenter ou vSphere ESXi. Cliquez sur **OK**.
4. Site Recovery se connecte aux serveurs VMware en utilisant les paramètres spécifiés, et découvre les machines virtuelles.

> [!NOTE]
> Si vous ajoutez un serveur ou un hôte à l’aide d’un compte qui ne dispose pas des privilèges d’administrateur sur le serveur vCenter ou le serveur hôte, assurez-vous que le compte bénéficie des privilèges suivants : centre de données, magasin de données, dossier, hôte, réseau, ressource, machine virtuelle, et commutateur distribué vSphere. En outre, le serveur VMware vCenter exige que le privilège d’affichage de stockage soit activé.


## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Avant de configurer l’environnement cible, assurez-vous de disposer d’un compte de stockage Azure et d’un réseau virtuel déjà configuré.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez si votre modèle de déploiement cible est basé sur Resource Manager ou est de type classique.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

   ![Cible](./media/vmware-walkthrough-source-target/gs-target.png)
4. Si vous n’avez pas créé de compte de stockage ou de réseau, cliquez sur **+Compte de stockage** ou **+Réseau** pour créer un compte Resource Manager ou un réseau en ligne.

## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 9 : configurer une stratégie de réplication](vmware-walkthrough-replication.md)
