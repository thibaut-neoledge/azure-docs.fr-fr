<properties
	pageTitle="Répliquer des machines virtuelles Hyper-V dans un cloud VMM sur un site secondaire avec Azure Site Recovery à l’aide d’un SAN | Microsoft Azure"
	description="Cet article décrit comment répliquer des machines virtuelles Hyper-V entre deux sites avec Azure Site Recovery à l’aide de la réplication SAN."
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
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Répliquer des machines virtuelles Hyper-V dans un cloud VMM sur un site secondaire avec Azure Site Recovery à l’aide d’un SAN

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

## Vue d'ensemble

Cet article décrit comment déployer Site Recovery pour orchestrer et automatiser la protection des machines virtuelles Hyper-V situées dans des clouds privés System Center Virtual Machine Manager (VMM). Dans ce scénario, les machines virtuelles sont répliquées depuis un site VMM principal sur un site VMM secondaire à l’aide de Site Recovery et d’une réplication SAN.

Cet article fournit une vue d'ensemble et décrit les conditions requises pour le déploiement. Il détaille la configuration et l'activation de la réplication dans VMM et dans le coffre Site Recovery. Vous allez découvrir et classer le stockage SAN dans VMM, approvisionner des LUN et allouer de l'espace de stockage aux clusters Hyper-V. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Pourquoi effectuer une réplication avec un SAN ?

Voici les caractéristiques de ce scénario :

- Fournit une solution de réplication évolutive et automatisée par Site Recovery.
- Tirer parti des fonctionnalités de réplication SAN fournies par des partenaires de stockage d'entreprise sur les stockages Fibre Channel et iSCSI. Découvrez nos [partenaires de stockage SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
- Tire parti de votre infrastructure SAN pour protéger les applications critiques déployées dans des clusters Hyper-V.
- Prend en charge les clusters invités.
- Garantit la cohérence de la réplication entre les différentes couches d’une application avec réplication synchronisée pour un RTO et une RPO faibles, et une réplication asynchronisée pour une haute flexibilité, selon les capacités des groupes de stockage.  
- L’intégration à VMM fournit une gestion SAN dans la console VMM et SMI-S dans VMM détecte un stockage existant.  

## Architecture

Ce scénario protège vos charges de travail en sauvegardant les machines virtuelles Hyper-V à partir d'un site VMM local sur un autre à l'aide de la réplication SAN.

![Architecture SAN](./media/site-recovery-vmm-san/architecture.png)

Les composants inclus dans ce scénario sont les suivants :

- **Machines virtuelles locales** : vos serveurs Hyper-V locaux gérés dans des clouds privés VMM contiennent des machines virtuelles que vous souhaitez protéger.
- **Serveurs VMM locaux** : vous pouvez avoir un ou plusieurs serveurs VMM sur le site primaire que vous souhaitez protéger et sur le site secondaire.
- **Stockage SAN** : un groupe SAN sur le site primaire et un sur le site secondaire.
-  **Coffre Azure Site Recovery** : le coffre coordonne et orchestre le réplica, le basculement et la récupération des données entre vos sites locaux.
- **Fournisseur Azure Site Recovery** : le fournisseur est installé sur chaque serveur VMM.

## Avant de commencer

Assurez-vous que les conditions préalables sont remplies :

