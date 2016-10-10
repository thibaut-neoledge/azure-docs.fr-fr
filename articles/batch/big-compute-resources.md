<properties
   pageTitle="Ressources pour les charges de travail par lots et HPC dans le cloud | Microsoft Azure"
   description="Répertorie des ressources techniques pour vous aider à exécuter vos charges de travail à grande échelle en parallèle, par lots et HPC (calcul haute performance) dans Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="09/22/2016"
   ms.author="danlep"/>

# Big Compute dans Azure : ressources techniques pour Batch et calcul haute performance 
Voici un guide des ressources techniques destiné à vous aider à exécuter vos charges de travail à grande échelle en parallèle, par lots et HPC (calcul haute performance) dans Azure. Étendez vos charges de travail existantes HPC ou Batch dans le cloud Azure ou créez des solutions Big Compute à l’aide d’une variété de services Azure.

## Options de solutions

Découvrez les options Big Compute dans Azure et choisissez l'approche adaptée à vos besoins professionnels et à votre charge de travail.

* [Solutions HPC et Batch](batch-hpc-solutions.md)

* [Vidéo : Big Compute dans le cloud avec Azure et HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](https://azure.microsoft.com/services/batch/) est un service de plateforme qui permet d’adapter facilement vos applications Linux et Windows au cloud et d’exécuter des tâches sans avoir à configurer ni gérer un planificateur de travail et de cluster. Utilisez le Kit de développement logiciel (SDK) pour intégrer les applications clientes à Azure Batch dans plusieurs langages, effectuer une copie intermédiaire des données dans Azure et générer des pipelines d’exécution du travail.

* [Documentation](https://azure.microsoft.com/documentation/services/batch/)

* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) et référence d’API [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)

* Référence de la [bibliothèque .NET de gestion Batch](https://msdn.microsoft.com/library/mt463120.aspx)

* Didacticiels : Mise en route de la [bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) et le [client Batch Python](batch-python-tutorial.md)

* [Forum Azure Batch](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=azurebatch)

* [Vidéos sur Azure Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Solutions de cluster HPC

Déployez ou étendez votre cluster HPC Windows ou Linux existant dans Azure pour exécuter des charges de travail nécessitant beaucoup de ressources système.

### Microsoft HPC Pack

HPC Pack est la solution HPC gratuite de Microsoft construite autour des technologies Microsoft Azure et Windows Server, capable d'exécuter des charges de travail HPC Windows et Linux.

* [Téléchargez la mise à jour 3 de HPC Pack 2012 R2](https://www.microsoft.com/download/details.aspx?id=49922)

* [Documentation](https://technet.microsoft.com/library/jj899572.aspx)


* Options de cluster [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) et [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) HPC Pack dans Azure

* [Intégration à des instances de travail Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Intégration à Azure Batch avec HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Forums Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Solutions de cluster Linux et OSS

Ces modèles Azure permettent de déployer des clusters HPC Linux.

* [Spin up a SLURM cluster](https://azure.microsoft.com/documentation/templates/slurm/) et [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Spin up a Torque cluster](https://azure.microsoft.com/documentation/templates/torque-cluster/)

* [Intel Cloud Edition for Lustre Software - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) est une implémentation Microsoft de la norme Message Passing Interface pour développer et exécuter des applications en parallèle sur la plateforme Windows. La version la plus récente est MS-MPI v7.


* [Télécharger MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informations de référence sur MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum MPI](https://social.microsoft.com/Forums/fr-FR/home?forum=windowshpcmpi)

## Instances nécessitant beaucoup de ressources système

Azure propose [plusieurs tailles de machines virtuelles](../virtual-machines/virtual-machines-windows-sizes.md), y compris des instances [nécessitant beaucoup de ressources système](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) et capables de se connecter à un réseau RDMA principal pour exécuter vos charges de travail HPC Linux et Windows.


* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)

* [Configuration d'un cluster Windows RDMA avec Microsoft HPC Pack pour exécuter des applications MPI](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)



## Exemples et démonstrations

* [Exemples de code C# et Python Azure Batch](https://github.com/Azure/azure-batch-samples)

* Kit de ressources [Batch Shipyard](https://azure.github.io/batch-shipyard/) pour faciliter le déploiement de charges de travail Dockerized de type batch

* [Test de SUSE Linux Enterprise Server pour HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## Services Azure connexes

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)

* [Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/)

* [App Service](https://azure.microsoft.com/documentation/services/app-service/)

* [Media Services](https://azure.microsoft.com/documentation/services/media-services/)

* [Fonctions](https://azure.microsoft.com/documentation/services/functions/)

## Plans d'architecture

* [HPC et orchestration de données à l’aide des services Azure Batch et Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) et [article](../data-factory/data-factory-data-processing-using-batch.md)

## Solutions métier

* [Banques et marchés financiers](https://finance.azure.com/)

* [Simulations techniques](https://simulation.azure.com/)

## Témoignages client

* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168)

* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)

* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)

* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)

* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)

* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)

* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)

* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



## Étapes suivantes

* Pour les dernières annonces, consultez le [blog de l'équipe Microsoft HPC et Batch](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Consultez également les [nouveautés de Batch](https://azure.microsoft.com/updates/?service=batch) ou abonnez-vous au [flux RSS](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0928_2016-->