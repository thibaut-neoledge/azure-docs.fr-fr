<properties 
	pageTitle="Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines" 
	description="Découvrez quand utiliser les services Sites Web, Cloud Services et Virtual Machines Azure pour héberger des applications web." 
	services="web-sites, virtual-machines, cloud-services" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="tdykstra"/>

# Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines

## Vue d'ensemble

Azure offre plusieurs méthodes pour héberger les sites web : [Sites Web Azure][], [Cloud Services][] et [Virtual Machines][]. Cet article vous aidera à comprendre ces différentes possibilités et à faire le bon choix en fonction de votre application web.

Sites Web Azure est le meilleur choix pour la plupart des applications web. Le déploiement et la gestion sont intégrés à la plateforme, les sites peuvent rapidement gérer des volumes importants de trafic et le gestionnaire d'équilibrage de charge et de trafic assurent une haute disponibilité. Vous pouvez facilement déplacer des sites existants vers Sites Web Azure avec un [outil de migration en ligne](https://www.migratetoazure.net/), utiliser une application open source de la galerie d'applications web ou bien créer un site avec l'infrastructure et les outils de votre choix. La fonctionnalité [WebJobs][] simplifie l'ajout du traitement de tâches en arrière-plan à votre application. 

Si vous avez besoin d'un plus grand niveau de contrôle sur l'environnement de serveur web, par exemple pour pouvoir vous connecter à distance au serveur ou pour configurer les tâches de démarrage du serveur, Azure Cloud Services est normalement la meilleure option.

Si vous avez une application qui nécessite des modifications importantes pour pouvoir fonctionner dans Sites Web Azure ou Azure Cloud Services, vous pouvez choisir Azure Virtual Machines pour simplifier la migration vers le cloud. Cependant, la configuration, la sécurisation et la gestion des machines virtuelles nécessitent bien plus de temps et de compétences que l'utilisation de Sites Web Azure et Azure Cloud Services. Si vous envisagez d'utiliser Azure Virtual Machines, tenez bien compte du temps de maintenance nécessaire pour mettre à jour, mettre à niveau et gérer votre environnement de machine virtuelle.  

Le schéma suivant illustre le degré de contrôle relatif par rapport à la simplicité d'utilisation de chacune de ces options d'hébergement web sur Azure. 

![ChoicesDiagram][ChoicesDiagram]

## <a name="scenarios"></a>Scénarios et recommandations

Voici quelques scénarios d'application courants avec des recommandations sur l'option d'hébergement web Azure la plus appropriée.

