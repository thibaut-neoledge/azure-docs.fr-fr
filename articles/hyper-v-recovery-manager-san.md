<properties 
	pageTitle="Didacticiel : Configuration de la protection entre des sites locaux VMM avec SAN" 
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles Hyper-V entre les sites locaux en utilisant la réplication SAN." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Didacticiel : Configuration de la protection entre des sites locaux VMM avec SAN



<h2><a id="overview" name="overview" href="#overview"></a>Vue d'ensemble</h2>
<p>Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité de votre entreprise et de sa charge de travail en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement.<p>

<P>Ce didacticiel explique comment déployer Azure Site Recovery pour orchestrer et automatiser la protection des charges de travail s'exécutant sur un serveur Hyper-V géré par System Center dans un site VMM local vers un autre site VMM local à l'aide de la réplication basée sur des baies de stockage (SAN). Il utilise, dans la mesure du possible, le chemin de déploiement le plus rapide et les paramètres par défaut. Dans ce scénario :</P>

<ul>
	<li>Vous tirez parti de votre infrastructure SAN existante pour protéger les applications critiques déployées dans des clusters Hyper-V.</li>
	<li>La réplication SAN prend en charge les clusters invités et garantit la cohérence de la réplication entre les différentes couches d'une application (via la réplication synchronisée et asynchrone), en fonction des capacités des baies de stockage.</li>
	<li>La configuration et l'activation de la réplication s'effectuent dans VMM et dans le coffre Azure Site Recovery. Vous allez découvrir et classer le stockage SAN dans VMM, approvisionner des LUN et allouer de l'espace de stockage aux clusters Hyper-V. Azure Site Recovery automatise la réplication et orchestre le basculement. </li>
	</ul>

<UL>
<LI>Vous trouverez des instructions complètes sur le déploiement dans les guides de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">planification</a> et de <a href="http://go.microsoft.com/fwlink/?LinkId=519251">déploiement</a>.</LI>
<LI>Vous pouvez découvrir d'autres scénarios de déploiement d'Azure Site Recovery dans <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Vue d'ensemble d'Azure Site Recovery</a>.</LI>
<LI>Si vous rencontrez des problèmes durant ce didacticiel, consultez l'article Wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery : erreurs courantes et résolutions</a> ou publiez vos questions sur le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>avant</h2>
<div class="dev-callout"> 
<P>Avant de commencer la procédure pas à pas, assurez-vous que tout est en place.</P>

<UL>
<LI><b>Compte Azure</b> : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page <a href="http://aka.ms/try-azure">Version d'évaluation gratuite d'Azure</a>. Pour plus d'informations sur la tarification, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Tarification d'Azure Site Recovery Manager</a>.</LI>
<LI>Si vous souhaitez savoir quelles informations sont collectées, traitées ou transmises pendant les opérations Azure Site Recovery, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=513016">Exigences de confidentialité</a> sur MSDN.</LI>
<LI><b>Serveur VMM</b> : vous devez disposer d'un serveur VMM dans chaque site local, déployé en tant que serveur autonome physique ou virtuel ou en tant que cluster virtuel, s'exécutant sur System Center 2012 R2 avec le <a href="http://go.microsoft.com/fwlink/?LinkId=517707">Correctif cumulatif VMM 5.0 (version préliminaire)</a>.</LI>
<LI>Vous devez disposer d'un cluster hôte Hyper-V déployé dans les sites principaux et secondaires, exécutant au moins Windows Server 2012 avec les dernières mises à jour.</LI>
<LI><b>Clouds VMM</b> : vous devez disposer d'au moins un cloud sur le serveur VMM principal à protéger et d'un autre sur le serveur VMM secondaire. Le cloud principal à protéger doit comporter les éléments suivants :<UL>
	<LI>un ou plusieurs groupes hôtes VMM ;</LI>
	<LI>un ou plusieurs clusters Hyper-V dans chaque groupe hôte ;</LI>
	<li>une ou plusieurs machines virtuelles situées sur le serveur Hyper-V source dans le cloud.</li>
		</UL>Pour en savoir plus sur la configuration des clouds VMM, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=513015">Planification de l'infrastructure VMM</a> dans le guide de planification.</LI>
