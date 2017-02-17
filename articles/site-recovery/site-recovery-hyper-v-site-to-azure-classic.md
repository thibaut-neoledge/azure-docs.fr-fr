---
title: "Répliquer des machines virtuelles Hyper-V sur Azure dans le portail Classic | Microsoft Docs"
description: "Cet article décrit comment répliquer des machines virtuelles Hyper-V vers Azure avec Azure Site Recovery lorsque les machines ne sont pas gérées dans des clouds VMM."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 3f4c4483-e3dd-495a-bd02-c16e9e28c88d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: b991b27304a4f65485b30431c5eb29461f60984d


---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-without-vmm-with-azure-site-recovery"></a>Réplication de machines virtuelles Hyper-V en local dans Azure (sans VMM) avec Azure Site Recovery
> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Portail Classic](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

Bienvenue dans le service Azure Site Recovery.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence. Il orchestre la réplication des machines virtuelles et des serveurs physiques locaux vers le cloud (Azure) ou un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, voir [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Cet article explique comment répliquer des machines virtuelles Hyper-V locales dans Azure à l’aide d’Azure Site Recovery dans le Portail Azure. Dans ce scénario, les serveurs Hyper-V ne sont pas gérés dans des clouds VMM.

Après avoir lu cet article, envoyez vos commentaires en bas ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).





## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery dans le portail Azure

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le déploiement Azure Resource Manager et le déploiement classique. Azure offre également deux portails : le Portail Azure Classic et le Portail Azure.

Cet article explique comment effectuer le déploiement dans le Portail Classic. Le portail classique peut être utilisé pour gérer les coffres existants. Il est impossible de créer des coffres à l’aide du portail Classic.

## <a name="site-recovery-in-your-business"></a>Site Recovery dans votre entreprise

Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Voici ce que Site Recovery peut faire :

* Protection hors site pour les applications professionnelles s’exécutant sur des machines virtuelles Hyper-V
* Fourniture d’un emplacement unique pour configurer, gérer et surveiller la réplication, le basculement et la récupération
* Basculement simple vers Azure et restauration depuis Azure sur des serveurs hôtes Hyper-V dans votre site local
* Plans de récupération incluant plusieurs machines virtuelles et permettant ainsi le basculement simultané de charges de travail d’applications hiérarchisées

## <a name="azure-prerequisites"></a>Conditions préalables pour Azure
* Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous avez besoin d’un compte de stockage Azure pour stocker les données répliquées. La géo-réplication doit être activée pour ce compte. Il doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. [En savoir plus sur le stockage Azure](../storage/storage-introduction.md). Notez que nous ne prenons pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) dans les groupes de ressources.
* Vous devez disposer d’un réseau virtuel Azure afin que les machines virtuelles puissent se connecter à un réseau lorsque vous procédez à un basculement depuis votre site principal.

