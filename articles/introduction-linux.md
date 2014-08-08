<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Intro to Linux" pageTitle="Introduction to Linux in Azure - Azure Tutorial" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="" solutions="" manager="" editor="" />

Présentation de Linux sous Azure
================================

Cette rubrique présente quelques aspects de l'utilisation de machines virtuelles Linux dans le cloud Azure. Le déploiement d'une machine virtuelle Linux est un processus simple si vous utilisez une image préexistante dans la galerie.

Sommaire
--------

-   [Authentification : noms d'utilisateur, mots de passe et clés SSH](#authentication)
-   [Génération et utilisation de clés SSH pour la connexion à des machines virtuelles Linux](#keygeneration)
-   [Obtention de privilèges de superutilisateur avec sudo](#superuserprivileges)
-   [Configuration du pare-feu](#firewallconfiguration)
-   [Changements de nom d'hôte](#hostnamechanges)
-   [Capture d'une image de machine virtuelle](#virtualmachine)
-   [Attachement de disques](#attachingdisks)

Authentification : noms d'utilisateur, mots de passe et clés SSH
----------------------------------------------------------------

Lorsque vous créez une machine virtuelle Linux avec le portail de gestion Azure, il vous est demandé de fournir un nom d'utilisateur, un mot de passe et (éventuellement) une clé publique SSH. Le choix d'un nom d'utilisateur pour déployer une machine virtuelle Linux dans Azure est soumis à la contrainte suivante : les noms des comptes système déjà présents dans la machine virtuelle (root, par exemple) ne sont pas autorisés. Si vous ne fournissez pas la clé publique SSH, vous pourrez vous connecter à la machine virtuelle en utilisant le nom d'utilisateur et le mot de passe spécifiés. Si vous choisissez de télécharger une clé publique SSH dans le portail de gestion, l'authentification basée sur le mot de passe sera désactivée et vous devrez utiliser la clé privée SSH correspondante pour vous authentifier sur la machine virtuelle et vous connecter.

Génération de clés SSH
----------------------

La version actuelle du portail de gestion accepte uniquement les clés publiques SSH encapsulées dans un certificat X509. Pour générer et utiliser des clés SSH avec Azure, procédez comme suit.

1.  Utilisez openssl pour générer un certificat X509 avec une paire de clés RSA 2048 bits.

         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Répondez aux quelques questions posées par openssl (ou vous pouvez les laisser vides). Le contenu de ces champs n'est pas utilisé par la plateforme.

2.  Modifiez les autorisations sur la clé privée pour la sécuriser.

         chmod 600 myPrivateKey.key

3.  Convertissez myCert.pem en myCert.cer (certificat codé DER X509)

         openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Téléchargez myCert.cer lors de la création de la machine virtuelle Linux. Le processus de déploiement installe automatiquement la clé publique de ce certificat dans le fichier authorized\_keys pour l'utilisateur spécifié dans la machine virtuelle.

5.  Connectez-vous à la machine virtuelle Linux à l'aide de ssh.

         ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    Lors de la première connexion, vous êtes invité à accepter l'empreinte digitale de la clé publique de l'hôte.

6.  Vous pouvez éventuellement copier myPrivateKey.key vers \~/.ssh/id\_rsa pour que votre client openssh puisse sélectionner la clé automatiquement sans utiliser l'option -i.

Obtention de privilèges de superutilisateur avec sudo
-----------------------------------------------------

Le compte utilisateur qui est spécifié pendant le déploiement de l'instance de machine virtuelle dans Azure est un compte privilégié. Ce compte est configuré par l'agent Linux Azure pour pouvoir élever les privilèges au niveau root (compte superutilisateur) avec l'outil sudo. Une fois connecté avec ce compte utilisateur, vous êtes en mesure d'exécuter les commandes en tant que root avec « sudo command ». Vous pouvez éventuellement obtenir un interpréteur de commandes root avec **sudo -s**.

Configuration du pare-feu
-------------------------

Azure fournit un filtre de paquets entrants qui limite la connectivité aux ports spécifiés dans le portail de gestion. Par défaut, le seul port autorisé est SSH. Vous pouvez ouvrir l'accès à d'autres ports sur votre machine virtuelle Linux en ajoutant des règles dans le portail de gestion.

Les images Linux dans la galerie n'activent pas le pare-feu iptables dans les machines virtuelles Linux. Si besoin est, le pare-feu IPtables peut être configuré pour fournir d'autres fonctionnalités.

Changements de nom d'hôte
-------------------------

Lorsque vous déployez initialement une instance d'une image Linux, vous devez fournir un nom d'hôte pour la machine virtuelle. Une fois que la machine virtuelle est en cours d'exécution, ce nom d'hôte est publié sur les serveurs DNS de la plateforme, si bien que plusieurs machines virtuelles interconnectées peuvent effectuer des recherches d'adresse IP avec des noms d'hôte. Si vous souhaitez procéder à des changements de nom après le déploiement d'une machine virtuelle, utilisez la commande **hostname newname**. L'agent Linux Azure inclut une fonctionnalité permettant de détecter automatiquement ce changement de nom et de configurer correctement la machine virtuelle pour rendre ce changement persistant et le publier sur les serveurs DNS de la plateforme. Pour obtenir plus de détails et découvrir les options de configuration relatives à cette fonctionnalité, consultez le fichier LISEZMOI de l'agent Linux Azure.

Capture d'une image de machine virtuelle
----------------------------------------

Azure vous permet de capturer l'état d'une machine virtuelle existante dans une image qui peut ensuite servir au déploiement d'autres instances de machine virtuelle. L'agent Linux Azure peut être utilisé pour restaurer une partie de la personnalisation réalisée pendant le processus de déploiement. Pour capturer une machine virtuelle en tant qu'image, vous pouvez procéder comme suit :

1.  Exécutez **waagent -deprovision** pour annuler la personnalisation du déploiement. Ou exécutez **waagent -deprovision+user** pour éventuellement supprimer le compte utilisateur spécifié pendant le déploiement, avec toutes les données associées.

2.  Arrêtez/mettez hors tension la machine virtuelle avec le portail de gestion/les outils.

3.  Cliquez sur Capture dans le portail de gestion ou utilisez les outils pour capturer la machine virtuelle en tant qu'image.

Attachement de disques
----------------------

L'opération est couverte étape par étape dans le didacticiel consacré à la [création d'une machine virtuelle Linux](../virtual-machine-from-gallery/).

