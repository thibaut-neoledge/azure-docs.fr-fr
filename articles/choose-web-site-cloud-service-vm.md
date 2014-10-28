<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Websites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Websites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines

Dans Azure, trois méthodes permettent d'héberger les applications Web, comme [Sites Web Azure][], [Azure Cloud Services][] et [Azure Virtual Machines][]. Si vous ne savez pas quelle solution vous correspond le mieux ou si certains concepts comme IaaS ou PaaS ne sont pas clairs, cet article vous aidera à faire le bon choix en fonction de votre scénario Web. Bien que les trois options permettent d'exécuter des applications Web extrêmement évolutives dans Azure, certaines différences vous aideront à prendre une décision.

Dans de nombreuses situations, Sites Web Azure constitue la meilleure option. Ce scénario offre des options de déploiement et de gestion simples et flexibles et permet d'héberger des sites Web volumineux. Vous pouvez créer rapidement un nouveau site Web avec des logiciels reconnus, tels que WordPress, à partir de la galerie d'applications Web ou déplacer un site Web existant vers Sites Web Azure. Le [Kit de développement logiciel (SDK) Azure WebJobs][] (disponible en version préliminaire) permet également d'ajouter un traitement des tâches en arrière-plan.

Vous pouvez également héberger des applications Web dans Azure Cloud Services ou Azure Virtual Machines. Ces options constituent un choix adapté lorsque votre niveau Web requiert le niveau accru de contrôle et de personnalisation qu'elles fournissent. Toutefois, ce contrôle accru implique une plus grande complexité en termes de création, de gestion et de déploiement des applications. Le schéma suivant illustre les compromis entre les trois options.

![ChoicesDiagram][]

Les sites Web sont plus faciles à configurer, à gérer et à surveiller, mais les options de configuration disponibles sont moins nombreuses. S'il ne vous est pas absolument nécessaire de placer votre application Web frontale dans Cloud Services ou Virtual Machines, il est préférable d'utiliser Sites Web Azure. Le reste du document fournit les informations nécessaires à une prise de décisions éclairée, notamment :

-   [Scénarios][]
-   [Synthèse des services][]
-   [Comparaison des fonctionnalités][]

## <a name="scenarios"></a>Scénarios

### Je dirige une PME et je recherche une méthode peu onéreuse pour héberger mon site Web, tout en gardant à l'esprit la future croissance de mon entreprise.

Sites Web Azure est une solution idéale dans ce scénario, car vous pouvez démarrer gratuitement, puis ajouter des fonctionnalités supplémentaires en fonction de vos besoins. Par exemple, chaque site Web gratuit inclut un domaine fourni par Azure (*votre\_entreprise*.azurewebsites.net). Lorsque vous êtes prêt à utiliser votre propre domaine, vous pouvez ajouter celui-ci à partir de 9,80 € par mois (depuis janvier 2014). De nombreux autres services et options de mise à l'échelle permettent de faire évoluer le site en fonction de l'augmentation de la demande utilisateur. **Sites Web Azure** offre les avantages suivants :

-   Commencez gratuitement, puis passez au niveau supérieur en fonction de vos besoins.
-   Utilisez la galerie d'applications pour configurer rapidement des applications Web reconnues, telles que WordPress.
-   Ajoutez des services et fonctionnalités Azure à votre application à votre convenance.
-   Sécurisez votre site Web avec le protocole HTTPS à l'aide du certificat fourni par votre nom de domaine *votre\_entreprise*.azurewebsites.net.

### Je suis un concepteur Web/graphiste et je souhaite concevoir et développer des sites Web pour mes clients

Pour les développeurs Web, Sites Web Azure offre tout ce dont vous avez besoin pour créer des applications Web sophistiquées. Sites Web Azure propose une intégration étroite à des outils tels que Visual Studio et la base de données SQL. **Sites Web Azure** permet aux développeurs d'effectuer les opérations suivantes :

-   utiliser les outils en ligne de commande pour les [tâches automatisées][] ;
-   utiliser des langages reconnus tels que [.Net][], [PHP][], [Node.js][] et [Python][] ;
-   sélectionner trois niveaux de mise à l'échelle pour bénéficier de capacités supérieures ;
-   intégrer d'autres services Azure, tels que [Base de données SQL][], [Service Bus][] et le [Azure Storage][] ou d'autres offres partenaire disponibles dans [Azure Store][], comme MySQL et MongoDB ;
-   intégrer des outils tels que Visual Studio, Git, WebMatrix, WebDeploy, TFS et FTP.

