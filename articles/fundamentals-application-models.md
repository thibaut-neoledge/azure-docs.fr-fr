<properties umbracoNaviHide="0" pageTitle="Mod&egrave;le d'application" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="D&eacute;couvrez le mod&egrave;le d'application de service h&eacute;berg&eacute; Azure. D&eacute;couvrez les concepts cl&eacute;s, les consid&eacute;rations en mati&egrave;re de conception, la d&eacute;finition et la configuration de votre application, et la mise &agrave; l'&eacute;chelle." urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Mod&egrave;le d'application" authors="robb" manager="johndaw" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb" />

# Modèles d'exécution Azure

Azure propose différents modèles d'exécution d'applications. Chaque modèle fournit un ensemble de services différent. Votre choix dépend de ce que vous essayez de faire. Cet article passe en revue trois modèles, avec une description de la technologie et des exemples d'utilisation.

## Sommaire

-   [Machines virtuelles][Machines virtuelles]
-   [Sites Web][Sites Web]
-   [Services cloud][Services cloud]
-   [Quel modèle dois-je utiliser ? Faire un choix][Quel modèle dois-je utiliser ? Faire un choix]

## <span id="VMachine"></span></a>Machines virtuelles

Azure Virtual Machines permet aux développeurs et aux personnes chargées des opérations informatiques, entre autres, de créer et d'utiliser des machines virtuelles dans le cloud. Cette technologie, qui fournit l'infrastructure en tant que service, communément appelée *Iaas (Infrastructure as a Service)*, peut être employée de différentes manières. La [figure 1][figure 1] présente les éléments de base qui la composent.

<a name="Fig1"></a>![01\_CreatingVMs][01\_CreatingVMs]

**Figure 1 : Azure Virtual Machines fournit une infrastructure IaaS.**

Comme le montre la figure, vous pouvez créer des machines virtuelles soit avec le portail de gestion Azure soit avec l'API Azure Service Management basée sur REST. Le portail de gestion est accessible depuis n'importe quel navigateur populaire, notamment Internet Explorer, Mozilla et Chrome. Pour l'API REST, Microsoft propose des outils de création de scripts client pour les systèmes Windows, Linux et Macintosh. D'autres logiciels sont également libres d'utiliser cette interface.

Quelle que soit la façon dont vous accédez à la plateforme, pour créer une machine virtuelle, vous devez choisir un disque dur virtuel (VHD) pour l'image de la machine virtuelle. Ces disques durs virtuels sont stockés dans des objets blob Azure.

Pour commencer, deux possibilités s'offrent à vous :

1.  Téléchargez votre propre disque dur virtuel.
2.  Utilisez les disques durs virtuels fournis par Microsoft et ses partenaires dans la galerie des machines virtuelles Azure ou sur le site Web open source de Microsoft [VMDepot][VMDepot].

Les disques durs virtuels dans la galerie et sur VMDepot incluent des images propres des systèmes d'exploitation Microsoft et Linux, ainsi que des images incluant les produits Microsoft et ceux d'autres fournisseurs qui y sont installés. Les options, toujours plus nombreuses, incluent par exemple diverses versions, éditions et configurations des produits suivants :

-   Windows Server
-   Serveurs Linux, tels que Suse, Ubuntu et CentOS
-   SQL Server
-   BizTalk Server
-   SharePoint Server

Outre un disque dur virtuel, vous spécifiez la taille de la nouvelle machine virtuelle. Les statistiques complètes pour chaque taille sont répertoriées dans la [bibliothèque Azure][bibliothèque Azure].

-   **Très petite**, avec un cœur partagé et 768 Mo de mémoire.
-   **Petite**, avec 1 cœur et 1,75 Go de mémoire.
-   **Moyenne**, avec 2 cœurs et 3,5 Go de mémoire.
-   **Grande**, avec 4 cœurs et 7 Go de mémoire.
-   **Très grande**, avec 8 cœurs et 14 Go de mémoire.
-   **A6**, avec 4 cœurs et 28 Go de mémoire.
-   **A7**, avec 8 cœurs et 56 Go de mémoire.

