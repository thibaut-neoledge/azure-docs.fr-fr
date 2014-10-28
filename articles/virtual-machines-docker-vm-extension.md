<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com"></tags>

# Utilisation de l'extension Docker VM pour Linux sur Azure

[Docker][Docker] fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux][conteneurs Linux] plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Vous pouvez utiliser l'extension Docker VM sur l'[agent Linux Azure][agent Linux Azure] afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure.

Cet article suit l'[annonce du blog MS Open Tech][annonce du blog MS Open Tech] et décrit les points suivants :

-   [Conteneurs Docker et Linux][Conteneurs Docker et Linux]
-   [Utilisation de l'extension Docker VM avec Azure][Utilisation de l'extension Docker VM avec Azure]
-   [Extensions de machine virtuelle pour Linux et Windows][Extensions de machine virtuelle pour Linux et Windows]
-   [Conteneurs et ressources de gestion de conteneurs pour Azure][Conteneurs et ressources de gestion de conteneurs pour Azure]

## Conteneurs Docker et Linux

[Docker][Docker] fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux][conteneurs Linux] plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Il fournit également d'autres services qui vous permettent de créer ou d'assembler rapidement des applications, et de les distribuer entre d'autres conteneurs Docker.

Les conteneurs Docker et Linux ne sont pas des [hyperviseurs][hyperviseurs] tels que Windows Hyper-V et [KVM][KVM] sur Linux (il en existe bien d'autres exemples). Les hyperviseurs virtualisent le système d'exploitation sous-jacent afin de permettre son exécution au sein de l'hyperviseur, comme s'il s'agissait d'une application.

L'utilisation de l'hyperviseur présente des avantages non négligeables sur le plan de la sécurité, dans la mesure où la machine virtuelle « invitée » n'a aucun accès au système d'exploitation « hôte ». Elle peut simplement utiliser les ressources de l'hyperviseur. Sur le plan des inconvénients, signalons une surcharge de traitement et de stockage plus élevée, ainsi qu'un démarrage relativement plus lent pour les nouvelles machines virtuelles, dû notamment à la réplication complète des systèmes d'exploitation invités.

#### Conteneurs Docker et Linux

Docker et les autres méthodes de type *conteneur* ont permis une diminution drastique de la durée de démarrage, ainsi que de la surcharge de traitement et de stockage requise, en utilisant les fonctionnalités d'isolement du système de fichiers et des processus du kernel Linux de manière à n'exposer que les fonctionnalités de kernel à un conteneur qui autrement serait isolé. Depuis l'intérieur du conteneur, les fonctionnalités du kernel et du système de fichiers se présentent à l'application comme si elle était la seule en cours d'exécution.

Docker propose, en outre, plusieurs fonctionnalités de gestion de conteneurs qui vous permettent de charger diverses images de conteneur de la communauté, ainsi que de créer et de charger très rapidement vos propres images. Pour de plus amples informations sur Docker et son fonctionnement, consultez l'article de [présentation de Docker][présentation de Docker].

À l'instar de la plupart des technologies, Docker s'accompagne d'avantages majeurs et de quelques inconvénients. Dans la mesure où les conteneurs partagent l'accès au kernel de l'ordinateur hôte, si du code malveillant parvient à obtenir des privilèges racine, il se peut qu'il obtienne l'accès à l'ordinateur hôte, mais aussi aux autres conteneurs. Pour renforcer la sécurité de votre système de conteneur, [Docker recommande][Docker recommande] d'utiliser également une stratégie de groupe supplémentaire ou un [contrôle d'accès en fonction du rôle][contrôle d'accès en fonction du rôle], tel que [SELinux][SELinux] ou [AppArmor][AppArmor], ainsi que de réduire, autant que possible, les fonctionnalités de kernel accordées aux conteneurs. Internet regorge d'autres documents qui décrivent les méthodes de sécurité utilisant des conteneurs tels que Docker.

## Utilisation de l'extension Docker VM avec Azure

Pour utiliser l'extension Docker VM avec Azure, vous devez installer une version de l'outil [azure-cli][azure-cli] supérieure à 0.8.6 (lors de la rédaction de ce document, la version la plus récente était 0.8.7). Vous pouvez également installer cet outil sur Mac et Linux.

> [WACOM.NOTE] Vous pouvez également installer l'outil azure-cli sur Microsoft Windows. Cependant, Docker ayant été créé avec des dépendances de kernel Linux, vous devez, pour utiliser Windows en tant que client Docker, héberger une distribution Linux complète comme machine virtuelle à l'intérieur de Hyper-V ou d'un autre hyperviseur. Vous pourrez alors utiliser l'outil azure-cli et les commandes Docker de ce document et celles de Docker. Docker intègre un programme de configuration, nommé [Boot2Docker][Boot2Docker], que vous pouvez utiliser pour automatiser cette configuration.

Le processus d'utilisation de Docker sur Azure est relativement simple :

-   Installez les outils en ligne de commande azure-cli et leurs dépendances sur l'ordinateur à partir duquel vous souhaitez contrôler Azure (sur Windows, il s'agira d'une distribution Linux qui s'exécute comme une machine virtuelle).
-   Utilisez les commandes Docker de l'outil azure-cli pour créer un hôte VM Docker dans Azure.
-   Utilisez les commandes Docker locales pour gérer vos conteneurs dans votre machine virtuelle Docker sous Azure.

