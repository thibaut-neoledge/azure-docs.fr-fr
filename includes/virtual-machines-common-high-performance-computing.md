Les organisations ont des besoins informatiques à grande échelle. Ces problèmes informatiques à grande échelle comprennent l’analyse et la conception d’ingénierie, les calculs de risques financiers, le rendu d’image, la modélisation complexe, les simulations Monte Carlo, etc. 

Azure permet d’exécuter des charges de travail de calcul haute performance et par lors sur des clusters de machines virtuelles, à l’aide de votre sélection de services d’infrastructure Azure ou de services gérés. Azure vous offre la possibilité d’augmenter le nombre de ressources de calcul pour le faire passer à des milliers de machines virtuelles ou de cœurs et de le réduire lorsque vous avez besoin de moins de ressources. 

Les solutions de calcul haute performance Azure exécutent efficacement des charges de travail calcul Linux et Windows nécessitant beaucoup de ressources système, allant de travaux par lots parallèles à des simulations de calcul haute performance classiques. Les applications de calcul haute performance sont prises en charge pour s’exécuter sur des machines virtuelles Azure. Vous pouvez, par ailleurs tirer parti des tailles et des images de machines virtuelles spécialisées conçues pour les travaux nécessitant beaucoup de ressources système. 


## <a name="solution-architectures"></a>Architectures de solution

Voici des exemples d’architectures de solutions nécessitant beaucoup de ressources système dans Azure :

