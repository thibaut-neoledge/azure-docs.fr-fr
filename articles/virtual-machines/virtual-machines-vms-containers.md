<properties 
	pageTitle="Machines virtuelles et conteneurs | Microsoft Azure" 
	description="Décrit Virtual Machines, Docker et les conteneurs Linux, ainsi que leur utilisation dans des groupes dans Azure, y compris les avantages de chacun et les scénarios dans lesquels chaque approche fonctionne bien." 
	services="virtual-machines" 
	documentationCenter="virtual-machines" 
	authors="squillace" 
	manager="timlt"
	tags="azure-resource-manager,azure-service-management" 
/>
	

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="infrastructure" 
	ms.workload="infrastructure" 
	ms.date="07/02/2015" 
	ms.author="rasquill" 
/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Machines virtuelles et conteneurs dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Azure vous offre des solutions cloud exceptionnelles reposant sur les machines virtuelles – basées sur l’émulation de matériel informatique physique – pour permettre des déploiements logiciels flexibles et améliorer considérablement la consolidation des ressources par rapport au matériel physique. Au cours des dernières années, en grande partie grâce à l’approche [Docker](https://www.docker.com) des conteneurs et à l’écosystème Docker, la technologie de conteneurs Linux a immensément développé les moyens dont vous disposez pour développer et gérer les logiciels distribués. Le code d’application dans un conteneur est isolé de la machine virtuelle Azure hôte ainsi que d’autres conteneurs sur la même machine virtuelle, ce qui vous donne plus de souplesse de développement et de déploiement au niveau de l’application, outre la flexibilité que les machines virtuelles Azure vous offrent déjà.

**Mais c’est de l’histoire ancienne.** La grande *nouveauté* est qu’Azure vous offre encore plus d’avantages Docker :

- [De nombreux](virtual-machines-docker-with-xplat-cli-install.md) [moyens](virtual-machines-docker-ubuntu-quickstart.md) [différents](virtual-machines-docker-with-portal.md) de [créer des hôtes Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) pour des conteneurs adaptés à vos besoins
- [Azure Resource Manager](resource-group-overview.md) et [modèles de groupes de ressources](resource-group-authoring-templates.md) pour simplifier le déploiement et la mise à jour des applications distribuées complexes
- Intégration avec un large éventail d’outils de gestion de configuration propriétaires et open source

Et, étant donné que vous pouvez créer des machines virtuelles et conteneurs Linux sur Azure par programmation, vous pouvez également utiliser les outils d’*orchestration* de machines virtuelles et de conteneurs pour créer des groupes de machines virtuelles et pour déployer des applications dans les conteneurs Linux et bientôt dans les [conteneurs Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

Cet article traite de ces concepts à un niveau supérieur et propose également de très nombreux liens vers plus d’informations, des didacticiels et des produits liés à l’utilisation de conteneurs et de clusters sur Azure. Si vous savez déjà tout cela et que seuls les liens vous intéressent, vous les trouverez [ici](#tools-for-working-with-containers).

## Différence entre les machines virtuelles et les conteneurs

Les machines virtuelles s’exécutent dans un environnement isolé de virtualisation matérielle, fourni par un [hyperviseur](http://en.wikipedia.org/wiki/Hypervisor). Dans Azure, le service [Virtual Machines](http://azure.microsoft.com/services/virtual-machines/) gère toutes les opérations pour vous. Il vous suffit de créer Virtual Machines en choisissant le système d’exploitation et en le configurant pour qu’il s’exécute comme vous le souhaitez ou en téléchargeant votre propre image de machine virtuelle personnalisée. Les machines virtuelles sont une technologie éprouvée, et aguerrie ; de nombreux outils sont disponibles pour gérer les systèmes d’exploitation et configurer les applications que vous installez et exécutez. Tous les éléments exécutés dans une machine virtuelle sont masqués du système d’exploitation hôte et, du point de vue d’une application ou d’un utilisateur exécutés dans une machine virtuelle, la machine virtuelle apparaît comme un ordinateur physique autonome.

Les [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) (qui incluent ceux créés et hébergés à l’aide d’outils Docker, d’autres approches existent) ne nécessitent pas d’hyperviseur pour assurer leur isolation et n’y ont pas recours. À la place, l’hôte conteneur utilise les fonctionnalités d’isolation des processus et systèmes de fichiers du noyau Linux pour exposer sur le conteneur (et son application) certaines fonctionnalités du noyau uniquement et son propre système de fichiers isolés (au minimum). Du point de vue d’une application exécutée dans un conteneur, le conteneur apparaît comme une instance unique du système d’exploitation. Une application à relation contenant-contenu ne peut pas voir les processus ou autres ressources en dehors de son conteneur.

Le noyau de l’ordinateur hôte Docker étant partagé dans ce modèle d’isolation et d’exécution, et l’espace disque nécessaire du conteneur n’incluant pas un système d’exploitation dans son intégralité, le temps de démarrage du conteneur ainsi que la surcharge de stockage disque requis sont grandement réduits.

C’est vraiment pratique.

Les [conteneurs Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) offrent les mêmes avantages que les conteneurs Linux pour les applications qui s’exécutent sur Windows. Les conteneurs Windows Server prennent en charge le format d’image et l’API Docker. Par conséquent, une application utilisant les conteneurs Windows Server peut être développée, publiée, récupérée et déployée à l’aide de commandes similaires à celles utilisées sur Mac et Linux. Cela vient compléter la [nouvelle prise en charge Docker dans Microsoft Visual Studio](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0). Ce nouvel [écosystème de conteneurs](https://msdn.microsoft.com/virtualization/windowscontainers/about/container_ecosystem) étendu offrira à chacun les outils nécessaires pour travailler avec les conteneurs.

C’est très pratique aussi.

### Est-ce trop beau pour être vrai ?

Eh bien, oui et non. À l’instar de toute autre technologie, les conteneurs n’éliminent pas comme par magie tous les efforts exigés par les applications distribuées. Toutefois, les conteneurs modifient en profondeur les éléments suivants :

- la rapidité selon laquelle le code d’application peut être développé et partagé à grande échelle ;
- la rapidité et la fiabilité selon lesquelles il peut être testé ;
- la rapidité et la fiabilité selon lesquelles il peut être déployé.

Cela étant dit, n’oubliez pas que les conteneurs s’exécutent sur un hôte conteneur – un système d’exploitation, et dans Azure cela signifie une machine virtuelle Azure. Même si l’idée des conteneurs vous plaît déjà, vous aurez toujours besoin d’une infrastructure de machines virtuelles qui héberge les conteneurs. Cependant, l’avantage est que les conteneurs ne se préoccupent de la machine virtuelle sur laquelle ils sont exécutés (bien que le conteneur puisse exiger un environnement d’exécution Linux ou Windows, par exemple).

## Quels sont les avantages des conteneurs ?

Ils sont utiles dans de nombreux cas mais, comme [Azure Cloud Services](http://azure.microsoft.com/services/cloud-services/) et [Azure Service Fabric](service-fabric-overview.md), ils favorisent la création d’applications distribuées orientées [microservices] à service unique, dans laquelle la conception d’applications repose sur des éléments composables plus petits, plutôt que sur des composants plus étroitement associés.

Cela est particulièrement vrai dans les environnements de cloud public comme Azure, dans lesquels vous louez des machines virtuelles quand et où vous le souhaitez. Non seulement vous bénéficiez de l’isolation, du déploiement rapide et des outils d’orchestration, mais vous pouvez prendre des décisions d’infrastructure des applications plus efficaces.

Par exemple, votre déploiement actuel est peut-être composé de 9 machines virtuelles Azure de grande taille pour une application distribuée et à haute disponibilité. Si les composants de cette application peuvent être déployés dans des conteneurs, vous pourrez peut-être utiliser 4 machines virtuelles uniquement et déployer les composants de votre application dans 20 conteneurs pour la redondance et l’équilibrage de charge.

Bien sûr, c’est juste un exemple. Mais si vous pouvez adopter cette solution dans votre scénario, vous pourrez vous adapter aux pics d’utilisation avec plus de conteneurs au lieu de plusieurs machines virtuelles Azure et utiliser la charge processeur globale restante beaucoup plus efficacement qu’auparavant.

En outre, il existe plusieurs scénarios qui ne se prêtent pas à une approche microservices ; vous saurez mieux quels conteneurs et microservices adopter.

### Avantages des conteneurs pour les développeurs

En général, il est facile de constater que la technologie des conteneurs constitue un grand pas en avant, mais il existe également des avantages plus spécifiques. Prenons l’exemple des conteneurs Docker. Cette rubrique ne développe pas en profondeur Docker pour le moment (lisez les pages [Qu’est-ce que Docker ?](https://www.docker.com/whatisdocker/) ou [Wikipédia](http://wikipedia.org/wiki/Docker_%28software%29) pour en savoir plus), mais Docker et son écosystème offrent des avantages considérables aux développeurs et professionnels de l’informatique.

Les développeurs adoptent rapidement les conteneurs Docker car, avant tout, ils simplifient l’utilisation des conteneurs Linux :

- Ils peuvent utiliser des commandes simples, incrémentielles pour créer une image fixe, facile à déployer, et peuvent automatiser la création de ces images grâce à un fichier docker
- Ils peuvent partager ces images en un tournemain en utilisant des commandes simples par envoi ou par extraction de type [git](https://git-scm.com/) sur des [registres Docker privés](virtual-machines-docker-registry-on-azure-blob-storage.md) ou [publics](https://registry.hub.docker.com/) 
- Ils peuvent penser en termes de composants d’application isolés au lieu d’ordinateurs
- Ils peuvent utilisent un grand nombre d’outils qui comprennent les conteneurs Docker et différentes images de base

### Avantages des conteneurs pour les professionnels opérationnels et informatiques

Les professionnels informatiques et opérationnels bénéficient également de la combinaison des conteneurs et des machines virtuelles :

- les services à relation contenant-contenu sont isolés de l’environnement d’exécution hôte de machines virtuelles ;
- le code à relation contenant-contenu est rigoureusement identique ;
- les services à relation contenant-contenu peuvent être démarrés, arrêtés et déplacés rapidement entre les environnements de développement, de test et de production.

De telles fonctionnalités – et elles il y en a d’autres – sont attractives pour les entreprises établies, où les départements d’informatique ont pour mission d’adapter les ressources – y compris la puissance de traitement – aux tâches requises, non seulement pour rester dans la course mais également pour augmenter leur portée et la satisfaction des clients. Les petites entreprises, les ISV et les start-ups ont exactement les mêmes exigences mais peuvent les décrire différemment.

## Quels sont les avantages des machines virtuelles ?

Les machines virtuelles constituent la colonne vertébrale du cloud computing. Rien de nouveau de ce côté. Si les machines virtuelles démarrent plus lentement, présentent un encombrement de disques plus élevé et ne correspondent pas directement à une architecture de microservices, elles offrent des avantages très importants :

1. Par défaut, elles offrent des protections de sécurité par défaut bien plus robustes pour l’ordinateur hôte.
2. Elles prennent en charge les principaux systèmes d’exploitation et les configurations d’applications
3. Elles disposent d’écosystèmes d’outils éprouvés pour le contrôle
4. Elles fournissent l’environnement d’exécution permettant d’héberger les conteneurs

Ce dernier élément est important car une application à relation contenant-contenu nécessite toujours des types de système d’exploitation et de processeur spécifiques, selon les appels que l’application passera. Il est essentiel de se rappeler que vous installez les conteneurs sur des machines virtuelles car ils contiennent les applications que vous souhaitez déployer. Les conteneurs ne remplacent ni les machines virtuelles ni les systèmes d’exploitation.

## Comparaison des fonctionnalités générales des machines virtuelles et des conteneurs

Le tableau suivant décrit de manière très générale le type de différences de fonctionnalités (sans trop de travail supplémentaire) entre des machines virtuelles et des conteneurs Linux. Notez que certaines fonctionnalités peuvent être plus ou moins souhaitables selon vos propres besoins en matière d’applications, et que, à l’instar de tous les logiciels, un travail supplémentaire accroît la prise en charge des fonctionnalités, notamment dans le domaine de la sécurité.

| Fonctionnalité | Machines virtuelles | Conteneurs |
| :------------- |-------------| ----------- |
| Prise en charge de la sécurité « par défaut » | À un degré supérieur | À un degré légèrement moindre |
| Mémoire nécessaire sur disque | Système d'exploitation complet, plus des applications | Conditions des applications uniquement |
| Durée de démarrage | Nettement plus long : démarrage du système d’exploitation et chargement des applications | Nettement plus court : seules les applications doivent démarrer du fait que le noyau est déjà en cours d’exécution |
| Portabilité | Portable avec une préparation adéquate | Portable dans le format d’image ; en général de format inférieur | 
| Automatisation des images | Varie considérablement en fonction du système d'exploitation et des applications | [Registre Docker](https://registry.hub.docker.com/) ; autres

## Création et gestion de groupes de machines virtuelles et de conteneurs

À ce stade, un architecte, développeur ou spécialiste des opérations informatiques pourrait penser : « Je peux automatiser TOUTES ces opérations ; le Data-Center-As-A-Service est une RÉALITÉ ! »

Vous avez raison, cela peut être le cas et il existe de nombreux systèmes, que vous utilisez déjà pour beaucoup d’entre eux, qui peuvent gérer des groupes de machines virtuelles Azure et injecter du code personnalisé en utilisant des scripts, souvent avec [CustomScriptingExtension pour Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) ou [CustomScriptingExtension pour Linux](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Vous pouvez automatiser vos déploiements Azure (et peut-être l’avez-vous déjà fait) en utilisant PowerShell ou des scripts de l’interface de ligne de commande Azure [comme indiqué ici](virtual-machines-create-multi-vm-deployment-xplat-cli-install.md).

Souvent, ces fonctionnalités sont ensuite migrées vers des outils tels que [Puppet](https://puppetlabs.com/) et [Chef](https://www.chef.io/) pour automatiser la création et la configuration des machines virtuelles à l’échelle. (Des liens expliquant l’utilisation de ces outils avec Azure sont disponibles [ici](#tools-for-working-with-containers).)

### Modèles de groupes de ressources Azure

Plus récemment, Azure a publié l’API REST de [gestion des ressources Azure](virtual-machines-azurerm-versus-azuresm.md) et a mis à jour les outils PowerShell et les outils de l’interface de ligne de commande Azure pour en faciliter l'utilisation. Vous pouvez déployer, modifier ou redéployer des topologies d’application intégrales à l’aide de [modèles Azure Resource Manager](../resource-group-authoring-templates.md) avec l’API de gestion des ressources Azure en utilisant :

- le [Portail Azure en version préliminaire en utilisant des modèles](https://github.com/Azure/azure-quickstart-templates) (astuce : utilisez le bouton « DeployToAzure ») ;
- l’[interface de ligne de commande Azure](virtual-machines-deploy-rmtemplates-azure-cli.md) ;
- les [modules Azure PowerShell](virtual-machines-deploy-rmtemplates-azure-cli.md).


### Déploiement et gestion de groupes entiers de machines virtuelles Azure et de conteneurs

Plusieurs systèmes courants peuvent déployer des groupes entiers de machines virtuelles et installer Docker (ou autres systèmes d’hébergement de conteneurs Linux ) comme un groupe automatisable. Pour des liens directs, consultez la section [Conteneurs et outils](#containers-and-vm-technologies) ci-dessous. Plusieurs systèmes peuvent plus ou moins réaliser ces opérations ; cette liste n’est pas exhaustive. En fonction de vos compétences et scénarios, ils peuvent se révéler utiles ou non

Docker possède son propre jeu d’outils de création de machines virtuelles ([docker-machine](virtual-machines-docker-machine.md)), ainsi qu’un outil de gestion des clusters, conteneur de Docker et à équilibrage de charge ([swarm](virtual-machines-docker-swarm.md)). En outre, l’[extension Docker VM Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) est prise en charge par défaut pour [`docker-compose`](https://docs.docker.com/compose/) qui peut déployer des conteneurs d’applications configurés sur plusieurs conteneurs.

En outre, vous pouvez essayer le [système d’exploitation de centre de données (Data Center Operating System, DCOS) de Mesosphere](http://docs.mesosphere.com/install/azurecluster/). DCOS est basé sur le « noyau de systèmes distribués » open source [mesos](http://mesos.apache.org/) qui vous permet de traiter votre centre de données comme un service adressable. DCOS intègre des packages pour plusieurs systèmes importants tels que [Spark](http://spark.apache.org/), [Kafka](http://kafka.apache.org/) et d’autres, ainsi que des services intégrés tels que [Marathon](https://mesosphere.github.io/marathon/) (un système de contrôle de conteneurs) et [Chronos](https://mesosphere.github.io/chronos/) (un planificateur distribué). Mesos est un dérivé de leçons tirées de Twitter, d’AirBnb et autres entreprises web à grande échelle.

En outre, [kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure) est un système open source pour la gestion de groupes de machines virtuelles et de conteneurs, fondé sur les enseignements tirés de Google. Vous pouvez même utiliser [kubernetes avec weave pour fournir la prise en charge réseau](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) est une plateforme Paas (Platform-as-a-Service) open source qui facilite le déploiement et la gestion des applications sur vos propres serveurs. Deis repose sur Docker et CoreOS pour fournir une plateforme PaaS légère avec un workflow inspiré de Heroku. Vous pouvez facilement [créer un groupe de machines virtuelles Azure à 3 nœuds et installer Deis](virtual-machines-deis-cluster.md) sur Azure, puis [installer une application Go Hello World](virtual-machines-deis-cluster.md#deploy-and-scale-a-hello-world-application).

[CoreOS](virtual-machines-linux-coreos-how-to.md), une distribution Linux avec un encombrement optimisé, la prise en charge Docker et leur propre système de conteneurs appelé [rkt](https://github.com/coreos/rkt) disposent également d’un outil de gestion de groupes de conteneurs appelé [fleet](virtual-machines-linux-coreos-fleet-get-started.md).

Ubuntu, une autre distribution Linux très populaire, prend très bien en charge Docker mais également les [clusters Linux (de type LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## Outils de travail avec les machines virtuelles Azure et les conteneurs

Le travail avec les conteneurs et les machines virtuelles Azure requiert des outils. Cette section fournit une liste de certains des concepts et outils les plus utiles ou importants pour les conteneurs et les groupes, ainsi que des outils plus vastes d’orchestration et de configuration associés.

> [AZURE.NOTE]Ce domaine évolue très rapidement et bien que nous nous efforcions de tenir cette rubrique et ses liens à jour, la tâche pourrait bien se révéler impossible. Assurez-vous d’effectuer des recherches sur les sujets intéressants pour vous tenir informé !

### Technologies relatives aux conteneurs et aux machines virtuelles

Certaines technologies de conteneurs Linux :

- [Docker](https://www.docker.com)
- [LXC](https://linuxcontainers.org/)
- [CoreOS et rkt](https://github.com/coreos/rkt)
- [Open Container Project](http://opencontainers.org/)
- [RancherOS](http://rancher.com/rancher-os/)

Liens pour les conteneurs Windows Server :

- [Conteneurs Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Liens pour Docker pour Visual Studio :

- [Visual Studio 2015 RC Tools pour Docker - Aperçu](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Outils Docker :

- [Démon Docker](https://docs.docker.com/installation/#installation)
- Clients Docker
	- [Windows Docker Client pour Chocolatey](https://chocolatey.org/packages/docker)
	- [Instructions d’installation de Docker](https://docs.docker.com/installation/#installation)


Docker sur Microsoft Azure :

- [Extension Docker VM pour Linux dans Azure](virtual-machines-docker-vm-extension.md)
- [Guide de l’utilisateur de l’extension Docker VM Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
- [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Microsoft Azure](virtual-machines-docker-with-xplat-cli-install.md)
- [Utilisation de l’extension Docker VM à partir du portail Azure en version préliminaire](virtual-machines-docker-with-portal.md)
- [Prise en main rapide de Docker dans Azure Marketplace](virtual-machines-docker-ubuntu-quickstart.md)
- [Comment utiliser docker-machine sur Azure](virtual-machines-docker-machine.md)
- [Comment utiliser docker avec swarm sur Azure](virtual-machines-docker-swarm.md)
- [Prise en main de Docker et Compose sur Microsoft Azure](virtual-machines-docker-compose-quickstart.md)
- [Utilisation d’un modèle de groupe de ressources Azure pour créer rapidement un hôte Docker sur Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
- [Prise en charge intégrée de `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) pour les applications à relation contenant-contenu
- [Déploiement de votre propre registre Docker privé sur Azure](virtual-machines-docker-registry-on-azure-blob-storage.md)

Distributions Linux et exemples Azure :

- [CoreOS](virtual-machines-linux-coreos-how-to.md)

Configuration, gestion de clusters et orchestration de conteneurs :

- [Fleet sur CoreOS](virtual-machines-linux-coreos-fleet-get-started.md)

-	Deis
	- [Créer un groupe de machines virtuelles Azure à 3 nœuds, installer Deis et démarrer une application Go Hello World](virtual-machines-deis-cluster.md)
	
-	Kubernetes
	- [Guide complet du déploiement automatisé de cluster Kubernetes avec CoreOS et Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualiseur Kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
	
-	[Mesos](http://mesos.apache.org/)
	-	[Système d’exploitation de centre de données (Data Center Operating System, DCOS) de Mesosphere](http://beta-docs.mesosphere.com/install/azurecluster/)
	
-	[Jenkins](https://jenkins-ci.org/) et [Hudson](http://hudson-ci.org/)
	- [Blog : Plug-in Jenkins Slave pour Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Référentiel GitHub : Plug-in Jenkins Storage pour Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Tiers : Plug-in Hudson Slave pour Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Tiers : Plug-in Hudson Storage pour Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
	
-	[Chef](https://docs.chef.io/index.html)
	- [Chef et Virtual Machines](virtual-machines-windows-install-chef-client.md)
	- [Vidéo : Description et fonctionnement de Chef](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

-	[Azure Automation](http://azure.microsoft.com/services/automation/)
	- [Vidéo : Comment utiliser Azure Automation avec des machines virtuelles Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
	
-	Configuration d'état souhaité Powershell pour Linux
    - [Blog : Configuration d’état souhaité PowerShell pour Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub : Configuration d’état souhaité du client Docker](https://github.com/anweiss/DockerClientDSC)

## Étapes suivantes

Découvrez [Docker](https://www.docker.com) et les [conteneurs Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservices]: http://martinfowler.com/articles/microservices.html
<!--Image references-->

<!---HONumber=Oct15_HO1-->