> [WACOM.NOTE] L'interface de ligne de commande Azure (azure-cli) est, pour l'heure, la seule méthode permettant de créer une machine virtuelle contrôlée par Docker sur Azure en vue d'héberger des conteneurs Docker. Le document d'installation générale est disponible [ici][ici]. Vous trouverez, dans les sections ci-dessous, d'autres conseils pour installer l'outil en toute simplicité sur divers systèmes d'exploitation.

### Installation de l'outil azure-cli sur Linux

Pour installer azure-cli sur Linux, le logiciel [npm (node package manager)][npm (node package manager)] est nécessaire, lequel nécessite à son tour la plateforme Node.js. Vous utiliserez donc votre gestionnaire de package favori pour installer Node.js, suivant la plateforme que vous avez choisie. Si npm est installé, tapez la ligne suivante pour obtenir le package azure-cli :

    npm install -g azure-cli

L'outil azure-cli est alors installé de manière globale. Pour confirmer l'installation, tapez `azure` à l'invite de commandes. Après quelques secondes, l'illustration ASCII azure-cli doit normalement apparaître. Elle répertorie les commandes de base mises à votre disposition. Si l'installation s'est déroulée correctement, vous pouvez taper `azure help vm` et voir que l'une des commandes répertoriées est « docker ».

> [WACOM.NOTE] Si vous utilisez une installation Ubuntu 14.04 LTS, cette image comporte une installation de nœud légèrement différente qui peut nécessiter quelques opérations supplémentaires. Vous trouverez, dans la section **Installation à l'aide d'un PPA** de [cet article][cet article], une solution qui donne d'excellents résultats. Cette section vous explique comment installer directement la version la plus récente de Node.js, ce qui semble bien fonctionner sur une distribution Ubuntu 14.04 LTS.

