<properties
	pageTitle="Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM | Microsoft Azure"
	description="Cet article explique comment répliquer vers Azure des machines virtuelles Hyper-V sur des ordinateurs hôtes Hyper-V hébergés dans des clouds System Center VMM."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/15/2016"
	ms.author="raynew"/>

#  Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

## Vue d'ensemble

Cet article décrit la procédure de déploiement de Site Recovery afin de répliquer vers Azure des machines virtuelles Hyper-V sur des serveurs hôtes Hyper-V hébergés dans des clouds privés VMM.

Cet article décrit les conditions requises pour le scénario et montre comment configurer un coffre Site Recovery, installer le fournisseur Azure Site Recovery sur le serveur VMM source, inscrire le serveur dans le coffre, ajouter un compte de stockage Azure, installer l’agent Azure Recovery Services sur les serveurs hôtes Hyper-V, configurer les paramètres de protection des clouds VMM qui seront appliqués à toutes les machines virtuelles protégées, puis activer la protection pour ces machines virtuelles. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Publiez vos commentaires ou questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Architecture

![Architecture](./media/site-recovery-vmm-to-azure/topology.png)

- Le fournisseur Azure Site Recovery est installé sur le serveur VMM lors du déploiement de Site Recovery et le serveur VMM est inscrit dans le coffre Site Recovery. Le fournisseur communique avec Site Recovery pour orchestrer la réplication.
- L’agent Azure Recovery Services est installé sur les serveurs hôtes Hyper-V lors du déploiement de Site Recovery. Il gère la réplication des données vers Azure Storage.


## Conditions préalables pour Azure

Voici ce dont vous aurez besoin dans Azure :

