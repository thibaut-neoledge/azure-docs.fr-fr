<properties
   pageTitle="Options de contexte de calcul pour R Server sur HDInsight (version préliminaire) | Azure"
   description="Découvrez les différentes options de contexte de calcul disponibles pour les utilisateurs avec R Server sur HDInsight (version préliminaire)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

#Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)

R Server sur HDInsight (version préliminaire) offre les dernières fonctionnalités pour les analyses basées sur R à l’aide des données stockées dans HDFS dans un conteneur de votre compte de stockage d’[objets blob Azure](../storage/storage-introduction.md "Stockage d'objets blob Azure") ou dans le système de fichiers Linux local. Comme R Server est basé sur R open source, les applications R que vous créez peuvent tirer parti de plus de 8 000 packages R open source, ainsi que des routines de [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), package d’analyse de Big Data Microsoft inclus dans R Server. Le nœud de périmètre des clusters Premium fournit une zone d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud de périmètre vous permet d’exécuter des fonctions distribuées parallélisées de ScaleR sur les cœurs du serveur de nœud de périmètre, ou sur les nœuds du cluster via l’utilisation des contextes de calcul Hadoop Map Reduce ou Spark de ScaleR.

## Contextes de calcul pour un nœud de périmètre

En général, un script R exécuté dans R Server sur le nœud de périmètre s’exécute au sein de l’interpréteur R sur ce nœud, à l’exception des étapes qui appellent une fonction ScaleR. Les appels ScaleR sont exécutés dans un environnement de calcul déterminé par la façon dont vous définissez le contexte de calcul ScaleR. Les valeurs possibles du contexte de calcul lors de l’exécution de votre script R à partir d’un nœud de périmètre sont les valeurs local (local sequential), localpar (local parallel), MapReduce et Spark, où :

| Contexte de calcul | Définition | Contexte d’exécution |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local Sequential | rxSetComputeContext(‘local’) | Exécution séquentielle (non parallélisée) sur le serveur de nœud de périmètre |
| Local Parallel | rxSetComputeContext(‘localpar’) | Parallélisée sur les cœurs du serveur de nœud de périmètre |
| Spark | RxSpark() | Exécution distribuée parallélisée via Spark sur les nœuds du cluster HDI |
| Map Reduce | RxHadoopMR() | Exécution distribuée parallélisée via Map Reduce sur les nœuds du cluster HDI |


En supposant que vous souhaitez une exécution parallélisée à des fins de performances, trois options sont disponibles. Celle que vous choisissez dépend de la nature de votre travail d’analyse ainsi que de la taille et de l’emplacement de vos données.

## Choix d’un contexte de calcul

Il n’existe actuellement aucune formule pour vous indiquer le contexte de calcul à utiliser, mais il existe quelques principes qui peuvent vous guider vers le choix approprié ou au moins vous aider à limiter ces choix avant d’exécuter un test d’évaluation, si un meilleur choix s’impose. Ces principes sont les suivants :

1.	Le système de fichiers Linux local est plus rapide que HDFS
2.	Les analyses répétées sont plus rapides si les données sont locales et au format XDF 
3.	Le flux à partir d’une source de données texte pour les données de petite taille est sinon converti au format XDF avant les analyses 
4.	La surcharge de copie/streaming des données vers le nœud de périmètre pour l’analyse devient intenable pour les données très volumineuses 
5.	Spark est plus rapide que Map Reduce pour les analyses dans Hadoop jusqu’à ce que les données deviennent très volumineuses et ne puissent plus être contenues dans la mémoire distribuée

Une fois ces principes connus, il existe quelques règles générales que vous pouvez suivre pour sélectionner un contexte de calcul :

### Local Parallel

- Si les données à analyser sont de petite taille et ne nécessitent pas d’analyses répétées, diffusez-les directement dans la routine d’analyse et utilisez localpar. 
- Si les données à analyser sont de petite taille et nécessitent des analyses répétées, ou si elles sont modestes, copiez-les dans le système de fichiers local, importez-les au format XDF et analysez-les via localpar. 

### Hadoop Spark

- Si les données à analyser sont volumineuses, importez-les au format XDF dans HDFS, sauf si le stockage est un problème, puis analysez-les via Spark. 

### Hadoop Map Reduce

- Si les données à analyser sont très volumineuses et que les performances Spark commencent à se dégrader, tentez une analyse via Map Reduce.

## Aide en ligne sur rxSetComputeContext

Pour obtenir plus d’informations et des exemples de contextes de calcul ScaleR, consultez l’aide en ligne dans R sur la méthode rxSetComputeContext, par exemple

    > ?rxSetComputeContext 

ou le guide « ScaleR Distributed Computing Guide », disponible dans [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server sur MSDN") Library.


## Étapes suivantes

Maintenant que vous savez comment créer un cluster HDInsight qui comprend R Server et que vous connaissez les notions de base de l’utilisation de la console R à partir d’une session SSH, utilisez les éléments suivants pour découvrir d’autres méthodes de travail avec R Server sur HDInsight.

- [Vue d’ensemble de R Server sur Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Prise en main de R Server sur Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter RStudio Server à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Options d’Azure Storage pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0420_2016-->