<properties urlDisplayName="Intro to Linux" pageTitle="Pr&eacute;sentation de&nbsp;Linux dans Azure - Didacticiel&nbsp;Azure" metaKeywords="Azure Linux vm, Linux vm" description="Apprenez &agrave; utiliser des machines virtuelles&nbsp;Linux sur&nbsp;Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Pr&eacute;sentation de Linux sous Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Présentation de Linux sous Azure

Cette rubrique présente quelques aspects de l'utilisation de machines virtuelles Linux dans le cloud Azure. Le déploiement d'une machine virtuelle Linux est un processus simple si vous utilisez une image préexistante dans la galerie.

## Sommaire

-   [Authentification : noms d'utilisateur, mots de passe et clés SSH][Authentification : noms d'utilisateur, mots de passe et clés SSH]
-   [Génération et utilisation de clés SSH pour la connexion à des machines virtuelles Linux][Génération et utilisation de clés SSH pour la connexion à des machines virtuelles Linux]
-   [Obtention de privilèges de superutilisateur avec sudo][Obtention de privilèges de superutilisateur avec sudo]
-   [Configuration du pare-feu][Configuration du pare-feu]
-   [Changements de nom d'hôte][Changements de nom d'hôte]
-   [Capture d'une image de machine virtuelle][Capture d'une image de machine virtuelle]
-   [Attachement de disques][Attachement de disques]

## <span id="authentication"></span></a>Authentification : noms d'utilisateur, mots de passe et clés SSH

Lorsque vous créez une machine virtuelle Linux avec le portail de gestion Azure, il vous est demandé de fournir un nom d'utilisateur, un mot de passe et (éventuellement) une clé publique SSH. Le choix d'un nom d'utilisateur pour déployer une machine virtuelle Linux dans Azure est soumis à la contrainte suivante : les noms des comptes système (UID \<100) déjà présents dans la machine virtuelle (root, par exemple) ne sont pas autorisés.

-   Consultez la rubrique [Utilisation de SSH avec Linux sur Azure][Utilisation de SSH avec Linux sur Azure].

### <span id="keygeneration"></span></a>Génération de clés SSH

La version actuelle du portail de gestion accepte uniquement les clés publiques SSH encapsulées dans un certificat X509. Pour générer et utiliser des clés SSH avec Azure, procédez comme suit.

1.  Utilisez openssl pour générer un certificat X509 avec une paire de clés RSA 2048 bits.

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Répondez aux quelques questions posées par openssl (ou vous pouvez les laisser vides). Le contenu de ces champs n'est pas utilisé par la plateforme.

2.  Modifiez les autorisations sur la clé privée pour la sécuriser.

        chmod 600 myPrivateKey.key

3.  Convertissez `myCert.pem` en `myCert.cer` (certificat codé DER X509)

        openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Téléchargez `myCert.cer` lors de la création de la machine virtuelle Linux. Le processus d'approvisionnement installe automatiquement la clé publique de ce certificat dans le fichier `~/.ssh/authorized_keys` pour l'utilisateur spécifié dans la machine virtuelle.

5.  Connectez-vous à la machine virtuelle Linux à l'aide de ssh.

        ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    Lors de la première connexion, vous êtes invité à accepter l'empreinte digitale de la clé publique de l'hôte.

6.  Vous pouvez facultativement copier `myPrivateKey.key` dans `~/.ssh/id_rsa` pour que votre client openssh puisse sélectionner la clé automatiquement sans utiliser l'option -i.
     Vous pouvez également modifier `~/.ssh/config` pour inclure une section pour votre machine virtuelle :

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key

### Génération d'une clé à partir d'une clé existante compatible OpenSSH

L'exemple précédent décrit la création d'une clé à utiliser avec Azure. Dans certains cas, il se peut que les utilisateurs possèdent déjà une paire de clés publique et privée compatible OpenSSH et qu'ils souhaitent utiliser les mêmes clés avec Azure.

Les clés privées OpenSSH sont directement accessibles en lecture à partir de l'utilitaire `openssl`. La commande suivante prend une clé privée SSH existante (id\_rsa dans l'exemple ci-dessous) et crée la clé publique `.pem` requise par Microsoft Azure :

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Le fichier **myCert.pem** est la clé publique à utiliser pour déployer une machine virtuelle Linux sur Microsoft Azure. Lors de l'approvisionnement, le fichier `.pem` est converti en clé publique compatible `openssh` et placé dans `~/.ssh/authorized_keys`.

## <span id="superuserprivileges"></span></a>Obtention de privilèges de superutilisateur `sudo`

Le compte utilisateur qui est spécifié pendant le déploiement de l'instance de machine virtuelle dans Azure est un compte privilégié. Ce compte est configuré par l'agent Linux Azure pour pouvoir élever les privilèges au niveau root (compte superutilisateur) avec l'utilitaire `sudo`. Une fois connecté avec ce compte utilisateur, vous êtes en mesure d'exécuter les commandes en tant que root avec la syntaxe de commande.

    # sudo <COMMAND>

Vous pouvez éventuellement obtenir un interpréteur de commandes root avec **sudo -s**.

-   Consultez la rubrique [Utilisation des privilèges root sur les machines virtuelles Linux dans Azure][Utilisation des privilèges root sur les machines virtuelles Linux dans Azure]

## <span id="firewallconfiguration"></span></a>Configuration du pare-feu

