<properties 
	pageTitle="Configurer la protection entre des sites VMM locaux avec la réplication SAN" 
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles Hyper-V entre les sites locaux en utilisant la réplication SAN." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="raynew"/>

# Configurer la protection entre des sites VMM locaux avec la réplication SAN

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Pour en savoir plus sur les scénarios de déploiement, consultez [Présentation d’Azure Site Recovery](site-recovery-overview.md).

Cette procédure explique comment déployer Azure Site Recovery pour orchestrer et automatiser la protection des charges de travail d’ordinateur virtuel sur un serveur Hyper-V local géré par System Center VMM vers un autre site VMM local, à l'aide de la réplication basée sur des groupes de stockage (SAN).

Les avantages pour l'entreprise sont les suivants :

- Fournir une solution de réplication évolutive et automatisée par Site Recovery.
- Tirer parti des fonctionnalités de réplication SAN fournies par des partenaires de stockage d'entreprise sur les stockages Fibre Channel et iSCSI. Découvrez nos [partenaires de stockage SAN](http://go.microsoft.com/fwlink/?LinkId=518669).
- Tirer parti de votre infrastructure SAN pour protéger les applications critiques déployées dans des clusters Hyper-V. 
- Prendre en charge les clusters invités.
- Garantir la cohérence de la réplication entre les différentes couches d’une application avec réplication synchronisée SAN pour un RTO et une RPO faibles, et une réplication synchronisée pour une haute flexibilité, selon les capacités des groupes de stockage.  
- L'intégration dans VMM fournit une gestion SAN dans la console VMM et SMI-S dans VMM détecte un stockage existant.  


## Dans cet article

Cet article fournit une vue d'ensemble et décrit les conditions requises pour le déploiement. Il détaille la configuration et l'activation de la réplication dans VMM et dans le coffre Site Recovery. Vous allez découvrir et classer le stockage SAN dans VMM, approvisionner des LUN et allouer de l'espace de stockage aux clusters Hyper-V. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes, posez vos questions sur le [Forum Azure Recovery Services](http://go.microsoft.com/fwlink/?LinkId=313628).

## Vue d'ensemble
Ce scénario protège vos charges de travail en sauvegardant les ordinateurs virtuels Hyper-V à partir d'un site VMM local sur un autre à l'aide de la réplication SAN.

![Architecture SAN](./media/site-recovery-vmm-san/ASRSAN_Arch.png)

### Composants du scénario

- **Ordinateurs virtuels locaux** : vos serveurs Hyper-V locaux gérés dans des clouds privés VMM contiennent des ordinateurs virtuels que vous souhaitez protéger.
- **Serveurs VMM locaux** : vous pouvez avoir un ou plusieurs serveurs VMM sur le site primaire que vous souhaitez protéger et sur le site secondaire.
- **Stockage SAN** : un groupe SAN sur le site primaire et un sur le site secondaire.
-  **Coffre Azure Site Recovery** : le coffre coordonne et orchestre le réplica, le basculement et la récupération des données entre vos sites locaux.
- **Fournisseur Azure Site Recovery** : le fournisseur est installé sur chaque serveur VMM.

## Avant de commencer

### Conditions préalables pour Azure

- Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](http://aka.ms/try-azure). Pour plus d'informations sur la tarification, consultez la page [Détails des prix d'Azure Site Recovery Manager](http://go.microsoft.com/fwlink/?LinkId=378268).

### Configuration requise pour VMM

- Vous devez disposer d'au moins un serveur VMM sur chaque site local, déployé en tant que serveur autonome physique ou virtuel ou en tant que cluster virtuel, sur System Center 2012 R2 avec le [Correctif cumulatif VMM 5.0](http://go.microsoft.com/fwlink/?LinkId=517707) (version préliminaire).
- Vous devez disposer d'au moins un cloud sur le serveur VMM primaire à protéger et d'un autre sur le serveur VMM secondaire. Le cloud principal à protéger doit comporter les éléments suivants :
	- un ou plusieurs groupes hôtes VMM ;
	- un ou plusieurs clusters Hyper-V dans chaque groupe hôte ;
	- une ou plusieurs machines virtuelles situées sur le serveur Hyper-V source dans le cloud.
		
### Configuration requise pour Hyper-V

- Vous devez disposer d'un cluster hôte Hyper-V déployé dans les sites principaux et secondaires, exécutant au moins Windows Server 2012 avec les dernières mises à jour.

### Configuration requise pour la réplication SAN

- La réplication SAN vous permet de répliquer des ordinateurs virtuels en cluster invité avec un stockage iSCSI ou Fibre Channel ou bien de disques durs virtuels partagés (VHDX). La configuration requise pour SAN est la suivante :
	- Vous devez disposer de deux groupes SAN configurés, l'un dans le site principal et l'autre dans le site secondaire.
	- Une infrastructure réseau doit être configurée entre les groupes. L'homologation et la réplication doivent être configurées. Des licences de réplication doivent être configurées conformément aux exigences des baies de stockage.
	- La mise en réseau doit être configurée entre les serveurs hôtes Hyper-V et la baie de stockage pour que les hôtes puissent communiquer avec les LUN de stockage via iSCSI ou Fibre Channel.
	- Consultez la liste des [groupes de stockage pris en charge](http://go.microsoft.com/fwlink/?LinkId=518669).
	- Les fournisseurs SMI-S, proposés par les fabricants de groupes de stockage, doivent être installés, et les groupes SAN doivent être gérés par le fournisseur. Configurez le fournisseur conformément à sa documentation.
	- Vérifiez que le fournisseur SMI-S pour le groupe se trouve sur un serveur auquel le serveur VMM peut accéder via le réseau au moyen d'une adresse IP ou du nom de domaine complet.
	- Chaque groupe SAN doit posséder un ou plusieurs pools de stockage disponibles dans ce déploiement. Le serveur VMM sur le site primaire doit gérer le groupe primaire, tandis que le serveur VMM secondaire gère le groupe secondaire.
	- Le serveur VMM sur le site primaire doit gérer le groupe primaire, tandis que le serveur VMM secondaire doit gérer le groupe secondaire.

### Configuration requise pour le réseau

Si vous le souhaitez, vous pouvez configurer le mappage réseau pour vous assurer que les ordinateurs virtuels de réplica sont placés de manière optimale sur les serveurs hôtes Hyper-V après le basculement et qu’ils peuvent se connecter aux réseaux d’ordinateurs virtuels appropriés. Notez les points suivants :

- Quand le mappage réseau est activé, une machine virtuelle à l'emplacement principal est connectée à un réseau et son réplica à l'emplacement cible est connecté à son réseau mappé.
- Si vous ne configurez pas le mappage réseau, les machines virtuelles ne seront pas connectées aux réseaux VM après le basculement.
- Les réseaux d'ordinateurs virtuels doivent être configurés dans VMM. Pour en savoir plus, consultez Configuration de réseaux d'ordinateurs virtuels et de passerelles dans VMM.
- Les ordinateurs virtuels sur le serveur VMM source doivent être connectés à un réseau d’ordinateurs virtuels. Ce réseau doit être relié à un réseau logique lui-même associé au cloud.


## Étape 1 : Préparer l'infrastructure VMM

Pour préparer votre infrastructure VMM, vous devez :

1. Vérifier que les clouds VMM sont configurés
2. Intégrez et classez le stockage SAN dans VMM.
3. Créer des LUN et allouer de l'espace de stockage
4. Créer des groupes de réplication
5. Configurer les réseaux d'ordinateurs virtuels

### Vérifier que les clouds VMM sont configurés

Site Recovery orchestre la protection des ordinateurs virtuels situés sur des serveurs hôtes Hyper-V dans des clouds VMM. Vous devez vérifier que ces clouds sont configurés correctement avant de commencer le déploiement de Site Recovery. Voici quelques sources fiables :

- [Nouveautés sur le cloud privé](http://go.microsoft.com/fwlink/?LinkId=324952)
- [VMM 2012 et les clouds](http://go.microsoft.com/fwlink/?LinkId=324956) dans le blog de Gunter Danzeisen.
- [Configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn883636.aspx#BKMK_Fabric)
- [Création d'un cloud privé dans VMM](http://go.microsoft.com/fwlink/?LinkId=324953)
- [Procédure pas à pas : création de clouds privés](http://go.microsoft.com/fwlink/?LinkId=324954) dans le blog de Keith Mayer.

### Intégrez et classez le stockage SAN dans VMM.


Ajoutez une classification SAN dans la console VMM :

1. Dans l'espace de travail **Structure**, cliquez sur **Stockage**. Cliquez sur **Accueil** > **Ajouter des ressources** > **Périphériques de stockage** pour démarrer l'Assistant Ajout de périphériques de stockage.
2. Dans **Sélectionner un type de fournisseur de stockage**, sélectionnez **Périphériques SAN et NAS découverts et gérés par un fournisseur SMI-S**.

	![Type de fournisseur](./media/site-recovery-vmm-san/SRSAN_Providertype.png)

3. Dans la page **Spécifier le protocole et l'adresse du fournisseur SMI-S de stockage**, sélectionnez **CIMXML SMI-S** et spécifiez les paramètres de connexion au fournisseur.
4. Dans **Adresse IP ou nom de domaine complet du fournisseur** et **Port TCP/IP**, spécifiez les paramètres de connexion au fournisseur. Vous pouvez uniquement utiliser une connexion SSL pour CIMXML SMI-S.

	![Connexion au fournisseur](./media/site-recovery-vmm-san/SRSAN_ConnectSettings.png)

5. Dans **Compte d'identification**, spécifiez un compte d'identification VMM ayant accès au fournisseur ou créez un compte.
6. Dans la page **Rechercher des informations**, VMM tente de détecter et d'importer automatiquement les informations des périphériques de stockage. Pour relancer la recherche, cliquez sur **Analyser le fournisseur**. Si le processus de découverte réussit, les baies de stockage découvertes, les pools de stockage, le fabricant, le modèle et la capacité sont indiqués dans la page.

	![Détecter le stockage](./media/site-recovery-vmm-san/SRSAN_Discover.png)

7. Dans **Sélectionner les pools de stockage à mettre sous gestion et affecter une classification**, sélectionnez les pools de stockage gérés par VMM et affectez-leur une classification. Les informations sur les LUN sont importées des pools de stockage. Créez des LUN en fonction des applications que vous devez protéger, de leurs besoins en matière de capacité et des éléments que vous devez répliquer ensemble.

	![Classifier le stockage](./media/site-recovery-vmm-san/SRSAN_Classify.png)

### Créer des LUN et allouer de l'espace de stockage

1. Une fois le stockage SAN intégré à VMM, vous créez (approvisionnez) des LUN :

- [Comment sélectionner une méthode de création d'unités logiques dans VMM](http://go.microsoft.com/fwlink/?LinkId=518490)
- [Comment approvisionner les unités logiques de stockage dans VMM](http://go.microsoft.com/fwlink/?LinkId=518491)

2. Ensuite, allouez de l'espace de stockage au cluster hôte Hyper-V pour que VMM puisse déployer les données des machines virtuelles vers le stockage approvisionné : 

	- Avant d'allouer de l'espace de stockage au cluster, vous devez en allouer au groupe hôte VMM sur lequel réside le cluster. Consultez [Comment allouer des unités logiques de stockage à un groupe hôte](http://go.microsoft.com/fwlink/?LinkId=518493) et [Comment faire pour affecter des pools de stockage à un groupe hôte](http://go.microsoft.com/fwlink/?LinkId=518492).</a>.
	- Allouez une capacité de stockage au cluster comme décrit dans [Comment configurer du stockage sur un cluster hôte Hyper-V dans VMM](http://go.microsoft.com/fwlink/?LinkId=513017).</a>.

### Créer des groupes de réplication

Créez un groupe de réplication qui recense tous les LUN devant être répliqués ensemble.

1. Dans la console VMM, ouvrez l'onglet **Groupes de réplication** des propriétés du groupe de stockage, puis cliquez sur **Nouveau**.
2. Puis, créez le groupe de réplication.

	![Groupe de réplication SAN](./media/site-recovery-vmm-san/SRSAN_RepGroup.png)

### Configurer des réseaux

Si vous souhaitez configurer le mappage réseau, procédez comme suit :

1. Pour en savoir plus sur le mappage réseau, [cliquez ici](https://msdn.microsoft.com/library/azure/dn801052.aspx).
2. Préparez les réseaux d'ordinateurs virtuels dans VMM :
 
	- Pour en savoir plus sur la configuration des réseaux logiques, consultez [Présentation de la configuration de réseaux logiques dans VMM](http://go.microsoft.com/fwlink/?LinkId=386307).
	- [Configurez les réseaux d'ordinateurs virtuels](http://go.microsoft.com/fwlink/?LinkId=386308).

## Étape 2 : Créer un coffre


1. Connectez-vous au [portail de gestion](https://portal.azure.com).


2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.


3. Cliquez sur **Créer nouveau** > **Création rapide**.
	
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.

5. Dans **Region**, sélectionnez la région géographique du coffre. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery](href="http://go.microsoft.com/fwlink/?LinkId=389880)

6. Cliquez sur **Create vault**.

	![Nouveau coffre](./media/site-recovery-vmm-san/SRSAN_HvVault.png)

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** dans la page **Recovery Services**.


### Enregistrer les serveurs VMM

1. Dans la page <b>Recovery Services</b>, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Icône Quick Start](./media/site-recovery-vmm-san/SRSAN_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre le site local Hyper-V à l'aide de la réplication de groupe**.

	![Clé d'enregistrement](./media/site-recovery-vmm-san/SRSAN_QuickStartRegKey.png)

3. Dans **Préparer les serveurs VMM**, cliquez sur le fichier **Générer le fichier de clé d'inscription**. Une fois générée, la clé est valide pendant 5 jours. Si vous n'exécutez pas la console sur le serveur VMM que vous souhaitez protéger, téléchargez le fichier dans un emplacement sûr auquel les serveurs VMM ont accès. Par exemple, un partage. Notez les points suivants :

- Si vous installez le fournisseur pour la première fois, installez-le sur un nœud actif et terminez l'installation pour inscrire le serveur VMM dans le coffre Azure Site Recovery. Ensuite, installez le fournisseur sur les autres nœuds du cluster.
- Si vous mettez à niveau la version du fournisseur, lancez l'installation du fournisseur sur tous les nœuds du cluster.

4. Pour obtenir la dernière version du fichier d'installation du fournisseur, cliquez sur **Télécharger le fournisseur Microsoft Azure Site Recovery pour une installation sur les serveurs VMM**.
5. Exécutez ce fichier sur les serveurs VMM source et cible pour ouvrir l'Assistant d'installation du fournisseur Azure Site Recovery.
6. Dans la page **Vérification de la configuration requise**, indiquez d'arrêter le service VMM pour lancer l'installation du fournisseur. Le service s'arrête et redémarre automatiquement une fois l'installation terminée. Si vous procédez à l’installation sur un cluster VMM, vous êtes invité à arrêter le rôle de cluster.

	![Composants requis](./media/site-recovery-vmm-san/SRSAN_ProviderPrereq.png)

5. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

Une fois le fournisseur installé, poursuivez l'installation afin d'enregistrer le serveur dans le coffre.

6. Sur la page Connexion Internet, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Vous pouvez choisir de ne pas utiliser un proxy, d'utiliser le proxy par défaut configuré sur le serveur VMM, ou d’utiliser un serveur proxy personnalisé. Notez les points suivants :

	- Si vous souhaitez utiliser un serveur proxy personnalisé, configurez-le avant d’installer le fournisseur.
	- Les URL suivantes doivent être accessibles à partir du serveur VMM.
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net- Autorisez les adresses IP décrites dans la zone [Étendues d’adresses IP du centre de données Azure](http://go.microsoft.com/fwlink/?LinkId=511094) et le protocole HTTPS (443). Vous devez autoriser les plages IP de la région Azure que vous prévoyez d'utiliser et de l'ouest des États-Unis. 
	
	- Si vous choisissez d’utiliser un proxy personnalisé, un compte d’identification VMM (DRAProxyAccount) est créé automatiquement avec les informations d'identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement.
	- Vous pouvez modifier les paramètres du compte RunAs VMM dans la console VMM. Pour cela, ouvrez l'espace de travail Paramètres, développez Sécurité, cliquez sur Comptes d'identification, puis modifiez le mot de passe de DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.
	- Un test s'exécute pour vérifier la connexion Internet. Les erreurs de proxy s’affichent dans la console VMM.

	![Paramètres Internet](./media/site-recovery-vmm-san/SRSAN_ProviderProxy.png)

7. Dans **Registration Key**, indiquez que vous téléchargez depuis Azure Site Recovery et que vous copiez sur le serveur VMM.
8. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre.

	![Enregistrement du serveur](./media/site-recovery-vmm-san/SRSAN_ProviderRegKeyServerName.png)

9. Dans la synchronisation **Initial cloud metadata**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l'aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM. L'option **Chiffrement des données** ne s'applique pas à ce scénario.

	![Enregistrement du serveur](./media/site-recovery-vmm-san/SRSAN_ProviderSyncEncrypt.png)

11. Dans la page suivante, cliquez sur **Inscrire** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît dans l'onglet <b>Ressources</b> de la page **Serveurs** du coffre. Une fois l’installation installée, modifiez les paramètres du fournisseur dans la console VMM.


## Étape 4 : Mapper les groupes et pools de stockage

Mappez les groupes pour spécifier le pool de stockage secondaire censé recevoir les données de réplication du pool primaire. Vous devez mapper le stockage avant de configurer la protection du cloud, car les informations de mappage sont utilisées quand vous activez la protection des groupes de réplication.

Avant de commencer, vérifiez que les clouds apparaissent bien dans le coffre. Pour détecter les clouds, vous pouvez soit synchroniser tous les clouds quand vous installez le fournisseur, soit synchroniser un cloud spécifique dans l'onglet **Général** des propriétés du cloud dans la console VMM. Ensuite, mappez les baies de stockage comme suit :

1. Cliquez sur **Ressources** > **Stockage serveur** > **Mapper les baies de stockage source et cible**. ![Enregistrement du serveur](./media/site-recovery-vmm-san/SRSAN_StorageMap.png)
2. Sélectionnez les baies de stockage dans le site principal et mappez-les aux baies de stockage dans le site secondaire.
3.  Mappez les pools de stockage source et cible dans les groupes. Pour ce faire, dans **Pools de stockage**, sélectionnez un pool de stockage source et un pool de stockage cible à mapper.

	![Enregistrement du serveur](./media/site-recovery-vmm-san/SRSAN_StorageMapPool.png)

## Étape 5 : Configurer les paramètres de protection de cloud

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Vous avez activé l'option **Synchroniser les données du cloud avec le coffre** lors de l'installation du fournisseur. Ainsi, tous les clouds du serveur VMM s’affichent dans l'onglet <b>Éléments protégés</b> du coffre.

![Cloud publié](./media/site-recovery-vmm-san/SRSAN_CloudsList.png)

1. Sur la page Démarrage rapide, cliquez sur **Set up protection for VMM clouds**.
2. Sous l'onglet **Protected Items**, sélectionnez le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**. Notez les points suivants :
3. Dans <b>Cible</b>, sélectionnez <b>VMM</b>.
4. Dans <b>Emplacement cible</b>, sélectionnez le serveur VMM local qui gère le cloud à utiliser pour la récupération.
5. Dans <b>Cloud cible</b>, sélectionnez le cloud cible à utiliser pour le basculement des ordinateurs virtuels du cloud source. Notez les points suivants :
	- Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
	- Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.
6. Azure Site Recovery vérifie que les clouds ont accès au stockage compatible avec la réplication SAN et que les baies de stockage sont homologuées. Les groupes homologues participant sont indiqués.
7. Si la vérification réussit, sélectionnez **SAN** dans **Type de réplication**.

<p>Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet <b>Jobs</b>. Les paramètres de cloud peuvent être modifiés sous l'onglet <b>Configure</b>. Pour modifier l'emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.</p>

## Étape 6 : Activer le mappage réseau

1. Dans la page Démarrage rapide, cliquez sur **Mapper les réseaux**.
2. Sélectionnez le serveur VMM source à partir duquel vous souhaitez mapper des réseaux, puis le serveur VMM cible auquel les réseaux seront mappés. La liste des réseaux sources et leurs réseaux cibles associés s'affiche à l'écran. Une valeur vide est indiquée pour les réseaux qui ne sont pas mappés. Cliquez sur l'icône d'information en regard du nom des réseaux source et cible pour afficher les sous-réseaux de chaque réseau.
3. Sélectionnez un réseau dans **Réseau sur la source**, puis cliquez sur **Mapper**. Le service détecte les réseaux de machines virtuelles sur le serveur cible et les affiche.

	![Architecture SAN](./media/site-recovery-vmm-san/ASRSAN_NetworkMap1.png)

4. Dans la boîte de dialogue, sélectionnez l'un des réseaux d’ordinateurs virtuels du serveur VMM cible.

	![Architecture SAN](./media/site-recovery-vmm-san/ASRSAN_NetworkMap2.png)

5. Quand vous sélectionnez un réseau cible, les clouds protégés qui utilisent le réseau source sont affichés. Les réseaux cibles disponibles qui sont associés aux clouds utilisés pour la protection sont également affichés. Nous vous recommandons de sélectionner un réseau cible qui est accessible à tous les clouds utilisés pour la protection.
6.  Cliquez sur la coche pour terminer le processus de mappage. Une tâche de suivi du processus de mappage démarre. Vous pouvez l'afficher dans l'onglet **Tâches**.


## Étape 7 : Activer la réplication des groupes de réplication</h3>

Avant d'activer la protection des machines virtuelles, vous devez activer la réplication des groupes de réplication de stockage.

1. Dans le portail Azure Site Recovery, dans la page des propriétés du cloud primaire, ouvrez l'onglet **Ordinateurs virtuels**. Cliquez sur **Ajouter un groupe de réplication**.
2. Sélectionnez un ou plusieurs groupes de réplication VMM associés au cloud, vérifiez les groupes source et cible, puis spécifiez la fréquence de réplication.

Une fois cette opération terminée, Azure Site Recovery, VMM et les fournisseurs SMI-S approvisionnent les LUN de stockage du site cible et activent la réplication du stockage. Si le groupe de réplication est déjà répliqué, Azure Site Recovery réutilise la relation de réplication existant et met à jour les informations dans Azure Site Recovery.

## activer la protection des machines virtuelles

Au terme de la réplication de la baie de stockage, activez la protection des machines virtuelles dans la console VMM à l'aide de l'une des méthodes suivantes :

- **Nouvel ordinateur virtuel** : dans la console VMM, lorsque vous créez un ordinateur virtuel, vous activez la protection Azure Site Recovery et associez l’ordinateur virtuel au groupe de réplication. Si vous choisissez cette option, VMM place de manière optimale le stockage des machines virtuelles sur les LUN du groupe de réplication. Azure Site Recovery orchestre la création d'une machine virtuelle masquée sur le site secondaire et alloue de l'espace de stockage pour que les machines virtuelles de réplication puissent être démarrées après le basculement.
- **Ordinateur virtuel existant** : si un ordinateur virtuel est déjà déployé dans VMM, vous pouvez activer la protection Azure Site Recovery et effectuer une migration du stockage vers un groupe de réplication. Au terme de l'opération, VMM et Azure Site Recovery détectent la nouvelle machine virtuelle et commencent à la gérer dans Azure Site Recovery à des fins de protection. Une machine virtuelle masquée est créée sur le site secondaire et un espace de stockage est alloué pour que la machine virtuelle de réplication puisse être démarrée après le basculement.


	![Activer la protection](./media/site-recovery-vmm-san/SRSAN_EnableProtection.png)
	

<P>Une fois la protection des ordinateurs virtuels activée, ceux-ci s’affichent dans la console Azure Site Recovery. Vous pouvez afficher les propriétés des machines virtuelles, suivre leur état et basculer des groupes de réplication contenant plusieurs machines virtuelles. Notez que dans le cadre de la réplication SAN, toutes les machines virtuelles associées à un groupe de réplication doivent basculer ensemble. Cela vient du fait que le basculement se produit d'abord au niveau de la couche de stockage. Il est important de regrouper vos groupes de réplication correctement et de ne placer ensemble que les ordinateurs virtuels associés.</P>

Suivez la progression de l'action d'activation de la protection, y compris la réplication initiale, dans l'onglet **Tâches**. Lorsque la tâche de finalisation de la protection est terminée, l’ordinateur virtuel est prêt pour le basculement. ![Tâche de protection de la machine virtuelle](./media/site-recovery-vmm-san/SRSAN_JobPropertiesTab.png)

## Étape 8 : Tester le déploiement</h3>

Pour vous assurer que les machines virtuelles et les données basculent comme prévu, testez votre déploiement. Pour ce faire, créez un plan de récupération en sélectionnant des groupes de réplication. Ensuite, exécutez un test de basculement sur le plan.

1. Dans l'onglet **Plans de récupération**, cliquez sur **Créer un plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM source et cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source. Sélectionnez **SAN** pour n’afficher que les clouds configurés pour la réplication SAN. 3. ![Créer un plan de récupération](./media/site-recovery-vmm-san/SRSAN_RPlan.png)

4. Dans **Sélectionner la machine virtuelle**, sélectionnez les groupes de réplication. Toutes les machines virtuelles associées au groupe de réplication sont sélectionnées et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutées au groupe par défaut du plan de récupération, à savoir le groupe 1. Vous pouvez ajouter d'autres groupes si nécessaire. Notez qu'après la réplication, les machines virtuelles démarrent dans l'ordre des groupes du plan de récupération.

	![Ajouter des machines virtuelles](./media/site-recovery-vmm-san/SRSAN_RPlanVM.png)	
5. Une fois un plan de récupération créé, il s’affiche dans la liste de l'onglet **Plans de récupération**. 
6. Dans l'onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
7. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Si cette option est activée, les machines virtuelles de réplication basculées ne sont connectées à aucun réseau. Le test vérifie que la machine virtuelle bascule comme prévu, mais il ne vérifie pas l'environnement de votre réseau de réplication. Si vous voulez exécuter un test de basculement plus complet, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Tester un déploiement d'un serveur local vers un serveur local sur MSDN</a>.

	![Sélectionner le réseau de test](./media/site-recovery-vmm-san/SRSAN_TestFailover1.png)


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

## Suivi de l'activité

Vous pouvez utiliser l'onglet **Jobs** et **Dashboard** pour afficher et surveiller les tâches principales effectuées par le coffre Azure Site Recovery, telles que la configuration de la protection d'un cloud, l'activation et la désactivation de la protection d'une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.

Depuis l'onglet **Jobs**, vous pouvez afficher les tâches, accéder aux détails de l'une d'entre elles ainsi qu'aux erreurs précédemment rencontrées lors de l'exécution de celle-ci, exécuter des requêtes de tâches pour récupérer les tâches qui correspondent à des critères spécifiques, exporter des tâches sur Excel et redémarrer celles qui ont échoué.

Depuis **Dashboard**, vous pouvez télécharger la dernière version des fichiers d'installation fournisseur et agent, obtenir des informations sur la configuration du coffre, découvrir le nombre de machines virtuelles dont la protection est assurée par le coffre, afficher les tâches récentes, gérer le certificat de coffre et resynchroniser des machines virtuelles.

Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez la page [Opérations et surveillance](http://go.microsoft.com/fwlink/?LinkId=398534).
	
 

<!---HONumber=58_postMigration-->