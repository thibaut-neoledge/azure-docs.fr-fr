---
title: "Configurer la récupération d’urgence pour les machines virtuelles Hyper-V locales vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 96e5027adfb443aba18895213e8d83894e3f060a
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel vous montre comment configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure. Le didacticiel s’applique aux machines virtuelles Hyper-V qui sont gérées dans des clouds System Center Virtual Machine Manager (VMM) et à celles qui ne le sont pas. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Définir les conditions préalables Azure et locales
> * Créer un coffre Recovery Services pour Site Recovery 
> * Configurer les environnements de réplication source et cible 
> * Configurer le mappage réseau (si Hyper-V est géré par System Center VMM)
> * Créer une stratégie de réplication
> * Activer la réplication d’une machine virtuelle


## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](concepts-hyper-v-to-azure-architecture.md).
- Examinez les [exigences de prise en charge](site-recovery-support-matrix-to-azure.md) pour tous les composants.
- Assurez-vous que les ordinateurs virtuels que vous souhaitez répliquer soient conformes aux [conditions requises pour les machines virtuelles Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Pour déterminer la planification de la capacité :
    - Utilisez l’[outil de planification de la capacité Hyper-V](http://aka.ms/asr-capacity-planner-excel) pour déterminer le taux de variation.
    - Utilisez l’[outil de planification de la capacité Site Recovery](http://aka.ms/asr-capacity-planner-excel) pour analyser votre environnement source, évaluer la bande passante et cibler des besoins.
- Préparez Azure. Vous avez besoin d’un abonnement Azure, d’un réseau virtuel Azure et d’un compte de stockage.
- Préparez des hôtes Hyper-V locaux et des serveurs VMM si nécessaire.





### <a name="set-up-an-azure-account"></a>Configurer un compte Azure

Procurez-vous un [compte Microsoft Azure](http://azure.microsoft.com/).

- Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Lisez les informations relatives à la [tarification de Site Recovery](site-recovery-faq.md#pricing) et prenez connaissance de la [tarification appliquée](https://azure.microsoft.com/pricing/details/site-recovery/).
- Identifiez les [régions prises en charge](https://azure.microsoft.com/pricing/details/site-recovery/) pour Site Recovery.

### <a name="verify-azure-account-permissions"></a>Vérifier les autorisations de compte Azure

Assurez-vous que votre compte Azure a les autorisations requises pour répliquer des machines virtuelles.

- Examinez les [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) nécessaires pour répliquer des machines vers Azure
- Vérifiez et modifiez les autorisations [d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md). 


### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Configurez un [réseau Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- Les machines virtuelles Azure sont placées dans ce réseau une fois créées après le basculement.
- Ce réseau doit se trouver dans la même région que le coffre Recovery Services.


### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

Configurez un [compte de stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery réplique les machines virtuelles locales sur le stockage Azure. Des machines virtuelles Azure sont créées à partir du stockage après le basculement.
- Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services.
- Le compte de stockage peut être standard ou [premium](../storage/common/storage-premium-storage.md).
- Si vous configurez un compte premium, vous avez besoin d’un compte standard supplémentaire pour les données de journal.

### <a name="prepare-hyper-v-hosts"></a>Préparer les hôtes Hyper-V

1. Vérifiez que les hôtes Hyper-V [prennent en charge les exigences](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Vérifiez que les hôtes peuvent accéder à ces URL :

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Toutes les règles de pare-feu basées sur une adresse IP doivent autoriser les communications vers Azure.
    - Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).

### <a name="prepare-vmm-servers"></a>Préparer les serveurs VMM

Si les hôtes Hyper-V sont gérés par VMM, vous devez préparer le serveur VMM local. 

- Vérifiez que le serveur VMM répond aux [exigences de support](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Vérifiez que le serveur VMM a un moins un cloud, avec un ou plusieurs groupes hôtes. L’hôte Hyper-V sur lequel les machines virtuelles s’exécutent doivent se trouver dans le cloud.
- Le serveur VMM requiert un accès Internet, avec un accès aux URL suivantes :

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Toutes les règles de pare-feu basées sur une adresse IP doivent autoriser les communications vers Azure.
    - Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).
- Préparez le serveur VMM pour le mappage réseau.


#### <a name="prepare-vmm-for-network-mapping"></a>Préparer VMM pour le mappage réseau

Si vous utilisez VMM, le [mappage réseau](site-recovery-network-mapping.md) effectue un mappage entre les réseaux de machines virtuelles VMM locales et les réseaux virtuels Azure. Le mappage garantit que les machines virtuelles Azure soient connectées au réseau adéquat lorsqu’elles sont créées après le basculement.

Préparez VMM pour le mappage réseau comme suit :

1. Vérifiez que vous disposez d’un [réseau logique VMM](https://docs.microsoft.com/system-center/vmm/network-logical) qui est associé au cloud dans lequel se trouvent les hôtes Hyper-V.
2. Assurez-vous d’avoir un [réseau de machines virtuelles](https://docs.microsoft.com/system-center/vmm/network-virtual) liées au réseau logique.
3. Connectez les machines virtuelles au réseau de machines virtuelles.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Sélectionner un objectif de protection

Sélectionnez l’élément à répliquer et l’emplacement où il doit être répliqué.

1. Dans le coffre, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
2. Dans **Objectif de protection**, sélectionnez **To Azure (Vers Azure)**> **, puis Oui, avec Hyper-V**. 
    - Si les hôtes Hyper-V ne sont pas gérés par VMM, sélectionnez **Non** pour confirmer que vous n’utilisez pas VMM.
    - Si les hôtes sont gérés dans des clouds VMM, sélectionnez **Oui**.

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Lorsque vous configurez l’environnement source, vous installez le fournisseur Azure Site Recovery et l’agent Azure Recovery Services, et inscrivez les serveurs locaux dans le coffre. 

1. Dans **Préparer l’infrastructure**, cliquez sur **Source**. 
    - Si vous n’utilisez pas VMM, cliquez sur **+ Site Hyper-V** et spécifiez un nom de site. Ensuite, cliquez sur **+ Hyper-V Server** et ajoutez un hôte ou un cluster au site.
    - Si vous utilisez VMM, dans **Préparer la source**, cliquez sur **+ VMM** pour ajouter un serveur VMM. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans **Type de serveur**.
2. Téléchargez les composants du fournisseur et de l’agent, en fonction de votre environnement.
    - Pour Hyper-V uniquement, téléchargez le fichier d’installation du fournisseur. Vous exécutez le fichier sur chaque hôte Hyper-V pour installer le fournisseur et l’agent.
        
        ![Fournisseur sans VMM](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Pour Hyper-V avec VMM, téléchargez le fournisseur et l’agent séparément. Exécutez le fichier du fournisseur sur le serveur VMM. Exécutez l’installation de l’agent sur chaque hôte Hyper-V.
    
        ![Fournisseur et agent avec VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lorsque vous exécuterez le programme d’installation du fournisseur. Une fois générée, la clé reste valide pendant 5 jours.

### <a name="install-components"></a>Installer les composants

Installez les composants comme indiqué sous forme de résumé dans le tableau. 

**Composant** | **Détails** | **Hyper-V uniquement** | **Hyper-V avec VMM**
--- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Orchestre la réplication vers Azure | Installer sur chaque hôte Hyper-V | Installer sur le serveur VMM
**Agent Recovery Services** | Gère la réplication des données | Installer sur chaque hôte Hyper-V | Installer sur le serveur hôte Hyper-V


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Installer le fournisseur sur Hyper-V sans VMM

Installez le fournisseur sur chaque hôte Hyper-V que vous avez ajouté au site Hyper-V. Si vous effectuez l’installation sur un cluster Hyper-V, exécutez le programme d’installation sur chaque nœud du cluster. L’installation et l’inscription de chaque nœud de cluster Hyper-V permettent de s’assurer que les machines virtuelles sont protégées, même si elles migrent entre les nœuds.

1. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
2. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation du fournisseur par défaut et cliquez sur **Installer**.
4. Dans **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé du coffre que vous avez téléchargé. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre et le site Hyper-V auquel appartient le serveur Hyper-V.
5. Dans **Paramètres du proxy**, indiquez comment le fournisseur s’exécutant sur le serveur VMM ou l’hôte Hyper-V se connecte à Site Recovery par le biais d’Internet.
    * Pour une connexion directe, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**.
    * Pour un serveur proxy, sélectionnez **Se connecter à Azure Site Recovery à l’aide d’un serveur proxy**. Spécifiez l’adresse, le port et les informations d’identification du serveur proxy si nécessaire.
6. Une fois que le serveur est inscrit dans le coffre, cliquez sur **Terminer**.

Les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s’affiche dans **Infrastructure Site Recovery** > **Hôtes Hyper-V**.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Installer l’agent Recovery Services sur l’hôte Hyper-V sans VMM

Si vous utilisez VMM dans votre déploiement, exécutez le programme d’installation de l’agent Recovery Services agent sur chaque hôte Hyper-V.

1. Dans l’Assistant Installation > **Vérification de la configuration requise**, cliquez sur **Suivant**. Tous les éléments manquants de la configuration requise sont automatiquement installés.

    ![Prerequisites Recovery Services Agent](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. Sous **Paramètres d’installation**, acceptez ou modifiez l’emplacement d’installation et l’emplacement du cache. Le lecteur de cache doit comporter au moins 5 Go d’espace de stockage. Nous recommandons un lecteur avec au moins 600 Go d’espace libre. Cliquez ensuite sur **Installer**.
4. Dans **Installation**, lorsque l’installation est terminée, cliquez sur **Fermer** pour quitter l’Assistant.

##### <a name="set-up-internet-access-via-a-proxy"></a>Configurer l’accès Internet via un proxy

L’agent Recovery Services sur l’hôte Hyper-V requiert un accès à Azure, via Internet, pour la réplication des machines virtuelles. Si vous accédez à Internet via un proxy, configurez-le comme suit :

1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup sur l’hôte Hyper-V. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le Bureau. Vous pouvez également le trouver ici : C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Dans l’onglet **Configuration du proxy**, spécifiez les informations du proxy.

    ![Inscrire l’Agent MARS](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Vérifiez que l’agent peut atteindre les [URL requises](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Installer le fournisseur sur le serveur VMM (Hyper-V avec VMM)

1. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
2. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation du fournisseur par défaut et cliquez sur **Installer**. 
3. Maintenant, inscrivez le serveur VMM auprès du coffre. Dans **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé du coffre que vous avez téléchargé. Spécifiez l’abonnement Azure Site Recovery et le nom du coffre.

    ![Enregistrement du serveur](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. Dans **Paramètres du proxy**, indiquez comment le fournisseur s’exécutant sur le serveur VMM ou l’hôte Hyper-V se connecte à Site Recovery par le biais d’Internet.

    * Pour une connexion directe, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**.
    * Pour un serveur proxy, sélectionnez **Se connecter à Azure Site Recovery à l’aide d’un serveur proxy**. Spécifiez l’adresse, le port et les informations d’identification du serveur proxy si nécessaire.
    - Un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs dans la console VMM (**Paramètres** > **Sécurité** > **Comptes d’identification**). Redémarrez le service VMM afin de mettre à jour les modifications.
5. Dans **Chiffrement des données**, indiquez si vous souhaitez que toutes les données envoyées vers Azure soient chiffrées. Si vous sélectionnez cette option, Site Recovery envoie un certificat. Vous aurez besoin de ce certificat pour déchiffrer les données ultérieurement.
6. Dans **Serveur VMM**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM. Dans **Synchroniser les métadonnées du cloud avec le coffre** , indiquez si vous voulez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l’aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud dans les propriétés du cloud de la console VMM.

Une fois l’inscription terminée, les métadonnées du serveur sont récupérées par Azure Site Recovery et le serveur VMM s’affiche dans **Infrastructure Site Recovery**.






## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles. 

1. Cliquez sur **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources dans lesquels les machines virtuelles Azure vont être créées après le basculement. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure pour les machines virtuelles.

3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

## <a name="configure-network-mapping-with-vmm"></a>Configurer le mappage réseau (avec VMM)

Si vous utilisez VMM, configurez le mappage de réseau.

1. Dans **Infrastructure Site Recovery** > **Mappages réseau** > **Mappage réseau**, cliquez sur l’icône**+Mappage réseau**.
2. Dans la zone **Ajouter un mappage réseau**, sélectionnez le serveur VMM source. Sélectionnez **Azure** comme cible.
3. Vérifiez l’abonnement et le modèle de déploiement après le basculement.
4. Dans **Réseau Source**, sélectionnez le réseau de machines virtuelles locales sources.
5. Dans **Réseau cible**, choisissez le réseau Azure dans lequel les machines virtuelles Azure de réplica se trouveront, une fois créées après le basculement. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

1. Cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie.
3. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).

    > [!NOTE]
    >  Une fréquence de 30 secondes n’est pas prise en charge lors de la réplication sur un Stockage Premium. La limitation est déterminée par le nombre de captures instantanées par objet blob (100) pris en charge par le Stockage Premium. [Plus d’informations](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. Dans **Rétention des points de récupération**, spécifiez la durée (en heures) de la fenêtre de rétention pour chaque point de récupération. Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
5. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre 1 et 12 heures). Hyper-V utilise deux types de captures instantanées :
    - **Instantané standard** : fournit un instantané incrémentiel de toute la machine virtuelle.
    - **Instantané de cohérence des applications** : prend un instantané à un point dans le temps des données d’application à l’intérieur de la machine virtuelle. Le service VSS (Volume Shadow Copy Service) s'assure que les applications sont dans un état cohérent au moment de la prise des captures instantanées. L’activation d’instantanés cohérents au niveau applicatif affecte la performance de l’application sur les machines virtuelles sources. Définissez une valeur inférieure au nombre de points de récupération supplémentaires que vous configurez.
6. Dans **Heure de début de la réplication initiale**, indiquez à quel moment démarrer la réplication initiale. La réplication se produit via votre bande passante Internet. Il est donc préférable de prévoir son exécution en dehors des heures de pointe.
7. Dans **Chiffrer les données stockées sur Azure**, indiquez si les données au repos doivent être chiffrées dans Azure Storage. Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/tutorial-hyper-v-to-azure/replication-policy.png)


Lorsque vous créez une stratégie, elle est automatiquement associée au cloud VMM ou au site Hyper-V.

## <a name="enable-replication"></a>Activer la réplication


1. Cliquez sur **Répliquer l’application** > **Source**. 
2. Dans **Source**, sélectionnez le site Hyper-V/serveur VMM/cloud. Cliquez ensuite sur **OK**.
3. Dans **Cible**, vérifiez Azure en tant que cible, l’abonnement du coffre et le modèle que vous souhaitez utiliser dans Azure après le basculement.
4. Spécifiez le compte de stockage pour les données répliquées, ainsi que le réseau Azure dans lequel se trouveront les machines virtuelles Azure après le basculement.
5. Dans **Machines virtuelles** > **Sélectionner**, sélectionnez les machines virtuelles que vous souhaitez répliquer. Cliquez ensuite sur **OK**.

 Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Lorsque le travail de **finalisation de la protection** est terminé, la réplication initiale est également terminée et la machine virtuelle est prête à être basculée.

## <a name="next-steps"></a>Étapes suivantes
[Exécuter une simulation de récupération d'urgence](tutorial-dr-drill-azure.md)

