<properties
	pageTitle="Présentation de Linux dans Azure | Microsoft Azure"
	description="Apprenez à utiliser des machines virtuelles Linux sur Azure."
	services="virtual-machines"
	documentationCenter="python"
	authors="szarkos"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="szark"/>

#Présentation de Linux sous Azure

Cette rubrique présente quelques aspects de l'utilisation de machines virtuelles Linux dans le cloud Azure. Le déploiement d'une machine virtuelle Linux est un processus simple qui fait appel à une image de la galerie.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Authentification : noms d’utilisateurs, mots de passe et clés SSH

Lorsque vous créez une machine virtuelle Linux avec le portail Azure Classic, il vous est demandé de fournir un nom d'utilisateur, un mot de passe et (éventuellement) une clé publique SSH. Le choix de nom d'utilisateur pour le déploiement d'une machine virtuelle Linux sur Azure est soumis à la contrainte suivante : les noms des comptes système (UID <100) déjà présents sur la machine virtuelle ne sont pas autorisés ('root', par exemple).


 - Consultez la page [Création d’une machine virtuelle exécutant Linux](virtual-machines-linux-tutorial.md)
 - Consultez la rubrique [Utilisation de SSH avec Linux sur Azure](virtual-machines-linux-use-ssh-key.md).


## Obtention de privilèges de superutilisateur avec `sudo`

Le compte utilisateur qui est spécifié pendant le déploiement de l'instance de machine virtuelle dans Azure est un compte privilégié. Ce compte est configuré par l’agent Linux Azure pour pouvoir élever les privilèges au niveau root (compte superutilisateur) avec l’utilitaire `sudo`. Une fois connecté avec ce compte utilisateur, vous êtes en mesure d'exécuter les commandes en tant que root avec la syntaxe de commande.

	# sudo <COMMAND>

Vous pouvez éventuellement obtenir un interpréteur de commandes root avec **sudo -s**.

- Consultez la rubrique [Utilisation des privilèges root sur les machines virtuelles Linux dans Azure](virtual-machines-linux-use-root-privileges.md)


## Configuration du pare-feu

Azure fournit un filtre de paquets entrants qui limite la connectivité aux ports spécifiés dans le portail Azure Classic. Par défaut, le seul port autorisé est SSH. Vous pouvez ouvrir l'accès à d'autres ports sur votre machine virtuelle Linux en configurant des points de terminaison dans le portail Azure Classic :

 - Consultez la page [Configuration des points de terminaison sur une machine virtuelle](virtual-machines-set-up-endpoints.md)

Les images Linux de la galerie Azure n'activent pas le pare-feu *iptables* par défaut. Si besoin est, le pare-feu peut être configuré pour fournir un filtrage supplémentaire.


## Changements de nom d’hôte

Lorsque vous déployez initialement une instance d'une image Linux, vous devez fournir un nom d'hôte pour la machine virtuelle. Une fois que la machine virtuelle est en cours d'exécution, ce nom d'hôte est publié sur les serveurs DNS de la plateforme, si bien que plusieurs machines virtuelles interconnectées peuvent effectuer des recherches d'adresse IP avec des noms d'hôte.

Si vous souhaitez procéder à des changements de nom après le déploiement d'une machine virtuelle, utilisez la commande suivante :

	# sudo hostname <newname>

L'agent Linux Azure inclut une fonctionnalité permettant de détecter automatiquement ce changement de nom et de configurer correctement la machine virtuelle pour conserver ce changement et le publier sur les serveurs DNS de la plateforme.

 - [Guide d’utilisateur de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md)

### Cloud-Init
Les images **Ubuntu** et **CoreOS** utilisent cloud-init pn Azure, qui fournit des fonctionnalités supplémentaires d’amorçage d’une machine virtuelle.

 - [Comment injecter des données personnalisées](virtual-machines-how-to-inject-custom-data.md)
 - [Données personnalisées et Cloud-Init sur Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Création de partitions d’échange Azure à l’aide de Cloud-Init](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Utilisation de CoreOS dans Azure](virtual-machines-linux-coreos-how-to.md)


## Capture d’une image de machine virtuelle

Azure vous permet de capturer l'état d'une machine virtuelle existante dans une image qui peut ensuite servir au déploiement d'autres instances de machine virtuelle. L'agent Linux Azure peut être utilisé pour restaurer une partie de la personnalisation réalisée pendant le processus de déploiement. Pour capturer une machine virtuelle en tant qu'image, vous pouvez procéder comme suit :

1. Exécutez **waagent -deprovision** pour annuler la personnalisation du déploiement. Ou exécutez **waagent -deprovision+user** pour éventuellement supprimer le compte utilisateur spécifié pendant le déploiement, avec toutes les données associées.

2. Arrêtez/mettez hors tension la machine virtuelle.

3. Cliquez sur *Capture* dans le portail Azure Classic ou utilisez les outils Powershell ou d'interface de ligne de commande pour capturer la machine virtuelle en tant qu'image.

 - Consultez la page [Capture d’une machine virtuelle Linux à utiliser comme modèle](virtual-machines-linux-capture-image.md).


## Attachement de disques

Chaque machine virtuelle est associée à un *disque de ressources* local temporaire. Étant donné que les données qui y figurent risquent de ne pas résister aux redémarrages, le disque de ressources est souvent utilisé par les applications et les processus exécutés dans la machine virtuelle pour le stockage **temporaire** des données, ainsi que pour stocker les fichiers de pagination ou d'échange du système d'exploitation.

Sous Linux, le disque de ressources est habituellement géré par l’agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu).


>[AZURE.NOTE] Notez que le disque de ressources est un disque **temporaire** et qu’il peut être effacé et reformaté lors du redémarrage de la machine virtuelle.

Sous Linux, le disque de données peut être nommé `/dev/sdc` par le noyau et les utilisateurs doivent partitionner, formater et monter cette ressource. L’opération est couverte étape par étape dans le didacticiel [Association d’un disque de données avec une machine virtuelle](virtual-machines-linux-how-to-attach-disk.md).

 - **Voir aussi :** [Configuration d’un RAID logiciel sur Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0204_2016-->