* Exécuter des applications HPC sur des machines virtuelles ou des[groupes de machines virtuelles identiques](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), à l’aide d’un gestionnaire de cluster Linux ou Windows ou d’un gestionnaire de grilles de votre choix [En savoir plus](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/)
* Créer des solutions hybrides qui étendent un cluster HPC local pour décharger les charges de travail de pointe vers des machines virtuelles Azure [En savoir plus](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/)
* Utiliser le service Azure [Batch](https://azure.microsoft.com/services/batch/) géré et évolutif pour exécuter des charges de travail nécessitant beaucoup de ressources système sans gérer d’infrastructures sous-jacentes [En savoir plus](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)

## <a name="hpc-cluster-and-grid-solutions"></a>Solutions de grille et de cluster HPC
Déployez ou étendez des outils de gestion des charges de travail HPC familiers sur des machines virtuelles Azure et exécutez des charges de travail nécessitant beaucoup de ressources système. Parmi les options figurent des outils open source, tels que le [gestionnaire de charge de travail Slurm](https://slurm.schedmd.com/)et les gestionnaires de grille ou de cluster de Microsoft et d’autres éditeurs.

### <a name="linux-and-oss-cluster-solutions"></a>Solutions de cluster Linux et OSS
Utilisez des modèles Azure Resource Manager pour déployer des solutions de cluster HPC Linux sur des machines virtuelles ou des groupes de machines virtuelles identiques, y compris :

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

Consultez également la collection des [modèles en 5 clics](https://github.com/tanewill/5clickTemplates).

### <a name="grid-managers"></a>Gestionnaires de grille
Microsoft travaille en partenariat avec des gestionnaires de grille commerciaux pour mettre leurs solutions à disposition sur des machines virtuelles Azure. Voici quelques exemples :

* [IBM Spectrum Symphony and Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
[HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) est la solution HPC gratuite de Microsoft basée sur les technologies Microsoft Azure et Windows Server. Elle offre plusieurs options pour s’exécuter sur des machines virtuelles [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Découvrez comment :

* [Créer un cluster HPC Pack 2016 dans Azure](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [Migrer vers Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx)




## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) est une plateforme qui permet d’exécuter efficacement des applications de calcul haute performance (HPC) en parallèle et à grande échelle dans le cloud. Azure Batch planifie les travaux nécessitant une grande quantité de ressources système à exécuter sur un pool géré de machines virtuelles. Il peut mettre automatiquement à l’échelle les ressources de calcul pour répondre aux besoins de vos travaux. 

Utilisez les outils et kits de développement logiciel (SDK) pour intégrer des applications HPC ou des charges de travail de conteneur dans Azure, stocker des données dans Azure et générer des pipelines d’exécution du travail. Vous pouvez également utiliser la capacité excédentaire de votre machine virtuelle Azure (priorité basse) à un [prix réduit](https://azure.microsoft.com/pricing/details/batch/), ce qui réduit considérablement le coût d’exécution de certaines charges de travail dans Batch.

Découvrez comment :

* [Commencer à développer avec Batch](../articles/batch/batch-dotnet-get-started.md)
* [Utiliser les exemples de code Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Utiliser des machines virtuelles de faible priorité avec Batch (préversion)](../articles/batch/batch-low-pri-vms.md)
* [Exécuter des charges de travail HPC en conteneur avec Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Utiliser le langage R avec Batch](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>Tailles de machines virtuelles HPC et GPU
Azure propose une plage de tailles de machines virtuelles [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), y compris des tailles conçues pour des charges de travail nécessitant beaucoup de ressources système. Par exemple, des machines virtuelles H16r et H16mr peuvent se connecter à un réseau RDMA principal au débit élevé. Ce réseau cloud peut améliorer les performances d’applications parallèles étroitement liées s’exécutant sous [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) ou Intel MPI. Les machines virtuelles de série N comportent des processeurs graphiques NVIDIA conçus pour des applications graphiques ou de calcul nécessitant beaucoup de ressources système, y compris la visualisation et l’apprentissage de l’intelligence artificielle (AI). 

En savoir plus :

* Tailles HPC pour machines virtuelles [Linux](../articles/virtual-machines/linux/sizes-hpc.md) et [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Tailles GPU pour machines virtuelles [Linux](../articles/virtual-machines/linux/sizes-gpu.md) et [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

Découvrez comment :

* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configuration d’un cluster Windows RDMA avec Microsoft HPC Pack pour exécuter des applications MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Utiliser des machines virtuelles nécessitant beaucoup de ressources système dans des pools Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>Images HPC

Visitez la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/) pour les images de machines virtuelles Linux et Windows conçues pour HPC. Voici quelques exemples :

* [HPC basé sur RogueWave CentOS](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server (SLES)](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Machine virtuelle de science des données Azure pour Windows et Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>Applications HPC

Exécuter des applications HPC commerciales ou personnalisées dans Azure. Plusieurs ont été testées et se montrent efficaces pour la mise à l’échelle avec des machines virtuelles ou des cœurs de calcul supplémentaires. Les sections suivantes sont des exemples.

> [!IMPORTANT]
> Vérifiez auprès du fournisseur de toute application commerciale les questions de licence ou toute autre restriction relative à l’exécution dans le cloud. Tous les fournisseurs ne proposent pas le paiement à l'utilisation pour les licences. Vous aurez peut-être besoin d’un serveur de licences dans le cloud pour votre solution ou de vous connecter à un serveur de licences sur site.

### <a name="engineering-applications"></a>Applications d’ingénierie


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Graphiques et rendu

* [Autodesk Maya, 3ds Max et Arnold](../articles/batch/batch-rendering-service.md) sur Azure Batch (préversion)

### <a name="ai-and-deep-learning"></a>Intelligence artificielle et apprentissage approfondi

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Kit d’apprentissage approfondi pour machine virtuelle de science des données](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Recettes Batch Shipyard pour l’apprentissage approfondi](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>Partenaires de solutions

Les partenaires qui développent ou proposent des applications HPC ou Batch dans Azure sont les suivants :

* [Cycle Computing](https://cyclecomputing.com/) (maintenant [joint à Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>Stockage HPC

Les charges de travail HPC et Batch à grande échelle nécessitent un stockage des données et un accès à ces dernières dépassant les capacités des systèmes de fichiers cloud classiques. Vous pouvez implémenter des solutions de système de fichiers parallèles dans Azure comme [Lustre](http://lustre.org/) et [BeeGFS](http://www.beegfs.com/content/).

En savoir plus :

* [Systèmes de fichiers parallèles pour le stockage HPC sur Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>Services Azure connexes

La plupart des solutions HPC Azure reposent sur les machines virtuelles Azure, les groupes de machines virtuelles identiques, Batch et les services de calcul associés. Toutefois, votre solution peut bénéficier de nombreux services Azure associés. En voici la liste non-exhaustive.

### <a name="storage"></a>Storage

* [Stockage Objet BLOB, Table et File d’attente](../articles/storage/storage-introduction.md)
* [Stockage Fichier](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Données et analyse
* [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md) pour les clusters Hadoop sur Azure
* [Data Factory](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [Base de données SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>Mise en réseau
* [Réseau virtuel](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Conteneurs
* [Service de conteneur](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Témoignages client

Voici des exemples de clients ayant résolu des problèmes d’entreprise à l’aide de solutions Azure HPC :

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les solutions Big Compute pour la [simulation d’ingénierie](https://simulation.azure.com/), le [rendu](https://simulation.azure.com/), et les[banques et marchés financiers](https://finance.azure.com/).
* Pour les dernières annonces, consultez le [blog de l’équipe Microsoft HPC et Batch](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](https://azure.microsoft.com/blog/tag/hpc/).


