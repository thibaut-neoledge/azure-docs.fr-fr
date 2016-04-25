<properties
	pageTitle="Répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire | Microsoft Azure"
	description="Cet article explique comment répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire avec Azure Site Recovery."
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
	ms.date="02/17/2016"
	ms.author="raynew"/>

# Répliquer des machines virtuelles Hyper-V dans des clouds VMM vers un site VMM secondaire

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

## Vue d'ensemble

Cet article décrit comment répliquer des machines virtuelles Hyper-V sur des serveurs hôtes Hyper-V gérés dans des clouds VMM vers un site VMM secondaire à l’aide d’Azure Site Recovery.

L’article décrit les conditions préalables et vous montre comment configurer un coffre Site Recovery, installer le fournisseur Azure Site Recovery sur les serveurs VMM source et cible, inscrire les serveurs dans le coffre, configurer les paramètres de protection des clouds VMM, puis activer la protection des machines virtuelles Hyper-V. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Publiez vos commentaires ou questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Architecture

L'illustration ci-dessous montre les différents canaux et ports de communication utilisés par Azure Site Recovery pour l'orchestration et la réplication.

![Topologie E2E](./media/site-recovery-vmm-to-vmm/e2e-topology.png)

## Avant de commencer

Assurez-vous que les conditions préalables sont remplies :

**Configuration requise** | **Détails**
--- | ---
**Microsoft Azure**| Vous aurez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.
**VMM** | Vous avez besoin d’au moins un serveur VMM.<br/><br/>Le serveur VMM doit exécuter au moins System Center 2012 SP1 avec les dernières mises à jour cumulatives.<br/><br/>Si vous voulez configurer la protection avec un seul serveur VMM, vous avez besoin d’au moins deux clouds configurés sur le serveur.<br/><br/>Si vous voulez déployer la protection avec deux serveurs VMM, chaque serveur doit avoir au moins un cloud configuré sur le serveur VMM principal à protéger et un cloud configuré sur le serveur VMM secondaire à utiliser pour la protection et la récupération.<br/><br/>Tous les clouds VMM doivent avoir le profil Capacité Hyper-V défini.<br/><br/>Le cloud source à protéger doit contenir un ou plusieurs groupes hôtes VMM.<br/><br/>Pour en savoir plus sur la configuration des clouds VMM, consultez et [Procédure pas à pas : création de clouds privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) dans le blog de Keith Mayer.
**Hyper-V** | Vous avez besoin d’un ou plusieurs serveurs hôtes Hyper-V dans les groupes hôtes VMM principaux et secondaires, ainsi que d’une ou plusieurs machines virtuelles sur chaque serveur hôte Hyper-V.<br/><br/>Les serveurs Hyper-V hôte et cible doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour installées.<br/><br/>Tout serveur Hyper-V contenant des machines virtuelles à protéger doit se situer dans un cloud VMM.<br/><br/>Si vous exécutez Hyper-V dans un cluster, notez que le service Broker de cluster n’est pas créé automatiquement si le cluster est basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. [En savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) sur l’entrée de blog d’Aidan Finn.
**Mappage réseau** | Vous pouvez configurer le mappage réseau pour vous assurer que les machines virtuelles répliquées sont placées de manière optimale sur les serveurs hôtes Hyper-V secondaires après le basculement et qu’elles peuvent se connecter aux réseaux de machines virtuelles appropriés. Si vous ne configurez pas de mappage réseau, les machines virtuelles de réplicas ne sont connectées à aucun réseau après le basculement.<br/><br/>Pour configurer le mappage réseau pendant le déploiement, assurez-vous que les machines virtuelles sur le serveur hôte Hyper-V source sont connectées à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.<br/<br/>Le cloud cible sur le serveur VMM secondaire que vous utilisez pour la récupération doit avoir un réseau de machines virtuelles correspondant configuré, qui lui-même doit être lié à un réseau logique correspondant associé au cloud cible.<br/><br/>[En savoir plus](site-recovery-network-mapping.md) sur le mappage réseau.
**Mappage de stockage** | Par défaut, quand vous répliquez une machine virtuelle sur un serveur hôte Hyper-V source vers un serveur hôte Hyper-V cible, les données répliquées sont stockées à l'emplacement par défaut indiqué pour l'hôte Hyper-V cible dans le Gestionnaire Hyper-V. Pour disposer d’un meilleur contrôle sur l’emplacement de stockage des données répliquées, vous pouvez configurer un mappage de stockage.<br/><br/> Pour cela, vous devez configurer des classifications de stockage sur les serveurs VMM source et cible avant de commencer le déploiement. [En savoir plus](site-recovery-storage-mapping.md).


