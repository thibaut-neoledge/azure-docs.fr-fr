<properties
	pageTitle="Qu’est-ce que R sur HDInsight ? Présentation de R Server sur HDInsight | Microsoft Azure"
	description="Qu’est-ce que R sur HDInsight et comment utiliser R pour créer des applications pour l’analyse du Big Data."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="jeffstok"/>


# Vue d’ensemble : R Server sur HDInsight

Avec HDInsight Premium, R Server est désormais disponible en tant qu’option lors de la création de clusters HDInsight dans Azure. Cette nouvelle fonctionnalité offre aux experts en science des données, aux statisticiens et aux programmeurs R un accès à la demande à des méthodes d’analyse extensibles et distribuées sur HDInsight. Les clusters peuvent être dimensionnés en fonction des projets et des tâches en cours, puis détruits quand vous n’en avez plus besoin. Ces clusters faisant partie d’Azure HDInsight, ils bénéficient d’un support technique de niveau entreprise 24 heures sur 24, 7 jours sur 7, d’un contrat SLA garantissant 99,9 % de temps de fonctionnement, et de la flexibilité d’intégration avec d’autres composants de l’écosystème Azure.

>[AZURE.NOTE] R Server est disponible uniquement avec HDInsight Premium. Actuellement, HDInsight Premium est disponible uniquement pour les clusters Hadoop et Spark. Par conséquent, vous pouvez actuellement utiliser R Server uniquement avec des clusters Hadoop et Spark sur HDInsight. Pour plus d’informations, voir [Quels sont les différents niveaux de service et les composants Hadoop disponibles avec HDInsight](hdinsight-component-versioning.md).

