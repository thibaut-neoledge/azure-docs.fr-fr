---
title: "Configuration de la source et de la cible pour la réplication de Hyper-V vers Azure (avec System Center VMM) avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer les paramètres de source et de cible pour la réplication de machines virtuelles Hyper-V dans les clouds VMM vers le stockage Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>Étape 8 : Configurer la source et la cible pour la réplication de Hyper-V (avec VMM) vers Azure

Après avoir [créé un coffre](vmm-to-azure-walkthrough-create-vault.md) et avoir indiqué ce que vous voulez répliquer, utilisez cet article pour configurer les paramètres sources et cibles lors de la réplication de machines virtuelles Hyper-V locales gérées dans les clouds System Center Virtual Machine Manager (VMM) vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) sur le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

S1. Cliquez sur **Préparer l’infrastructure** > **Source**.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. Dans **Préparer la source**, cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans le champ **Type de serveur** et que le serveur VMM répond à la [configuration requise et aux exigences concernant les URL](#prerequisites).
4. Téléchargez le fichier d’installation du fournisseur Azure Site Recovery.
5. Téléchargez la clé d’inscription. Vous en aurez besoin lorsque vous exécuterez le programme d’installation. Une fois générée, la clé reste valide pendant 5 jours.

    ![Configurer la source](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>Installer le fournisseur sur le serveur VMM

1. Exécutez le fichier Provider setup sur le serveur VMM.
2. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
3. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation du fournisseur par défaut et cliquez sur **Installer**.

    ![Emplacement d’installation](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. Une fois l’installation terminée, cliquez sur **Inscrire** pour inscrire le serveur VMM dans le coffre.
5. Dans la page **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé du coffre. Spécifiez l’abonnement Azure Site Recovery et le nom du coffre.

    ![Enregistrement du serveur](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. Dans **Connexion Internet**, indiquez le mode de connexion à Site Recovery du fournisseur exécuté sur le serveur VMM, via Internet.

   * Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**.
   * Si votre proxy nécessite une authentification ou si vous voulez utiliser un proxy personnalisé, sélectionnez **Se connecter directement à Azure Site Recovery avec un serveur proxy**.
   * Si vous utilisez un proxy personnalisé, spécifiez l’adresse, le port et les informations d’identification.
   * Si vous utilisez un proxy, vous devez avoir déjà autorisé les URL indiquées dans la section relative à la [configuration requise](#on-premises-prerequisites).
   * Si vous utilisez un proxy personnalisé, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs VMM dans la console VMM. Dans **Paramètres**, développez **Sécurité** > **Comptes d’identification**, puis modifiez le mot de passe de DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.

     ![Internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. Vous pouvez accepter ou modifier l’emplacement d’un certificat SSL généré automatiquement pour le chiffrement de données. Ce certificat est utilisé si vous activez le chiffrement de données pour un cloud protégé par Azure dans le portail Azure Site Recovery. Conservez ce certificat en sécurité. Lorsque vous exécuterez un basculement vers Azure, vous en aurez besoin pour le déchiffrement (si le chiffrement des données est activé).
8. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
9. Si vous souhaitez synchroniser les métadonnées de l’ensemble des clouds sur le serveur VMM avec le coffre, activez l’option **Synchroniser les métadonnées du cloud**. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM. Cliquez sur **Register** pour terminer le processus.

    ![Enregistrement du serveur](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. L’inscription débute. Une fois l’inscription terminée, le serveur s’affiche dans **Infrastructure Site Recovery** > **Serveurs VMM**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installer l’agent Azure Recovery Services sur des hôtes Hyper-V

1. Une fois le fournisseur configuré, vous devez télécharger le fichier d’installation de l’agent Azure Recovery Services. Exécutez le programme d’installation sur chaque serveur Hyper-V dans le cloud VMM.

    ![Sites Hyper-V](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. Sous **Vérification de la configuration requise**, cliquez sur **Suivant**. Tous les éléments manquants de la configuration requise sont automatiquement installés.

    ![Prerequisites Recovery Services Agent](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. Sous **Paramètres d’installation**, acceptez ou modifiez l’emplacement d’installation et l’emplacement du cache. Vous pouvez configurer le cache sur un lecteur qui dispose d’au moins 5 Go de stockage disponible. Toutefois, nous vous recommandons d’utiliser un lecteur de cache présentant au moins 600 Go d’espace disponible. Cliquez ensuite sur **Installer**.
4. Une fois l’installation terminée, cliquez sur **Fermer** pour finaliser le processus.

    ![Inscrire l’Agent MARS](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>Installer à partir de la ligne de commande
Vous pouvez installer cet agent à partir de la ligne de commande, en saisissant la commande suivante :

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configuration de l’accès proxy à Site Recovery via Internet à partir d’hôtes Hyper-V

L’agent Recovery Services exécuté sur les hôtes Hyper-V requiert un accès à Azure, via Internet, pour la réplication des machines virtuelles. Si vous accédez à Internet via un proxy, configurez-le comme suit :

1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup sur l’hôte Hyper-V. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le Bureau. Vous pouvez également le trouver ici : C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Dans l’onglet **Configuration du proxy** , spécifiez les informations du serveur proxy.

    ![Inscrire l’Agent MARS](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. Vérifiez que l’agent peut atteindre les URL décrites dans la section [Configuration requise](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible
Spécifiez le compte Azure Storage à utiliser pour la réplication, ainsi que le réseau Azure auquel les machines virtuelles Azure se connecteront après le basculement.

1. Cliquez sur **Préparer l’infrastructure** > **Cible**, puis sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou gestion des ressources) pour les machines virtuelles basculées.

    ![Storage](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

    ![Storage](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. Si vous n’avez pas créé de compte de stockage et que vous souhaitez le faire à l’aide de Resource Manager, cliquez sur **+Compte de stockage** pour effectuer l’opération en ligne.  Dans le panneau **Créer un compte de stockage** , saisissez le nom, le type, l’abonnement associé et l’emplacement du compte de stockage. Ce compte doit se trouver au même emplacement que le coffre Recovery Services.

   ![Storage](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * Si vous souhaitez créer un compte de stockage en mode Classic, vous pouvez le faire dans le Portail Azure. [En savoir plus](../storage/common/storage-create-storage-account.md)
   * Si vous utilisez un compte de stockage Premium pour les données répliquées, configurez un compte de stockage standard supplémentaire, afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.
5. Si vous n’avez pas créé de réseau Azure et que vous souhaitez le faire avec Azure Resource Manager, cliquez sur **+Réseau** afin d’effectuer l’opération en ligne. Dans le panneau **Créer un réseau virtuel** , spécifiez le nom, la plage d’adresses, les informations sur le sous-réseau associé, l’abonnement et l’emplacement du réseau virtuel. Ce réseau doit se trouver au même emplacement que le coffre Recovery Services.

   ![Réseau](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   Si vous souhaitez créer un réseau en suivant le modèle classique, utilisez le Portail Azure. [Plus d’informations](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)





## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 9 : Configurer le mappage réseau](vmm-to-azure-walkthrough-network-mapping.md)