Enfin, vous choisissez le centre de données Azure, aux États-Unis, en Europe ou en Asie, dans lequel votre nouvelle machine virtuelle doit s'exécuter.

Lorsqu'une machine virtuelle est en cours d'exécution, vous payez à l'heure d'utilisation et vous cessez de payer lorsque vous supprimez cette machine virtuelle. Le tarif ne varie pas selon que vous utilisez peu ou beaucoup la machine virtuelle : il est exclusivement fondé sur le temps. Une machine virtuelle qui reste inactive pendant une heure coûte autant qu'une machine lourdement chargée.

À chaque machine virtuelle en cours d'exécution est associé un *disque de système d'exploitation*, stocké dans un objet blob. Lorsque vous créez une machine virtuelle en utilisant un disque dur virtuel de la galerie, ce dernier est copié sur le disque de système d'exploitation de la machine virtuelle. Toutes les modifications que vous apportez au système d'exploitation de la machine virtuelle en cours d'exécution sont stockées à cet emplacement. Par exemple, si vous installez une application qui modifie le Registre Windows, cette modification sera stockée sur le disque de système d'exploitation de la machine virtuelle. La prochaine fois que vous créerez une machine virtuelle à partir de ce disque, elle continuera de s'exécuter dans l'état où vous l'avez laissée. Pour les disques durs virtuels stockés dans la galerie, Microsoft applique des mises à jour si besoin, notamment des correctifs du système d'exploitation. Dans le cas des disques durs virtuels situés sur vos propres disques de système d'exploitation, il vous appartient cependant d'appliquer ces mises à jour (bien que Windows Update soit activé par défaut).

Les machines virtuelles en cours d'exécution peuvent également être modifiées, puis capturées avec l'outil sysprep. Sysprep supprime les caractéristiques telles que le nom de la machine afin de rendre possible l'utilisation d'une image de disque dur virtuel pour créer d'autres machines virtuelles avec la même configuration générale. Ces images sont stockées dans le portail de gestion avec vos images téléchargées de manière à pouvoir servir de point de départ pour d'autres machines virtuelles.

Les machines virtuelles surveillent également le matériel qui héberge chaque machine virtuelle que vous créez. Si un serveur physique exécutant une machine virtuelle tombe en panne, la plateforme s'en aperçoit et démarre la même machine virtuelle sur un autre ordinateur. Et à supposer que vous disposiez de la licence appropriée, vous pouvez copier un disque dur virtuel modifié hors de votre disque de système d'exploitation, puis l'exécuter à un autre emplacement, par exemple, dans votre centre de données local ou chez un autre fournisseur de services cloud.

Outre son disque de système d'exploitation, la machine virtuelle possède un ou plusieurs disques de données. Bien que ces derniers aient chacun l'aspect d'un lecteur de disque monté pour votre machine virtuelle, leur contenu est en fait stocké dans un objet blob. Toute écriture sur un disque de données est enregistrée de façon permanente dans l'objet blob sous-jacent. Azure réplique ces objets, comme il le fait pour tous les objets blob, à la fois dans un centre de données unique et entre plusieurs centres de données pour assurer leur protection contre les défaillances.