## <a name="hyper-v-prerequisites"></a>Conditions préalables liées à Hyper-V
* Dans le site local source, vous devez disposer d’un ou de plusieurs serveurs exécutant **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2**. Ce serveur doit :
* contenir une ou plusieurs machines virtuelles ;
* être connecté à Internet, directement ou via un proxy ;
* exécuter les correctifs décrits dans l’article [2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977").

## <a name="virtual-machine-prerequisites"></a>Configuration requise pour les machines virtuelles
Les machines virtuelles à protéger doivent présenter la [configuration requise pour les machines virtuelles Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

## <a name="provider-and-agent-prerequisites"></a>Conditions préalables requises par les fournisseurs et les agents
Dans le cadre du déploiement de Microsoft Azure Site Recovery, vous allez installer le fournisseur Azure Site Recovery et l’agent associé sur chaque serveur Hyper-V. Notez les points suivants :

* Il est recommandé de toujours utiliser la dernière version de ce fournisseur et de cet agent. Ceux-ci sont disponibles dans le portail Site Recovery.
* Tous les serveurs Hyper-V d’un coffre doivent disposer des mêmes versions du fournisseur et de l’agent.
* Le fournisseur exécuté sur le serveur se connecte à Site Recovery via Internet. Vous pouvez effectuer cette action sans proxy, au moyen des paramètres de proxy actuellement configurés sur le serveur Hyper-V, ou à l’aide des paramètres de proxy personnalisés que vous avez configurés lors de l’installation du fournisseur. Vous devez vous assurer que le serveur proxy que vous souhaitez utiliser peut accéder à ces URL pour se connecter à Azure :

  * *.accesscontrol.windows.net
  * *.backup.windowsazure.com
  * *.hypervrecoverymanager.windowsazure.com
  * *.store.core.windows.net      
  * *.blob.core.windows.net
  - https://www.msftncsi.com/ncsi.txt
  - time.windows.com
  - time.nist.gov
* Autorisez également les adresses IP décrites dans les [Plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) et le protocole HTTPS (443). Vous devez autoriser les plages IP de la région Azure que vous prévoyez d’utiliser, ainsi que celles de la région États-Unis de l'Ouest.

Cette illustration montre les différents canaux et ports de communication utilisés par Azure Site Recovery pour l’orchestration et la réplication.

![Topologie B2A](./media/site-recovery-hyper-v-site-to-azure-classic/b2a-topology.png)

## <a name="step-1-create-a-vault"></a>Étape 1 : Créer un coffre
1. Connectez-vous au [portail de gestion](https://portal.azure.com).
2. Développez **Data Services** > **Recovery Services**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer nouveau** > **Création rapide**.
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
5. Dans **Region**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section relative aux disponibilités géographiques sur la page [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Cliquez sur **Create vault**.

    ![Nouveau coffre](./media/site-recovery-hyper-v-site-to-azure-classic/vault.png)

Vérifiez la barre d’état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.

## <a name="step-2-create-a-hyper-v-site"></a>Étape 2 : Créer un site Hyper-V
1. Dans la page Recovery Services, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l’icône.

    ![Quick Start](./media/site-recovery-hyper-v-site-to-azure-classic/quick-start-icon.png)
2. Dans la liste déroulante, sélectionnez **Entre un site Hyper-V local et Microsoft Azure**.

    ![Scénario de site Hyper-V](./media/site-recovery-hyper-v-site-to-azure-classic/select-scenario.png)
3. Sur la page **Créer un site Hyper-V**, cliquez sur **Créer un site Hyper-V**. Spécifiez un nom de site et enregistrez-le.

    ![Site Hyper-V](./media/site-recovery-hyper-v-site-to-azure-classic/create-site.png)

## <a name="step-3-install-the-provider-and-agent"></a>Étape 3 : Installer le fournisseur et l’agent
Installez le fournisseur et l’agent sur chaque serveur Hyper-V hébergeant les machines virtuelles que vous souhaitez protéger.

Si vous les installez sur un cluster Hyper-V, exécutez les étapes 5 à 11 sur chaque nœud du cluster de basculement. Une fois que tous les nœuds sont enregistrés et que la protection est activée, les machines virtuelles sont protégées, même si elles sont migrées d’un nœud vers un autre dans le cluster.

1. Dans **Préparer les serveurs Hyper-V**, cliquez sur **Télécharger une clé d’inscription**.
2. Sur la page **Télécharger une clé d’inscription**, cliquez sur l’option **Télécharger** située en regard du site. Téléchargez la clé dans un lieu sûr facilement accessible par le serveur Hyper-V. Une fois générée, la clé est valide pendant 5 jours.

    ![Clé d’enregistrement](./media/site-recovery-hyper-v-site-to-azure-classic/download-key.png)
3. Cliquez sur **Télécharger le fournisseur** pour obtenir sa dernière version.
4. Exécutez le fichier sur chaque serveur Hyper-V à inscrire dans le coffre. Le fichier installe deux composants :
   * **Fournisseur Azure Site Recovery**: gère la communication et l’orchestration entre le serveur Hyper-V et le portail Microsoft Azure Site Recovery.
   * **Agent Azure Recovery Services**: gère le transport de données entre les machines virtuelles en cours d’exécution sur le serveur Hyper-V source et Microsoft Azure Storage.
5. Dans **Microsoft Update** , vous pouvez opter pour l’installation de mises à jour. Une fois ce paramètre activé, les mises à jour du fournisseur et de l’agent sont installées en fonction de votre stratégie Microsoft Update.

    ![Mises à jour Microsoft](./media/site-recovery-hyper-v-site-to-azure-classic/provider1.png)
6. Dans le champ **Installation** , indiquez où vous souhaitez installer le fournisseur et l’agent sur le serveur Hyper-V.

    ![Emplacement d’installation](./media/site-recovery-hyper-v-site-to-azure-classic/provider2.png)
7. Une fois l’installation terminée, poursuivez la procédure pour inscrire le serveur dans le coffre.
8. Sur la page **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre et le site Hyper-V auquel appartient le serveur Hyper-V.

    ![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure-classic/provider8.PNG)
9. Sur la page **Connexion Internet** , indiquez la façon dont le fournisseur se connecte à Microsoft Azure Site Recovery. Sélectionnez **Utiliser les paramètres proxy par défaut du système** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur. Si vous n’indiquez aucune valeur, les paramètres par défaut seront utilisés.

   ![Paramètres Internet](./media/site-recovery-hyper-v-site-to-azure-classic/provider7.PNG)
10. Le serveur est alors inscrit dans le coffre.

    ![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure-classic/provider15.PNG)
11. Une fois l’inscription terminée, les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s’affiche dans l’onglet **Sites Hyper-V**, sur la page **Serveurs** du coffre.

### <a name="install-the-provider-from-the-command-line"></a>Installer le fournisseur à partir de la ligne de commande
Vous avez également la possibilité d’installer le fournisseur Azure Site Recovery à partir de la ligne de commande. Nous vous recommandons d’utiliser cette méthode si vous souhaitez installer le fournisseur sur un ordinateur exécutant Windows Server Core 2012 R2. Utilisez la ligne de commande ci-dessous :

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription dans un dossier, par exemple C:\ASR.
2. Ouvrez une invite de commandes en tant qu’administrateur et saisissez la commande suivante :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Installez ensuite le fournisseur en exécutant :

        C:\ASR> setupdr.exe /i
4. Exécutez la commande suivante pour terminer l’enregistrement :

        CD C:\Program Files\Microsoft Azure Site Recovery Provider
        C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Les paramètres sont les suivants :

* **/Credentials**: spécifiez l’emplacement de la clé d’inscription que vous avez téléchargée.
* **/FriendlyName**: spécifiez un nom pour identifier le serveur hôte Hyper-V. Ce nom apparaîtra dans le portail
* **/EncryptionEnabled**: facultatif. Spécifiez si vous souhaitez chiffrer les machines virtuelles de réplication dans Azure (chiffrement au repos).
* **/proxyAddress** ; **/proxyport** ; **/proxyUsername** ; **/proxyPassword** : facultatifs. Spécifiez les paramètres de proxy si vous souhaitez utiliser un proxy personnalisé ; sinon, votre serveur proxy existant requiert une authentification.

## <a name="step-4-create-an-azure-storage-account"></a>Étape 4 : Création d’un compte de stockage Azure
* Dans la zone **Préparer des ressources**, sélectionnez l’option **Créer un compte de stockage** pour créer un compte de stockage Azure, si nécessaire. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le coffre Microsoft Azure Site Recovery et être associé au même abonnement.

    ![Créer un compte de stockage](./media/site-recovery-hyper-v-site-to-azure-classic/create-resources.png)

> [!NOTE]
> 1. Nous ne prenons pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau Portail Azure](../storage/storage-create-storage-account.md) dans les groupes de ressources.
> 2. [La migration de comptes de stockage](../azure-resource-manager/resource-group-move-resources.md) entre les groupes de ressources d’un même abonnement ou de plusieurs abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de Site Recovery.
>

## <a name="step-5-create-and-configure-protection-groups"></a>Étape 5 : Créer et configurer des groupes de protection
Les groupes de protection sont des regroupements logiques incluant les machines virtuelles que vous voulez protéger au moyen de ces paramètres de protection. Vous appliquez des paramètres de protection à un groupe de protection, et ces paramètres sont appliqués à toutes les machines virtuelles que vous ajoutez au groupe.

1. Dans **Créer et configurer des groupes de protection**, cliquez sur **Créer un nouveau groupe de protection**. Si la configuration requise n’est pas respectée, un message s’affiche. Vous pouvez cliquer sur **Afficher les détails** pour obtenir plus d’informations.
2. Sur l’onglet **Groupes de protection** , ajoutez un groupe de protection. Spécifiez un nom, le site Hyper-V source, le système **Azure**cible, le nom de votre abonnement Azure Site Recovery et votre compte Azure Storage.

    ![Groupe de protection](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group.png)
3. Dans **Paramètres de réplication**, définissez l’option **Copier la fréquence** pour indiquer la fréquence de synchronisation du delta des données entre les systèmes cible et source. Vous pouvez choisir une fréquence de 30 secondes, 5 minutes ou 15 minutes.
4. Dans la zone **Conserver les points de récupération** , indiquez le nombre d’heures de stockage de l’historique de récupération.
5. Dans la zone **Fréquence des instantanés cohérents au niveau applicatif** , vous pouvez indiquer si le système doit créer des instantanés en utilisant le service VSS (Volume Shadow Copy Service) pour garantir que les applications présentent un état cohérent lors de la création de l’instantané. Par défaut, aucun instantané n’est créé. Assurez-vous que cette valeur est inférieure au nombre de points de récupération supplémentaires que vous configurez. Cette option est uniquement prise en charge lorsque la machine virtuelle exécute un système d’exploitation Windows.
6. Dans la zone **Heure de début de la réplication initiale** , spécifiez à quel moment la réplication initiale des machines virtuelles au sein du groupe de protection doit être envoyée à Microsoft Azure.

    ![Groupe de protection](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group2.png)

## <a name="step-6-enable-virtual-machine-protection"></a>Étape 6 : Activer la protection des machines virtuelles
Ajoutez des machines virtuelles à un groupe de protection pour activer leur protection.

> [!NOTE]
> La protection des machines virtuelles exécutant Linux avec une adresse IP statique n’est pas prise en charge.
>
>

1. Dans l’onglet **Machines** du groupe de protection, cliquez sur **Ajouter des machines virtuelles à des groupes de protection pour les protéger**.
2. Dans la page **Activer la protection pour les machines virtuelles** , sélectionnez les machines virtuelles à protéger.

    ![Activer la protection pour les machines virtuelles](./media/site-recovery-hyper-v-site-to-azure-classic/add-vm.png)

    Les tâches d’activation de la protection démarrent. Effectuez un suivi de la progression sur l’onglet **Tâches** . Lorsque la tâche de finalisation de la protection s’exécute, la machine virtuelle est prête à être basculée.
3. Une fois la protection activée, vous pouvez effectuer les opérations suivantes :

   * afficher les machines virtuelles dans le champ **Éléments protégés** > **Groupes de protection** > *nom_groupeprotection* > **Machines virtuelles**. Vous pouvez accéder aux détails de la machine dans l’onglet **Propriétés** ;
   * configurer les propriétés du basculement des machines virtuelles dans **Éléments protégés** > **Groupes de protection** > *nom_groupeprotection* > **Machines virtuelles** *nom_machine_virtuelle* > **Configurer**. Vous pouvez configurer les éléments suivants :

     * **Nom**: nom de la machine virtuelle dans Microsoft Azure.
     * **Taille**: taille cible de la machine virtuelle ayant basculé.

       ![Configurer les propriétés des machines virtuelles](./media/site-recovery-hyper-v-site-to-azure-classic/vm-properties.png)
   * Configurez les paramètres supplémentaires des machines virtuelles dans le champ *Éléments protégés** > **Groupes de protection** > *nom_groupeprotection* > **Machines virtuelles** *nom_machine_virtuelle* > **Configurer**. Cela inclut notamment les éléments suivants :

     * **Cartes réseau**: le nombre de cartes réseau est défini par la taille spécifiée pour la machine virtuelle cible. Vérifiez dans les [spécifications de taille de machine virtuelle](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) le nombre de cartes réseau prises en charge par une machine virtuelle de cette taille.

       Lorsque vous modifiez la taille d’une machine virtuelle et enregistrez les paramètres, le nombre de cartes réseau changera lors de la prochaine ouverture de la page **Configurer** . Le nombre de cartes réseau des machines virtuelles cible est au minimum le nombre de cartes réseau sur la machine virtuelle source et au maximum le nombre de cartes réseau prises en charge par la machine virtuelle choisie en fonction de sa taille. Vous trouverez l'explication ci-dessous :

       * Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
       * Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
       * Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.

     * **Réseau Microsoft Azure**: indiquez le réseau vers lequel la machine virtuelle doit basculer. Si la machine virtuelle présente plusieurs cartes réseau, l’ensemble des cartes doit être connecté au même réseau Microsoft Azure.
     * **Sous-réseau** : pour chaque carte réseau de la machine virtuelle, sélectionnez le sous-réseau dans le réseau Microsoft Azure auquel la machine doit se connecter après le basculement.
     * **Adresse IP cible**: si la carte réseau de la machine virtuelle source est configurée pour utiliser une adresse IP statique, vous pouvez indiquer l’adresse IP de la machine virtuelle cible, afin de vérifier que la machine présente la même adresse IP après le basculement.  Si vous n’en indiquez aucune, n’importe quelle adresse disponible sera affectée lors du basculement. Si vous spécifiez une adresse en cours d’utilisation, le basculement échoue.

     > [!NOTE]
     > La [migration des réseaux](../azure-resource-manager/resource-group-move-resources.md) entre les groupes de ressources d’un même abonnement ou de plusieurs abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de Site Recovery.
     >

     ![Configurer les propriétés des machines virtuelles](./media/site-recovery-hyper-v-site-to-azure-classic/multiple-nic.png)




## <a name="step-7-create-a-recovery-plan"></a>Étape 7 : créer un plan de récupération
Pour tester le déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle ou un plan de récupération qui contient une ou plusieurs machines virtuelles. [En savoir plus](site-recovery-create-recovery-plans.md) sur la création d’un plan de récupération.

## <a name="step-8-test-the-deployment"></a>Étape 8 : tester le déploiement
Il existe deux manières d’exécuter un test de basculement vers Azure.

* **Tester le basculement sans réseau Azure**: ce type de test de basculement vérifie que la machine virtuelle s’affiche correctement dans Azure. La machine virtuelle ne sera connectée à aucun réseau Azure après le basculement.
* **Tester le basculement avec un réseau Azure**: ce type de basculement vérifie que l’ensemble de l’environnement de réplication s’affiche comme prévu et qu’après le basculement les machines virtuelles se connectent au réseau Azure cible spécifié. Notez que, pour le test de basculement, le sous-réseau de la machine virtuelle test sera déterminé en fonction du sous-réseau de la machine virtuelle de réplication. Ceci diffère de la réplication normale, selon laquelle le sous-réseau d’une machine virtuelle de réplication est basé sur le sous-réseau de la machine virtuelle source.

Si vous souhaitez exécuter un test de basculement sans spécifier de réseau Azure, vous n’avez rien à préparer.

Pour exécuter un test de basculement avec un réseau Azure cible, vous devez créer un réseau Azure isolé de votre réseau de production Azure (comportement par défaut quand vous créez un réseau dans Azure). Lisez l’article [Basculement via Microsoft Azure Site Recovery](site-recovery-failover.md#run-a-test-failover) pour plus de détails.

Pour pouvoir tester intégralement votre déploiement réseau et de réplication, vous devez configurer l’infrastructure afin que la machine virtuelle répliquée fonctionne comme prévu. Pour ce faire, vous pouvez par exemple configurer une machine virtuelle en tant que contrôleur de domaine avec DNS et la répliquer dans Azure à l’aide de Site Recovery pour la créer dans le réseau de test en exécutant un test de basculement.  [Lisez cet article](site-recovery-active-directory.md#test-failover-considerations) pour passer en revue les considérations relatives au test de basculement pour Active Directory.

Exécutez un test de basculement, en procédant comme suit :

> [!NOTE]
> Pour obtenir les meilleures performances possibles lorsque vous effectuez un basculement vers Azure, assurez-vous que vous avez installé l’Agent Azure sur l’ordinateur protégé. Cela permet de démarrer plus rapidement et de réaliser un diagnostic en cas de problème. L’agent Linux est disponible [ici](https://github.com/Azure/WALinuxAgent) et l’agent Windows est disponible [ici](http://go.microsoft.com/fwlink/?LinkID=394789)
>
>

1. Dans l’onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
2. Sur la page **Confirmer le test de basculement**, sélectionnez **Aucun** ou un réseau Azure spécifique.  Remarque : si vous sélectionnez **Aucun** , le test de basculement vérifie que la machine virtuelle a été répliquée correctement dans Microsoft Azure, mais il ne vérifie pas la configuration de votre réseau de réplication.

    ![Test de basculement](./media/site-recovery-hyper-v-site-to-azure-classic/test-nonetwork.png)
3. Sur l’onglet **Tâches** , vous pouvez suivre la progression du basculement. Vous devriez aussi pouvoir voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
4. Lorsque le basculement atteint la phase **Terminer le test**, cliquez sur **Terminer le test** pour mettre un terme à l’opération. Sous l’onglet **Tâches** , vous pouvez suivre la progression et l’état du basculement et effectuer toutes les actions nécessaires.
5. Après le basculement, vous pouvez voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.

   1. Vérifiez que les machines virtuelles démarrent correctement.
   2. Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement. Vous devez également ajouter un point de terminaison RDP sur la machine virtuelle. Pour cela, vous pouvez utiliser les [Runbooks Azure Automation](site-recovery-runbook-automation.md) .
   3. Une fois le basculement effectué, si vous utilisez une adresse IP publique pour vous connecter à la machine virtuelle dans Microsoft Azure via le Bureau à distance, vérifiez qu’aucune stratégie du domaine ne vous empêche de vous connecter à une machine virtuelle au moyen d’une adresse publique.
6. Une fois le test effectué, procédez comme suit :

   * Cliquez sur **Le test de basculement est terminé**. Nettoyez l’environnement de test afin qu’il mette hors tension et supprime automatiquement les machines virtuelles de test.
   * Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
7. Lorsque le basculement se trouve en phase **Terminer le test** , terminez la vérification comme suit :
   1. Examinez la machine virtuelle de réplication dans le portail Microsoft Azure. Vérifiez que la machine virtuelle démarre correctement.
   2. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
   3. Cliquez sur **Terminer le test** pour finir l’opération.
   4. Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
   5. Cliquez sur **Le test de basculement est terminé**. Nettoyez l’environnement de test pour mettre automatiquement hors tension et supprimer la machine virtuelle de test.

## <a name="next-steps"></a>Étapes suivantes
Une fois votre déploiement configuré et en cours d'exécution, découvrez [plus d'informations](site-recovery-failover.md) sur le basculement.



<!--HONumber=Jan17_HO5-->


