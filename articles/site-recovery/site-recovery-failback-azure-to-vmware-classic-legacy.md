<properties 
   pageTitle="Restaurer des machines virtuelles VMware et des serveurs physiques d’Azure sur VMware (hérité) | Microsoft Azure" 
   description="Cet article décrit la restauration automatique d’une machine virtuelle VMware qui a été répliquée vers Azure à l’aide d’Azure Site Recovery." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="storage-backup-recovery" 
   ms.date="03/06/2016"
   ms.author="ruturajd@microsoft.com"/>

# Restaurer automatiquement des serveurs physiques et des machines virtuelles VMware à partir d’Azure vers VMware avec Azure Site Recovery (procédure héritée)

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-failback-azure-to-vmware.md)
- [Portail Azure Classic](site-recovery-failback-azure-to-vmware-classic.md)
- [Portail Azure Classic (version héritée)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour avoir un rapide aperçu, consultez la section [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md).

## Vue d'ensemble

Cet article décrit comment restaurer des machines virtuelles VMware et des serveurs physiques Windows/Linux à partir d’Azure sur votre site local après avoir opéré une réplication de votre site local sur Azure.

>[AZURE.NOTE] Cet article décrit un scénario hérité. Vous devez suivre les instructions de cet article uniquement si vous avez opéré une réplication sur Azure en suivant [ces instructions héritées](site-recovery-vmware-to-azure-classic-legacy.md). Si vous avez configuré la réplication à l’aide du [déploiement amélioré](site-recovery-vmware-to-azure-classic-legacy.md), suivez les instructions de [cet article](site-recovery-failback-azure-to-vmware-classic.md) pour opérer la restauration.


## Architecture

Le diagramme ci-dessous représente le scénario de basculement et de restauration automatique. Les lignes bleues représentent les connexions utilisées lors du basculement. Les lignes rouges représentent les connexions utilisées lors de la restauration automatique. Les lignes avec des flèches représentent les connexions vers Internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Avant de commencer 

- Vous devez avoir basculé vos machines virtuelles VMware ou serveurs physiques, et ils doivent être en cours d’exécution dans Azure.
- Sachez que vous pouvez uniquement restaurer des machines virtuelles VMware et des serveurs physiques Windows/Linux à partir d’Azure sur des machines virtuelles VMware situées sur le site principal local. Si vous effectuez la restauration automatique d’un ordinateur physique, le basculement vers Azure le convertit en machine virtuelle Azure, et la restauration automatique sur VMware le convertit en machine virtuelle VMware.

Voici comment configurer la restauration :

1. **Configuration des composants de restauration** : vous devez configurer un serveur vContinuum local et le faire pointer vers la machine virtuelle serveur de configuration dans Azure. Vous allez également configurer un serveur de traitement en tant que machine virtuelle Azure pour renvoyer des données au serveur cible maître local. Vous inscrivez le serveur de traitement auprès du serveur de configuration qui a géré le basculement. Vous installez un serveur cible maître local. Si vous avez besoin d’un serveur cible maître Windows, il est configuré automatiquement lorsque vous installez vContinuum. Si vous avez besoin de Linux, vous devrez le configurer manuellement sur un serveur distinct.
2. **Activation de la protection et de la restauration** : après avoir configuré les composants, dans vContinuum, vous devez activer la protection pour les machines virtuelles Azure basculées. Vous devez effectuer une vérification de disponibilité sur les machines virtuelles et opérer un basculement à partir d’Azure vers votre site local. Une fois la restauration terminée, vous protégez à nouveau les machines locales afin qu’elles démarrent la réplication sur Azure.



## Étape 1 : Installer le serveur vContinuum en local

Vous devez installer un serveur vContinuum en local et le pointer vers le serveur de configuration.

1.  [Téléchargez vContinuum](http://go.microsoft.com/fwlink/?linkid=526305).
2.  Téléchargez ensuite la version [mise à jour vContinuum](http://go.microsoft.com/fwlink/?LinkID=533813).
3. Installez la dernière version de vContinuum. Dans la page **d’accueil**, cliquez sur **Suivant**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Dans la première page de l’Assistant, spécifiez l’adresse IP et le port du serveur CX. Sélectionnez **Utiliser HTTPS**.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Recherchez l’adresse IP du serveur de configuration dans l’onglet **Tableau de bord** de la machine virtuelle du serveur de configuration dans Azure. ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Recherchez le port public HTTPS du serveur de configuration dans l’onglet **Points de terminaison** de la machine virtuelle du serveur de configuration dans Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Dans la page **Détails de la phrase secrète du serveur de configuration**, spécifiez la phrase secrète que vous avez notée lors de l’inscription du serveur de configuration. Si vous l’avez oubliée, recherchez-la dans **C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase** sur la machine virtuelle du serveur de configuration.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Dans la page **Sélectionner l’emplacement de destination**, indiquez où vous souhaitez installer le serveur vContinuum, puis cliquez sur **Installer**.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Une fois l’installation terminée, vous pouvez lancer vContinuum. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Étape 2 : Installer un serveur de processus dans Azure 

Vous devez installer un serveur de processus dans Azure pour que les machines virtuelles d’Azure puissent renvoyer les données sur un serveur cible maître local.

1.  Dans la page **Serveurs de configuration** d’Azure, effectuez votre sélection pour ajouter un nouveau serveur de processus.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Indiquez un nom de serveur de processus, puis un nom et un mot de passe pour vous connecter à la machine virtuelle en tant qu’administrateur. Sélectionnez le serveur de configuration sur lequel vous voulez inscrire le serveur de processus. Celui-ci doit être le même serveur que vous utilisez pour protéger et basculer vos machines virtuelles.
3.  Indiquez le réseau Azure dans lequel le serveur de processus doit être déployé. Celui-ci doit être le même réseau que celui du serveur de configuration. Indiquez une adresse IP unique dans le sous-réseau sélectionné et commencez le déploiement.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Une tâche est déclenchée pour déployer le serveur de processus.

	![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Une fois le serveur de processus déployé dans Azure, vous pouvez vous y connecter à l’aide des informations d’identification que vous avez spécifiées. Inscrivez le serveur de processus de la même façon que vous inscrit le serveur de processus local.

	![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Les serveurs inscrits pendant la restauration automatique ne sont pas visibles dans les propriétés de la machine virtuelle dans Site Recovery. Ils sont affichés uniquement dans l’onglet **Serveurs** du serveur de configuration auquel ils sont associés. L’affichage des serveurs de processus dans l’onglet peut prendre environ 10 à 15 minutes.


## Étape 3 : Installer un serveur cible maître local

En fonction du système d’exploitation des machines virtuelles sources dont vous disposez, vous devez installer en local un serveur cible maître Linux ou Windows.

### Déployer un serveur cible maître Windows

Un serveur cible maître Windows est d’ores et déjà fourni avec la configuration du serveur vContinuum. Lorsque vous installez le serveur vContinuum, un serveur maître est également déployé sur la même machine et inscrit sur le serveur de configuration.

1.  Pour commencer le déploiement, créez en local une machine vide sur l’hôte ESX sur lequel vous souhaitez récupérer les machines virtuelles d’Azure.

2.  Vérifiez qu’au moins deux disques sont attachés à la machine virtuelle, l’un pour le système d’exploitation et l’autre pour le lecteur de rétention.

3.  Installez le système d'exploitation.

4.  Installez le serveur vContinuum sur le serveur. Cette opération complète l’installation du serveur cible maître.

### Déployer un serveur cible maître Linux

1.  Pour commencer le déploiement, créez en local une machine vide sur l’hôte ESX sur lequel vous souhaitez récupérer les machines virtuelles d’Azure.

2.  Vérifiez qu’au moins deux disques sont attachés à la machine virtuelle, l’un pour le système d’exploitation et l’autre pour le lecteur de rétention.

3.  Installez le système d’exploitation Linux. Le système cible maître Linux ne doit pas utiliser LVM pour les espaces de stockage racine ou de rétention. Un serveur cible maître Linux est configuré pour éviter la découverte des disques/partitions LVM par défaut.
4.  Partitions que vous pouvez créer :

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Avant de démarrer l’installation du serveur cible maître, exécutez la procédure de post-installation ci-dessous.


#### Procédure de post-installation du système d’exploitation

Pour obtenir l’ID SCSI de chacun des disques durs SCSI d’une machine virtuelle Linux, activez le paramètre disk.EnableUUID = TRUE, comme suit :

1. Arrêtez votre machine virtuelle.
2. Cliquez avec le bouton droit sur l’entrée de la machine virtuelle dans le panneau gauche, puis sélectionnez **Modifier les paramètres**.
3. Cliquez sur l’onglet **Options**. Sélectionnez **Avancé > Général** > **Paramètres de configuration**. L’option **Paramètres de configuration** est disponible uniquement lorsque la machine est arrêtée.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Vérifiez si une ligne comportant **disk.EnableUUID** existe. Le cas échéant et si elle est définie sur **False**, définissez la sur **True** (non-respect de la casse). Si elle existe et qu’elle est définie sur true, cliquez sur **Annuler** et testez la commande SCSI dans le système d’exploitation invité après le démarrage. Si elle n’existe pas, cliquez sur **Ajouter une ligne**.
5. Dans la colonne **Nom**, ajoutez disk.EnableUUID. Définissez la valeur sur TRUE. N’ajoutez pas les valeurs ci-dessus avec des guillemets doubles.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Télécharger et installer les packages supplémentaires

Remarque : assurez-vous que le système dispose d’une connectivité Internet avant de télécharger et d’installer les packages supplémentaires.

# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Cette commande permet de télécharger ces 15 packages à partir du référentiel CentOS 6.6 et de les installer :

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm
	
perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

REMARQUE : si l’ordinateur source utilise un système de fichiers Reiser ou XFS pour l’appareil racine ou de démarrage, les packages suivants doivent être téléchargés et installés sur le système cible maître Linux avant la protection.

# cd /usr/local

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Appliquer des modifications de configuration personnalisées

Avant d’appliquer ces modifications, assurez-vous que vous avez terminé la section précédente, puis procédez comme suit :


1. Copiez le binaire de l’agent unifié RHEL (Red Hat Enterprise Linux) 6-64 sur le SE nouvellement créé.

2. Exécutez cette commande pour décompresser le fichier binaire : **tar -zxvf <nom du fichier>**.

3. Exécutez cette commande pour accorder des autorisations : **# chmod 755 ./ApplyCustomChanges.sh**.

4. Exécutez le script : **#./ApplyCustomChanges.sh**. Exécutez le script une seule fois sur le serveur. Redémarrez le serveur après l’exécution du script.



### Installer le serveur Linux


1. [Téléchargez](http://go.microsoft.com/fwlink/?LinkID=529757) le fichier d’installation.
2. Copiez le fichier sur la machine virtuelle du serveur cible maître Linux à l’aide d’un utilitaire de client sftp de votre choix. Vous pouvez également vous connecter à la machine virtuelle du serveur cible maître Linux et utiliser wget pour télécharger le package d’installation à partir du lien fourni.
3. Connectez-vous à la machine virtuelle du serveur cible maître Linux à l’aide d’un client ssh de votre choix.
4. Si vous êtes connecté au réseau Azure sur lequel est déployé votre serveur cible maître Linux via une connexion VPN, utilisez l’adresse IP interne du serveur que vous pouvez trouver dans l’onglet **Tableau de bord** de la machine virtuelle, ainsi que le port 22, pour vous connecter au serveur cible maître Linux à l’aide de Secure Shell.
5. Si vous vous connectez au serveur cible maître Linux via une connexion Internet publique, utilisez l’adresse IP virtuelle publique du serveur cible maître Linux (de l’onglet **Tableau de bord** des machines virtuelles) et le point de terminaison public créé pour ssh.
6. Extrayez les fichiers de l’archive tar du programme d’installation du serveur cible maître Linux compressée en exécutant *tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64\* dans le répertoire qui contient le fichier du programme d’installation.

	![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Si vous avez extrait les fichiers du programme d’installation dans un autre répertoire, utilisez le répertoire dans lequel le contenu de l’archive tar a été extrait. À partir de ce répertoire, exécutez « sudo ./install.sh ».

	![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Lorsque vous êtes invité à choisir un rôle principal, sélectionnez **2 (Master Target)**. Ne modifiez pas les autres valeurs par défaut des autres options d’installation.
9. Attendez que l’installation se poursuive jusqu’à l’affichage de la ligne Host Config Interface. L’utilitaire de configuration de l’hôte associé au serveur cible maître Linux est un utilitaire de ligne de commande. Ne redimensionnez pas la fenêtre de l’utilitaire du client ssh. Utilisez les touches de direction pour sélectionner l’option **Global**, puis appuyez sur ENTRÉE sur votre clavier.

	![](./media/site-recovery-failback-azure-to-vmware/image18.png)

	![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Dans le champ **IP**, entrez l’adresse IP interne du serveur de configuration (vous pouvez le trouver dans l’onglet **Tableau de bord** de la machine virtuelle du serveur de configuration), puis appuyez sur ENTRÉE. Dans le champ **Port**, entrez la valeur **22**, puis appuyez sur ENTRÉE.
11.  Laissez le champ **Use HTTPS** défini sur **Yes**, puis appuyez sur ENTRÉE.
12.  Entrez la phrase secrète générée sur le serveur de configuration. Si vous utilisez un client PuTTY pour connecter un ordinateur Windows via ssh à la machine virtuelle Linux, appuyez sur les touches Maj + Insertion pour coller le contenu du Presse-papiers. Pour copier la phrase secrète sur le presse-papiers local, appuyez sur les touches Ctrl-C et exécutez Maj + Insertion pour la coller. Appuyez sur ENTRÉE.
13.  Utilisez la touche Droite pour quitter, puis appuyez sur ENTRÉE. Attendez la fin de l’installation.

	![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si pour une raison quelconque, l’inscription du serveur cible maître Linux sur le serveur de configuration échoue, vous pouvez réessayer en exécutant l’utilitaire de configuration de l’hôte à partir de l’emplacement /usr/local/ASR/Vx/bin/hostconfigcli (vous devez tout d’abord définir des autorisations d’accès à ce répertoire en exécutant chmod en tant que super utilisateur).


#### Valider l’inscription du serveur cible maître

Vous pouvez valider l’inscription du serveur cible maître sur le serveur de configuration dans Coffre Azure Site Recovery > **Serveur de configuration** > **Détails du serveur**.

>[AZURE.NOTE] Après avoir inscrit le serveur cible maître, si vous recevez des erreurs de configuration indiquant que la machine virtuelle a peut-être été supprimée d’Azure ou que les points de terminaison ne sont pas configurés correctement, c’est parce que la configuration du serveur cible maître est détectée par les points de terminaison Azure lorsque le serveur cible maître est déployé dans Azure, même si cela n’est pas vrai pour un serveur cible maître local. Cela n’affecte pas la restauration automatique, et vous pouvez ignorer ces erreurs.



## Étape 4 : Protéger les machines virtuelles sur le site local

Vous devez protéger les machines virtuelles sur le site local avant de procéder à la restauration automatique.

### Avant de commencer

Le basculement d’une machine virtuelle vers Microsoft Azure ajoute un lecteur temporaire associé au fichier de page. Il s’agit d’un lecteur supplémentaire qui n’est généralement pas requis par votre machine virtuelle basculée, dans la mesure où bien souvent, elle présente déjà un lecteur dédié au fichier de page. Avant de commencer à inverser la protection des machines virtuelles, vérifiez que le lecteur n’est plus en ligne, et qu’il n’est ainsi plus protégé. Procédez comme suit :

1.  Ouvrez Gestion de l’ordinateur, puis sélectionnez Gestion du stockage, de manière à ce que les disques en ligne soient répertoriés et associés à la machine.
2.  Sélectionnez le disque temporaire attaché à la machine, et configurez sa mise hors ligne.

### Protéger les machines virtuelles

1. Sur le portail Azure, vérifiez l’état des machines virtuelles afin de vous assurer de leur basculement.

	![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Lancez vContinuum sur votre machine.

	![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Cliquez sur **Nouvelle protection** et sélectionnez le type de système d’exploitation.

4.  Dans la nouvelle fenêtre qui s’ouvre, sélectionnez **Type de SE** > **Obtenir les détails** pour les machines virtuelles que vous souhaitez restaurer automatiquement. Dans **Détails du serveur principal**, identifiez et sélectionnez les machines virtuelles que vous souhaitez protéger. Les machines virtuelles sont répertoriées sous le nom d’hôte vCenter sur lequel elles se trouvaient avant le basculement.
5.  Lorsque vous sélectionnez une machine virtuelle à protéger (et qu’elle est déjà basculée vers Azure), une fenêtre indépendante indiquant deux entrées associées s’affiche. Ceci est dû au fait que le serveur de configuration détecte deux instances de machines virtuelles inscrites. Vous devez supprimer l’entrée de machine virtuelle sur site, de manière à pouvoir protéger la machine virtuelle appropriée. Pour identifier l’entrée appropriée de machine virtuelle Microsoft Azure, connectez-vous à la machine virtuelle Microsoft Azure, puis accédez à C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc. Dans le fichier drscout.conf, identifiez l’ID d’hôte. Dans la boîte de dialogue vContinuum, conservez l’entrée associée à l’ID d’hôte sur la machine virtuelle. Supprimez toutes les autres entrées. Pour sélectionner la machine virtuelle appropriée, vérifiez son adresse IP. La plage d’adresses IP locale sera la machine virtuelle locale.

	![](./media/site-recovery-failback-azure-to-vmware/image22.png)

	![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Sur le serveur vCenter, arrêtez la machine virtuelle. Vous pouvez également supprimer les machines virtuelles locales.
7. Spécifiez alors le serveur cible maître local sur lequel vous souhaitez protéger les machines virtuelles. Pour ce faire, connectez-vous au serveur vCenter sur lequel vous souhaitez effectuer la restauration automatique.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Sélectionnez le serveur cible maître basé sur l’hôte sur lequel vous souhaitez récupérer la machine virtuelle.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Indiquez l’option de réplication pour chacune des machines virtuelles. Pour ce faire, vous devez sélectionner la banque de données du site de récupération sur laquelle les machines virtuelles seront récupérées. Le tableau ci-dessous récapitule les différentes options que vous devez fournir pour chaque machine virtuelle.

	![](./media/site-recovery-failback-azure-to-vmware/image25.png)

	**Option** | **Valeur recommandée d’option**
	---|---
	Adresse IP du serveur de processus | Sélectionnez le serveur de processus déployé dans Azure.
	Retention size in MB| 
	Retention value | 1
	Days/Hours | Jours
	Intervalle de cohérence | 1
	Sélectionner une banque de données cible | Banque de données disponible sur le site de récupération. La banque de données doit contenir suffisamment d’espace et être disponible sur l’hôte ESX sur lequel vous souhaitez restaurer la machine virtuelle.


10. Configurez les propriétés acquises par la machine virtuelle après le basculement sur le site local. Les propriétés sont récapitulées dans le tableau ci-dessous.

	![](./media/site-recovery-failback-azure-to-vmware/image26.png)

	**Propriété** | **Détails**
	---|---
	Configuration réseau| Sélectionnez chaque carte réseau détectée et cliquez sur **Modifier** pour configurer l’adresse IP de restauration automatique de la machine virtuelle. 
	Hardware Configuration| Spécifiez l’UC et la mémoire de la machine virtuelle. Les paramètres peuvent être appliqués à toutes les machines virtuelles que vous essayez de protéger. Pour identifier les valeurs appropriées des paramètres UC et Mémoire, référez-vous à la taille de rôle des machines virtuelles IAAS afin de connaître le nombre de cœurs et le volume de mémoire alloués.
	Nom complet | Après la restauration automatique sur le site local, vous pouvez renommer les machines virtuelles telles qu’elles apparaissent dans l’inventaire de vCenter. Le nom par défaut est le nom d’hôte de la machine virtuelle. Pour identifier le nom de la machine virtuelle, consultez la liste du groupe Protection.
	NAT Configuration | Cette propriété est abordée en détail ci-dessous.

	![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### Configurer des paramètres NAT

1. Pour activer la protection des machines virtuelles, vous devez établir deux canaux de communication. Le premier canal doit être défini entre la machine virtuelle et le serveur de processus. Ce canal collecte les données de la machine virtuelle et les envoie au serveur de processus qui les envoie à son tour au serveur cible maître. Si le serveur de processus et la machine virtuelle à protéger sont sur le même réseau virtuel Azure, il n’est pas nécessaire de recourir aux paramètres NAT. Dans le cas contraire, spécifiez les paramètres NAT. Affichez l’adresse IP publique du serveur de processus dans Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Le second canal est défini entre le serveur de processus et le serveur cible maître. Devez-vous recourir aux paramètres NAT ? Cela dépend si vous utilisez une connexion VPN ou si vous communiquez via Internet. Ne sélectionnez pas NAT si vous utilisez un réseau VPN, mais uniquement si vous utilisez une connexion Internet.

	![](./media/site-recovery-failback-azure-to-vmware/image29.png)

	![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Si vous n’avez pas supprimé les machines virtuelles locales comme indiqué et si la banque de données cible de la restauration automatique contient toujours les anciens fichiers VMDK, vous devez alors vous assurer que la machine virtuelle restaurée automatiquement est créée à un nouvel emplacement. Pour ce faire, sélectionnez **Paramètres avancés** et spécifiez un autre dossier cible de restauration dans **Paramètres de nom de dossier**. Conservez les paramètres par défaut des autres options. Appliquez les paramètres de nom de dossier à l’ensemble des serveurs.

	![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Exécutez une vérification de la disponibilité pour vous assurer que les machines virtuelles sont prêtes à être protégées en local.

	![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Patientez jusqu’à la fin de son exécution. Si elle est fructueuse sur toutes les machines virtuelles, vous pouvez spécifier un nom pour le plan de protection, puis cliquer sur **Protéger** pour commencer.

	![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Vous pouvez surveiller la progression dans vContinuum.

	![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. À l’issue de l’étape **Activation d’un plan de protection**, vous pouvez surveiller la protection des machines virtuelles sur le portail Site Recovery.

	![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Vous pouvez observer l’état exact en cliquant sur la machine virtuelle et en surveillant sa progression.

	![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Préparer le plan de restauration automatique

Vous pouvez préparer un plan de restauration automatique à l’aide de vContinuum afin de pouvoir restaurer automatiquement l’application sur le site local, à tout moment. Ces plans de récupération sont très similaires aux plans de récupération de Site Recovery.

1.  Lancez vContinuum et sélectionnez **Gérer des plans** > **Récupérer**. Vous pouvez voir la liste de tous les plans qui ont été utilisés pour le basculement des machines virtuelles. Vous pouvez utiliser ces mêmes plans pour la récupération.

	![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Sélectionnez le plan de protection et l’ensemble des machines virtuelles que vous souhaitez récupérer par son biais. Lorsque vous sélectionnez chaque machine virtuelle, vous pouvez voir plus de détails, notamment le serveur ESX cible et le disque de machine virtuelle source. Cliquez sur **Suivant** pour lancer l’Assistant Récupération, puis sélectionnez les machines virtuelles à récupérer.

	![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Vous pouvez procéder à une récupération en utilisant plusieurs options, mais nous vous recommandons d’utiliser **Dernière balise** et de sélectionner **Appliquer pour toutes les machines virtuelles** pour vous assurer que les dernières données de la machine virtuelle seront utilisées.
4. Exécutez la vérification de disponibilité (**Readiness Check**). Elle vérifie si les paramètres appropriés sont configurés pour la récupération des machines virtuelles. Cliquez sur **Suivant** si toutes les vérifications sont réussies. Si tel n’est pas le cas, vérifiez le journal et résolvez les erreurs.

	![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Dans **Configuration de la machine virtuelle**, assurez-vous de la bonne définition des paramètres de récupération. Vous pouvez modifier les paramètres de la machine virtuelle au besoin.

	![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Passez en revue la liste des machines virtuelles à récupérer et spécifiez un ordre de récupération. Notez que les machines virtuelles sont répertoriées à l’aide du nom d’hôte de l’ordinateur. Il peut s’avérer difficile de mapper le nom d’hôte d’ordinateur à la machine virtuelle. Pour mapper les noms, accédez au **Tableau de bord** des machines virtuelles dans Azure et recherchez le nom d’hôte de machine virtuelle.

	![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Spécifiez un nom de plan et sélectionnez **Récupérer plus tard**. Nous vous recommandons de procéder à une récupération ultérieure, car la protection initiale risque de ne pas être terminée.
11. Cliquez sur **Récupérer** pour enregistrer le plan ou déclencher la récupération si vous avez choisi de procéder à une récupération maintenant et pas plus tard. Vous pouvez vérifier le statut de la récupération pour voir si le plan a été enregistré.

	![](./media/site-recovery-failback-azure-to-vmware/image42.png)

	![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Récupérer les machines virtuelles

Une fois le plan créé, vous pouvez récupérer les machines virtuelles. Au préalable, vous devez vous assurer que les machines virtuelles ont terminé la synchronisation. Si l’état de réplication présente la mention OK, cela signifie que la protection est achevée et que le seuil d’objectif de point de récupération est atteint. Vous pouvez vérifier l’intégrité dans les propriétés des machines virtuelles.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Avant de lancer la récupération, arrêtez les machines virtuelles Azure. Cela permet de s’assurer de l’absence de Split-Brain et que les utilisateurs auront accès uniquement à une copie de l’application.


1.  Démarrez le plan enregistré. Dans vContinuum, sélectionnez **Surveiller** les plans. Cette option répertorie tous les plans qui ont été exécutés.

	![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Sélectionnez le plan dans **Récupération**, puis cliquez sur **Démarrer**. Vous pouvez surveiller la récupération. Une fois que les machines virtuelles ont été activées, vous pouvez vous y connecter dans vCenter.

	![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## Protéger de nouveau les machines virtuelles sur Azure après une restauration automatique

Une fois la restauration automatique effectuée, vous souhaiterez probablement rétablir la protection des machines virtuelles. Procédez comme suit :

1.  Vérifiez que les machines virtuelles en local fonctionnent et que les applications sont joignables.
2.  Sur le portail Azure Site Recovery, sélectionnez les machines virtuelles, puis supprimez-les. Désactivez la protection des machines virtuelles. Vous êtes ainsi certain que les machines virtuelles ne sont plus protégées.
3.  Dans Azure, supprimez les machines virtuelles Azure basculées.
4.  Supprimez les anciennes machines virtuelles sur vSphere. Il s’agit des machines virtuelles préalablement basculées vers Azure.
5.  Sur le portail Site Recovery, protégez les machines virtuelles récemment basculées. Une fois qu’elles sont protégées, vous pouvez les ajouter à un plan de récupération.
 
## Étapes suivantes



- [En savoir plus sur](site-recovery-vmware-to-azure-classic.md) la réplication de machines virtuelles VMware et de serveurs physiques sur Azure à l’aide d’un déploiement amélioré.

 

<!---HONumber=AcomDC_0629_2016-->