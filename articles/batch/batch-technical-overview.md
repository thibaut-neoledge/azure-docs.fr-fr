---
title: "Azure Batch exécute des solutions de calcul parallèles à grande échelle dans le cloud | Microsoft Docs"
description: "Découvrez l’utilisation d’Azure Batch pour les charges de travail HPC et parallèles à grande échelle"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: a99f96db0c1e8bcd0cf29c564e5badf0eb728e56
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Exécuter des charges de travail intrinsèquement parallèles avec Batch

Azure Batch est une plateforme qui permet d’exécuter efficacement des applications de calcul haute performance (HPC) en parallèle et à grande échelle dans le cloud. Azure Batch planifie les travaux nécessitant une grande quantité de ressources système à exécuter sur une collection gérée de machines virtuelles. Il peut mettre automatiquement à l’échelle les ressources de calcul pour répondre aux besoins des tâches.

Avec le Azure Batch, vous définissez facilement des ressources de calcul Azure pour exécuter vos applications en parallèle et à grande échelle. Vous n’avez pas à créer, configurer et gérer manuellement un cluster HPC, des machines virtuelles individuelles, des réseaux virtuels ou une infrastructure complexe de planification des tâches et des travaux. Azure Batch automatise ou simplifie ces tâches.

## <a name="use-cases-for-batch"></a>Exemples d’utilisation du service Batch
Batch est un service Azure géré qui est utilisé pour le *traitement par lots* ou *Batch Computing*, et qui exécute un grand nombre de tâches similaires afin d’obtenir les résultats souhaités. Le traitement par lots est couramment utilisé dans les entreprises qui sont régulièrement amenées à traiter, transformer et analyser d’importants volumes de données.

Le service Batch fonctionne parfaitement avec les applications et charges de travail intrinsèquement parallèles (ou « massivement parallèles »). Les charges de travail intrinsèquement parallèles peuvent être facilement fractionnées en plusieurs tâches exécutées simultanément sur plusieurs ordinateurs.

![Tâches parallèles][1]<br/>

Voici quelques exemples de charges de travail communément traitées à l’aide de cette technique :

* Modélisation de risques financiers
* Analyse des données hydrologiques et météorologiques
* Rendu, analyse et traitement d’images
* Encodage et transcodage multimédia
* Analyse de séquence génétique
* Analyse des contraintes en ingénierie
* Test de logiciels

Batch peut également effectuer des calculs parallèles avec une opération de réduction à la fin, et des charges de travail HPC plus complexes telles que les applications [MPI (Interface de transfert de message)](batch-mpi.md) .

Pour obtenir une comparaison entre Batch et d’autres solutions HPC utilisées dans Azure, consultez [HPC, Batch, and Big Compute solutions using Azure VMs](../virtual-machines/linux/high-performance-computing.md) (Solutions HPC, Batch et Big Compute utilisant des machines virtuelles Azure).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Scénario : Montée en charge d’une charge de travail parallèle
Une solution courante qui utilise les API Batch pour interagir avec le service Batch suppose une montée en charge de travaux intrinsèquement parallèles (par exemple, le rendu d’images pour les scènes 3D) sur un pool de nœuds de calcul. Ce pool de nœuds de calcul peut être, par exemple, une « ferme de rendu » qui fournit des dizaines, des centaines voire des milliers de cœurs à votre travail de rendu.

Le schéma suivant illustre un flux de travail Batch courant, dans lequel une application cliente ou un service hébergé utilise Batch pour exécuter une charge de travail parallèle.

![Flux de travail de la solution Batch][2]

Dans ce scénario courant, votre application ou service traite une charge de travail de calcul dans Azure Batch en effectuant les opérations suivantes :

