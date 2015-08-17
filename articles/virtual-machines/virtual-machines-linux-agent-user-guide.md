<properties 
	pageTitle="Guide d'utilisation de l'agent Linux pour Azure" 
	description="Apprenez à installer et à configurer l'agent Linux (waagent) pour gérer l'interaction de votre machine virtuelle avec le contrôleur de structure Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/30/2015" 
	ms.author="szark"/>



#Guide d'utilisateur de l'agent Linux Azure

##Introduction

L’agent Linux Azure (/usr/sbin/waagent) gère l’interaction entre la machine virtuelle et le contrôleur de structure Microsoft Azure. Il effectue les opérations suivantes :

* **Approvisionnement d’image**
  - Création d'un compte d'utilisateur
  - Configuration des types d'authentification SSH
  - Déploiement des clés publiques et des paires de clés SSH
  - Définition du nom d'hôte
  - Publication du nom d'hôte sur la plateforme DNS
  - Génération de rapports sur l'empreinte digitale de la clé d'hôte SSH destinés à la plateforme
  - Gestion du disque de ressources
  - Formatage et montage du disque de ressources
  - Configuration de l'espace d'échange
* **Mise en réseau**
  - Gestion des itinéraires afin d'améliorer la compatibilité avec les serveurs DHCP de plateforme
  - Garantie de la stabilité du nom de l'interface réseau
* **Noyau**
  - Configuration de l'architecture NUMA virtuelle
  - Consommation de l'entropie Hyper-V pour /dev/random
  - Configuration des délais d'expiration SCSI de l'appareil racine (qui peut être distant)
* **Diagnostics**
  - Redirection de la console vers le port série
* **Déploiements SCVMM**
    - Détection et amorçage de l'agent VMM pour Linux lors de l'exécution dans un environnement System Center Virtual Machine Manager 2012 R2
