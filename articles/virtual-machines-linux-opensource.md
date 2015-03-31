<properties
  pageTitle="Linux et open source dans Azure"
  description="Cette rubrique présente une liste d'informations sur Linux et l'open source dans Azure, notamment sur l'utilisation de base de Linux, certains concepts essentiels sur l'exécution ou le téléchargement d'images Linux dans Azure, ainsi que d'autres indications sur des technologies et optimisations spécifiques."
  documentationCenter=""
  authors="squillace"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/28/2015"
  ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Linux et open source dans Azure

Ce document tente de regrouper l'ensemble des rubriques rédigées par Microsoft et ses partenaires à propos de l'exécution de machines virtuelles basées sur Linux ou d'autres environnements et applications open source sur Microsoft Azure. Les univers d'Azure et de l'open source évoluant assez vite, ce document est probablement obsolète, *bien que* nous nous efforcions de constamment ajouter de nouvelles rubriques et supprimer celles qui ne sont plus d'actualité. Si vous constatez qu'une rubrique obsolète n'a pas été supprimée, n'hésitez pas à laisser un commentaire ou envoyez une demande d'extraction à notre [référentiel Github](https://github.com/Azure/azure-content/).

## Remarques générales
Les sections sont énumérées sur la droite de cette page. (Les rubriques pouvant traiter de plusieurs concepts, distributions ou technologies, il est possible qu'un lien apparaisse dans plusieurs sections.) Les rubriques suivantes décrivent quant à elles des options Linux, référentiels d'images et études de cas, ou encore les procédures permettant de télécharger vos images personnalisées : 

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index)
- [Événements et démonstrations : Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Procédure : téléchargement de votre propre image de distribution](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd/) (et instructions pour utiliser une [distribution approuvée dans Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/))
- [Notes : configuration générale requise pour exécuter Linux dans Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-generic/)
- [Notes : présentation générale de Linux dans Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-introduction/)

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

Il existe de nombreuses distributions de Linux, généralement réparties selon le système de gestion des packages : basé sur dpgk, comme Debian et Ubuntu, ou basé sur rpm, comme CentOS, SUSE et RedHat. Certaines sociétés fournissent des images de distribution en tant que partenaires officiels de Microsoft, qui sont approuvées. D'autres images sont fournies par la communauté. Les distributions décrites dans cette section font l'objet d'articles officiels, même lorsqu'elles sont simplement citées en exemple d'autres technologies.

### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu est une distribution de Linux très répandue et approuvée par Azure, basée sur la gestion de packages dkpg et apt-get.

1. [Procédure : téléchargement de votre propre image Ubuntu](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-ubuntu/)
2. [Procédure : pile LAMP Ubuntu](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
2. [Images : pile LAPP](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Procédure : clusters MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
4. [Procédure : Node.js et Cassandra](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. [Procédure : IPython Notebook](http://azure.microsoft.com/documentation/articles/virtual-machines-python-ipython-notebook/)
6. [Application : Exécution d'ASP.NET 5 sur Linux à l'aide de conteneurs Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Images : Redis Server](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Images : Minecraft Server](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Images : Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Images : Mono as a Service](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)

### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian est une distribution importante pour Linux et l'univers open source, basée sur la gestion de packages dpgk et apt-get. Le dépôt de machines virtuelles MSOpenTech compte plusieurs images disponibles.

### CentOS

La distribution Linux CentOS est une plateforme stable, prévisible, gérable et reproductible dérivée des sources de Red Hat Enterprise Linux (RHEL).

1. [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Galerie d'images](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Procédure : préparation d'une machine virtuelle basée sur CentOS personnalisée pour Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-centos/)
4. [Blog : déploiement d'une image de machine virtuelle CentOS à partir d'OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Procédure : installation d'Apache Qpid Proton-C pour AMQP et Service Bus](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Images : Apache 2.2.15 sur OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Images : Drupal 7.2, LAMP Server sur OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

### SUSE Enterprise Linux et OpenSUSE

9. [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Procédure : installation et exécution de MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
12. [Procédure : préparation d'une machine virtuelle SLES ou openSUSE personnalisée](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/)  
13. [[Forum SUSE] Procédure : déplacement vers un nouveau serveur de correctifs](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Images : SUSE Linux Enterprise Server pour SAP Cloud Appliance Library](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS est une petite distribution optimisée pour la mise à l'échelle pure avec un niveau élevé de contrôle pour la personnalisation.

10. [Galerie d'images](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Procédure : utilisation de CoreOS sur Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
12. [Blog : TechEd Europe -- Conteneurs du client Windows Docker et de Linux](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
13. [Blog : Azure devient plus grand, plus rapide et plus ouvert](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
14. [Github : démarrage rapide pour le déploiement de CoreOS dans Azure](https://github.com/timfpark/coreos-azure)
15. [Github : déploiement d'applications Java avec Spring Boot, MongoDB et CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Préparation d'une machine virtuelle Linux Oracle pour Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-oracle/)

### FreeBSD

12. [Dépôt de machines virtuelles MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog : exécution de FreeBSD dans Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog : déploiement facile de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog : déploiement d'une image FreeBSD personnalisée](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Procédure : installation de l'agent Linux Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
18. [Marketplace : Kaspersky Antivirus pour Linux File Server](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Concepts de base

1. [Concepts de base : interface de ligne de commande Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/)
4. [Concepts de base : utilisation et gestion des certificats](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Concepts de base : sélection de noms d'utilisateurs Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-usernames/)
6. [Concepts de base : connexion à une machine virtuelle Linux à l'aide du portail Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-log-on/)
7. [Concepts de base : SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Concepts de base : réinitialisation d'un mot de passe ou des propriétés SSH pour Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Concepts de base : utilisation de la racine](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)
10. [Concepts de base : association d'un disque de données à une machine virtuelle Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)
11. [Concepts de base : détachement d'un disque de données d'une machine virtuelle Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-detach-disk/)
12. [Blog sur les concepts de base : optimisation du stockage, des disques et des performances avec Linux et Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Concepts de base : RAID](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/)
14. [Concepts de base : capture d'une machine virtuelle Linux pour en faire un modèle](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/)
15. [Concepts de base : agent Linux Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
16. [Concepts de base : extensions et fonctionnalités des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Concepts de base : injection de données personnalisées dans une machine virtuelle à utiliser avec Cloud-init](http://azure.microsoft.com/documentation/articles/virtual-machines-how-to-inject-custom-data/)
18. [Blog sur les concepts de base : génération d'une distribution Linux à haute disponibilité dans Azure en 12 étapes](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Blog sur les concepts de base : automatisation de l'approvisionnement de Linux dans Azure avec xplat, node.js et jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
19. [Créer un déploiement de plusieurs machines virtuelles à l'aide de l'interface de ligne de commande multiplateforme Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-create-multi-vm-deployment-xplat-cli/)
20. [Concepts de base : extension Docker VM dans Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
23. [Documentation de référence sur l'](https://msdn.microsoft.com/library/azure/ee460799.aspx)API REST de gestion des services Azure
24. [GlusterFS sur Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Référentiels et images de la communauté
3. [Dépôt de machines virtuelles](https://vmdepot.msopentech.com/List/Index) &mdash; pour les images de machines virtuelles fournies par la communauté.
4. [Github](https://github.com/Azure/) &mdash; pour xplat-cli et de nombreux autres outils et projets.
5. [Docker Hub Registry](https://registry.hub.docker.com/) &mdash; Registre réservé aux images de conteneurs Docker.

## Langages et plateformes
### [Centre de développement Java pour Azure](http://azure.microsoft.com/develop/java/)

1. [Images](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Procédure : utilisation de Service Bus à partir de Java avec AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Procédure : configuration de Tomcat7 sur Linux à l'aide du portail Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-setup-tomcat7-linux/)
4. [Vidéo : Kit de développement logiciel (SDK) Azure Java pour la gestion des services](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog : prise en main des bibliothèques de gestion Azure pour Java](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Référentiel Github : Kit de ressources Azure pour Eclipse avec Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Référence : Kit de ressources Azure pour Eclipse avec Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Référentiel Github : plug-in Outils MS Open Tech pour IntelliJ IDEA et Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog : contributions de MSOpenTech à OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Images : WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Images : WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [Images : JDK6 sur Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [Images : JDK7 sur Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [Images : JDK8 sur Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

### Langages JVM

1. [Scala : exécution d'applications Play Framework dans Azure Cloud Services](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### Types de Kit de développement logiciel (SDK), installations et mises à niveau
4. [SDK Azure Service Management : Java](http://dl.windowsazure.com/javadoc/)
5. [SDK Azure Service Management : Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [SDK Azure Service Management : Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [Procédure : installation de Ruby sur Rails](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-rails-web-app-linux/)
    - [Procédure : installation de Ruby sur Rails avec Capistrano, Nginx, Unicorn et PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)
6. [SDK Azure Service Management : Python](https://github.com/Azure/azure-sdk-for-python)
    - [Procédure : application web Django Hello World (Mac-Linux)](http://azure.microsoft.com/documentation/articles/virtual-machines-python-django-web-app-linux/)
7. [SDK Azure Service Management : Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [SDK Azure Service Management : PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [Procédure : installation de la pile LAMP sur une machine virtuelle Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
    - [Vidéo : installation d'une pile LAMP sur une machine virtuelle Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [SDK Azure Service Management : .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog : Mono, ASP.NET 5, Linux et Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## Exemples et scripts

Consultez cette liste qui va très vite s'allonger. Si vous avez des suggestions, envoyez-nous une réponse privée ou laissez-nous un commentaire ci-dessous.

1. [Créer un déploiement de plusieurs machines virtuelles à l'aide de l'interface de ligne de commande multiplateforme Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-create-multi-vm-deployment-xplat-cli/)
2. [Référentiel Github Linux Azure de Patrick Chanezon](https://github.com/chanezon/azure-linux)
3. [Vidéo : déplacement de données USB locales sur Linux vers Azure à l'aide d'**usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Vidéo : accès à une GUI Linux dans Azure dans le navigateur avec fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Vidéo : stockage partagé sur Linux à l'aide de l'aperçu de fichiers Azure -- 1ère partie](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Vidéo : adoption de périphériques Linux dans Azure à l'aide de Service Bus et de sites web](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Vidéo : connexion d'une application mise en cache Linux native à Windows Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Vidéo : Équilibrage de charge des services Linux à haute disponibilité dans Azure : OpenLDAP et MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Données

Cette section contient des informations sur différentes approches et technologies de stockage, notamment NoSQL, Relational et Big Data.

### Nosql

1. [Blog : 8 sources de données NoSql open source pour Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech) : expériences d'utilisation de CouchDb dans Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog : exécution de CouchDB-as-a-Service avec node.js, CORS et Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [Procédure : création d'une application Node.js sur Azure avec MongoDB à l'aide du module MongoLab](http://azure.microsoft.com/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/)
4. Cassandra
    - [Procédure : exécution de Cassandra avec Linux sur Azure et accès au cluster depuis Node.js](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. Redis
    - [Blog : Redis sur Windows dans le service de mise en cache Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog : annonce du fournisseur d'état de session ASP.NET pour la version d'aperçu de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog : RavenHQ désormais disponible dans Azure Marketplace](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Données volumineuses (" Big Data ")
2. Hadoop/Cloudera  
	- [Blog : installation de Hadoop sur des machines virtuelles Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Procédure : prise en main de Hadoop et Hive avec HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-get-started/)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) : un service Hadoop entièrement géré dans Azure.

### Données relationnelles
2. MySQL
    - [Procédure : installation et exécution de MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
    - [Procédure : optimisation des performances de MySQL dans Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/)
    - [Procédure : clusters MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
    - [Procédure : création d'une base de données MySQL à l'aide de Marketplace](http://azure.microsoft.com/documentation/articles/store-php-create-mysql-database/)
    - [Procédure : Django et MySQL sur des sites web Azure avec Python et Visual Studio](http://azure.microsoft.com/documentation/articles/web-sites-python-ptvs-django-mysql/)
    - [Procédure : PHP et MySQL sur des sites web Azure avec WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/)
7. MariaDB
    - [Procédure : création d'un cluster multimaîtres de bases de données MariaDb](http://azure.microsoft.com/documentation/articles/virtual-machines-mariadb-cluster/)
7. PostgreSQL
    - [Procédure : installation de Ruby sur Rails avec Capistrano, Nginx, Unicorn et PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)
8. [Installation de Postgres avec corosync, pg_bouncer en utilisant ILB](https://github.com/chgeuer/postgres-azure) 


## <a id='security'>Authentification et chiffrement</a>

L'authentification et le chiffrement sont des thèmes essentiels du développement logiciel. De nombreuses rubriques disponibles sur le web décrivent les techniques de sécurité appropriées à chacun et la manière de les utiliser. Nous présentons certaines utilisations de base pour être opérationnel rapidement sur Linux et les charges de travail open source. Nous indiquons également des outils permettant de réinitialiser ou de supprimer des fonctionnalités de sécurité distantes dans Azure. Il s'agit de procédures de base, auxquelles nous ajouterons bientôt des scénarios plus complexes. 

4. [Concepts de base : utilisation et gestion des certificats](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Concepts de base : SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Concepts de base : comment réinitialiser un mot de passe ou des propriétés SSH pour Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Concepts de base : utilisation de la racine](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)

## DevOps, gestion et optimisation

Cette section présente en premier lieu une entrée de blog contenant une série de vidéos sur [Vidéo : machines virtuelles Azure : utilisation de Chef, Puppet et Docker pour la gestion des machines virtuelles Linux](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Cependant, les domaines des DevOps, de la gestion et de l'optimisation sont assez vastes et évoluent rapidement. Il convient donc de considérer la liste ci-dessous comme un point de départ.

1. Docker
	- [Extension Docker VM pour Linux dans Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
	- [Utilisation de l'extension Docker VM à partir de l'interface de ligne de commande multiplateforme Azure (xplat-cli)](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/)
	- [Utilisation de l'extension Docker VM à partir du portail Azure en version préliminaire](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/)
	- [Prise en main rapide de Docker dans Azure Marketplace](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/)
	- [Comment utiliser docker-machine sur Azure]
	- [Comment utiliser docker avec swarm sur Azure]
	
2. [Utilisation de CoreOS](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
3. Deis
	- [Référentiel GitHub :
Installation de Deis sur un cluster CoreOS dans Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Visualiseur Kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins et Hudson
	- [Blog : Plug-in Jenkins Slave pour Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Référentiel GitHub : Plug-in Jenkins Storage pour Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Tiers : Plug-in Hudson Slave pour Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Tiers : Plug-in Hudson Storage pour Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef et machines virtuelles](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-install-chef-client/)
	- [Vidéo : Description et fonctionnement de Chef](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure Automation
	- [Vidéo : comment utiliser Azure Automation avec des machines virtuelles Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Configuration d'état souhaité Powershell pour Linux
    - [Blog : configuration d'état souhaité Powershell pour Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [Github : configuration d'état souhaité du client Docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Plug-in Packer pour Azure](https://github.com/msopentech/packer-azure)

## Support technique, résolution des problèmes et " Ça ne fonctionne pas "

1. Documentation de support technique Microsoft
	- [Support technique : support technique pour les images Linux dans Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distributions]: #distros
[Concepts de base]: #basics
[Référentiels et images de la communauté]: #images
[Langages et plateformes]: #langsandplats
[Exemples et scripts]: #samples
[Authentification et chiffrement]: #security
[DevOps, gestion et optimisation]: #devops
[Support technique, résolution des problèmes et " Ça ne fonctionne pas "]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash.>
[Comment utiliser docker-machine avec Azure]: ../virtual-machines-docker-machine/
[Comment utiliser docker avec swarm sur Azure]: ../virtual-machines-docker-swarm/

<!--HONumber=47-->
