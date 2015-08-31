<properties
	pageTitle="Exécution des exemples Hadoop dans HDInsight | Microsoft Azure"
	description="Prise en main du service Azure HDInsight avec les exemples fournis. Utilisez des scripts PowerShell qui exécutent des programmes MapReduce sur des clusters de données."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>




#Exécution des exemples Hadoop dans HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Un ensemble d'exemples vous est fourni pour vous aider à prendre en main les tâches MapReduce sur les clusters Hadoop en utilisant Azure HDInsight. Ces exemples sont disponibles sur chacun des clusters gérés HDInsight que vous créez. Exécutez-les pour vous familiariser avec les cmdlets Azure PowerShell afin d’exécuter des tâches sur les clusters Hadoop.

Les programmes MapReduce peuvent également être exécutés par programme à partir d’une application au moyen de l’API Microsoft .NET pour HDInsight. Pour plus d’informations sur l’utilisation des API HDInsight pour envoyer des tâches, consultez la page [Envoi de tâches Hadoop dans HDInsight][hdinsight-submit-jobs].

Une documentation abondante existe sur le Web pour les technologies liées à Hadoop, telles que la programmation et la diffusion MapReduce basées sur Java, ainsi que pour l’utilisation des cmdlets dans les scripts Windows PowerShell. Pour plus d’informations sur ces ressources, consultez la section finale **Ressources relatives à HDInsight** de la rubrique [Présentation d’Azure HDInsight][hdinsight-introduction].

**Description de ces exemples**

<p>Ces exemples ont été conçus pour vous aider à maîtriser rapidement le déploiement de tâches Hadoop et pour vous fournir une plateforme de test extensible destinée aux concepts et procédures de création de scripts utilisés par le service. Il s’agit d’exemples de tâches courantes telles que la création et l’importation de jeux de données de tailles diverses, l’exécution et la composition de tâches de façon séquentielle, et l’examen des résultats de vos tâches. La taille des jeux de données utilisés peut varier, vous permettant ainsi d’observer l’effet des différentes tailles de jeux de données sur les performances des tâches.</p>


**Conditions préalables** :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un cluster HDInsight**. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de cluster, consultez la page [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## Exemples disponibles ##

HDInsight est fourni avec les exemples suivants :

- [**Exemple Hadoop Estimateur de la valeur de Pi**][hdinsight-sample-pi-estimator] : ce didacticiel explique comment exécuter un programme MapReduce avec HDInsight qui utilise une méthode statistique (quasi-Monte-Carlo) pour estimer la valeur de Pi.
- [**Exécution d’un exemple de statistiques MapReduce sur un cluster Hadoop**][hdinsight-sample-wordcount] : ce didacticiel explique comment utiliser un cluster HDInsight pour exécuter un programme MapReduce qui calcule les occurrences de mots dans un fichier texte.
- [**Exemple Hadoop Graysort 10 Go**][hdinsight-sample-10gb-graysort] : ce didacticiel explique comment exécuter un programme GraySort généraliste sur un fichier de 10 Go au moyen de HDInsight. Il faut exécuter trois tâches : Teragen pour générer les données, Terasort pour trier les données et Teravalidate pour confirmer que les données ont été correctement triées.
- [**Exemple MapReduce WordCount (comptage de mots) de diffusion en continu C# dans Hadoop**][hdinsight-sample-csharp-streaming] : ce didacticiel explique comment utiliser C# pour écrire un programme MapReduce utilisant l’interface de diffusion en continu Hadoop.


## Exécution des exemples ##

Les exemples peuvent être exécutés au moyen d’Azure PowerShell. Des instructions sur la façon de procéder sont fournies pour chacun des exemples précédents.

##Étapes suivantes ##

Dans cet article et les articles liés à chacun des exemples, vous avez appris à exécuter les exemples inclus avec les clusters HDInsight au moyen d’Azure PowerShell. Pour des didacticiels sur l’utilisation de Pig, Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Prise en main de Hadoop avec Hive dans HDInsight pour analyser l’utilisation des téléphones mobiles][hdinsight-get-started]
* [Utilisation de Pig avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Utilisation de Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Envoi de tâches Hadoop dans HDInsight][hdinsight-submit-jobs]
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]
* [Débogage de Hadoop dans HDInsight : messages d’erreur][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=August15_HO8-->