* **Extension de machine virtuelle**
    - Injection de composants créés par Microsoft et ses partenaires dans la machine virtuelle Linux pour activer les logiciels et l’automatisation de la configuration
    - Implémentation de référence de l’extension de machine virtuelle à l’adresse [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


##Communication

Le flux d'informations de la plateforme à l'agent se produit via deux canaux :

* Un DVD attaché au moment du démarrage pour les déploiements IaaS. Ce DVD comprend un fichier de configuration compatible OVF qui inclut toutes les informations d'approvisionnement différentes des paires de clés SSH réelles.

* Un point de terminaison TCP qui expose une API REST utilisée pour obtenir la configuration du déploiement et de la topologie.

###Obtention de l'agent Linux
Vous pouvez obtenir l'agent Linux le plus récent directement :

- [auprès des différents fournisseurs de distribution approuvés Linux sur Azure ;](http://support.microsoft.com/kb/2805216)
- ou sur le référentiel[ Open Source Github pour l’agent Linux Azure.](https://github.com/Azure/WALinuxAgent)


## Configuration requise
Les systèmes suivants ont été testés et fonctionnent avec l’agent Linux Azure. **Notez que cette liste peut être différente de la liste officielle des systèmes pris en charge sur la plateforme Microsoft Azure **, tel que décrit ici : [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

###Distributions de Linux prises en charge

* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Autres systèmes pris en charge :

* FreeBSD 9 + (agent Linux Azure v2.0.10 +)


L’agent Linux repose sur certains packages système pour fonctionner correctement :

* Python 2.6+
* Openssl 1.0+
* Openssh 5.3+
* Utilitaires de système de fichiers : sfdisk, fdisk, mkfs, séparés
* Outils de mot de passe : chpasswd, sudo
* Outils de traitement de texte : sed, grep
* Outils réseau : ip-route


##Installation

L’installation à l’aide d’un package RPM ou DEB à partir de votre référentiel de packages de distribution est la méthode privilégiée pour installer et mettre à niveau l’agent Microsoft Linux Azure.

En cas d’installation manuelle, le script waagent doit être copié sur /usr/sbin/waagent et installé en exécutant :

	# sudo chmod 755 /usr/sbin/waagent
	# sudo /usr/sbin/waagent -install -verbose

Le fichier journal de l'agent est conservé sur /var/log/waagent.log.


##Options de la ligne de commande

###Indicateurs

- verbose : accroît le niveau de détail de la commande spécifiée.
- force : ignore la confirmation interactive de certaines commandes.

###Commandes

- help : répertorie les commandes et les indicateurs pris en charge.

- install : installation manuelle de l’agent.
 * Vérifie le système en termes de dépendances requises.

 * Crée le script SysV init (/etc/init.d/waagent), le fichier de configuration logrotate (/etc/logrotate.d/waagent) et configure l'image pour exécuter le script init au démarrage.

 * Écrit l'exemple de fichier de configuration sur /etc/waagent.conf.

 * Tout fichier de configuration existant est déplacé sur /etc/waagent.conf.old.

 * Détecte la version du noyau et applique la solution de contournement VNUMA si nécessaire.

 * Déplace les règles udev qui peuvent interférer avec la mise en réseau (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) sur /var/lib/waagent/.

- uninstall : supprime waagent et les fichiers associés.
 * Annule l'enregistrement du script init du système et le supprime.

 * Supprime le fichier de configuration logrotate et le fichier de configuration waagent dans /etc/waagent.conf.

 * Restaure les règles udev qui ont été déplacées au cours de l'installation.

 * Le rétablissement automatique de la solution de contournement VNUMA n'est pas pris en charge. Veuillez éditer manuellement les fichiers de configuration GRUB afin de réactiver NUMA si nécessaire.

- deprovision : essaie de nettoyer le système et de le préparer pour le réapprovisionnement. Cette opération supprime les éléments suivants :
 * toutes les clés de l'hôte SSH (si Provisioning.RegenerateSshHostKeyPair a la valeur « y » dans le fichier de configuration) ;

 * la configuration de Nameserver dans /etc/resolv.conf ;

 * le mot de passe racine de /etc/shadow (si Provisioning.DeleteRootPassword a la valeur « y » dans le fichier de configuration) ;

 * les baux du client DHCP mis en cache.

 * Réinitialise le nom d'hôte sur localhost.localdomain.

 **Avertissement :** l’annulation de l’approvisionnement ne garantit pas que l’image est exempte de toute information sensible et qu’elle convient pour la redistribution.

- deprovision+user : effectue tout ce qui est décrit sous -deprovision (ci-dessus) et supprime également le dernier compte d’utilisateur approvisionné (obtenu à partir de /var/lib/waagent) et les données associées. Ce paramètre est utilisé pour annuler l'approvisionnement d'une image qui a été précédemment approvisionnée sur Azure en vue d'être capturée et réutilisée.

- version : affiche la version de waagent.

- serialconsole : configure GRUB pour marquer ttyS0 (premier port série) en tant que console de démarrage. Les journaux de démarrage du noyau sont ainsi envoyésau port série et sont prêts à faire l'objet d'un débogage.

- daemon : exécute waagent en tant que démon afin de gérer l’interaction avec la plateforme. Cet argument est spécifié à waagent dans le script waagent init.

##Configuration

Un fichier de configuration (/etc/waagent.conf) contrôle les actions de waagent. Un exemple de fichier de configuration est affiché ci-dessous :
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

Les diverses options de configuration sont décrites de manière détaillée ci-dessous. Elles sont de trois types : Boolean, String ou Integer. Les options de configuration Boolean peuvent être spécifiées à l'aide de « y » (oui) ou « n » (non). Le mot clé « None » (Aucun) peut être utilisé dans le cas de certaines entrées de type chaîne comme décrit ci-dessous.

**Role.StateConsumer :**

Type : String Valeur par défaut : Aucun

Si le chemin d'un programme exécutable est spécifié, il est appelé lorsque waagent a approvisionné l'image et que l'état « Ready » (Prêt) est sur le point d'être signalé à la structure. L'argument spécifié au programme est « Ready ». L'agent n'attend pas le retour du programme pour continuer.

**Role.ConfigurationConsumer :**

Type : String Valeur par défaut : Aucun

Si le chemin d'un programme exécutable est spécifié, le programme est appelé lorsque la structure signale qu'un fichier de configuration est disponible pour la machine virtuelle. Le chemin du fichier de configuration XML est fourni en tant qu'argument à l'exécutable. Il peut être appelé chaque fois que le fichier de configuration est modifié. Un exemple de fichier est fourni dans l'Annexe. Le chemin actuel de ce fichier est /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer :**

Type : String Valeur par défaut : Aucun

Si le chemin d'un programme exécutable est spécifié, le programme est appelé lorsque la structure signale qu'une nouvelle configuration de topologie de réseau est disponible pour la machine virtuelle. Le chemin du fichier de configuration XML est fourni en tant qu'argument à l'exécutable. Il peut être appelé chaque fois que la topologie de réseau est modifiée (en raison d'une réparation du service par exemple). Un exemple de fichier est fourni dans l'Annexe. Le chemin actuel de ce fichier est /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled :**

Type : Boolean Valeur par défaut : y

L'utilisateur peut activer ou désactiver la fonctionnalité d'approvisionnement dans l'agent. Les valeurs valides sont « y » ou « n ». Si l'approvisionnement est désactivé, les clés d'utilisateur et d'hôte SSH dans l'image sont conservées et toute configuration spécifiée dans l'API d'approvisionnement Azure est ignorée.

**Remarque** : La valeur par défaut de ce paramètre est « n » dans les images Cloud Ubuntu qui utilisent cloud-init pour l’approvisionnement.

**Provisioning.DeleteRootPassword :**

Type : Boolean Valeur par défaut : n

Si elle est définie, le mot de passe racine dans le fichier /etc/shadow est effacé au cours du processus d'approvisionnement.

**Provisioning.RegenerateSshHostKeyPair :**

Type : Boolean Valeur par défaut : y

Si elle est définie, toutes les paires de clés d'hôte SSH (ecdsa, dsa et rsa) sont supprimées de /etc/ssh/ au cours du processus d'approvisionnement. Une nouvelle paire de clés unique est générée.

L'entrée Provisioning.SshHostKeyPairType peut configurer le type de chiffrement pour la nouvelle paire de clés. Veuillez noter que certaines distributions créent à nouveau les paires de clés SSH pour tout type de chiffrement manquant au redémarrage du démon SSH.

**Provisioning.SshHostKeyPairType :**

Type : String Valeur par défaut : rsa

Un type d'algorithme de chiffrement qui est pris en charge par le démon SSH sur la machine virtuelle peut être défini. Les valeurs généralement prises en charge sont « rsa », « dsa » et « ecdsa ». Notez que « putty.exe » sur Windows ne prend pas en charge « ecdsa ». Si vous envisagez d'utiliser putty.exe sur Windows pour établir une connexion sur un déploiement Linux, veuillez utiliser « rsa » ou « dsa ».

**Provisioning.MonitorHostName :**

Type : Boolean Valeur par défaut : y

Si elle est définie, waagent surveille la machine virtuelle Linux en vue de détecter des modifications de nom d'hôte (comme renvoyé par la commande « hostname ») et met automatiquement à jour la configuration de mise en réseau dans l'image afin de refléter la modification. Afin de transmettre la modification de nom aux serveurs DNS, la mise en réseau est redémarrée sur la machine virtuelle. La connexion Internet est alors brièvement interrompue.

**ResourceDisk.Format :**

Type : Boolean Valeur par défaut : y

Si elle est définie, le disque de ressources fourni par la plateforme est formaté et monté par waagent si le type de système de fichiers demandé par l'utilisateur dans « ResourceDisk.Filesystem » est différent de « ntfs ». Une partition unique de type Linux (83) est mise à la disposition sur le disque. Notez que cette partition n'est pas formatée si elle ne peut pas être correctement montée.

**ResourceDisk.Filesystem :**

Type : String Valeur par défaut : ext4

Cette commande spécifie le type de système de fichiers pour le disque de ressources. Les valeurs prises en charge diffèrent selon la distribution Linux. Si la chaîne est X, mkfs.X doit être présent sur l'image Linux. Les images SLES 11 doivent généralement utiliser « ext3 ». Les images FreeBSD doivent utiliser « ufs2 » ici.

**ResourceDisk.MountPoint :**

Type : String Valeur par défaut : /mnt/resource

Cette commande spécifie le chemin où le disque de ressources est monté. Notez que le disque de ressources est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle.

**ResourceDisk.EnableSwap :**

Type : Boolean Valeur par défaut : n

Si elle est définie, un fichier d'échange (/swapfile) est créé sur le disque de ressources et est ajouté à l'espace d'échange système.

**ResourceDisk.SwapSizeMB :**

Type : Integer Valeur par défaut : 0

Taille du fichier d'échange en mégaoctets.

**LBProbeResponder :**

Type : Boolean Valeur par défaut : y

Si elle est définie, waagent répond aux sondes d'équilibrage de charge à partir de la plateforme (en cas de présence).

**Logs.Verbose :**

Type : Boolean Valeur par défaut : n

Si elle est définie, le niveau de détail du journal est optimisé. Waagent enregistre dans /var/log/waagent.log et exploite la fonctionnalité logrotate du système pour faire tourner les journaux.

**OS.RootDeviceScsiTimeout :**

Type : Integer Valeur par défaut : 300

Le délai d'expiration SCSI est configuré en secondes sur le disque du système d'exploitation et les lecteurs de données. Si elle n'est pas définie, les valeurs par défaut du système sont utilisées.

**OS.OpensslPath :**

Type : String Valeur par défaut : Aucun

Cette commande sert à spécifier un autre chemin pour les données binaires openssl à utiliser pour les opérations de chiffrement.



##Images cloud Ubuntu

Notez que les images cloud Ubuntu utilisent [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) pour exécuter les tâches de configuration qui pourraient être gérées par l’agent Linux Azure. Notez les différences suivantes :


- **Provisioning.Enabled** est défini par défaut sur n sur les images cloud Ubuntu utilisant Cloud-init pour exécuter les tâches d’approvisionnement.

- Les paramètres de configuration suivants n’ont aucun effet sur les images cloud Ubuntu utilisant Cloud-init pour gérer le disque de ressources et l’espace d’échange :

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consultez les ressources suivantes pour configurer le point de montage du disque de ressources et l’espace d’échange sur les images cloud Ubuntu durant l’approvisionnement :

 - [Wiki Ubuntu : Configurer les partitions d’échange](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Injection de données personnalisées dans une machine virtuelle Azure](virtual-machines-how-to-inject-custom-data.md)

 

<!---HONumber=August15_HO6-->