1. Chargement des **fichiers d’entrée** et de **l’application** qui traitera ces fichiers dans votre compte de stockage Azure. Les fichiers d’entrée peuvent correspondre à des données que votre application va traiter (par exemple, des données de modélisation financière ou des fichiers vidéo à transcoder). Les fichiers d’application peuvent être n’importe quelle application pour traiter les données, telle qu’une application de rendu 3D ou un transcodeur multimédia.
2. Création d’un **pool** de nœuds de calcul dans votre compte Batch : il s’agit des machines virtuelles qui exécuteront les tâches. Vous devez spécifier des propriétés, telles que la [taille du nœud](../cloud-services/cloud-services-sizes-specs.md), le système d’exploitation et l’emplacement d’installation de l’application dans Stockage Azure lorsque les nœuds rejoignent le pool (c’est-à-dire l’application que vous avez chargée à l’étape 1). Vous pouvez également configurer la [mise à l’échelle automatique](batch-automatic-scaling.md)du pool en réponse à la charge de travail que génèrent vos tâches. La mise à l’échelle automatique ajuste le nombre de nœuds de calcul dans le pool.
3. Création d’un **travail** Batch pour exécuter la charge de travail sur le pool de nœuds de calcul. Lorsque vous créez un travail, vous devez l’associer à un pool Batch.
4. Ajoutez des **tâches** au travail. Lorsque vous ajoutez des tâches à un travail, le service Batch planifie automatiquement l’exécution des tâches sur les nœuds de calcul du pool. Chaque tâche utilise l’application que vous avez téléchargée pour traiter les fichiers d’entrée.
   
   * 4a. Avant son exécution, la tâche peut charger les données (les fichiers d’entrée) qu’elle devra traiter dans le nœud de calcul auquel elle est affectée. Si l’application n’a pas déjà été installée sur le nœud (voir l’étape 2), vous pouvez la télécharger à cet emplacement. Lorsque les téléchargements sont terminés, les tâches s’exécutent sur les nœuds qui leur sont attribués.
5. Au cours de l’exécution des tâches, vous pouvez interroger le service Batch pour surveiller la progression du travail et des tâches associées. Votre application cliente ou le service communique avec le service Batch par le biais de HTTPS. Étant donné que vous surveillez peut-être des milliers de tâches qui s’exécutent sur des milliers de nœuds de calcul, veillez à [interroger le service Batch efficacement](batch-efficient-list-queries.md).
6. Une fois les tâches exécutées, elles peuvent chargent leurs données de sortie dans Azure Storage. Vous pouvez également extraire des fichiers directement du système de fichiers ou d’un nœud de calcul.
7. Lorsque votre analyse détecte la fin de l’exécution des tâches de votre travail, votre application cliente ou votre service peut télécharger les données de sortie afin de les traiter ou de les évaluer.

N’oubliez pas qu’il ne s’agit ici que d’une des nombreuses façons d’utiliser Batch et que ce scénario décrit seulement quelques-unes des fonctionnalités disponibles. Par exemple, vous pouvez exécuter [plusieurs tâches en parallèle](batch-parallel-node-tasks.md) sur chaque nœud de calcul et utiliser [les tâches de préparation et d’exécution de travaux](batch-job-prep-release.md) pour préparer les nœuds à l’exécution de vos travaux avant de les nettoyer.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous disposez d’une vue d’ensemble du service Batch, le moment est venu d’approfondir cette première approche pour découvrir comment utiliser ce service dans le traitement de charges de travail parallèles nécessitant beaucoup de ressources système.

* Consultez la [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). Elle contient des informations indispensables pour toute personne se préparant à l’utilisation de Batch. L’article contient des informations plus détaillées sur les ressources du service Batch telles que les pools, les nœuds, les travaux et les tâches, ainsi que sur les nombreuses fonctionnalités API que vous pouvez utiliser lors de la création de votre application Batch.
* Découvrez les [outils et API Batch](batch-apis-tools.md) disponibles pour créer des solutions Batch.
* [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) pour apprendre à utiliser C# et la bibliothèque .NET Batch pour exécuter une charge de travail simple à l’aide d’un flux de travail Batch courant. Cet article servira de référence essentielle dans la découverte de l’utilisation du service Batch. Il existe également une [version Python](batch-python-tutorial.md) du didacticiel.
* Téléchargez les [exemples de code sur GitHub][github_samples] pour voir comment C# et Python peuvent interagir avec Batch pour planifier et traiter des exemples de charge de travail.
* Consultez le [parcours d’apprentissage de Batch][learning_path] pour avoir une idée des ressources dont vous allez disposer dans votre découverte du service Batch.


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