**Configuration requise** | **Détails**
--- | ---
**Compte Azure**| Vous aurez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. 
**Stockage Azure** | Vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement. <br/><br/>Vous avez besoin d’un [compte de stockage géo-redondant standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit se trouver dans la même région que le service Site Recovery et être associé au même abonnement. Notez que la réplication vers les comptes Premium Storage n’est pas prise en charge actuellement et ne doit pas être utilisée. Nous ne prenons pas en charge le déplacement des comptes Storage créés à l’aide du [nouveau portail Azure ](../storage/storage-create-storage-account.md) à travers les groupes de ressources.<br/><br/>[Découvrez](../storage/storage-introduction.md) Azure Storage.
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront au moment du basculement. Le réseau virtuel Azure doit se trouver dans la même région que le coffre Site Recovery. 

## Conditions préalables locales

Voici ce dont vous aurez besoin en local :

**Configuration requise** | **Détails**
--- | ---
**VMM** | Vous devez disposer d’au moins un serveur VMM déployé comme serveur autonome physique ou virtuel, ou comme cluster virtuel. <br/><br/>Le serveur VMM doit exécuter System Center 2012 R2 sur lequel sont installées les dernières mises à jour cumulatives.<br/><br/>Vous aurez besoin au moins d’un cloud configuré sur le serveur VMM.<br/><br/>Le cloud source que vous souhaitez protéger doit contenir un ou plusieurs groupes hôtes VMM.<br/><br/>Pour en savoir plus sur la configuration des clouds VMM, consultez l’article [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) (Procédure pas à pas : Création de clouds privés avec System Center 2012 SP1 VMM) publié sur le blog de Keith Mayer.
**Hyper-V** | Vous aurez besoin d’un ou plusieurs serveurs hôtes Hyper-V ou clusters dans le cloud VMM. Le serveur hôte doit disposer d’une ou plusieurs machines virtuelles. <br/><br/>Le serveur Hyper-V doit s’exécuter au moins sur Windows Server 2012 R2 avec le rôle Hyper-V et les dernières mises à jour installées.<br/><br/>Tout serveur Hyper-V contenant des machines virtuelles à protéger doit se trouver dans un cloud VMM.<br/><br/>Si vous exécutez Hyper-V dans un cluster, notez que le service Broker de cluster n’est pas créé automatiquement si le cluster est basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. Pour [en savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters), consultez le billet d’Aidan Finn.
**Machines protégées** | Les machines virtuelles à protéger doivent remplir les [conditions requises pour Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## Conditions préalables liées au mappage réseau
Quand vous protégez des machines virtuelles dans Azure, le mappage réseau effectue un mappage entre les réseaux de machines virtuelles sur le serveur VMM source et les réseaux Azure cibles pour permettre ce qui suit :

- Toutes les machines qui basculent sur le même réseau peuvent se connecter les unes aux autres, quel que soit le plan de récupération auquel elles appartiennent.
- Si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter à d'autres machines virtuelles locales.
- Si vous ne configurez pas le mappage réseau, seules les machines virtuelles qui basculent dans le même plan de récupération pourront se connecter les unes aux autres après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, les conditions suivantes doivent être remplies :

- Les machines virtuelles que vous souhaitez protéger sur le serveur VMM source doivent être connectées à un réseau de machines virtuelles. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
- Un réseau Azure auquel les machines virtuelles répliquées peuvent se connecter après le basculement. Vous sélectionnerez ce réseau au moment du basculement. Le réseau doit être dans la même région que votre abonnement Azure Site Recovery.

Préparez le mappage réseau comme suit :

1. [En savoir plus](site-recovery-network-mapping.md) sur les exigences relatives au mappage réseau.
2. Préparez les réseaux de machines virtuelles dans VMM :

	- [Configurez les réseaux logiques](https://technet.microsoft.com/library/jj721568.aspx).
	- [Configurez les réseaux de machines virtuelles](https://technet.microsoft.com/library/jj721575.aspx).


## Étape 1 : Création d’un coffre Site Recovery

1. Connectez-vous au [Portail de gestion](https://portal.azure.com) à partir du serveur VMM à inscrire.
2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer nouveau** > **Création rapide**.
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
5. Dans **Region**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section relative aux disponibilités géographiques sur la page [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Cliquez sur **Create vault**.

	![Nouveau coffre](./media/site-recovery-vmm-to-azure/create-vault.png)

Vérifiez la barre d’état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.

## Étape 2 : Génération d’une clé d'inscription du coffre

Générez une clé d'inscription dans le coffre. Une fois que vous aurez téléchargé et installé le fournisseur Azure Site Recovery sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans le coffre.

1. Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Icône Quick Start](./media/site-recovery-vmm-to-azure/qs-icon.png)

2. Dans la liste déroulante, sélectionnez **Entre un site VMM local et Azure**.
3. Dans **Prepare VMM Servers**, cliquez sur le fichier **Generate registration key**. Le fichier de clé est généré automatiquement et est valide pendant cinq jours après sa création. Si vous n'accédez pas au portail Azure à partir du serveur VMM, vous devez copier ce fichier sur le serveur.

	![Clé d’enregistrement](./media/site-recovery-vmm-to-azure/register-key.png)

## Étape 3 : Installation du fournisseur Azure Site Recovery

1. Dans **Démarrage rapide** > **Préparer les serveurs VMM**, cliquez sur **Télécharger le fournisseur Microsoft Azure Site Recovery pour une installation sur les serveurs VMM** pour obtenir la dernière version du fichier d’installation du fournisseur.
2. Exécutez ce fichier sur le serveur VMM source. Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois, installez-le sur un nœud actif et terminez l'installation pour inscrire le serveur VMM dans le coffre. Ensuite, installez le fournisseur sur les autres nœuds. Notez que si vous mettez à niveau le fournisseur, vous devez le mettre à niveau sur tous les nœuds, car ils doivent tous exécuter la même version du fournisseur.
3. Le programme d’installation procède à une vérification de la configuration requise et demande l’autorisation d’arrêter le service VMM pour commencer l’installation du fournisseur. Le service VMM redémarre automatiquement une fois l’installation terminée. Si vous installez sur un cluster VMM, vous serez invité à arrêter le rôle de cluster.

	![Conditions préalables](./media/site-recovery-vmm-to-azure/prereq.png)

4. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

	![Mises à jour Microsoft](./media/site-recovery-vmm-to-azure/updates.png)


5.  L’emplacement de l’installation du fournisseur est défini dans **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Cliquez sur **Installer**.

	![Emplacement de l’installation](./media/site-recovery-vmm-to-azure/install-location.png)

6. Une fois le fournisseur installé, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

	![Installation terminée](./media/site-recovery-vmm-to-azure/install-complete.png)

7. Sur la page **Connexion Internet**, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Sélectionnez **Utiliser les paramètres proxy par défaut du système** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

	![Paramètres Internet](./media/site-recovery-vmm-to-azure/proxy.png)

	- Si vous souhaitez utiliser un proxy personnalisé, vous devez le configurer avant d'installer le fournisseur. Quand vous configurez les paramètres de proxy personnalisé, un test s'exécute pour vérifier la connexion proxy.
	- Si vous n'utilisez pas de proxy personnalisé ou si votre proxy par défaut nécessite une authentification, vous devez saisir les détails du proxy, y compris l'adresse du proxy et le port.
	- Les URL suivantes doivent être accessibles à partir du serveur VMMet des hôtes Hyper-V :
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Autorisez les adresses IP décrites dans la zone [Étendues d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) et le protocole HTTPS (443). Vous devez également autoriser les plages IP de la région Microsoft Azure que vous prévoyez d’utiliser, ainsi que celles de la région ouest des États-Unis.

	- Si vous utilisez un proxy personnalisé, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d'identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs VMM dans la console VMM. Pour cela, ouvrez l'espace de travail Paramètres, développez Sécurité, cliquez sur Comptes d'identification, puis modifiez le mot de passe de DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.

8. Dans **Registration Key**, indiquez que vous téléchargez depuis Azure Site Recovery et que vous copiez sur le serveur VMM.
9. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré. 

	![Enregistrement du serveur](./media/site-recovery-vmm-to-azure/credentials.png)

10. Vous pouvez spécifier un emplacement pour enregistrer le certificat SSL généré automatiquement pour le chiffrement de données. Ce certificat sera utilisé si vous activez le chiffrement de données pour un cloud VMM lors du déploiement de Site Recovery. Conservez ce certificat en sécurité. Quand vous exécutez un basculement vers Azure, vous le sélectionner pour déchiffrer les données chiffrées.

	![Enregistrement du serveur](./media/site-recovery-vmm-to-azure/encryption.png)

11. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.

12. Dans la synchronisation **Initial cloud metadata**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l'aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.

	![Enregistrement du serveur](./media/site-recovery-vmm-to-azure/friendly.png)

13. Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l’onglet **Serveurs VMM** de la page **Serveurs** du coffre.

### Installation à partir de la ligne de commande

Le fournisseur Azure Site Recovery peut également être installé à l’aide de la ligne de commande suivante. Cette méthode peut être utilisée pour installer le fournisseur sur un module Server Core pour Windows Server 2012 R2.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription dans un dossier, par exemple : C:\\ASR.
2. Arrêter le service System Center Virtual Machine Manager
3. À partir d’une invite de commandes avec élévation de privilèges, extrayez le programme d’installation du fournisseur en utilisant les commandes suivantes :

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installez le fournisseur comme suit :

		C:\ASR> setupdr.exe /i

5. Inscrivez le fournisseur comme suit :

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Où les paramètres sont les suivants :

 - **/Credentials**: paramètre obligatoire, qui spécifie l’emplacement auquel le fichier de clé d’inscription se trouve  
 - **/FriendlyName**: paramètre obligatoire, qui correspond au nom du serveur hôte Hyper-V qui s’affiche sur le portail Microsoft Azure Site Recovery
 - **/EncryptionEnabled**: paramètre facultatif permettant d’indiquer si vous souhaitez chiffrer vos machines virtuelles dans Azure (chiffrement « au repos »). Le nom de fichier doit comporter une extension **.pfx**.
 - **/proxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy
 - **/proxyport**: paramètre facultatif qui spécifie le port du serveur proxy
 - **/proxyUsername**: paramètre facultatif qui spécifie le nom d’utilisateur proxy.
 - **/proxyPassword**: paramètre facultatif qui spécifie le mot de passe proxy.  


## Étape 4 : Création d’un compte de stockage Azure

1. Si vous ne possédez pas de compte de stockage Azure, cliquez sur **Ajouter un compte Azure Storage** pour créer un compte.
2. La géo-réplication doit être activée dans le compte que vous créez. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.

	![Compte de stockage](./media/site-recovery-vmm-to-azure/storage.png)

>[AZURE.NOTE] Nous ne prenons pas en charge le déplacement des comptes Storage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) à travers les groupes de ressources.

## Étape 5 : Installation de l'agent Azure Recovery Services

Installez l’agent Azure Recovery Services sur chaque serveur hôte Hyper-V situé dans le cloud VMM.

1. Dans **Démarrage rapide**, cliquez sur **Télécharger l’agent Azure Site Recovery Services et l’installer sur les hôtes** afin d’obtenir la dernière version du fichier d’installation de l’agent.

	![Install Recovery Services Agent](./media/site-recovery-vmm-to-azure/install-agent.png)

2. Exécutez le fichier d’installation sur chaque serveur hôte Hyper-V.
3. Sur la page **Vérification de la configuration requise**, cliquez sur **Suivant**. Tous les éléments manquants de la configuration requise sont automatiquement installés.

	![Prerequisites Recovery Services Agent](./media/site-recovery-vmm-to-azure/agent-prereqs.png)

4. Dans la page **Paramètres d’installation**, indiquez où vous voulez installer l’agent et sélectionnez l’emplacement du cache dans lequel les métadonnées de sauvegarde seront installées. Cliquez ensuite sur **Installer**.
5. Une fois l’installation terminée, cliquez sur **Fermer** pour terminer l’Assistant.
	
	![Inscrire l’Agent MARS](./media/site-recovery-vmm-to-azure/agent-register.png)

### Installation à partir de la ligne de commande 

Vous pouvez également installer l’agent Microsoft Azure Recovery Services à partir de la ligne de commande à l’aide de la commande suivante :

    marsagentinstaller.exe /q /nu
	
## Étape 6 : Configuration des paramètres de protection de cloud

Une fois le serveur VMM inscrit, vous pouvez configurer les paramètres de protection de cloud. Vous avez activé l'option **Synchroniser les données du cloud avec le coffre** lors de l'installation du fournisseur. Ainsi, tous les clouds du serveur VMM s’affichent dans l'onglet <b>Éléments protégés</b> du coffre.

![Cloud publié](./media/site-recovery-vmm-to-azure/clouds-list.png)

1. Sur la page Démarrage rapide, cliquez sur **Set up protection for VMM clouds**.
2. Sous l'onglet **Éléments protégés**, cliquez sur le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**.
3. Dans **Cible**, sélectionnez **Azure**.
4. Dans **Compte de stockage**, sélectionnez le compte de stockage Azure que vous utilisez pour la réplication. 

	>[AZURE.NOTE] Nous ne prenons pas en charge le déplacement des comptes Storage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) à travers les groupes de ressources.

5. En regard de **Chiffrer les données stockées**, cliquez sur **Désactivé**. Ce paramètre indique que les données doivent être répliquées avec chiffrement entre le site local et Azure.
6. Dans **Copy frequency**, laissez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible.
7. Dans la zone **Conserver les points de récupération pour**, conservez le paramètre par défaut. Avec une valeur par défaut de zéro, seul le dernier point de récupération d'une machine virtuelle principale est stocké sur un serveur hôte de réplication.
8. Dans **Frequency of application-consistent snapshots**, laissez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées. Si vous définissez une valeur, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.
9. Dans la zone **Heure de démarrage de la réplication**, indiquez l'heure à laquelle la réplication initiale des données dans Azure doit démarrer. Le fuseau horaire du serveur hôte Hyper-V est utilisé à cet effet. Nous vous recommandons de planifier la réplication initiale sur des heures creuses.

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/cloud-settings.png)

Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet **Jobs**. Tous les serveurs hôtes Hyper-V dans le cloud VMM source seront configurés pour la réplication.

Les paramètres du cloud peuvent être modifiés sous l'onglet **Configurer**. Pour modifier l'emplacement ou le compte de stockage cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier. Notez que si vous modifiez le compte de stockage, la modification n'est appliquée que pour les machines virtuelles qui sont activées pour la protection après la modification du compte. Les machines virtuelles existantes ne sont pas migrées vers le nouveau compte de stockage.

## Étape 7 : Configuration du mappage réseau
Avant de commencer le mappage réseau, vérifiez que les machines virtuelles sur le serveur VMM source sont connectées à un réseau de machines virtuelles. En outre, vous devez créer un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux de machines virtuelles peuvent être mappés à un seul réseau Azure.

1. Dans la page Démarrage rapide, cliquez sur **Mapper les réseaux**.
2. Sous l’onglet **Réseaux**, dans **Emplacement source**, sélectionnez le serveur VMM source. Dans **Emplacement cible**, sélectionnez Azure.
3. Dans **Source**, une liste de réseaux de machines virtuelles associées au serveur VMM est affichée. Dans **Cible**, les réseaux Azure associés à l’abonnement sont affichés.
4. Sélectionnez le réseau de machines virtuelles sources et cliquez sur **Mapper**.
5. Dans la page **Sélectionner un réseau cible**, sélectionnez le réseau Azure cible que vous souhaitez utiliser.
6. Cliquez sur la coche pour terminer le processus de mappage.

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/map-networks.png)

Une fois les paramètres enregistrés, une tâche commence à suivre la progression du mappage et vous pouvez la surveiller sous l'onglet Tâches. Toute machine virtuelle de réplication existante qui correspond au réseau de machines virtuelles source sera connectée aux réseaux Azure cibles. Les nouvelles machines virtuelles qui sont connectées au réseau de machines virtuelles source seront connectées au réseau Azure mappé après la réplication. Si vous modifiez un mappage existant avec un nouveau réseau, les machines virtuelles de réplication seront connectées à l'aide des nouveaux paramètres.

Notez que si le réseau cible a plusieurs sous-réseaux et que l'un d'entre eux a le même nom que le sous-réseau où se trouve la machine virtuelle source, la machine virtuelle de réplication sera connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.

## Étape 8 : Activation de la protection des machines virtuelles

Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud. Notez les points suivants :

- Les machines virtuelles doivent répondre à la [configuration requise pour Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Pour activer la protection, vous devez définir les propriétés du système d'exploitation et du disque du système d'exploitation pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l'aide d'un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et **Configuration matérielle** des propriétés de la machine virtuelle. Si vous ne définissez pas ces propriétés dans VMM, vous pourrez les configurer dans le portail Azure Site Recovery.

	![Create virtual machine](./media/site-recovery-vmm-to-azure/enable-new.png)

	![Modify virtual machine properties](./media/site-recovery-vmm-to-azure/enable-existing.png)


1. Pour activer la protection, dans l’onglet **Machines virtuelles** dans le cloud où se trouve la machine virtuelle, cliquez sur **Activer la protection**, puis sélectionnez **Ajouter des machines virtuelles**.
2. Dans la liste des machines virtuelles du cloud, sélectionnez celle que vous voulez protéger.

	![activer la protection des machines virtuelles](./media/site-recovery-vmm-to-azure/select-vm.png)

	Suivez la progression de l’action **Activer la protection**, y compris la réplication initiale, dans l’onglet **Tâches**. À l’issue de l’exécution de la tâche **Finaliser la protection**, la machine virtuelle est prête à être basculée. Une fois la protection activée et les machines virtuelles répliquées, celles-ci sont affichées dans Azure.


	![Tâche de protection de la machine virtuelle](./media/site-recovery-vmm-to-azure/vm-jobs.png)

3. Vérifiez les propriétés de la machine virtuelle et modifiez-les si besoin est.

	![Verify virtual machines](./media/site-recovery-vmm-to-azure/vm-properties.png)

4. Dans l’onglet **Configurer** des propriétés de la machine virtuelle, les propriétés réseau suivantes peuvent être modifiées.



- **Nombre de cartes réseau sur la machine virtuelle cible** : le nombre de cartes réseau est défini en fonction de la taille de machine virtuelle cible que vous avez spécifiée. Vérifiez dans les [spécifications de taille de machine virtuelle](../virtual-machines/virtual-machines-size-specs.md#size-tables) le nombre de cartes réseau prises en charge par une machine virtuelle de cette taille. Lorsque vous modifiez la taille d’une machine virtuelle et enregistrez les paramètres, le nombre de cartes réseau changera lors de la prochaine ouverture de la page **Configurer**. Le nombre de cartes réseau des machines virtuelles cible correspond, au minimum, au nombre de cartes réseau sur la machine virtuelle source et, au maximum, au nombre de cartes réseau prises en charge par la machine virtuelle choisie en fonction de sa taille, comme suit :

	- Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
	- Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
	- Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte. 	

- **Réseau de la machine virtuelle cible** : le réseau auquel la machine virtuelle se connecte est déterminé par le mappage réseau du réseau de la machine virtuelle source. Si la machine virtuelle source possède plusieurs cartes réseau et si les réseaux source sont mappés à des réseaux différents sur la cible, vous devrez choisir l’un des réseaux cibles.
- **Sous-réseau de chacune des cartes réseau** : pour chaque carte réseau, vous pouvez choisir le sous-réseau auquel la machine virtuelle basculée se connectera.
- **Adresse IP cible** : si la carte réseau de la machine virtuelle source est configurée pour utiliser une adresse IP statique, vous pouvez fournir l’adresse IP de la machine virtuelle cible. Utilisez cette fonctionnalité pour conserve l’adresse IP d’une machine virtuelle source après un basculement. Si aucune adresse IP n’est fournie, une adresse IP disponible, quelle qu’elle soit, est attribuée à la carte réseau au moment du basculement. Si l’adresse IP cible est spécifiée, mais qu’elle est déjà utilisée par une autre machine virtuelle exécutée dans Azure, le basculement échoue.  

	![Modifier les propriétés du réseau](./media/site-recovery-vmm-to-azure/multi-nic.png)

>[AZURE.NOTE] Les machines virtuelles Linux avec adresse IP statique ne sont pas prises en charge.

## test du déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et y exécuter un test de basculement.

Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :

- Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement.
- Après le basculement, vous utiliserez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.

### Créer un plan de récupération

1. Sous l’onglet **Plans de récupération**, ajoutez un nouveau plan. Spécifiez un nom, **VMM** (Gestionnaire d’ordinateurs virtuels) dans **Type de source**, puis le serveur VMM source dans **Source**. La cible est Azure.

	![Créer un plan de récupération](./media/site-recovery-vmm-to-azure/recovery-plan1.png)

2. Dans la page **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles à ajouter au plan de récupération. Ces machines virtuelles sont ajoutées au groupe de plan de récupération par défaut (Groupe 1). Un maximum de 100 machines virtuelles dans un même plan de récupération ont été testées.

	- Si vous souhaitez vérifier les propriétés de la machine virtuelle avant de les ajouter au plan, cliquez sur la machine virtuelle dans la page de propriétés du cloud où elle se trouve. Vous pouvez également configurer les propriétés de la machine virtuelle dans la console VMM.
	- Toutes les machines virtuelles affichées ont été activées pour la protection. La liste inclut à la fois les machines virtuelles qui sont activées pour la protection et où la réplication initiale est terminée, et celles qui sont activées pour la protection avec réplication initiale en attente. Seules les machines virtuelles avec réplication initiale terminée peuvent basculer dans le cadre d'un plan de récupération. 


	![Créer un plan de récupération](./media/site-recovery-vmm-to-azure/select-rp.png)

Une fois qu’un plan de récupération a été créé, il apparaît dans la liste sous l’onglet **Plans de récupération**. Vous pouvez également ajouter des [Runbooks Azure Automation](site-recovery-runbook-automation.md) au plan de récupération pour automatiser les actions au cours du basculement.

### Exécution d’un test de basculement

Il existe deux manières d’exécuter un test de basculement vers Azure.

- **Tester le basculement sans réseau Azure**: ce type de test de basculement vérifie que la machine virtuelle s’affiche correctement dans Azure. La machine virtuelle ne sera connectée à aucun réseau Azure après le basculement.
- **Tester le basculement avec un réseau Azure**: ce type de basculement vérifie que l’ensemble de l’environnement de réplication s’affiche comme prévu et que les machines virtuelles seront connectées au réseau Azure cible spécifié après le basculement. Pour la gestion de sous-réseau, pour le test de basculement le sous-réseau de la machine virtuelle test sera déterminé en fonction du sous-réseau de la machine virtuelle de réplication. Ceci diffère de la réplication normale, selon laquelle le sous-réseau d’une machine virtuelle de réplication est basé sur le sous-réseau de la machine virtuelle source.

Si vous souhaitez exécuter un test de basculement pour une machine virtuelle activée pour la protection dans Azure sans spécifier de réseau Azure cible, vous n’avez pas besoin de rien préparer. Pour exécuter un test de basculement avec un réseau Azure cible, vous devez créer un réseau Azure isolé de votre réseau de production Azure (comportement par défaut quand vous créez un réseau dans Azure). Découvrez comment [exécuter un test de basculement](site-recovery-failover.md#run-a-test-failover) pour plus de détails.


Vous devrez également configurer l’infrastructure pour que la machine virtuelle répliquée fonctionne comme prévu. Par exemple, une machine virtuelle sur laquelle le contrôleur de domaine et DNS sont installés peut être répliquée sur Microsoft Azure via Microsoft Azure Site Recovery et peut être créée dans le réseau de test, via le test de basculement. Consultez la rubrique [Considérations relatives au test de basculement](site-recovery-active-directory.md#considerations-for-test-failover) pour plus de détails.

Pour exécuter un test de basculement, procédez comme suit :

1. Dans l'onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
2. Sur la page **Confirmer le test de basculement**, sélectionnez **Aucun** ou un réseau Microsoft Azure spécifique. Notez que si vous sélectionnez Aucun, le test de basculement vérifie que la machine virtuelle a été répliquée correctement dans Azure, mais il ne vérifie pas la configuration de votre réseau de réplication.

	![Pas de réseau](./media/site-recovery-vmm-to-azure/test-no-network.png)

3. Si le chiffrement des données est activé pour le cloud, dans **Clé de chiffrement**, sélectionnez le certificat émis pendant l’installation du fournisseur sur le serveur VMM au moment où vous avez activé le chiffrement des données pour un cloud.
4. Sur l’onglet **Tâches**, vous pouvez suivre la progression du basculement. Vous devriez aussi pouvoir voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
5. Lorsque le basculement atteint la phase **Terminer le test**, cliquez sur **Terminer le test** pour finir l’opération. Sous l’onglet **Tâches**, vous pouvez suivre la progression et l’état du basculement et effectuer toutes les actions nécessaires.
6. Après le basculement, vous pouvez voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle. Effectuez les actions suivantes :

    1. Vérifiez que les machines virtuelles démarrent correctement.
    2. Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement. Vous devrez également ajouter un point de terminaison RDP sur la machine virtuelle. Pour cela, vous pouvez utiliser les [Runbooks de Microsoft Azure Automation](site-recovery-runbook-automation.md).
    3. Une fois le basculement effectué, si vous utilisez une adresse IP publique pour vous connecter à la machine virtuelle dans Microsoft Azure via le Bureau à distance, vérifiez qu’aucune stratégie du domaine ne vous empêche de vous connecter à une machine virtuelle au moyen d’une adresse publique.

7.  Une fois le test effectué, procédez comme suit :
	- Cliquez sur **Le test de basculement est terminé**. Nettoyez l’environnement de test afin qu’il mette hors tension et supprime automatiquement les machines virtuelles de test.
	- Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.

## Étapes suivantes

En savoir plus sur la [configuration des plans de récupération](site-recovery-create-recovery-plans.md) et sur le [basculement](site-recovery-failover.md).

<!---HONumber=AcomDC_0316_2016-->