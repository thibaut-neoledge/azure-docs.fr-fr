<properties
	pageTitle="Configurer la protection entre un site local Hyper-V et Microsoft Azure" 
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération de machines virtuelles Hyper-V situées sur des serveurs Hyper-V vers Microsoft Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="06/08/2015" 
	ms.author="raynew"/>


# Configurer la protection entre un site local Hyper-V et Microsoft Azure


## Vue d’ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Pour obtenir des instructions sur les différents scénarios de déploiement possibles, voir [Présentation d’Azure Site Recovery](site-recovery-overview.md).

Cet article explique comment déployer Site Recovery pour répliquer des machines virtuelles situées sur des serveurs Hyper-V locaux qui exécutent Windows Server 2012 R2. La réplication vers Microsoft Azure Storage est orchestrée par Site Recovery. Ce déploiement est particulièrement utile si vous exécutez des serveurs Hyper-V, mais que System Center Virtual Machine Manager (VMM) n’est pas déployé.


## À propos de cet article

Cet article résume les conditions préalables au déploiement, vous aide à configurer les paramètres de réplication et vous permet d’activer la protection des machines virtuelles. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes, soumettez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Avant de commencer

Avant de commencer, assurez-vous que tout est en place.

### Conditions préalables pour Azure

- Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer par une version d’[essai gratuit](pricing/free-trial/).
- - Vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. La géo-réplication doit être activée pour ce compte. Il doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. Pour en savoir plus, voir [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md).
- - Il vous faut un réseau virtuel Microsoft Azure, afin que les machines virtuelles répliquées soient connectées à un réseau après le basculement.

## Conditions préalables liées à Hyper-V

- Sur le site local source, vous devez disposer d’au moins un serveur exécutant Windows Server 2012 R2 avec le rôle Hyper-V.
- Le serveur Hyper-V doit contenir une ou plusieurs machines virtuelles.
- Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.

### Configuration requise pour les machines virtuelles

