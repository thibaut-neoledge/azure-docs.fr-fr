---
title: "Présentation de R Server sur Azure HDInsight | Microsoft Docs"
description: "Consultez une présentation de R Server sur HDInsight. Découvrez comment utiliser R Server pour créer des applications pour l’analyse du Big Data."
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: cd57f3a43142b3af3546eafd9749123fadd333c2
ms.openlocfilehash: 2f5cf1155f116b22fa28cf86bb8c87667df981b7


---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introduction à R Server et aux fonctionnalités Open Source R sur HDInsight

Avec Microsoft Azure HDInsight, Microsoft R Server est désormais disponible en tant qu’option lorsque vous créez des clusters HDInsight dans Azure. Cette nouvelle fonctionnalité offre aux experts en science des données, aux statisticiens et aux programmeurs R un accès à la demande à des méthodes d’analyse extensibles et distribuées sur HDInsight.

Les clusters peuvent être dimensionnés en fonction des projets et des tâches en cours, puis détruits lorsque vous n’en avez plus besoin. Étant donné que ces clusters font partie d’Azure HDInsight, ils bénéficient d’un support technique de niveau entreprise 24 heures sur 24, 7 jours sur 7, d’un contrat SLA garantissant 99,9 % de temps de fonctionnement, et de la flexibilité d’intégration avec d’autres composants de l’écosystème Azure.

R Server sur HDInsight fournit les dernières fonctionnalités d’analyse basée sur R sur des jeux de données de toutes tailles, chargés dans un stockage de type Azure Blob ou Data Lake. Étant donné que R Server est basé sur du code R open source, les applications R que vous créez peuvent exploiter les plus de 8 000 packages open source disponibles, ainsi que les routines de ScaleR, package d’analyse du Big Data de Microsoft qui est inclus dans R Server.

Le nœud de périmètre d’un cluster fournit un lieu d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud de périmètre vous permet d’exécuter des fonctions distribuées parallélisées de ScaleR sur les cœurs du serveur de nœud de périmètre. Vous avez également la possibilité de les exécuter sur les nœuds du cluster via l’utilisation des contextes de calcul Hadoop Map Reduce ou Spark de ScaleR.

