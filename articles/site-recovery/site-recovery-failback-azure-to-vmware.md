<properties 
   pageTitle="Procédure de restauration automatique de Microsoft Azure vers VMware" 
   description="Cet article décrit comment Microsoft Azure Site Recovery et l’outil vContinuum peuvent être utilisés pour restaurer automatiquement une machine virtuelle sur VMware." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="05/27/2015"
   ms.author="ruturajd@microsoft.com"/>

# Procédure de restauration automatique de Microsoft Azure vers VMware

Ce document vous explique la procédure de restauration automatique de Microsoft Azure vers votre site VMware. Vous devez déjà avoir suivi les étapes décrites dans le didacticiel [Configurer la protection entre des machines virtuelles ou des serveurs physiques VMware locaux et Azure](site-recovery-vmware-to-azure.md).

Une fois qu’un basculement a été effectué sur Microsoft Azure, les machines virtuelles sont disponibles dans l’onglet dédié. Si vous décidez de procéder à une restauration automatique, suivez la procédure ci-dessous.

Si vous effectuez une restauration automatique de Microsoft Azure vers votre site VMware, l’opération peut être exécutée uniquement sur une machine virtuelle. Même si votre source initiale sur VMware était une machine physique, un basculement sur Microsoft Azure suivi par une restauration automatique vers VMware la convertira en machine virtuelle.

## Vue d'ensemble

1.  Installez le serveur vContinuum en local.

    a. Faites-le pointer vers le serveur de configuration.

2.  Déployez un serveur de traitement sur Microsoft Azure.

3.  Installez un serveur maître cible en local.

4.  Procédure de protection des machines virtuelles basculées en local

    a. Considérations relatives à la configuration

5.  Surveillance de la protection des machines virtuelles basculées en local

6.  Basculement en local des machines virtuelles

Vous trouverez ci-dessous la présentation de la configuration effectuée via la procédure suivante. La configuration a d’ores et déjà été partiellement effectuée pendant le basculement.

-   Les lignes bleues représentent les connexions utilisées lors du basculement.

-   Les lignes rouges représentent les connexions utilisées lors de la restauration automatique.

-   Les lignes avec des flèches représentent les connexions vers Internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Installer en local le serveur vContinuum

