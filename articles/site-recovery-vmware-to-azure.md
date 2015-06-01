<properties 
	pageTitle="Configurer la protection entre des machines virtuelles ou des serveurs physiques VMWare locaux et Azure" 
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles situées sur des serveurs VMWare locaux vers Azure et entre les serveurs physiques locaux et Azure." 
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
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# Configurer la protection entre des machines virtuelles ou des serveurs physiques VMWare locaux et Azure

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Découvrez les scénarios de déploiement possibles dans la [Vue d'ensemble d'Azure Site Recovery](hyper-v-recovery-manager-overview.md).

Cette procédure pas à pas explique comment déployer la récupération de sites pour :

- **protéger les machines virtuelles VMWare locales sur Azure** ;
- **protéger les serveurs physiques Windows et Linux locaux sur Azure**.

Les avantages pour l'entreprise sont les suivants :

- Protection des serveurs Windows ou Linux physiques.
- Réplication, basculement et restauration simples à l'aide du portail Azure Site Recovery.
- Réplication des données sur Internet, une connexion VPN de site à site ou Azure ExpressRoute.   
- Restauration automatique à partir d'Azure vers une infrastructure VMWare locale. 
- Détection simplifiée des machines virtuelles VMWare.
- Cohérence multimachine virtuelle pour que les machines virtuelles et les serveurs physiques exécutant des charges de travail spécifiques puissent être récupérés ensemble à un point de données cohérent.
- Plans de récupération pour le basculement et la récupération simplifiées des charges de travail échelonnées sur plusieurs machines.

Cette fonctionnalité est actuellement en version préliminaire. Consultez les [Conditions d'utilisation supplémentaires des versions préliminaires](preview-supplemental-terms).

## À propos de ce guide

Ce guide fournit une vue d'ensemble et décrit les conditions requises pour le déploiement. Il explique comment installer tous les composants de déploiement et activer la protection des machines virtuelles et des serveurs. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes, soumettez vos questions sur le [Forum Azure Recovery Services](http://go.microsoft.com/fwlink/?LinkId=313628).

## Vue d'ensemble

Ce diagramme illustre les composants de déploiement.

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### Composants de déploiement

- **Ordinateurs locaux** : votre site local comporte des ordinateurs que vous souhaitez protéger. Il s'agit soit de machines virtuelles exécutée sur un hyperviseur VMWare, soit des serveurs physiques exécutant Windows ou Linux.

- **Serveur de traitement local** : les ordinateurs protégés envoient des données de réplication au serveur de traitement local. Celui-ci exécute un certain nombre d'actions sur ces données. Il les optimise avant de les envoyer au serveur cible maître dans Azure. Il possède un cache disque pour mettre en cache les données de réplication qu'il reçoit. Il gère également l'installation poussée du service de mobilité qui doit être installé sur chaque machine virtuelle ou un serveur physique que vous souhaitez protéger, et il effectue la détection automatique des serveurs vCenter VMWare. Le serveur de traitement est un serveur virtuel ou physique exécutant Windows Server 2012 R2. Nous vous recommandons de le placer sur le même réseau et le même segment de réseau local que les ordinateurs que vous souhaitez protéger, mais vous pouvez l'exécuter sur un autre réseau tant que les ordinateurs protégés disposent d'une visibilité de réseau L3. Durant le déploiement, vous allez configurer le serveur de traitement et l'inscrire auprès du serveur de configuration.

- **Archivage Azure Site Recovery** : le coffre coordonne et orchestre les réplicas des données, le basculement et la récupération entre votre site local et Azure.

- **Serveur de configuration Azure** : le serveur de configuration coordonne la communication entre les ordinateurs protégés, le serveur de traitement et les serveurs cibles maîtres dans Azure. Il configure la réplication et coordonne la récupération dans Azure lors du basculement. Le serveur de configuration s'exécute sur une machine virtuelle A3 Standard Azure dans votre abonnement Azure. Durant le déploiement, vous allez configurer le serveur et l'inscrire dans le coffre Azure Site Recovery.

- **Serveur cible maître** : le serveur cible maître dans Azure stocke les données répliquées à partir de vos ordinateurs protégés à l'aide de disques durs virtuels attachés créés sur le stockage d'objets blob dans votre compte de stockage Azure. Vous le déployez comme une machine virtuelle Azure comme serveur Windows basé sur une image de la galerie Windows Server 2012 R2 (pour protéger des ordinateurs Windows) ou comme serveur Linux basé sur une image de la galerie OpenLogic CentOS 6.6 (pour protéger les ordinateurs Linux). Deux options de dimensionnement sont disponibles : standard A3 et standard D14. Le serveur est connecté au même réseau Azure que le serveur de configuration. Durant le déploiement, vous créez le serveur et l'inscrivez auprès du serveur de configuration.

- **Service de mobilité** : vous installez le service de mobilité sur chaque ordinateur virtuel VMWare ou serveur physique Windows/Linux que vous souhaitez protéger. Le service envoie les données de réplication au serveur de traitement, qui à son tour les envoie au serveur cible maître dans Azure. Le serveur de traitement peut installer automatiquement le service de mobilité sur les ordinateurs protégés, ou vous pouvez déployer le service manuellement à l'aide de votre processus de déploiement de logiciels interne.

- **Canal de réplication et de communication de données** : il existe deux options. Notez que ni l'une ni l'autre ne vous oblige à ouvrir des ports réseau entrants sur les ordinateurs protégés. Toutes les communications réseau sont initiées à partir du site local.
	- **Sur Internet** : communique et réplique les données à partir de serveurs locaux protégés et d'Azure via une connexion internet publique sécurisée. Il s'agit de l'option par défaut.
	- **VPN/ExpressRoute** : communique et réplique les données entre les serveurs locaux et Azure via une connexion VPN. Vous devez configurer un VPN de site à site ou une connexion [ExpressRoute]expressroute) entre le site local et votre réseau Azure. 

 