Azure fournit un filtre de paquets entrants qui limite la connectivité aux ports spécifiés dans le portail de gestion. Par défaut, le seul port autorisé est SSH. Vous pouvez ouvrir l'accès à d'autres ports sur votre machine virtuelle Linux en configurant des points de terminaison dans le portail de gestion :

-   Consultez la rubrique : [Configuration des points de terminaison sur une machine virtuelle][Configuration des points de terminaison sur une machine virtuelle]

Les images Linux de la galerie Azure n'activent pas le pare-feu *iptables* par défaut. Si besoin est, le pare-feu peut être configuré pour fournir un filtrage supplémentaire.

## <span id="hostnamechanges"></span></a>Changements de nom d'hôte

Lorsque vous déployez initialement une instance d'une image Linux, vous devez fournir un nom d'hôte pour la machine virtuelle. Une fois que la machine virtuelle est en cours d'exécution, ce nom d'hôte est publié sur les serveurs DNS de la plateforme, si bien que plusieurs machines virtuelles interconnectées peuvent effectuer des recherches d'adresse IP avec des noms d'hôte.

Si vous souhaitez procéder à des changements de nom après le déploiement d'une machine virtuelle, utilisez la commande suivante :

    # sudo hostname <newname>

L'agent Linux Azure inclut une fonctionnalité permettant de détecter automatiquement ce changement de nom et de configurer correctement la machine virtuelle pour conserver ce changement et le publier sur les serveurs DNS de la plateforme.

-   [Guide d'utilisation de l'agent Linux Azure][Guide d'utilisation de l'agent Linux Azure]

### Images Ubuntu

Les images Ubuntu utilisent cloud-init, qui fournit des fonctionnalités supplémentaires d'amorçage d'une machine virtuelle.

-   Consultez l'article [Données personnalisées et Cloud-Init sur Microsoft Azure][Données personnalisées et Cloud-Init sur Microsoft Azure]

## <span id="virtualmachine"></span></a>Capture d'une image de machine virtuelle

Azure vous permet de capturer l'état d'une machine virtuelle existante dans une image qui peut ensuite servir au déploiement d'autres instances de machine virtuelle. L'agent Linux Azure peut être utilisé pour restaurer une partie de la personnalisation réalisée pendant le processus de déploiement. Pour capturer une machine virtuelle en tant qu'image, vous pouvez procéder comme suit :

1.  Exécutez **waagent -deprovision** pour annuler la personnalisation du déploiement. Ou exécutez **waagent -deprovision+user** pour éventuellement supprimer le compte utilisateur spécifié pendant le déploiement, avec toutes les données associées.

2.  Arrêtez/mettez hors tension la machine virtuelle.

3.  Cliquez sur *Capture* dans le portail de gestion ou utilisez les outils Powershell ou d'interface de ligne de commande pour capturer la machine virtuelle en tant qu'image.

-   Consultez la rubrique : [Capture d'une machine virtuelle Linux à utiliser comme modèle][Capture d'une machine virtuelle Linux à utiliser comme modèle]

## <span id="attachingdisks"></span></a>Attachement de disques

Chaque machine virtuelle est associée à un *disque de ressources* local temporaire. Étant donné que les données qui y figurent risquent de ne pas résister aux redémarrages, le disque de ressources est souvent utilisé par les applications et les processus exécutés dans la machine virtuelle pour le stockage **temporaire** des données, ainsi que pour stocker les fichiers de pagination ou d'échange du système d'exploitation.

Sous Linux, le disque de ressources est habituellement géré par l’agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu).

    >[WACOM.NOTE] Note that the resource disk is a **temporary** disk, and might be deleted and reformatted when the VM is rebooted.

Sur Linux, le disque de données peut être nommé par le noyau `/dev/sdc`, et les utilisateurs doivent partitionner, formater et monter cette ressource. Vous trouverez des instructions pas à pas dans le didacticiel suivant : [Association d'un disque de données avec une machine virtuelle][Association d'un disque de données avec une machine virtuelle].

-   Voir aussi : [Configuration d'un RAID logiciel sur Linux][Configuration d'un RAID logiciel sur Linux]

  [Authentification : noms d'utilisateur, mots de passe et clés SSH]: #authentication
  [Génération et utilisation de clés SSH pour la connexion à des machines virtuelles Linux]: #keygeneration
  [Obtention de privilèges de superutilisateur avec sudo]: #superuserprivileges
  [Configuration du pare-feu]: #firewallconfiguration
  [Changements de nom d'hôte]: #hostnamechanges
  [Capture d'une image de machine virtuelle]: #virtualmachine
  [Attachement de disques]: #attachingdisks
  [Utilisation de SSH avec Linux sur Azure]: ../linux-use-ssh-key/
  [Utilisation des privilèges root sur les machines virtuelles Linux dans Azure]: ../virtual-machines-linux-use-root-privileges/
  [Configuration des points de terminaison sur une machine virtuelle]: ../virtual-machines-set-up-endpoints/
  [Guide d'utilisation de l'agent Linux Azure]: ../virtual-machines-linux-agent-user-guide/
  [Données personnalisées et Cloud-Init sur Microsoft Azure]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Capture d'une machine virtuelle Linux à utiliser comme modèle]: ../virtual-machines-linux-capture-image/
  [Association d'un disque de données avec une machine virtuelle]: ../virtual-machines-linux-how-to-attach-disk/
  [Configuration d'un RAID logiciel sur Linux]: ../virtual-machines-linux-configure-raid/
