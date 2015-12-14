<properties
	pageTitle="Extension Docker VM pour Linux sur Azure"
	description="Décrit Docker et les conteneurs, les extensions Azure Virtual Machines et les points vers d’autres ressources pour créer des conteneurs Docker à partir de l’interface de ligne de commande Azure et du portail."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Extension Docker VM pour Linux sur Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[Docker](https://www.docker.com/) fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des machines virtuelles pour isoler les données d’application et le traitement sur des ressources partagées. Vous pouvez utiliser l'[extension Docker VM dans Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) sur l'[agent Linux Azure](virtual-machines-linux-agent-user-guide.md) afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure.

Cette rubrique décrit :

+ [Conteneurs Docker et Linux]
+ [Utilisation de l’extension Docker VM avec Azure]
+ [Extensions de machine virtuelle pour Linux et Windows]

Pour créer des machines virtuelles Docker, consultez les pages :

+ [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Azure]
+ [Utilisation de l’extension Docker VM avec le portail Azure Classic]
+ [Prise en main rapide de Docker dans Azure Marketplace]

Pour plus d'informations sur l'extension et son fonctionnement, consultez la rubrique [Guide d'utilisation de l’extension Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md).

## Conteneurs Docker et Linux
[Docker](https://www.docker.com/) fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Il fournit également d’autres services qui vous permettent de créer ou d’assembler rapidement des applications, et de les distribuer entre d’autres conteneurs Docker.

Les conteneurs Docker et Linux ne sont pas des [hyperviseurs](http://en.wikipedia.org/wiki/Hypervisor) tels que Windows Hyper-V et [KVM](http://www.linux-kvm.org/page/Main_Page) sur Linux (il en existe bien d'autres exemples). Les hyperviseurs virtualisent le système d’exploitation sous-jacent afin de permettre l’exécution de systèmes d’exploitation (appelés *machines virtuelles*) au sein de l’hyperviseur, comme s’il s’agissait d’une application.

Docker et les autres méthodes de type *conteneur* ont permis une diminution drastique de la durée de démarrage, ainsi que de la surcharge de traitement et de stockage requise, en utilisant les fonctionnalités d'isolement du système de fichiers et des processus du kernel Linux de manière à n'exposer que les fonctionnalités de kernel à un conteneur qui autrement serait isolé.

Le tableau suivant décrit à très haut niveau le type de différences de fonctionnalités entre des hyperviseurs et des conteneurs Linux. Remarque : certaines fonctionnalités peuvent être plus ou moins souhaitables en fonction des besoins de vos applications.

| Fonctionnalité | Hyperviseurs | Conteneurs |
| :------------- |-------------| ----------- |
| Isolement des processus | Plus ou moins complet | Si la racine est obtenue, l'hôte du conteneur peut être compromis |
| Mémoire nécessaire sur disque | Système d'exploitation complet, plus des applications | Conditions des applications uniquement |
| Durée de démarrage | Nettement plus long : démarrage du système d’exploitation et chargement des applications | Nettement plus court : seules les applications doivent démarrer du fait que le noyau est déjà en cours d’exécution |
| Automatisation des conteneurs | Varie considérablement en fonction du système d'exploitation et des applications | [Galerie d'images Docker ](https://registry.hub.docker.com/) ; autres

Pour une discussion sur les conteneurs et leurs avantages, consultez le [Tableau blanc Docker de haut niveau](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Pour en savoir plus sur Docker et son fonctionnement, consultez la rubrique [Présentation de Docker](https://www.docker.com/whatisdocker/).

#### Pratiques de sécurité recommandées pour Docker et un conteneur Linux

Dans la mesure où les conteneurs partagent l'accès au kernel de l'ordinateur hôte, si du code malveillant parvient à obtenir des privilèges racine, il se peut qu'il obtienne l'accès à l'ordinateur hôte, mais aussi aux autres conteneurs. Pour renforcer la sécurité de votre système de conteneur par rapport à la configuration par défaut, [Docker recommande](https://docs.docker.com/articles/security/) d'utiliser également une stratégie de groupe supplémentaire ou un [contrôle d'accès en fonction du rôle](http://en.wikipedia.org/wiki/Role-based_access_control) (ex. [SELinux](http://selinuxproject.org/page/Main_Page) ou [AppArmor](http://wiki.apparmor.net/index.php/Main_Page)), ainsi que de réduire autant que possible les fonctionnalités du noyau accordées aux conteneurs. Internet regorge d'autres documents qui décrivent les méthodes de sécurité utilisant des conteneurs tels que Docker.

## Utilisation de l'extension Docker VM avec Azure

L’extension Docker VM est un composant installé dans l’instance de machine virtuelle que vous créez qui, elle-même, installe le moteur Docker et gère les communications distantes avec la machine virtuelle. Il existe deux façons d'installer l'extension de machine virtuelle : vous pouvez créer votre machine virtuelle en utilisant le portail de gestion, ou vous pouvez créer l’image en utilisant l’interface de ligne de commande Azure.

Vous pouvez utiliser le portail pour ajouter l’extension Docker VM à n’importe quelle machine virtuelle compatible Linux (actuellement, la seule image qui prend en charge cette fonctionnalité est l’image Ubuntu 14.04 LTS postérieure au mois de juillet). Néanmoins, en utilisant la ligne de commande de l’interface de ligne de commande Azure, vous pouvez installer l’extension Docker VM, et créer et télécharger vers le serveur vos certificats de communication Docker en même temps que vous créez l’instance de machine virtuelle.

Pour créer des machines virtuelles Docker, consultez les pages :

+ [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Azure]
+ [Utilisation de l’extension Docker VM avec le portail Azure Classic]

## Extensions de machine virtuelle pour Linux et Windows
L’[extension de machine virtuelle Docker pour Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) est l’une des nombreuses extensions de ce type offrant un comportement spécial. D’autres sont en cours de développement. Par exemple, plusieurs fonctionnalités de l’[extension Agent de machine virtuelle Linux](virtual-machines-linux-agent-user-guide.md) vous permettent de modifier et de gérer l’image, y compris les fonctionnalités de sécurité, de noyau, de mise en réseau, etc. L'extension VMAccess, entre autres, vous permet de réinitialiser le mot de passe administrateur ou une clé SSH.

Pour obtenir une liste complète, consultez la page [Extensions de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx).

<!--Anchors-->
[Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Utilisation de l’extension Docker VM avec le portail Azure Classic]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Prise en main rapide de Docker dans Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Conteneurs Docker et Linux]: #Docker-and-Linux-Containers
[Utilisation de l’extension Docker VM avec Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensions de machine virtuelle pour Linux et Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows

<!---HONumber=AcomDC_1203_2015-->