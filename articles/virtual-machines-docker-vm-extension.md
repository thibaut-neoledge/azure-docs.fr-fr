<properties title="Extension Docker&nbsp;VM pour&nbsp;Linux sur&nbsp;Azure" pageTitle="Extension Docker&nbsp;VM pour&nbsp;Linux sur&nbsp;Azure" description="D&eacute;crit Docker et les conteneurs, les extensions&nbsp;Azure&nbsp;Virtual&nbsp;Machines et les points vers d'autres ressources pour cr&eacute;er des conteneurs&nbsp;Docker &agrave; partir de l'interface&nbsp;xplat-cli et du portail." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />

# Extension Docker VM pour Linux sur Azure

[Docker][Docker] fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux][conteneurs Linux] plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Vous pouvez utiliser l'extension Docker VM sur l'[agent Linux Azure][agent Linux Azure] afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure.

Cette rubrique décrit :

-   [Conteneurs Docker et Linux][Conteneurs Docker et Linux]
-   [Utilisation de l'extension Docker VM avec Azure][Utilisation de l'extension Docker VM avec Azure]
-   [Extensions de machine virtuelle pour Linux et Windows][Extensions de machine virtuelle pour Linux et Windows]

Pour créer des machines virtuelles Docker, consultez les pages :

-   [Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)][Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)]
-   [Utilisation de l'extension Docker VM avec le portail Azure][Utilisation de l'extension Docker VM avec le portail Azure]

## <span id="Docker and Linux Containers"></span>Conteneurs Docker et Linux</a>

[Docker][Docker] fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux][conteneurs Linux] plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Il fournit également d'autres services qui vous permettent de créer ou d'assembler rapidement des applications, et de les distribuer entre d'autres conteneurs Docker.

Les conteneurs Docker et Linux ne sont pas des [hyperviseurs][hyperviseurs] tels que Windows Hyper-V et [KVM][KVM] sur Linux (il en existe bien d'autres exemples). Les hyperviseurs virtualisent le système d'exploitation sous-jacent afin de permettre son exécution au sein de l'hyperviseur, comme s'il s'agissait d'une application.

Docker et les autres méthodes de type *conteneur* ont permis une diminution drastique de la durée de démarrage, ainsi que de la surcharge de traitement et de stockage requise, en utilisant les fonctionnalités d'isolement du système de fichiers et des processus du kernel Linux de manière à n'exposer que les fonctionnalités de kernel à un conteneur qui autrement serait isolé.

Le tableau suivant décrit à très haut niveau le type de différences de fonctionnalités entre des hyperviseurs et des conteneurs Linux. Remarque : certaines fonctionnalités peuvent être plus ou moins souhaitables en fonction des besoins de vos applications.

| Fonctionnalité                | Hyperviseurs                                                                                 | Conteneurs                                                                                                            |
|-------------------------------|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Isolement des processus       | Plus ou moins complet                                                                        | Si la racine est obtenue, l'hôte du conteneur peut être compromis                                                     |
| Mémoire nécessaire sur disque | Système d'exploitation complet, plus des applications                                        | Conditions des applications uniquement                                                                                |
| Durée de démarrage            | Notablement plus longue : démarrage du système d'exploitation et chargement des applications | Notablement plus courte : seules les applications doivent démarrer du fait que le noyau est déjà en cours d'exécution |
| Automatisation des conteneurs | Varie considérablement en fonction du système d'exploitation et des applications             | [Galerie d'images Docker][Galerie d'images Docker] ; autres                                                                                  |

Pour une discussion sur les conteneurs et leurs avantages, consultez le [Tableau blanc Docker de haut niveau][Tableau blanc Docker de haut niveau].

Pour en savoir plus sur Docker et son fonctionnement, consultez la rubrique [Présentation de Docker][Présentation de Docker].

#### Pratiques de sécurité recommandées pour Docker et un conteneur Linux

Dans la mesure où les conteneurs partagent l'accès au kernel de l'ordinateur hôte, si du code malveillant parvient à obtenir des privilèges racine, il se peut qu'il obtienne l'accès à l'ordinateur hôte, mais aussi aux autres conteneurs. Pour renforcer la sécurité de votre système de conteneur par rapport à la configuration par défaut, [Docker recommande][Docker recommande] d'utiliser également une stratégie de groupe supplémentaire ou un [contrôle d'accès en fonction du rôle][contrôle d'accès en fonction du rôle] (ex. [SELinux][SELinux] ou [AppArmor][AppArmor]), ainsi que de réduire autant que possible les fonctionnalités du noyau accordées aux conteneurs. Internet regorge d'autres documents qui décrivent les méthodes de sécurité utilisant des conteneurs tels que Docker.

## <span id="How to use the Docker VM Extension with Azure"></span>Utilisation de l'extension Docker VM avec Azure</a>

L'extension Docker VM est un composant installé dans l'instance de machine virtuelle que vous créez qui, elle-même, installe le moteur Docker et gère les communications distantes avec la machine virtuelle. Il existe deux façons d'installer l'extension de machine virtuelle : vous pouvez créer votre machine virtuelle en utilisant le portail de gestion, ou vous pouvez créer l'image en utilisant l'interface xplat-cli (de ligne de commande interplateforme Azure).

Vous pouvez utiliser le portail pour ajouter l'extension Docker VM à n'importe quelle machine virtuelle compatible Linux (actuellement, la seule image qui prend en charge cette fonctionnalité est l'image Ubuntu 14.04 LTS postérieure au mois de juillet). Néanmoins, en utilisant la ligne de commande de l'interface xplat-cli, vous pouvez installer l'extension Docker VM, et créer et télécharger vers le serveur vos certificats de communication Docker en même temps que vous créez l'instance de machine virtuelle.

Pour créer des machines virtuelles Docker, consultez les pages :

-   [Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)][Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)]
-   [Utilisation de l'extension Docker VM avec le portail Azure][Utilisation de l'extension Docker VM avec le portail Azure]

## <span id="Virtual Machine Extensions for Linux and Windows"></span>Extensions de machine virtuelle pour Linux et Windows</a>

L'extension de machine virtuelle Docker pour Azure est l'une des nombreuses extensions de ce type offrant un comportement spécial. D'autres sont en cours de développement. Plusieurs fonctionnalités de l'[extension Agent de machine virtuelle Linux][agent Linux Azure] vous permettent de modifier et de gérer l'image, y compris les fonctionnalités de sécurité, de kernel, de mise en réseau, etc. L'extension VMAccess pour les images Windows vous permet de réinitialiser ou de modifier les paramètres d'accès au Bureau à distance et de réinitialiser le mot de passe administrateur.

Pour obtenir une liste complète, consultez la page [Extensions de machine virtuelle Azure][Extensions de machine virtuelle Azure].

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [conteneurs Linux]: http://en.wikipedia.org/wiki/LXC
  [agent Linux Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-agent-user-guide/
  [Conteneurs Docker et Linux]: #Docker-and-Linux-Containers
  [Utilisation de l'extension Docker VM avec Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Extensions de machine virtuelle pour Linux et Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Utilisation de l'extension Docker VM avec le portail Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-docker-with-portal/
  [hyperviseurs]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Galerie d'images Docker]: https://registry.hub.docker.com/
  [Tableau blanc Docker de haut niveau]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Présentation de Docker]: https://www.docker.com/whatisdocker/
  [Docker recommande]: https://docs.docker.com/articles/security/
  [contrôle d'accès en fonction du rôle]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [Extensions de machine virtuelle Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn606311.aspx