<LI><b>Stockage SAN</b> : la réplication SAN vous permet de répliquer des machines virtuelles en cluster invitées à l'aide d'un espace de stockage iSCSI ou Fibre Channel ou bien de disques durs virtuels partagés (vhdx). La configuration requise pour SAN est la suivante :</LI>
	<UL>
	<LI>Vous devez disposer de deux groupes SAN configurés, l'un dans le site principal et l'autre dans le site secondaire.</LI>
	<LI>Une infrastructure réseau doit être configurée entre les groupes. L'homologation et la réplication doivent être configurées. Des licences de réplication doivent être configurées conformément aux exigences des baies de stockage.</LI>
	<LI>La mise en réseau doit être configurée entre les serveurs hôtes Hyper-V et la baie de stockage pour que les hôtes puissent communiquer avec les LUN de stockage via iSCSI ou Fibre Channel.</LI>
	<LI>Consultez la liste des baies de stockage prises en charge dans <a href="http://go.microsoft.com/fwlink/?LinkId=518669">Déploiement d'Azure Site Recovery avec VMM et SAN : baies de stockage prises en charge</a>.</LI>
	
	<LI>Les fournisseurs SMI-S, fournis par EMC et NetApp, doivent être installés, et les groupes SAN doivent être gérés par le fournisseur. Configurez le fournisseur conformément à la documentation qui l'accompagne.</LI>
	<LI>Vérifiez que le fournisseur SMI-S pour le groupe se trouve sur un serveur auquel le serveur VMM peut accéder via le réseau au moyen d'une adresse IP ou du nom de domaine complet.</LI>
	<LI>Chaque groupe SAN doit posséder un ou plusieurs pools de stockage disponibles dans le cadre de ce déploiement.</LI>
	<LI>Le serveur VMM au niveau du site principal doit gérer le groupe principal, tandis que le serveur VMM secondaire doit gérer le groupe secondaire.</LI>
	</UL>
<LI><b>Réseaux</b> : vous pouvez éventuellement configurer le mappage réseau pour vous assurer, d'une part, que les machines virtuelles de réplication sont placées de manière optimale sur les serveurs hôtes Hyper-V après le basculement et, d'autre part, que ces machines virtuelles peuvent se connecter aux réseaux VM appropriés. Quand le mappage réseau est activé, une machine virtuelle à l'emplacement principal est connectée à un réseau et son réplica à l'emplacement cible est connecté à son réseau mappé. Si vous ne configurez pas le mappage réseau, les machines virtuelles ne seront pas connectées aux réseaux VM après le basculement. Ce didacticiel décrit la procédure pas à pas la plus élémentaire et ne couvre pas le mappage réseau. Pour en savoir plus, consultez les sections suivantes :</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Mappage réseau</a> dans le guide de planification.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522290">Activation du mappage réseau</a> dans le guide de déploiement SAN.</LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Étapes du didacticiel</h2>
Après avoir vérifié la configuration requise, procédez comme suit :

1. <a href="#VMM">Étape 1 : préparer l'infrastructure VMM</a>
	- Intégrez et classez le stockage SAN dans VMM.
	- Approvisionnez les unités logiques (LUN) et les pools de stockage, et allouez de l'espace de stockage aux clusters Hyper-V.
	- Créez des groupes de réplication pour les LUN qui doivent être répliqués ensemble. 
2. <a href="#vault">Étape 2 : créer un coffre</a>
3. <a href="#download">Étape 3 : installer le fournisseur sur les serveurs VMM</a>. Générez une clé d'inscription dans le coffre, puis téléchargez le fichier d'installation du fournisseur. Exécutez le programme d'installation sur le serveur VMM pour installer le fournisseur et inscrivez les serveurs dans le coffre.
4. <a href="#storage">Étape 4 : mapper les groupes et les pools de stockage</a>. Mappez les groupes pour spécifier le pool de stockage secondaire qui doit recevoir les données de réplication du pool principal. Vous devez mapper le stockage avant de configurer la protection du cloud, car les informations de mappage sont utilisées quand vous activez la protection des groupes de réplication.
5. <a href="#clouds">Étape 5 : configurer la protection du cloud</a>. Configurez les paramètres de protection des clouds VMM.
7. <a href="#replication">Étape 6 : activer les groupes de réplication</a>. Avant d'activer la protection des machines virtuelles, vous devez activer la réplication des groupes de réplication de stockage.
8. <a href="#enablevirtual">Étape 7 : activer la protection des machines virtuelles</a>. Une fois la réplication des groupes de réplication activée, activez la protection des machines virtuelles.
9. <a href="#recovery plans">Étape 8 : tester le déploiement</a>. Vous pouvez effectuer un test de basculement pour vérifier que les machines virtuelles et les données basculent comme prévu. 