### Je migre actuellement mon application multiniveau et un serveur Web frontal dans le cloud

Si vous exécutez une application multiniveau, telle qu'un serveur Web qui communique avec un serveur de base de données pour stocker et récupérer les données du site Web, Azure propose différentes options d'architecture, notamment Sites Web Azure, Azure Cloud Services et Azure Virtual Machines. Pour commencer, **Sites Web Azure** constitue une option idéale pour le niveau Web de votre solution et peut être utilisé avec la base de données SQL Azure pour créer une architecture sur deux niveaux. Sites Web Azure vous permet également d'exécuter des processus à long terme ou en arrière-plan à l'aide du Kit de développement logiciel (SDK) Azure WebJobs (version préliminaire). Si vous avez besoin d'une architecture plus complexe ou d'options plus flexibles en termes d'évolutivité, Azure Cloud Services ou Azure Virtual Machines représente un choix plus adapté.

**Cloud Services** vous permet d'effectuer les opérations suivantes :

-   héberger des services principaux, Web et de niveau intermédiaire sur des rôles Web et de travail évolutifs ;
-   héberger uniquement des services principaux et de niveau intermédiaire sur des rôles de travail et conserver les services frontaux sur Sites Web Azure ;
-   faire évoluer les services principaux et frontaux indépendamment.

**Virtual Machines** vous permet d'effectuer les opérations suivantes :

-   migrer plus facilement les environnements hautement personnalisés en tant qu'image de machine virtuelle ;
-   exécuter des logiciels ou services qui ne peuvent pas être configurés dans Sites Web ou Cloud Services.

### Mon application dépend d'environnements Windows ou Linux hautement personnalisés

Si votre application nécessite l'installation ou la configuration complexe des logiciels et du système d'exploitation, Virtual Machines représente probablement la meilleure solution. **Virtual Machines** vous permet d'effectuer les opérations suivantes :

-   utiliser la galerie de machines virtuelles pour commencer avec un système d'exploitation tel que Windows ou Linux, puis personnaliser ce dernier en fonction des besoins de votre application ;
-   créer et télécharger une image personnalisée d'un serveur local existant à exécuter sur une machine virtuelle dans Azure.

### Mon site utilise des logiciels open source et je souhaite l'héberger dans Azure

Les trois options vous permettent d'héberger des langages et infrastructures open source. **Cloud Services** requiert que vous fassiez appel à des tâches de démarrage pour installer et configurer les logiciels open source requis exécutés sur Windows. **Virtual Machines** vous permet d'installer et de configurer les logiciels sur l'image de machine virtuelle qui peut être basée sur Windows ou Linux. Si votre infrastructure open source est prise en charge par Sites Web Azure, il s'agit d'un moyen plus simple d'héberger ces types d'applications, car Sites Web Azure peut être configuré automatiquement avec les langages et infrastructures nécessaires à votre application. **Sites Web Azure** vous permet d'effectuer les opérations suivantes :

-   utiliser de nombreux langages open source reconnus tels que [.NET][.Net], [PHP][], [Node.js][] et [Python][] ;
-   configurer WordPress, Drupal, Umbraco, DNN et de nombreuses autres applications Web tierces ;
-   migrer une application existante ou en créer une à partir de la galerie d'applications.

### Je possède une application métier qui doit se connecter au réseau de l'entreprise

Si vous souhaitez créer une application métier, votre site Web requiert peut-être un accès direct aux services et données figurant sur le réseau de l'entreprise. Ceci est possible avec **Sites Web Azure**, **Azure Cloud Services** et **Azure Virtual Machines**. Il existe des différences en fonction de l'approche choisie, notamment :

-   Sites Web Azure peut se connecter de manière sécurisée aux ressources locales via l'utilisation de Service Bus Relay. Ainsi, les services du réseau de l'entreprise sont en mesure d'effectuer des tâches au nom de l'application Web sans rien déplacer vers le cloud ni configurer de réseau virtuel.
-   Cloud Services et Virtual Machines peuvent tirer parti de Virtual Network.. En effet, Virtual Network permet aux machines exécutées dans Azure de se connecter à un réseau local. Azure devient alors une extension de votre centre de données d'entreprise.

### Je souhaite héberger une API REST ou un service Web pour des clients mobiles

