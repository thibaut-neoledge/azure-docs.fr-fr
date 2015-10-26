<properties
   pageTitle="Big Compute : ressources techniques pour Batch et HPC (calcul haute performance) | Microsoft Azure"
   description="Répertorie des ressources techniques pour vous aider à exécuter vos charges de travail à grande échelle en parallèle, par lots et HPC (calculs complexes) dans Azure."
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
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Big Compute dans Azure : ressources techniques pour Batch et HPC (calcul haute performance)
Il s'agit d'un guide de ressources techniques pour vous aider à exécuter vos charges de travail à grande échelle en parallèle, par lots et HPC (calculs complexes) dans Azure. Étendez vos charges de travail existantes HPC ou Batch dans le cloud Azure ou créez des solutions Big Compute dans Azure à l'aide d'une variété de services Azure.

## Options de solutions

Découvrez les options Big Compute dans Azure et choisissez l'approche adaptée à vos besoins professionnels et à votre charge de travail.

* [Solutions HPC et Batch](batch-hpc-solutions.md)

* [Vidéo : Big Compute dans le cloud avec Azure et HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) est un service de plateforme qui permet d'adapter facilement vos applications au cloud et d'exécuter des tâches sans avoir à configurer ni gérer un planificateur de travail et de cluster. Utilisez le Kit de développement logiciel (SDK) pour intégrer les applications clientes à Azure Batch dans plusieurs langages, effectuer une copie intermédiaire des données dans Azure et générer des pipelines d'exécution du travail.

* [Documentation](http://azure.microsoft.com/documentation/services/batch/)

* [Informations de référence sur l'API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Didacticiel : prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md)

* [Forum Azure Batch](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Vidéos sur Azure Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Solutions de cluster HPC

Déployez ou étendez votre cluster HPC Windows ou Linux existant dans Azure pour exécuter des charges de travail nécessitant beaucoup de ressources système.

### Microsoft HPC Pack

HPC Pack est la solution HPC gratuite de Microsoft construite autour des technologies Microsoft Azure et Windows Server, capable d'exécuter des charges de travail HPC Windows et Linux.

* [Téléchargez la mise à jour 2 de HPC Pack 2012 R2](https://www.microsoft.com/fr-FR/download/details.aspx?id=47755)

* [Documentation](https://technet.microsoft.com/library/jj899572.aspx)


* [Options de cluster HPC avec Microsoft HPC Pack dans Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Intégration à Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)


* [Forums Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Solutions de cluster Linux et OSS

Utilisez ces modèles de démarrage rapide Azure pour déployer des clusters HPC Linux avec des outils open source.

* [Spin up a SLURM cluster](http://azure.microsoft.com/documentation/templates/slurm/) et [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Spin up a Torque cluster](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) est une implémentation Microsoft de la norme Message Passing Interface pour développer et exécuter des applications en parallèle sur la plateforme Windows.


* [Télécharger MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informations de référence sur MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instances de calcul intensif

Azure propose [plusieurs tailles](../virtual-machines/virtual-machines-size-specs.md), y compris des [instances A8, A9, A10 et A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) nécessitant beaucoup de ressources système pour exécuter vos charges de travail HPC Linux et Windows.

* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Configuration d'un cluster Windows RDMA avec Microsoft HPC Pack pour exécuter des applications MPI](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Plans d'architecture

* Le document [Calculs à grande échelle - Services financiers](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) montre comment effectuer et orchestrer les calculs et l'analyse de données à grande échelle dans le cloud pour la gestion des risques, la création de rapports et les simulations.

## Exemples et démonstrations

* [Exemples de code Azure Batch](https://github.com/Azure/azure-batch-samples)

* [Exemple de Batch Apps Blender](https://github.com/Azure/azure-batch-apps-blender) et [billet de blog](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Services Azure connexes

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Cloud Services](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Media Services](http://azure.microsoft.com/documentation/services/media-services/)



## Étapes suivantes

* Pour les dernières annonces, consultez le [blog de l'équipe Microsoft HPC et Batch](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](http://azure.microsoft.com/blog/tag/hpc/).
* Consultez également les [nouveautés de Batch](http://azure.microsoft.com/updates/?service=batch) ou abonnez-vous au [flux RSS](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=Oct15_HO3-->