Les modèles ou prévisions résultant des analyses peuvent être téléchargés pour une utilisation locale. Ils peuvent également être mis en œuvre ailleurs dans Azure, par exemple via un [service web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-on-hdinsight"></a>Prise en main de R sur HDInsight
Pour inclure R Server dans un cluster HDInsight, vous devez sélectionner le type de cluster R Server lors de la création d’un cluster HDInsight via le portail Azure. Le type de cluster R Server inclut R Server sur les nœuds de données d’un cluster, et un nœud de périmètre comme zone d’accueil pour l’analyse basée sur R Server. Pour une description détaillée de la création d’un cluster, voir [Prise en main de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md) .

## <a name="learn-about-data-storage-options"></a>En savoir plus sur les options de stockage de données
Le stockage par défaut du système de fichiers HDFS de clusters HDInsight peut être associé à un compte Azure Storage ou à un magasin Azure Data Lake. Cela garantit que toutes les données chargées dans le stockage en cluster au cours de l’analyse sont rendues persistantes. Il existe différents outils pour transférer les données à l’option de stockage que vous sélectionnez, notamment la fonctionnalité de téléchargement basée sur le portail du compte de stockage et l'utilitaire [AzCopy](../storage/storage-use-azcopy.md).

Que vous choisissiez un stockage d’objets Azure Blob ou Data Lake comme stockage principal de votre cluster, vous avez la possibilité d’ajouter un accès aux objets blob supplémentaires et aux magasins Data Lake pendant le processus de configuration de cluster. Consultez [Prise en main de R Server sur HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-get-started) pour plus d’informations sur l’ajout de l’accès à des comptes supplémentaires, et l'article supplémentaire [Options d’Azure Storage pour R Server sur HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-storage) pour en savoir plus sur l’utilisation de plusieurs comptes de stockage.

Vous pouvez également utiliser [Azure Files](../storage/storage-how-to-use-files-linux.md) comme option de stockage pour une utilisation sur le nœud de périphérie. Le service Azure Files vous permet de monter un partage de fichiers qui a été créé dans un compte Azure Storage sur le système de fichiers Linux. Pour plus d’informations concernant les options de stockage de données pour R Server sur un cluster HDInsight, voir les [options d'Azure Storage pour R Server sur des clusters HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Accéder à R Server sur le cluster
Une fois que vous avez créé un cluster avec R Server, vous pouvez vous connecter à R Server sur le nœud de périmètre à l’aide d’un navigateur si vous avez choisi d’inclure RStudio Server pendant le processus de configuration, l'avez ajouté ultérieurement, ou si vous utilisez SSH/PuTTY pour accéder à la console R. Pour plus d’informations sur l’installation de RStudio Server après la création d'un cluster, voir [Installation de RStudio avec R Server sur HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Développer et exécuter des scripts R
Les scripts R que vous créez et exécutez peuvent utiliser les plus de 8 000 packages open source disponibles, en plus des routines distribuées et parallélisées de la bibliothèque ScaleR. En général, un script exécuté avec R Server sur le nœud de périmètre s’exécute au sein de l’interpréteur R sur ce nœud. à l’exception des étapes qui appellent une fonction ScaleR avec un contexte de calcul défini sur Hadoop MapReduce (RxHadoopMR) ou Spark (RxSpark).

Dans ces cas, la fonction s’exécute en mode distribué sur les nœuds de données (tâche) du cluster qui sont associés aux données référencées. Pour plus d’informations concernant les différentes options de contexte de calcul, voir [Options de contexte de calcul pour R Server sur HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Faire fonctionner un modèle
Lorsque votre modélisation des données est terminée, vous pouvez mettre en œuvre le modèle pour effectuer des prévisions sur de nouvelles données dans Azure et localement. Ce processus est appelé notation. Voici quelques exemples :

### <a name="score-in-hdinsight"></a>Noter dans HDInsight
Pour noter dans HDInsight, écrivez une fonction R qui appelle votre modèle pour effectuer des prévisions pour un nouveau fichier de données que vous avez chargé sur votre compte de stockage. Ensuite, enregistrez les prévisions dans le compte de stockage. Vous pouvez exécuter la routine à la demande sur le nœud de périmètre de votre cluster ou à l’aide d’une tâche planifiée.  

### <a name="score-in-azure-machine-learning"></a>Noter dans Azure Machine Learning
Pour noter à l’aide d’un service web Azure Machine Learning, utilisez le package R Azure Machine Learning open source, également appelé [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) , pour publier votre modèle en tant que service web Azure. Pour plus de commodité, ce package est déjà installé sur le nœud de périphérie. Ensuite, utilisez les fonctionnalités d’Azure Machine Learning pour créer une interface utilisateur pour le service web, puis appelez le service web en fonction des besoins de notation.

Si vous choisissez cette option, vous allez devoir convertir les objets de modèle ScaleR en objets de modèle open source équivalents pour une utilisation avec le service web. Cela est possible en utilisant les fonctions de forçage de ScaleR, telles que `as.randomForest()` pour les modèles basés sur un ensemble.

### <a name="score-on-premises"></a>Noter localement
Pour noter localement après la création de votre modèle, vous pouvez sérialiser le modèle dans R, le télécharger, le désérialiser, puis l’utiliser pour noter de nouvelles données. Vous pouvez noter les nouvelles données à l’aide de l’approche décrite précédemment dans [Noter dans HDInsight](#scoring-in-hdinsight) ou en utilisant [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Maintenance du cluster
### <a name="install-and-maintain-r-packages"></a>Installation et maintenance des packages R
La plupart des packages R que vous utilisez seront requis sur le nœud de périmètre, car la majeure partie des étapes de vos scripts R y sera exécutée. Pour installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser la méthode `install.packages()` habituelle dans R.

Dans la plupart des cas, vous n’aurez pas besoin d’installer des packages R supplémentaires sur les nœuds de données si vous utilisez simplement des routines de la bibliothèque ScaleR au sein du cluster. Toutefois, il se peut que vous ayez besoin de packages supplémentaires pour prendre en charge l’utilisation de **rxExec** ou l’exécution de **RxDataStep** sur les nœuds de données.

Dans de tels cas, les packages supplémentaires peuvent être installés à l’aide d’une action de script après la création du cluster. Pour plus d’informations, consultez la section [Création d’un cluster HDInsight avec R Server](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Modifier les paramètres de mémoire de Hadoop MapReduce
Vous pouvez modifier un cluster afin de changer la quantité de mémoire disponible pour R Server lors de l’exécution d’un travail MapReduce. Pour modifier un cluster, utilisez l’interface utilisateur Apache Ambari qui est disponible via le panneau du portail Azure pour votre cluster. Pour obtenir des instructions concernant l’accès à l’interface utilisateur d’Ambari pour votre cluster, consultez la section [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).

Vous pouvez également modifier la quantité de mémoire disponible pour R Server en utilisant des commutateurs Hadoop dans l’appel à **RxHadoopMR** comme suit :

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Mettre à l’échelle votre cluster
Vous pouvez modifier l’échelle d’un cluster existant via le portail. Grâce à la mise à l’échelle, vous pouvez obtenir la capacité supplémentaire éventuellement nécessaire pour les tâches de traitement plus importantes, ou rétablir l’échelle d’un cluster quand il est inactif. Pour obtenir des instructions concernant la mise à l’échelle d’un cluster, consultez la section [Gérer des clusters HDInsight](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Maintenance du système
La maintenance est effectuée sur les machines virtuelles Linux sous-jacentes dans un cluster HDInsight pendant les heures creuses pour appliquer des correctifs de système d’exploitation ainsi que d’autres mises à jour. En règle générale, la maintenance s’effectue à 3 h 30 (en fonction de l’heure locale de la machine virtuelle) tous les lundis et jeudis. Les mises à jour sont effectuées de façon à ne pas avoir d’incidence sur plus d’un quart du cluster à la fois.  

Étant donné que les nœuds principaux sont redondants et que certains nœuds de données ne sont pas affectés, toutes les tâches en cours d’exécution pendant ce temps peuvent être ralenties. Elles doivent cependant être exécutées jusqu’à la fin. L’ensemble des logiciels personnalisés ou des données locales que vous détenez sont conservés durant ces événements de maintenance, sauf si une défaillance irrémédiable se produit, nécessitant une reconstruction du cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>En savoir plus sur les options IDE pour R Server sur un cluster HDInsight
Le nœud de périmètre Linux d’un cluster HDInsight est la zone d’accueil pour l’analyse basée sur R.  Les versions récentes de HDInsight fournissent une option par défaut pour l’installation de la version communautaire de [RStudio Server](https://www.rstudio.com/products/rstudio-server/) sur le nœud de périmètre comme un IDE basé sur navigateur. L'utilisation de RStudio Server comme IDE pour le développement et l’exécution de scripts R peuvent être considérablement plus productifs que la simple utilisation de la console R. Si vous choisissez de ne pas ajouter RStudio Server lors de la création du cluster mais souhaitez l’ajouter ultérieurement, consultez [Installation de R Studio Server sur des clusters HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-install-r-studio).+

Une autre option IDE complète consiste à installer un IDE de bureau comme les outils récemment annoncés par Microsoft, [R Tools for Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), RStudio ou [StatET](http://www.walware.de/goto/statet) basé sur Eclipse de Walware, puis d'accéder au cluster via l’utilisation d’un contexte de calcul Map Reduce ou Spark à distance.  

Enfin, vous pouvez simplement accéder à la console R Server sur le nœud de périmètre en tapant **R** à l’invite de commande Linux après vous être connecté via SSH ou PuTY. L’utilisation de l’interface de la console est améliorée si vous exécutez un éditeur de texte pour le développement de script R dans une autre fenêtre, et coupez et collez des sections de votre script dans la console R en fonction des besoins.

## <a name="learn-about-pricing"></a>En savoir plus sur la tarification
Les frais associés à un cluster HDInsight avec R Server sont structurés de manière similaire à ceux applicables à des clusters HDInsight standard. Ils sont basés sur le dimensionnement des machines virtuelles sous-jacentes pour le nom, les données et les nœuds de périmètre, avec l’ajout d’une extension pendant les heures normales. Pour plus d’informations concernant la tarification de HDInsigh et la disponibilité de la version d’évaluation gratuite de 30 jours, consultez la section [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Étapes suivantes
Suivez les liens ci-dessous pour en savoir plus sur l’utilisation de R Server avec des clusters HDInsight.

* [Prise en main de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md)
* [Ajouter RStudio Server à HDInsight (s’il n’est pas installé lors de la création du cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Options d’Azure Storage pour R Server sur HDInsight](hdinsight-hadoop-r-server-storage.md)



<!--HONumber=Jan17_HO2-->