R Server sur HDInsight fournit les dernières fonctionnalités d’analyse basée sur R sur des jeux de données volumineux chargés dans un objet blob Azure (WASB). Étant donné que R Server est basé sur du code R open source, les applications R que vous créez peuvent exploiter les plus de 8 000 packages open source disponibles, ainsi que les routines de ScaleR, package d’analyse du Big Data de Microsoft inclus dans R Server. Le nœud de périmètre des clusters Premium fournit une zone d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud de périmètre vous permet d’exécuter des fonctions distribuées parallélisées de ScaleR sur les cœurs du serveur de nœud de périmètre, ou sur les nœuds du cluster via l’utilisation des contextes de calcul Hadoop Map Reduce ou Spark de ScaleR. Les modèles ou prévisions résultant des analyses peuvent être téléchargés pour une utilisation locale ou mis en œuvre ailleurs dans Azure, par exemple via un service web [Azure Machine Learning Studio](http://studio.azureml.net) (AML) [](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md).

## Prise en main de R sur HDInsight

Pour inclure R Server sur un cluster HDInsight, vous devez créer un cluster Hadoop ou Spark avec l’option Premium lors de la création d’un cluster à l’aide du portail Azure. Les deux types de clusters utilisent la même configuration incluant R Server sur les nœuds de données d’un cluster, et un nœud de périmètre comme zone d’accueil pour l’analyse basée sur R Server. Pour une description détaillée de la création d’un cluster, voir [Prise en main de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md).

## Options de stockage de données

Le stockage par défaut pour les clusters HDInsight est un objet blob Azure (WASB) avec le système de fichiers HDFS mappé à un conteneur d’objets blob. Cela garantit que toutes les données chargées dans le stockage en cluster ou écrites dans celui-ci au cours de l’analyse sont rendues persistantes. Une méthode pratique de copie des données vers un objet blob et à partir d’un objet blob consiste à recourir à l’utilitaire [AzCopy](../storage/storage-use-azcopy/).

Vous pouvez également utiliser [Azure Files](../storage/storage-how-to-use-files-linux.md) comme option de stockage pour une utilisation sur le nœud de périmètre. Le service Azure Files vous permet de monter un partage de fichiers créé sur un compte Azure Storage pour le système de fichiers Linux. Pour plus d’informations sur les options de stockage de données pour R Server sur un cluster HDInsight, voir [Options de stockage pour R Server sur des clusters HDInsight](hdinsight-hadoop-r-server-storage.md).
  
## Accès à R Server sur le cluster

Après avoir créé un cluster avec R Server, vous pouvez vous connecter à la console R sur le nœud de périmètre du cluster via SSH/Putty, ou via un navigateur si vous choisissez d’installer l’IDE de RStudio Server sur le nœud de périmètre. Pour plus d’informations sur l’installation de RStudio Server, voir [Installation de RStudio Server sur des clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Développement et exécution de scripts R

Les scripts R que vous créez et exécutez peuvent utiliser les plus de 8 000 packages open source disponibles, en plus des routines distribuées et parallélisées de la bibliothèque ScaleR. Un script exécuté dans R Server sur le nœud de périmètre y est exécuté à l’aide de l’interpréteur R, à l’exception des étapes qui appellent l’une des fonctions ScaleR avec un contexte de calcul défini sur Hadoop MapReduce (RxHadoopMR) ou Spark (RxSpark). Dans ces cas, la fonction est exécutée en mode distribué sur les nœuds de données (tâche) du cluster associé aux données référencées. Pour plus d’informations sur les différentes options de contexte de calcul, voir [Options de contexte de calcul pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## Mise en œuvre d’un modèle

Lorsque votre modélisation des données est terminée, vous pouvez mettre en œuvre le modèle pour effectuer des prévisions sur de nouvelles données (opération également appelée notation) tant dans Azure que localement. Voici quelques exemples :

### Notation dans HDInsight

Pour noter dans HDInsight, vous pouvez écrire une fonction R qui appelle votre modèle pour effectuer des prévisions sur un nouveau fichier de données que vous avez téléchargé sur votre compte Azure Storage, puis enregistrer les prévisions dans le compte Storage. Vous pouvez exécuter la routine à la demande sur le nœud de périmètre de votre cluster ou à l’aide d’une tâche planifiée.

### Notation dans Azure Machine Learning 

Pour noter à l’aide d’un service web Azure Machine Learning, vous pouvez utiliser le [Package R Azure Machine Learning open source](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio) pour [publier votre modèle en tant que service web Azure](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/), utiliser les fonctionnalités d’Azure Machine Learning pour créer une interface utilisateur pour le service web, puis appeler le service web en fonction des besoins de notation. Si vous choisissez cette option, vous devez convertir tout objet de modèle ScaleR en un objet de modèle open source équivalent pour une utilisation avec le service web. Cela est possible en utilisant les fonctions de forçage de ScaleR, telle que `as.randomForest()`, pour les modèles basés sur un ensemble.
  
### Notation locale

Pour noter localement après la création de votre modèle, vous pouvez sérialiser le modèle dans R, le télécharger localement, le désérialiser, puis l’utiliser pour noter de nouvelles données. Vous pouvez faire cela en adoptant l’approche de la [Notation dans HDInsight](#scoring-in-hdinsight) décrite ci-dessus ou à l’aide de [DeployR](https://deployr.revolutionanalytics.com/).

## Maintenance du cluster 

### Installation et maintenance de packages R

La plupart des packages R que vous utilisez sont nécessaires sur le nœud de périmètre, car la partie principale de vos scripts R y est exécutée. Pour installer des packages R supplémentaires sur le nœud de périmètre, vous pouvez utiliser la méthode `install.packages()` habituelle dans R.
  
Dans la plupart des cas, il est inutile d’installer des packages R supplémentaires sur les nœuds de données si vous utilisez simplement des routines de la bibliothèque ScaleR à exécuter au sein du cluster. Toutefois, il se peut que vous ayez besoin de packages supplémentaires pour prendre en charge l’utilisation de **rxExec** ou l’exécution de **RxDataStep** sur les nœuds de données. Dans ces cas, ces packages supplémentaires doivent être spécifiés à l’aide d’une action de script après la création du cluster. Pour plus d’informations, voir [Création d’un cluster HDInsight avec R Server](hdinsight-hadoop-r-server-get-started.md).
  
### Modification des paramètres de mémoire de Hadoop MapReduce 

Vous pouvez modifier un cluster afin de changer la quantité de mémoire disponible pour R Server lors de l’exécution d’un travail MapReduce. Pour ce faire, utilisez l’interface utilisateur d’Ambari disponible via le panneau du portail Azure pour votre cluster. Pour obtenir des instructions sur l’accès à l’interface utilisateur d’Ambari pour votre cluster, voir [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).

Vous pouvez également modifier la quantité de mémoire disponible pour R Server en utilisant des commutateurs Hadoop dans l’appel à **RxHadoopMR**, comme indiqué ci-dessous.
 
	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### Mise à l’échelle de votre cluster

Vous pouvez modifier l’échelle d’un cluster existant via le portail Azure. Cela permet d’obtenir la capacité supplémentaire nécessaire pour un traitement plus important, ou de rétablir l’échelle d’un cluster quand il est inactif. Pour obtenir des instructions sur la mise à l’échelle d’un cluster, voir [Gérer des clusters HDInsight](hdinsight-administer-use-portal-linux.md).

### Maintenance du système 

La maintenance est effectuée sur les machines virtuelles Linux sous-jacentes dans un cluster HDInsight pendant les heures creuses pour appliquer des correctifs de système d’exploitation, etc. En général, ces opérations sont effectuées à 3 h 30 (selon l’heure locale pour la machine virtuelle), les lundi et jeudi de chaque semaine. Les mises à jour sont effectuées de façon à ce qu’elles n’aient pas d’incidence sur plus des ¼ du cluster à la fois. Étant donné que les nœuds principaux sont redondants et que certains nœuds de données ne sont pas affectés, toutes les tâches en cours d’exécution pendant ce temps peuvent être ralenties mais doivent être exécutées jusqu’à la fin. L’ensemble des logiciels personnalisés que vous pouvez avoir installés, ainsi que les données locales, sont conservés durant ces événements de maintenance, sauf si une défaillance irrémédiable se produit, qui nécessite une reconstruction du cluster.

## Options IDE pour R Server sur un cluster HDInsight

Le nœud de périmètre Linux d’un cluster HDInsight Premium est la zone d’accueil pour l’analyse basée sur R. Une fois connecté au cluster, vous pouvez lancer l’interface de la console R Server en tapant « R » à l’invite de commande Linux. L’utilisation de l’interface de la console est améliorée si vous exécutez un éditeur de texte pour le développement de script R dans une autre fenêtre, et coupez et collez des sections de votre script dans la console R en fonction des besoins.
  
Une alternative à l’utilisation d’un simple éditeur de texte pour le développement de votre script R consiste à utiliser un environnement de développement intégré (IDE) basé sur R sur votre bureau, tel que les [Outils R pour Visual Studio](https://www.visualstudio.com/fr-FR/features/rtvs-vs.aspx) (RTV), une famille d’outils de bureau et de serveur de [RStudio](https://www.rstudio.com/products/rstudio-server/) récemment annoncée par Microsoft, ou la solution [StatET](http://www.walware.de/goto/statet) de Walware, basée sur Eclipse .
  
Une autre option consiste à installer un IDE sur le nœud de périmètre Linux proprement dit. Un choix répandu dans ces cas est celui de [RStudio Server](https://www.rstudio.com/products/rstudio-server/), qui fournit un IDE basé sur un navigateur pour une utilisation par des clients distants. L’installation de RStudio Server sur le nœud de périmètre d’un cluster HDInsight Premium offre une expérience d’IDE complète pour le développement et l’exécution de scripts R avec R Server sur le cluster, et peut être considérablement plus productive que l’utilisation par défaut de la console R. Si vous souhaitez utiliser RStudio Server, voir [Installation de RStudio Server sur des clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Tarification
 
Les frais associés à un cluster HDInsight Premium avec R Server sont structurés de manière similaire à ceux applicables à des clusters HDInsight standard. Ils sont basés sur le dimensionnement des machines virtuelles sous-jacentes pour le nom, les données et les nœuds de périmètre, avec l’ajout d’une extension pendant les heures normales pour l’édition Premium. Pour plus d’informations sur la tarification de HDInsight Premium, notamment de la version préliminaire publique, et la disponibilité de la version d’évaluation gratuite de 30 jours, voir [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Étapes suivantes

Suivez les liens ci-dessous pour en savoir plus sur l’utilisation de R Server avec des clusters HDInsight.

- [Prise en main de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Ajouter RStudio Server à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Options de contexte de calcul pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Options d’Azure Storage pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

 

<!---HONumber=AcomDC_0330_2016-->