La configuration vContinuum est disponible à l’adresse [emplacement de téléchargement](http://go.microsoft.com/fwlink/?linkid=526305).

Installez également le correctif fourni ici sur le serveur vContinuum, disponible à l’adresse [emplacement de téléchargement](http://go.microsoft.com/fwlink/?LinkID=533813).

1.  Lancez la configuration afin de démarrer l’installation du serveur vContinuum. Une fois sur l’écran d’accueil, cliquez sur Next pour commencer à définir les paramètres.

![](./media/site-recovery-failback-azure-to-vmware/image2.png)

2.  Spécifiez l’adresse IP et le port du serveur CX. Veillez à sélectionner HTTPS dans la case à cocher.

![](./media/site-recovery-failback-azure-to-vmware/image3.png)

    a.  To discover the CX IP go to the CS deployment on Azure and view
        its dashboard. The public IP address will be displayed under
        Public Virtual IP address.

![](./media/site-recovery-failback-azure-to-vmware/image4.png)

    b.  To discover the CX public port go to the endpoints tab in the VM
        page and identify the HTTPs endpoints public port

![](./media/site-recovery-failback-azure-to-vmware/image5.png)

3.  Spécifiez la phrase secrète du serveur de configuration. Vous devez avoir noté la phrase secrète lors de l’inscription du serveur de configuration. Vous auriez également eu recours à la phrase secrète lors des déploiements du serveur maître cible et du serveur de traitement. Si vous ne vous souvenez pas de la phrase secrète, accédez au serveur de configuration sur Microsoft Azure et recherchez-la à l’emplacement « C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase ».

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

4.  Spécifiez l’emplacement d’installation du serveur vContinuum, puis démarrez l’installation.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

5.  Une fois l’installation terminée, lancez le serveur vContinuum pour observer son fonctionnement.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

## Installer le serveur de traitement sur Microsoft Azure

Pour que les machines virtuelles de Microsoft Azure puissent renvoyer les données sur le serveur maître cible en local, un serveur de traitement doit être installé sur Microsoft Azure. Dans Microsoft Azure, le serveur de traitement doit être déployé sur le réseau du serveur de configuration.

1.  Sur la page des serveurs de configuration de Microsoft Azure, effectuez votre sélection pour ajouter un serveur de traitement ![](./media/site-recovery-failback-azure-to-vmware/image9.png).

2.  Sur le serveur de traitement à déployer, configurez les paramètres ci-dessous.

    a. Attribuez un nom au serveur de traitement.

    b. Entrez un nom d’utilisateur afin de vous connecter en tant qu’administrateur à la machine virtuelle.

    c. Entrez le mot de passe de connexion.

    d. Sélectionnez le serveur de configuration auquel le serveur de traitement doit être inscrit. Assurez-vous de sélectionner le serveur de configuration approprié. Il s’agit du serveur utilisé pour la protection et le basculement de vos machines virtuelles.

    e. Spécifiez le réseau Microsoft Azure dans lequel déployer le serveur de traitement. Assurez-vous d’avoir sélectionné le réseau du serveur de configuration.

    f. Spécifiez une adresse IP unique à partir du sous-réseau sélectionné.

    g. Démarrez le déploiement du serveur de traitement.

![](./media/site-recovery-failback-azure-to-vmware/image10.png)

1.  Une tâche de déploiement du serveur de traitement sera déclenchée.

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

Une fois que le serveur de traitement est déployé sur Microsoft Azure, vous pouvez vous y connecter à l’aide des informations d’identification définies. Pour inscrire le serveur de traitement, appliquez la procédure utilisée pour la mise en œuvre de la protection.

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

Les serveurs inscrits durant la restauration automatique ne sont pas visibles dans les propriétés de machine virtuelle. Ils sont affichés uniquement dans l’onglet Serveurs, sous le serveur de configuration auquel ils sont associés.

L’inscription du serveur de traitement sous le serveur de configuration peut prendre entre 10 et 15 minutes.

## Installer un serveur maître cible en local

En fonction des machines virtuelles côté source dont vous disposez, vous devez installer en local un serveur maître cible Linux ou Windows.

### Déployer un serveur maître cible Windows

Un serveur maître cible est d’ores et déjà fourni avec la configuration du serveur vContinuum. Lorsque vous installez le serveur vContinuum, un serveur maître cible est également déployé sur la même machine et inscrit sur le serveur de configuration.

1.  Pour commencer le déploiement, créez en local une machine vide sur l’hôte ESX sur lequel vous souhaitez récupérer les machines virtuelles de Microsoft Azure.

2.  Vérifiez qu’au moins deux disques sont attachés à la machine virtuelle (un pour le SE et l’autre pour le lecteur de rétention).

3.  Installez le système d'exploitation.

4.  Installez le serveur vContinuum sur le serveur. Cette action terminera l’installation du serveur maître cible.

### Déployer un serveur maître cible Linux

1.  Pour commencer le déploiement, créez en local une machine vide sur l’hôte ESX sur lequel vous souhaitez récupérer les machines virtuelles de Microsoft Azure.

2.  Vérifiez qu’au moins deux disques sont attachés à la machine virtuelle (un pour le SE et l’autre pour le lecteur de rétention).

3.  Installez le système d’exploitation Linux.

    a. REMARQUE : le système maître cible Linux ne doit pas utiliser LVM pour les espaces de stockages racines ou de rétention. Le serveur maître cible Linux est configuré pour éviter la découverte des disques/partitions LVM par défaut.

    b. Les partitions que vous créez sont ![](./media/site-recovery-failback-azure-to-vmware/image13.png).

4.  Avant de démarrer l’installation du serveur maître cible, exécutez la procédure de post-installation ci-dessous.

#### Procédure de post-installation du système d’exploitation

Pour obtenir l’ID SCSI de chacun des disques durs SCSI d’une machine virtuelle Linux, activez le paramètre disk.EnableUUID = TRUE.

Pour activer ce paramètre, suivez la procédure ci-dessous :

a. Arrêtez votre machine virtuelle.

b. Cliquez avec le bouton droit sur l’entrée de machine virtuelle du panneau de gauche, puis sélectionnez **Modifier les paramètres.**

c. Cliquez sur l’onglet **Options**.

d. Sélectionnez l’**élément Avancé>Général** sur la gauche, puis cliquez sur les **Paramètres de configuration** affichés sur la droite.

![](./media/site-recovery-failback-azure-to-vmware/image14.png)

L’option Paramètres de configuration est inactive lorsque l’ordinateur est en cours d’exécution. Pour rendre cet onglet actif, arrêtez l’ordinateur.

e. Vérifiez si une ligne comportant **disk.EnableUUID** existe.

Si cette valeur existe et qu’elle est définie sur False, remplacez ce paramétrage par True (ces éléments sont sensibles à la casse).

Si cette valeur existe et qu’elle est définie sur true, cliquez sur Annuler et testez la commande SCSI du système d’exploitation invité après le démarrage.

f. Si elle n’existe pas, cliquez sur **Ajouter des lignes.**

Dans la colonne Nom, ajoutez disk.EnableUUID.

Affectez-lui la valeur TRUE.

Remarque : n’ajoutez pas les valeurs ci-dessus avec des guillemets doubles.

![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Télécharger et installer les packages supplémentaires

Remarque : assurez-vous que le système dispose d’une connectivité Internet avant de télécharger et d’installer les packages supplémentaires.

# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

La commande ci-dessus téléchargera les 15 packages mentionnés ci-dessous à partir du référentiel CentOS 6 et procèdera à leur installation.

bc-1.06.95-1.el6.x86_64.rpm

busybox-1.15.1-20.el6.x86_64.rpm

elfutils-libs-0.158-3.2.el6.x86_64.rpm

kexec-tools-2.0.0-280.el6.x86_64.rpm

lsscsi-0.23-2.el6.x86_64.rpm

lzo-2.03-3.1.el6_5.1.x86_64.rpm

perl-5.10.1-136.el6_6.1.x86_64.rpm

perl-Module-Pluggable-3.90-136.el6_6.1.x86_64.rpm

perl-Pod-Escapes-1.04-136.el6_6.1.x86_64.rpm

perl-Pod-Simple-3.13-136.el6_6.1.x86_64.rpm

perl-libs-5.10.1-136.el6_6.1.x86_64.rpm

perl-version-0.77-136.el6_6.1.x86_64.rpm

rsync-3.0.6-12.el6.x86_64.rpm

snappy-1.1.0-1.el6.x86_64.rpm

wget-1.12-5.el6_6.1.x86_64.rpm

REMARQUE : si l’ordinateur source utilise un système de fichiers Reiser ou XFS pour l’appareil racine ou de démarrage, les packages suivants doivent être téléchargés et installés sur le système maître cible Linux avant la protection.

# cd /usr/local

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm

# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### Appliquer les modifications de configuration personnalisée

Avant d’appliquer les modifications de configuration personnalisée, assurez-vous d’avoir exécuté la procédure de post-installation.

Pour appliquer les modifications de configuration personnalisée, procédez comme suit :

1. Copiez le binaire de l’agent unifié RHEL (Red Hat Enterprise Linux) 6-64 sur le SE nouvellement créé.

2. Exécutez la commande tar ci-dessous pour désarchiver le fichier binaire.

**tar -zxvf <Nom fichier>**

3. Exécutez la commande ci-dessous pour attribuer l’autorisation.

# **chmod 755 ./ApplyCustomChanges.sh**

4. Lancez la commande ci-dessous, puis exécutez le script.

**# ./ApplyCustomChanges.sh**

REMARQUE : exécutez le script une seule fois sur le serveur. **Redémarrez** le serveur une fois le script ci-dessus exécuté.

### Démarrer l’installation du serveur maître cible

[Téléchargez](http://go.microsoft.com/fwlink/?LinkID=529757) le fichier d’installation du serveur maître cible Linux.

Copiez le programme d’installation du serveur maître cible Linux téléchargé sur la machine virtuelle du serveur maître cible Linux à l’aide d’un utilitaire de client sftp de votre choix. Vous pouvez également vous connecter à la machine virtuelle du serveur maître cible Linux et utiliser wget pour télécharger le package d’installation à partir du lien fourni.

Connectez-vous à la machine virtuelle du serveur maître cible à l’aide d’un client ssh de votre choix.

Si vous êtes connecté au réseau Microsoft Azure sur lequel est déployé votre serveur maître cible Linux via une connexion VPN, utilisez l’adresse IP interne associée au serveur maître cible Linux et obtenue à partir du tableau de bord de la machine virtuelle, ainsi que le port 22, pour vous connecter au serveur maître cible Linux à l’aide de Secure Shell.

Si vous vous connectez au serveur maître cible Linux via une connexion Internet publique, utilisez l’adresse IP virtuelle publique du serveur maître cible Linux (de la page du tableau de bord des machines virtuelles) et le point de terminaison public créé pour ssh.

Décompressez les fichiers de l’archive tar du programme d’installation compressé du serveur maître cible Linux en exécutant

*“tar –xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64*”* dans le répertoire hébergeant le programme d’installation du serveur maître cible.

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

Si vous avez auparavant décompressé les fichiers du programme d’installation dans un répertoire différent, modifiez le chemin de manière à les placer dans le répertoire contenant l’archivage tar. À partir de ce répertoire, exécutez sudo ./install.sh

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

Lorsque vous êtes invité à choisir le rôle principal de cet agent, sélectionnez 2 (maître cible).

Ne modifiez pas les autres valeurs par défaut des autres options d’installation.

Attendez que l’installation soit terminée et que la ligne de commande Host Config s’affiche. L’utilitaire de configuration de l’hôte associé au serveur maître cible Linux est un utilitaire de ligne de commande. Ne redimensionnez pas la fenêtre de l’utilitaire du client ssh. Utilisez les touches de direction pour sélectionner l’option Global, puis appuyez sur la touche Entrée de votre clavier.

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  Dans le champ IP, entrez l’adresse IP interne du serveur de configuration obtenue à partir de la page du tableau de bord des machines virtuelles, puis appuyez sur Entrée.

2.  Dans le champ Port, entrez la valeur 22, puis appuyez sur Entrée.

3.  Laissez l’option Utiliser HTTPS définie sur Oui. Appuyez de nouveau sur la touche Entrée.

4.  Entrez la phrase secrète générée sur le serveur de configuration. Si vous utilisez le client PuTTY pour connecter un ordinateur Windows via ssh à la machine virtuelle du serveur maître cible Linux, appuyez sur les touches Maj + Insertion pour coller le contenu du Presse-papiers. Pour copier la phrase secrète sur le presse-papiers local, appuyez sur les touches Ctrl-C et exécutez Maj + Insertion pour la coller. Appuyez sur Entrée.

5.  Utilisez la touche Droite pour quitter, puis appuyez sur Entrée.

Attendez que l'installation se termine.

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si pour une raison quelconque l’inscription de votre serveur maître cible Linux échoue, réessayez en exécutant l’utilitaire de configuration de l’hôte à partir de l’emplacement /usr/local/ASR/Vx/bin/hostconfigcli (il vous faut tout d’abord définir les autorisations d’accès à ce répertoire en exécutant chmod en tant que superutilisateur).


#### Valider l’inscription du serveur maître cible sur le serveur de configuration

Pour valider l’inscription du serveur maître cible sur le serveur de configuration, accédez à la page Détails du serveur, accessible via la page Serveur de configuration du coffre Azure Site Recovery.


## Commencer la protection des machines virtuelles basculées en local

Avant de restaurer automatiquement les machines virtuelles basculées en local, il vous faudra d’abord assurer leur protection. Pour protéger les machines virtuelles d’une application, procédez comme suit.

### Remarque sur le lecteur temporaire

Le basculement d’une machine virtuelle vers Microsoft Azure ajoute un lecteur temporaire associé au fichier de page. Il s’agit d’un lecteur supplémentaire qui n’est généralement pas requis par votre machine virtuelle basculée, dans la mesure où bien souvent, elle présente déjà un lecteur dédié au fichier de page.

Avant de commencer à inverser la protection des machines virtuelles, vérifiez que le lecteur n’est plus en ligne, et qu’il n’est ainsi plus protégé.

Pour ce faire,

1.  Ouvrez Gestion de l’ordinateur (via l’outil d’administration du panneau de configuration ou en cliquant avec le bouton droit sur Ce PC dans la fenêtre de l’Explorateur, puis sur Gérer.)

2.  Sélectionnez Gestion du stockage, de manière à ce que les disques soient répertoriés en ligne et associés à la machine.

3.  Sélectionnez le disque temporaire attaché à la machine, et configurez sa mise hors ligne.

4.  Une fois qu’il a été mis hors ligne, vous pouvez poursuivre l’inversion de la protection de la machine virtuelle.

### Plan de protection pour les machines virtuelles

Sur le portail Microsoft Azure, examinez l’état des machines virtuelles afin de vous assurer de leur basculement.

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

Remarque : durant le basculement de Microsoft Azure vers le système local, la machine virtuelle Microsoft Azure est considérée similaire à une machine virtuelle physique. Un basculement vers le système local est exécuté vers une machine virtuelle.

1.  Lancez le serveur vContinuum sur votre machine.

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

1.  Dans le paramètre **Choose Application**, sélectionnez **P2V**.

2.  Pour commencer, cliquez sur l’option **New Protection**.

3.  Dans la fenêtre qui s’ouvre, vous allez commencer à protéger les machines virtuelles basculées en local.

    a. Dans **OS type**, sélectionnez le type de système d’exploitation en fonction des machines virtuelles que vous souhaitez basculer, puis cliquez sur **Get Details**.

    b. Dans **Primary server details**, vous devez identifier les machines virtuelles que vous souhaitez protéger.

    c. Les machines virtuelles sont répertoriées suivant leur nom d’hôte d’ordinateur, non en fonction de leur visibilité sur vCenter ou Microsoft Azure.

    d. Les machines virtuelles sont répertoriées sous le nom d’hôte vCenter qui les hébergeait avant le basculement.

    e. Une fois que vous avez identifié les machines virtuelles que vous souhaitez protéger, sélectionnez-les une par une.

4.  Lorsque vous sélectionnez une machine virtuelle à protéger (et qu’elle est déjà basculée vers Microsoft Azure), une fenêtre contextuelle indiquant deux entrées associées s’affiche. Ceci est dû au fait que le serveur de configuration a détecté deux instances de machines virtuelles inscrites. Vous devez supprimer l’entrée de machine virtuelle sur site, de manière à pouvoir protéger la machine virtuelle appropriée. Notez que les entrées s’affichent en fonction du nom d’hôte d’ordinateur. Pour identifier l’entrée appropriée de machine virtuelle Microsoft Azure, connectez-vous à la machine virtuelle Microsoft Azure, puis accédez à C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc. Dans le fichier drscout.conf, identifiez l’ID d’hôte. Dans la boîte de dialogue vContinuum, conservez l’entrée associée à l’ID d’hôte dans la machine virtuelle. Supprimez toutes les autres entrées.

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    a.  To select the correct VM – you can refer to its IP address. The
        IP address range on-premises will be the on-premises VM.

    b.  Click **Remove** to delete the entry

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

    c.  Go to the vCenter and stop the virtual machine on the vCenter

    d.  Next you can also delete the virtual machines on-premises

5.  Vous devez ensuite spécifier le serveur maître cible local sur lequel vous souhaitez protéger les machines virtuelles.

    a. Connectez-vous au serveur vCenter sur lequel vous souhaitez effectuer la restauration rapide.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

a. Sélectionnez le serveur maître cible basé sur l’hôte sur lequel vous souhaitez récupérer les machines virtuelles.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

1.  Fournissez ensuite l’option de réplication pour chacune des machines virtuelles.

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

a. Vous devez sélectionner le côté de récupération **Datastore**. Il s’agit de la banque de données sur laquelle les machines virtuelles seront récupérées.

Les différentes options que vous devez fournir par machine virtuelle sont les suivantes :

<table>
<tr><td>Option</td><td>Valeur recommandée d’option</td></tr>
<tr><td>Process Server IP</td><td>Sélectionnez le serveur de traitement déployé sur Microsoft&#160;Azure</td></tr>
<tr><td>Retention size in MB</td><td></td></tr>
<tr><td>Retention value</td><td>1</td></tr>
<tr><td>Days/Hours</td><td>Jours</td></tr>
<tr><td>Consistency Interval</td><td>1</td></tr>
<tr><td>Select Target Datastore</td><td>La banque de données disponible sur le côté de récupération. La banque de données doit présenter suffisamment d’espace et être disponible sur l’hôte&#160;ESX sur lequel vous souhaitez déployer la machine virtuelle.</td></tr>
</table>


1.  Vous pouvez ensuite configurer les propriétés acquises par la machine virtuelle après le basculement sur le site local. Les différentes propriétés à configurer sont les suivantes :

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


  <table>
<tr><td>Propriété</td><td>Méthode de configuration</td></tr>
<tr><td>Network Configuration</td><td>Pour chaque NIC détectée, configurez l’adresse&#160;IP de basculement associée à la machine virtuelle. Sélectionnez la NIC, puis cliquez sur **Change** pour définir les détails de l’adresse&#160;IP.

</td></tr>
<tr><td>Hardware Configuration</td><td>Vous pouvez spécifier les valeurs CPU&#160;(UC) et Memory&#160;(mémoire) associées à la machine virtuelle. Ce paramètre peut être appliqué à toutes les machines virtuelles que vous essayez de protéger. Pour identifier les valeurs appropriées pour CPU et Memory, référez-vous à la taille de rôle des machines virtuelles&#160;IAAS afin de connaître le nombre de cœurs et le volume de mémoire alloués.
</td></tr>
<tr><td>Display Name</td><td>Après le basculement en local, vous pouvez définir le nouveau nom d’affichage des machines virtuelles dans l’inventaire de vCenter. Notez que la valeur par défaut affichée ici est le nom d’hôte de la machine virtuelle. Pour identifier le nom de la machine virtuelle, consultez la liste du groupe&#160;Protection.</td></tr>
<tr><td>NAT Configuration</td><td>Cette propriété est abordée en détail ci-dessous.</td></tr>
</table>

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **Sélection des paramètres PS NAT**
>
> Pour activer la protection des machines virtuelles, vous devez établir deux canaux de communication.
>
> Le premier canal est à définir entre la machine virtuelle et le serveur de traitement. Ce canal recueille les données de la machine virtuelle, avant de les envoyer vers le serveur de traitement. Ce dernier transmet les données au serveur maître cible. Si le serveur de traitement et la machine virtuelle à protéger sont sur le même réseau virtuel Microsoft Azure, il n’est pas nécessaire de recourir aux paramètres NAT. Si le serveur de traitement et la machine virtuelle à protéger sont sur deux réseaux virtuels différents, vous devez définir les paramètres NAT pour le serveur de traitement, puis sélectionner la première option.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> Pour identifier l’adresse IP publique du serveur de traitement, accédez au déploiement de ce serveur dans Microsoft Azure.
>
> Le deuxième canal est défini entre le serveur de traitement et le serveur maître cible. Devez-vous recourir aux paramètres NAT ? Cela dépend si vous utilisez une connexion VPN entre le serveur maître cible et le serveur de traitement ou si la protection est assurée via Internet. Si le serveur de traitement interagit avec le serveur maître cible via une connexion VPN, ne sélectionnez pas cette option. SI le lien est établi via une connexion Internet, définissez les paramètres NAT pour le serveur de traitement.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> Pour identifier l’adresse IP publique du serveur de traitement, accédez au déploiement de ce serveur dans Microsoft Azure.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  Si vous n’avez pas supprimé les machines virtuelles en local, tel que définie à l’étape 5.d et si la banque de données cible de la restauration automatique, tel que défini à l’étape 7.A contient toujours les anciens fichiers VMDK, vous devez également vous assurer que la machine virtuelle restaurée automatiquement est créée à un nouvel emplacement. Pour ce faire, sélectionnez l’option Advanced Settings, puis spécifiez un dossier cible de restauration différent dans la section **Folder Name Settings** de la fenêtre Advanced Settings.

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

Les valeurs par défaut des autres options de cette fenêtre peuvent être conservées. Assurez-vous d’appliquer les paramètres de nom de dossier à l’ensemble des serveurs.

1.  Ensuite, accédez à l’étape finale de la Protection. Vous devez ici exécuter une vérification de la disponibilité afin de contrôler que les machines virtuelles sont prêtes à être protégées en local.

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


1.  La Protection commence.

    a. Vous pouvez voir la progression de la protection sur le serveur vContinuum.

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

Pour afficher l’état exact des paires, cliquez sur la machine virtuelle et surveillez sa progression dans la section dédiée à la réplication de volume.

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Préparer le plan de restauration automatique

Vous pouvez préparer un plan de restauration automatique à l’aide de vContinuum, de manière à pouvoir restaurer automatiquement l’application en local, à tout moment. Ces plans de récupération sont très similaires aux plans de récupération automatique de système.

1.  Lancez vContinuum, puis sélectionnez l’option **Manage plans**.

2.  Sélectionnez **Recover** dans les sous-options.

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

1.  Vous pouvez afficher la liste de tous les plans utilisés pour protéger les machines virtuelles. Ces plans peuvent également servir à la restauration.

2.  Sélectionnez le plan de protection, puis l’ensemble des machines virtuelles que vous souhaitez récupérer par son biais.

    a. En sélectionnant chacune des machines virtuelles, vous pouvez afficher des détails sur la machine virtuelle source, le serveur ESX cible dans lequel la machine virtuelle sera récupérée et le disque source de la machine virtuelle.

3.  Pour démarrer l’assistant **Recover**, cliquez sur Next.

4.  Sélectionnez les machines virtuelles que vous souhaitez récupérer.

    a. Consultez la liste des machines virtuelles que vous pouvez récupérer.

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


1.  Exécutez la vérification de disponibilité (**Readiness Check**). Elle vous indiquera si les paramètres appropriés sont définis pour activer la dernière récupération de balises de la machine virtuelle. Si toutes les vérifications sont réussies, cliquez sur Next. Sinon, étudiez le journal et résolvez les erreurs.

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

2.  Dans l’étape de configuration de machine virtuelle de l’assistant, assurez-vous de la bonne définition des paramètres de récupération. Si les paramètres de machine virtuelle sont différents de ceux requis, vous pouvez les modifier. Comme nous avons déjà exécuté cette action durant la protection, vous pouvez l’ignorer ici.

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

1.  Enfin, parcourez la liste des machines virtuelles à récupérer.

    a. Spécifiez un ordre de récupération des machines virtuelles.

Remarque : les machines virtuelles sont répertoriées par nom d’hôte d’ordinateur. Il peut s’avérer difficile de mapper le nom d’hôte d’ordinateur sur la machine virtuelle. Pour mapper les noms, accédez au tableau de bord des machines virtuelles dans Azure IAAS, et étudiez le nom d’hôte de la machine virtuelle.

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

1.  Attribuez le nom dans le champ **Recovery plan name**, puis sélectionnez **Recover later** dans **Recovery options.**

    a. Si vous souhaitez procéder immédiatement à la récupération, sélectionnez **Recover now** sous **Recovery options**.

    b. Il est recommandé de sélectionner l’option Recovery Later, dans la mesure où la réplication initiale de la protection n’est pas obligatoirement terminée.

    c. Enfin, cliquez sur le bouton **Recover** afin d’enregistrer le plan ou de déclencher la récupération en fonction du paramétrage de **Recovery options**.

2.  En affichant l’état de récupération, vous pouvez vérifier l’enregistrement du plan.

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

1.  Si vous avez choisi de procéder ultérieurement à la récupération, vous serez informé de la création du plan et de la possibilité de récupération ultérieure.

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Récupérer les machines virtuelles

Une fois le plan créé, vous pouvez décider de récupérer les machines virtuelles. Au préalable, vous devez vous assurer que les machines virtuelles ont terminé la synchronisation.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Si l’état de réplication présente la mention OK, cela signifie que la protection est achevée et que le seuil d’objectif de point de récupération est atteint. Pour confirmer l’intégrité de la paire de réplication, accédez aux propriétés de la machine virtuelle.

**Avant de lancer la récupération, arrêtez les machines virtuelles Microsoft Azure. Ainsi, vous être certain d’évacuer tout risque de syndrome Split-Brain, et vos clients profiteront d’une copie de l’application. Une fois que vous êtes sûr que les machines virtuelles Microsoft Azure sont arrêtées, suivez la procédure suivante.**

Pour démarrer la récupération en local des machines virtuelles, vous devez démarrer le plan enregistré.

1.  Dans l’outil vContinuum, sélectionnez l’option **Monitor**, dédiée à la surveillance des plans.

2.  L’assistant répertorie les plans exécutés jusqu’à présent.

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

1.  Sélectionnez le nœud **Recovery**, puis le plan que vous souhaitez récupérer.

    a. Il vous indiquera que le plan n’a pas encore démarré.

2.  Pour démarrer la récupération, cliquez sur **Start**.

3.  Vous pouvez surveiller la récupération des machines virtuelles.


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

4. Une fois que les machines virtuelles ont été mises sous tension, vous pouvez vous y connecter sur votre serveur vCenter.

## Protéger de nouveau sur Microsoft Azure après une restauration automatique

Une fois que la restauration rapide a été effectuée, vous pouvez souhaiter rétablir la protection des machines virtuelles. Pour reconfigurer la protection, procédez comme suit.

1.  Vérifiez que les machines virtuelles en local fonctionnent et que l’application peut interagir avec vos clients finaux.

2.  Dans le portail Microsoft Azure Site Recovery, sélectionnez les machines virtuelles, puis supprimez-les.

    a. Désactivez la protection des machines virtuelles. Ainsi, vous êtes certains que les machines virtuelles ne sont plus protégées.

3.  Accédez aux machines virtuelles Azure IAAS et supprimez les éléments basculés.

4.  Supprimez les anciennes machines virtuelles sur vSphere. Il s’agit des machines virtuelles préalablement basculées vers Microsoft Azure.

5.  Sur le portail de récupération automatique du système, activez la protection des machines virtuelles récemment basculées.

6.  Une fois que les machines virtuelles sont protégées, vous pouvez les ajouter à un plan de récupération et définir leur protection continue.


 

<!---HONumber=July15_HO2-->