- [J'ai besoin d'un site Web frontal avec traitement en arrière-plan et base de données principale pour exécuter des applications métier intégrées à des ressources locales.](#onprem)
- [J'ai besoin d'un hébergement pour le site Web de ma société, qui soit fiable, extensible et de portée mondiale.](#corp)
- [J'ai une application IIS6 qui s'exécute sur Windows Server 2003.](#iis6)
- [Je dirige une PME et je recherche une méthode peu onéreuse pour héberger mon site Web, tout en gardant à l'esprit la future croissance de mon entreprise.](#smallbusiness)
- [Je suis concepteur web/graphiste et je souhaite concevoir et développer des sites Web pour mes clients.](#designer)
- [Je migre actuellement mon application multiniveau et un serveur Web frontal dans le cloud.](#multitier)
- [Mon application dépend d'environnements Windows ou Linux hautement personnalisés et je souhaite la transférer dans le cloud.](#custom)
- [Mon site utilise des logiciels open source et je souhaite l'héberger dans Azure.](#oss)
- [Je possède une application métier qui doit se connecter au réseau de l'entreprise.](#lob)
- [Je souhaite héberger une API REST ou un service web pour des clients mobiles.](#mobile)


### <a id="onprem"></a> J'ai besoin d'un site Web frontal avec traitement en arrière-plan et base de données principale pour exécuter des applications métier intégrées à des ressources locales.

Sites Web Azure est une excellente solution pour l'hébergement d'applications métier complexes. Elle vous permet de développer des applications automatiquement mises à l'échelle sur les plateformes avec équilibrage de la charge. Elles sont aussi sécurisées grâce à Active Directory et se connectent à vos ressources locales. Elle simplifie la gestion de ces applications grâce au portail de gestion et aux API et vous permet de savoir comment vos clients les utilisent avec les outils d'analyse. La nouvelle fonctionnalité [Webjobs][] vous permet d'exécuter des processus et des tâches en arrière-plan dans le cadre de votre niveau de service. La connectivité hybride et les [fonctionnalités VNET](../fundamentals-introduction-to-azure/#networking/) simplifient la connexion aux ressources locales. Sites Web Azure offre des contrats SLA à 0,001 % d'erreur et offre les possibilités suivantes :

* Exécution fiable de vos applications sur une plateforme cloud avec corrections et mises à jour automatiques. 
* Extension automatique sur un réseau mondial de centres de données.
* Sauvegarde et restauration après sinistre. 
* Conformité ISO, SOC2 et PCI.
* Intégration avec Active Directory

### <a id="corp"></a>J'ai besoin d'un hébergement pour le site Web de ma société, qui soit fiable, extensible et de portée mondiale.

Sites Web Azure est une excellente solution pour l'hébergement de sites web d'entreprises. Il permet une mise à l'échelle rapide et simple pour faire face à la demande, grâce au réseau international de centres de données. Il offre un lien local, une grande tolérance aux pannes et une gestion intelligente du trafic. Le tout sur une plateforme avec des outils de pointe, qui offrent un accès facile et rapide aux informations de l'état du site et aux données de trafic. Sites Web Azure offre des contrats SLA à 0,001 % d'erreur et offre les possibilités suivantes :

* Exécution fiable de vos sites web sur une plateforme cloud avec corrections et mises à jour automatiques. 
* Extension automatique sur un réseau mondial de centres de données.
* Sauvegarde et restauration après sinistre. 
* Gestion des journaux et du trafic avec des outils intégrés. 
* Conformité ISO, SOC2 et PCI.
* Intégration avec Active Directory

### <a id="iis6"></a>J'ai une application IIS6 qui s'exécute sur Windows Server 2003.

Sites Web Azure permet d'éviter facilement les coûts d'infrastructure liés à la migration d'anciennes applications IIS6. Microsoft a créé des [outils de migration simples et des informations de migration détaillées](https://www.movemetowebsites.net/) qui vous permettent de vérifier la compatibilité et d'identifier les modifications nécessaires. L'intégration à Visual Studio, TFS et autres outils de CMS simplifie le déploiement d'applications IIS6 directement dans le cloud. Une fois déployé, le portail de gestion Azure offre l'accès à des outils de gestion robustes, afin de réduire vos coûts et de faire face à la demande. Avec l'outil de migration, vous pouvez :

* Migrer rapidement et facilement vos applications web Windows Server 2003 sur le cloud.
* Choisir de laisser votre base de données SQL attachée localement pour créer une application hybride. 
* Déplacer automatiquement votre base de données SQL avec votre application héritée. 

### <a id="smallbusiness"></a>Je dirige une PME et je recherche une méthode peu onéreuse pour héberger mon site Web, tout en gardant à l'esprit la future croissance de mon entreprise.

Sites Web Azure est une solution idéale dans ce scénario, car vous pouvez démarrer gratuitement, puis ajouter des fonctionnalités supplémentaires en fonction de vos besoins. Chaque site web est fourni avec un domaine Azure (*votre_société*.azurewebsites.net). La plateforme comprend des outils de déploiement et de gestion intégrés, ainsi qu'une galerie d'applications pour une mise en route rapide. De nombreux autres services et options de mise à l'échelle permettent de faire évoluer le site en fonction de l'augmentation de la demande utilisateur. Sites Web Azure offre les avantages suivants :

- Commencez gratuitement, puis passez au niveau supérieur en fonction de vos besoins.
- Utilisez la galerie d'applications pour configurer rapidement des applications web reconnues, telles que WordPress.
- Ajoutez des services et fonctionnalités Azure à votre application à votre convenance.
- Sécurisez votre site web avec HTTPS.

### <a id="designer"></a>Je suis un concepteur Web/graphiste et je souhaite concevoir et développer des sites Web pour mes clients

Pour les développeurs web et les graphistes, Sites Web Azure s'intègre facilement à de nombreuses infrastructures et à de nombreux outils. Il comprend la prise en charge du déploiement avec Git et FTP et offre une excellente intégration à des outils et services tels que Visual Studio et Base de données SQL. Sites Web offre les avantages suivants :

- utiliser les outils en ligne de commande pour les [tâches automatisées][scripting].
- utiliser des langages reconnus tels que [.Net][dotnet], [PHP][], [Node.js][nodejs] et [Python][].
- sélectionner trois niveaux de mise à l'échelle pour bénéficier de capacités supérieures ;
- intégrer d'autres services Azure, tels que [Base de données SQL][sqldatabase], [Service Bus][servicebus] et le [Stockage][] ou d'autres offres partenaire disponibles dans [Azure Store][azurestore], comme MySQL et MongoDB ;
- intégrer des outils tels que Visual Studio, Git, WebMatrix, WebDeploy, TFS et FTP.

### <a id="multitier"></a>Je migre actuellement mon application multiniveau et un serveur Web frontal dans le cloud

Si vous exécutez une application multiniveau, par exemple un serveur web qui se connecte à une base de données, Sites Web, Azure est une bonne option, qui permet une intégration à la Base de données SQL Azure. Et vous pouvez utiliser la fonctionnalité WebJobs pour exécuter des processus principaux.

Choisissez un service cloud pour une ou plusieurs couches si vous avez besoin de contrôler l'environnement de serveur de façon plus précise, par exemple pour pouvoir vous connecter à distance au serveur ou pour configurer les tâches de démarrage du serveur.

Choisissez Virtual Machines pour une ou plusieurs couches si vous voulez utiliser votre propre image ou exécuter des logiciels ou des services que vous ne pouvez pas configurer sur Cloud Services. 

### <a id="custom"></a>Mon application dépend d'environnements Windows ou Linux hautement personnalisés et je souhaite la transférer dans le cloud.

Si votre application nécessite l'installation ou la configuration complexe des logiciels et du système d'exploitation, Virtual Machines représente probablement la meilleure solution. Virtual Machines vous permet d'effectuer les opérations suivantes :

- utiliser la galerie de machines virtuelles pour commencer avec un système d'exploitation tel que Windows ou Linux, puis personnaliser ce dernier en fonction des besoins de votre application ; 
- créer et télécharger une image personnalisée d'un serveur local existant à exécuter sur une machine virtuelle dans Azure. 

### <a id="oss"></a>Mon site utilise des logiciels open source et je souhaite l'héberger dans Azure

Si votre infrastructure open source est prise en charge par Sites Web Azure, les langages et infrastructures nécessaires à votre application sont automatiquement configurés pour vous. Sites Web Azure vous permet d'effectuer les opérations suivantes :

- utiliser des langages open source reconnus tels que [.NET][dotnet], [PHP][], [Node.js][nodejs] et [Python][]. 
- configurer WordPress, Drupal, Umbraco, DNN et de nombreuses autres applications web tierces. 
- migrer une application existante ou en créer une à partir de la galerie d'applications. 

Si votre infrastructure open source n'est pas prise en charge par Sites Web Azure, vous pouvez l'exécuter sur une des deux autres options d'hébergement web Azure. Avec Cloud Services, vous devez faire appel à des tâches de démarrage pour installer et configurer les logiciels open source requis exécutés sur Windows. Virtual Machines vous permet d'installer et de configurer les logiciels sur l'image de machine virtuelle, qui peut être basée sur Windows ou Linux. 

### <a id="lob"></a>Je possède une application métier qui doit se connecter au réseau de l'entreprise

Si vous souhaitez créer une application métier, votre site Web requiert peut-être un accès direct aux services et données figurant sur le réseau de l'entreprise. C'est possible sur Sites Web, Cloud Services et Virtual Machines grâce au [service Azure Virtual Network](/services/virtual-network/). Sur Sites Web, vous pouvez utiliser la nouvelle [fonction d'intégration VNET](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), qui permet à vos applications Azure de s'exécuter comme si elles se trouvaient sur votre réseau d'entreprise.

### <a id="mobile"></a>Je souhaite héberger une API REST ou un service Web pour des clients mobiles

Les services web HTTP permettent de prendre en charge un large éventail de clients, dont des clients mobiles. Les infrastructures telles que l'API web ASP.NET s'intègrent à Visual Studio, ce qui facilite la création et l'utilisation des services REST.  Ces derniers étant exposés à partir d'un point de terminaison Web, il est possible d'utiliser une des techniques d'hébergement Web dans Azure pour prendre en charge ce scénario. Sites Web constitue un choix idéal pour l'hébergement des API REST. Sites Web offre les avantages suivants :

- créer rapidement un site web hébergeant le service web HTTP dans un des centres de données Azure distribués globalement ;
- migrer des services existants ou en créer de nouveaux.
- se conformer au contrat SLA en termes de disponibilité d'une seule instance ou effectuer une montée en charge vers plusieurs machines dédiées ; 
- utiliser le site publié pour fournir des API REST aux clients HTTP, dont les clients mobiles.

## <a name="features"></a>Comparaison des fonctionnalités

Le tableau suivant compare les fonctionnalités de Sites Web Azure, Azure Cloud Services et Azure Virtual Machines afin de vous aider dans votre choix. Pour obtenir des informations à jour sur le contrat SLA de chaque option, consultez la page des [contrats de niveau de service Azure](/support/legal/sla/).

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Fonctionnalité</th>
   <th align="left" valign="middle">Sites Web</th>
   <th align="left" valign="middle">Azure Cloud Services (rôles Web)</th>
   <th align="left" valign="middle">Machines virtuelles</th>
   <th align="left" valign="middle">Remarques</th>
</tr>
<tr>
   <td valign="middle"><p>Déploiement presque instantané</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">Le déploiement d'une application ou de la mise à jour d'une application sur un service cloud, ou la création d'une machine virtuelle prend au moins quelques minutes. Le déploiement d'une application sur un site web se fait en quelques secondes.</td>
</tr>
<tr>
   <td valign="middle"><p>Évolution vers des machines plus volumineuses sans redéploiement</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Les instances de serveur web partagent le contenu et la configuration, ce qui fait que vous n'avez pas à refaire le déploiement ou la configuration en cas d'extension.</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Plusieurs environnements de déploiement (de production et intermédiaire)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Gestion automatique des mises à jour du système d'exploitation</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Migration transparente entre les plateformes (de 32 à 64 bits)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Déploiement de code avec GIT, FTP</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Déploiement de code avec Web Deploy</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services prend en charge l'utilisation de Web Deploy pour déployer des mises à jour sur des instances de rôle. Cependant, vous ne pouvez pas l'utiliser pour le déploiement initial du rôle, et si vous utilisez Web Deploy pour une mise à jour, vous devez effectuer le déploiement séparément pour chaque instance du rôle. Plusieurs instances sont nécessaires afin de répondre aux exigences du contrat SLA Cloud Services pour les environnements de production.</td>
</tr>
<tr>
   <td valign="middle"><p>Prise en charge de WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Accès aux services tels que Service Bus, Storage et Base de données SQL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Hébergement des services Web ou niveaux d'une architecture multiniveau</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Hébergement du niveau intermédiaire d'une architecture multiniveau</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Les sites Web peuvent facilement héberger un niveau intermédiaire d'API REST, et la fonctionnalité <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> de Sites Web peut héberger des tâches de traitement en arrière-plan. Vous pouvez exécuter WebJobs sur un site web dédié pour obtenir une flexibilité indépendante pour cette couche.</td>
</tr>
<tr>
   <td valign="middle"><p>Prise en charge intégrée de MySQL-as-a-service</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services peut intégrer MySQL en tant que service via les offres ClearDB, mais pas dans le cadre du workflow du portail de gestion.</td>
</tr>
<tr>
   <td valign="middle"><p>Prise en charge d'ASP.NET, d'ASP, de Node.js, de PHP et de Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Montée en charge vers plusieurs instances sans redéploiement</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Bien que les machines virtuelles puissent évoluer vers plusieurs instances, les services exécutés sur celles-ci doivent être écrits de manière à prendre en charge cette montée en charge. Vous devez configurer un programme d'équilibrage de la charge pour acheminer les demandes entre les machines et créer un groupe d'affinités afin d'empêcher les redémarrages simultanés de toutes les instances en cas de maintenance ou de panne matérielle.</td>
</tr>
<tr>
   <td valign="middle"><p>Prise en charge de SSL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Pour Sites Web Azure, le protocole SSL pour les noms de domaine personnalisés est pris en charge uniquement en mode Basic et Standard. Pour plus d'informations sur l'utilisation de SSL avec Sites Web Azure, consultez la page <a href="../web-sites-configure-ssl-certificate/">Configuration d'un certificat SSL pour un site Web Azure</a>.</td>
</tr>
<tr>
   <td valign="middle"><p>Intégration de Visual Studio</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Débogage à distance</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Déploiement de code avec TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Isolation du réseau avec <a href="/services/virtual-network/">Azure Virtual Network</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Voir aussi <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Intégration au réseau virtuel de Sites Web Azure</a></td>
</tr>
<tr>
   <td valign="middle"><p>Prise en charge d'<a href="/services/traffic-manager/">Azure Traffic Manager</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Surveillance intégrée des points de terminaison</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Accès aux serveurs via le Bureau à distance</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Installez n'importe quel MSI personnalisé</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Possibilité de définir et d'exécuter des tâches de démarrage</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Possibilité d'écouter des événements ETW</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>


> [AZURE.NOTE]
> Si vous voulez prendre en main Azure Web Sites avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.


## <a id="nextsteps"></a> Étapes suivantes

Pour plus d'informations sur les trois options d'hébergement web, consultez les ressources suivantes :

* [Présentation d'Azure](fundamentals-introduction-to-azure.md)
* [Calcul des options d'hébergement fournies par Azure](fundamentals-application-models.md)

Pour choisir une ou plusieurs de ces options pour votre application, consultez les ressources suivantes :

* [Sites Web Azure](/documentation/services/websites/)
* [Azure Cloud Services](/documentation/services/cloud-services/)
* [Azure Virtual Machines](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Sites Web Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Configuration d'un certificat SSL pour un site web Azure]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [Stockage]: http://www.windowsazure.com/documentation/services/storage/

<!--HONumber=47-->
