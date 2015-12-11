<properties
	pageTitle="Concepts de base d’Azure Batch Service | Microsoft Azure"
	description="Découvrez les concepts, les flux de travail et les scénarios du service Azure Batch pour les charges de travail HPC et parallèle à grande échelle"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/19/2015"
	ms.author="danlep"/>

# Notions de base d’Azure Batch

Azure Batch vous permet d'exécuter des applications de calculs complexes parallèles à grande échelle efficacement dans le cloud. Il s’agit d’un service de plateforme qui planifie les travaux nécessitant beaucoup de ressources système à exécuter sur une collection gérée de machines virtuelles (nœuds de calcul) et peut mettre à l’échelle les ressources de calcul pour répondre aux besoins du travail. En utilisant le service Batch, vous pouvez définir par programme des ressources de calcul Azure et des travaux par lots à grande échelle qui s’exécutent à la demande ou selon une planification, sans que vous deviez manuellement configurer et gérer un cluster HPC, des machines virtuelles spécifiques, des réseaux virtuels ou un planificateur de travaux.

## Cas d'utilisation

Batch est un service géré pour le *traitement par lots* ou le *Batch Computing* et l’exécution d’un grand nombre de tâches similaires afin d’obtenir les résultats souhaités. Batch Computing est un modèle courant pour les organisations qui traitent, transforment et analysent de grandes quantités de données, de manière planifiée ou à la demande, dans des domaines tels que les services financiers et l’ingénierie.

Le service Batch fonctionne bien avec des applications ou charges de travail intrinsèquement parallèles, qui se prêtent à l’exécution de tâches en parallèle sur plusieurs ordinateurs. Voir la Figure 1.

![Tâches parallèles][parallel]

**Figure 1. Tâches parallèles exécutées sur plusieurs ordinateurs**

Voici quelques exemples :

* Modélisation de risques financiers
* Rendu et traitement d'images
* Encodage et transcodage multimédia
* Analyse de séquence génétique
* Test de logiciels

Batch permet également d’effectuer des calculs parallèles avec une étape de réduction à la fin et d’autres charges de travail parallèles plus compliquées.

>[AZURE.NOTE] À ce stade, Batch prend uniquement en charge les charges de travail qui s’exécutent sur des machines virtuelles Windows Server. De plus, Batch ne prend pas actuellement en charge les applications MPI (Message Passing Interface).

Pour une comparaison de Batch avec d’autres solutions HPC dans Azure, consultez la page [Solutions Batch et HPC](batch-hpc-solutions.md).

## Développement avec Batch

Développez avec les API Batch pour créer et gérer les pools de nœuds de calcul et planifier les travaux et les tâches qui s’y exécutent. Écrivez des applications clientes ou frontales pour exécuter des travaux et des tâches à la demande, selon une planification ou dans le cadre d’un plus grand flux de travail géré par des outils tels qu’[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

Consultez [Présentation de la fonction Azure Batch](batch-api-basics.md) pour en savoir plus sur les concepts de Batch.

### Comptes dont vous aurez besoin

+ **Compte et abonnement Azure** : si vous ne possédez pas de compte, vous pouvez activer les [avantages de votre abonnement MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou obtenir un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/).

+ **Compte Batch** : utilisez le nom et l’URL d’un compte Batch et une clé d’accès comme informations d’identification lorsque vous effectuez des appels de l’API Batch. Toutes vos ressources Batch, telles que les nœuds de calcul, les pools, les travaux et les tâches, sont associées à un compte Batch. Une des méthodes qui permet de créer un compte Batch et de gérer les clés d’accès pour le compte consiste à utiliser le [portail Azure](batch-account-create-portal.md).

+ **Compte de stockage** : pour la plupart des scénarios Batch, vous aurez besoin d’un compte de stockage Azure pour stocker vos entrées et sorties de données et les scripts ou exécutables qui s’exécutent sur les nœuds de calcul. Pour créer un compte de stockage, consultez l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

### Bibliothèques et outils de développement pour Batch

Utilisez ces bibliothèques et outils .NET avec Visual Studio pour développer et gérer des applications Azure Batch.

+ [Bibliothèque .NET cliente Batch (NuGet)](http://www.nuget.org/packages/Azure.Batch/) : développez des applications clientes pour exécuter des travaux à l’aide du service Batch
+ [Bibliothèque .NET de gestion Batch (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) : gérez les comptes Batch
+ [Batch Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) : générez cette application GUI permettant de parcourir, d’utiliser et de mettre à jour les ressources d’un compte Batch, y compris les travaux et les tâches, les pools et nœuds de calcul et les fichiers sur un nœud de calcul. Consultez ce [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Scénario : Montée en charge d’une charge de travail parallèle

Un scénario courant d’utilisation des API Batch implique la montée en charge d’un travail intrinsèquement parallèle comme le rendu d’image sur un pool de nœuds de calcul fournissant jusqu’à plusieurs milliers de cœurs de calcul.

La figure 2 illustre un flux de travail qui utilise une application cliente .NET Batch pour exécuter une charge de travail parallèle avec Batch.


![Flux de travail des éléments de travail][work_item_workflow]

**Figure 2 : Montée en charge d’une charge de travail parallèle sur Batch**

1.	Téléchargez les fichiers d'entrée (comme données ou images sources) nécessaires à un travail dans un compte de stockage Azure. Le service Batch charge des fichiers sur des nœuds de calcul lors de l’exécution du travail.

2.	Téléchargez les fichiers programmes ou les scripts qui s’exécuteront sur les nœuds de calcul pour le compte de stockage. Ceux-ci peuvent inclure des fichiers binaires et les assemblys dépendants. Le service Batch charge également ces fichiers sur des nœuds de calcul lors de l’exécution des tâches.

3.	Créez par programme un pool de nœuds de calcul Batch dans votre compte Batch, en spécifiant les propriétés comme la taille et le système d’exploitation exécuté. Vous pouvez également définir l’[augmentation ou la diminution du nombre](batch-automatic-scaling.md) de nœuds dans le pool en réponse à la charge de travail. Quand une tâche s’exécute, elle reçoit un nœud à partir de ce pool.

4.	Définissez par programme un travail Batch pour exécuter la charge de travail sur le pool de nœuds.

5.	Ajoutez des tâches au travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé. En fonction de votre charge de travail, vous pouvez exécuter [plusieurs tâches en même temps](batch-parallel-node-tasks.md) sur chaque nœud de calcul afin d’optimiser l’utilisation des ressources. Batch prend également en charge les [tâches spécialisées de préparation et d’achèvement des travaux](batch-job-prep-release.md) pour préparer les nœuds de calcul avant l’exécution des tâches planifiées et pour le nettoyage qui suivra.

6.	Exécutez la charge de travail Batch et surveillez la progression et les résultats. L’application communique via le protocole HTTPS avec le service Batch. Pour améliorer les performances de l’application lors de l’analyse d’un grand nombre de pools, de travaux et de tâches, tirez parti de [techniques efficaces d’interrogation](batch-efficient-list-queries.md) du service Batch.






## Étapes suivantes

* Prise en main du développement de votre première application avec la [bibliothèque .NET cliente Batch](batch-dotnet-get-started.md)
* Découverte des exemples de code Batch sur [Github](https://github.com/Azure/azure-batch-samples)

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=AcomDC_1203_2015-->