<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Exécution des exemples Hadoop dans HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the Hadoop samples in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Exécution des exemples Hadoop dans HDInsight

Un ensemble d'exemples vous est fourni pour vous aider à prendre en main les tâches MapReduce sur les clusters Hadoop en utilisant Azure HDInsight. Ces exemples sont disponibles sur chacun des clusters gérés HDInsight que vous créez. Exécutez-les pour vous familiariser avec les cmdlets Azure PowerShell HDInsight afin d'exécuter des tâches sur les clusters Hadoop.

Les programmes MapReduce peuvent également être exécutés par programme à partir d'une application au moyen de l'API Microsoft .NET pour HDInsight. Pour plus d'informations sur l'utilisation des API HDInsight pour envoyer des tâches, consultez la rubrique [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs].

Une documentation abondante existe sur le Web pour les technologies liées à Hadoop, telles que la programmation et la diffusion MapReduce basées sur Java, ainsi que pour l'utilisation des cmdlets dans les scripts PowerShell. Pour plus d'informations sur ces ressources, consultez la section finale **Ressources pour HDInsight** de la rubrique [Présentation d'Azure HDInsight][hdinsight-introduction].

**Description de ces exemples**

<p>Ces exemples ont été conçus pour vous aider à maîtriser rapidement le déploiement de tâches Hadoop et pour vous fournir une plateforme de test extensible destinée aux concepts et procédures de création de scripts utilisés par le service. Il s'agit d'exemples de tâches courantes telles que la création et l'importation de jeux de données de tailles diverses, l'exécution et la composition de tâches de façon séquentielle, et l'examen des résultats de vos tâches. La taille des jeux de données utilisés peut varier, vous permettant ainsi d'observer l'effet des différentes tailles de jeux de données sur les performances des tâches.</p>


**Conditions préalables** :	

- Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/) .

- Vous devez avoir approvisionné un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de cluster, consultez la rubrique [Approvisionnement de clusters HDInsight](../hdinsight-provision-clusters/)

- Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell][powershell-install-configure].

## Exemples ##

HDInsight est fourni avec les exemples suivants.

- [**Exemple Estimateur de la valeur de Pi**][hdinsight-sample-pi-estimator] Ce didacticiel explique comment exécuter un programme MapReduce avec HDInsight qui utilise une méthode statistique (quasi-Monte-Carlo) pour estimer la valeur de Pi.
- [**Exemple WordCount**][hdinsight-sample-wordcount] Ce didacticiel explique comment utiliser un cluster HDInsight pour exécuter un programme MapReduce qui calcule les occurrences de mots dans un fichier texte.
- [**Exemple Graysort 10 Go**][hdinsight-sample-10gb-graysort] Ce didacticiel explique comment exécuter un programme GraySort généraliste sur un fichier de 10 Go au moyen de HDInsight. Il y a trois tâches à exécuter : Teragen pour générer les données, Terasort pour trier les données et Teravalidate pour confirmer que les données ont été correctement triées.
- [**Exemple C# Streaming**][hdinsight-sample-csharp-streaming] Ce didacticiel explique comment utiliser C# pour écrire un programme MapReduce utilisant l'interface de diffusion en continu Hadoop. 


## Exécution des exemples ##

Les exemples peuvent être exécutés au moyen d'Azure PowerShell. Pour des instructions sur la façon de procéder pour chacun des exemples, cliquez sur les liens fournis ci-dessus.

##Étapes suivantes ##

Dans cet article et les articles liés à chacun des exemples, vous avez appris à exécuter les exemples inclus avec les clusters HDInsight au moyen d'Azure PowerShell. Pour des didacticiels sur l'utilisation de Pig, Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Prise en main du service Azure HDInsight][hdinsight-get-started]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]
* [Débogage de HDInsight : messages d'erreur][hdinsight-errors]


[hdinsight-errors]: ../hdinsight-debug-jobs/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-introduction]: ../hdinsight-hadoop-introduction/



[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=35.1-->
