<properties
   pageTitle="Big Compute dans Azure : ressources techniques pour Batch et HPC (calculs complexes)"
   description="Cet article répertorie des ressources techniques pour vous aider à exécuter vos charges de travail à grande échelle en parallèle, par lots et HPC (calculs complexes) dans Azure."
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
   ms.date="04/28/2015"
   ms.author="danlep"/>

# Big Compute dans Azure : ressources techniques pour Batch et HPC (calculs complexes)
Il s'agit d'un guide de ressources techniques pour vous aider à exécuter vos charges de travail à grande échelle en parallèle, par lots et HPC (calculs complexes) dans Azure. Étendez vos charges de travail existantes HPC ou Batch dans le cloud Azure ou créez des solutions Big Compute dans Azure à l'aide d'une variété de services Azure.

## Options de solutions


Découvrez les options Big Compute dans Azure et choisissez l'approche adaptée à vos besoins professionnels et à votre charge de travail.

* [Vue d'ensemble](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Vidéo : Big Compute dans le cloud avec Azure et HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) (version préliminaire) est un service de plateforme qui permet d'adapter facilement vos applications au cloud et d'exécuter des tâches sans avoir à configurer ni gérer un planificateur de travail et de cluster. Utilisez le Kit de développement logiciel (SDK) pour intégrer les applications clientes à Azure Batch dans plusieurs langages, effectuer une copie intermédiaire des données dans Azure et générer des pipelines d'exécution du travail.

* S'inscrire à la [version préliminaire](http://azure.microsoft.com/services/preview/)

* [Documentation](http://azure.microsoft.com/documentation/services/batch/)

* [Informations de référence sur l'API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Forum Azure Batch](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Didacticiel : prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md)

* [Vidéo : présentation d'Azure Batch](http://azure.microsoft.com/documentation/videos/introduction-to-azure-batch-with-mark-scurrell/)

* [Vidéo : démonstration du service Azure Batch par Mark Russinovich](http://azure.microsoft.com/documentation/videos/azure-batch-demo-blender/)

* [Vidéo : créer un service Batch](http://azure.microsoft.com/documentation/videos/azure-batch-build-a-batch-service/)

## Solutions de cluster HPC

Migrez ou étendez votre cluster Windows HPC existant pour exécuter des charges de travail de calcul intensif dans Azure.

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) est le gestionnaire de cluster et la solution de planification des tâches gratuits de Microsoft pour les tâches HPC locales, hybrides et cloud.

* [Intégration à Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC Pack dans les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Didacticiel : configurer un cluster hybride avec HPC Pack dans Azure](cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Forums Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) est une implémentation Microsoft de la norme Message Passing Interface pour développer et exécuter des applications en parallèle sur la plateforme Windows.


* [Télécharger MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informations de référence sur MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instances de calcul intensif

Azure offre une [variété de tailles](https://msdn.microsoft.com/library/azure/dn197896.aspx), y compris des [instances A8, A9, A10 et A11](https://msdn.microsoft.com/library/azure/dn689095.aspx), pour exécuter vos charges de travail Batch et HPC.


* [Instances A8 et A9 : démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Exécuter des applications MPI sur des instances A8 et A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Plans d'architecture

* Le document [Calculs à grande échelle - Services financiers](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) montre comment effectuer et orchestrer les calculs et l'analyse de données à grande échelle dans le cloud pour la gestion des risques, la création de rapports et les simulations.

## Exemples et démonstrations

* [Exemples de code Azure Batch](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch)

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

<!---HONumber=58-->