Les machines virtuelles à protéger doivent répondre aux [conditions préalables requises pour Microsoft Azure](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Conditions préalables requises par les fournisseurs et les agents

Dans le cadre du déploiement de Microsoft Azure Site Recovery, vous allez installer le fournisseur Azure Site Recovery et l’agent associé sur chaque serveur Hyper-V exécutant les machines virtuelles que vous souhaitez protéger. Notez les points suivants :

- Vous devez exécuter la dernière version de ce fournisseur et de cet agent.
- Tous les serveurs Hyper-V d’un coffre doivent présenter la même version.
- Le fournisseur devra se connecter à Microsoft Azure Site Recovery via Internet. Vous pouvez décider d’effectuer cette action sans proxy, au moyen des paramètres de proxy actuellement configurés sur le serveur VMM, ou via les paramètres de proxy personnalisés que vous avez configurés lors de l’installation du fournisseur. Pour utiliser un serveur proxy existant, vérifiez que les URL de connexion à Microsoft Azure sont autorisées à traverser le pare-feu :
	- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
- Pour utiliser un proxy personnalisé, configurez le serveur proxy avant l’installation du fournisseur. Lors de la configuration du fournisseur, vous devez indiquer l’adresse et le port du serveur proxy et saisir les informations d’identification utilisées pour l’accès.

L'illustration ci-dessous montre les différents canaux et ports de communication utilisés par Azure Site Recovery pour l'orchestration et la réplication.

![Topologie B2A](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)

 
## Étape 1 : Créer un coffre

1. Connectez-vous au [portail de gestion](https://portal.azure.com).


2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.


3. Cliquez sur **Créer nouveau** > **Création rapide**.

4. Dans **Name**, entrez un nom convivial pour identifier le coffre.

5. Dans **Region**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section relative aux disponibilités géographiques sur la page [Tarification d’Azure Site Recovery](pricing/details/site-recovery/).

6. Cliquez sur **Create vault**.

	![Nouveau coffre](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

Vérifiez la barre d’état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.


## Étape 2 : Créer un site Hyper-V

1. Dans la page Recovery Services, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l’icône.

	![Quick Start](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre un site Hyper-V local et Microsoft Azure**.

	![Scénario de site Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. Sur la page **Créer un site Hyper-V**, cliquez sur **Créer un site Hyper-V**. Spécifiez un nom de site et enregistrez-le.

	![Site Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## Étape 3 : Installer le fournisseur et l’agent
Installez le fournisseur et l’agent. Si vous les installez sur un cluster Hyper-V, exécutez les étapes 5 à 11 sur chaque nœud du cluster de basculement. Une fois que tous les nœuds sont enregistrés et que la protection est activée, les machines virtuelles sont protégées, même si elles sont migrées d’un nœud vers un autre dans le cluster de basculement.

1. Dans **Préparer les serveurs Hyper-V**, cliquez sur **Télécharger un fichier de clé d’inscription** .
2. Sur la page **Télécharger une clé d’inscription**, cliquez sur l’option **Télécharger** située en regard du site. Téléchargez la clé dans un lieu sûr facilement accessible par le serveur Hyper-V. Une fois générée, la clé est valide pendant 5 jours.

	![Clé d’enregistrement](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. Cliquez sur **Télécharger le fournisseur** pour obtenir sa dernière version.
5. Exécutez le fichier sur chaque serveur Hyper-V à inscrire dans le coffre. Le fichier installe deux composants :
	- **Fournisseur Azure Site Recovery** : gère la communication et l’orchestration entre le serveur Hyper-V et le portail Microsoft Azure Site Recovery. 
	- **Agent Azure Recovery Services** : gère le transport de données entre les machines virtuelles en cours d’exécution sur le serveur Hyper-V source et Microsoft Azure Storage. 
6. Dans **Microsoft Update**, vous pouvez opter pour l’installation de mises à jour. Une fois ce paramètre activé, les mises à jour du fournisseur et de l’agent sont installées en fonction de votre stratégie Microsoft Update.

	![Mises à jour Microsoft](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. Dans le champ **Installation**, indiquez où vous souhaitez installer le fournisseur et l’agent sur le serveur Hyper-V.

	![Emplacement d’installation](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. Une fois l’installation terminée, poursuivez la procédure pour inscrire le serveur dans le coffre.

	![Installation terminée](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)


9. Sur la page **Connexion Internet**, indiquez la façon dont le fournisseur se connecte à Microsoft Azure Site Recovery. Sélectionnez **Utiliser les paramètres proxy par défaut du système** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur. Si vous n’indiquez aucune valeur, les paramètres par défaut seront utilisés.

	![Paramètres Internet](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

	Notez les points suivants :

	- Si le proxy par défaut se trouvant sur le serveur Hyper-V nécessite une authentification, vous devez opter pour l’utilisation d’un serveur proxy personnalisé. Saisissez les informations de proxy par défaut et spécifiez les informations d’identification.
	- Si vous souhaitez utiliser un serveur proxy personnalisé, configurez-le avant d’installer le fournisseur. 
	- Les URL suivantes doivent être accessibles à partir de l'hôte Hyper-V.
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net
	- Autorisez les adresses IP décrites dans la zone [Étendues d’adresses IP du centre de données Azure](http://go.microsoft.com/fwlink/?LinkId=511094) et le protocole HTTPS (443). Vous devez autoriser les plages IP de la région Microsoft Azure que vous prévoyez d’utiliser, ainsi que celles de la région ouest des États-Unis.

9. Sur la page **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre et le site Hyper-V auquel appartient le serveur Hyper-V.

	![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. Le serveur est alors inscrit dans le coffre.

	![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. Une fois l’inscription terminée, les métadonnées du serveur Hyper-V sont récupérées par Microsoft Azure Site Recovery et le serveur s’affiche sur l’onglet **Sites Hyper-V**, sur la page **Serveurs** du coffre.

	![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)

Remarque : si vous souhaitez installer le fournisseur sur Server Core pour Windows Server 2012 R2 ou la version autonome du logiciel Hyper-V Server 2012 R2, procédez comme décrit ci-après.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription associée vers un dossier, par exemple C:\\ASR.
2. Extrayez le programme d’installation du fournisseur en saisissant la chaîne suivante :

	    C:\Windows\System32> CD C:\ASR
	    C:\ASR>AzureSiteRecoveryProvider.exe /x:. /q

3. Installez le fournisseur en tapant la chaîne suivante :

	    C:\ASR> setupdr.exe /i

4. Enregistrez le serveur en tapant la chaîne suivante :

	    C:\Program Files\Azure Site Recovery Provider> DRConfigurator.exe /r /Credentials <Location of key> /FriendlyName <Hyper-V host name>

	- /Credentials : paramètre obligatoire, qui spécifie l’emplacement auquel le fichier de clé d’inscription se trouve.
	- /FriendlyName : paramètre obligatoire, qui correspond au nom du serveur hôte Hyper-V qui s’affiche sur le portail Microsoft Azure Site Recovery.
	- Paramètres facultatifs :
		- /proxyAddress <address> : adresse du serveur proxy.
		- /proxyport <port> : port du serveur proxy.
		- /proxyUsername <username> : informations d’identification fournies si le proxy requiert une authentification.
		- proxyPassword <password>

>[AZURE.NOTE]Vous pouvez configurer chacun des hôtes Hyper-V individuellement afin d’utiliser des paramètres de bande passante réseau différents pour répliquer les machines virtuelles vers Azure. En savoir plus sur la [Gestion de l’utilisation de la bande passante réseau de protection d’un serveur local vers Azure](https://support.microsoft.com/fr-fr/kb/3056159)


## Étape 4 : Créer des ressources Microsoft Azure

1. Dans la zone **Préparer des ressources**, sélectionnez l’option **Créer un compte de stockage** pour créer un compte Microsoft Azure Storage, si nécessaire. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le coffre Microsoft Azure Site Recovery et être associé au même abonnement.

	![Créez un compte de stockage](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## Étape 5 : Créer et configurer des groupes de protection

Les groupes de protection sont des regroupements logiques incluant les machines virtuelles que vous voulez protéger au moyen de ces paramètres de protection. Vous appliquez des paramètres de protection à un groupe de protection, et ces paramètres sont appliqués à toutes les machines virtuelles que vous ajoutez au groupe. 1. Dans **Créer et configurer des groupes de protection**, cliquez sur **Créer un nouveau groupe de protection**. Si la configuration requise n’est pas respectée, un message s’affiche. Vous pouvez cliquer sur **Afficher les détails** pour obtenir plus d’informations.

2. Sur l’onglet **Groupes de protection**, ajoutez un groupe de protection. Spécifiez un nom, le site Hyper-V source, le système Microsoft **Azure** cible, le nom de votre abonnement à Microsoft Azure Site Recovery et votre compte Microsoft Azure Storage.

	![Groupe de protection](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. Dans **Paramètres de réplication**, définissez l’option **Copier la fréquence** pour indiquer la fréquence de synchronisation du delta des données entre les systèmes cible et source. Vous pouvez choisir une fréquence de 30 secondes, 5 minutes ou 15 minutes.
3. Dans la zone **Conserver les points de récupération**, indiquez le nombre d’heures de stockage de l’historique de récupération.
4. Dans la zone **Fréquence des instantanés cohérents au niveau applicatif**, vous pouvez indiquer si le système doit créer des instantanés en utilisant le service VSS (Volume Shadow Copy Service) pour garantir que les applications présentent un état cohérent lors de la création de l’instantané. Par défaut, aucun instantané n’est créé. Assurez-vous que cette valeur est inférieure au nombre de points de récupération supplémentaires que vous configurez. Cette option est uniquement prise en charge lorsque la machine virtuelle exécute un système d’exploitation Windows.
5. Dans la zone **Heure de début de la réplication initiale**, spécifiez à quel moment la réplication initiale des machines virtuelles au sein du groupe de protection doit être envoyée à Microsoft Azure.

	![Groupe de protection](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## Étape 6 : Activer la protection des machines virtuelles


Ajoutez des machines virtuelles à un groupe de protection pour activer leur protection.

1. Sur l’onglet **Machines** du groupe de protection, cliquez sur **Ajouter des machines virtuelles aux groupes de protection pour activer la protection**.
2. Dans la page **Activer la protection pour les machines virtuelles**, sélectionnez les machines virtuelles à protéger. 

	![activer la protection des machines virtuelles](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

	Les tâches d’activation de la protection démarrent. Effectuez un suivi de la progression sur l’onglet **Tâches**. Lorsque la tâche de finalisation de la protection s’exécute, la machine virtuelle est prête à être basculée. 
3. Une fois la protection activée, vous pouvez effectuer les opérations suivantes :

	- afficher les machines virtuelles dans le champ **Éléments protégés** > **Groupes de protection** > *nom_groupeprotection* > **Machines virtuelles**. Vous pouvez accéder aux détails de la machine dans l’onglet **Propriétés** ;
	- configurer les propriétés du basculement des machines virtuelles dans **Éléments protégés** > **Groupes de protection** > *nom_groupeprotection* > **Machines virtuelles** *nom_machine_virtuelle* > **Configurer**. Vous pouvez configurer les éléments suivants :
		- **Nom** : nom de la machine virtuelle dans Microsoft Azure.
		- **Taille** : taille cible de la machine virtuelle ayant basculé.

		![Configurer les propriétés des machines virtuelles](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
	- Configurez les paramètres supplémentaires des machines virtuelles dans le champ *Éléments protégés** > **Groupes de protection** > *nom_groupeprotection* > **Machines virtuelles** *nom_machine_virtuelle* > **Configurer**. Cela inclut :

		- **Cartes réseau** : le nombre de cartes réseau est défini par la taille spécifiée pour la machine virtuelle cible. 
			- Grande taille (A3) et A6 : 2
			- Extra large (A4) et A7 :
			- A9 : 2
			- D3 : 2
			- D4 : 4
			- D13 : 4
			
			Lorsque vous modifiez la taille d’une machine virtuelle et enregistrez les paramètres, les cartes réseau sont affichées quand vous ouvrez la page **Configurer** par la suite. Le nombre de cartes d’une machine virtuelle est déterminé comme suit :


			- Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
			- Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
			- Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte. 	
		- **Réseau Microsoft Azure** : indiquez le réseau vers lequel la machine virtuelle doit basculer. Si la machine virtuelle présente plusieurs cartes réseau, l’ensemble des cartes doit être connecté au même réseau Microsoft Azure.
		- **Sous-réseau** : pour chaque carte réseau de la machine virtuelle, sélectionnez le sous-réseau dans le réseau Microsoft Azure auquel la machine doit se connecter après le basculement.
		- **Adresse IP cible** : si la carte réseau de la machine virtuelle source est configurée pour utiliser une adresse IP statique, vous pouvez indiquer l’adresse IP de la machine virtuelle cible, afin de vérifier que la machine présente la même adresse IP après le basculement. Si vous n’en indiquez aucune, n’importe quelle adresse disponible sera affectée lors du basculement. Si vous spécifiez une adresse en cours d’utilisation, le basculement échoue.
		 
		![Configurer les propriétés des machines virtuelles](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)

## Étape 7 : créer un plan de récupération

Pour tester le déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle ou un plan de récupération qui contient une ou plusieurs machines virtuelles. Pour créer un plan de récupération, [suivez ces instructions](site-recovery-create-recovery-plans.md)

## Étape 8 : tester le déploiement

Il existe deux manières d’exécuter un test de basculement vers Azure.

- Tester le basculement sans réseau Azure : ce type de test de basculement vérifie que la machine virtuelle s’affiche correctement dans Azure. La machine virtuelle ne sera connectée à aucun réseau Azure après le basculement.
- Tester le basculement avec un réseau Azure : ce type de basculement vérifie que l’ensemble de l’environnement de réplication s’affiche comme prévu et qu’après le basculement les machines virtuelles seront connectées au réseau Azure cible spécifié. Pour la gestion de sous-réseau, pour le test de basculement le sous-réseau de la machine virtuelle test sera déterminé en fonction du sous-réseau de la machine virtuelle de réplication. Ceci diffère de la réplication normale, selon laquelle le sous-réseau d’une machine virtuelle de réplication est basé sur le sous-réseau de la machine virtuelle source.

Si vous souhaitez exécuter un test de basculement pour une machine virtuelle activée pour la protection dans Azure sans spécifier de réseau Azure cible, vous n’avez pas besoin de rien préparer. Pour exécuter un test de basculement avec un réseau Azure cible, vous devez créer un réseau Azure isolé de votre réseau de production Azure (comportement par défaut quand vous créez un réseau dans Azure) et configurer l’infrastructure pour que la machine virtuelle répliquée fonctionne comme prévu. Par exemple, une machine virtuelle sur laquelle le contrôleur de domaine et DNS sont installés peut être répliquée sur Microsoft Azure via Microsoft Azure Site Recovery et peut être créée dans le réseau de test, via le test de basculement. Pour exécuter un test de basculement, procédez comme suit :


1. Effectuez un test de basculement de la machine virtuelle avec le contrôleur de domaine et DNS, dans le même réseau que celui que vous utiliserez pour le test de basculement réel de la machine virtuelle locale.
2. Notez les adresses IP allouées à la machine virtuelle DNS soumise au basculement.
3. Sur le réseau virtuel Microsoft Azure qui sera utilisé pour le basculement, ajoutez l’adresse IP en tant qu’adresse du serveur DNS.
4. Exécutez le test de basculement des machines virtuelles locales sources, en spécifiant le réseau de test Microsoft Azure.
5. Après avoir vérifié que le test de basculement a fonctionné comme prévu, signalez qu’il a abouti pour le plan de récupération, puis pour le contrôleur de domaine et les machines virtuelles DNS.

Pour exécuter le test de basculement, procédez comme suit :

1. Sous l’onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
2. Sur la page **Confirmer le test de basculement**, sélectionnez **Aucun** ou un réseau Microsoft Azure spécifique. Remarque : si vous sélectionnez **Aucun**, le test de basculement vérifie que la machine virtuelle a été répliquée correctement dans Microsoft Azure, mais il ne vérifie pas la configuration de votre réseau de réplication.

	![Test de basculement](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. Sur l’onglet **Tâches**, vous pouvez suivre la progression du basculement. Vous devriez aussi pouvoir voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
4. Lorsque le basculement atteint la phase **Terminer le test**, cliquez sur **Terminer le test** pour finir l’opération. Sous l’onglet **Tâches**, vous pouvez suivre la progression et l’état du basculement et effectuer toutes les actions nécessaires.
5. Après le basculement, vous pouvez voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.

	1. Vérifiez que les machines virtuelles démarrent correctement.
    2. Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement. Vous devez également ajouter un point de terminaison RDP sur la machine virtuelle. Pour cela, vous pouvez utiliser les [Runbooks de Microsoft Azure Automation](site-recovery-runbook-automation.md).
    3. Une fois le basculement effectué, si vous utilisez une adresse IP publique pour vous connecter à la machine virtuelle dans Microsoft Azure via le Bureau à distance, vérifiez qu’aucune stratégie du domaine ne vous empêche de vous connecter à une machine virtuelle au moyen d’une adresse publique.

6. Une fois le test effectué, procédez comme suit :

	- Cliquez sur **Le test de basculement est terminé**. Nettoyez l’environnement de test afin qu’il mette hors tension et supprime automatiquement les machines virtuelles de test.
	- Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
7. Lorsque le basculement se trouve en phase **Terminer le test**, terminez la vérification comme suit :
	1. Examinez l’ordinateur virtuel de réplication dans le portail Microsoft Azure. Vérifiez que la machine virtuelle démarre correctement.
	2. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
	3. Cliquez sur **Terminer le test** pour finir l’opération.
	4. Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
	5.  Cliquez sur **Le test de basculement est terminé**. Nettoyez l’environnement de test pour mettre automatiquement hors tension et supprimer la machine virtuelle de test.

## Étapes suivantes

Une fois votre déploiement configuré et en cours d'exécution, découvrez [plus d'informations](site-recovery-failover.md) sur le basculement.

<!---HONumber=July15_HO2-->