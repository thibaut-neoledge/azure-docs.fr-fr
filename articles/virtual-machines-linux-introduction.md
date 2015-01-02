<properties urlDisplayName="Intro to Linux" pageTitle="Présentation de Linux dans Azure - Didacticiel Azure" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="09/13/2014" ms.author="szark" />





#Présentation de Linux sous Azure

Cette rubrique présente quelques aspects de l'utilisation de machines virtuelles Linux dans le cloud Azure. Le déploiement d'une machine virtuelle Linux est un processus simple qui fait appel à une image de la galerie. 

## Sommaire ##

* [Authentication : noms d'utilisateurs, mots de passe et clés SSH.](#authentication)
* [Génération et utilisation de clés SSH pour la connexion à des machines virtuelles Linux.](#keygeneration)
* [Obtention de privilèges de superutilisateur avec sudo](#superuserprivileges)
* [Configuration du pare-feu](#firewallconfiguration)
* [Changements de nom d'hôte](#hostnamechanges)
* [Capture d'une image de machine virtuelle](#virtualmachine)
* [Attachement de disques](#attachingdisks)

## <a id="authentication"></a>Authentification : noms d'utilisateurs, mots de passe et clés SSH

Lorsque vous créez une machine virtuelle Linux avec le portail de gestion Azure, il vous est demandé de fournir un nom d'utilisateur, un mot de passe et (éventuellement) une clé publique SSH. Le choix de nom d'utilisateur pour le déploiement d'une machine virtuelle Linux sur Azure est soumis à la contrainte suivante : les noms des comptes système (UID <100) déjà présents sur la machine virtuelle ne sont pas autorisés ('root', par exemple).

 - Consultez la rubrique [Utilisation de SSH avec Linux sur Azur](../linux-use-ssh-key/)


### <a id="keygeneration"></a>Génération de clés SSH

La version actuelle du portail de gestion accepte uniquement les clés publiques SSH encapsulées dans un certificat X509. Pour générer et utiliser des clés SSH avec Azure, procédez comme suit.

1. Utilisez openssl pour générer un certificat X509 avec une paire de clés RSA 2048 bits.
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	Répondez aux quelques questions posées par openssl (ou vous pouvez les laisser vides). Le contenu de ces champs n'est pas utilisé par la plateforme.

2. Modifiez les autorisations sur la clé privée pour la sécuriser.

		chmod 600 myPrivateKey.key

3. Convertissez `myCert.pem` en `myCert.cer` (certificat codé DER X509)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Téléchargez `myCert.cer` lors de la création de la machine virtuelle Linux. Le processus de déploiement installe automatiquement la clé publique de ce certificat dans le fichier `~/.ssh/authorized_keys` pour l'utilisateur spécifié dans la machine virtuelle.

5. Connectez-vous à la machine virtuelle Linux à l'aide de ssh.

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	Lors de la première connexion, vous êtes invité à accepter l'empreinte digitale de la clé publique de l'hôte.

6. Vous pouvez éventuellement copier  `myPrivateKey.key` vers `~/.ssh/id_rsa` pour que votre client openssh puisse sélectionner la clé automatiquement sans utiliser l'option -i.
   Vous pouvez également modifier `~/.ssh/config` pour inclure une section pour votre machine virtuelle :

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key


### Génération d'une clé à partir d'une clé existante compatible OpenSSH
L'exemple précédent décrit la création d'une clé à utiliser avec Azure. Dans certains cas, il se peut que les utilisateurs possèdent déjà une paire de clés publique et privée compatible OpenSSH et qu'ils souhaitent utiliser les mêmes clés avec Azure.

Les clés privées OpenSSH sont directement accessibles en lecture à partir de l'utilitaire `openssl`. La commande suivante prend une clé privée SSH existante (id_rsa dans l'exemple ci-dessous) et crée la clé publique `.pem` requise par Microsoft Azure :

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Le fichier  **myCert.pem** est la clé publique à utiliser pour déployer une machine virtuelle Linux sur Microsoft Azure. Lors du déploiement, le fichier `.pem` est converti en clé publique compatible `openssh` et placé dans `~/.ssh/authorized_keys`.


## <a id="superuserprivileges"></a>Obtention de privilèges de superutilisateur avec `sudo`

Le compte utilisateur qui est spécifié pendant le déploiement de l'instance de machine virtuelle dans Azure est un compte privilégié. Ce compte est configuré par l'agent Linux Azure pour pouvoir élever les privilèges au niveau root (compte superutilisateur) avec l'utilitaire `sudo`. Une fois connecté avec ce compte utilisateur, vous êtes en mesure d'exécuter les commandes en tant que root avec la syntaxe de commande.

	# sudo <COMMAND>

Vous pouvez éventuellement obtenir un interpréteur de commandes root avec **sudo -s**.

- Consultez la rubrique [Utilisation des privilèges root sur les machines virtuelles Linux dans Azure](../virtual-machines-linux-use-root-privileges/)


## <a id="firewallconfiguration"></a>Configuration du pare-feu

Azure fournit un filtre de paquets entrants qui limite la connectivité aux ports spécifiés dans le portail de gestion. Par défaut, le seul port autorisé est SSH. Vous pouvez ouvrir l'accès à d'autres ports sur votre machine virtuelle Linux en configurant des points de terminaison dans le portail de gestion :

 - Consultez la rubrique : [Configuration des points de terminaison sur une machine virtuelle](../virtual-machines-set-up-endpoints/)

Les images Linux de la galerie Azure n'activent pas le pare-feu *iptables* par défaut. Si besoin est, le pare-feu peut être configuré pour fournir un filtrage supplémentaire.


## <a id="hostnamechanges"></a>Changements de nom d'hôte

Lorsque vous déployez initialement une instance d'une image Linux, vous devez fournir un nom d'hôte pour la machine virtuelle. Une fois que la machine virtuelle est en cours d'exécution, ce nom d'hôte est publié sur les serveurs DNS de la plateforme, si bien que plusieurs machines virtuelles interconnectées peuvent effectuer des recherches d'adresse IP avec des noms d'hôte.

Si vous souhaitez procéder à des changements de nom après le déploiement d'une machine virtuelle, utilisez la commande suivante :

	# sudo hostname <newname>

L'agent Linux Azure inclut une fonctionnalité permettant de détecter automatiquement ce changement de nom et de configurer correctement la machine virtuelle pour conserver ce changement et le publier sur les serveurs DNS de la plateforme.

 - [Guide d'utilisation de l'agent Linux Azure](../virtual-machines-linux-agent-user-guide/)

### Images Ubuntu
Les images Ubuntu utilisent cloud-init, qui fournit des fonctionnalités supplémentaires d'amorçage d'une machine virtuelle.

 - Consultez l'article [Données personnalisées et Cloud-Init sur Microsoft Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)


## <a id="virtualmachine"></a>Capture d'une image de machine virtuelle

Azure vous permet de capturer l'état d'une machine virtuelle existante dans une image qui peut ensuite servir au déploiement d'autres instances de machine virtuelle. L'agent Linux Azure peut être utilisé pour restaurer une partie de la personnalisation réalisée pendant le processus de déploiement. Pour capturer une machine virtuelle en tant qu'image, vous pouvez procéder comme suit :

1. Exécutez **waagent -deprovision** pour annuler la personnalisation du déploiement. Ou exécutez **waagent -deprovision+user** pour éventuellement supprimer le compte utilisateur spécifié pendant le déploiement, avec toutes les données associées.

2. Arrêtez/mettez hors tension la machine virtuelle.

3. Cliquez sur *Capturer* dans le portail de gestion ou utilisez les outils Powershell ou d'interface de ligne de commande pour capturer la machine virtuelle en tant qu'image.

 - Consultez la rubrique : [Capture d'une machine virtuelle Linux à utiliser comme modèle](../virtual-machines-linux-capture-image/)


## <a id="attachingdisks"></a>Attachement de disques

Chaque machine virtuelle est associée à un *disque de ressources* local temporaire. Étant donné que les données qui y figurent risquent de ne pas résister aux redémarrages, le disque de ressources est souvent utilisé par les applications et les processus exécutés dans la machine virtuelle pour le stockage **temporaire** des données, ainsi que pour stocker les fichiers de pagination ou d'échange du système d'exploitation.

Sous Linux, le disque de ressources est habituellement géré par l'agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu).

	>[WACOM.NOTE] Notez que le disque de ressources est un disque **temporaire** et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle.

Sous Linux, le disque de données peut être nommé par le noyau " /dev/sdc ", et les utilisateurs doivent partitionner, formater et monter cette ressource. Vous trouverez des instructions pas à pas dans le didacticiel suivant : [Association d'un disque de données avec une machine virtuelle](../virtual-machines-linux-how-to-attach-disk/).

 - Voir aussi : [Configuration d'un RAID logiciel sur Linux](../virtual-machines-linux-configure-raid/)


<!--HONumber=35_1-->