Les services Web HTTP permettent de prendre en charge un large éventail de clients, dont des clients mobiles. Les infrastructures telles que l'API Web ASP.NET s'intègrent à Visual Studio, ce qui facilite la création et l'utilisation des services REST. Ces derniers étant exposés à partir d'un point de terminaison Web, il est possible d'utiliser une des techniques d'hébergement Web dans Azure pour prendre en charge ce scénario. **Sites Web** constitue un choix idéal pour l'hébergement des API REST. Sites Web offre les avantages suivants :

-   créer rapidement un site Web hébergeant le service Web HTTP dans un des centres de données Azure distribués globalement ;
-   migrer des services existants ou en créer, et tirer parti des avantages de l'API Web ASP.NET dans Visual Studio ;
-   se conformer au contrat SLA en termes de disponibilité d'une seule instance ou effectuer une montée en charge vers plusieurs machines dédiées ;
-   utiliser le site publié pour fournir des API REST aux clients HTTP, dont les clients mobiles.

## <a name="services"></a>Synthèse des services

[Sites Web Azure][] vous permet de générer rapidement des sites Web très évolutifs dans Azure. Vous pouvez utiliser le portail Azure ou les outils en ligne de commande pour configurer un site Web avec des langages reconnus, tels que .NET, PHP, Node.js et Python. Les infrastructures prises en charge sont déjà déployées et ne nécessitent pas d'installation supplémentaire. La galerie Sites Web Azure contient de nombreuses applications tierces, telles que Drupal et WordPress, ainsi que des infrastructures de développement comme Django et CakePHP. Une fois le site créé, vous pouvez migrer un site Web existant ou en créer un. L'utilisation de Sites Web Azure évite de devoir gérer le matériel physique et fournit également différentes options de mise à l'échelle. Vous pouvez passer du modèle mutualisé partagé au mode standard où des machines dédiées reçoivent le trafic entrant. Sites Web Azure permet également d'intégrer d'autres services Azure, tels que Base de données SQL, Service Bus et Storage. Le [Kit de développement logiciel (SDK) Azure WebJobs][] (version préliminaire) permet d'ajouter des traitements en arrière-plan. En résumé, Sites Web Azure facilite le développement d'applications en prenant en charge un large éventail de langages, d'applications open source et de méthodes de déploiement (FTP, Git, Web Deploy ou TFS). Si vous n'avez pas besoin des fonctionnalités spécialisées de Cloud Services ou Virtual Machines, Sites Web Azure est le choix le plus adapté.

[Cloud Services][Azure Cloud Services] permet de créer des applications Web extrêmement disponibles et évolutives dans un environnement PaaS (Platform as a Service) enrichi. Contrairement à Sites Web Azure, un service cloud est tout d'abord créé dans un environnement de développement, tel que Visual Studio, avant d'être déployé dans Azure. Les infrastructures telles que PHP requièrent des étapes ou tâches de développement personnalisées qui installent l'infrastructure au démarrage du rôle. L'avantage principal de Cloud Services réside dans sa capacité à prendre en charge des architectures multiniveau plus complexes. Un service cloud spécifique peut être constitué d'un rôle Web frontal et d'un ou plusieurs rôles de travail. Chaque niveau peut être mis à l'échelle indépendamment. Vous bénéficiez également d'un niveau de contrôle accru sur l'infrastructure de votre application Web. Par exemple, vous pouvez accéder via le Bureau à distance aux machines qui exécutent les instances de rôle. Vous pouvez également apporter à l'aide d'un script des modifications avancées à la configuration des machines et d'IIS et qui s'exécutent au démarrage du rôle, notamment des tâches nécessitant un contrôle de la part de l'administrateur.

[Virtual Machines][Azure Virtual Machines] permet d'exécuter les applications Web sur des machines virtuelles dans Azure. Cette fonctionnalité est également connue sous le nom d'IaaS (Infrastructure as a Service). Créez des machines Windows Server ou Linux via le portail ou téléchargez une image de machine virtuelle existante. Virtual Machines offre un contrôle accru sur le système d'exploitation, la configuration et les logiciels et services installés. Il s'agit d'un choix idéal pour la migration rapide d'applications Web locales complexes dans le cloud, car les machines peuvent être déplacées en une seule fois. Grâce à Virtual Network, vous pouvez également connecter ces machines virtuelles aux réseaux d'entreprise locaux. À l'instar de Cloud Services, vous pouvez accéder à distance à ces machines et modifier la configuration au niveau administrateur. Toutefois, contrairement à Sites Web Azure et Cloud Services, vous devez gérer vos images de machines virtuelles et l'architecture de l'application intégralement au niveau de l'infrastructure. C'est le cas notamment si vous appliquez par exemple vos propres correctifs au système d'exploitation.