<a name="VMM"></a> <h3>Étape 1 : préparer l'infrastructure VMM</h3>

<a name="SAN"></a> <h4>Intégrez et classez le stockage SAN dans VMM.</h4>


1. Dans l'espace de travail **Fabric**, cliquez sur **Stockage**. Cliquez sur **Accueil** > **Ajouter des ressources** > **Périphériques de stockage** pour démarrer l'Assistant Ajout de périphériques de stockage.
2. Dans **Sélectionner un type de fournisseur de stockage**, sélectionnez **Périphériques SAN et NAS découverts et gérés par un fournisseur SMI-S**.

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. Dans la page **Spécifier le protocole et l'adresse du fournisseur SMI-S de stockage**, sélectionnez **CIMXML SMI-S** et spécifiez les paramètres de connexion au fournisseur.
4. Dans **Adresse IP ou nom de domaine complet du fournisseur** et **Port TCP/IP**, spécifiez les paramètres de connexion au fournisseur. Vous pouvez uniquement utiliser une connexion SSL pour CIMXML SMI-S.

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. Dans **Compte d'identification**, spécifiez un compte d'identification VMM capable d'accéder au fournisseur ou créez un compte.
6. Dans la page **Rechercher des informations**, VMM tente de détecter et d'importer automatiquement les informations des périphériques de stockage. Pour relancer la découverte, cliquez sur **Analyser le fournisseur**. Si le processus de découverte réussit, les baies de stockage découvertes, les pools de stockage, le fabricant, le modèle et la capacité sont indiqués dans la page.

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. Dans **Sélectionner les pools de stockage à mettre sous gestion et affecter une classification**, sélectionnez les pools de stockage gérés par VMM et affectez-leur une classification. Les informations sur les LUN sont importées des pools de stockage. Créez des LUN en fonction des applications que vous devez protéger, de leurs besoins en matière de capacité et des éléments que vous devez répliquer ensemble.

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>Créer des LUN et allouer de l'espace de stockage</h4>


1. Une fois le stockage SAN intégré à VMM, vous créez (approvisionnez) des LUN. Pour plus d'informations, consultez :
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">Comment sélectionner une méthode de création d'unités logiques dans VMM</a>
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">Comment approvisionner les unités logiques de stockage dans VMM</a>
2. Ensuite, allouez de l'espace de stockage au cluster hôte Hyper-V pour que VMM puisse déployer les données des machines virtuelles vers le stockage approvisionné : 
	- Avant d'allouer de l'espace de stockage au cluster, vous devez en allouer au groupe hôte VMM sur lequel réside le cluster. Consultez <a href="http://go.microsoft.com/fwlink/?LinkId=518493">Comment allouer des unités logiques de stockage à un groupe hôte</a> et <a href="http://go.microsoft.com/fwlink/?LinkId=518492">Comment faire pour affecter des pools de stockage à un groupe hôte</a>.
	- Allouez une capacité de stockage au cluster comme décrit dans <a href="http://go.microsoft.com/fwlink/?LinkId=513017">Configuration du stockage sur un cluster hôte Hyper-V dans VMM</a>.
	

<a name="RGs"></a> <h4>Créer des groupes de réplication</h4>

1. Créez un groupe de réplication qui recense tous les LUN devant être répliqués ensemble.
2. Dans la console VMM, ouvrez l'onglet **Groupes de réplication** des propriétés de la baie de stockage, puis cliquez sur **Nouveau**.
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>Étape 2 : créer un coffre</h3>
Après avoir validé la configuration requise pour le déploiement, créez un coffre Azure Site Recovery. Vous pouvez également utiliser un coffre existant et configurer la réplication SAN.

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).


