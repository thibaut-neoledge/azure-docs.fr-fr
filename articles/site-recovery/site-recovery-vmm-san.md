---
title: "Répliquer des machines virtuelles Hyper-V dans VMM avec SAN à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment répliquer des machines virtuelles Hyper-V entre deux sites avec Azure Site Recovery à l’aide de la réplication SAN."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 67b4861ac564565b2a36932ae15141a1e1f56035
ms.openlocfilehash: 29084a52aca22b31a2fa42660ec2cdf050aec7ca
ms.lasthandoff: 02/23/2017


---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-by-using-san"></a>Répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site secondaire avec Azure Site Recovery à l’aide d’un SAN


Utilisez cet article si vous souhaitez déployer [Azure Site Recovery](site-recovery-overview.md) pour gérer la réplication des machines virtuelles Hyper-V (gérées dans des clouds System Center Virtual Machine Manager) vers un site VMM secondaire à l’aide d’Azure Site Recovery dans le portail classique. Ce scénario n’est pas disponible dans le nouveau portail Azure.



Postez des commentaires à la fin de cet article. Obtenez des réponses à vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="why-replicate-with-san-and-site-recovery"></a>Pourquoi effectuer une réplication avec SAN et Site Recovery ?

* SAN offre une solution de réplication professionnelle et évolutive, afin qu’un site principal contenant Hyper-V avec SAN puisse répliquer les numéros d’unité logique vers un site secondaire avec un réseau SAN. Le stockage géré par VMM, et la réplication et le basculement sont orchestrés avec Site Recovery.
* Site Recovery a collaboré avec plusieurs [partenaires de stockage SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) pour proposer la réplication dans un stockage Fibre Channel et iSCSI.  
* Tirez parti de votre infrastructure SAN existante pour protéger les applications critiques déployées dans des clusters Hyper-V. Les machines virtuelles peuvent être répliquées en tant que groupe afin que les applications multiniveaux puissent être basculées régulièrement.
* La réplication SAN garantit la cohérence de la réplication entre les différentes couches d’une application avec réplication synchronisée pour un RTO et une RPO faibles, et une réplication asynchronisée pour une haute flexibilité, selon les capacités de votre groupe de stockage.  
* Vous pouvez gérer le stockage SAN dans la structure VMM et utiliser SMI-S dans VMM pour détecter le stockage existant.  

Notez les points suivants :
- La réplication de site à site avec SAN n’est pas disponible dans le portail Azure. Elle est uniquement disponible dans le portail classique. Il est impossible de créer des coffres dans le portail classique. Les coffres existants peuvent être conservés.
- La réplication SAN vers Azure n’est pas prise en charge.
- Vous ne pouvez pas répliquer les fichiers VHDX partagés ou les numéros d’unité logique qui sont directement connectés aux machines virtuelles via iSCSI ou Fibre Channel. Les clusters invités peuvent être répliqués.


## <a name="architecture"></a>Architecture

![Architecture SAN](./media/site-recovery-vmm-san/architecture.png)

- **Azure** : configurez un coffre Site Recovery dans le portail Azure.
- **Stockage SAN** : le stockage SAN est géré dans la structure VMM. Vous ajoutez le fournisseur de stockage, créez des classifications de stockage et configurez des pools de stockage.
- **VMM et Hyper-V** : nous recommandons un serveur VMM sur chaque site. Définissez des clouds privés VMM et placez des clusters Hyper-V dans ces clouds. Lors du déploiement, le fournisseur Azure Site Recovery est installé sur chaque serveur VMM et le serveur VMM est inscrit dans le coffre. Le fournisseur communique avec le service Site Recovery pour gérer la réplication, le basculement et la restauration automatique.
- **Réplication** : après avoir configuré le stockage dans VMM et la réplication dans Site Recovery, la réplication se produit entre le stockage SAN primaire et secondaire. Aucune donnée de réplication n’est envoyée à Site Recovery.
- **Basculement** : activez le basculement dans le portail Site Recovery. Il n’y a aucune perte de données pendant le basculement, car la réplication est synchrone.
- **Restauration automatique** : pour restaurer automatiquement, activez la réplication inverse pour transférer les modifications delta à partir du site secondaire vers le site principal. Une fois la réplication inverse terminée, exécutez un basculement planifié du site secondaire vers le site principal. Ce basculement planifié arrête les machines virtuelles de réplication sur le site secondaire, et les démarre sur le site principal.