## Planification de la capacité

- Pour bénéficier de performances optimales et pour tirer parti de la fonctionnalité de cohérence multimachine virtuelle qui permet de récupérer plusieurs ordinateurs protégés vers un point de données cohérent, nous vous recommandons de regrouper les machines virtuelles dans des groupes de protection par charge de travail.
- Vous ne pouvez pas protéger un ordinateur sur plusieurs serveurs cibles maîtres, car à mesure que les disques sont répliqués, un disque dur virtuel qui reflète la taille du disque est créé dans le stockage d'objets blob Azure et attaché en tant que disque de données au serveur cible maître. Bien sûr, vous pouvez protéger plusieurs ordinateurs avec un seul serveur cible maître.
- La machine virtuelle du serveur maître cible peut être une machine Azure A4 ou D14 standard :
	- Avec un disque A4 standard, vous pouvez ajouter 16 disques de données (maximum de 1 023 Go par disque de données) à chaque machine virtuelle.
	- Avec un disque D14 standard, vous pouvez ajouter 32 disques de données (maximum de 1 023 Go par disque de données) à chaque machine virtuelle.
- Notez qu'un disque attaché au serveur maître cible est réservé comme lecteur de rétention. Azure Site Recovery vous permet de définir des fenêtres de rétention et de récupérer les ordinateurs protégés à tout moment dans cette fenêtre. Le lecteur de rétention conserve un journal des modifications du disque pendant la durée des fenêtres.  Cela réduit le nombre de disques maximum à 15 (pour A4) et 31 (pour D14). 
- Pour étendre votre déploiement, vous devez ajouter plusieurs serveurs de traitement et plusieurs serveurs cibles maîtres. Vous devez déployer un second serveur maître cible si vous n'avez pas suffisamment de disques libres sur un serveur maître cible existant. Vous devez déployer un serveur de traitement supplémentaire si le taux de modification des données des ordinateurs protégés dépasse la capacité d'un serveur de traitement existant. Notez que les serveurs de traitement et les serveurs cibles maîtres ne nécessitent pas de mappage un-à-un. Vous pouvez déployer le premier serveur de traitement avec le deuxième serveur maître cible, et ainsi de suite.
Le serveur de traitement utilise le cache disque. Assurez-vous qu'il y a assez d'espace libre C:/ pour le cache. Le dimensionnement du cache est affecté par le taux de modification des données des ordinateurs que vous protégez. Nous recommandons généralement d'affecter une taille de répertoire de cache de 600 Go pour les déploiements de taille moyenne, mais vous pouvez appliquer les recommandations suivantes.

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## Avant de commencer

### Conditions préalables pour Azure