2. Développez <b>Services de données</b>, <b>Services de récupération</b>, puis cliquez sur <b>Coffre Site Recovery</b>.


3. Cliquez sur <b>Créer</b>, puis sur <b>Création rapide</b>.
	
4. Dans <b>Nom</b>, entrez un nom convivial permettant d'identifier le coffre.

5. Dans <b>Région</b>, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez les disponibilités géographiques dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Tarification d'Azure Site Recovery</a>.

6. Cliquez sur <b>Créer un coffre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme <b>Actif</b> dans la page Recovery Services principale.</P>

<a name="upload"></a> <h2>Étape 3 : configurer le coffre</h2>


1. Dans la page <b>Recovery Services</b>, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre le site local Hyper-V à l'aide de la réplication de baie**.


<a name="download"></a> <h3>Étape 3 : installer le fournisseur Azure Site Recovery</h3>
Une fois le coffre créé, générez un fichier d'inscription comprenant une clé d'inscription. Vous sélectionnez ce fichier quand vous installez le fournisseur Azure Site Recovery. 

1. Dans la page <b>Démarrage rapide</b>, dans **Préparer les serveurs VMM**, cliquez sur **Générer le fichier de clé d'inscription**. Une fois générée, la clé est valide pendant 5 jours. Téléchargez le fichier dans un lieu sûr accessible par les serveurs VMM, par exemple un partage. Vous devrez sélectionner ce fichier lors de la configuration du fournisseur.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. Dans la page <b>Démarrage rapide</b>, dans **Préparer les serveurs VMM**, cliquez sur <b>Télécharger le fournisseur Microsoft Azure Site Recovery pour une installation sur les serveurs VMM</b> pour obtenir la dernière version du fichier d'installation du fournisseur.
3. Exécutez ce fichier sur les serveurs VMM source et cible.
4. Dans **Vérification de la configuration requise**, indiquez d'arrêter le service VMM pour lancer l'installation du fournisseur. Le service s'arrête et redémarre automatiquement une fois l'installation terminée. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

Une fois le fournisseur installé, poursuivez l'installation afin d'enregistrer le serveur dans le coffre.

6. Dans la page Connexion Internet, indiquez la façon dont le fournisseur s'exécutant sur le serveur VMM se connecte à Internet. Sélectionnez <b>Utiliser les paramètres du proxy système par défaut</b> pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. Dans **Clé d'inscription**, indiquez que la clé a été téléchargée depuis Azure Site Recovery et copiée sur le serveur VMM.
8. Dans **Nom du serveur**, entrez un nom convivial permettant d'identifier le serveur VMM dans le coffre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. Dans **Synchronisation initiale des métadonnées du cloud**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM avec le coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud
10. 
11. via les propriétés de cloud de la console VMM.

10. L'option **Chiffrement des données** ne s'applique pas à ce scénario. 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

11. Cliquez sur <b>Inscrire</b> pour terminer le processus. Après l'inscription, les métadonnées du serveur VMM sont récupérées par Azure Site Recovery. Le serveur apparaît sous l'onglet <b>Ressources</b> de la page **Serveurs** du coffre. Une fois le fournisseur installé, modifiez les paramètres du fournisseur dans la console VMM.

 
<h3><a id="storage"></a>Étape 4 : mapper les groupes et les pools de stockage</h3>

Vous devez créer un mappage entre les groupes et les pools de stockage dans les sites principaux et secondaires.

Avant de commencer, vérifiez que les clouds apparaissent bien dans le coffre. Pour détecter les clouds, vous pouvez soit synchroniser tous les clouds quand vous installez le fournisseur, soit synchroniser un cloud spécifique sous l'onglet **Général** des propriétés du cloud dans la console VMM. Ensuite, mappez les baies de stockage comme suit :

1. Cliquez sur **Ressources** > **Stockage serveur** > **Mapper les baies de stockage source et cible**.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)