## <a name="before-you-start"></a>Avant de commencer


**Configuration requise** | **Détails**
--- | ---
**Microsoft Azure** |Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. Créez un coffre Azure Site Recovery pour configurer et gérer la réplication et le basculement.
**VMM** | Vous pouvez utiliser un seul serveur VMM et répliquer entre les différents clouds, mais nous recommandons l’utilisation d’un VMM sur le site principal et sur le site secondaire. Un VMM peut être déployé comme serveur autonome physique ou virtuel, ou en tant que cluster. <br/><br/>Le serveur VMM doit exécuter System Center 2012 R2 ou version ultérieure avec les dernières mises à jour cumulatives.<br/><br/> Vous avez besoin d’au moins un cloud configuré sur le serveur VMM principal que vous souhaitez protéger et un cloud configuré sur le serveur VMM secondaire que vous souhaitez utiliser pour la récupération.<br/><br/> Le cloud source doit contenir un ou plusieurs groupes hôtes VMM.<br/><br/> Tous les clouds VMM doivent avoir le profil Capacité Hyper-V défini.<br/><br/> Pour plus d’informations sur la configuration des clouds VMM, consultez [Déployer un cloud de machine virtuelle privé](https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | Vous devez avoir un ou plusieurs clusters Hyper-V dans les clouds VMM principaux et secondaires.<br/><br/> Le cluster Hyper-V source doit contenir au moins une machine virtuelle.<br/><br/> Les groupes hôtes VMM dans les sites principaux et secondaires doivent contenir au moins l’un des clusters Hyper-V.<br/><br/>Les serveurs Hyper-V hôte et cible doivent exécuter Windows Server 2012 ou version ultérieure avec le rôle Hyper-V et les dernières mises à jour doivent être installées.<br/><br/> Si vous utilisez Hyper-V dans un cluster et avez un cluster basé sur des adresses IP statiques, le service Broker du cluster n’est pas créé automatiquement. Vous devez le configurer manuellement. Pour plus d’informations, consultez [Préparation des clusters hôtes pour un réplica Hyper-V](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters).
**Stockage SAN** | Vous pouvez répliquer des machines virtuelles en cluster invité avec un stockage iSCSI ou Fibre Channel, ou bien à l’aide de disques durs virtuels partagés (VHDX).<br/><br/> Vous devez disposer de deux groupes SAN, l’un dans le site principal et l’autre dans le site secondaire.<br/><br/> Une infrastructure réseau doit être configurée entre les groupes. L'homologation et la réplication doivent être configurées. Des licences de réplication doivent être configurées conformément aux exigences des baies de stockage.<br/><br/>Configurez la mise en réseau entre les serveurs hôtes Hyper-V et le groupe de stockage pour que les hôtes puissent communiquer avec les numéros d’unité logique de stockage via iSCSI ou Fibre Channel.<br/><br/> Consultez la liste des [groupes de stockage pris en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> Les fournisseurs SMI-S des fabricants de groupes de stockage doivent être installés, et les groupes SAN doivent être gérés par le fournisseur. Configurez le fournisseur selon les instructions du fabricant.<br/><br/>Vérifiez que le fournisseur SMI-S pour le groupe se trouve sur un serveur auquel le serveur VMM peut accéder via le réseau au moyen d’une adresse IP ou du nom de domaine complet.<br/><br/> Chaque groupe SAN doit posséder un ou plusieurs pools de stockage disponibles.<br/><br/> Le serveur VMM principal doit gérer le groupe principal, tandis que le serveur VMM secondaire doit gérer le groupe secondaire.
**Mappage réseau** | Configurez le mappage réseau pour que les machines virtuelles répliquées soient placées de manière optimale sur les serveurs hôtes Hyper-V secondaires après le basculement et qu’elles puissent se connecter aux réseaux de machines virtuelles appropriés. Si vous ne configurez pas de mappage réseau, les machines virtuelles de réplicas ne sont connectées à aucun réseau après le basculement.<br/><br/> Assurez-vous que les réseaux VMM sont configurés correctement, afin de pouvoir configurer le mappage réseau lors du déploiement de Site Recovery. Dans VMM, les machines virtuelles de l’hôte Hyper-V source doivent être connectées à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.<br/><br/> Le cloud cible doit avoir un réseau de machines virtuelles correspondant configuré, qui lui-même doit être lié à un réseau logique correspondant associé au cloud cible.<br/><br/>.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>Étape 1 : Préparer l'infrastructure VMM
Pour préparer votre infrastructure VMM, vous devez :

1. vérifier les clouds VMM ;
2. intégrer et classer le stockage SAN dans VMM ;
3. créer des LUN et allouer de l’espace de stockage ;
4. créer des groupes de réplication ;
5. configurer les réseaux de machines virtuelles.

### <a name="verify-vmm-clouds"></a>Vérifier les clouds VMM

Vérifiez que vos clouds VMM sont configurés correctement avant de commencer le déploiement de Site Recovery.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>Intégrer et classer le stockage SAN dans la structure VMM

1. Dans la console VMM, accédez à **Structure** > **Stockage** > **Ajouter des ressources** > **Périphériques de stockage**.
2. Dans l’assistant **Ajout de périphériques de stockage**, choisissez **Sélectionner un type de fournisseur de stockage**, puis **Périphériques SAN et NAS découverts et gérés par un fournisseur SMI-S**.

    ![Type de fournisseur](./media/site-recovery-vmm-san/provider-type.png)

3. Dans la page **Spécifier le protocole et l’adresse du fournisseur SMI-S de stockage**, sélectionnez **SMI-S CIMXML** et spécifiez les paramètres de connexion au fournisseur.
4. Dans **Adresse IP ou nom de domaine complet du fournisseur** et **Port TCP/IP**, spécifiez les paramètres de connexion au fournisseur. Vous pouvez uniquement utiliser une connexion SSL pour CIMXML SMI-S.

    ![Connexion au fournisseur](./media/site-recovery-vmm-san/connect-settings.png)
5. Dans **Compte d’identification**, spécifiez un compte d’identification VMM ayant accès au fournisseur ou créez un compte.
6. Dans la page **Rechercher des informations** , VMM tente de détecter et d'importer automatiquement les informations des périphériques de stockage. Pour relancer la recherche, cliquez sur **Analyser le fournisseur**. Si le processus de découverte réussit, les baies de stockage découvertes, les pools de stockage, le fabricant, le modèle et la capacité sont indiqués dans la page.

    ![Détecter le stockage](./media/site-recovery-vmm-san/discover.png)
7. Dans **Sélectionner les pools de stockage à mettre sous gestion et affecter une classification**, sélectionnez les pools de stockage gérés par VMM et affectez-leur une classification. Les informations sur les LUN sont importées des pools de stockage. Créez des LUN en fonction des applications que vous devez protéger, de leurs besoins en matière de capacité et des éléments que vous devez répliquer ensemble.

    ![Classifier le stockage](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Créer des LUN et allouer de l'espace de stockage

Créez des LUN en fonction des applications que vous devez protéger, de leurs besoins en matière de capacité et des éléments que vous devez répliquer ensemble.

1. Une fois que le stockage apparaît dans la structure VMM, vous pouvez [approvisionner des numéros d’unité logique](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm).

     > [!NOTE]
     > N’ajoutez pas à la machine virtuelle de disques durs virtuels activés pour la réplication vers les numéros d’unité logique. Si ces LUN ne font pas partie d’un groupe de réplication Site Recovery, elles ne seront pas détectées par Site Recovery.
     >

2. Allouez de l’espace de stockage au cluster hôte Hyper-V pour que VMM puisse déployer les données des machines virtuelles vers le stockage approvisionné :

   * Avant d’allouer de l’espace de stockage au cluster, vous devez en allouer au groupe hôte VMM sur lequel réside le cluster. Pour plus d’informations, consultez [Comment allouer des unités logiques de stockage à un groupe hôte dans VMM](https://technet.microsoft.com/library/gg610686.aspx) et [Comment allouer des pools de stockage à un groupe hôte dans VMM](https://technet.microsoft.com/library/gg610635.aspx).
   * Allouez une capacité de stockage au cluster comme décrit dans [Configuration du stockage sur un cluster hôte Hyper-V dans VMM](https://technet.microsoft.com/library/gg610692.aspx).

    ![Type de fournisseur](./media/site-recovery-vmm-san/provider-type.png)
3. Dans **Spécifier le protocole et l’adresse du fournisseur SMI-S de stockage**, sélectionnez **SMI-S CIMXML**. Spécifiez les paramètres de connexion au fournisseur. Vous pouvez uniquement utiliser une connexion SSL pour CIMXML SMI-S.

    ![Connexion au fournisseur](./media/site-recovery-vmm-san/connect-settings.png)
4. Dans **Compte d’identification**, spécifiez un compte d’identification VMM ayant accès au fournisseur ou créez un compte.
5. Dans **Rechercher des informations**, VMM tente de détecter et d’importer automatiquement les informations des périphériques de stockage. Pour relancer la recherche, cliquez sur **Analyser le fournisseur**. Lorsque le processus de découverte réussit, les groupes de stockage, les pools de stockage, le fabricant, le modèle et la capacité sont indiqués dans la page.

    ![Détecter le stockage](./media/site-recovery-vmm-san/discover.png)
7. Dans **Sélectionner les pools de stockage à mettre sous gestion et affecter une classification**, sélectionnez les pools de stockage gérés par VMM et affectez-leur une classification. Les informations sur les LUN sont importées des pools de stockage.

    ![Classifier le stockage](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>Créer des groupes de réplication

Créez un groupe de réplication qui recense toutes les LUN devant être répliquées ensemble.

1. Dans la console VMM, ouvrez l’onglet **Groupes de réplication** des propriétés du groupe de stockage, puis cliquez sur **Nouveau**.
2. Créez le groupe de réplication.

    ![Groupe de réplication SAN](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Configurer des réseaux

Si vous souhaitez configurer le mappage réseau, procédez comme suit :

1. Consultez Mappage réseau de Site Recovery.
2. Préparez les réseaux de machines virtuelles dans VMM :

   * [Configurez les réseaux logiques](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [Configurez les réseaux de machines virtuelles](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>Étape 2 : Créer un coffre

1. Connectez-vous au [portail Azure](https://portal.azure.com) à partir du serveur VMM à inscrire dans le coffre.
2. Développez **Data Services** > **Recovery Services**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer nouveau** > **Création rapide**.
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
5. Dans **Region**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez [Informations détaillées sur la tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Cliquez sur **Create vault**.

    ![Nouveau coffre](./media/site-recovery-vmm-san/create-vault.png)

Vérifiez la barre d’état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale **Recovery Services**.

### <a name="register-the-vmm-servers"></a>Enregistrer les serveurs VMM

1. Ouvrez la page **Démarrage rapide** depuis la page **Recovery Services**. Vous pouvez aussi ouvrir cette page à tout moment en sélectionnant l’icône.

    ![Icône Quick Start](./media/site-recovery-vmm-san/quick-start-icon.png)
2. Dans la zone de liste déroulante, sélectionnez **Entre le site local Hyper-V à l’aide de la réplication de groupe**.

    ![Registration Key](./media/site-recovery-vmm-san/select-san.png)
3. Dans **Préparer les serveurs VMM**, téléchargez la dernière version du fichier d’installation du fournisseur Azure Site Recovery.
4. Exécutez ce fichier sur le serveur VMM source. Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois, installez-le sur un nœud actif et terminez l’installation pour inscrire le serveur VMM dans le coffre. Ensuite, installez le fournisseur sur les autres nœuds. Si vous mettez à niveau le fournisseur, vous devez le mettre à niveau sur tous les nœuds, car ils doivent tous avoir la même version du fournisseur.
5. Le programme d’installation vérifie la configuration requise et demande l’autorisation d’arrêter le service VMM pour commencer l’installation du fournisseur. Le service redémarre automatiquement une fois l’installation terminée. Sur un cluster VMM, vous serez invité à arrêter le rôle de cluster.
6. Dans **Microsoft Update** , vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.

    ![Mises à jour Microsoft](./media/site-recovery-vmm-san/ms-update.png)

7. Par défaut, l’emplacement de l’installation du fournisseur est <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin. Cliquez sur **Installer** pour commencer.

    ![Emplacement d’installation](./media/site-recovery-vmm-san/install-location.png)

8. Une fois le fournisseur installé, cliquez sur **Inscrire** pour inscrire le serveur VMM dans le coffre.

    ![Installation terminée](./media/site-recovery-vmm-san/install-complete.png)

9. Sur la page **Connexion Internet**, indiquez la façon dont le fournisseur se connecte à Internet. Sélectionnez **Utiliser les paramètres proxy par défaut du système** pour utiliser les paramètres de connexion Internet par défaut sur le serveur.

    ![Paramètres Internet](./media/site-recovery-vmm-san/proxy-details.png)

   * Si vous souhaitez utiliser un proxy personnalisé, configurez-le avant d’installer le fournisseur. Quand vous configurez les paramètres de proxy personnalisé, un test s’exécute pour vérifier la connexion proxy.
   * Si vous n’utilisez pas de proxy personnalisé ou si votre proxy par défaut nécessite une authentification, vous devez saisir les détails du proxy, y compris son adresse et son port.
   * Les URL requises doivent être accessibles à partir du serveur VMM.
   * Si vous utilisez un proxy personnalisé, un compte d’identification VMM (DRAProxyAccount) est créé automatiquement avec les informations d'identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s’authentifier. Vous pouvez modifier les paramètres de compte d’identification dans la console VMM (**Paramètres** > **Sécurité** > **Comptes d’identification** > **DRAProxyAccount**). Vous devez redémarrer le service VMM pour que la modification prenne effet.
10. Dans **Clé d’inscription**, sélectionnez la clé que vous avez téléchargée depuis le portail et copiez-la sur le serveur VMM.
11. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré.

    ![Enregistrement du serveur](./media/site-recovery-vmm-san/vault-creds.png)
12. Le paramètre de chiffrement est utilisé uniquement pour la réplication de VMM vers Azure. Vous pouvez l’ignorer.

    ![Enregistrement du serveur](./media/site-recovery-vmm-san/encrypt.png)
13. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
14. Dans la synchronisation **Initial cloud metadata**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.

    ![Enregistrement du serveur](./media/site-recovery-vmm-san/friendly-name.png)

15. Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur s’affiche dans **Serveurs** > **Serveurs VMM** dans le coffre.

### <a name="command-line-installation"></a>Installation avec une ligne de commande

Le fournisseur Azure Site Recovery peut également être installé à l’aide de la ligne de commande suivante. Vous pouvez utiliser cette méthode pour installer le fournisseur sur un module Server Core pour Windows Server 2012 R2.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription dans un dossier, par exemple, C:\ASR.
2. Arrêtez le service VMM.
3. Extrayez le programme d’installation du fournisseur. En tant qu’administrateur, exécutez les commandes suivantes :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Installez le fournisseur :

        C:\ASR> setupdr.exe /i
5. Inscrivez le fournisseur :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Paramètres :

* **/Credentials** : paramètre obligatoire, qui spécifie l’emplacement auquel le fichier de clé d’inscription se trouve.  
* **/FriendlyName** : paramètre obligatoire, qui correspond au nom du serveur hôte Hyper-V qui s’affiche sur le portail Azure Site Recovery.
* **/EncryptionEnabled** : paramètre facultatif utilisé uniquement lors de la réplication de VMM vers Azure.
* **/proxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy.
* **/proxyport**: paramètre facultatif qui spécifie le port du serveur proxy.
* **/proxyUsername** : paramètre facultatif qui spécifie le nom d’utilisateur proxy (si le proxy nécessite une authentification).
* **/proxyPassword** : paramètre facultatif qui spécifie le mot de passe pour l’authentification auprès du serveur proxy (si le proxy nécessite une authentification).

## <a name="step-3-map-storage-arrays-and-pools"></a>Étape 3 : Mapper les groupes et pools de stockage

Mappez les groupes principaux et secondaires pour spécifier le pool de stockage secondaire censé recevoir les données de réplication du pool principal. Mappez le stockage avant de configurer la réplication, car les informations de mappage sont utilisées quand vous activez la protection des groupes de réplication.

Avant de commencer, vérifiez que les clouds VMM apparaissent bien dans le coffre. Les clouds sont détectés quand vous synchronisez tous les clouds lors de l’installation du fournisseur, ou lorsque vous synchronisez un cloud spécifique dans la console VMM.

1. Cliquez sur **Ressources** > **Stockage serveur** > **Mapper les baies de stockage source et cible**.
    ![Inscription du serveur](./media/site-recovery-vmm-san/storage-map.png)

2. Sélectionnez les groupes de stockage dans le site principal et mappez-les aux groupes de stockage dans le site secondaire. Dans **Pools de stockage**, sélectionnez un pool de stockage source et un pool de stockage cible à mapper.

    ![Enregistrement du serveur](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>Étape 4 : Configurer les paramètres de réplication

Une fois les serveurs VMM inscrits, configurez les paramètres de protection de cloud.

1. Sur la page **Démarrage rapide**, cliquez sur **Configurer la protection pour les clouds VMM**.
2. Dans l’onglet **Éléments protégés**, sélectionnez la **Configuration** du cloud.
3. Dans **Cible**, sélectionnez **VMM**.
4. Dans **Emplacement cible**, sélectionnez le serveur VMM qui gère le cloud à utiliser pour la récupération.
5. Dans **Cloud cible**, sélectionnez le cloud cible à utiliser pour le basculement de la machine virtuelle.
   * Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous protégez.
   * Un cloud ne peut appartenir qu’à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.
6. Azure Site Recovery vérifie que les clouds ont accès au stockage SAN et que les groupes de stockage sont mappés.
7. Si la vérification aboutit, sélectionnez **SAN** dans **Type de réplication**.

Suite à l’enregistrement des paramètres, une tâche est créée et peut être surveillée dans l’onglet **Tâches**. Les paramètres peuvent être modifiés dans l’onglet **Configure**. Pour modifier l’emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.

## <a name="step-5-enable-network-mapping"></a>Étape 5 : Activer le mappage réseau

1. Dans la page **Démarrage rapide**, cliquez sur **Mapper les réseaux**.
2. Sélectionnez le serveur VMM source, puis le serveur VMM cible auquel les réseaux seront mappés. La liste des réseaux sources et leurs réseaux cibles associés s'affiche à l'écran. Les réseaux non mappés n’affichent aucune valeur. Cliquez sur l'icône d'information en regard du nom des réseaux source et cible pour afficher les sous-réseaux de chaque réseau.
3. Sélectionnez un réseau dans **Réseau sur la source**, puis cliquez sur **Mapper**. Le service détecte les réseaux de machines virtuelles sur le serveur cible et les affiche.

    ![Architecture SAN](./media/site-recovery-vmm-san/network-map1.png)
4. Sélectionnez l’un des réseaux de machines virtuelles du serveur VMM cible.

    ![Architecture SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Quand vous sélectionnez un réseau cible, les clouds protégés qui utilisent le réseau source sont affichés. Les réseaux cibles disponibles sont également affichés. Nous vous recommandons de sélectionner un réseau cible accessible à tous les clouds utilisés pour la réplication.
6. Cliquez sur la coche pour terminer le processus de mappage. Une tâche de suivi de la progression commence. Vous pouvez l'afficher dans l'onglet **Tâches** .

## <a name="step-6-enable-replication-for-replication-groups"></a>Étape 6 : activer la réplication des groupes de réplication

Avant d’activer la protection des machines virtuelles, vous devez activer la réplication des groupes de réplication de stockage.

1. Dans la page **Propriétés** du cloud principal du portail Site Recovery, ouvrez l’onglet **Machines virtuelles**, puis cliquez sur **Ajouter un groupe de réplication**.
2. Sélectionnez un ou plusieurs groupes de réplication VMM associés au cloud, vérifiez les groupes source et cible, puis spécifiez la fréquence de réplication.

Site Recovery, VMM et les fournisseurs SMI-S approvisionnent les numéros d’unité logique de stockage du site cible et activent la réplication du stockage. Si le groupe de réplication est déjà répliqué, Site Recovery réutilise la relation de réplication existante et met à jour les informations.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Étape 7 : Activation de la protection des machines virtuelles


Lors de la réplication d’un groupe de stockage, activez la protection des machines virtuelles dans la console VMM à l’aide de l’une des méthodes suivantes :

* **Nouvelle machine virtuelle** : lorsque vous créez une machine virtuelle, vous activez la réplication et associez la machine virtuelle au groupe de réplication.
  Si vous choisissez cette option, VMM place de manière optimale le stockage des machines virtuelles sur les numéros d’unité logique du groupe de réplication. Site Recovery orchestre la création d’une machine virtuelle masquée sur le site secondaire et alloue de l’espace de stockage pour que les machines virtuelles de réplication puissent être démarrées après le basculement.
* **Machine virtuelle existante** : si une machine virtuelle est déjà déployée dans VMM, vous pouvez activer la réplication et effectuer une migration du stockage vers un groupe de réplication. Au terme de l’opération, VMM et Site Recovery détectent la nouvelle machine virtuelle et commencent à la gérer dans Site Recovery. Une machine virtuelle masquée est créée sur le site secondaire et des capacités sont allouées pour que la machine virtuelle de réplication puisse être démarrée après le basculement.

![Activer la protection](./media/site-recovery-vmm-san/enable-protect.png)

Une fois que les machines virtuelles sont activées pour la réplication, elles apparaissent dans la console Site Recovery. Vous pouvez afficher les propriétés des machines virtuelles, suivre leur état et suivre le basculement des groupes de réplication contenant plusieurs machines virtuelles. Dans le cadre de la réplication SAN, toutes les machines virtuelles associées à un groupe de réplication doivent basculer ensemble. Cela vient du fait que le basculement se produit d'abord au niveau de la couche de stockage. Il est important de regrouper vos groupes de réplication correctement et de placer uniquement les machines virtuelles associées ensemble.

> [!NOTE]
> Une fois que vous avez activé la réplication sur une machine, n’ajoutez pas de disques durs virtuels pour celle-ci aux numéros d’unité logique, sauf s’ils se trouvent dans un groupe de réplication Site Recovery. Les disques durs virtuels seront détectés par Site Recovery uniquement s’ils font partie d’un groupe de réplication Site Recovery.
>
>

Vous pouvez suivre la progression, y compris la réplication initiale, sous l’onglet **Tâches**. Lorsque la tâche de finalisation de la protection s’exécute, la machine virtuelle est prête à être basculée.

![Tâche de protection de la machine virtuelle](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Étape 8 : tester le déploiement

Pour vous assurer que les machines virtuelles basculent comme prévu, testez votre déploiement. Pour ce faire, créez un plan de récupération et exécutez un test de basculement.

1. Sous l’onglet **Plans de récupération**, cliquez sur **Créer un plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et sélectionnez les serveurs VMM source et cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source. Sélectionnez **SAN** pour n’afficher que les clouds configurés pour la réplication SAN.

    ![Créer un plan de récupération](./media/site-recovery-vmm-san/r-plan.png)

3. Dans **Sélectionner la machine virtuelle**, sélectionnez les groupes de réplication. Toutes les machines virtuelles associées au groupe sont ajoutées au plan de récupération. Ces machines virtuelles sont ajoutées au groupe par défaut (Groupe 1) du plan de récupération. Vous pouvez ajouter d’autres groupes si nécessaire. Après la réplication, les machines virtuelles sont numérotées selon l’ordre des groupes du plan de récupération.

    ![Sélectionner les machines virtuelles](./media/site-recovery-vmm-san/r-plan-vm.png)
4. Une fois le plan de récupération créé, il s’affiche dans la liste de l’onglet **Plans de récupération** . Sélectionnez le plan, puis choisissez **Test de basculement**.
5. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Si cette option est activée, les machines virtuelles de réplication basculées ne sont connectées à aucun réseau. Ce test vérifie que les machines virtuelles basculent correctement, mais il ne vérifie pas l’environnement réseau. Pour plus d’informations sur les autres options de mise en réseau, consultez [Basculement Site Recovery](site-recovery-failover.md).

    ![Sélectionner le réseau de test](./media/site-recovery-vmm-san/test-fail1.png)

6. La machine virtuelle de test est créée sur le même hôte que celui sur lequel se trouve la machine virtuelle de réplication. Elle n’est pas ajoutée au cloud où se trouve la machine virtuelle de réplication.
2. Après la réplication, la machine virtuelle de réplication possède une adresse IP différente de celle de la machine virtuelle principale. Si vous émettez des adresses à partir de DHCP, la mise à jour s’effectue automatiquement. Si vous n’utilisez pas DHCP et que vous souhaitez que les adresses soient identiques, vous devez exécuter quelques scripts.
3. Exécutez ce script pour récupérer l’adresse IP :

       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. Exécutez cet exemple de script pour mettre à jour DNS. Spécifiez l’adresse IP extraite.

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>Étapes suivantes

Après avoir exécuté un test de basculement pour vérifier que votre environnement fonctionne comme prévu, consultez [Basculement Site Recovery](site-recovery-failover.md) pour découvrir les différents types de basculement.