## Étape 1 : Création d’un coffre Site Recovery

1. Connectez-vous au [Portail de gestion](https://portal.azure.com) à partir du serveur VMM à inscrire.

2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.

3. Cliquez sur **Créer nouveau** > **Création rapide**.

4. Dans **Name**, entrez un nom convivial pour identifier le coffre.

5. Dans **Région**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section Disponibilité géographique dans la page [Détails sur la tarification d'Azure Site Recovery](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Cliquez sur **Create vault**.

	![Créer le coffre](./media/site-recovery-vmm-to-vmm/create-vault.png)

Dans la barre d'état, vérifiez que le coffre a été créé. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.

## Étape 2 : Génération d’une clé d'inscription du coffre

Générez une clé d'inscription dans le coffre. Une fois que vous aurez téléchargé et installé le fournisseur Azure Site Recovery sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans le coffre.

1. Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Icône Quick Start](./media/site-recovery-vmm-to-vmm/quick-start-icon.png)

2. Dans la liste déroulante, sélectionnez **Entre deux sites VMM locaux**.
3. Dans **Préparer les serveurs VMM**, cliquez sur **Générer le fichier de clé d'inscription**. Le fichier de clé est généré automatiquement et est valide pendant cinq jours après sa création. Si vous n'accédez pas au portail Azure à partir du serveur VMM, vous devez copier ce fichier sur le serveur.

	![Clé d'enregistrement](./media/site-recovery-vmm-to-vmm/register-key.png)

## Étape 3 : Installation du fournisseur Azure Site Recovery

4. Sur la page **Quick Start**, dans **Prepare VMM servers**, cliquez sur **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** pour obtenir la dernière version du fichier d'installation du fournisseur.

2. Exécutez ce fichier sur le serveur VMM source.

	>[AZURE.NOTE] Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois, installez-le sur un nœud actif et terminez l'installation pour inscrire le serveur VMM dans le coffre. Ensuite, installez le fournisseur sur les autres nœuds. Notez que si vous mettez à niveau le fournisseur, vous devez le mettre à niveau sur tous les nœuds, car ils doivent tous exécuter la même version du fournisseur.

3. Le programme d’installation effectue quelques **vérifications de la configuration requise** et demande l’autorisation d’arrêter le service VMM pour commencer l’installation du fournisseur. Le service VMM redémarre automatiquement une fois l’installation terminée. Si vous installez sur un cluster VMM, vous serez invité à arrêter le rôle de cluster.

4. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

	![Mises à jour Microsoft](./media/site-recovery-vmm-to-vmm/ms-update.png)

5. L’emplacement de l’installation est défini sur **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Cliquez sur le bouton Installer pour démarrer l’installation du fournisseur.

	![Emplacement de l’installation](./media/site-recovery-vmm-to-vmm/install-location.png)

6. Une fois le fournisseur installé, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

	![Installation terminée](./media/site-recovery-vmm-to-vmm/install-complete.png)

7. Sur la page **Connexion Internet**, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Sélectionnez **Se connecter avec des paramètres de proxy existants** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

	![Paramètres Internet](./media/site-recovery-vmm-to-vmm/proxy-details.png)

	- Si vous souhaitez utiliser un proxy personnalisé, vous devez le configurer avant d'installer le fournisseur. Quand vous configurez les paramètres de proxy personnalisé, un test s'exécute pour vérifier la connexion proxy.
	- Si vous n'utilisez pas de proxy personnalisé ou si votre proxy par défaut nécessite une authentification, vous devez saisir les détails du proxy, y compris l'adresse du proxy et le port.
	- Les URL suivantes doivent être accessibles à partir du serveur VMMet des hôtes Hyper-V :
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Autorisez les adresses IP décrites dans la zone [Étendues d’adresses IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443). Vous devez autoriser les plages IP de la région Microsoft Azure que vous prévoyez d’utiliser, ainsi que celles de la région ouest des États-Unis.
	- Si vous utilisez un proxy personnalisé, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d'identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs VMM dans la console VMM. Pour cela, ouvrez l’espace de travail **Paramètres**, développez **Sécurité**, cliquez sur **Comptes d’identification**, puis modifiez le mot de passe de DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.

8. Dans **Registration Key**, indiquez que vous téléchargez depuis Azure Site Recovery et que vous copiez sur le serveur VMM.
9. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré. Cliquez sur *Next*.

	![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm/vault-creds.png)

10.  Le paramètre de chiffrement est uniquement utilisé quand vous répliquez des machines virtuelles Hyper-V dans des clouds VMM sur Azure. Si vous répliquez sur un site secondaire, il n’est pas utilisé.

	![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm/encrypt.png)

11.  Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
12.  Dans **Synchroniser les métadonnées du cloud**, indiquez si vous voulez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l’aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.

	![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm/friendly-name.png)

13.  Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l’onglet **Serveurs VMM** de la page **Serveurs** du coffre.


### Installation à partir de la ligne de commande

Le fournisseur Azure Site Recovery peut également être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur un module Server CORE pour Windows Server 2012 R2.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription dans un dossier, par exemple C:\\ASR.
2. Arrêter le service System Center Virtual Machine Manager
3. Extrayez le programme d’installation du fournisseur en exécutant les commandes ci-après à partir d’une invite de commandes avec des privilèges d’**administrateur** :

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installez le fournisseur en exécutant :

    	C:\ASR> setupdr.exe /i

5. Inscrivez le fournisseur en exécutant :

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

## Étape 4 : Configuration des paramètres de protection de cloud

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Si vous avez activé l’option **Synchroniser les données du cloud avec le coffre** pendant l’installation du fournisseur, tous les clouds du serveur VMM apparaissent sous l’onglet **Éléments protégés** du coffre. Si ce n’est pas le cas, vous pouvez synchroniser un cloud spécifique avec Azure Site Recovery sous l’onglet **Général** de la page des propriétés du cloud dans la console VMM.

![Cloud publié](./media/site-recovery-vmm-to-vmm/clouds-list.png)

1. Sur la page Démarrage rapide, cliquez sur **Set up protection for VMM clouds**.
2. Sous l’onglet **Clouds VMM**, sélectionnez le cloud à configurer, puis accédez à l’onglet **Configuration**.
3. Dans **Target**, sélectionnez **VMM**.
4. Dans **Target Location**, spécifiez le serveur VMM local qui gère le cloud à utiliser pour la récupération.
4. Dans **Target Cloud**, spécifiez le cloud cible à utiliser pour le basculement des machines virtuelles du cloud source. Notez les points suivants :

	- Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
	- Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.

5. Dans **Fréquence de copie**, spécifiez la fréquence de synchronisation des données entre les emplacements source et cible. Notez que ce paramètre est pertinent uniquement quand l'hôte Hyper-V exécute Windows Server 2012 R2. Les autres serveurs utilisent un paramétrage par défaut de cinq minutes.
6. Dans **Points de récupération supplémentaires**, spécifiez si vous souhaitez créer des points de récupération supplémentaires. La valeur par défaut (zéro) indique que seul le point de récupération le plus récent d'une machine virtuelle principale est stocké sur un serveur hôte de réplication. Notez que l'activation de plusieurs points de récupération nécessite un stockage supplémentaire pour les instantanés qui sont stockés à chaque point de récupération. Par défaut, des points de récupération sont créés toutes les heures, pour que chacun contienne une heure de données. La valeur de point de récupération que vous attribuez à la machine virtuelle dans la console VMM ne doit pas être inférieure à la valeur que vous attribuez dans la console Azure Site Recovery.
7. Dans **Fréquence des instantanés cohérents avec l’application**, spécifiez la fréquence de création des instantanés cohérents au niveau de l’application. Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle. Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés. Notez que si vous activez les instantanés cohérents avec l'application, cela affectera les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.

	![Configurer les paramètres de protection](./media/site-recovery-vmm-to-vmm/cloud-settings.png)

8. Dans **Compression du transfert de données**, indiquez si les données répliquées transférées doivent être compressées.
9. Dans **Authentication**, indiquez le mode d'authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. Sélectionnez HTTPS, sauf si vous avez configuré un environnement Kerberos opérationnel. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Aucune configuration manuelle n'est nécessaire. Si vous sélectionnez Kerberos, un ticket Kerberos est utilisé pour l'authentification mutuelle des serveurs hôtes. Par défaut, les ports 8083 et 8084 (pour les certificats) seront ouverts dans le Pare-feu Windows sur les serveurs hôtes Hyper-V. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
10. Dans **Port**, modifiez le numéro de port sur lequel les ordinateurs hôtes source et cible écoutent le trafic de réplication. Par exemple, vous pouvez modifier ce paramètre si vous souhaitez appliquer la limitation de bande passante réseau de Qualité de service (QoS) pour le trafic de réplication. Vérifiez que le port n'est utilisé par aucune autre application et qu'il est ouvert dans les paramètres du pare-feu.
11. Dans **Méthode de réplication**, indiquez comment sera traitée la réplication de données initiale entre les emplacements source et cible avant que la réplication normale ne démarre :

	- **Over network** : la copie de données sur le réseau peut demander du temps et des ressources. Nous vous recommandons d'utiliser cette option si le cloud contient des machines virtuelles dotées de disques durs virtuels relativement petits et si le site principal est connecté au site secondaire sur une connexion à large bande passante. Vous pouvez opter pour un démarrage immédiat de la copie ou sélectionner une heure. Si vous utilisez la réplication réseau, nous vous recommandons de la planifier sur des heures creuses.
	- **Offline** : cette méthode précise que la réplication initiale s'exécute à l'aide d'un support externe. Elle est indiquée si vous voulez éviter une dégradation des performances réseau ou pour les emplacements éloignés géographiquement. Pour utiliser cette méthode, il convient de spécifier l'emplacement d'exportation sur le cloud source et l'emplacement d'importation sur le cloud cible. Lorsque vous activez la protection pour une machine virtuelle, le disque dur virtuel est copié vers l'emplacement d'exportation spécifié. Vous l'envoyez vers le site cible et le copiez vers l'emplacement d'importation. Le système copie les informations importées vers les machines virtuelles de réplication.

12. Sélectionnez **Delete Replica Virtual Machine** pour indiquer que la machine virtuelle de réplication doit être supprimé si vous cessez de protéger la machine virtuelle en sélectionnant l'option **Delete protection for the virtual machine** dans l'onglet Virtual Machines des propriétés du cloud. Si vous activez ce paramètre et que vous désactivez la protection, la machine virtuelle est supprimée d'Azure Site Recovery, les paramètres Site Recovery de la machine virtuelle sont supprimés de la console VMM et le réplica est supprimé.

	![Configurer les paramètres de protection](./media/site-recovery-vmm-to-vmm/cloud-settings-replica.png)

Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet **Jobs**. Tous les serveurs hôte Hyper-V du cloud VMM source sont configurés pour la réplication. Les paramètres de cloud peuvent être modifiés sous l'onglet **Configure**. Pour modifier l'emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.

### Préparer la réplication initiale hors connexion

Vous devez effectuer les actions suivantes pour préparer la réplication initiale hors connexion :

- Sur le serveur source, vous allez spécifier un emplacement de chemin d'accès à partir duquel l'exportation des données aura lieu. Attribuez des autorisations de partage et de contrôle total pour NTFS au service VMM sur le chemin d'exportation. Sur le serveur cible, vous allez spécifier un emplacement de chemin d'accès à partir duquel l'importation des données aura lieu. Attribuez les mêmes autorisations sur ce chemin d'accès d'importation.
- Si le chemin d'accès d'importation ou d'exportation est partagé, attribuez l'appartenance au groupe Administrateur, Utilisateur avec pouvoir, Opérateur d'impression ou Opérateur de serveur pour le compte de service VMM sur l'ordinateur distant sur lequel le partage réside.
- Si vous utilisez des comptes d'identification pour ajouter des hôtes, sur les chemins d'accès d'importation et d'exportation, attribuez des autorisations de lecture et d'écriture aux comptes d'identification dans VMM.
- Les partages d'importation et d'exportation ne doivent pas se trouver sur un ordinateur utilisé comme serveur hôte Hyper-V, car la configuration en boucle n'est pas prise en charge par Hyper-V.
- Dans Active Directory, sur chaque serveur hôte Hyper-V qui contient des machines virtuelles à protéger, activez et configurez la délégation de contrainte pour approuver les ordinateurs distants sur lesquels se trouvent les chemins d'accès d'importation et d'exportation, comme suit :
	1. Sur le contrôleur de domaine, ouvrez **Utilisateurs et ordinateurs Active Directory**.
	2. Dans l’arborescence de la console, cliquez sur **NomDomaine** > **Ordinateurs**.
	3. Cliquez avec le bouton droit sur le nom du serveur hôte Hyper-V > **Propriétés**.
	4. Sous l’onglet **Délégation**, cliquez sur **N’approuver cet ordinateur que pour la délégation aux services spécifiés**.
	5. Cliquez sur **Utiliser tout protocole d’authentification**.
	6. Cliquez sur **Ajouter** > **Utilisateurs et ordinateurs**.
	7. Tapez le nom de l’ordinateur qui héberge le chemin d’exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l'ordinateur qui héberge le chemin d'accès d'importation. Répétez cette procédure pour les serveurs hôtes Hyper-V supplémentaires.

## Étape 5 : Configuration du mappage réseau
1. Dans la page Démarrage rapide, cliquez sur **Mapper les réseaux**.
2. Sélectionnez le serveur VMM source à partir duquel vous souhaitez mapper des réseaux, puis le serveur VMM cible auquel les réseaux seront mappés. La liste des réseaux sources et leurs réseaux cibles associés s'affiche à l'écran. Une valeur vide est indiquée pour les réseaux qui ne sont pas mappés.
3. Sélectionnez un réseau dans **Réseau sur la source** > **Mapper**. Le service détecte les réseaux de machines virtuelles sur le serveur cible et les affiche. Cliquez sur l'icône d'information en regard du nom des réseaux source et cible pour afficher les sous-réseaux de chaque réseau.

	![configurer le mappage réseau](./media/site-recovery-vmm-to-vmm/network-mapping1.png)

4. Dans la boîte de dialogue, sélectionnez l'un des réseaux de machines virtuelles du serveur VMM cible.

	![Sélectionner un réseau cible](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

5. Quand vous sélectionnez un réseau cible, les clouds protégés qui utilisent le réseau source sont affichés. Les réseaux cibles disponibles qui sont associés aux clouds utilisés pour la protection sont également affichés. Nous vous recommandons de sélectionner un réseau cible qui est accessible à tous les clouds utilisés pour la protection. Vous pouvez également accéder au serveur VMM et modifier les propriétés de cloud pour ajouter le réseau logique correspondant au réseau de machines virtuelles que vous souhaitez.
6. Cliquez sur la coche pour terminer le processus de mappage. Une tâche de suivi du processus de mappage démarre. Vous pouvez l'afficher dans l'onglet **Tâches**.


## Étape 6 : Configuration du mappage de stockage
Par défaut, quand vous répliquez une machine virtuelle sur un serveur hôte Hyper-V source vers un serveur hôte Hyper-V cible, les données répliquées sont stockées à l'emplacement par défaut indiqué pour l'hôte Hyper-V cible dans le Gestionnaire Hyper-V. Si vous souhaitez mieux contrôler l'emplacement de stockage des données de réplication, vous pouvez configurer des mappages de stockage comme suit :



1. Définissez des classifications de stockage sur les serveurs VMM source et cible. [En savoir plus](https://technet.microsoft.com/library/gg610685.aspx). Les classifications doivent accessibles aux serveurs hôtes Hyper-V dans les clouds source et cible. Il n'est pas obligatoire qu'elles aient le même type de stockage. Par exemple, vous pouvez mapper une classification source contenant des partages SMB à une classification cible contenant des volumes partagés de cluster.
2. Une fois les classifications en place, vous pouvez créer des mappages. Pour cela, dans la page **Démarrage rapide**, cliquez sur **Mapper le stockage**.
3. Cliquez sur l’onglet **Stockage** > **Mapper les classifications de stockage**.
4. Sous l’onglet **Mapper les classifications de stockage**, sélectionnez les classifications sur les serveurs VMM source et cible. Enregistrez vos paramètres.

	![Sélectionner un réseau cible](./media/site-recovery-vmm-to-vmm/storage-mapping.png)


## Étape 7 : Activation de la protection des machines virtuelles
Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud.

1. Sous l’onglet **Machines virtuelles** du cloud dans lequel se trouve la machine virtuelle, cliquez sur **Activer la protection** > **Ajouter des machines virtuelles**.
2. Dans la liste des machines virtuelles du cloud, sélectionnez celle que vous voulez protéger.

	![activer la protection des machines virtuelles](./media/site-recovery-vmm-to-vmm/enable-protection.png)

3. Suivez la progression de l'action d'activation de la protection, y compris la réplication initiale, dans l'onglet **Tâches**. Lorsque la tâche de finalisation de la protection s’exécute, la machine virtuelle est prête à être basculée. Une fois la protection activée et les machines virtuelles répliquées, celles-ci sont affichées dans Azure.

	![Tâche de protection de la machine virtuelle](./media/site-recovery-vmm-to-vmm/vm-jobs.png)

>[AZURE.NOTE] Vous pouvez également activer la protection des machines virtuelles dans la console VMM. Cliquez sur **Activer la protection** dans la barre d’outils sous l’onglet **Azure Site Recovery** dans les propriétés de la machine virtuelle.

### Intégrer des machines virtuelles existantes

Si vous avez des machines virtuelles existantes dans VMM qui sont répliquées à l’aide du réplica Hyper-V, vous devrez les intégrer à la protection Azure Site Recovery comme suit :

1. Vérifiez que vous avez un cloud principal et un cloud secondaire. Vérifiez que le serveur Hyper-V qui héberge la machine virtuelle se trouve dans le cloud principal et que le serveur Hyper-V qui héberge la machine virtuelle de réplication se trouve dans le cloud secondaire. Assurez-vous que vous avez configuré les paramètres de protection pour les clouds. Les paramètres doivent correspondre à ceux actuellement configurés pour la réplication Hyper-V. Sinon, la réplication de machine virtuelle risque de ne pas fonctionner comme prévu.
2. Ensuite, activez la protection de la machine virtuelle principale. Azure Site Recovery et VMM s'assureront que la même machine virtuelle et hôte de réplication seront détectés, et Azure Site Recovery réutilisera et rétablira la réplication à l'aide des paramètres configurés lors de la configuration du cloud.


## Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé.

### Créer un plan de récupération

1. Dans l'onglet **Plans de récupération**, cliquez sur **Créer un plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM source et cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source. Sélectionnez **Hyper-V** pour afficher uniquement les clouds configurés pour la réplication Hyper-V.

	![Créer un plan de récupération](./media/site-recovery-vmm-to-vmm/recovery-plan1.png)

3. Dans **Sélectionner la machine virtuelle**, sélectionnez les groupes de réplication. Toutes les machines virtuelles associées au groupe de réplication sont sélectionnées et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutées au groupe par défaut du plan de récupération, à savoir le groupe 1. Vous pouvez ajouter d'autres groupes si nécessaire. Notez qu'après la réplication, les machines virtuelles démarrent dans l'ordre des groupes du plan de récupération.

	![Ajouter des machines virtuelles](./media/site-recovery-vmm-to-vmm/recovery-plan2.png)

Une fois un plan de récupération créé, il s’affiche dans la liste de l'onglet **Plans de récupération**.

###Exécution d’un test de basculement

1. Dans l'onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
2. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Si cette option est activée, les machines virtuelles de réplication basculées ne sont connectées à aucun réseau. Le test vérifie que la machine virtuelle bascule comme prévu, mais il ne vérifie pas l'environnement de votre réseau de réplication. Découvrez comment [exécuter un test de basculement](site-recovery-failover.md#run-a-test-failover) pour plus d’informations sur l’utilisation des différentes options de réseau.
3. La machine virtuelle de test est créée sur le même hôte que celui sur lequel la machine virtuelle de réplication existe. Elle est ajoutée au cloud où se trouve la machine virtuelle de réplication.

### Exécuter un plan de récupération
Après la réplication, il est possible que la machine virtuelle de réplication ne possède pas d’adresse IP différente de celle de la machine virtuelle principale. Les machines virtuelles mettront à jour le serveur DNS qu'elles utilisent après leur démarrage. Vous pouvez également ajouter un script pour mettre à jour le serveur DNS et ainsi garantir une mise à jour en temps voulu.

#### Script pour récupérer l'adresse IP
Exécutez cet exemple de script pour récupérer l'adresse IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### Script pour mettre à jour le système DNS
Exécutez cet exemple de script pour mettre à jour DNS, en spécifiant l'adresse IP que vous avez récupérée à l'aide de l'exemple de script précédent.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Étapes suivantes

Après avoir exécuté un test de basculement pour vérifier que votre environnement fonctionne comme prévu, [découvrez](site-recovery-failover.md) les différents types de basculement.


## Informations de confidentialité pour Azure Site Recovery

Cette section fournit des informations supplémentaires sur la confidentialité pour le service Microsoft Azure Site Recovery (« Service »). Pour consulter la déclaration de confidentialité relative aux services Microsoft Azure, consultez la [Déclaration de confidentialité Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Fonctionnalité : Inscription**

- **Résultat** : inscrit le serveur auprès du service pour que les machines virtuelles puissent être protégées.
- **Informations recueillies** : après l’inscription, le Service recueille, traite et transmet les informations de certificat de gestion à partir du serveur VMM qui est désigné pour fournir la récupération d’urgence à l’aide du nom du Service du serveur VMM et du nom des clouds de machines virtuelles sur votre serveur VMM.
- **Utilisation des informations** :
	- Certificat de gestion : permet d'identifier et d'authentifier le serveur VMM inscrit pour l'accès au Service. Le Service utilise la portion de clé publique du certificat pour sécuriser un jeton auquel seul le serveur VMM inscrit a accès. Le serveur a besoin d'utiliser ce jeton pour accéder aux fonctionnalités du Service.
	- Nom du serveur VMM : ce nom est nécessaire pour identifier et communiquer avec le serveur VMM approprié sur lequel les clouds sont situés.
	- Noms de clouds du serveur VMM : les noms de clouds sont nécessaires en cas d'utilisation de la fonctionnalité de couplage/découplage de clouds du Service décrite ci-dessous. Quand vous décidez de coupler le cloud d'un centre de données principal avec un autre cloud du centre de données de récupération, les noms de tous les clouds du centre de données de récupération sont présentés.

- **Choix** : ces informations sont une part essentielle du processus d’inscription du Service dans la mesure où elles vous permettent, à vous et au Service, d’identifier le serveur VMM pour lequel fournir la protection Azure Site Recovery et d’identifier le serveur VMM inscrit approprié. Si vous ne souhaitez pas communiquer ces informations au Service, n'utilisez pas ce Service. Si vous inscrivez votre serveur et que vous décidez par la suite d'annuler son inscription, il vous suffit de supprimer les informations du serveur VMM depuis le portail du Service (c'est-à-dire, le portail Azure).

**Fonctionnalité : Activer la protection Azure Site Recovery**

- **Résultat** : le fournisseur Azure Site Recovery installé sur le serveur VMM est l’intermédiaire qui permet de communiquer avec le Service. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Une fois que le fournisseur est installé, la fonctionnalité « Datacenter Recovery » est activée dans la console Administrateur VMM. Les machines virtuelles nouvelles ou existantes d'un cloud peuvent activer une propriété appelée « Datacenter Recovery », qui assure leur protection. Une fois que cette propriété est définie, le fournisseur envoie le nom et l'ID de la machine virtuelle au Service. La protection virtuelle est activée par la technologie de réplication Hyper-V de Windows Server 2012 ou Windows Server 2012 R2. Les données des machines virtuelles sont répliquées d'un hôte Hyper-V vers un autre (généralement situé dans un autre centre de données de « récupération »).

- **Informations recueillies** : le Service collecte, traite et transmet les métadonnées de la machine virtuelle, à savoir, le nom, l’identifiant, le réseau virtuel et le nom du cloud auquel elle appartient.

- **Utilisation des informations** : le Service utilise ces informations pour compléter les informations de machine virtuelle sur votre portail Service.

- **Choix** : il s’agit d’un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, n'activez pas la protection Azure Site Recovery pour les machines virtuelles. Notez que toutes les données que le fournisseur transmet au Service sont envoyées via HTTPS.

**Fonctionnalité : Plan de récupération**

- **Résultat** : cette fonctionnalité vous permet de créer un plan d’orchestration pour le centre de données de « récupération ». Vous pouvez définir l'ordre de démarrage des machines virtuelles ou d'un groupe de machines virtuelles sur le site de récupération. Pour chaque machine virtuelle, vous pouvez aussi spécifier les scripts automatisés à exécuter ou l'action manuelle à entreprendre au moment de la récupération. Le basculement (décrit dans la section suivante) est généralement déclenché au niveau du plan de récupération pour une récupération coordonnée.

- **Informations recueillies** : le Service recueille, traite et transmet les métadonnées pour le plan de récupération, y compris les métadonnées des machines virtuelles et les métadonnées des éventuels scripts d’automatisation et notes d’actions manuelles.

- **Utilisation des informations** : les métadonnées décrites ci-dessus servent à générer le plan de récupération dans le portail du Service.

- **Choix** : il s’agit d’un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, ne créez pas de plans de récupération dans ce Service.

**Fonctionnalité : Mappage réseau**

- **Résultat** : cette fonctionnalité vous permet de mapper les informations de réseau du centre de données principal vers le centre de données de récupération. Dès lors que les machines virtuelles sont récupérées sur le site de récupération, ce mappage permet de leur établir une connectivité réseau.

- **Informations recueillies** : dans le cadre de la fonctionnalité de mappage réseau, le Service collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (principal et centre de données).

- **Utilisation des informations** : le Service utilise les métadonnées pour renseigner votre portail de Service, où vous pouvez mapper les informations réseau.

- **Choix** : il s'agit d'un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, n'utilisez pas la fonctionnalité de mappage réseau.

**Fonctionnalité : Basculement - planifié, non planifié et test**

- **Résultat** : cette fonctionnalité permet de faire basculer une machine virtuelle d’un centre de données géré VMM vers un autre. L'action de basculement est déclenchée par l'utilisateur sur son portail Service. Parmi les raisons possibles d'un basculement, citons un événement imprévu (par exemple, une catastrophe naturelle) ; un événement prévu (par exemple, l'équilibrage de charge d'un centre de données) ; un test de basculement (par exemple, la répétition d'un plan de récupération).

Le fournisseur du serveur VMM est averti de l'événement par le Service et exécute une action de basculement sur l'hôte Hyper-V via les interfaces VMM. Le basculement effectif de la machine virtuelle d'un hôte Hyper-V vers un autre (généralement exécuté dans un autre centre de données de « récupération ») est géré par la technologie de réplication Hyper-V de Windows Server 2012 ou Windows Server 2012 R2. À l'issue du basculement, le fournisseur installé sur le serveur VMM du centre de données de « récupération » envoie au Service des informations indiquant la réussite de l'opération.

- **Informations recueillies** : le Service utilise ces informations pour spécifier l'état des informations de l'action de basculement votre portail Service.

- **Utilisation des informations** : voici comment le Service utilise ces informations :

	- Certificat de gestion : permet d'identifier et d'authentifier le serveur VMM inscrit pour l'accès au Service. Le Service utilise la portion de clé publique du certificat pour sécuriser un jeton auquel seul le serveur VMM inscrit a accès. Le serveur a besoin d'utiliser ce jeton pour accéder aux fonctionnalités du Service.
	- Nom du serveur VMM : ce nom est nécessaire pour identifier et communiquer avec le serveur VMM approprié sur lequel les clouds sont situés.
	- Noms de clouds du serveur VMM : les noms de clouds sont nécessaires en cas d'utilisation de la fonctionnalité de couplage/découplage de clouds du Service décrite ci-dessous. Quand vous décidez de coupler le cloud d'un centre de données principal avec un autre cloud du centre de données de récupération, les noms de tous les clouds du centre de données de récupération sont présentés.

- **Choix** : il s’agit d’un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, n'utilisez pas ce Service.

<!---HONumber=AcomDC_0413_2016-->