Comme c'est le cas avec la plupart des composants Linux, vous pouvez également cloner la source, la compiler et l'installer en local. Vous trouverez des informations à ce sujet à la page suivante : [][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a>.

### Installation de l'outil azure-cli sur Mac

Sur Mac, la méthode la plus simple pour installer l'outil azure-cli consiste également à utiliser npm avec la même commande : `npm install -g azure-cli`. Cependant, vous pouvez également utiliser le [programme d'installation pour Mac][programme d'installation pour Mac]. Comme pour Linux et Windows, vous pouvez ensuite taper `azure` à l'invite de commandes associée et confirmer l'installation correcte de l'outil azure-cli.

### Connexion de l'outil azure-cli à votre compte Azure

Avant de pouvoir utiliser azure-cli, vous devez y associer les informations d'identification de votre compte Azure sur votre plateforme. La section [Connexion à votre abonnement Azure][Connexion à votre abonnement Azure] vous explique comment télécharger et importer votre fichier **.publishsettings** ou associer votre outil en ligne de commande azure-cli à un ID d'organisation. La procédure à suivre pour les méthodes d'authentification et d'autorisation est décrite dans le document ci-dessus.

> [WACOM.NOTE] Le comportement diffère quelque peu selon que vous utilisez l'une ou l'autre méthode d'autorisation. Veuillez donc lire attentivement le document ci-dessus pour bien comprendre les différences de fonctionnalité.

### Installation de Docker et utilisation de l'extension Docker VM pour Azure

Vous disposez à présent d'un ordinateur (ou d'une machine virtuelle sur cet ordinateur) sur lequel azure-cli est installé et connecté à votre compte Azure. Suivez les [instructions d'installation de Docker][instructions d'installation de Docker] pour installer Docker sur votre ordinateur. Pour la plupart des distributions et systèmes d'exploitation, cela signifie taper la commande `apt-get install docker.io`. Vérifiez que la version de Docker est supérieure ou égale à 1.0.

Vous avez installé azure-cli sur votre ordinateur, vous l'avez connecté à votre compte Azure et vous avez installé Docker. Pour créer une machine virtuelle hôte Docker dans Azure, vous avez besoin d'une image de machine virtuelle Linux équipée de l'[agent de machine virtuelle Linux Azure][agent Linux Azure]. Pour l'heure, les seules images sur lesquelles cet agent est déjà installé sont

-   une image Ubuntu de la galerie d'images et

-   une image Linux personnalisée que vous avez créée avec l'agent de machine virtuelle Linux Azure installé et configuré ; pour plus d'informations sur la création de votre machine Linux personnalisée avec l'agent de machine virtuelle Azure, consultez la page [Agent de machine virtuelle Linux Azure][agent Linux Azure].

À l'aide de l'invite de commandes azure-cli, recherchez l'image Ubuntu la plus récente à utiliser dans la galerie des machines virtuelles en tapant

`azure vm image list | grep Ubuntu-14_04`

Tenez-vous prêt à copier le nom de l'une des images les plus récentes parmi celles qui sont répertoriées. À l'invite de commandes, tapez

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-fr-fr-30GB" <username> <password>

où :

-   *<vm-cloudservice name>* est le nom de la machine virtuelle qui deviendra l'ordinateur hôte du conteneur Docker dans Azure ;

-   *<username>* est le nom de l'utilisateur racine par défaut de la machine virtuelle ;

-   *<password>* est le mot de passe du compte *nom\_utilisateur* qui répond aux normes de complexité pour Azure.

