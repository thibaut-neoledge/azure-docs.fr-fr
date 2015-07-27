<properties
   pageTitle="Solutions Batch et HPC dans le cloud | Microsoft Azure"
   description="Présente des scénarios de traitement par lot et de calcul haute performance (Big Compute) et les solutions proposées dans Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/01/2015"
   ms.author="danlep"/>

# Solutions HPC et Batch

Cet article présente des solutions Azure pour le calcul haute performance et par lots, également appelé *Big Compute*. Même si cet article est principalement destiné aux décideurs techniques, aux responsables informatiques et aux éditeurs de logiciels indépendants, d’autres professionnels de l'informatique et développeurs peuvent l’utiliser pour se familiariser avec ces solutions.

Les organisations ont des problèmes informatiques à grande échelle, y compris l’analyse et la conception d’ingénierie, le rendu d'image, la modélisation complexe, des simulations Monte Carlo et des calculs de risques financiers. Pour aider les organisations à résoudre ces problèmes et prendre des décisions avec les ressources, l’échelle et la planification nécessaires, Azure propose des capacités et des services de calcul évolutifs et à la demande. Avec Azure, les entreprises peuvent :

* créer des solutions hybrides, en étendant un cluster HPC local pour décharger les charges de travail de pointe vers le cloud ;

* exécuter des charges de travail de cluster HPC entièrement dans Azure à l'aide de jeux d'outils et d’applications existants ;

