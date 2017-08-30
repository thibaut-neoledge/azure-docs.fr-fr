---
title: "Solutions Batch et HPC dans le cloud - Azure | Microsoft Docs"
description: "Découvrez les scénarios de Batch Computing et de calcul haute performance (HPC et Big Compute) et les solutions proposées dans Azure"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: d30c09d23a84200ba81df133c9de5b11910ffb61
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Solutions Batch et HPC pour les charges de travail de calcul à grande échelle

Azure offre des solutions efficaces et évolutives pour le Batch Computing et le calcul haute performance (HPC), également désignés ensemble sous le terme de *Big Compute*. Découvrez ici les charges de travail Big Compute et les services Azure nécessaires à leur prise en charge, ou passez directement aux [scénarios de solution](#scenarios) plus loin dans cet article. Cet article est principalement destiné aux décideurs techniques, aux responsables informatiques et aux éditeurs de logiciels indépendants, mais d’autres professionnels de l'informatique et développeurs peuvent l’utiliser pour se familiariser avec ces solutions.

Les organisations ont des problèmes informatiques à grande échelle : analyse et conception d’ingénierie, rendu d’image, modélisation complexe, simulations Monte Carlo, calculs de risques financiers, etc. Azure les aide à résoudre ces problèmes avec les ressources, l’échelle et la planification nécessaires. Avec Azure, les entreprises peuvent :

* créer des solutions hybrides, en étendant un cluster HPC local pour décharger les charges de travail de pointe vers le cloud ;
* Exécuter des outils de cluster HPC et des charges de travail entièrement dans Azure
* utiliser des services Azure évolutifs et gérés comme [Batch](https://azure.microsoft.com/documentation/services/batch/) pour exécuter des charges de travail nécessitant beaucoup de ressources sans avoir à déployer et gérer l’infrastructure de calcul.

Bien que cela dépasse le cadre de cet article, Azure fournit également aux développeurs et aux partenaires un ensemble complet de fonctionnalités, de choix d'architectures et d'outils de développement pour créer des flux de travail Big Compute à grande échelle et personnalisés. Et un écosystème croissant de partenaires est prêt à vous aider à améliorer la productivité de vos charges de travail Big Compute dans le cloud Azure.

## <a name="batch-and-hpc-applications"></a>Applications Batch et HPC
Contrairement aux applications Web et à de nombreuses applications métier, les applications Batch et HPC ont un début et une fin définis et elles peuvent s’exécuter selon une planification ou à la demande, parfois pendant des heures ou plus. La plupart se répartissent en deux catégories principales : *intrinsèquement parallèles* (parfois appelées « massivement parallèles », car les problèmes qu'elles résolvent se prêtent à l'exécution en parallèle sur plusieurs ordinateurs ou processeurs) et *fortement couplées*. Pour plus d'informations sur ces types d'applications, consultez le tableau suivant. Certaines solutions Azure fonctionnent mieux pour un type ou l'autre.

> [!NOTE]
> Dans les solutions Batch et HPC, une instance en cours d’exécution d'une application est généralement appelée un *travail* et chaque travail peut être divisé en *tâches*. Les ressources de calcul en cluster pour l'application sont souvent appelées *nœuds de calcul*.
> 
> 

| Type | Caractéristiques | Exemples |
| --- | --- | --- |
| **Intrinsèquement parallèles**<br/><br/>![Intrinsèquement parallèles][parallel] |• Les ordinateurs exécutent la logique applicative indépendamment<br/><br/> • L’ajout d’ordinateurs permet une mise à l’échelle de l’application et la réduction du temps de calcul<br/><br/>• L’application se compose d'exécutables séparés ou est divisée en un groupe de services appelés par un client (une application SOA ou une architecture orientée services) |• Modélisation de risques financiers<br/><br/>• Rendu et traitement d’images<br/><br/>• Encodage et transcodage multimédia<br/><br/>• Simulations Monte Carlo<br/><br/>• Tests de logiciels |
| **Tightly coupled**<br/><br/>![Tightly coupled][coupled] |• L’application requiert des nœuds de calcul pour interagir ou échanger des résultats intermédiaires<br/><br/>• Les nœuds de calcul peuvent communiquer à l’aide de MPI (Message Passing Interface), un protocole de communication courant pour le calcul parallèle<br/><br/>• L’application est sensible à la bande passante et à la latence du réseau<br/><br/>• Les performances de l'application peuvent être améliorées en utilisant des technologies de réseau à haut débit comme InfiniBand et l'accès direct à la mémoire à distance (RDMA) |• Modélisation de réservoirs de pétrole et de gaz<br/><br/>• Analyse et conception technique, comme le calcul des dynamiques des fluides<br/><br/>• Simulations physiques comme des accidents de voiture et des réactions nucléaires<br/><br/>• Prévisions météorologiques |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considérations relatives à l'exécution d’applications Batch et HPC dans le cloud
Vous pouvez facilement migrer de nombreuses applications conçues pour fonctionner dans les clusters HPC locaux vers Azure ou un environnement hybride (intersite). Toutefois, il peut y avoir des limitations ou des points à prendre en considération, notamment :

* **Disponibilité des ressources cloud** : selon le type de ressources de calcul cloud que vous utilisez, il est possible que vous ne puissiez pas compter sur la disponibilité continue des machines pendant l’exécution d’une tâche. Des points de contrôle de progression et la gestion d’état sont des techniques courantes pour gérer les défaillances temporaires possibles. Elles sont d’autant plus nécessaires lors de l’utilisation de ressources cloud.
* **Accès aux données** : les techniques d’accès aux données généralement disponibles dans les clusters d’entreprise, telles que NFS, peuvent nécessiter une configuration spéciale dans le cloud. Il se peut également que vous deviez adopter des modèles et des pratiques d’accès aux données différents pour le cloud.
* **Déplacement des données** : pour les applications traitant de grandes quantités de données, des stratégies sont nécessaires pour déplacer les données dans le stockage cloud et vers les ressources de calcul. Vous devrez peut-être utiliser un réseau haut débit intersite, par exemple [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pensez également aux limites juridiques, réglementaires ou de politique pour le stockage ou l'accès à ces données.
* **Gestion des licences** : vérifiez auprès du fournisseur de toute application commerciale les questions de licence ou toute autre restriction relative à l'exécution dans le cloud. Tous les fournisseurs ne proposent pas le paiement à l'utilisation pour les licences. Vous devrez peut-être planifier un serveur de licences dans le cloud pour votre solution ou pour vous connecter à un serveur de licences sur site.

### <a name="big-compute-or-big-data"></a>Big Compute ou Big Data ?
La ligne de démarcation entre les applications Big Compute et Big Data n'est sont pas toujours claire. Certaines applications peuvent avoir des caractéristiques des deux catégories. Elles concernent toutes deux l'exécution de calculs à grande échelle, généralement sur des clusters d'ordinateurs. Mais les approches des solutions et les outils de prise en charge peuvent différer.

• **Big Compute** implique généralement des applications qui reposent sur la puissance du CPU et la mémoire, comme les simulations d'ingénierie, la modélisation de risques financiers et le rendu numérique. L’infrastructure d’une solution Big Compute peut inclure des ordinateurs dotés de processeurs multicœurs spécialisés pour effectuer des calculs bruts et du matériel réseau haute vitesse spécialisé pour connecter les ordinateurs.

• Le **Big Data** résout les problèmes d’analyse de données qui impliquent de grandes quantités de données ne pouvant pas être gérées par un seul ordinateur ou un système de gestion de bases de données, par exemple les volumes importants de journaux web ou d’autres données décisionnelles. Le Big Data s’appuie généralement plus sur la capacité de disque et les performances E/S que sur la puissance de processeur. Il existe également des outils de Big Data spécialisés, tels qu’Apache Hadoop, pour gérer le cluster et partitionner les données. (Pour plus d’informations sur Azure HDInsight et d’autres solutions Azure Hadoop, consultez [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Gestion des calculs et planification des travaux
L’exécution d’applications Batch et HPC inclut souvent un *gestionnaire de cluster* et un *planificateur de travaux* pour aider à gérer les ressources de calcul en cluster et les allouer aux applications qui exécutent les tâches. Ces fonctions peuvent être exécutées par des outils distincts ou un outil ou service intégré.

* **Gestionnaire de cluster** : fournit, libère et administre les ressources de calcul (ou nœuds de calcul). Un gestionnaire de cluster peut automatiser l'installation des images de système d'exploitation et des applications sur les nœuds de calcul, mettre à l’échelle les ressources de calcul selon les besoins et surveiller les performances des nœuds.
* **Planificateur de travaux** : spécifie les ressources (telles que les processeurs ou la mémoire) dont une application a besoin et les conditions d’exécution. Un planificateur de travaux gère une file d'attente de travaux et leur alloue des ressources selon leur priorité ou d'autres caractéristiques.

Les outils de clustering et de planification des travaux pour les clusters Windows et Linux migrent correctement vers Azure. Par exemple, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), la solution de cluster de calcul gratuite de Microsoft pour les charges de travail HPC Windows et Linux, offre plusieurs options d'exécution dans Azure. Vous pouvez également créer des clusters Linux pour exécuter des outils open source tels que Torque et SLURM. Vous avez par ailleurs la possibilité d’intégrer dans Azure des solutions de Grid Computing commerciales, telles que [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), [IBM Spectrum Symphony and Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/) et [Univa Grid Engine](http://www.univa.com/products/grid-engine).

Comme indiqué dans les sections suivantes, vous pouvez également tirer parti des services Azure pour gérer les ressources de calcul et planifier des travaux sans (ou en plus) les outils de gestion de cluster traditionnels.

## <a name="scenarios"></a>Scénarios
Voici trois scénarios courants pour exécuter des charges de travail Big Compute dans Azure en utilisant des solutions de cluster HPC existantes, des services Azure ou une combinaison des deux. Les points clés à prendre en compte pour choisir chaque scénario sont répertoriés mais ne sont pas exhaustifs. Vous trouverez plus loin dans cet article des informations complémentaires sur les services Azure disponibles dans votre solution.

| Scénario | Pourquoi choisir celui-ci ? |
| --- | --- | --- |
| **Intégration d’un cluster HPC avec Azure**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> En savoir plus :<br/>• [Intégration à des instances de travail Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurer un cluster de calcul hybride avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Intégration à Azure Batch avec HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• Combinez votre [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) ou autre cluster local avec des ressources Azure supplémentaires dans une solution hybride.<br/><br/>• Étendez vos charges de travail Big Compute pour une exécution sur des instances de machine virtuelle PaaS (actuellement Windows Server uniquement).<br/><br/>• Accédez à une banque de données ou à un serveur de licences local à l’aide d’un réseau virtuel Azure facultatif. |
| **Création d’un cluster HPC entièrement dans Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>En savoir plus :<br/>• [Solutions de cluster HPC dans Azure](big-compute-resources.md)<br/><br/> |• Déployez rapidement et uniformément vos applications et outils de cluster sur des machines virtuelles IaaS (infrastructure as a service) Windows ou Linux standard ou personnalisées.<br/><br/>• Exécutez différentes charges de travail Big Compute à l’aide de la solution de planification des tâches de votre choix.<br/><br/>• Utilisez des services Azure supplémentaires, y compris de mise en réseau et de stockage, pour créer des solutions complètes basées sur le cloud. |
| **Montée en charge d’une application parallèle vers Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>En savoir plus :<br/>• [Notions de base d’Azure Batch](batch-technical-overview.md)<br/><br/>• [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) |• Développez avec [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) pour la montée en charge de différentes charges de travail Big Compute à exécuter sur des pools de machines virtuelles Windows ou Linux.<br/><br/>• Utilisez un service de plateforme Azure pour gérer le déploiement et la mise à l’échelle automatique des machines virtuelles, la planification des travaux, la récupération d’urgence, le déplacement des données, la gestion des dépendances et le déploiement d’applications. |

## <a name="azure-services-for-big-compute"></a>Services Azure pour Big Compute
Voici des informations supplémentaires sur les services de calcul, de données, de mise en réseau et autres services associés que vous pouvez combiner pour les flux de travail et les solutions Big Compute. Pour obtenir des instructions détaillées sur les services Azure, consultez la [documentation](https://azure.microsoft.com/documentation/)correspondante. Les [scénarios](#scenarios) décrits plus haut dans cet article montrent quelques façons d’utiliser ces services.

> [!NOTE]
> Azure lance régulièrement de nouveaux services qui pourraient être utiles dans votre scénario. Si vous avez des questions, contactez un [partenaire Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou envoyez un e-mail à *bigcompute@microsoft.com*.
> 
> 

### <a name="compute-services"></a>Services de calcul
Les services de calcul Azure sont au cœur d'une solution Big Compute, et les divers services de calcul offrent des avantages pour différents scénarios. Ces services offrent des modes différents pour que les applications s’exécutent sur des instances de calcul basées sur une machine virtuelle qu’Azure fournit à l'aide de la technologie Hyper-V de Windows Server. Ces instances peuvent exécuter des systèmes d’exploitation et des outils Linux et Windows standard et personnalisés. Azure vous propose un choix de [tailles d’instance](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avec différentes configurations de cœurs d’UC, de mémoire, de capacité de disque et d’autres caractéristiques. Selon vos besoins, vous pouvez mettre à l’échelle les instances à des milliers de cœurs et les réduire lorsque vous avez besoin de moins de ressources.

> [!NOTE]
> Tirez parti des [instances Azure nécessitant beaucoup de ressources système telles que la série H](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour améliorer les performances et l’évolutivité des charges de travail HPC. Ces instances prennent également en charge des applications MPI parallèles qui nécessitent un réseau d’application à faible latence et à débit élevé. Sont également disponibles les machines virtuelles de [série N](../virtual-machines/windows/sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avec des processeurs graphiques NVIDIA pour étendre l’éventail de scénarios de calcul et de visualisation dans Azure.  
> 
> 

| de diffusion en continu | Description |
| --- | --- |
| **[Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fournissent l’infrastructure de calcul en tant que service (IaaS) à l’aide de la technologie Microsoft Hyper-V.<br/><br/>• Vous permettent de configurer et de gérer de manière flexible les ordinateurs cloud persistants à partir d’images Windows Server ou Linux standard depuis [Azure Marketplace](https://azure.microsoft.com/marketplace/) ou d’images et de disques de données que vous fournissez<br/><br/>• Peuvent être déployées et gérées comme des [VM Scale Sets](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) pour générer des services à grande échelle à partir de machines virtuelles identiques, avec mise à l’échelle automatique pour augmenter ou diminuer automatiquement la capacité<br/><br/>• Exécutent des applications et des outils de cluster de calcul local entièrement sur le cloud<br/><br/> |
| **[Services cloud](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Peuvent exécuter des applications Big Compute dans des instances de rôle de travail, qui sont des machines virtuelles qui exécutent une version de Windows Server et sont entièrement gérées par Azure<br/><br/>• Activent des applications évolutives et fiables avec une charge administrative basse, exécutées dans un modèle de plateforme en tant que service (PaaS)<br/><br/>• Peuvent nécessiter un développement ou des outils supplémentaires pour une intégration avec les solutions de cluster HPC locales |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Exécute des charges de travail à grande échelle parallèles et par lot dans un service entièrement géré.<br/><br/>• Fournit la planification des tâches et la mise à l’échelle automatique d’un pool géré de machines virtuelles<br/><br/>• Permet aux développeurs de générer et d’exécuter des applications en tant que service ou des applications cloud existantes<br/> |

### <a name="storage-services"></a>Services de stockage
Une solution Big Compute fonctionne généralement sur un jeu de données d'entrée et génère des données pour ses résultats. Voici quelques-uns des services de stockage Azure utilisés dans les solutions Big Compute :

* [Stockage de file d’attente, table et blob](https://azure.microsoft.com/documentation/services/storage/) : gère de grandes quantités de données non structurées, de données NoSQL et de messages de flux de travail et de communication. Par exemple, vous pouvez utiliser le stockage d’objets blob pour les jeux volumineux de données techniques ou pour les images d’entrée ou les fichiers multimédias traités par votre application. Vous pouvez utiliser les files d'attente pour la communication asynchrone dans une solution. Voir l’article [Introduction à Microsoft Azure Storage](../storage/common/storage-introduction.md).
* [Stockage de fichiers Azure](https://azure.microsoft.com/services/storage/files/) : partage des fichiers communs et des données dans Azure à l’aide du protocole SMB standard, qui est requis pour certaines solutions de cluster HPC.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) : fournit un système Apache HDFS à très grande échelle pour le cloud, utile pour l’analyse interactive, en temps réel et en mode batch.

### <a name="data-and-analysis-services"></a>Services de données et d’analyse
Certains scénarios Big Compute impliquent des flux de données à grande échelle ou génèrent des données nécessitant un traitement ou une analyse supplémentaire. Azure propose plusieurs services de données et d’analyse, notamment :

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) : génère des flux de travail (pipelines) pilotés par les données qui joignent, agrègent et transforment des données provenant de banques de données Internet, locales et basées sur le cloud.
* [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/) : offre les fonctionnalités clés d’un système de gestion de base de données relationnelle Microsoft SQL Server dans un service administré.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) : déploie et approvisionne les clusters Windows Server ou Apache Hadoop basés sur Linux dans le cloud pour gérer, analyser les Big Data et créer des rapports afférents.
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) : vous permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans un service entièrement administré.

### <a name="additional-services"></a>Services supplémentaires
Votre solution Big Compute peut devoir inclure d'autres services Azure pour se connecter aux ressources locales ou dans d'autres environnements. Voici quelques exemples :

* [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) : crée une section logiquement isolée dans Azure pour connecter des ressources Azure entre elles ou à votre centre de données local. Avec un réseau virtuel intersite, les applications Big Compute peuvent accéder aux données, aux services Active Directory et aux serveurs de licences locaux.
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) : crée une connexion privée entre les centres de données Microsoft et une infrastructure locale ou dans un environnement de colocalisation. ExpressRoute améliore la sécurité, la fiabilité, les débits et les latences par rapport aux connexions classiques sur Internet.
* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) : fournit plusieurs mécanismes pour permettre aux applications de communiquer ou d'échanger des données, qu’elles se trouvent sur Azure, sur une autre plateforme cloud ou dans un centre de données.

## <a name="next-steps"></a>Étapes suivantes
* Consultez l’article [Ressources techniques pour Batch et HPC](big-compute-resources.md) pour obtenir des conseils techniques afin de créer votre solution.
* Discutez de vos options Azure avec des partenaires, tels que Cycle Computing, Rescale et UberCloud.
* Découvrez les solutions Azure Big Compute fournies par [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) et [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).
* Pour les dernières annonces, consultez le [blog de l’équipe Microsoft HPC et Batch](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