L'exécution des machines virtuelles peut être gérée à l'aide du portail de gestion, de PowerShell et d'autres outils de création de scripts, ou directement par l'API REST. (En fait, tout ce que vous pouvez faire par l'intermédiaire du portail de gestion peut être réalisé par programme avec cette API.) Les partenaires de Microsoft tels que RightScale et ScaleXtreme fournissent eux aussi des services de gestion qui s'appuient sur l'API REST.

Azure Virtual Machines peut être utilisé de différentes manières. Les principaux scénarios ciblés par Microsoft sont les suivants :

-   **Machines virtuelles pour le développement et les tests.** Les groupes de développement ont souvent besoin de machines virtuelles avec des configurations particulières pour créer des applications. Azure Virtual Machines fournit un moyen simple et économique pour créer ces machines virtuelles, les utiliser, puis les supprimer lorsqu'elles ne servent plus.
-   **Exécution d'applications dans le cloud.** Pour certaines applications, l'exécution sur le cloud public peut s'avérer intéressante sur le plan économique. Cela concernera, par exemple, une application qui se caractérise par des pics de demande importants. Il est toujours possible d'acheter un nombre suffisant de machines pour exécuter cette application dans votre propre centre de données, mais la plupart de ces machines risquent de rester inutilisées la plupart du temps. L'exécution de cette application sur Azure vous permet de payer pour les machines virtuelles supplémentaires uniquement lorsque vous en avez besoin, en les arrêtant lorsque le pic de demande est dépassé. Ou prenons une start-up qui a besoin de ressources informatiques à la demande rapidement et sans engagement : dans ce cas également, Azure peut être le bon choix.
-   **Extension de votre centre de données dans le cloud public.** Avec Azure Virtual Network, votre organisation peut, en créant un réseau virtuel (VNET), faire en sorte qu'un groupe de machines virtuelles Azure apparaisse comme faisant partie de votre réseau local. Cela permet d'exécuter sur Azure des applications telles que SharePoint, entre autres. Cette approche peut s'avérer plus facile à déployer et/ou moins coûteuse que leur exécution dans votre propre centre de données.
-   **Récupération d'urgence.** Plutôt que de payer continuellement pour un centre de données de sauvegarde rarement utilisé, la récupération d'urgence basée sur IaaS vous permet de payer pour les ressources informatiques dont vous avez besoin, uniquement lorsque vous en avez besoin. Par exemple, si votre centre de données principal tombe en panne, vous pouvez créer des machines virtuelles qui s'exécutent sur Azure pour les applications essentielles, puis les arrêter lorsque vous n'en avez plus besoin.

Ces possibilités ne sont pas exhaustives, mais elles illustrent bien la façon dont vous pouvez utiliser Azure Virtual Machines.

### Regroupement de machines virtuelles : Services cloud

Lorsque vous créez une machine virtuelle avec Azure Virtual Machines, vous pouvez choisir de l'exécuter de manière autonome ou de l'intégrer à un groupe de machines virtuelles qui s'exécutent ensemble dans un *service cloud*. (Malgré des noms similaires, ne confondez pas ce concept avec Cloud Services, le nom de la technologie PaaS d'Azure ; il s'agit de deux choses différentes.) Chaque machine virtuelle autonome se voit attribuer sa propre adresse IP publique, tandis que toutes les machines virtuelles dans le même service cloud sont accessibles par le biais d'une seule adresse IP publique. [La figure 2][La figure 2] illustre ce concept.

<a name="Fig2"></a>![02\_CloudServices][02\_CloudServices]

**Figure 2 : Chaque machine virtuelle autonome possède sa propre adresse IP publique, tandis que les machines virtuelles dans un service cloud sont exposées via une seule adresse IP publique.**

Par exemple, si vous créez une machine virtuelle pour créer et tester une simple application, vous utiliserez probablement une machine virtuelle autonome. En revanche, si vous créez une application multiniveau avec un site Web frontal, une base de données et éventuellement un niveau intermédiaire, vous connecterez plus probablement plusieurs machines virtuelles dans un service cloud, comme le suggère la figure 2.

Le regroupement de plusieurs machines virtuelles dans un service cloud vous permet également d'utiliser d'autres options. Azure fournit l'équilibrage de la charge pour les machines virtuelles appartenant à un même service cloud, en répartissant entre elles les requêtes utilisateur. Les machines virtuelles ainsi connectées peuvent également communiquer directement les unes avec les autres sur le réseau local d'un centre de données Azure.

Les machines virtuelles appartenant à un même service cloud peuvent aussi être regroupées dans un ou plusieurs *groupes à haute disponibilité*. Pour comprendre l'importance de cette fonctionnalité, songez à une application Web qui exécute plusieurs machines virtuelles frontales. Si ces dernières sont toutes déployées sur le même ordinateur physique ou dans le même rack de machines, une seule défaillance matérielle peut les rendre toutes inaccessibles. Mais si ces machines virtuelles sont regroupées dans un groupe à haute disponibilité, Azure les déploiera à travers le centre de données de telle sorte qu'elles ne partagent aucun point de défaillance.

### Scénario : exécution d'une application avec SQL Server

Pour avoir une meilleure idée du fonctionnement d'Azure Virtual Machines, il peut être utile d'examiner plus en détail deux scénarios. Supposons, par exemple, que vous souhaitez créer une application Web fiable et évolutive qui s'exécute sur Azure. Une façon de procéder consiste à exécuter la logique de l'application dans une ou plusieurs machines virtuelles Azure, puis à utiliser SQL Server pour la gestion des données. [La figure 3][La figure 3] illustre ce concept.

<a name="Fig3"></a>![03\_AppUsingSQLServer][03\_AppUsingSQLServer]

**Figure 3 : une application s'exécutant dans Azure Virtual Machines peut utiliser SQL Server pour le stockage.**

Dans cet exemple, les deux types de machines virtuelles sont créés à partir des disques durs virtuels standard de la galerie. La logique de l'application s'exécute sur Windows Server 2008 R2. Le développeur crée donc trois machines virtuelles à partir de ce disque dur virtuel, puis installe son application dans chacune d'elles. Dans la mesure où ces machines virtuelles se trouvent toutes dans le même service cloud, il peut configurer l'équilibrage de la charge matérielle de manière à répartir les requêtes entre elles. Le développeur crée également deux machines virtuelles à partir du disque dur virtuel de la galerie contenant SQL Server 2012. Une fois qu'elles sont en cours d'exécution, il configure SQL Server dans chaque instance pour utiliser la mise en miroir des bases de données avec le basculement automatique. Bien que cela ne soit pas obligatoire (l'application pourrait en effet n'utiliser qu'une seule instance SQL Server), cette approche renforce la fiabilité.

### Scénario : exécution d'une batterie de serveurs SharePoint

Supposons qu'une organisation souhaite créer une batterie de serveurs SharePoint, mais ne souhaite pas l'exécuter dans son propre centre de données. Il se peut que le centre de données local soit à court de ressources ou que l'unité commerciale qui a créé la batterie de serveurs ne souhaite pas faire appel à son service informatique interne. Dans de telles situations, il peut être intéressant d'exécuter SharePoint sur Azure Virtual Machines. [La figure 4][La figure 4] illustre ce concept.

<a name="Fig4"></a>![04\_SharePointFarm][04\_SharePointFarm]

**Figure 4 : Azure Virtual Machines permet d'exécuter une batterie de serveurs SharePoint dans le cloud.**

Une batterie de serveurs SharePoint a plusieurs composants, chacun étant exécuté dans une machine virtuelle Azure créée à partir d'un disque dur virtuel différent. Les éléments requis sont les suivants :

-   Microsoft SharePoint. Il existe des images d'évaluation dans la galerie ou l'organisation fournit son propre disque dur virtuel.
-   Microsoft SQL Server. SharePoint dépend de SQL Server. L'organisation crée par conséquent des machines virtuelles exécutant SQL Server 2012 à partir d'un disque dur virtuel standard de la galerie.
-   Windows Server Active Directory. SharePoint nécessite également Active Directory. L'organisation crée par conséquent des contrôleurs de domaine dans le cloud en utilisant une image Windows Server de la galerie. Cela n'est pas strictement nécessaire (il est également possible d'utiliser des contrôleurs de domaine locaux), mais SharePoint interagit fréquemment avec Active Directory et l'approche décrite ici permet d'obtenir de meilleures performances.

La figure ne l'indique pas, mais cette batterie de serveurs SharePoint est probablement connectée à un réseau local avec Azure Virtual Network. Les machines virtuelles, ainsi que les applications qu'elles contiennent, apparaissent ainsi comme locales pour les personnes qui utilisent ce réseau.

Comme le montrent ces exemples, vous pouvez utiliser Azure Virtual Machines pour créer et exécuter de nouvelles applications dans le cloud, pour exécuter des applications existantes ou pour d'autres usages. Quelle que soit l'option que vous choisissez, l'objectif de la technologie est le même : fournir une base générale pour le cloud computing public.

## <span id="WebSites"></span></a>Sites Web

Il existe de nombreuses façons d'utiliser les technologies Web. Une entreprise peut avoir besoin de faire migrer ou de configurer un site de présence capable de gérer des millions d'accès par semaine et pouvant être déployé dans plusieurs centres de données répartis dans le monde entier. Une agence de conception Web peut travailler avec une équipe de développeurs pour créer une application Web personnalisée capable de gérer des milliers d'utilisateurs. Un développeur peut avoir besoin de configurer une application permettant d'effectuer le suivi des rapports de dépenses dans le cloud pour les utilisateurs authentifiés de l'annuaire Active Directory de son entreprise. Un consultant en informatique peut utiliser une application open source populaire pour configurer un système de gestion de contenu destiné à une petite entreprise.
Azure Virtual Machines permet d'effectuer toutes ces opérations. Mais la création et la gestion de machines virtuelles brutes exigent certaines compétences et requièrent des efforts. Si vous avez besoin de mettre en œuvre un site Web ou une application Web, il existe une solution plus simple (et moins coûteuse) : l'approche communément appelée Paas (Platform as a Service). Comme le montre la figure 5, Azure fournit PaaS avec les sites Web.

<a name="Fig5"></a>![05\_Websites][05\_Websites]

**Figure 5 : les sites Web Azure prennent en charge les sites Web statiques, les applications Web populaires et les applications Web personnalisées créées avec diverses technologies.**

Les sites Web Azure s'appuient sur Azure Cloud Services afin de créer une solution PaaS optimisée pour l'exécution des applications Web. Comme le montre la figure, les sites Web s'exécutent sur un ensemble de machines virtuelles, qui peut contenir plusieurs sites Web créés par plusieurs utilisateurs, ainsi que des machines virtuelles standard appartenant à un utilisateur individuel. Les machines virtuelles appartiennent à une réserve de ressources gérées par les sites web Azure et permettent ainsi d'obtenir un niveau élevé de fiabilité et de tolérance aux pannes.
La mise en route est aisée. Avec les sites Web Azure, les utilisateurs peuvent faire leur choix dans un large éventail d'applications, d'infrastructures et de modèles, et créer un site Web en quelques secondes. Ils peuvent ensuite utiliser leurs outils de développement préférés (WebMatrix, Visual Studio ou un autre éditeur) et les options de contrôle du code source pour configurer l'intégration continue et développer comme une équipe. Les applications qui s'appuient sur une base de données MySQL peuvent utiliser un service MySQL fourni pour Azure par ClearDB, un partenaire de Microsoft.
Les développeurs peuvent créer de volumineuses applications web évolutives avec les sites web. La technologie prend en charge la création d'applications avec ASP.NET, PHP, Node.js et Python. Les applications peuvent utiliser des sessions rémanentes, par exemple, et les applications Web existantes peuvent être transférées vers cette plateforme cloud sans modification. Les applications basées sur les sites Web peuvent utiliser librement d'autres aspects d'Azure, notamment Service Bus, la base de données SQL et le stockage d'objets blob. Vous pouvez également exécuter plusieurs copies d'une application dans différentes machines virtuelles, et les sites Web équilibreront automatiquement la charge des requêtes entre ces dernières. Et dans la mesure où de nouvelles instances des sites web sont créées dans les machines virtuelles qui existent déjà, le démarrage d'une nouvelle instance d'application a lieu très rapidement, nettement plus vite que si vous attendez la création d'une machine virtuelle.
Comme indiqué dans la [Figure 5][Figure 5], vous pouvez publier du code et d'autres contenus web dans les sites web de différentes manières. Vous pouvez utiliser FTP, FTPS ou la technologie WebDeploy de Microsoft. Les sites Web prennent également en charge la publication de code provenant des systèmes de contrôle de code source, y compris Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server et Team Foundation Service basé dans le cloud.

## <span id="CloudServices"></span></a>Cloud Services

Azure Virtual Machines fournit une infrastructure IaaS, tandis que les sites Web Azure fournissent un hébergement Web. La troisième option, Cloud Services, fournit la plateforme *PaaS (Platform as a Service)*. Cette technologie est conçue pour prendre en charge des applications évolutives, fiables et dont l'exploitation est peu coûteuse. Elle vise également à libérer les développeurs des soucis liés à la gestion de la plateforme qu'ils utilisent, pour leur permettre de se consacrer entièrement à leurs applications. La [figure 6][figure 6] illustre cette idée.

<a name="Fig6"></a>![06\_CloudServices2][06\_CloudServices2]

**Figure 6 : Azure Cloud Services fournit une plateforme PaaS (Platform as a Service).**

À l'instar des autres options de calcul Azure, Azure Cloud Services s'appuie sur des machines virtuelles. La technologie propose deux options de machine virtuelle légèrement différentes : les instances de *rôles Web* exécutent une variante de Windows Server avec IIS, tandis que les instances de *rôles de travail* exécutent la même variante de Windows Server sans IIS. Une application Cloud Services repose sur une combinaison de ces deux options.

Par exemple, une application simple peut utiliser uniquement un rôle Web, tandis qu'une plus complexe utilisera un rôle de travail pour traiter les requêtes entrantes des utilisateurs, puis transmettre le travail créé par ces requêtes à un rôle de travail pour le traitement. (Cette communication pourrait utiliser Service Bus ou les files d'attente Azure.)

Ainsi que le suggère la figure, toutes les machines virtuelles d'une même application s'exécutent dans le même service cloud, comme décrit précédemment pour Azure Virtual Machines. Pour cette raison, les utilisateurs accèdent à l'application par le biais d'une seule adresse IP publique, avec un équilibrage automatique de la charge des requêtes entre les machines virtuelles de l'application. Et, de même que pour les services cloud créés avec Azure Virtual Machines, la plateforme déploiera les machines virtuelles dans une application Cloud Services de manière à éviter un unique point de défaillance matérielle.

Même si les applications s'exécutent sur des machines virtuelles, il est important de comprendre que Cloud Services fournit l'option PaaS, et non IaaS. Le processus peut être décrit de la manière suivante : avec IaaS, par exemple avec Azure Virtual Machines, vous commencez par créer et configurer l'environnement dans lequel s'exécutera votre application, puis vous déployez votre application dans cet environnement. Il vous appartient de gérer une grande partie de ce monde, par exemple, en déployant de nouvelles versions corrigées du système d'exploitation dans chaque machine virtuelle. Avec PaaS, en revanche, c'est comme si l'environnement existait déjà. Vous n'avez plus qu'à déployer votre application. La gestion de la plateforme sur laquelle elle s'exécute, y compris le déploiement de nouvelles versions du système d'exploitation, est assurée à votre place.

Avec Cloud Services, vous ne créez pas de machines virtuelles. Au contraire, vous fournissez un fichier de configuration qui indique à Azure le nombre d'instances de chaque type voulu, par exemple, trois instances de rôle Web et deux instances de rôle de travail, et la plateforme se charge de les créer pour vous. Vous continuez de choisir la taille que doivent avoir ces machines virtuelles (les options sont les mêmes qu'avec les machines virtuelles Azure), mais vous ne les créez pas vous-même de manière explicite. Si votre application a besoin de traiter une charge plus importante, vous pouvez demander davantage de machines virtuelles : Azure créera ces instances. Si la charge diminue, vous pouvez arrêter ces instances et cesser de payer pour elles.

Une application Cloud Services est normalement mise à la disposition des utilisateurs au moyen d'un processus en deux étapes. Un développeur télécharge d'abord l'application dans la zone de transit de la plateforme. Lorsqu'il est prêt à activer l'application, il utilise le portail de gestion Azure pour demander qu'elle passe en production. Le basculement entre la zone de transit et l'environnement de production peut se faire sans temps d'arrêt, ce qui permet de mettre à niveau une application en cours d'exécution vers une nouvelle version sans perturbation pour les utilisateurs.

Cloud Services fournit également la surveillance. À l'instar d'Azure Virtual Machines, ils détectent l'échec d'un serveur physique et redémarrent sur un nouvel ordinateur les machines virtuelles qui étaient exécutées sur ce serveur. Mais Cloud Services détecte aussi les échecs des machines virtuelles et des applications, et pas seulement les défaillances matérielles. À la différence des machines virtuelles, ils ont un agent à l'intérieur de chaque rôle Web et de travail, et sont donc en mesure de démarrer de nouvelles instances des machines virtuelles et des applications lorsqu'une défaillance se produit.

La nature PaaS de Cloud Services a également d'autres implications. L'une des principales est le fait que les applications basées sur cette technologie doivent être écrites pour s'exécuter correctement en cas d'échec d'un rôle Web ou de travail. Cela nécessite qu'une application Cloud Services ne conserve pas l'état dans le système de fichiers de ses propres machines virtuelles. Les écritures dans les machines virtuelles Cloud Services ne sont pas persistantes, comme elles le sont dans les machines virtuelles créées avec Azure Virtual Machines ; il n'existe rien de tel qu'un disque de données des machines virtuelles. En revanche, une application Cloud Services doit écrire explicitement tous les états dans la base de données SQL, les objets blob, les tables ou un autre stockage externe. Les applications créées de la sorte sont plus faciles à mettre à l'échelle et davantage résistantes aux défaillances, deux objectifs importants de Cloud Services.

## <span id="WhatShouldIUse"></span></a>Quel modèle dois-je utiliser ? Faire un choix

Les trois modèles d'exécution Azure vous permettent de créer des applications évolutives et fiables dans le cloud. Étant donné cette similitude fondamentale, quel modèle dois-je utiliser ? La réponse dépend de ce que vous essayez de faire.

Les machines virtuelles offrent la solution la plus générale. Si vous souhaitez exercer un contrôle maximal ou si vous avez besoin de machines virtuelles génériques, par exemple, pour le développement et les tests, il s'agit de la meilleure option. Les machines virtuelles sont également le meilleur choix pour exécuter les applications locales disponibles dans le commerce dans le cloud, comme illustré par l'exemple SharePoint décrit plus haut. Et dans la mesure où les machines virtuelles que vous créez avec cette technologie peuvent ressembler en tous points à vos machines virtuelles locales, ce choix risque également d'être le plus pertinent pour les récupérations d'urgence. L'inconvénient, naturellement, c'est que de ce pouvoir accru découle une plus grande responsabilité exigée par IaaS dans certaines tâches administratives.

Les sites Web constituent l'option idéale si vous voulez créer un simple site Web. Cela est particulièrement vrai si votre site est basé sur une application existante telle que Joomla, WordPress ou Drupal. Les sites Web conviennent également pour créer une application Web avec un faible besoin d'administration, même si elle doit être relativement évolutive, ou pour transférer une application Web IIS existante vers le cloud public. Ils permettent en outre un déploiement rapide. Une nouvelle instance de votre application peut commencer à s'exécuter presque immédiatement, alors que le déploiement d'une nouvelle machine virtuelle avec Virtual Machines ou Cloud Services peut prendre plusieurs minutes.

Cloud Services, le modèle d'exécution initial fourni par Azure, est une approche PaaS explicite. Alors que la ligne de démarcation entre PaaS et l'hébergement Web est floue, Cloud Services présente quelques différences importantes par rapport aux sites Web, notamment :

-   À la différence des sites Web, Cloud Services vous offre un accès administrateur aux machines virtuelles de votre application, ce qui vous permet d'installer les logiciels arbitraires dont votre application a besoin. Cette possibilité n'existe pas avec les sites Web.
-   Dans la mesure où Cloud Services propose à la fois des rôles Web et des rôles de travail, c'est un choix préférable aux sites Web dans le cas des applications multiniveau qui ont besoin de machines virtuelles distinctes pour leur logique métier.
-   Cloud Services fournit des environnements de production et intermédiaire distincts. Les mises à jour des applications sont ainsi plus fluides qu'avec les sites Web.
-   Vous pouvez utiliser des technologies de mises en réseau telles qu'Azure Virtual Network et Azure Connect pour connecter des ordinateurs locaux aux applications Cloud Services, ce que ne permettent pas les sites Web.
-   Cloud Services vous permet d'utiliser le Bureau à distance pour vous connecter directement aux machines virtuelles d'une application, ce qui n'est pas possible avec les sites Web.

Comme il s'agit d'un modèle PaaS, Cloud Services présente aussi certains avantages par rapport à Azure Virtual Machines. Par exemple, il permet d'exécuter à votre place un plus grand nombre de tâches de gestion, notamment le déploiement des mises à jour du système d'exploitation, et vos coûts d'exploitation sont donc susceptibles d'être plus bas qu'avec l'approche IaaS d'Azure Virtual Machines.

Les trois modèles d'exécution Azure présentent des avantages et des inconvénients. Pour faire le bon choix, vous devez comprendre ces modèles, savoir ce que vous essayez d'accomplir, puis opter pour celui qui vous convient le mieux.

Parfois, aucun modèle d'exécution ne convient à lui seul. Dans de telles situations, il est parfaitement légal de combiner des options. Supposons, par exemple, que vous créez une application dans laquelle vous souhaitez bénéficier des avantages en matière de gestion offerts par les rôles Web Cloud Services, tout en ayant besoin également d'utiliser SQL Server standard pour des raisons de compatibilité ou de performances. Dans ce cas, la meilleure solution consiste à combiner les modèles d'exécution, comme illustré dans la [figure 7][figure 7].

<a name="Fig7"></a>![07\_CombineTechnologies][07\_CombineTechnologies]

**Figure 7 : une même application peut utiliser plusieurs modèles d'exécution.**

Comme le montre cette figure, les machines virtuelles Cloud Services s'exécutent dans un service cloud distinct de celui des machines virtuelles Virtual Machines. Les deux peuvent quand même communiquer de manière relativement efficace. Ainsi, l'utilisation de cette méthode pour créer une application est parfois la meilleure option.

Azure propose plusieurs modèles d'exécution, car les plateformes cloud ont besoin de prendre en charge de nombreux scénarios. Tous ceux qui souhaitent utiliser cette plateforme de manière efficace – et si vous avez lu ce document jusqu'à la fin, vous en faites probablement partie – doivent comprendre chacun de ces modèles.

  [Machines virtuelles]: #VMachine
  [Sites Web]: #WebSites
  [Services cloud]: #CloudServices
  [Quel modèle dois-je utiliser ? Faire un choix]: #WhatShouldIUse
  [figure 1]: #Fig1
  [01\_CreatingVMs]: ./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png
  [VMDepot]: http://vmdepot.msopentech.com/
  [bibliothèque Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn197896.aspx
  [La figure 2]: #Fig2
  [02\_CloudServices]: ./media/fundamentals-application-models/ExecModels_02_CloudServices.png
  [La figure 3]: #Fig3
  [03\_AppUsingSQLServer]: ./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png
  [La figure 4]: #Fig4
  [04\_SharePointFarm]: ./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png
  [05\_Websites]: ./media/fundamentals-application-models/ExecModels_05_Websites.png
  [Figure 5]: #Fig5
  [figure 6]: #Fig6
  [06\_CloudServices2]: ./media/fundamentals-application-models/ExecModels_06_CloudServices2.png
  [figure 7]: #Fig7
  [07\_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