- Vous aurez besoin d'un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](http://aka.ms/try-azure). 
- Vous aurez besoin d'un compte de stockage Azure pour stocker les données répliquées. La géo-réplication doit être activée pour ce compte. Il doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. Pour en savoir plus, consultez [Introduction à Microsoft Azure Storage](http://go.microsoft.com/fwlink/?LinkId=398704).
- Vous aurez besoin d'un réseau virtuel Azure sur lequel le serveur de configuration et le serveur cible maître seront déployés. Il doit être dans le même abonnement et la même région que le coffre Azure Site Recovery.
- Assurez-vous d'avoir suffisamment de ressources Azure pour déployer tous les composants. Pour en savoir plus, consultez [Limites d'abonnement Azure](azure-subscription-service-limits).
- Vérifiez que les ordinateurs que vous souhaitez protéger sont conformes aux exigences de machine virtuelle Azure. 

	- **Nombre de disques** : un maximum de 31 disques peuvent être pris en charge sur un même serveur protégé.
	- **Tailles des disques** : la capacité d'un disque ne doit pas dépasser 1 023 Go.
	- **Clustering** : les serveurs en cluster ne sont pas pris en charge.
	- **Démarrage** : le démarrage UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) n'est pas pris en charge.
	- **Volumes** : les volumes chiffrés Bitlocker ne sont pas pris en charge.
	- **Noms des serveurs** : les noms doivent contenir entre 1 et 63 caractères (lettres, chiffres et traits d'union). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Une fois qu'un ordinateur est protégé, vous pouvez modifier le nom Azure.	

	Pour en savoir plus sur les exigences Azure, consultez [Prise en charge des machines virtuelles](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Configuration requise pour les composants du scénario

- Serveur de traitement :
	- Vous pouvez déployer le serveur de traitement sur un ordinateur physique ou virtuel exécutant Windows Server 2012 R2 avec les dernières mises à jour. Installez-le sur C:/.
	- Le serveur doit avoir au moins huit cœurs de processeur, et 64 Go de RAM et 300 Go d'espace disponible sur C: sont recommandés.
	- Nous vous recommandons de placer le serveur sur le même réseau et sous-réseau que les ordinateurs que vous souhaitez protéger.
	- Installez VMware vSphere CLI 5.1 sur le serveur pour qu'il puisse effectuer la détection automatique des serveurs VMWare vCenter.
- Le chemin d'installation du serveur de restauration automatique, du serveur cible maître, du serveur de configuration et des serveurs de restauration automatique doit comporter uniquement des caractères anglais. Par exemple, le chemin d'accès doit être **/usr/local/ASR** pour un serveur cible maître exécutant Linux.

### Configuration requise pour VMWare

- Un serveur VMWare vCenter qui gère vos hyperviseurs vSphere VMware. Il doit exécuter vCenter version 5.0 ou ultérieure avec les dernières mises à jour. 
- Un ou plusieurs hyperviseurs vSphere contenant des ordinateurs virtuels VMWare que vous souhaitez protéger. L'hyperviseur doit exécuter ESX/ESXi version 5.0 ou ultérieure avec les dernières mises à jour.
- Les outils VMware doivent être installés et en cours d'exécution sur les ordinateurs virtuels VMWare détectés via un serveur vCenter.

### Configuration requise pour les ordinateurs Windows protégés

Les serveurs physiques ou ordinateurs virtuels VMWare protégés exécutant Windows doivent avoir :

- Un système d'exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 avec au moins SP1.
- Le nom d'hôte, les points de montage, les noms de périphériques, le chemin d'accès système Windows (par exemple : C:\Windows) doivent être uniquement en anglais.
- Le système d'exploitation doit être installé sur le lecteur C:.
- Les options de stockage standard pour les serveurs Windows sont prises en charge.
- Les règles de pare-feu sur les ordinateurs protégés doivent leur permettre de joindre les serveurs de configuration et les serveurs cibles maîtres dans Azure. 
- Après un basculement, si vous voulez vous connecter à des ordinateurs virtuels Windows dans Azure avec le Bureau à distance, assurez-vous que le Bureau à distance est activé pour l'ordinateur local. Si vous vous connectez via un VPN, les règles de pare-feu doivent autoriser les connexions Bureau à distance sur internet.

### Configuration requise pour les ordinateurs Linux protégés

Les serveurs physiques ou ordinateurs virtuels VMWare protégés exécutant Linux doivent avoir :

- Un système d'exploitation pris en charge : CentOS 6.4, 6.5, 6.6 (32 ou 64 bits) ; Oracle Enterprise Linux 6.4, 6.5 (32 ou 64 bits) exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kern Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 (32 ou 64 bits).
- Le nom d'hôte, les points de montage, les noms de périphériques et les chemins d'accès système et les noms de fichiers Linux (par exemple /etc/; /usr) doivent uniquement être en anglais.
- La protection peut être activée pour les ordinateurs locaux avec le stockage suivant :
	- Système de fichiers : EXT3, ETX4, ReiserFS, XFS
	- Logiciel multichemin : Mappeur de périphérique - multichemin
	- Gestionnaire de volume : LVM2
	- Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge.
- Les règles de pare-feu sur les ordinateurs protégés doivent leur permettre de joindre les serveurs de configuration et les serveurs cibles maîtres dans Azure.
- Si vous souhaitez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l'aide d'un client Secure Shell (ssh), assurez-vous que le service Secure Shell sur l'ordinateur protégé est configuré pour démarrer automatiquement au démarrage du système et que les règles de pare-feu autorisent une connexion ssh à cet ordinateur.  

### Configuration requise pour les logiciels tiers

Le fonctionnement correct de certains composants de déploiement de ce scénario dépend de logiciels tiers. Pour obtenir une liste complète, consultez <a href="#thirdparty">Informations et remarques relatives aux logiciels tiers</a>. 

## Étape 1 : créer un coffre

1. Connectez-vous au [Portail de gestion](https://portal.azure.com).


2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.


3. Cliquez sur **Créer un nouveau** > **Création rapide**.
	
4. Dans **Nom**, entrez un nom convivial permettant d'identifier le coffre.

5. Dans **Région**, sélectionnez la région géographique du coffre. Pour découvrir les régions prises en charge, consultez la section Disponibilité géographique dans [Tarification d'Azure Site Recovery](href="http://go.microsoft.com/fwlink/?LinkId=389880).

6. Cliquez sur **Créer un coffre**. 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** dans la page **Services de récupération** principale.

## Étape 2 : déployer un serveur de configuration


1. Dans la page **Services de récupération**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez également ouvrir la page Démarrage rapide à tout moment au moyen de l'icône.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre un site local avec VMware/serveurs physiques et Azure**.
3. Dans **Préparer les ressources cibles** cliquez sur **Déployer un serveur de configuration**.

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. Spécifiez les détails du serveur de configuration et les informations d'identification pour se connecter au serveur. Sélectionnez le réseau Azure sur lequel le serveur doit se trouver. Spécifiez le sous-réseau et l'adresse IP interne à affecter au serveur. Quand vous cliquez sur **OK**, une machine virtuelle standard A3 basée sur une image de la galerie Azure Site Recovery Windows Server 2012 R2 est créée dans votre abonnement pour le serveur de configuration. Elle est créée comme première instance d'un service cloud avec une adresse IP publique réservée.

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. Vous pouvez surveiller la progression sous l'onglet **Tâches**.

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  Une fois le serveur de configuration déployé, notez l'adresse IP publique qui lui est affectée dans la page **Machines virtuelles** du portail Azure. Ensuite, sous l'onglet **Points de terminaison**, notez le port HTTPS public mappé au port privé 443. Vous aurez besoin de ces informations ultérieurement, lors de l'inscription du serveur cible maître et du serveur de traitement auprès du serveur de configuration. Le serveur de configuration est déployé avec quatre points de terminaison publics :

	- 443 : canal HTTPS utilisé pour coordonner les communications entre les serveurs de composants et Azure.
	- 9443 : utilisé pour l'outil de restauration automatique et la communication de restauration automatique.
	- Bureau à distance
	- PowerShell


## Étape 3 : inscrire le serveur de configuration dans le coffre

1. Dans **Préparer les ressources cibles**, cliquez sur **Télécharger une clé d'inscription**. Le fichier de clé est généré automatiquement. Il est valide pendant cinq jours après sa création. Copiez le fichier sur le serveur de configuration.
2. Dans la page **Tableau de bord** de la machine virtuelle, cliquez sur **Connexion**. Utilisez le fichier RDP téléchargé pour vous connecter au serveur de configuration avec le Bureau à distance.  Quand vous vous connectez pour la première fois, l'Assistant Installation et inscription Azure Site Recovery s'exécute automatiquement.

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. Suivez les instructions de l'Assistant. Vous devez télécharger et installer MySQL Server et spécifier des informations d'identification. Dans la page **Inscription à Azure Site Recovery**, recherchez le fichier de clé que vous avez copié sur le serveur.

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. Une fois l'inscription terminée, une phrase secrète est générée. Copiez-la à un emplacement sécurisé. Vous en aurez besoin pour authentifier et inscrire le serveur de traitement et le serveur cible maître auprès du serveur de configuration. Elle sert également à garantir l'intégrité du canal lors des communications avec le serveur de configuration. Vous pouvez regénérer la phrase secrète, mais vous devrez dans ce cas réinscrire le serveur de traitement et le serveur cible maître à l'aide de la nouvelle phrase secrète.

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

Après l'inscription, le serveur de configuration est répertorié dans la page **Serveurs de configuration** dans le coffre.

## Étape 4 : configurer une connexion VPN
 
Vous pouvez vous connecter au serveur de configuration via internet ou à l'aide d'une connexion VPN ou ExpressRoute. Une connexion internet utilise les points de terminaison de la machine virtuelle avec l'adresse IP virtuelle publique du serveur. VPN utilise l'adresse IP interne du serveur et les ports privés du point de terminaison.
 
Vous pouvez configurer une connexion VPN au serveur comme suit :

1. Si vous n'avez pas configuré de connexion de site à site ou Azure ExpressRoute, consultez les articles suivants :

	- [ExpressRoute or VPN - What's right for me](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/) 
	- [Configurer un VPN de site à site dans le portail de gestion](https://msdn.microsoft.com/library/azure/dn133795.aspx)
	- [Configuration d'une connexion ExpressRoute via un fournisseur Exchange](https://msdn.microsoft.com/library/azure/dn606306.aspx)
	
2. Dans le coffre, cliquez sur **Serveurs** > **Serveurs de Configuration** > Serveur de configuration > **Configurer**.
3. Dans **Paramètres de connectivité**, affectez la valeur **VPN** à **Type de connectivité**. Notez que si vous avez configuré un VPN mais que vous n'avez pas d'accès à internet à partir du site local, vous devez sélectionner l'option VPN. Si vous ne sélectionnez pas cette option, le serveur de traitement ne pourra pas envoyer les données de réplication au serveur cible maître sur ses points de terminaison publics.

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## Étape 5 : déployer le serveur cible maître

1. Dans **Préparer les ressources cibles**, cliquez sur **Déployer le serveur maître cible**.
2. Spécifiez les détails du serveur cible maître et les informations d'identification. Le serveur sera déployé sur le même réseau Azure que le serveur de configuration auprès duquel vous l'inscrivez. Quand vous cliquez sur Terminer, une machine virtuelle Azure est créée avec une image de la galerie Windows ou Linux. 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Une machine virtuelle de serveur maître Windows est créée avec les points de terminaison TCP publics suivants :

	- Personnalisé : le port public est utilisé pour envoyer des données de réplication via internet. Le port privé 9443 est utilisé par le serveur de traitement pour envoyer des données au serveur cible maître via VPN.
	- Personalisé1 : le port privé 9080 est utilisé par le serveur de traitement pour envoyer des données au serveur cible via VPN.
	- PowerShell : port privé : 5986
	- Bureau à distance : port privé : 3389

4. Une machine virtuelle de serveur maître Linux est créée avec les points de terminaison suivants :

	- Personnalisé : le port public est utilisé pour envoyer des données de réplication via internet. Le port privé 9443 est utilisé par le serveur de traitement pour envoyer des données au serveur cible maître via VPN.
	- Personalisé1 : le port privé 9080 est utilisé par le serveur de traitement pour envoyer des données au serveur cible maître via VPN.
	- SSH : port privé 22

5. Dans **Machines virtuelles**, attendez que la machine virtuelle démarre. 

	- Si vous avez configuré le serveur avec Windows, notez les détails du Bureau à distance.
	- Si vous avez configuré le serveur avec Linux et que vous vous connectez via VPN, notez l'adresse IP interne de la machine virtuelle. Si vous vous connectez via internet, notez l'adresse IP publique.
6.  Ouvrez une session sur le serveur pour terminer l'installation et l'inscrire auprès du serveur de configuration. Si vous exécutez Windows :

	1. Initiez une connexion Bureau à distance à la machine virtuelle. La première fois que vous ouvrez une session, un script s'exécute dans une fenêtre PowerShell. Ne la fermez pas. Une fois terminé, l'outil de configuration de l'agent hôte s'ouvre automatiquement pour inscrire le serveur.
	2. Dans **Configuration de l'agent hôte**, spécifiez l'adresse IP interne du serveur de configuration et le port 443. Vous pouvez utiliser l'adresse interne et le port privé 443 même si vous ne vous connectez pas en mode VPN, car la machine virtuelle est associée au même réseau Azure que le serveur de configuration. Laissez l'option **Utiliser HTTPS** activée. Entrez la phrase secrète pour le serveur de configuration que vous avez notée précédemment. Cliquez sur **OK** pour inscrire le serveur. Notez que vous pouvez ignorer les options NAT. Elles ne sont pas utilisées.

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Si vous exécutez Linux :
	1. Copiez le [fichier tar du programme d'installation de serveur](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409) sur la machine virtuelle à l'aide d'un client sftp ou ouvrez une session et utilisez wget pour télécharger le fichier à partir du lien fourni dans la page Démarrage rapide. 
	2. Ouvrez une session sur le serveur à l'aide d'un client Secure Shell. Notez que si vous êtes connecté au réseau Azure via VPN, vous devez utiliser l'adresse IP interne. Sinon, utilisez l'adresse IP externe et le point de terminaison public SSH.
	3. Décompressez les fichiers en exécutant le programme d'installation compressé : **tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***."   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. Assurez-vous que vous êtes dans le répertoire dans lequel vous avez extrait le contenu du fichier tar et exécutez la commande " **sudo ./install.sh** ". Sélectionnez l'option **2. Master Target**. Conservez les paramètres par défaut des autres options.

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. Une fois l'installation terminée, la ligne de commande **Host Config Interface** s'affiche. Ne redimensionnez pas cette fenêtre. 
	6. Utilisez les touches de direction pour sélectionner **Global** et appuyez sur Entrée.
	7. Dans **Enter the IP address**, entrez l'adresse interne du serveur de configuration et le port 22.
	8.  Spécifiez la phrase secrète du serveur de configuration que vous avez notée précédemment, puis appuyez sur Entrée. Sélectionnez **Quit** pour terminer l'installation. Notez que si vous utilisez le client PuTTY pour vous connecter par ssh à la machine virtuelle, vous pouvez utiliser Maj+Insertion pour coller. 
	9.  Utilisez la touche Droite pour quitter et appuyez sur Entrée.

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. Patientez de cinq à dix minutes et, dans la page **Serveurs** > **Serveurs de Configuration**, vérifiez que le serveur cible maître est répertorié comme inscrit sous l'onglet **Détails du serveur**. Si vous exécuté Linux et que l'inscription n'a pas été effectué, réexécutez l'outil de configuration d'hôte à partir de /usr/local/ASR/Vx/bin/hostconfigcli. Vous devrez définir des autorisations d'accès en exécutant chmod en tant que super utilisateur.

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## Étape 6 : déployer le serveur de traitement local

1. Cliquez sur Démarrage rapide > **Installer un serveur de traitement local** > **Télécharger et installer le serveur de traitement**.

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. Copiez le fichier zip téléchargé sur le serveur sur lequel vous allez installer le serveur de traitement. Le fichier .zip contient deux fichiers d'installation :

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. Décompressez l'archive et copiez les fichiers d'installation à un emplacement sur le serveur.
4. Exécutez le fichier d'installation **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** et suivez les instructions. Cette opération installe les composants tiers nécessaires au déploiement.
5. Ensuite, exécutez **Microsoft-ASR_CX_8.2.0.0_Windows***.
6. Dans la page **Mode du serveur**, sélectionnez **Serveur de traitement**.
7.	Dans **Détails du serveur de configuration**, si vous vous connectez via un VPN, spécifiez l'adresse IP interne du serveur de configuration et le port 443. Sinon, spécifiez l'adresse IP virtuelle publique et un point de terminaison HTTP public mappé.
8.	Désactivez l'option **Vérifier la signature logicielle du service de mobilité** si vous souhaitez désactiver la vérification lors de l'utilisation de la transmission automatique pour installer le service. La vérification de la signature nécessite une connectivité internet à partir du serveur de traitement.
9.	Tapez la phrase secrète du serveur de configuration.

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. Terminez l'installation du serveur. N'oubliez pas que vous devez installer VMware vSphere CLI 5.1 sur le serveur pour pouvoir détecter les serveurs vCenter.

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

Vérifiez que le serveur de traitement a été correctement inscrit dans le coffre > **Serveur de configuration** > **Détails du serveur**.

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Notez que si vous n'avez pas désactivé la vérification de la signature pour le service de mobilité lors de l'inscription du serveur de traitement, vous pouvez le faire ultérieurement comme suit :

1. Ouvrez une session sur le serveur de traitement en tant qu'administrateur et ouvrez le fichier C:\pushinstallsvc\pushinstaller.conf. Dans la section **[PushInstaller.transport]**, ajoutez cette ligne : **SignatureVerificationChecks="0"**. Enregistrez et fermez le fichier.
1. Redémarrez le service InMage PushInstall.


## Étape 7 : ajouter des serveurs vCenter

1. Sous l'onglet **Serveurs** > **Serveurs de configuration**, sélectionnez le serveur de configuration et cliquez pour ajouter un serveur vCenter.

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Spécifiez les détails du serveur vCenter et sélectionnez le serveur de traitement qui sera utilisé pour le détecter.  Le serveur de traitement doit se trouver sur le même réseau que le serveur vCenter et VMware vSphere CLI 5.1 doit être installé dessus.
3. Une fois la détection terminée, le serveur vCenter apparaît sous les détails du serveur de configuration.
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## Étape 8 : créer un groupe de protection

1. Ouvrez **Éléments protégés** > **Groupe de Protection** et cliquez pour ajouter un groupe de protection.

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Dans la page **Spécifier les paramètres du groupe de protection**, spécifiez un nom pour le groupe et sélectionnez le serveur de configuration sur lequel vous souhaitez créer le groupe.

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Dans la page **Spécifier les paramètres de réplication**, configurez les paramètres de réplication qui seront utilisés pour tous les ordinateurs du groupe.   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Paramètres :
	- **Cohérence multimachine virtuelle** : si vous activez cette option, elle crée des points de récupération cohérents au niveau de l'application partagés entre les ordinateurs du groupe de protection. Ce paramètre est particulièrement important quand tous les ordinateurs du groupe de protection  exécutent la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Disponible uniquement pour les serveurs Windows. 
	- **Seuil d'objectif de point de récupération** : des alertes sont générées quand la valeur d'objectif de point de récupération de réplication de la protection continue des données dépasse la valeur de seuil d'objectif de point de récupération configurée.
	- **Rétention de point de récupération** : spécifie la fenêtre de rétention. Les ordinateurs protégés peuvent être récupérés à tout moment dans cette fenêtre.
	- **Fréquence des instantanés cohérente avec l'application** : spécifie la fréquence de création des points de récupération contenant des instantanés cohérents avec l'application.

Vous pouvez surveiller le groupe de protection à mesure que les points de récupération sont créés dans la page **Éléments protégés**. 

## Étape 9 : pousser le service de mobilité

Quand vous ajoutez des ordinateurs à un groupe de protection, le service de mobilité est automatiquement envoyé et installé sur chaque ordinateur par le serveur de traitement. Si vous souhaitez utiliser ce mécanisme d'envoi automatique pour les ordinateurs protégés exécutant Windows, vous devez effectuer les opérations suivantes sur chaque ordinateur :

1. Configurer le Pare-feu Windows pour autoriser le **Partage de fichiers et d'imprimantes** et **Windows Management Instrumentation**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.
2. Le compte utilisé pour effectuer l'installation Push doit appartenir au groupe Administrateurs sur l'ordinateur que vous souhaitez protéger. Notez que ces informations d'identification sont utilisées uniquement pour l'installation Push. Elles ne sont stockées nulle part par le service de mobilité et sont supprimées une fois le serveur protégé. Vous devez fournir ces informations d'identification quand vous ajoutez un ordinateur à un groupe de protection.

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. Si le compte d'administrateur n'est pas un compte de domaine, vous devez désactiver le contrôle d'accès utilisateur distant sur l'ordinateur local. Pour cela, dans HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, créez l'entrée LocalAccountTokenFilterPolicy si elle n'existe pas et attribuez-lui une valeur DWORD de 

Si vous souhaitez protéger des ordinateurs Linux, vous devez effectuer les opérations suivantes :

1. Assurez-vous que le compte est un utilisateur racine sur le serveur Linux source.
1. Installez les packages openssh, openssh-server et openssl les plus récents sur l'ordinateur que vous souhaitez protéger.
1. Activez le port ssh 22.
2. Activez le sous-système Sftp et l'authentification par mot de passe dans le fichier de configuration sshd :
	1. Ouvrez une session sous le compte d'utilisateur racine.
	2. Dans le fichier /etc/ssh/sshd_config, recherchez la ligne qui commence par " PasswordAuthentication ". Supprimez les commentaires de la ligne et remplacez la valeur " no " par " yes ". 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. Recherchez la ligne qui commence par " Subsystem " et supprimez les commentaires de la ligne. 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## Étape 10 : ajouter des ordinateurs à un groupe de protection

1. Ouvrez l'onglet **Éléments protégés** > **Groupe de Protection** > **Ordinateurs** et ajoutez des ordinateurs virtuels ou physiques gérés par un serveur vCenter détecté. Il est recommandé que les groupes de protection reflètent vos charges de travail, pour que vous ajoutiez des ordinateurs qui exécutent une application spécifique au même groupe.

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

2. Dans la page **Sélectionner les machines virtuelles** de **Ajouter des machines virtuelles**, sélectionnez un serveur V-Center, puis sélectionnez des ordinateurs à partir de celui-ci.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. Quand vous ajoutez des ordinateurs à un groupe de protection, le service de la mobilité est installé automatiquement à partir du serveur de traitement local. Pour que le mécanisme d'envoi automatique fonctionne, assurez-vous d'avoir configuré vos ordinateurs protégés comme décrit à l'étape précédente.
4. Dans **Sélectionner les machines virtuelles**, sélectionnez le serveur vCenter qui gère les ordinateurs que vous souhaitez protéger, puis sélectionnez les machines virtuelles.

4. Sélectionnez les serveurs et le stockage à utiliser pour la réplication. 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. Fournissez les informations d'identification pour le serveur source. Cela est nécessaire pour installer automatiquement le service de mobilité sur les ordinateurs sources. Pour Windows server, le compte doit disposer de privilèges d'administrateur sur le serveur source. Pour Linux, le compte doit disposer de privilèges de superutilisateur sur le serveur.

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Cochez la case pour terminer l'ajout d'ordinateurs au groupe de protection et démarrer la réplication initiale pour chaque ordinateur. Vous pouvez surveiller l'état dans la page **Tâches**.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. Cliquez également sur **Éléments protégés** > <groupe de protection> > **Machines virtuelles** pour surveiller l'état de la protection. Une fois que la réplication initiale est terminée et que les ordinateurs synchronisent des données, ils affichent l'état **Protégé**.

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## Étape 11 : définir les propriétés de l'ordinateur protégé

1. Dès qu'un ordinateur est à l'état **Protégé**, vous pouvez configurer ses propriétés de basculement. Dans les détails du groupe de protection, sélectionnez l'ordinateur et ouvrez l'onglet **Configurer**.
2. Vous pouvez modifier le nom qui sera attribué à l'ordinateur dans Azure après le basculement et la taille Azure. Vous pouvez également sélectionner le réseau Azure auquel l'ordinateur sera connecté après le basculement. Notez les points suivants :

	- Le nom de la machine Azure doit satisfaire aux exigences Azure décrites dans la section Configuration requise.
	- Par défaut, les machines virtuelles répliquées dans Azure ne sont pas connectées à un réseau Azure. Si vous souhaitez que des machines virtuelles communiquent, veillez à définir le même réseau Azure pour chacune d'elles.

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## Étape 12 : exécuter un basculement

1. Dans la page **Plans de récupération**, ajoutez un plan de récupération. Spécifiez les détails du plan et sélectionnez **Azure** comme cible.

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. Dans **Sélectionner les machines virtuelles**, sélectionnez un groupe de protection, puis sélectionnez les ordinateurs du groupe à ajouter au plan de récupération.

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Si nécessaire, vous pouvez personnaliser le plan pour créer des groupes et définir l'ordre dans lequel les ordinateurs du plan de récupération sont basculés. Vous pouvez également ajouter des invites pour des actions manuelles et des scripts.

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. Dans la page **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
6. Dans **Confirmer le basculement**, vérifiez le sens du basculement (Vers Azure) et sélectionnez le point de récupération vers lequel basculer. 
7. Attendez que la tâche de basculement soit terminée, puis vérifiez que le basculement a fonctionné comme prévu et que les machines virtuelles répliquées démarrent dans Azure.

<a name="thirdparty"></a><h2>INFORMATIONS ET REMARQUES RELATIVES AUX LOGICIELS TIERS</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49-->