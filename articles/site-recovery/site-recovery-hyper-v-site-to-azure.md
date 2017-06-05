---
title: "Répliquer des machines virtuelles Hyper-V dans Azure | Microsoft Docs"
description: "Explique comment orchestrer la réplication, le basculement et la récupération des machines virtuelles Hyper-V locales dans Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0ef782a7bb7a98da2ec63c91732b3d5ddd959848
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Répliquez des machines virtuelles Hyper-V (sans VMM) vers Azure avec Azure Site Recovery sur le Portail Azure.

> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Portail Azure Classic](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Cet article explique comment répliquer des machines virtuelles Hyper-V locales dans Azure à l’aide d’[Azure Site Recovery](site-recovery-overview.md) dans le portail Azure. Dans ce déploiement, les machines virtuelles Hyper-V ne sont pas gérées par System Center Virtual Machine Manager (VMM).

Après avoir lu cet article, envoyez vos commentaires en bas ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Si vous souhaitez migrer des machines vers Azure (sans restauration automatique), pour en savoir plus, lisez [cet article](site-recovery-migrate-to-azure.md).



## <a name="deployment-steps"></a>Étapes du déploiement

Suivez l’article pour effectuer les étapes de déploiement ci-dessous :

1. [En savoir plus](site-recovery-components.md#hyper-v-to-azure) sur l’architecture de ce déploiement. En outre, [découvrez](site-recovery-hyper-v-azure-architecture.md) le fonctionnement de la réplication Hyper-V dans Site Recovery.
2. Vérifiez les conditions préalables et les limitations.
3. Configurez des comptes de réseau et de stockage Azure.
4. Préparez les hôtes Hyper-V.
5. Créez un coffre Recovery Services. Le coffre comporte les paramètres de configuration et orchestre la réplication.
6. Spécifiez les paramètres de la source. Créez un site Hyper-V qui contient les hôtes Hyper-V et inscrivez le site dans le coffre. Installez le fournisseur Azure Site Recovery et l’agent Microsoft Recovery Services sur les hôtes Hyper-V.
7. Configurez les paramètres de la cible et de réplication.
8. Activez la réplication des machines virtuelles.
9. Exécutez un test de basculement afin de vérifier que tout fonctionne bien.



## <a name="prerequisites"></a>Conditions préalables


**Prérequis** | **Détails** |
--- | --- |
**Microsoft Azure** | Découvrez la [configuration requise pour Azure](site-recovery-prereq.md#azure-requirements).
**Serveurs locaux** | [Découvrez](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager) la configuration requise pour les hôtes Hyper-V locaux.
**Machines virtuelles Hyper-V en local** | Les machines virtuelles que vous souhaitez répliquer doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) et être conformes aux [conditions préalables pour Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL Azure** | Les hôtes Hyper-V doivent accéder à ces URL :<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/></br> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/></br> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).



## <a name="prepare-for-deployment"></a>Préparation du déploiement

Pour préparer un déploiement, vous devez :

1. [configurer un réseau Azure](#set-up-an-azure-network) dans lequel les machines virtuelles Azure sont placées lorsqu’elles sont créées après un basculement.
2. [configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données répliquées.
3. [préparer les hôtes Hyper-V](#prepare-the-hyper-v-hosts) pour assurer leur accès aux URL requises.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Configurez un réseau Azure afin que les machines virtuelles Azure créées après le basculement puissent être connectées à un réseau.

* Ce réseau doit se trouver dans la même région que le coffre Recovery Services.
* Selon le modèle de ressource que vous souhaitez utiliser pour les machines virtuelles Azure ayant fait l’objet d’un basculement, vous devez configurer le réseau Azure en [mode Azure Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou en [mode Classic](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* Nous vous recommandons de configurer un réseau avant de commencer. Sinon, vous devrez le faire lors du déploiement de Site Recovery.
- Les comptes de stockage utilisés par Site Recovery ne peuvent pas être [déplacés](../azure-resource-manager/resource-group-move-resources.md), que ce soit dans le même abonnement ou dans des abonnements différents.


### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous avez besoin d’un compte de stockage Azure Standard ou Premium pour accueillir les données répliquées sur Azure. Un [Stockage Premium](../storage/storage-premium-storage.md) est utilisé pour des machines virtuelles nécessitant en permanence des performances d’E/S élevées et une faible latence pour héberger des charges de travail lourdes en nombre d’E/S.
- Si vous souhaitez utiliser un compte Premium pour stocker les données répliquées, vous avez aussi besoin d’un compte de stockage standard afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.
- Selon le modèle de ressource que vous souhaitez utiliser pour les machines virtuelles Azure ayant fait l’objet d’un basculement, vous allez configurer un compte en [mode Azure Resource Manager](../storage/storage-create-storage-account.md) ou en [mode Classic](../storage/storage-create-storage-account-classic-portal.md).
- Nous vous recommandons de configurer un compte de stockage avant de commencer. Sinon, vous devrez le faire lors du déploiement de Site Recovery. Les comptes doivent se trouver dans la même région que le coffre Recovery Services.
- Vous ne pouvez pas déplacer de comptes de stockage utilisés par Site Recovery entre des groupes de ressources au sein du même abonnement, ou entre différents abonnements.


### <a name="prepare-the-hyper-v-hosts"></a>préparer les hôtes Hyper-V

* Assurez-vous que les hôtes Hyper-V respectent la [configuration requise](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager).
- Assurez-vous que les hôtes peuvent accéder aux URL requises.


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **Surveillance + gestion** > **Sauvegarde et Site Recovery (OMS)**.  

    ![Nouveau coffre](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez-en un.

4. [Créez un groupe de ressources](../azure-resource-manager/resource-group-template-deploy-portal.md) ou sélectionnez un groupe existant et spécifiez une région Azure. Les machines seront répliquées dans cette région. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

5. Si vous souhaitez accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

    ![Nouveau coffre](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

Le nouveau coffre s’affiche dans la liste **Tableau de bord** > **Toutes les ressources**, et dans le panneau principal **Coffres Recovery Services**.



## <a name="select-the-protection-goal"></a>Sélectionner l’objectif de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Sélectionnez le coffre parmi les **Coffres Recovery Services**.
2. Dans **Prise en main**, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.

    ![Sélectionner des objectifs](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. Dans **Objectif de protection**, sélectionnez **To Azure (Vers Azure)**, puis **Oui, avec Hyper-V**. Sélectionnez **Non** pour confirmer que vous n’utilisez pas VMM. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Configurez le site Hyper-V, installez le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur les hôtes Hyper-V et inscrivez le site dans le coffre.

1. Dans **Préparer l’infrastructure**, cliquez sur **Source**. Pour ajouter un nouveau site Hyper-V en tant que conteneur pour vos hôtes ou clusters Hyper-V, cliquez sur **+Site Hyper-V**.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. Dans **Créer un site Hyper-V**, spécifiez un nom pour le site. Cliquez ensuite sur **OK**. Sélectionnez le site que vous avez créé, puis cliquez sur **+Serveur Hyper-V** pour ajouter un serveur au site.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Dans **Ajouter un serveur** > **Type de serveur**, vérifiez que **Serveur Hyper-V** est affiché.

    - Assurez-vous que le serveur Hyper-V à ajouter est conforme à la [configuration requise](#on-premises-prerequisites) et est en mesure d’accéder aux URL spécifiées.
    - Téléchargez le fichier d’installation du fournisseur Azure Site Recovery. Vous allez exécuter ce fichier pour installer le fournisseur et l’agent Recovery Services sur chaque hôte Hyper-V.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Installer le fournisseur et l’agent

1. Exécutez le fichier d’installation du fournisseur sur chaque hôte que vous avez ajouté au site Hyper-V. Si vous effectuez l’installation sur un cluster Hyper-V, exécutez le programme d’installation sur chaque nœud du cluster. L’installation et l’inscription de chaque nœud de cluster Hyper-V permettent de s’assurer que les machines virtuelles sont protégées, même si elles migrent entre les nœuds.
2. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
3. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation du fournisseur par défaut et cliquez sur **Installer**.
4. Dans **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé du coffre que vous avez téléchargé. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre et le site Hyper-V auquel appartient le serveur Hyper-V.

    ![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. Dans **Paramètres du proxy**, indiquez de quelle manière le fournisseur qui s’exécute sur les hôtes Hyper-V doit se connecter à Azure Site Recovery par le biais d’Internet.

    * Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**.
    * Si votre proxy nécessite une authentification ou si vous voulez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **Se connecter directement à Azure Site Recovery avec un serveur proxy**.
    * Si vous utilisez un proxy :
        - Spécifiez l’adresse, le port et les informations d’identification.
        - Vérifiez que les URL décrites dans la [configuration requise](#prerequisites) sont autorisées via le proxy.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. Une fois l’installation terminée, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

    ![Emplacement d’installation](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. Une fois l’inscription terminée, les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s’affiche dans **Infrastructure Site Recovery** > **Hôtes Hyper-V**.


## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Spécifiez le compte Azure Storage à utiliser pour la réplication, ainsi que le réseau Azure auquel les machines virtuelles Azure se connecteront après le basculement.

1. Cliquez sur **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles Azure après le basculement. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou gestion des ressources) pour les machines virtuelles.

3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

    - Si vous n’avez pas de compte de stockage, cliquez sur **+Stockage** pour créer un compte basé sur Resource Manager en ligne. Consultez les [conditions requises pour le stockage](site-recovery-prereq.md#azure-requirements).
    - Si vous n’avez pas de réseau Azure, cliquez sur **+Réseau** pour créer un compte basé sur Resource Manager en ligne.

    ![Storage](./media/site-recovery-vmware-to-azure/enable-rep3.png)




## <a name="configure-replication-settings"></a>Configurer les paramètres de réplication

1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.

    ![Réseau](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie.
3. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).

    > [!NOTE]
    > Une fréquence de 30 secondes n’est pas prise en charge lors de la réplication sur un Stockage Premium. La limitation est déterminée par le nombre de captures instantanées par objet blob (100) pris en charge par le Stockage Premium. [Plus d’informations](../storage/storage-premium-storage.md#snapshots-and-copy-blob)

4. Dans **Rétention des points de récupération**, spécifiez la durée (en heures) de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
5. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre 1 et 12 heures).

    - Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle.
    - Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés.
    - Si vous activez les instantanés cohérents avec l'application, cela affecte les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.

6. Dans **Heure de début de la réplication initiale**, indiquez à quel moment démarrer la réplication initiale. La réplication se produit via votre bande passante Internet. Il est donc préférable de prévoir son exécution en dehors des heures de bureau. Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Lorsque vous créez une stratégie, elle est automatiquement associée au site Hyper-V. Vous pouvez associer un site Hyper-V (et les machines virtuelles qu’il contient) à plusieurs stratégies de réplication dans **Réplication** > nom de la stratégie > **Associer le site Hyper-V**.

## <a name="capacity-planning"></a>Planification de la capacité

Votre infrastructure de base est désormais configurée. Vous pouvez donc réfléchir à la planification de la capacité et déterminer si des ressources supplémentaires sont nécessaires.

Site Recovery propose une fonctionnalité, Capacity Planner, qui vous permet d’allouer les bonnes ressources pour le calcul, la mise en réseau et le stockage. Vous pouvez exécuter Capacity Planner en mode rapide, afin d’obtenir une estimation basée sur le nombre moyen de machines virtuelles et de disques, ainsi que sur l’espace de stockage disponible ou en mode détaillé. Dans ce mode, vous saisissez des chiffres au niveau des charges de travail. Avant de commencer, vous devez :

* collecter les informations relatives à votre environnement de réplication, notamment les machines virtuelles, le nombre de disques par machine virtuelle et le stockage par disque ;
* déterminer le taux de modification (l’évolution) quotidienne de vos données répliquées. Pour cela, vous pouvez utiliser la fonction [Capacity Planner pour réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) .

1. Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lisez l’article](site-recovery-capacity-planner.md) relatif à cet outil.
2. Quand vous avez terminé, sélectionnez **Oui** sous **Have you run the Capacity Planner?** (Avez-vous exécuté Capacity Planner ?)

   ![Planification de la capacité](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

En savoir plus sur le [contrôle de la bande passante réseau](#network-bandwidth-considerations)



## <a name="enable-replication"></a>Activer la réplication

Avant de commencer, vérifiez que votre compte d’utilisateur Azure a les [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) requises pour activer la réplication d’une nouvelle machine virtuelle dans Azure.

Activez la réplication des machines virtuelles comme suit :          

1. Cliquez sur **Répliquer l’application** > **Source**. Après avoir configuré la réplication pour la première fois, vous pouvez cliquer sur l’option **+Répliquer** pour activer la réplication des machines supplémentaires.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. Dans **Source**, sélectionnez le site Hyper-V. Cliquez ensuite sur **OK**.
3. Dans **Cible**, sélectionnez l’abonnement de coffre et le modèle de basculement que vous souhaitez utiliser dans Azure (gestion des ressources ou classique) après le basculement.
4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous ne possédez pas de compte, vous pouvez en [créer un](#set-up-an-azure-storage-account). Cliquez ensuite sur **OK**.
5. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement.

    - Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer les paramètre réseau à l’ensemble des machines que vous activez pour la réplication.
    - Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine.
    - Si vous ne possédez pas de réseau, vous pouvez en [créer un](#set-up-an-azure-network). Sélectionnez un sous-réseau, le cas échéant. Cliquez ensuite sur **OK**.

   ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. Dans **Propriétés** > **Configurer les propriétés**, choisissez le système d’exploitation des machines virtuelles sélectionnées, ainsi que le disque du système d’exploitation.
8. Vérifiez que le nom de la machine virtuelle Azure (nom de la cible) est conforme à la [configuration requise pour les machines virtuelles Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Par défaut, tous les disques de la machine virtuelle sont sélectionnés pour la réplication, mais vous pouvez décocher des disques afin de les exclure.
    - Vous pouvez exclure des disques pour réduire la bande passante de réplication. Par exemple, vous pouvez ne pas répliquer les disques contenant des données temporaires ou des données actualisées à chaque redémarrage d’une machine ou d’une application (telles que pagefile.sys ou tempdb dans Microsoft SQL Server). Vous pouvez exclure un disque de la réplication en le désélectionnant.
    - Vous ne pouvez exclure que des disques de base. Vous ne pouvez pas exclure de disques de système d’exploitation.
    - Nous vous recommandons de ne pas exclure de disques dynamiques. Site Recovery ne peut pas déterminer si un disque dur virtuel à l’intérieur d’une machine virtuelle invitée est un disque de base ou dynamique. Si tous les disques de volume dynamique dépendants ne sont pas exclus, le disque dynamique protégé s’affichera comme un disque défectueux lors du basculement de la machine virtuelle, et les données de ce disque ne seront pas accessibles.
        - Une fois la réplication activée, vous ne pouvez pas ajouter ni supprimer de disques pour la réplication. Si vous voulez ajouter ou exclure un disque, vous devez désactiver la protection de la machine virtuelle, puis la réactiver.
        - Les disques que vous créez manuellement dans Azure ne sont pas restaurés automatiquement. Par exemple, si vous basculez trois disques et que vous en créez deux directement dans une machine virtuelle Azure, seuls les trois disques qui ont été basculés seront restaurés automatiquement à partir d’Azure sur Hyper-V. Vous ne pouvez pas inclure de disques créés manuellement dans le processus de restauration automatique ou de réplication inverse d’Hyper-V vers Azure.
        - Si vous excluez un disque requis pour le bon fonctionnement d’une application, après le basculement vers Azure, vous devez le créer manuellement dans Azure afin que l’application répliquée puisse s’exécuter. Vous pouvez également intégrer Azure Automation dans un plan de récupération afin de créer le disque pendant le basculement de la machine.

10. Cliquez sur **OK** pour enregistrer les modifications. Vous pouvez opter pour une définition ultérieure des propriétés.

     ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, sélectionnez la stratégie de réplication que vous souhaitez appliquer aux machines virtuelles protégées. Cliquez ensuite sur **OK**. Vous pouvez modifier la stratégie de réplication dans **Stratégies de réplication** > Nom de la stratégie > **Modifier les paramètres**. Les modifications que vous appliquez seront utilisées pour les nouvelles machines et les machines dont la réplication est déjà en cours.


   ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Vous pouvez suivre la progression du travail **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.

### <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés des machines virtuelles

Nous vous recommandons de vérifier les propriétés de la machine source.

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** et sélectionnez la machine.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. Dans **Propriétés**, vous pouvez afficher les informations sur la réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. Dans **Calcul et réseau** > **Propriétés de calcul**, vous pouvez spécifier la taille de la cible et le nom de la machine virtuelle Azure. Si besoin, modifiez ce nom afin de respecter les exigences d’Azure. Vous pouvez également afficher et modifier les informations sur le réseau cible, le sous-réseau et l’adresse IP qui seront affectés à la machine virtuelle Azure. Notez les points suivants :

   * Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas d’adresse IP, la machine ayant basculé utilisera le service DHCP. Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue. Vous pouvez utiliser la même adresse IP cible pour le test de basculement si cette adresse est disponible sur le réseau de test de basculement.
   * Le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible, comme suit :

     * Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
     * Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
     * Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.     
     * Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau.
     * Si la machine virtuelle possède plusieurs cartes réseau, la première qui s’affiche dans la liste devient la carte réseau *par défaut* dans la machine virtuelle Azure.

     ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. Les disques de données et du système d’exploitation de la machine virtuelle qui seront répliqués s’affichent dans **Disques** .

#### <a name="managed-disks"></a>Disques gérés

Dans **Calcul et réseau** > **Propriétés de calcul**, vous pouvez définir « Utiliser des disques managés » sur « Oui » pour la machine virtuelle si vous souhaitez attacher des disques managés à votre ordinateur lors de la migration vers Azure. Les disques managés simplifient la gestion des disques des machines virtuelles Azure IaaS, en gérant les comptes de stockage associés aux disques de machines virtuelles. [En savoir plus sur les disques managés](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - Les disques managés sont créés et attachés à la machine virtuelle uniquement lors d’un basculement vers Azure. Lors de l’activation de la protection, les données des machines locales continuent à se répliquer sur des comptes de stockage.
   Des disques managés ne peuvent être créés que pour des machines virtuelles déployées à l’aide du modèle de déploiement Resource Manager.

  > [!NOTE]
  > La restauration automatique d’Azure vers l’environnement local Hyper-V n’est actuellement pas prise en charge pour les ordinateurs avec disques managés. Ne définissez « Utiliser des disques managés » sur « Oui » que si vous avez l’intention de migrer cette machine vers Azure.

   - Lorsque vous définissez « Utiliser des disques managés » sur « Oui », seuls les groupes à haute disponibilité du groupe de ressources dont la propriété « Utiliser des disques managés » est définie sur « Oui » sont sélectionnables. En effet, les machines virtuelles avec disques managés ne peuvent faire partie que de groupes à haute disponibilité dont la propriété « Utiliser des disques managés » est définie sur « Oui ». Vérifiez que vous créez des groupes à haute disponibilité avec la propriété « Utiliser des disques managés » activée si vous comptez utiliser des disques managés lors du basculement. De même, lorsque vous définissez « Utiliser des disques managés » sur « Non », seuls les groupes à haute disponibilité du groupe de ressources dont la propriété « Utiliser des disques managés » est définie sur « Non » sont sélectionnables. [En savoir plus sur les disques managés et les groupes à haute disponibilité](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > Si le compte de stockage utilisé pour la réplication a été chiffré à un moment donné avec Storage Service Encryption, la création de disques managés pendant le basculement échoue. Vous pouvez définir « Utiliser des disques managés » sur « Non » et réessayer la restauration ou désactiver la protection de la machine virtuelle et la protéger sur un compte de stockage dont le chiffrement de service de stockage n’a jamais été activé.
  > [En savoir plus sur Storage Service Encryption et les disques managés](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>test du déploiement

Pour tester le déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou un plan de récupération qui contient une ou plusieurs machines virtuelles.

### <a name="before-you-start"></a>Avant de commencer

 - Si vous souhaitez vous connecter à des machines virtuelles Azure à l’aide du protocole RDP après le basculement, découvrez comment [préparer la connexion](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Pour effectuer un test complet, vous avez besoin d’une copie d’Active Directory et du DNS dans votre environnement de test. [Plus d’informations](site-recovery-active-directory.md#test-failover-considerations)

### <a name="run-a-test-failover"></a>Exécution d’un test de basculement

1. Pour effectuer le basculement d’une seule machine, cliquez sur la machine virtuelle dans **Éléments répliqués**, puis sur l’icône **+Test de basculement**.
2. Pour effectuer le basculement d’un plan de récupération, cliquez avec le bouton droit sur le plan dans **Plans de récupération** et sélectionnez **Test de basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md).
3. Dans **Test de basculement**, sélectionnez le réseau Azure auquel les machines virtuelles Azure seront connectées après le basculement.
4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression du basculement en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou en sélectionnant le travail **Test de basculement** dans le nom du coffre > **Travaux** > **Travaux Site Recovery**.
5. Une fois le basculement terminé, vous devez également voir la machine Azure de réplication apparaître dans le Portail Azure > **Machines virtuelles**. Vous devrez peut-être vous assurer que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.
6. Si vous avez préparé les connexions après le basculement, vous devez être à même de vous connecter à la machine virtuelle Azure.
7. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement de nettoyage** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations associées au test de basculement. Cette opération supprimera les machines virtuelles qui ont été créés au cours du test de basculement.

Pour plus d’informations, consultez l’article [Test de basculement vers Azure dans Site Recovery](site-recovery-test-failover-to-azure.md).



## <a name="monitor-the-deployment"></a>Surveiller le déploiement

Surveillez l’intégrité, l’état et les paramètres de configuration de votre déploiement Site Recovery :

1. Cliquez sur le nom du coffre pour accéder au tableau de bord **Essentials** . Dans ce tableau de bord, vous pouvez suivre les événements, les plans de récupération, l’état de la récupération et les travaux de Site Recovery.  

    ![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. Dans la mosaïque **Intégrité** , vous pouvez surveiller le fonctionnement des serveurs du site qui rencontrent un problème, ainsi que les événements signalés par Site Recovery au cours des dernières 24 heures. Vous pouvez personnaliser Essentials de manière afficher les vignettes et les dispositions qui sont les plus utiles, y compris l’état des autres coffres Site Recovery et Backup.
3. Vous pouvez gérer et surveiller la réplication dans les vignettes **Éléments répliqués**, **Plans de récupération** et **Travaux Site Recovery**. Vous pouvez accéder au détail des travaux dans **Travaux** > **Travaux Site Recovery**.

## <a name="command-line-provider-and-agent-installation"></a>Installation de l’agent et du fournisseur en ligne de commande

L’agent et le fournisseur Azure Site Recovery peuvent également être installés à l’aide de la ligne de commande suivante. Cette méthode peut être utilisée pour installer le fournisseur sur un module Server Core pour Windows Server 2012 R2.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription dans un dossier, par exemple C:\ASR.
2. À partir d’une invite de commandes avec élévation de privilèges, exécutez les commandes suivantes pour extraire le programme d’installation du fournisseur :

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Exécutez cette commande pour installer les composants :

            C:\ASR> setupdr.exe /i
4. Ensuite, exécutez les commandes suivantes pour inscrire le serveur dans le coffre :

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
Où :

* **/Credentials**: paramètre obligatoire, qui spécifie l’emplacement où se trouve le fichier de clé d’inscription.  
* **/FriendlyName**: paramètre obligatoire qui correspond au nom du serveur hôte Hyper-V qui s’affiche dans le portail Azure Site Recovery.
* **/proxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy.
* **/proxyport** : paramètre facultatif qui spécifie le port du serveur proxy
* **/proxyUsername**: paramètre facultatif qui spécifie le nom d’utilisateur proxy (si le proxy nécessite une authentification).
* **/proxyPassword**: paramètre facultatif qui spécifie le mot de passe pour l’authentification auprès du serveur proxy (si le proxy nécessite une authentification).


## <a name="network-bandwidth-considerations"></a>Remarques relatives à la bande passante réseau
Vous pouvez utiliser [l’outil Capacity Planner Hyper-V](site-recovery-capacity-planner.md) pour calculer la bande passante requise pour la réplication (réplication initiale, puis delta). Vous disposez de plusieurs options pour déterminer la quantité de bande passante utilisée pour la réplication :

* **Limite de bande passante**: le trafic Hyper-V qui est répliqué vers Azure passe par un hôte Hyper-V spécifique. Vous pouvez limiter la bande passante sur le serveur hôte.
* **Ajustement de la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre.

### <a name="throttle-bandwidth"></a>Limiter la bande passante
1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup sur le serveur hôte Hyper-V. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le Bureau. Vous pouvez également le trouver ici : C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Dans l’onglet **Limitation**, sélectionnez **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde** et définissez les limites relatives aux heures de travail et aux heures non travaillées. Les plages valides vont de 512 Kbits/s à 102 Mbits/s par seconde.

    ![Limiter la bande passante](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indique qu’aucune limitation n’est requise.

### <a name="influence-network-bandwidth"></a>Influer sur la bande passante réseau
1. Dans le registre, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur du paramètre **UploadThreadsPerVM**, ou créez la clé si elle n’existe pas.
   * Pour influer sur la bande passante utilisée pour le trafic lié à la restauration automatique à partir d’Azure, modifiez la valeur du paramètre **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « surutilisé », ces clés de Registre doivent être modifiées par rapport aux valeurs par défaut. La valeur maximale est de 32. Surveillez le trafic pour optimiser la valeur.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé la réplication initiale et testé le déploiement, vous pouvez appeler des basculements en cas de besoin. [Apprenez-en davantage](site-recovery-failover.md) sur les différents types de basculement et leur mode exécution.

