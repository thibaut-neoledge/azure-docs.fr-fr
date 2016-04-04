<properties
	pageTitle="Linux et informatique open-source sur Azure | Microsoft Azure"
	description="Répertorie des articles sur Linux et l’informatique open source dans Azure, notamment sur l’utilisation de base de Linux, certains concepts essentiels sur l’exécution ou le téléchargement d’images Linux dans Azure, ainsi que d’autres indications sur des technologies et optimisations spécifiques."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/03/2016"
	ms.author="rasquill"/>



# Linux et informatique open-source sur Azure

Ce document tente de regrouper l'ensemble des rubriques rédigées par Microsoft et ses partenaires à propos de l'exécution de machines virtuelles basées sur Linux ou d'autres environnements et applications open source sur Microsoft Azure. Les articles qui décrivent uniquement le modèle de déploiement classique sont signalés, à l’instar de ceux qui utilisent le modèle de déploiement de Resource Manager. Les articles dépourvus de signalement de modèle de déploiement décrivent les deux modèles.

Les univers d’Azure et de l’open source évoluant assez vite, ce document est probablement obsolète,*bien que* nous nous efforcions de constamment ajouter de nouvelles rubriques et supprimer celles qui ne sont plus d’actualité. Si vous constatez qu’une rubrique obsolète n’a pas été supprimée, n’hésitez pas à laisser un commentaire ou envoyez une demande d’extraction à notre [référentiel Github](https://github.com/Azure/azure-content/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Remarques générales
Les sections sont énumérées sur la droite de cette page. (Les rubriques pouvant traiter de plusieurs concepts, distributions ou technologies, il est possible qu'un lien apparaisse dans plusieurs sections.) Les rubriques suivantes décrivent quant à elles des options Linux, référentiels d'images et études de cas, ou encore les procédures permettant de télécharger vos images personnalisées :

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index)
- [Evénements et démonstrations : Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Déploiement classique] : [Chargement de votre propre image de distribution](virtual-machines-linux-classic-create-upload-vhd.md) (et instructions pour utiliser une [distribution approuvée dans Azure](virtual-machines-linux-endorsed-distros.md))
- [Remarques : configuration générale requise pour exécuter Linux dans Azure](virtual-machines-linux-create-upload-generic.md)
- [Déploiement classique] : [Remarques : présentation générale de Linux dans Azure](virtual-machines-linux-intro-on-azure.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## Distributions

Il existe de nombreuses distributions de Linux, généralement réparties selon le système de gestion des packages : certaines sont basées sur dpkg, comme Debian et Ubuntu, tandis que d’autres sont basées sur rpm, comme CentOS, SUSE et RedHat. Certaines sociétés fournissent des images de distribution en tant que partenaires officiels de Microsoft, qui sont approuvées. D'autres images sont fournies par la communauté. Les distributions décrites dans cette section font l'objet d'articles officiels, même lorsqu'elles sont simplement citées en exemple d'autres technologies.

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu est une distribution de Linux très répandue et approuvée par Azure, basée sur la gestion de packages dkpg et apt-get.

1. [Téléchargement de votre propre image Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)
2. [Pile LAMP Ubuntu](virtual-machines-linux-install-lamp-stack.md)
3. [Déploiement classique] : [Clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
4. [Déploiement classique] : [Node.js et Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
5. [Déploiement de Resource Manager] : [Notebook IPython](virtual-machines-linux-jupyter-notebook.md)
6. [Déploiement classique] : [Exécution de ASP.NET 5 sur Linux à l’aide de conteneurs Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian est une distribution importante pour Linux et l’univers open source, basée sur la gestion de packages dpgk et apt-get. Le dépôt de machines virtuelles MSOpenTech compte plusieurs images disponibles.

### CentOS

La distribution Linux CentOS est une plateforme stable, prévisible, gérable et reproductible dérivée des sources de Red Hat Enterprise Linux (RHEL).

1. [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Galerie d’images](https://azure.microsoft.com/marketplace/partners/OpenLogic/)
3. [Préparation d’une machine virtuelle basée sur CentOS personnalisée pour Azure](virtual-machines-linux-create-upload-centos.md)
4. [Déploiement classique] : [Blog : Déploiement d’une image de machine virtuelle CentOS à partir d’OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Déploiement classique] : [Installation d’Apache Qpid Proton-C pour AMQP et Service Bus](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server et openSUSE

9. [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Déploiement classique] : [Installation et exécution de MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
12. [Préparation d’une machine virtuelle SLES ou openSUSE personnalisée](virtual-machines-linux-suse-create-upload-vhd.md)  
13. [[Forum SUSE] Déplacement vers un nouveau serveur de correctifs](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Images : SUSE Linux Enterprise Server pour SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS est une petite distribution optimisée pour la mise à l'échelle pure avec un niveau élevé de contrôle pour la personnalisation.

10. [Galerie d’images](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [Déploiement classique] : [Utilisation de CoreOS sur Azure](virtual-machines-linux-classic-coreos-howto.md)
12. [Déploiement classique] : [Prise en main de Fleet et de Docker avec CoreOS sur Azure](virtual-machines-linux-classic-coreos-fleet-get-started.md)


#### [Oracle Linux](https://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Préparation d’une machine virtuelle Linux Oracle pour Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

### FreeBSD

12. [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Déploiement classique] : [Blog : Exécution de FreeBSD dans Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Déploiement classique] : [Blog : Déploiement facile de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog : Déploiement d’une image FreeBSD personnalisée](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Installation de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace : Kaspersky Antivirus pour Linux File Server](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Concepts de base

1. [Concepts de base : interface de ligne de commande Microsoft Azure (Azure CLI)](../xplat-cli-install.md)
<!-- 4. [The basics: Certificate Use and Management](http://msdn.microsoft.com/library/azure/gg981929.aspx)-->
5. [Concepts de base : sélection de noms d'utilisateur Linux](virtual-machines-linux-usernames.md)
6. [Déploiement classique] : [Concepts de base : Connexion à une machine virtuelle sur Linux à l’aide du portail Azure Classic](virtual-machines-linux-classic-log-on.md)
7. [Concepts de base : SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Déploiement classique] : [Concepts de base : Comment réinitialiser un mot de passe ou des propriétés SSH pour Linux](virtual-machines-linux-classic-reset-access.md)
9. [Concepts de base : utilisation de la racine](virtual-machines-linux-use-root-privileges.md)
10. [Déploiement classique] : [Concepts de base : Association d’un disque de données à une machine virtuelle Linux](virtual-machines-linux-classic-attach-disk.md)
11. [Déploiement classique] : [Concepts de base : Détachement d’un disque de données d’une machine virtuelle Linux](virtual-machines-linux-classic-detach-disk.md)
12. [Blog sur les concepts de base : optimisation du stockage, des disques et des performances avec Linux et Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Concepts de base : RAID](virtual-machines-linux-configure-raid.md)
14. [Déploiement classique] : [Concepts de base : Capture d’une machine virtuelle Linux pour en faire un modèle](virtual-machines-linux-classic-capture-image.md)
15. [Concepts de base : Agent Linux Azure](virtual-machines-linux-agent-user-guide.md)
16. [Concepts de base : extensions et fonctionnalités des machines virtuelles Azure](virtual-machines-windows-extensions-features.md)
17. [Déploiement classique] : [Concepts de base : Injection de données personnalisées dans une machine virtuelle à utiliser avec Cloud-init](virtual-machines-windows-classic-inject-custom-data.md)
18. [Déploiement classique] : [Blog sur les concepts de base : Génération d’une distribution Linux à haute disponibilité dans Azure en 12 étapes](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Déploiement classique] : [Blog sur les concepts de base : Automatisation de l’approvisionnement de Linux dans Azure avec l’interface de ligne de commande Azure, node.js et jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [Concepts de base : extension de machine virtuelle Docker dans Azure](virtual-machines-linux-dockerextension.md)
23. [Déploiement classique] : Documentation de référence sur l’[API REST Gestion des services Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [Déploiement classique] : [GlusterFS sur Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Référentiels et images de la communauté
3. [Dépôt de machines virtuelles](https://vmdepot.msopentech.com/List/Index) pour les images de machines virtuelles fournies par la communauté.
4. [Github](https://github.com/Azure/) pour l’interface de ligne de commande Microsoft Azure et de nombreux autres outils et projets.
5. [Docker Hub Registry](https://registry.hub.docker.com/) : Registre réservé aux images de conteneurs Docker.

## Langages et plates-formes
### [Centre de développement Java pour Azure](https://azure.microsoft.com/develop/java/)

1. [Images](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Utilisation de Service Bus à partir de Java avec AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Configuration de Tomcat7 sous Linux à l’aide du portail Azure Classic](virtual-machines-linux-classic-setup-tomcat.md)
4. [Vidéo : Kit de développement logiciel (SDK) Azure Java pour la gestion des services](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog : prise en main des bibliothèques de gestion Azure pour Java](https://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Référentiel GitHub : Kit de ressources Azure pour Eclipse avec Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Référence : Kit de ressources Azure pour Eclipse avec Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Référentiel GitHub : Plug-in Outils MS Open Tech pour IntelliJ IDEA et Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog : Contributions de MSOpenTech à OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Images : WebSphere](https://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Images : WebLogic](https://azure.microsoft.com/marketplace/?term=weblogic)


### Langages JVM

1. [Scala : Exécution d’applications Play Framework dans Azure Cloud Services](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### Types de Kits de développement logiciel (SDK), installations et mises à niveau
4. [SDK Azure Service Management : Java](http://dl.windowsazure.com/javadoc/)
5. [SDK Azure Service Management : Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [SDK Azure Service Management : Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [Installation de Ruby sur Rails](virtual-machines-linux-classic-ruby-rails-web-app.md)
6. [SDK Azure Service Management : Python](https://github.com/Azure/azure-sdk-for-python)
    - [Application web Django Hello World (Mac-Linux)](virtual-machines-linux-python-django-web-app.md)
7. [SDK Azure Service Management : Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [SDK Azure Service Management : PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [Installation de la pile LAMP sur une machine virtuelle Azure](virtual-machines-linux-install-lamp-stack.md)
    - [Vidéo : Installation d’une pile LAMP sur une machine virtuelle Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [SDK Azure Service Management : .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog : Mono, ASP.NET 5, Linux et Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## Exemples et scripts

Consultez cette liste qui va très vite s'allonger. Si vous avez des suggestions, envoyez-nous une réponse privée ou laissez-nous un commentaire ci-dessous.

2. [Référentiel Github Linux Azure de Patrick Chanezon](https://github.com/chanezon/azure-linux)
3. [Vidéo : Déplacement de données USB locales sur Linux vers Azure à l’aide d’**usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Vidéo : Accès à une GUI Linux dans Azure dans le navigateur avec fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Vidéo : Stockage partagé sur Linux à l’aide de l’aperçu de fichiers Azure -- 1ère partie](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Vidéo : Adoption de périphériques Linux dans Azure à l’aide de Service Bus et de sites web](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Vidéo : Connexion d’une application mise en cache Linux native à Microsoft Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Vidéo : Équilibrage de charge des services Linux à haute disponibilité dans Azure : OpenLDAP et MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Données

Cette section contient des informations sur différentes approches et technologies de stockage, notamment NoSQL, Relational et Big Data.

### Nosql

1. [Blog : 8 sources de données NoSql open source pour Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech) : Expériences d’utilisation de CouchDb dans Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog : Exécution de CouchDB-as-a-Service avec node.js, CORS et Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [Création d’une application Node.js dans Azure avec MongoDB à l’aide du composant additionnel MongoLab](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md)
4. Cassandra
    - [Exécution de Cassandra avec Linux dans Azure et accès à partir de Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [Blog : Redis sur Windows dans le service de mise en cache Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog : Annonce du fournisseur d’état de session ASP.NET pour la version d’aperçu de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog : RavenHQ désormais disponible dans Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Données volumineuses (« Big Data »)
2. Hadoop/Cloudera  
	- [Blog : Installation de Hadoop sur des machines virtuelles Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) : un service Hadoop entièrement géré dans Azure.

### Base de données relationnelle
2. MySQL
    - [Installation et exécution de MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [Optimisation des performances de MySQL dans Azure](virtual-machines-linux-classic-optimize-mysql.md)
    - [Clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
    - [Création d’une base de données MySQL à l’aide de Marketplace](../store-php-create-mysql-database.md)
    - [Django et MySQL sur des sites Web Azure avec Python et Visual Studio](../app-service-web/web-sites-python-ptvs-django-mysql.md)
    - [Architecture de haute disponibilité MySQL dans Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Création d’un cluster multimaîtres de bases de données MariaDb](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [Installation de Postgres avec corosync, pg\_bouncer en utilisant ILB](https://github.com/chgeuer/postgres-azure)


## Authentification et chiffrement

L'authentification et le chiffrement sont des thèmes essentiels du développement logiciel. De nombreuses rubriques disponibles sur le web décrivent les techniques de sécurité appropriées à chacun et la manière de les utiliser. Nous présentons certaines utilisations de base pour être opérationnel rapidement sur Linux et les charges de travail open source. Nous indiquons également des outils permettant de réinitialiser ou de supprimer des fonctionnalités de sécurité distantes dans Azure. Il s'agit de procédures de base, auxquelles nous ajouterons bientôt des scénarios plus complexes.

4. [Concepts de base : utilisation et gestion des certificats](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Concepts de base : SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Concepts de base : comment réinitialiser un mot de passe ou des propriétés SSH pour Linux](virtual-machines-linux-classic-reset-access.md)
9. [Concepts de base : utilisation de la racine](virtual-machines-linux-use-root-privileges.md)

## Calcul haute performance (HPC) Linux

Exécutez des charges de travail HPC sur des clusters de machines virtuelles Linux créés avec les outils open source ou avec Microsoft HPC Pack.

1.	[Modèle de démarrage rapide : Spin up a SLURM cluster](https://azure.microsoft.com/documentation/templates/slurm/) (et [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[Modèle de démarrage rapide : Création d'un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Didacticiel : prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[Didacticiel : exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[Didacticiel : configuration d'un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)


## DevOps, gestion et optimisation

Cette section présente en premier lieu une entrée de blog contenant une série de vidéos sur [Vidéo : Microsoft Azure Virtual Machines : Utilisation de Chef, Puppet et Docker pour la gestion des machines virtuelles Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Cependant, les domaines des DevOps, de la gestion et de l'optimisation sont assez vastes et évoluent rapidement. Il convient donc de considérer la liste ci-dessous comme un point de départ.

1. Docker
	- [Extension Docker VM pour Linux dans Azure](virtual-machines-linux-dockerextension.md)
	- [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Microsoft Azure](virtual-machines-linux-classic-cli-use-docker.md)
	- [Utilisation de l’extension Docker VM avec le portail Azure](virtual-machines-linux-classic-portal-use-docker.md)
    - [Prise en main rapide de Docker dans Azure Marketplace](virtual-machines-linux-classic-docker-quickstart.md)
	- [Comment utiliser docker-machine sur Azure](virtual-machines-linux-classic-docker-machine.md)
	- [Comment utiliser docker avec swarm sur Azure](virtual-machines-linux-docker-swarm.md)
	- [Prise en main de Docker et Compose sur Microsoft Azure](virtual-machines-linux-docker-compose-quickstart.md)

2. [Utilisation de CoreOS](virtual-machines-linux-classic-coreos-howto.md)
3. Deis
	- [Référentiel GitHub : Installation de Deis sur un cluster CoreOS dans Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Guide complet du déploiement automatisé de cluster Kubernetes avec CoreOS et Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualiseur Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins et Hudson
	- [Blog : Plug-in Jenkins Slave pour Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Référentiel GitHub : Plug-in Jenkins Storage pour Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Tiers : Plug-in Hudson Slave pour Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Tiers : Plug-in Hudson Storage pour Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef et Virtual Machines](virtual-machines-windows-chef-automation.md)
	- [Vidéo : Description et fonctionnement de Chef](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure Automation
	- [Vidéo : Comment utiliser Azure Automation avec des machines virtuelles Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Configuration d'état souhaité Powershell pour Linux
    - [Blog : Configuration d’état souhaité PowerShell pour Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub : Configuration d’état souhaité du client Docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Plug-in Packer pour Azure](https://github.com/msopentech/packer-azure)

## Support technique, résolution des problèmes et « Ça ne fonctionne pas »

1. Documentation de support technique Microsoft
	- [Support : Support technique pour les images Linux dans Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-linux-classic-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md

<!---HONumber=AcomDC_0323_2016-->