**Configuration requise** | **Détails** 
--- | ---
**Microsoft Azure**| Vous aurez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. 
**VMM** | Vous devez disposer d’au moins un serveur VMM déployé comme serveur autonome physique ou virtuel, ou comme cluster virtuel. <br/><br/>Le serveur VMM doit exécuter System Center 2012 R2 avec les dernières mises à jour cumulatives.<br/><br/>Vous devez disposer d’au moins un cloud configuré sur le serveur VMM principal à protéger et d’un cloud configuré sur le serveur VMM secondaire à utiliser pour la protection et la récupération.<br/><br/>Le cloud source à protéger doit contenir un ou plusieurs groupes hôtes VMM.<br/><br/>Tous les clouds VMM doivent avoir le profil Capacité Hyper-V défini.<br/><br/>Pour plus d’informations sur la configuration de clouds VMM, consultez [Configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) et [Procédure pas à pas : création de clouds privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
**Hyper-V** | Vous avez besoin d’un ou plusieurs clusters Hyper-V dans les sites principaux et secondaires et d’une ou plusieurs machines virtuelles sur le cluster Hyper-V source. Les groupes hôtes VMM situés à des emplacements principaux et secondaires doivent chacun comporter un ou plusieurs clusters Hyper-V.<br/><br/>Les serveurs Hyper-V hôte et cible doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour installées.<br/><br/>Tout serveur Hyper-V contenant des machines virtuelles à protéger doit être dans un cloud VMM.<br/><br/>Si vous exécutez Hyper-V dans un cluster, le service Broker de cluster n’est pas créé automatiquement si le cluster est basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. [En savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) sur l’entrée de blog d’Aidan Finn.
**Stockage SAN** | À l’aide de la réplication SAN, vous pouvez répliquer des machines virtuelles en cluster invité avec un stockage iSCSI ou Fibre Channel, ou à l’aide de disques durs virtuels (VHDX) partagés.<br/><br/>Vous avez besoin des deux groupes SAN configurés, l’un dans le site principal et l’autre dans le site secondaire.<br/><br/>Une infrastructure réseau doit être configurée entre les groupes. L'homologation et la réplication doivent être configurées. Des licences de réplication doivent être configurées conformément aux exigences des baies de stockage.<br/><br/>La mise en réseau doit être configurée entre les serveurs hôtes Hyper-V et la baie de stockage pour que les hôtes puissent communiquer avec les LUN de stockage par le biais d’iSCSI ou de Fibre Channel.<br/><br/> Consultez la liste des [groupes de stockage pris en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/>Les fournisseurs SMI-S, proposés par les fabricants de groupes de stockage, doivent être installés, et les groupes SAN doivent être gérés par le fournisseur. Configurez le fournisseur conformément à sa documentation.<br/><br/>Vérifiez que le fournisseur SMI-S pour le groupe se trouve sur un serveur auquel le serveur VMM peut accéder par le biais du réseau au moyen d’une adresse IP ou du nom de domaine complet.<br/><br/>Chaque groupe SAN doit avoir un ou plusieurs pools de stockage disponibles dans ce déploiement. Le serveur VMM sur le site primaire doit gérer le groupe primaire, tandis que le serveur VMM secondaire gère le groupe secondaire.<br/><br/>Le serveur VMM sur le site primaire doit gérer le groupe primaire, tandis que le serveur VMM secondaire doit gérer le groupe secondaire.
**Mappage réseau** | Vous pouvez configurer le mappage réseau pour vous assurer que les machines virtuelles répliquées sont placées de manière optimale sur les serveurs hôtes Hyper-V secondaires après le basculement et qu’elles peuvent se connecter aux réseaux de machines virtuelles appropriés. Si vous ne configurez pas de mappage réseau, les machines virtuelles de réplicas ne sont connectées à aucun réseau après le basculement.<br/><br/>Pour configurer le mappage réseau pendant le déploiement, assurez-vous que les machines virtuelles sur le serveur hôte Hyper-V source sont connectées à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.<br/<br/>Le cloud cible sur le serveur VMM secondaire que vous utilisez pour la récupération doit avoir un réseau de machines virtuelles correspondant configuré, qui lui-même doit être lié à un réseau logique correspondant associé au cloud cible.<br/><br/>[En savoir plus](site-recovery-network-mapping.md) sur le mappage réseau.


## Étape 1 : Préparer l'infrastructure VMM

Pour préparer votre infrastructure VMM, vous devez :

1. Vérifier que les clouds VMM sont configurés
2. Intégrez et classez le stockage SAN dans VMM.
3. Créer des LUN et allouer de l'espace de stockage
4. Créer des groupes de réplication
5. Configurer les réseaux de machines virtuelles

### Vérifier que les clouds VMM sont configurés

Site Recovery orchestre la protection des machines virtuelles situées sur des serveurs hôtes Hyper-V dans des clouds VMM. Vous devez vérifier que ces clouds sont configurés correctement avant de commencer le déploiement de Site Recovery. Pour en savoir plus, consultez [Création de clouds privés](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) dans le blog de Keith Mayer.

### Intégrez et classez le stockage SAN dans VMM.

Ajoutez une classification SAN dans la console VMM :

1. Dans l'espace de travail **Structure**, cliquez sur **Stockage**. Cliquez sur **Accueil** > **Ajouter des ressources** > **Périphériques de stockage** pour démarrer l'Assistant Ajout de périphériques de stockage.
2. Dans **Sélectionner un type de fournisseur de stockage**, sélectionnez **Périphériques SAN et NAS découverts et gérés par un fournisseur SMI-S**.

	![Type de fournisseur](./media/site-recovery-vmm-san/provider-type.png)

3. Dans la page **Spécifier le protocole et l'adresse du fournisseur SMI-S de stockage**, sélectionnez **CIMXML SMI-S** et spécifiez les paramètres de connexion au fournisseur.
4. Dans **Adresse IP ou nom de domaine complet du fournisseur** et **Port TCP/IP**, spécifiez les paramètres de connexion au fournisseur. Vous pouvez uniquement utiliser une connexion SSL pour CIMXML SMI-S.

	![Connexion au fournisseur](./media/site-recovery-vmm-san/connect-settings.png)

5. Dans **Compte d'identification**, spécifiez un compte d'identification VMM ayant accès au fournisseur ou créez un compte.
6. Dans la page **Rechercher des informations**, VMM tente de détecter et d'importer automatiquement les informations des périphériques de stockage. Pour relancer la recherche, cliquez sur **Analyser le fournisseur**. Si le processus de découverte réussit, les baies de stockage découvertes, les pools de stockage, le fabricant, le modèle et la capacité sont indiqués dans la page.

	![Détecter le stockage](./media/site-recovery-vmm-san/discover.png)

7. Dans **Sélectionner les pools de stockage à mettre sous gestion et affecter une classification**, sélectionnez les pools de stockage gérés par VMM et affectez-leur une classification. Les informations sur les LUN sont importées des pools de stockage. Créez des LUN en fonction des applications que vous devez protéger, de leurs besoins en matière de capacité et des éléments que vous devez répliquer ensemble.

	![Classifier le stockage](./media/site-recovery-vmm-san/classify.png)

### Créer des LUN et allouer de l'espace de stockage

1. Une fois le stockage SAN intégré à VMM, vous créez (approvisionnez) des LUN :

	- [Comment sélectionner une méthode de création d'unités logiques dans VMM](https://technet.microsoft.com/library/gg610624.aspx)
	- [Comment approvisionner les unités logiques de stockage dans VMM](https://technet.microsoft.com/library/gg696973.aspx)

2. Ensuite, allouez de l'espace de stockage au cluster hôte Hyper-V pour que VMM puisse déployer les données des machines virtuelles vers le stockage approvisionné :

	- Avant d'allouer de l'espace de stockage au cluster, vous devez en allouer au groupe hôte VMM sur lequel réside le cluster. Consultez [Comment allouer des unités logiques de stockage à un groupe hôte](https://technet.microsoft.com/library/gg610686.aspx) et [Comment faire pour affecter des pools de stockage à un groupe hôte](https://technet.microsoft.com/library/gg610635.aspx).</a>.
	- Allouez une capacité de stockage au cluster comme décrit dans [Comment configurer du stockage sur un cluster hôte Hyper-V dans VMM](https://technet.microsoft.com/library/gg610692.aspx).</a>.

### Créer des groupes de réplication

Créez un groupe de réplication qui recense tous les LUN devant être répliqués ensemble.

1. Dans la console VMM, ouvrez l'onglet **Groupes de réplication** des propriétés du groupe de stockage, puis cliquez sur **Nouveau**.
2. Puis, créez le groupe de réplication.

	![Groupe de réplication SAN](./media/site-recovery-vmm-san/rep-group.png)

### Configurer des réseaux

Si vous souhaitez configurer le mappage réseau, procédez comme suit :

1. Pour en savoir plus sur le mappage réseau, [cliquez ici](site-recovery-network-mapping.md).
2. Préparez les réseaux de machines virtuelles dans VMM :

	- [Configurez les réseaux logiques](https://technet.microsoft.com/library/jj721568.aspx).
	- [Configurez les réseaux de machines virtuelles](https://technet.microsoft.com/library/jj721575.aspx).

## Étape 2 : Créer un coffre


1. Connectez-vous au [Portail de gestion](https://portal.azure.com) à partir du serveur VMM à inscrire.

2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.

3. Cliquez sur **Créer nouveau** > **Création rapide**.

4. Dans **Name**, entrez un nom convivial pour identifier le coffre.

5. Dans **Région**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section Disponibilité géographique dans la page [Détails sur la tarification d'Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

6. Cliquez sur **Create vault**.

	![Nouveau coffre](./media/site-recovery-vmm-san/create-vault.png)

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** dans la page **Recovery Services**.


### Enregistrer les serveurs VMM

1. Ouvrez la page de démarrage rapide depuis la page **Recovery Services**. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Icône Quick Start](./media/site-recovery-vmm-san/quick-start-icon.png)

2. Dans la liste déroulante, sélectionnez **Entre le site local Hyper-V à l'aide de la réplication de groupe**.

	![Clé d'enregistrement](./media/site-recovery-vmm-san/select-san.png)


3. Dans **Préparer les serveurs VMM**, téléchargez la dernière version du fichier d’installation du fournisseur Azure Site Recovery.
4. Exécutez ce fichier sur le serveur VMM source. Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois, installez-le sur un nœud actif et terminez l'installation pour inscrire le serveur VMM dans le coffre. Ensuite, installez le fournisseur sur les autres nœuds. Notez que si vous mettez à niveau le fournisseur, vous devez le mettre à niveau sur tous les nœuds, car ils doivent tous exécuter la même version du fournisseur.
5. Le programme d’installation effectue quelques **vérifications de la configuration requise** et demande l’autorisation d’arrêter le service VMM pour commencer l’installation du fournisseur. Le service VMM redémarre automatiquement une fois l’installation terminée. Si vous installez sur un cluster VMM, vous serez invité à arrêter le rôle de cluster.
6. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

	![Mises à jour Microsoft](./media/site-recovery-vmm-san/ms-update.png)

7. L’emplacement de l’installation est défini sur **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Cliquez sur le bouton Installer pour démarrer l’installation du fournisseur.

	![Emplacement de l’installation](./media/site-recovery-vmm-san/install-location.png)

8. Une fois le fournisseur installé, cliquez sur le bouton « Register » pour enregistrer le serveur dans le coffre.

	![Installation terminée](./media/site-recovery-vmm-san/install-complete.png)

9. Sur la page **Connexion Internet**, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Sélectionnez **Utiliser les paramètres proxy par défaut du système** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

	![Paramètres Internet](./media/site-recovery-vmm-san/proxy-details.png)

	- Si vous souhaitez utiliser un proxy personnalisé, vous devez le configurer avant d'installer le fournisseur. Quand vous configurez les paramètres de proxy personnalisé, un test s'exécute pour vérifier la connexion proxy.
	- Si vous n'utilisez pas de proxy personnalisé ou si votre proxy par défaut nécessite une authentification, vous devez saisir les détails du proxy, y compris l'adresse du proxy et le port.
	- Les URL suivantes doivent être accessibles à partir du serveur VMMet des hôtes Hyper-V :
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Autorisez les adresses IP décrites dans la zone [Étendues d’adresses IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443). Vous devez autoriser les plages IP de la région Microsoft Azure que vous prévoyez d’utiliser, ainsi que celles de la région ouest des États-Unis.
	- Si vous utilisez un proxy personnalisé, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d'identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs VMM dans la console VMM. Pour cela, ouvrez l'espace de travail Paramètres, développez Sécurité, cliquez sur Comptes d'identification, puis modifiez le mot de passe de DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.

10. Dans **Registration Key**, indiquez que vous téléchargez depuis Azure Site Recovery et que vous copiez sur le serveur VMM.
11. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré. 

	![Enregistrement du serveur](./media/site-recovery-vmm-san/vault-creds.png)

12. Ce paramètre de chiffrement est utilisé uniquement pour le scénario VMM vers Azure. Dans le cadre d’une utilisation VMM vers VMM uniquement, vous pouvez ignorer cet écran.

	![Enregistrement du serveur](./media/site-recovery-vmm-san/encrypt.png)

13. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
14. Dans **Synchronisation initiale des métadonnées du cloud**, indiquez le nom convivial du serveur qui apparaît dans le coffre, puis indiquez si vous voulez synchroniser les métadonnées de tous les clouds sur le serveur VMM avec le coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.

	![Enregistrement du serveur](./media/site-recovery-vmm-san/friendly-name.png)

15. Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l’onglet *Serveurs VMM* de la page **Serveurs** du coffre.

### Installation à partir de la ligne de commande

Le fournisseur Azure Site Recovery peut également être installé à l’aide de la ligne de commande suivante. Cette méthode peut être utilisée pour installer le fournisseur sur un module Server CORE pour Windows Server 2012 R2.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription vers un dossier, par exemple C:\\ASR.
2. Arrêter le service System Center Virtual Machine Manager
3. Extrayez le programme d’installation du fournisseur en exécutant les commandes ci-après à partir d’une invite de commandes avec des privilèges d’**administrateur** :

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installez le fournisseur en exécutant la commande suivante :

		C:\ASR> setupdr.exe /i

5. Inscrivez le fournisseur en exécutant la commande suivante :

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Où les paramètres sont :

 - **/Credentials** : paramètre obligatoire, qui spécifie l’emplacement auquel le fichier de clé d’inscription se trouve  
 - **/FriendlyName** : paramètre obligatoire, qui correspond au nom du serveur hôte Hyper-V qui s’affiche sur le portail Microsoft Azure Site Recovery
 - **/EncryptionEnabled** : paramètre facultatif que vous ne devez utiliser que dans le scénario VMM vers Azure si vos machines virtuelles doivent être chiffrées au repos dans Azure. Vérifiez que le nom du fichier que vous fournissez porte l'extension **.pfx**.
 - **/proxyAddress** : paramètre facultatif qui spécifie l’adresse du serveur proxy
 - **/proxyport** : paramètre facultatif qui spécifie le port du serveur proxy
 - **/proxyUsername** : paramètre facultatif qui spécifie le nom d’utilisateur proxy (si le proxy nécessite une authentification)
 - **/proxyPassword** : paramètre facultatif qui spécifie le mot de passe pour l’authentification auprès du serveur proxy (si le proxy nécessite une authentification)


## Étape 3 : Mapper les groupes et pools de stockage

Mappez les groupes pour spécifier le pool de stockage secondaire censé recevoir les données de réplication du pool primaire. Vous devez mapper le stockage avant de configurer la protection du cloud, car les informations de mappage sont utilisées quand vous activez la protection des groupes de réplication.

Avant de commencer, vérifiez que les clouds apparaissent bien dans le coffre. Pour détecter les clouds, vous pouvez soit synchroniser tous les clouds quand vous installez le fournisseur, soit synchroniser un cloud spécifique dans l'onglet **Général** des propriétés du cloud dans la console VMM. Ensuite, mappez les baies de stockage comme suit :

1. Cliquez sur **Ressources** > **Stockage serveur** > **Mapper les baies de stockage source et cible**. ![Enregistrement du serveur](./media/site-recovery-vmm-san/storage-map.png)
2. Sélectionnez les baies de stockage dans le site principal et mappez-les aux baies de stockage dans le site secondaire.
3.  Mappez les pools de stockage source et cible dans les groupes. Pour ce faire, dans **Pools de stockage**, sélectionnez un pool de stockage source et un pool de stockage cible à mapper.

	![Enregistrement du serveur](./media/site-recovery-vmm-san/storage-map-pool.png)

## Étape 4 : Configuration des paramètres de protection de cloud

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Vous avez activé l'option **Synchroniser les données du cloud avec le coffre** lors de l'installation du fournisseur. Ainsi, tous les clouds du serveur VMM s’affichent dans l'onglet <b>Éléments protégés</b> du coffre.

![Cloud publié](./media/site-recovery-vmm-san/clouds-list.png)

1. Sur la page Démarrage rapide, cliquez sur **Set up protection for VMM clouds**.
2. Sous l'onglet **Protected Items**, sélectionnez le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**. Notez les points suivants :
3. Dans <b>Cible</b>, sélectionnez <b>VMM</b>.
4. Dans <b>Emplacement cible</b>, sélectionnez le serveur VMM local qui gère le cloud à utiliser pour la récupération.
5. Dans <b>Cloud cible</b>, sélectionnez le cloud cible à utiliser pour le basculement des machines virtuelles du cloud source. Notez les points suivants :
	- Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
	- Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.
6. Azure Site Recovery vérifie que les clouds ont accès au stockage compatible avec la réplication SAN et que les baies de stockage sont homologuées. Les groupes homologues participant sont indiqués.
7. Si la vérification réussit, sélectionnez **SAN** dans **Type de réplication**.

<p>Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet <b>Jobs</b>. Les paramètres de cloud peuvent être modifiés sous l'onglet <b>Configure</b>. Pour modifier l'emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.</p>

## Étape 5 : Activer le mappage réseau

1. Dans la page Démarrage rapide, cliquez sur **Mapper les réseaux**.
2. Sélectionnez le serveur VMM source à partir duquel vous souhaitez mapper des réseaux, puis le serveur VMM cible auquel les réseaux seront mappés. La liste des réseaux sources et leurs réseaux cibles associés s'affiche à l'écran. Une valeur vide est indiquée pour les réseaux qui ne sont pas mappés. Cliquez sur l'icône d'information en regard du nom des réseaux source et cible pour afficher les sous-réseaux de chaque réseau.
3. Sélectionnez un réseau dans **Réseau sur la source**, puis cliquez sur **Mapper**. Le service détecte les réseaux de machines virtuelles sur le serveur cible et les affiche.

	![Architecture SAN](./media/site-recovery-vmm-san/network-map1.png)

4. Dans la boîte de dialogue, sélectionnez l'un des réseaux de machines virtuelles du serveur VMM cible.

	![Architecture SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Quand vous sélectionnez un réseau cible, les clouds protégés qui utilisent le réseau source sont affichés. Les réseaux cibles disponibles qui sont associés aux clouds utilisés pour la protection sont également affichés. Nous vous recommandons de sélectionner un réseau cible qui est accessible à tous les clouds utilisés pour la protection.
6.  Cliquez sur la coche pour terminer le processus de mappage. Une tâche de suivi du processus de mappage démarre. Vous pouvez l'afficher dans l'onglet **Tâches**.


## Étape 6 : activer la réplication des groupes de réplication</h3>

Avant d'activer la protection des machines virtuelles, vous devez activer la réplication des groupes de réplication de stockage.

1. Dans le portail Azure Site Recovery, dans la page des propriétés du cloud primaire, ouvrez l'onglet **Machines virtuelles**. Cliquez sur **Ajouter un groupe de réplication**.
2. Sélectionnez un ou plusieurs groupes de réplication VMM associés au cloud, vérifiez les groupes source et cible, puis spécifiez la fréquence de réplication.

Une fois cette opération terminée, Azure Site Recovery, VMM et les fournisseurs SMI-S approvisionnent les LUN de stockage du site cible et activent la réplication du stockage. Si le groupe de réplication est déjà répliqué, Azure Site Recovery réutilise la relation de réplication existant et met à jour les informations dans Azure Site Recovery.

## Étape 7 : Activation de la protection des machines virtuelles

Au terme de la réplication de la baie de stockage, activez la protection des machines virtuelles dans la console VMM à l'aide de l'une des méthodes suivantes :

- **Nouvelle machine virtuelle** : dans la console VMM, lorsque vous créez une machine virtuelle, vous activez la protection Azure Site Recovery et associez la machine virtuelle au groupe de réplication. Si vous choisissez cette option, VMM place de manière optimale le stockage des machines virtuelles sur les LUN du groupe de réplication. Azure Site Recovery orchestre la création d'une machine virtuelle masquée sur le site secondaire et alloue de l'espace de stockage pour que les machines virtuelles de réplication puissent être démarrées après le basculement.
- **Ordinateur virtuel existant** : si un ordinateur virtuel est déjà déployé dans VMM, vous pouvez activer la protection Azure Site Recovery et effectuer une migration du stockage vers un groupe de réplication. Au terme de l'opération, VMM et Azure Site Recovery détectent la nouvelle machine virtuelle et commencent à la gérer dans Azure Site Recovery à des fins de protection. Une machine virtuelle masquée est créée sur le site secondaire et un espace de stockage est alloué pour que la machine virtuelle de réplication puisse être démarrée après le basculement.

	![Activer la protection](./media/site-recovery-vmm-san/enable-protect.png)

Une fois la protection des machines virtuelles activée, les machines virtuelles apparaissent dans la console Azure Site Recovery. Vous pouvez afficher les propriétés des machines virtuelles, suivre leur état et basculer des groupes de réplication contenant plusieurs machines virtuelles. Notez que dans le cadre de la réplication SAN, toutes les machines virtuelles associées à un groupe de réplication doivent basculer ensemble. Cela vient du fait que le basculement se produit d'abord au niveau de la couche de stockage. Il est important de regrouper vos groupes de réplication correctement et de placer uniquement les machines virtuelles associées ensemble.

Vous pouvez suivre la progression de l’action d’activation de la protection, y compris la réplication initiale, sous l’onglet **Tâches**. Lorsque la tâche de finalisation de la protection s’exécute, la machine virtuelle est prête à être basculée.

![Tâche de protection de la machine virtuelle](./media/site-recovery-vmm-san/job-props.png)

## Étape 8 : tester le déploiement

Pour vous assurer que les machines virtuelles et les données basculent comme prévu, testez votre déploiement. Pour ce faire, créez un plan de récupération en sélectionnant des groupes de réplication. Ensuite, exécutez un test de basculement sur le plan.

1. Dans l'onglet **Plans de récupération**, cliquez sur **Créer un plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM source et cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source. Sélectionnez **SAN** pour n’afficher que les clouds configurés pour la réplication SAN. 3. ![Créer un plan de récupération](./media/site-recovery-vmm-san/r-plan.png)

4. Dans **Sélectionner la machine virtuelle**, sélectionnez les groupes de réplication. Toutes les machines virtuelles associées au groupe de réplication sont sélectionnées et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutées au groupe par défaut du plan de récupération, à savoir le groupe 1. Vous pouvez ajouter d'autres groupes si nécessaire. Notez qu’après la réplication, les machines virtuelles démarrent dans l’ordre des groupes du plan de récupération.

	![Ajouter des machines virtuelles](./media/site-recovery-vmm-san/r-plan-vm.png)
5. Une fois un plan de récupération créé, il s’affiche dans la liste de l'onglet **Plans de récupération**.
6. Dans l'onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
7. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Si cette option est activée, les machines virtuelles de réplication basculées ne sont connectées à aucun réseau. Le test vérifie que la machine virtuelle bascule comme prévu, mais il ne vérifie pas l'environnement de votre réseau de réplication. Découvrez comment [exécuter un test de basculement](site-recovery-failover.md#run-a-test-failover) pour plus d’informations sur l’utilisation des différentes options de réseau.


	![Sélectionner le réseau de test](./media/site-recovery-vmm-san/test-fail1.png)


8. La machine virtuelle de test est créée sur le même hôte que celui sur lequel la machine virtuelle de réplication existe. Elle n'est pas ajoutée au cloud où se trouve la machine virtuelle de réplication.
9. Après la réplication, la machine virtuelle de réplication possède une adresse IP différente de celle de la machine virtuelle principale. Si vous émettez des adresses à partir de DHCP, DNS est mis à jour automatiquement. Si vous n'utilisez pas DHCP et que vous souhaitez vous assurer que les adresses sont identiques, vous devez exécuter quelques scripts.
10. Exécutez cet exemple de script pour récupérer l'adresse IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. Exécutez cet exemple de script pour mettre à jour DNS, en spécifiant l'adresse IP que vous avez récupérée à l'aide de l'exemple de script précédent.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Étapes suivantes

Après avoir exécuté un test de basculement pour vérifier que votre environnement fonctionne comme prévu, [découvrez](site-recovery-failover.md) les différents types de basculement.

<!---HONumber=AcomDC_0218_2016-->