2. Sélectionnez les baies de stockage dans le site principal et mappez-les aux baies de stockage dans le site secondaire.
3.  Mappez les pools de stockage source et cible dans les groupes. Pour cela, dans **Pools de stockage**, sélectionnez un pool de stockage source et un pool de stockage cible à mapper.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>Étape 5 : Configurer les paramètres de protection de cloud</h3>

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Vous avez activé l'option **Synchroniser les données du cloud avec le coffre** lors de l'installation du fournisseur. Ainsi, tous les clouds du serveur VMM apparaissent sous l'onglet <b>Éléments protégés</b> du coffre.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. Dans la page Démarrage rapide, cliquez sur **Configurer la protection pour les clouds VMM**.
2. Sous l'onglet **Éléments protégés**, sélectionnez le cloud à configurer, puis accédez à l'onglet **Configuration**. Notez les points suivants :
3. Dans <b>Cible</b>, sélectionnez <b>VMM</b>.
4. Dans <b>Emplacement cible</b>, sélectionnez le serveur VMM local qui gère le cloud à utiliser pour la récupération.
5. Dans <b>Cloud cible</b>, sélectionnez le cloud cible à utiliser pour le basculement des machines virtuelles du cloud source. Notez les points suivants :
	- Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
	- Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.
6. Azure Site Recovery vérifie que les clouds ont accès au stockage compatible avec la réplication SAN et que les baies de stockage sont homologuées. Les groupes homologues participant sont indiqués.
7. Si la vérification réussit, sélectionnez **SAN** dans **Type de réplication**.

<p>Suite à l'enregistrement des paramètres, une tâche est créée que vous pouvez surveiller sous l'onglet <b>Tâches</b>. Les paramètres du cloud peuvent être modifiés sous l'onglet <b>Configurer</b>. Pour modifier l'emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.</p>


<h3><a id="replication"></a>Étape 6 : activer la réplication pour les groupes de réplication</h3>

Avant d'activer la protection des machines virtuelles, vous devez activer la réplication des groupes de réplication de stockage. 

1. Dans le portail Azure Site Recovery, dans la page des propriétés du cloud principal, ouvrez l'onglet **Machines virtuelles**. Cliquez sur **Ajouter un groupe de réplication**.
2. Sélectionnez un ou plusieurs groupes de réplication VMM associés au cloud, vérifiez les groupes source et cible, puis spécifiez la fréquence de réplication.

<P>Une fois cette opération terminée, Azure Site Recovery, VMM et les fournisseurs SMI-S approvisionnent les LUN de stockage du site cible et activent la réplication du stockage. Si le groupe de réplication est déjà répliqué, Azure Site Recovery réutilise la relation de réplication existant et met à jour les informations dans Azure Site Recovery.</P>

<h2><a id="enablevirtual"></a>Étape 7 : activer la protection des machines virtuelles</h2>
<p>Au terme de la réplication de la baie de stockage, activez la protection des machines virtuelles dans la console VMM à l'aide de l'une des méthodes suivantes :</p>



- **Nouvelle machine virtuelle** : dans la console VMM, quand vous créez une machine virtuelle, vous activez la protection Azure Site Recovery et associez la machine virtuelle au groupe de réplication.
Si vous choisissez cette option, VMM place de manière optimale le stockage des machines virtuelles sur les LUN du groupe de réplication. Azure Site Recovery orchestre la création d'une machine virtuelle masquée sur le site secondaire et alloue de l'espace de stockage pour que les machines virtuelles de réplication puissent être démarrées après le basculement.
- **Machine virtuelle existante** : si une machine virtuelle est déjà déployée dans VMM, vous pouvez activer la protection Azure Site Recovery et effectuer une migration de stockage vers un groupe de réplication. Au terme de l'opération, VMM et Azure Site Recovery détectent la nouvelle machine virtuelle et commencent à la gérer dans Azure Site Recovery à des fins de protection. Une machine virtuelle masquée est créée sur le site secondaire et un espace de stockage est alloué pour que la machine virtuelle de réplication puisse être démarrée après le basculement.


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>Une fois la protection des machines virtuelles activée, les machines virtuelles apparaissent dans la console Azure Site Recovery. Vous pouvez afficher les propriétés des machines virtuelles, suivre leur état et basculer des groupes de réplication contenant plusieurs machines virtuelles. Notez que dans le cadre de la réplication SAN, toutes les machines virtuelles associées à un groupe de réplication doivent basculer ensemble. Cela vient du fait que le basculement se produit d'abord au niveau de la couche de stockage. Il est important de regrouper vos groupes de réplication correctement et de placer uniquement les machines virtuelles associées ensemble.</P>