* utiliser un service Azure évolutif et géré comme [Batch](http://azure.microsoft.com/documentation/services/batch/) pour exécuter des charges de travail nécessitant beaucoup de ressources sans avoir à déployer et gérer l’infrastructure de calcul.

Azure propose également des outils de développement et des services pour les organisations et les éditeurs de logiciels pour créer des solutions Big Compute personnalisées de bout en bout.


## Arrière-plan : applications Batch et HPC

Contrairement aux applications Web et à de nombreuses applications métier, les applications Batch et HPC ont un début et une fin définis et elles peuvent s’exécuter selon une planification ou à la demande, parfois pendant des heures ou plus. La plupart se répartissent en deux catégories principales : *intrinsèquement parallèles* (parfois appelées « massivement parallèles », car les problèmes qu'elles résolvent se prêtent à l'exécution en parallèle sur plusieurs ordinateurs ou processeurs) et *fortement couplées*. Pour plus d'informations sur ces types d'applications, consultez le tableau suivant. Certaines solutions Azure fonctionnent mieux pour un type ou l'autre.

>[AZURE.NOTE]Dans les solutions Batch et HPC, une instance en cours d'exécution d'une application est généralement appelée un *travail* et chaque travail peut être divisé en *tâches*. Les ressources de calcul en cluster pour l'application sont souvent appelées *nœuds de calcul*.

Type | Caractéristiques | Exemples
------------- | ----------- | ---------------
**Intrinsèquement parallèles**<br/><br/>![Intrinsèquement parallèles][parallel] |• Les ordinateurs exécutent la logique applicative indépendamment<br/><br/>• L’ajout d’ordinateurs permet à l'application une mise à l’échelle et une réduction du temps de calcul<br/><br/>• L’application se compose d'exécutables séparés ou est divisée en un groupe de services appelés par un client (une application SOA ou une architecture orientée services) |• Modélisation de risques financiers<br/><br/>• Rendu et traitement d'image<br/><br/>• Codage et transcodage de fichiers multimédia<br/><br/>• Simulations Monte Carlo<br/><br/>• Tests de logiciels
**Fortement couplées**<br/><br/>![Fortement couplées][coupled] |• L’application requiert des nœuds de calcul pour interagir ou échanger des résultats intermédiaires<br/><br/>• Les nœuds de calcul peuvent communiquer à l'aide de l'interface MPI (Message Passing), un protocole de communication commun pour l'informatique parallèle<br/><br/>• L'application est sensible à la latence du réseau et à la bande passante<br/><br/>• Les performances de l'application peuvent être améliorées en utilisant une infrastructure de calcul prenant en charge les technologies de réseau à haut débit comme InfiniBand et l'accès direct à la mémoire à distance (RDMA) |• Modélisation de réservoirs de pétrole et de gaz<br/><br/>• Conception et analyse d’ingénierie, comme la dynamique des fluides informatisée<br/><br/>• Simulations physiques comme des accidents automobiles et les réactions nucléaires<br/><br/>• Prévisions météorologiques

### Considérations relatives à l'exécution d’applications Batch et HPC dans le cloud

Vous pouvez facilement migrer de nombreuses applications conçues pour fonctionner dans les clusters HPC locaux vers Azure ou un environnement hybride (intersite). Toutefois, il peut y avoir des limitations ou des points à prendre en considération, notamment :


* **Disponibilité ininterrompue des ressources cloud** : selon le type de ressources de calcul cloud sélectionnées pour la solution, il est possible que vous ne puissiez pas compter sur la disponibilité continue des machines pour la durée d'exécution d'un travail. Des points de contrôle de progression et la gestion d'état sont des techniques courantes pour gérer les défaillances temporaires possibles. Elles sont d’autant plus nécessaires lors de l'utilisation des ressources cloud.


* **Accès aux données** : les techniques d'accès aux données généralement disponibles dans un cluster de réseau d'entreprise, tels que NFS, peuvent nécessiter une configuration spéciale dans le cloud, ou il vous faudra peut-être adopter des modèles et des pratiques d'accès aux données différents pour le cloud.

* **Déplacement des données** : pour les applications traitant de grandes quantités de données, des stratégies sont nécessaires pour déplacer les données dans le stockage cloud et vers les ressources de calcul. Vous devrez peut-être envisager d’utiliser un réseau à grande vitesse intersite, comme [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/). Pensez également aux limites juridiques, réglementaires ou de politique pour le stockage ou l'accès à ces données.


* **Gestion des licences** : vérifiez auprès du fournisseur de toute application commerciale les questions de licence ou toute autre restriction relative à l'exécution dans le cloud. Tous les fournisseurs ne proposent pas le paiement à l'utilisation pour les licences. Vous devrez peut-être planifier un serveur de licences dans le cloud pour votre solution ou une connexion à un serveur de licences sur site.


### Big Compute ou Big Data ?

Les lignes de démarcation entre les applications Big Compute et Big Data ne sont pas toujours claires. Certaines applications peuvent avoir des caractéristiques des deux catégories. Elles concernent toutes deux l'exécution de calculs à grande échelle, généralement sur des clusters d'ordinateurs locaux, dans le cloud ou les deux. Mais les approches des solutions et les outils de prise en charge peuvent différer.

• **Big Compute** implique généralement des applications qui reposent sur la puissance du CPU et la mémoire, comme les simulations d'ingénierie, la modélisation de risques financiers et le rendu numérique. Les clusters qui alimentent une solution Big Compute peuvent inclure des ordinateurs dotés de processeurs multicœurs spécialisés pour effectuer des calculs bruts et du matériel réseau haute vitesse spécialisé pour connecter les ordinateurs.

• **Big Data** résout les problèmes d'analyse de données qui impliquent de grandes quantités de données ne pouvant pas être gérées par un seul ordinateur ou un système de gestion de bases de données, comme de grands volumes de journaux Web ou d'autres données décisionnelles. Les données volumineuses tendent à reposer sur la capacité des disques et les performances d'E/S plutôt que sur la puissance du processeur. Une solution Big Data utilise souvent des outils spécialisés comme Apache Hadoop pour gérer le cluster et partitionner les données. (Pour plus d'informations sur Azure HDInsight et d'autres solutions Azure Hadoop, consultez [Hadoop](http://azure.microsoft.com/solutions/hadoop/).)

## Gestion des ressources et planification des travaux

L’exécution d’une application Batch et HPC inclut généralement un *gestionnaire de cluster* et un *planificateur de travaux* pour aider à gérer les ressources de calcul en cluster et les allouer aux applications qui exécutent les tâches. Ces fonctions peuvent être exécutées par des outils distincts ou un outil intégré.

* **Gestionnaire de cluster** : fournit, libère et administre les ressources de calcul (ou nœuds de calcul). En fonction de l'outil, un gestionnaire de cluster peut automatiser l'installation des images de système d'exploitation et des applications sur les nœuds de calcul, mettre à l’échelle les ressources de calcul selon les besoins et surveiller les performances des nœuds.

* **Planificateur de travaux** : spécifie les ressources (telles que les processeurs ou la mémoire) dont une application a besoin et les conditions d’exécution. Un planificateur de travaux gère une file d'attente de travaux et leur alloue des ressources selon leur priorité ou d'autres caractéristiques.

Les outils de clustering et de planification des travaux pour les clusters Windows et Linux, ou ceux développés de manière indépendante, migrent correctement vers Azure. Par exemple,[Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) est la solution de cluster de calcul gratuite de Microsoft pour les ordinateurs Windows et Windows Server. Pour réduire le besoin de ressources de calcul locales dédiées, vous pouvez étendre un cluster HPC Pack pour utiliser les nœuds de calcul Azure à la demande ou déployer un cluster entièrement dans des machines virtuelles Azure.

### Flux de travail Big Compute

Les outils de gestion des clusters et de planification des travaux permettent un flux de travail Big Compute aux étapes prévisibles. En fonction de la solution, vous pouvez omettre certaines étapes de la liste suivante ou introduire des outils supplémentaires et personnalisés. Les flux de travail avec beaucoup de données peuvent impliquer des étapes de pré et post-traitement des données supplémentaires (non répertoriées).

1. **Provisionnement** : prépare l'environnement de calcul avec l'infrastructure, les ressources de calcul et le stockage nécessaires pour exécuter des applications

2. **Mise en place** : déplace les données d'entrée et les applications dans l'environnement de calcul

3. **Planification** : configure des travaux et des tâches et leur alloue des ressources de calcul lorsque les ressources sont disponibles

4. **Surveillance** : fournit des informations d'état sur les travaux et les tâches ; gère les erreurs ou exceptions

5. **Fin** : retourne les résultats et les données de post-traitement si nécessaire

## Services Azure pour Big Compute

Azure dispose d’une gamme de services de calcul, de données, de mise en réseau et autres services associés que vous pouvez utiliser pour les flux de travail et les solutions Big Compute. Pour obtenir des instructions détaillées sur chacun de ces services, consultez la documentation des services Azure. Voir la rubrique [Scénarios de solution](#solution-scenarios) dans cet article pour certaines des approches courantes avec les applications Batch et HPC.

>[AZURE.NOTE]De nouveaux services sont introduits régulièrement sur la plateforme Azure et peuvent s'avérer utiles pour votre scénario. L’utilisation des services en version préliminaire est recommandée uniquement pour les déploiements de test ou de validation technique, et non pour les charges de travail de production. Si vous avez des questions, contactez un [partenaire Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou envoyez un courrier électronique à *bigcompute@microsoft.com*.

### Services de calcul

Les services de calcul dans Azure sont au cœur des solutions Big Compute. Les services de calcul du tableau suivant sont fréquemment utilisés et offrent des avantages pour différents scénarios. Ces services offrent des modes différents pour que les applications s’exécutent sur des instances de calcul basées sur une machine virtuelle qu’Azure fournit à l'aide de la technologie Hyper-V de Windows Server. Selon le service, ces instances peuvent exécuter une variété de systèmes d'exploitation et d’outils Linux et Windows, standard et personnalisés. Azure fournit une [plage de tailles d'instance](../virtual-machines/virtual-machines-sizes-specs.md) avec différentes configurations de cœurs de CPU, de mémoire, de capacité de disque et d'autres caractéristiques. Selon vos besoins, vous pouvez mettre à l'échelle les instances à des milliers de cœurs et les réduire lorsque vous avez besoin de moins de ressources.

>[AZURE.NOTE]Vous pouvez tirer parti des instances A8-A11 pour améliorer les performances des charges de travail ayant besoin de beaucoup de ressources, y compris des applications MPI parallèles qui nécessitent une faible latence et un réseau d'application haut débit. Consultez la page [À propos des instances de calcul intensif A8, A9, A10 et A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

de diffusion en continu | Description
------------- | -----------
**[Services cloud](http://azure.microsoft.com/documentation/services/cloud-services)**<br/><br/> |• Peuvent exécuter des applications Big Compute dans des instances de rôle de travail, qui sont des machines virtuelles qui exécutent une version de Windows Server et sont entièrement gérées par Azure<br/><br/>• Fournissent des applications évolutives et fiables avec une charge administrative basse, exécutées dans un modèle de plateforme en tant que service (PaaS)<br/><br/>• Peuvent nécessiter un développement ou des outils supplémentaires pour une intégration avec les solutions de cluster HPC locales
**[Machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines)**<br/><br/> |• Fournissent l’infrastructure de calcul en tant que service (IaaS) à l'aide de la technologie Microsoft Hyper-V<br/><br/>• Permettent de configurer et de gérer de manière flexible des ordinateurs cloud persistants depuis des images standard Windows Server ou Linux, ou encore des images et des disques de données que vous fournissez ou provenant d’[Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Exécutent les outils de clusters de calcul locaux et les applications entièrement dans le cloud
**[Batch](http://azure.microsoft.com/documentation/services/batch)**<br/><br/> |• Exécute des charges de travail à grande échelle parallèles et par lot comme le rendu d'image et le codage et transcodage multimédia dans un service entièrement géré<br/><br/>• Fournit la planification des tâches et la mise à l'échelle automatique d'un pool de machines virtuelles géré<br/><br/>• Permet aux développeurs de créer et d’exécuter des applications en tant que service ou d’adapter des applications existantes au cloud<br/>

### Services de stockage

Une solution Big Compute fonctionne généralement sur un jeu de données d'entrée et génère des données pour ses résultats. Voici quelques-uns des services de stockage Azure utilisés dans de nombreuses solutions Big Compute :

* [Stockage de file d’attente, table et blob](http://azure.microsoft.com/documentation/services/storage) : gère de grandes quantités de données non structurées, de données NoSQL et de messages de flux de travail et de communication. Par exemple, vous pouvez utiliser le stockage d'objets blob pour les grands ensembles de données techniques, des images d'entrée ou des fichiers multimédia pour vos processus d'application. Vous pouvez utiliser les files d'attente pour la communication asynchrone dans une solution. Voir [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md) pour en savoir plus sur ces solutions de stockage.

* [Stockage de fichiers Azure](http://azure.microsoft.com/services/storage/files/) : partage de fichiers communs et de données dans Azure à l'aide du protocole SMB standard, qui est requis pour certaines solutions de cluster HPC.

### Services de données et d’analyse

Certains scénarios Big Compute impliquent des flux de données à grande échelle ou génèrent des données nécessitant un traitement ou une analyse supplémentaire. Pour ce faire, Azure propose plusieurs services de données et d’analyse, notamment :

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory) : génère des flux de travail (pipelines) pilotés par les données qui joignent, agrègent et transforment des données provenant de magasins de données Internet, locaux et sur le cloud

* [Base de données SQL](http://azure.microsoft.com/documentation/services/sql-database) : offre les fonctionnalités clés d'un système de gestion de bases de données relationnelles Microsoft SQL Server dans un service de plateforme gérée.

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight) : déploie et approvisionne les clusters Windows Server ou Apache Hadoop basés sur Linux dans le cloud pour gérer, analyser et créer des rapports sur les données volumineuses avec une grande fiabilité et une disponibilité importante.

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning) : vous permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans un service de plateforme entièrement managé.

### Services supplémentaires

Votre solution Big Compute peut devoir inclure d'autres services d'infrastructure et de plateforme Azure pour se connecter aux ressources locales ou dans d'autres environnements. En voici quelques exemples :

* [Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network) : crée une section logiquement isolée dans Azure pour connecter des ressources Azure à votre centre de données local ou un seul ordinateur client à l'aide d'IPSec ; permet aux applications Big Compute d'accéder aux données locales, aux services Active Directory et aux serveurs de licences

* [ExpressRoute](http://azure.microsoft.com/documentation/services/expressroute) : crée une connexion privée entre les centres de données Microsoft et l'infrastructure locale ou dans un environnement de colocalisation, avec une sécurité accrue, plus de fiabilité, des vitesses plus élevées et des latences moindres que les connexions classiques sur Internet.

* [Service Bus](http://azure.microsoft.com/documentation/services/service-bus) : fournit plusieurs mécanismes pour permettre aux applications de communiquer ou d'échanger des données, qu’elles se trouvent sur Azure, sur une autre plateforme cloud ou dans un centre de données.

## Scénarios de solution

Voici des scénarios courants pour exécuter des charges de travail Big Compute dans Azure en tirant parti des solutions de cluster HPC existantes, des services Azure ou une combinaison des deux.

>[AZURE.NOTE]Pour les organisations avec des charges de travail intensives qui ne conviennent pas aux outils de cluster HPC standard ou qui ne migrent pas directement vers les services Azure, Azure fournit aux développeurs et aux partenaires un ensemble de capacités de calcul, de services, de choix d'architecture et d’outils de développement. Azure prend en charge les flux de travail personnalisés Big Compute qui s'adaptent à des milliers de cœurs de calcul.

### Scénario 1 Croissance d’un cluster HPC local vers Azure

**Quand choisir cette solution ?** : vous avez peut-être déjà un cluster HPC local exécutant vos charges de travail de calcul intensives, mais il a besoin de ressources de calcul supplémentaires pour les périodes de pointe, par exemple pour l’établissement des rapports de fin de mois ou pour des projets spéciaux. Au lieu d'acheter, de déployer et de gérer du matériel et des logiciels supplémentaires qui seront inactifs la plupart du temps, vous pouvez utiliser Azure pour ajouter de la puissance de calcul à la demande à votre cluster existant.

Par exemple, si votre cluster HPC local existant est généré avec [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), vous pouvez ajouter des ressources de calcul supplémentaires sous la forme d'instances de rôle de travail Azure s'exécutant dans un service cloud. Voir la figure suivante. Pour plus d'informations et pour obtenir des instructions détaillées, consultez [Burst to Azure with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

![Intégration de cluster][burst_cluster]

>[AZURE.NOTE]Si vous souhaitez réduire l'encombrement du cluster HPC Pack, vous pouvez réduire le cluster local au nœud principal HPC Pack uniquement. Ensuite, ajoutez toutes les ressources de calcul à la demande dans Azure. Pour obtenir un didacticiel qui vous guide dans ce scénario, consultez [Configuration d'un cluster de calcul hybride avec Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md).

Cette solution hybride exploite un investissement existant dans un cluster local, mais vous permet de mettre à l'échelle l'infrastructure locale fixe pour les charges de travail habituelles (heures creuses). Si vous avez besoin d’accéder à un magasin de données ou à un serveur de licences local, vous pouvez configurer un réseau virtuel Azure pour connecter le cluster local à Azure.

### Scénario 2 Création d’un cluster HPC entièrement dans Azure

**Quand choisir cette solution ?** : vous disposez peut-être déjà d’un investissement important dans un cluster local HPC Windows ou Linux comprenant des outils de gestion et de planification, ainsi que des applications personnalisées. Vous avez besoin d’une capacité de cluster supplémentaire pour exécuter vos applications et ensembles d'outils existants, mais vous ne souhaitez pas investir dans une infrastructure locale supplémentaire ou modifier vos applications. Ou vous devez peut-être créer un nouveau cluster pour une période courte ou longue, mais vous ne souhaitez pas supporter le coût d'achat, de maintenance et de fonctionnement, ni allouer l'espace pour l’installer et le déployer.

Vous pouvez utiliser les outils d'automatisation Azure pour créer un cluster HPC dans les machines virtuelles Azure pour créer la capacité nécessaire. Selon les machines virtuelles que vous déployez, vous pouvez exécuter une variété de charges de travail HPC et parallèles, y compris des applications MPI fortement couplées.

>[AZURE.NOTE]Vérifiez auprès du fournisseur de votre solution de cluster local et d’applications les exigences supplémentaires et les meilleures pratiques pour l'exécution dans un cloud public fournissant une infrastructure en tant que service (IaaS).

Par exemple, vous pouvez créer un cluster HPC Windows Server avec [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) dans les machines virtuelles de services d’infrastructure Azure (IaaS) pour exécuter vos charges de travail, comme indiqué dans l'illustration simplifiée suivante. Un utilisateur de cluster peut soumettre un travail de manière sécurisée au cluster cloud via les outils d'envoi de travail du HPC Pack standard s’exécutant sur un ordinateur client. Voir [Microsoft HPC Pack dans les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx) pour les détails et les options de déploiement.

![Cluster dans IaaS][iaas_cluster]

**Déploiement automatisé** : pour déployer un grand nombre de machines virtuelles Windows Server ou Linux, vous pouvez utiliser des images de machines virtuelles standard ou personnalisées et des outils d'automatisation Azure comme [l'interface de ligne de commande Azure](../xplat-cli.md) ou [Azure PowerShell](../powershell-install-configure.md). En voici quelques exemples :

* Pour déployer un cluster HPC Pack dans les services d'infrastructure Azure, vous pouvez exécuter un [script Azure PowerShell](https://msdn.microsoft.com/library/azure/dn864734.aspx) flexible à partir d'un ordinateur client : le script utilise une image de machine virtuelle Windows Server avec HPC Pack préinstallé. Vous pouvez également utiliser un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) Azure avec Azure PowerShell ou l'interface de ligne de commande Azure pour déployer un cluster HPC Pack.

* Vous pouvez utiliser un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/slurm/) Azure avec Azure PowerShell ou l'interface de ligne de commande Azure pour déployer un cluster Linux exécutant le gestionnaire de charge de travail open source [SLURM](https://computing.llnl.gov/linux/slurm/).

Placer l'ensemble d'un cluster HPC dans le cloud présente des avantages clairs.

* Une organisation peut exécuter des travaux HPC sans acheter et gérer du matériel local supplémentaire et peut contrôler la taille du cluster afin d’utiliser les ressources de calcul efficacement.

* De nombreuses applications de cluster local peuvent continuer à s’exécuter sans modification, ou avec quelques modifications mineures, dans un cluster basé sur le cloud.

* En comparaison avec une solution hybride qui étend un cluster local vers le cloud, l’exécution d'une application entièrement dans le cloud peut simplifier l'accès aux données. Plutôt que de diviser les données entre les installations cloud et locales ou de forcer une partie de l'application à accéder aux données à distance, toutes les données d'application peuvent être stockées dans le cloud.

* Certains éditeurs de logiciels optimisent leurs applications pour une exécution dans des clusters dans le cloud. Par exemple, en déployant [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) de MathWorks sur un cluster HPC Pack dans les machines virtuelles Azure, vous pouvez exécuter des travaux MATLAB parallèles entièrement avec des ressources de calcul cloud.

### Scénario 3 Montée en charge d’une application parallèle vers Azure

**Quand choisir cette solution ?** : vous pouvez exécuter une application nécessitant beaucoup de ressources, comme une simulation de Monte Carlo, un rendu d'animation ou un transcodage multimédia sur des stations de travail locales ou un petit cluster. Vous ne souhaitez pas gérer les ressources de calcul ou un planificateur de tâches ; au lieu de cela, vous souhaitez vous concentrer sur votre application efficacement pour résoudre les problèmes commerciaux. Ou vous pouvez vouloir décharger votre application nécessitant beaucoup de ressources ou une application tierce afin qu’elle s'exécute entièrement en tant que service dans le cloud.

En fonction de la charge de travail, vous pourriez tirer parti d'un service Big Compute existant dans Azure, hébergé par Microsoft ou un autre fournisseur de services, pour simplifier la gestion de l'infrastructure et de l'application de votre solution. Certains services hébergent des applications spécifiques pour les clients des secteurs concernés. Certains services se connectent à des applications locales, pour une solution hybride. D'autres, comme [Azure Media Services](http://azure.microsoft.com/documentation/services/media-services), sont des services de plateforme dédiés.

Pour adapter facilement au cloud une application que vous exécutez aujourd’hui et permettre sa montée en charge, [Batch](http://azure.microsoft.com/documentation/services/batch) fournit des API pour planifier des travaux et gérer des ressources de calcul dans un service. Batch gère le déploiement et la mise à l'échelle automatique des machines virtuelles, la planification des tâches, la récupération d'urgence, le déplacement des données, la gestion des dépendances, le déploiement d'applications et tous les autres éléments nécessaires à l'exécution des travaux dans le cloud. Actuellement, Batch est idéal pour les applications intrinsèquement parallèles comme le rendu d'image, la modélisation de risques financiers et les simulations Monte Carlo s'exécutant avec des ressources de calcul Windows Server.

Consultez la figure suivante pour voir un flux de travail typique qu'un développeur peut créer avec Batch.

![Azure Batch][batch_proc]

1. Téléchargez les fichiers d'entrée (comme les données ou images sources, les fichiers .exe des applications ou les fichiers de script requis et leurs dépendances) vers le stockage Azure.

2. Créez un service Batch qui :

    a. Déploie un pool de machines virtuelles Azure identiques pour exécuter l'application, comme des nœuds de calcul dans un cluster HPC. Le développeur spécifie leur taille, le système d'exploitation sur lequel elles s'exécutent et d'autres propriétés comme la possibilité de mise à l'échelle automatique du pool. Quand une tâche s'exécute, elle reçoit automatiquement une machine virtuelle à partir de ce pool.

    b. Crée un travail pour exécuter l'application. Le développeur peut spécifier une planification et une priorité pour le travail, ou le travail peut s’exécuter à la demande.

    c. Partitionne le travail en tâches. Chaque tâche est essentiellement une ligne de commande qui s'exécute sur une des machines virtuelles dans le pool pour traiter les informations à partir d'un des fichiers de données téléchargés vers le stockage.

3. Exécutez le service et surveillez les résultats.


## Étapes suivantes

* Consultez [Ressources techniques pour Batch et HPC](big-compute-resources.md) pour des conseils techniques pour votre solution.

* Pour les dernières annonces, consultez le [blog de l'équipe Microsoft HPC et Batch](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](http://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=July15_HO3-->