> [WACOM.NOTE] Un mot de passe doit comporter au moins 8 caractères, dont une lettre minuscule et une lettre majuscule, un chiffre et un caractère spécial tel que [!@\#$%^&+=][!@\#$%^&+=]. Le point à la fin de la phrase précédente n'est PAS un caractère spécial.

Si la commande a été exécutée correctement, vous devriez normalement obtenir un résultat comparable à ce qui est illustré ci-dessous, suivant les arguments et options spécifiques que vous avez utilisés :

![][0]

> [WACOM.NOTE] Comme indiqué précédemment, la création d'une machine virtuelle peut prendre quelques minutes. Cependant, une fois l'approvisionnement terminé, le démon Docker démarre et vous pouvez vous connecter à l'hôte du conteneur Docker.

Pour tester la machine virtuelle Docker que vous avez créée dans Azure, tapez

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

où *<vm-name-you-used>* est le nom de la machine virtuelle utilisée dans votre appel vers `azure vm docker create`. Ici encore, en fonction des arguments et options spécifiques que vous avez indiqués, une réponse semblable à celle affichée ci-dessous doit normalement être présentée. Cela indique que votre machine virtuelle hôte Docker est opérationnelle dans Azure et qu'elle attend la saisie de commandes.

![][1]

## Remarque concernant l'authentification de machine virtuelle hôte Docker

Outre la création de la machine virtuelle Docker, la commande `azure vm docker create` crée automatiquement les certificats nécessaires pour autoriser votre ordinateur client Docker à se connecter à l'hôte de conteneur Azure à l'aide du protocole HTTPS. Les certificats sont stockés sur les ordinateurs clients et hôtes, suivant le cas. Lors des exécutions suivantes, les certificats existants sont réutilisés et partagés avec le nouvel hôte.

Par défaut, les certificats sont placés dans `~/.docker`, et Docker est configuré pour s'exécuter sur le port **4243**. Si vous souhaitez utiliser un autre port ou répertoire, vous pouvez utiliser l'une des options de ligne de commande `azure vm docker create` suivantes afin de configurer votre machine virtuelle hôte de conteneur Docker, de telle sorte qu'elle utilise un port ou des certificats différents pour la connexion des clients :

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

Le démon Docker sur l'hôte est configuré pour écouter et authentifier les connexions client sur le port spécifié à l'aide des certificats générés par la commande `azure vm docker create`. L'ordinateur client doit posséder ces certificats pour avoir accès à l'hôte Docker.

> [WACOM.NOTE] À l'inverse, un hôte en réseau qui s'exécute sans ces certificats est à la merci de toute personne capable de se connecter à l'ordinateur. Avant de modifier la configuration par défaut, vous devez bien comprendre les risques auxquels sont exposés vos ordinateurs et applications.

## Étapes suivantes

Vous êtes maintenant prêt à émettre des commandes Docker du [guide d'utilisation de Docker][guide d'utilisation de Docker] sur votre machine virtuelle hôte Docker dans Azure.

## Extensions de machine virtuelle pour Linux et Windows

L'extension de machine virtuelle Docker pour Azure est l'une des nombreuses extensions de ce type offrant un comportement spécial. D'autres sont en cours de développement. Plusieurs fonctionnalités de l'[extension Agent de machine virtuelle Linux][agent Linux Azure] vous permettent de modifier et de gérer l'image, y compris les fonctionnalités de sécurité, de kernel, de mise en réseau, etc. L'extension VMAccess pour les images Windows vous permet de réinitialiser ou de modifier les paramètres d'accès au Bureau à distance et de réinitialiser le mot de passe administrateur.

Pour obtenir une liste complète, consultez la page [Extensions de machine virtuelle Azure][Extensions de machine virtuelle Azure].

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [conteneurs Linux]: http://en.wikipedia.org/wiki/LXC
  [agent Linux Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-agent-user-guide/
  [annonce du blog MS Open Tech]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Conteneurs Docker et Linux]: #Docker-and-Linux-Containers
  [Utilisation de l'extension Docker VM avec Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Extensions de machine virtuelle pour Linux et Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Conteneurs et ressources de gestion de conteneurs pour Azure]: #Container-and-Container-Management-Resources-for-Azure
  [hyperviseurs]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [présentation de Docker]: https://www.docker.com/whatisdocker/
  [Docker recommande]: https://docs.docker.com/articles/security/
  [contrôle d'accès en fonction du rôle]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [ici]: http://azure.microsoft.com/fr-fr/documentation/articles/xplat-cli/#install
  [npm (node package manager)]: http://en.wikipedia.org/wiki/Npm_%28software%29
  [cet article]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [programme d'installation pour Mac]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [Connexion à votre abonnement Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/xplat-cli/#configure
  [instructions d'installation de Docker]: https://docs.docker.com/installation/#installation
  [!@\#$%^&+=]: mailto:!@#$%^&+=
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [1]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [guide d'utilisation de Docker]: https://docs.docker.com/userguide/
  [Extensions de machine virtuelle Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn606311.aspx
