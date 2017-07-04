---
title: "Répliquer des machines virtuelles Hyper-V sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment répliquer des machines virtuelles Hyper-V de clouds VMM sur un site secondaire à l’aide du portail Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a2bc32193ad539053984794a411ae7307b8d8532
ms.contentlocale: fr-fr
ms.lasthandoff: 03/15/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire avec le portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmm-to-vmm.md)
> * [Portail classique](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Cet article décrit comment utiliser [Site Recovery](site-recovery-overview.md) dans le portail Azure pour répliquer des machines virtuelles Hyper-V locales gérées sur les clouds System Center Virtual Machines Manager (VMM) dans un site secondaire. En savoir plus sur cette [architecture du scénario](site-recovery-components.md#hyper-v-to-a-secondary-site).

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Composants requis

**Configuration requise** | **Détails**
--- | ---
**Microsoft Azure** | Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.
**Instances VMM locales** | Nous vous recommandons de déployer 2 serveurs VMM, un sur le site principal et un sur le site secondaire.<br/><br/> Vous pouvez effectuer la réplication entre des clouds sur un seul serveur VMM.<br/><br/> Les serveurs VMM doivent exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Les serveurs VMM doivent avoir accès à Internet.
**Clouds VMM** | Chaque serveur VMM doit disposer d’un ou plusieurs clouds et tous les clouds doivent avoir le profil de capacité Hyper-V. <br/><br/>Les clouds doivent contenir un ou plusieurs groupes hôtes VMM.<br/><br/> Si vous ne disposez que d’un seul serveur VMM, vous devez lui associer au moins 2 clouds, agissant en tant qu’instances primaire et secondaire.
**Hyper-V** | Les serveurs Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/>  Les serveurs hôtes Hyper-V doivent être situés dans des groupes hôtes dans les clouds VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster sur Windows Server 2012 R2, installez la [mise à jour 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012, notez que le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Configurez manuellement le répartiteur de cluster. [En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Les serveurs Hyper-V doivent disposer d’un accès Internet.
**URLs** | Les serveurs VMM et les hôtes Hyper-V doivent être en mesure d’atteindre ces URL :<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

## <a name="deployment-steps"></a>Étapes du déploiement

Voici la procédure à suivre :

1. Assurez-vous que la configuration requise est respectée.
2. Préparez le serveur VMM et les hôtes Hyper-V.
3. Créez un coffre Recovery Services. Le coffre comporte les paramètres de configuration et orchestre la réplication.
4. Spécifiez les paramètres de la source, de la cible et de réplication.
5. Déploiement du service Mobilité sur les machines virtuelles que vous souhaitez répliquer.
6. Préparez la réplication, et activez la réplication pour les machines virtuelles Hyper-V.
7. Exécutez un test de basculement afin de vérifier que tout fonctionne bien.

## <a name="prepare-vmm-servers-and-hyper-v-hosts"></a>Préparer les serveurs VMM et les hôtes Hyper-V

Pour préparer le déploiement :

1. Assurez-vous que le serveur VMM et les hôtes Hyper-V sont conformes aux conditions préalables décrites ci-dessus et peuvent atteindre les URL requises.
2. Configurez les réseaux VMM de manière à pouvoir définir le [mappage réseau](#network-mapping-overview).

    - Assurez-vous que l’ensemble des machines virtuelles du serveur hôte Hyper-V source sont connectées à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
    Vérifiez que le cloud secondaire que vous utilisez pour la récupération est configuré avec un réseau de machines virtuelles correspondant. Ce réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud secondaire.

3. Préparer un [déploiement de serveur unique](#single-vmm-server-deployment), si vous souhaitez répliquer des machines virtuelles entre des clouds sur le même serveur VMM.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **Gestion** > **Recovery Services**.
3. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez-en un.
4. [Créez un groupe de ressources](../azure-resource-manager/resource-group-template-deploy-portal.md)ou sélectionnez-en un. Spécifiez une région Azure. Les machines sont répliquées dans cette région. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Si vous souhaitez accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord** > **Créer un archivage**.

    ![Nouveau coffre](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Le nouveau coffre s’affiche dans la zone **Tableau de bord** dans **Toutes les ressources** et dans le panneau principal **Coffres Recovery Services**.


## <a name="choose-a-protection-goal"></a>Choisir un objectif en matière de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

2. Cliquez sur **Site Recovery** > **Étape 1 : Préparez l’infrastructure** > **Objectif de protection**.
3. Sélectionnez **Vers le site de récupération**, puis **Oui, avec Hyper-V**.
4. Sélectionnez **Oui** pour indiquer que vous utilisez VMM pour gérer les hôtes Hyper-V.
5. Sélectionnez **Oui** si vous avez un serveur VMM secondaire. Si vous déployez la réplication entre des clouds sur un seul serveur VMM, cliquez sur **Non**. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Installez le fournisseur Azure Site Recovery sur les serveurs VMM puis découvrez et inscrivez ces derniers dans le coffre.

1. Cliquez sur **Étape 1 : Préparer l’infrastructure** > **Source**.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. Dans **Préparer la source**, cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans le champ **Type de serveur** et que le serveur VMM répond à la [configuration requise](#prerequisites).
4. Téléchargez le fichier d’installation du fournisseur Azure Site Recovery.
5. Téléchargez la clé d’inscription. Vous en aurez besoin lorsque vous exécuterez le programme d’installation. Une fois générée, la clé reste valide pendant 5 jours.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. Installez le fournisseur Azure Site Recovery sur le serveur VMM. Vous n’avez besoin de rien installer explicitement sur les serveurs hôtes Hyper-V.


### <a name="install-the-azure-site-recovery-provider"></a>installation du fournisseur Azure Site Recovery

1. Exécutez le fichier d’installation du fournisseur sur chaque serveur VMM. Si VMM est déployé dans un cluster, procédez comme suit lors de la première installation :
    -  Installez le fournisseur sur un nœud actif et terminez l’installation pour inscrire ce serveur VMM dans le coffre.
    - Ensuite, installez le fournisseur sur les autres nœuds. Les nœuds de cluster doivent exécuter la même version du fournisseur.
2. Le programme d’installation exécute quelques vérifications préalables et demande l’autorisation d’arrêter le service VMM. Le service VMM redémarre automatiquement une fois l’installation terminée. Si vous installez sur un cluster VMM, vous êtes invité à arrêter le rôle de cluster.
3. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
4. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation par défaut et cliquez sur **Installer**.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. Une fois l’installation terminée, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré. Cliquez sur *Next*.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. Sur la page **Connexion Internet** , indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Azure.

    ![Paramètres Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   - Vous pouvez définir la connexion directe, ou via un proxy, du fournisseur à Internet.
   - Spécifiez les paramètres de proxy, si nécessaire.
   - Si vous utilisez un proxy, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs dans la console VMM (**Paramètres** > **Sécurité** > **Comptes d’identification**). Redémarrez le service VMM afin de mettre à jour les modifications.
8. Dans **Registration Key**, sélectionnez la clé que vous avez téléchargée depuis Azure Site Recovery et copiez-la sur le serveur VMM.
9. Le paramètre de chiffrement est uniquement utilisé quand vous répliquez des machines virtuelles Hyper-V dans des clouds VMM sur Azure. Si vous répliquez sur un site secondaire, il n’est pas utilisé.
10. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
11. Dans **Synchroniser les métadonnées du cloud** , indiquez si vous voulez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l’aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud dans les propriétés du cloud de la console VMM.
12. Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l’onglet **Serveurs VMM** de la page **Serveurs** du coffre.

    ![Serveur](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. Une fois le serveur disponible dans la console Site Recovery, dans **Source** > **Préparer la source** , sélectionnez le serveur VMM, puis sélectionnez le cloud dans lequel se trouve l’hôte Hyper-V. Cliquez ensuite sur **OK**.

Vous pouvez également installer le fournisseur à partir de la ligne de commande :

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez le cloud et le serveur VMM cible.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez le serveur VMM cible à utiliser.
2. Les clouds du serveur qui sont synchronisés avec Site Recovery s’affichent. Sélectionnez le cloud cible.

   ![Cible](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="set-up-replication-settings"></a>Configurer les paramètres de réplication

- Lorsque vous créez une stratégie de réplication, l’ensemble des hôtes utilisant la stratégie doivent posséder le même système d’exploitation. Le cloud VMM peut contenir des hôtes Hyper-V exécutant différentes versions de Windows Server. Dans ce cas, vous devez posséder plusieurs stratégies de réplication.
- Vous pouvez exécuter la réplication initiale hors connexion. [En savoir plus](#prepare-for-initial-offline-replication)

1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.

    ![Réseau](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie. Le type source et cible doit être **Hyper-V**.
3. Dans **Version de l’hôte Hyper-V**, sélectionnez le système d’exploitation sous lequel fonctionne l’hôte.
4. Dans **Type d’authentification** et **Port d’authentification**, indiquez le mode d’authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. Sélectionnez **Certificat** , sauf si vous avez un environnement Kerberos opérationnel. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Vous n’avez pas besoin de faire quoi que ce soit manuellement. Par défaut, les ports 8083 et 8084 (pour les certificats) seront ouverts dans le Pare-feu Windows sur les serveurs hôtes Hyper-V. Si vous sélectionnez **Kerberos**, un ticket Kerberos est utilisé pour l'authentification mutuelle des serveurs hôtes. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
5. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).
6. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
7. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre 1 et 12 heures). Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle. Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés. Si vous activez les instantanés cohérents avec l'application, cela affecte les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
8. Dans **Compression du transfert de données**, indiquez si les données répliquées transférées doivent être compressées.
9. Sélectionnez **Supprimer une machine virtuelle de réplica** pour spécifier que la machine virtuelle doit être supprimée si vous désactivez la protection de la machine virtuelle source. Si vous activez ce paramètre et que vous désactivez la protection de la machine virtuelle source, elle est supprimée de la console Site Recovery, les paramètres Site Recovery de VMM sont supprimés de la console VMM et le réplica est supprimé.
10. Dans **Méthode de réplication initiale**, si vous effectuez une réplication sur le réseau, indiquez si vous souhaitez lancer la réplication initiale ou la planifier. Pour économiser de la bande passante réseau, il peut être intéressant de la planifier en dehors des heures de pointe. Cliquez ensuite sur **OK**.

     ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. Lorsque vous créez une stratégie, elle est automatiquement associée au cloud VMM. Dans **Stratégie de réplication**, cliquez sur **OK**. Vous pouvez associer des clouds VMM supplémentaires (ainsi que les machines virtuelles correspondantes) à cette stratégie de réplication en cliquant sur **Réplication** > Nom de la stratégie > **Associer un Cloud VMM**.

     ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/policy-associate.png)


### <a name="configure-network-mapping"></a>configurer le mappage réseau

- En savoir plus sur le [mappage réseau](#prepare-for-network-mapping) avant de démarrer.
- Vérifiez que les machines virtuelles sur les serveurs VMM sont connectées à un réseau de machines virtuelles.


1. Dans **Infrastructure Site Recovery** > **Mappage réseau** > **Mappages réseau**, cliquez sur **+Mappage réseau**.

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. Dans l’onglet **Ajouter un mappage réseau**, sélectionnez les serveurs VMM source et cible. Les réseaux de machines virtuelles associés aux serveurs VMM sont récupérés.
3. Dans la zone **Réseau source**, sélectionnez le réseau à utiliser dans la liste de réseaux de machines virtuelles associés au serveur VMM principal.
4. Dans **Réseau cible**, sélectionnez le réseau que vous souhaitez utiliser sur le serveur VMM secondaire. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Voici le processus exécuté lorsque le mappage réseau démarre :

* Toute machine virtuelle de réplication existante qui correspond au réseau de machines virtuelles source sera connectée aux réseaux de machines virtuelles cibles.
* Les nouvelles machines virtuelles qui sont connectées au réseau de machines virtuelles source seront connectées au réseau mappé cible après la réplication.
* Si vous modifiez un mappage existant avec un nouveau réseau, les machines virtuelles de réplication seront connectées à l'aide des nouveaux paramètres.
* Remarque : si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.

### <a name="configure-storage-mapping"></a>Configurez le mappage de stockage.

Le [mappage de stockage](#prepare-for-storage-mapping) n’est actuellement pas pris en charge dans le nouveau portail Azure. Toutefois, il peut être activé à l’aide de Powershell. [En savoir plus](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Étape 5 : planifier la capacité

Votre infrastructure de base est désormais configurée. Vous pouvez donc réfléchir à la planification de la capacité et déterminer si des ressources supplémentaires sont nécessaires.

- Téléchargez et exécutez l’outil [Azure Site Recovery Capacity Planner](site-recovery-capacity-planner.md) afin de collecter les informations sur votre environnement de réplication, notamment sur les machines virtuelles, les disques par machine virtuelle et le stockage par disque.
- Une fois que vous avez collecté les informations en temps réel sur la réplication, vous pouvez modifier la stratégie NetQos afin de contrôler la bande passante de réplication pour les machines virtuelles. Pour en savoir plus sur la [limitation du trafic des réplicas Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/), consultez le blog de Thomas Maurer. Informations supplémentaires sur l’[applet de commande New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx.).

## <a name="enable-replication"></a>Activer la réplication

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**. Après avoir activé la réplication pour la première fois, cliquez sur l’option **+Répliquer** dans le coffre pour activer la réplication des machines supplémentaires.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. Dans **Source**, sélectionnez le serveur VMM et le cloud hébergeant les hôtes Hyper-V à répliquer. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. Dans **Cible**, vérifiez le serveur VMM secondaire et le cloud.
4. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez protéger dans la liste.

    ![Activer la protection pour les machines virtuelles](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Lorsque la tâche de **finalisation de la protection** s’exécute, la machine virtuelle est prête à être basculée.

Notez les points suivants :

- Vous pouvez également activer la protection des machines virtuelles dans la console VMM. Cliquez sur **Activer la protection** dans la barre d’outils dans les propriétés de la machine virtuelle > onglet **Azure Site Recovery**.
- Une fois que vous avez activé la réplication, vous pouvez afficher les propriétés de la machine virtuelle dans **Éléments répliqués**. Dans le tableau de bord **Essentials**, vous pouvez voir des informations sur la stratégie de réplication pour la machine virtuelle et son état. Cliquez sur **Propriétés** pour obtenir plus de détails.

### <a name="onboard-existing-virtual-machines"></a>Intégrer des machines virtuelles existantes
Si vous avez des machines virtuelles existantes dans VMM qui sont répliquées à l’aide du réplica Hyper-V, vous pouvez les intégrer à la réplication Azure Site Recovery comme suit :

1. Vérifiez que le serveur Hyper-V qui héberge la machine virtuelle se trouve dans le cloud principal et que le serveur Hyper-V qui héberge la machine virtuelle de réplication se trouve dans le cloud secondaire.
2. Assurez-vous que la stratégie de réplication est configurée pour le cloud VMM principal.
3. Activez la réplication de la machine virtuelle principale. Azure Site Recovery et VMM s’assurent que la même machine virtuelle et le même hôte de réplication seront détectés, et Azure Site Recovery réutilisera et rétablira la réplication à l’aide des paramètres spécifiés.

## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un [test de basculement](site-recovery-test-failover-vmm-to-vmm.md) pour une seule machine virtuelle ou [créer un plan de récupération](site-recovery-create-recovery-plans.md) qui contient une ou plusieurs machines virtuelles.



## <a name="prepare-for-offline-initial-replication"></a>Préparer la réplication initiale hors connexion

Vous pouvez effectuer la réplication hors connexion pour la copie initiale des données. Vous pouvez la préparer comme suit :

* Sur le serveur source, vous spécifiez un emplacement de chemin d'accès à partir duquel l'exportation des données sera exécutée. Attribuez un contrôle total pour NTFS et des autorisations de partage au service VMM sur le chemin d'exportation. Sur le serveur cible, vous spécifiez un emplacement de chemin d'accès à partir duquel l'importation des données sera exécutée. Attribuez les mêmes autorisations sur ce chemin d'accès d'importation.
* Si le chemin d'accès d'importation ou d'exportation est partagé, attribuez l'appartenance au groupe Administrateur, Utilisateur avec pouvoir, Opérateur d'impression ou Opérateur de serveur pour le compte de service VMM sur l'ordinateur distant sur lequel le partage réside.
* Si vous utilisez des comptes d'identification quelconques pour ajouter des hôtes, sur les chemins d'accès d'importation et d'exportation, attribuez des autorisations de lecture et d'écriture aux comptes d'identification dans VMM.
* Les partages d'importation et d'exportation ne doivent pas se trouver sur un ordinateur utilisé comme serveur hôte Hyper-V, car la configuration en boucle n'est pas prise en charge par Hyper-V.
* Dans Active Directory, sur chaque serveur hôte Hyper-V qui contient des machines virtuelles à protéger, activez et configurez la délégation de contrainte pour approuver les ordinateurs distants sur lesquels se trouvent les chemins d'accès d'importation et d'exportation, comme suit :
  1. Sur le contrôleur de domaine, ouvrez **Utilisateurs et ordinateurs Active Directory**.
  2. Dans l’arborescence de la console, cliquez sur **DomainName** > **Ordinateurs**.
  3. Cliquez avec le bouton droit sur le nom du serveur hôte Hyper-V > **Propriétés**.
  4. Sous l’onglet **Délégation**, cliquez sur **N’approuver cet ordinateur que pour la délégation aux services spécifiés**.
  5. Cliquez sur **Utiliser tout protocole d’authentification**.
  6. Cliquez sur **Ajouter** > **Utilisateurs et ordinateurs**.
  7. Saisissez le nom de l'ordinateur qui héberge le chemin d'accès d'exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l'ordinateur qui héberge le chemin d'accès d'importation. Répétez cette procédure pour les serveurs hôtes Hyper-V supplémentaires.



## <a name="prepare-for-network-mapping"></a>Préparer le mappage réseau
Le mappage réseau effectue un mappage entre les réseaux de machines virtuelles VMM sur les serveurs VMM principaux et secondaires pour :

* Optimiser le positionnement des machines virtuelles de réplication sur les hôtes Hyper-V secondaires après le basculement.
* Connectez les machines virtuelles de réplication aux réseaux de machines virtuelles appropriés après le basculement.

Notez les points suivants :
- Le mappage réseau peut être configuré entre des réseaux de machines virtuelles sur deux serveurs VMM, ou sur un seul serveur VMM, lorsque deux sites sont gérés par le même serveur.
- Lorsque le mappage réseau est correctement configuré et que la réplication est activée, une machine virtuelle située à l’emplacement principal est connectée à un réseau, et son réplica (à l’emplacement cible) est connecté à son réseau mappé.
- Si les réseaux ont été correctement configurés dans VMM, lorsque vous sélectionnez un réseau de machines virtuelles cible dans le cadre du mappage réseau, les clouds sources VMM qui utilisent ce réseau de machines virtuelles source seront affichés, ainsi que les réseaux de machines virtuelles cibles disponibles sur les clouds cibles à utiliser pour la protection.
- Si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.


Voici un exemple permettant d’illustrer ce processus. Prenons l’exemple d’une entreprise ayant ouvert deux bureaux, l’un à New York et l’autre à Chicago.

| **Emplacement** | **Serveur VMM** | **Réseaux de machines virtuelles** | **Mappés à** |
| --- | --- | --- | --- |
| New York |VMM-NewYork |VMNetwork1-NewYork |Mappé au réseau VMNetwork1-Chicago |
| VMNetwork2-NewYork |Non mappé | | |
| Chicago |VMM-Chicago |VMNetwork1-Chicago |Mappé au réseau VMNetwork1-NewYork |
| VMNetwork2-Chicago |Non mappé | | |

Dans cet exemple :

* Lorsqu’un ordinateur virtuel de réplication est créé pour une machine virtuelle connectée au réseau VMNetwork1-NewYork, il est connecté au réseau VMNetwork1-Chicago.
* Lorsqu’un ordinateur virtuel de réplication est créé pour le réseau VMNetwork2-NewYork ou VMNetwork2-Chicago, il n’est pas connecté à un réseau.

Voici comment les clouds VMM sont configurés dans notre exemple d’organisation, ainsi que les réseaux logiques associés aux clouds.

### <a name="cloud-protection-settings"></a>Paramètres de protection des clouds
| **Cloud protégé** | **Cloud de protection** | **Réseau logique (New York)** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>N/D</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |
| SilverCloud2 |<p>N/D</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |

### <a name="logical-and-vm-network-settings"></a>Paramètres des réseaux de machines virtuelles et des réseaux logiques
| **Emplacement** | **Réseau logique** | **Réseau de machines virtuelles associé** |
| --- | --- | --- |
| New York |LogicalNetwork1-NewYork |VMNetwork1-NewYork |
| Chicago |LogicalNetwork1-Chicago |VMNetwork1-Chicago |
| LogicalNetwork2Chicago |VMNetwork2-Chicago | |

### <a name="target-networks"></a>Réseaux cibles
Selon ces paramètres, lorsque vous sélectionnez le réseau de machines virtuelles cible, le tableau suivant répertorie les choix disponibles.

| **Sélection** | **Cloud protégé** | **Cloud de protection** | **Réseau cible disponible** |
| --- | --- | --- | --- |
| VMNetwork1-Chicago |SilverCloud1 |SilverCloud2 |Disponible |
| GoldCloud1 |GoldCloud2 |Disponible | |
| VMNetwork2-Chicago |SilverCloud1 |SilverCloud2 |Non disponible |
| GoldCloud1 |GoldCloud2 |Disponible | |


### <a name="failback"></a>Restauration automatique
Pour voir ce qui se produit en cas de restauration automatique (réplication inverse), supposons que le réseau VMNetwork1-NewYork est mappé au réseau VMNetwork1-Chicago, avec les paramètres suivants.

| **Machine virtuelle** | **Connectée au réseau de machines virtuelles** |
| --- | --- |
| MV1 |VMNetwork1-Network |
| VM2 (réplica de VM1) |VMNetwork1-Chicago |

Examinons ce qui se passe dans différents scénarios possibles avec ces paramètres.

| **Scénario** | **Résultat** |
| --- | --- |
| Aucune modification n’est apportée aux propriétés du réseau de la machine VM2 après le basculement. |La machine VM1 reste connectée au réseau source. |
| Les propriétés du réseau de la machine VM2 sont modifiées après le basculement ; la machine est déconnectée. |La machine VM1 est déconnectée. |
| Les propriétés du réseau de la machine VM2 sont modifiées après le basculement ; la machine est connectée au réseau VMNetwork2-Chicago. |Si le réseau VMNetwork2-Chicago n’est pas mappé, la machine VM1 est déconnectée. |
| Le mappage réseau de VMNetwork1-Chicago est modifié. |La machine VM1 est connectée au réseau désormais mappé à VMNetwork1-Chicago. |


## <a name="prepare-for-single-server-deployment"></a>Préparation du déploiement d’un serveur unique


Si vous n’avez qu’un seul serveur VMM, vous pouvez répliquer des machines virtuelles dans des hôtes Hyper-V du cloud VMM vers [Azure](site-recovery-vmm-to-azure.md) ou vers un cloud VMM secondaire. Nous vous recommandons la première option, dans la mesure où la réplication entre les clouds n’est pas transparente. Si vous ne souhaitez pas répliquer entre les clouds, vous pouvez le faire avec un seul serveur VMM autonome ou avec un seul serveur VMM déployé dans un cluster Windows étendu.

### <a name="standalone-vmm-server"></a>Serveur VMM autonome

Dans ce scénario, vous déployez le serveur VMM unique comme machine virtuelle sur le site principal et répliquez cette machine virtuelle sur un site secondaire avec Site Recovery et le réplica Hyper-V.

1. **Configurez VMM sur une machine virtuelle Hyper-V**. Nous vous conseillons de stocker l’instance SQL Server utilisée par VMM sur la même machine virtuelle. De cette manière, vous pouvez gagner du temps, car une seule machine virtuelle doit être créée. Si vous souhaitez utiliser une instance distante de SQL Server, vous devez récupérer cette instance pour pouvoir récupérer VMM en cas de défaillance.
2. **Vérifiez que le serveur VMM a au moins deux clouds configurés**. Un cloud contient les machines virtuelles à répliquer et l’autre cloud sert d’emplacement secondaire. Le cloud qui contient les machines virtuelles que vous souhaitez protéger doit remplir les [conditions préalables](#prerequisites).
3. Configurez Site Recovery comme le décrit cet article. Créez et inscrivez le serveur VMM dans un coffre, configurez une stratégie de réplication et activez la réplication. Les noms source et cible VMM sont alors identiques. Spécifiez que la réplication initiale s’effectue via le réseau.
4. Lorsque vous configurez le mappage réseau, vous mappez le réseau de machines virtuelles du cloud principal au réseau de machines virtuelles du cloud secondaire.
5. Dans la console Gestionnaire Hyper-V, activez la réplication Hyper-V sur l’hôte Hyper-V qui contient la machine virtuelle VMM et activez la réplication sur la machine virtuelle. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à des clouds protégés par Site Recovery afin de garantir que les paramètres de réplication Hyper-V ne sont pas remplacés par Site Recovery.
6. Si vous créez des plans de récupération pour le basculement, vous utilisez le même serveur VMM source et cible.
7. Lors d’une panne complète, vous effectuez le basculement et la récupération comme suit :

   1. Dans la console Hyper-V Manager sur le site secondaire, exécutez un basculement non planifié pour basculer la machine virtuelle VMM du site principal vers le site secondaire.
   2. Vérifiez que la machine virtuelle VMM est opérationnelle et dans le coffre, puis exécutez un basculement non planifié des machines virtuelles du cloud principal vers le cloud secondaire. Validez le basculement, puis sélectionnez un autre point de récupération si nécessaire.
   3. Une fois que le basculement non planifié est terminé, l’utilisateur peut à nouveau accéder à toutes les ressources sur le site principal.
   4. Lorsque le site principal est de nouveau disponible, dans la console du gestionnaire Hyper-V du site secondaire, activez la réplication inverse de la machine virtuelle VMM. La réplication de la machine virtuelle du site secondaire au site principal démarre.
   5. Exécutez un basculement planifié dans la console du gestionnaire Hyper-V du site secondaire, afin de basculer la machine virtuelle VMM vers le site principal. Validez le basculement. Activez ensuite la réplication inverse, de manière à ce que la machine virtuelle VMM soit de nouveau répliquée du site principal vers le site secondaire.
   6. Dans le coffre Recovery Services, activez la réplication inverse pour les machines virtuelles de charge de travail pour démarrer la réplication du site secondaire vers le site principal.
   7. Dans le coffre Recovery Services, exécutez un basculement planifié pour restaurer les machines virtuelles de charge de travail vers le site principal. Validez le basculement pour le terminer. Ensuite, activez la réplication inverse pour commencer la réplication des machines virtuelles de charge de travail du site principal vers le site secondaire.

### <a name="stretched-vmm-cluster"></a>Cluster VMM étiré

Au lieu de déployer un serveur VMM autonome en tant que machine virtuelle répliquée vers un site secondaire, vous pouvez rendre VMM hautement disponible en le déployant comme machine virtuelle dans un cluster de basculement Windows. pour fournir une résilience de la charge de travail et une protection contre les défaillances matérielles. Pour effectuer un déploiement avec Site Recovery, la machine virtuelle VMM doit être déployée dans un cluster étendu sur des sites géographiquement distincts. Pour ce faire :

1. Installez VMM sur des machines virtuelles dans un cluster de basculement Windows et sélectionnez l’option permettant d’exécuter le serveur comme étant hautement disponible lors de configuration.
2. L’instance SQL Server utilisée par VMM doit être répliquée avec des groupes de disponibilité SQL Server AlwaysOn pour qu’un réplica de la base de données se trouve sur le site secondaire.
3. Suivez les instructions de cet article pour créer un coffre, inscrire le serveur et configurer la protection. Vous devez inscrire chaque serveur VMM dans le cluster du coffre Recovery Services. Pour ce faire, vous installez le fournisseur sur un nœud actif et inscrivez le serveur VMM. Vous installez ensuite le fournisseur sur les autres nœuds.
4. En cas de défaillance, le serveur VMM et la base de données SQL Server correspondante sont basculés et accessibles sur le site secondaire.



## <a name="prepare-for-storage-mapping"></a>Préparer le mappage de stockage


Vous configurez le mappage de stockage en mappant les classifications de stockage sur des serveurs VMM source et cible pour effectuer les opérations suivantes :

  * **Identifier le stockage cible pour les machines virtuelles de réplication** : un disque dur de machine virtuelle source réplique sur le stockage spécifié (partage de SMB ou volumes partagés de cluster (VPC)) dans l’emplacement cible.
  * **Placement d’ordinateurs virtuels de réplica**: le mappage de stockage permet d’optimiser le positionnement d’ordinateurs virtuels de réplica sur des serveurs hôtes Hyper-V. Les ordinateurs virtuels de réplica seront placés sur des hôtes ayant accès à la classification de stockage mappé.
  * **Aucun mappage de stockage**: si vous ne configurez aucun mappage de stockage, les machines virtuelles seront répliquées sur l’emplacement de stockage par défaut spécifié sur le serveur hôte Hyper-V associé à la machine virtuelle de réplica.

Notez les points suivants :
- Vous pouvez configurer le mappage entre 2 clouds VMM sur un serveur unique.
- Les classifications de stockage doivent être accessibles aux groupes hôtes situés dans les clouds source et cible.
- Il n'est pas obligatoire qu'elles aient le même type de stockage. Par exemple, vous pouvez mapper une classification source contenant des partages SMB à une classification cible contenant des volumes partagés de cluster.

### <a name="example"></a>Exemple
Si les classifications sont correctement configurées dans VMM lorsque vous sélectionnez les serveurs VMM source et cible lors du mappage de stockage, les classifications source et cible s’affichent. Voici un exemple de partages de fichiers de stockage et de classifications pour une entreprise ayant deux sites à New York et Chicago.

| **Emplacement** | **Serveur VMM** | **Partage de fichiers (source)** | **Classification (source)** | **Mappé à** | **Partage de fichiers (cible)** |
| --- | --- | --- | --- | --- | --- |
| New York |VMM_Source |SourceShare1 |GOLD |GOLD_TARGET |TargetShare1 |
| SourceShare2 |SILVER |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONZE |BRONZE_TARGET |TargetShare3 | | |
| Chicago |VMM_Target | |GOLD_TARGET |Non mappé | |
|  |SILVER_TARGET |Non mappé | | | |
|  |BRONZE_TARGET |Non mappé | | | |

Dans cet exemple :

* Lorsqu’une machine virtuelle de réplication est créée pour une machine virtuelle sur le stockage GOLD (SourceShare1), elle est répliquée sur un stockage GOLD_TARGET (TargetShare1).
* Lorsqu’une machine virtuelle de réplication est créée pour n’importe quelle machine virtuelle sur le stockage SILVER (SourceShare2), elle est répliquée sur un stockage SILVER_TARGET (TargetShare2) etc.

### <a name="multiple-storage-locations"></a>Emplacements de stockage multiples
Si la classification cible est affectée à plusieurs partages SMB ou VPC, l’emplacement de stockage optimal est sélectionné automatiquement lorsque l’ordinateur virtuel est protégé. Si aucun stockage cible approprié n'est disponible avec la classification spécifiée, l'emplacement de stockage par défaut spécifié sur l'hôte Hyper-V est utilisé pour placer les disques durs virtuels de réplica.

Le tableau suivant affiche la classification de stockage et les volumes partagés de cluster sont configurés dans notre exemple.

| **Emplacement** | **Classification** | **Stockage associé** |
| --- | --- | --- |
| New York |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| Chicago |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

Ce tableau récapitule le comportement lorsque vous activez la protection des ordinateurs virtuels (VM1 à VM5) dans cet exemple d'environnement.

| **Ordinateur virtuel** | **Stockage source** | **Classification source** | **Stockage cible mappé** |
| --- | --- | --- | --- |
| MV1 |C:\ClusterStorage\SourceVolume1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>GOLD_TARGET pour les deux</p> |
| MV2 |\\FileServer\SourceShare1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>GOLD_TARGET pour les deux</p> |
| MV3 |C:\ClusterStorage\SourceVolume2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| MV4 |\FileServer\SourceShare2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>SILVER_TARGET pour les deux</p> |
| MV5 |C:\ClusterStorage\SourceVolume3 |N/A |Aucun mappage. Donc, l’emplacement de stockage par défaut de l’hôte Hyper-V est utilisé. |



### <a name="data-privacy-overview"></a>Présentation de la confidentialité des données

Le tableau suivant résume la façon dont les données sont stockées dans ce scénario :

- - -
| Action | **Détails** | **Données collectées** | **Utilisation** | **Obligatoire** |
| --- | --- | --- | --- | --- |
| **Inscription** | Vous inscrivez un serveur VMM dans un coffre Recovery Services. Si, par la suite, vous souhaitez annuler l’inscription d’un serveur, vous pouvez le faire en supprimant les informations du serveur sur le portail Azure. | Une fois un serveur VMM inscrit, Site Recovery collecte, traite et transfère les métadonnées du serveur VMM et les noms des clouds VMM détectés par Site Recovery. | Les données sont utilisées pour identifier et communiquer avec le serveur VMM approprié et configurer les paramètres des clouds VMM appropriés. | Cette fonctionnalité est requise. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, n’utilisez pas le service Site Recovery. |
| **Activer la réplication** | Le fournisseur Azure Site Recovery est installé sur le serveur VMM et est l’intermédiaire qui permet de communiquer avec le service Site Recovery. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Une fois que le fournisseur est installé, la fonctionnalité « Récupération de centre de données » est activée dans la console Administrateur VMM. Les machines virtuelles actuelles et nouvelles peuvent activer cette fonctionnalité pour assurer la protection d’une machine virtuelle. |Cette propriété étant définie, le fournisseur envoie le nom et l’ID de la machine virtuelle à Site Recovery.  La réplication est activée par le réplica Hyper-V de Windows Server 2012 ou Windows Server 2012 R2. Les données des machines virtuelles sont répliquées d'un hôte Hyper-V vers un autre (généralement situé dans un autre centre de données de « récupération »). |Site Recovery utilise les métadonnées pour renseigner les informations de la machine virtuelle dans le portail Azure. | Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations, n’activez pas la protection des machines virtuelles Site Recovery. Notez que toutes les données que le fournisseur transmet à Site Recovery sont envoyées au moyen du protocole HTTPS. |
| **Plan de récupération** | Les plans de récupération vous permettent de créer un plan d’orchestration pour le centre de données de récupération. Vous pouvez définir l’ordre de démarrage des machines virtuelles ou d’un groupe de machines virtuelles sur le site de récupération. Pour chaque machine virtuelle, vous pouvez aussi spécifier les scripts automatisés à exécuter ou l’action manuelle à entreprendre au moment de la récupération. Le basculement est généralement déclenché au niveau du plan de récupération pour une récupération coordonnée. | Site Recovery recueille, traite et transmet les métadonnées pour le plan de récupération, notamment les métadonnées des machines virtuelles et les métadonnées des éventuels scripts d’automatisation et notes d’actions manuelles. |Les métadonnées sont utilisées pour générer le plan de récupération dans le portail Azure. |Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, ne créez pas de plan de récupération. |
| **Mappage réseau** | Mappe les informations de réseau du centre de données principal vers le centre de données de récupération. Dès lors que les machines virtuelles sont récupérées sur le site de récupération, le mappage réseau permet d’établir une connectivité réseau. |Site Recovery collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (principal et centre de données). |Les métadonnées sont utilisées pour remplir les paramètres de réseau afin de vous permettre de mapper les informations réseau. | Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, n’utilisez pas le mappage réseau. |
| **Basculement (planifié/non planifié/test)** | Le basculement bascule les machines virtuelles d’un centre de données géré par VMM à un autre. L’action de basculement est déclenchée manuellement dans le portail Azure. |Le fournisseur du serveur VMM est averti de l’événement de basculement par Site Recovery et exécute une action de basculement sur l’hôte Hyper-V au moyen des interfaces VMM. Le basculement réel d’une machine virtuelle s’effectue d’un hôte Hyper-V à un autre et est géré par un réplica Hyper-V Windows Server 2012 ou Windows Server 2012 R2. Site Recovery utilise les informations envoyées pour spécifier l’état des informations de l’action de basculement dans le portail Azure. | Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, n’utilisez pas le basculement. |

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez testé le déploiement, apprenez-en davantage sur les autres types de [basculement](site-recovery-failover.md).