## <a name="features"></a>Comparaison des fonctionnalités

Le tableau suivant compare les fonctionnalités de Sites Web Azure, Azure Cloud Services et Azure Virtual Machines afin de vous aider dans votre choix. Les zones accompagnées d'un astérisque sont expliquées en détail dans les notes disponibles à la suite du tableau.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tr class="header">
<th align="left">Fonctionnalité</th>
<th align="left">Sites Web</th>
<th align="left">Azure Cloud Services (rôles Web)</th>
<th align="left">Machines virtuelles</th>
</tr>
<tr class="odd">
<td align="left"><p>Accès aux services tels que Service Bus, Storage et Base de données SQL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Hébergement des services Web ou niveaux d'une architecture multiniveau</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Hébergement du niveau intermédiaire d'une architecture multiniveau</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Prise en charge intégrée de MySQL-as-a-service</p></td>
<td align="left">X</td>
<td align="left">X <sup>1</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Prise en charge d'ASP.NET, d'ASP, de Node.js, de PHP et de Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Montée en charge vers plusieurs instances sans redéploiement</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X <sup>2</sup></td>
</tr>
<tr class="odd">
<td align="left"><p>Prise en charge de SSL</p></td>
<td align="left">X <sup>3</sup></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Intégration de Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Débogage à distance</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Déploiement de code avec TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Déploiement de code avec GIT, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Déploiement de code avec Web Deploy</p></td>
<td align="left">X</td>
<td align="left"><sup>4</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Prise en charge de WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Déploiement presque instantané</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Instances partageant le contenu et la configuration</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Évolution vers des machines plus volumineuses sans redéploiement</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Plusieurs environnements de déploiement (de production et intermédiaire)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Isolation du réseau avec Azure Virtual Network</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Prise en charge d'Azure Traffic Manager</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Accès aux serveurs via le Bureau à distance</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Possibilité de définir et d'exécuter des tâches de démarrage</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Gestion automatique des mises à jour du système d'exploitation</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Surveillance intégrée des points de terminaison</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Migration transparente entre les plateformes 32 et 64 bits</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</table>

<sup>1</sup> Les rôles Web ou de travail peuvent intégrer MySQL-as-a-service via les offres ClearDB, mais pas dans le cadre du workflow du portail de gestion.

<sup>2</sup> Bien que les machines virtuelles puissent évoluer vers plusieurs instances, les services exécutés sur celles-ci doivent être écrits de manière à prendre en charge cette montée en charge. Un équilibrage de charge supplémentaire doit être configuré pour acheminer les demandes vers les machines. Pour terminer, un groupe d'affinités doit être créé pour toutes les machines d'un même rôle afin de les protéger contre tout redémarrage simultané suite à des défaillances matérielles ou de maintenance.

<sup>3</sup> Pour Sites Web Azure, le protocole SSL pour les noms de domaine personnalisés est pris en charge uniquement en mode standard. Pour plus d'informations sur l'utilisation de SSL avec Sites Web Azure, consultez la page [Configuration d'un certificat SSL pour un site Web Azure][].

<sup>4</sup> Web Deploy est pris en charge par les services cloud lors d'un déploiement vers des rôles contenant une seule instance. Toutefois, conformément au contrat SLA Azure, les rôles de production requièrent plusieurs instances. Par conséquent, Web Deploy n'est pas un mécanisme de déploiement acceptable pour les services cloud en production.

  [Sites Web Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Azure Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Azure Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
  [Kit de développement logiciel (SDK) Azure WebJobs]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  [Scénarios]: #scenarios
  [Synthèse des services]: #services
  [Comparaison des fonctionnalités]: #features
  [tâches automatisées]: http://www.windowsazure.com/fr-fr/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/fr-fr/develop/net/
  [PHP]: http://www.windowsazure.com/fr-fr/develop/php/
  [Node.js]: http://www.windowsazure.com/fr-fr/develop/nodejs/
  [Python]: http://www.windowsazure.com/fr-fr/develop/python/
  [Base de données SQL]: http://www.windowsazure.com/fr-fr/documentation/services/sql-database/
  [Service Bus]: http://www.windowsazure.com/fr-fr/documentation/services/service-bus/
  [Azure Storage]: http://www.windowsazure.com/fr-fr/documentation/services/storage/
  [Azure Store]: http://www.windowsazure.com/fr-fr/gallery/store/
  [Configuration d'un certificat SSL pour un site Web Azure]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/enable-ssl-web-site/