Vous pouvez suivre la progression de l'action d'activation de la protection, y compris la réplication initiale, sous l'onglet **Tâches**. Lorsque la tâche de finalisation de la protection s'exécute, la machine virtuelle est prête à être basculée. 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>Étape 8 : tester le déploiement</h3>



Pour vous assurer que les machines virtuelles et les données basculent comme prévu, testez votre déploiement. Pour ce faire, créez un plan de récupération en sélectionnant des groupes de réplication. Ensuite, exécutez un test de basculement sur le plan.

1. Sous l'onglet **Plans de récupération**, cliquez sur **Créer un plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM source et cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source. Sélectionnez **SAN** pour afficher uniquement les clouds configurés pour la réplication SAN.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. Dans **Sélectionner les machines virtuelles**, sélectionnez les groupes de réplication. Toutes les machines virtuelles associées au groupe de réplication sont sélectionnées et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutées au groupe par défaut du plan de récupération, à savoir le groupe 1. Vous pouvez ajouter d'autres groupes si nécessaire. Notez qu'après la réplication, les machines virtuelles démarrent dans l'ordre des groupes du plan de récupération.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. Une fois un plan de récupération créé, celui-ci apparaît dans la liste sous l'onglet **Plans de récupération**. 
6. Sous l'onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
7. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Si cette option est activée, les machines virtuelles de réplication basculées ne sont connectées à aucun réseau. Le test vérifie que la machine virtuelle bascule comme prévu, mais il ne vérifie pas l'environnement de votre réseau de réplication. Si vous voulez exécuter un test de basculement plus complet, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Tester un déploiement d'un serveur local vers un serveur local</a> sur MSDN.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. La machine virtuelle de test est créée sur le même hôte que celui sur lequel la machine virtuelle de réplication existe. Elle n'est pas ajoutée au cloud où se trouve la machine virtuelle de réplication.
9. Après la réplication, la machine virtuelle de réplication possède une adresse IP différente de celle de la machine virtuelle principale. Si vous émettez des adresses à partir de DHCP, DNS est mis à jour automatiquement. Si vous n'utilisez pas DHCP et que vous souhaitez vous assurer que les adresses sont identiques, vous devez exécuter quelques scripts.
10. Exécutez cet exemple de script pour récupérer l'adresse IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Exécutez cet exemple de script pour mettre à jour DNS, en spécifiant l'adresse IP que vous avez récupérée à l'aide de l'exemple de script précédent.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

<h2><a id="runtest" name="runtest" href="#runtest"></a>Suivi de l'activité</h2>
<p>Vous pouvez utiliser l'onglet <b>Tâches</b> et le <b>Tableau de bord</b> pour afficher et surveiller les tâches principales effectuées par le coffre Azure Site Recovery, telles que la configuration de la protection d'un cloud, l'activation et la désactivation de la protection d'une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.</p>

<p>Sous l'onglet <b>Tâches</b>, vous pouvez afficher les tâches, accéder aux détails de l'une d'elles ainsi qu'aux erreurs, exécuter des requêtes pour récupérer les tâches qui correspondent à des critères spécifiques, exporter des tâches vers Excel et redémarrer celles qui ont échoué.</p>

<p>Dans le <b>Tableau de bord</b>, vous pouvez télécharger la dernière version des fichiers d'installation du fournisseur et de l'agent, obtenir des informations sur la configuration du coffre, découvrir le nombre de machines virtuelles dont la protection est assurée par le coffre, afficher les tâches récentes, gérer le certificat du coffre et resynchroniser les machines virtuelles.</p>

<p>Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guide sur les opérations et la surveillance</a>.</p>
	
<h2><a id="next" name="next" href="#next"></a>Étapes suivantes</h2>
<UL>
<LI>Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guide de planification d'Azure Site Recovery</a> et le <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guide de déploiement d'Azure Site Recovery</a>.</LI>
<LI>Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Forum Azure Recovery Services</a>.</LI> 
</UL>

<!--HONumber=49-->