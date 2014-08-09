<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Run HDInsight Samples" pageTitle="Run the HDInsight samples | Azure" metaKeywords="" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the HDInsight samples" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Exécution des exemples HDInsight
================================

Un ensemble d'exemples vous est fourni pour vous aider à démarrer dans Azure HDInsight. Ces exemples sont disponibles sur chacun des clusters HDInsight que vous créez. Exécutez-les pour vous familiariser avec les cmdlets Azure PowerShell HDInsight.

Les programmes MapReduce peuvent également être exécutés par programme à partir d'une application au moyen de l'API Microsoft .NET pour HDInsight. Pour plus d'informations sur l'utilisation des API HDInsight pour envoyer des tâches, consultez la page [Envoi de tâches Hadoop par programme](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/).

Une documentation abondante existe sur le Web pour les technologies liées à Hadoop, telles que la programmation et la diffusion MapReduce basées sur Java, ainsi que pour l'utilisation des cmdlets dans les scripts PowerShell. Pour plus d'informations sur ces ressources, consultez la section finale **Ressources relatives à HDInsight** de la rubrique [Présentation d'Azure HDInsight](/en-us/manage/services/hdinsight/introduction-hdinsight/).

**Description de ces exemples**

Ces exemples ont été conçus pour vous aider à maîtriser rapidement le déploiement de tâches Hadoop et pour vous fournir une plateforme de test extensible destinée aux concepts et procédures de création de scripts utilisés par le service. Il s'agit d'exemples de tâches courantes telles que la création et l'importation de jeux de données de tailles diverses, l'exécution et la composition de tâches de façon séquentielle, et l'examen des résultats de vos tâches. La taille des jeux de données utilisés peut varier, vous permettant ainsi d'observer l'effet des différentes tailles de jeux de données sur les performances des tâches.

**Conditions préalables** :

-   Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/).

-   Vous devez avoir mis en service un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de clusters, consultez la page [Mise en service de clusters HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

Exemples disponibles
--------------------

HDInsight est fourni avec les exemples suivants.

-   [**Exemple Estimateur de la valeur de Pi**](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/) Ce didacticiel explique comment exécuter un programme MapReduce avec HDInsight qui utilise une méthode statistique (quasi-Monte-Carlo) pour estimer la valeur de Pi.
-   [**Exemple WordCount**](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/) Ce didacticiel explique comment utiliser un cluster HDInsight pour exécuter un programme MapReduce qui calcule les occurrences de mots dans un fichier texte.
-   [**Exemple Graysort 10 Go**](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/) Ce didacticiel explique comment exécuter un programme GraySort généraliste sur un fichier de 10 Go au moyen de HDInsight. Il y a trois tâches à exécuter : Teragen pour générer les données, Terasort pour trier les données et Teravalidate pour confirmer que les données ont été correctement triées.
-   [**Exemple C\# Streaming**](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/) Ce didacticiel explique comment utiliser C\# pour écrire un programme MapReduce utilisant l'interface de diffusion en continu Hadoop.

Exécution des exemples
----------------------

Les exemples peuvent être exécutés au moyen d'Azure PowerShell. Pour des instructions sur la façon de procéder pour chacun des exemples, cliquez sur les liens fournis ci-dessus.

Étapes suivantes
----------------

Dans cet article et les articles liés à chacun des exemples, vous avez appris à exécuter les exemples inclus avec les clusters HDInsight au moyen d'Azure PowerShell. Pour des didacticiels sur l'utilisation de Pig, Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

-   [Prise en main du service Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Envoi de tâches Hadoop par programme](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)
-   [Débogage d'HDInsight : Messages d'erreur](/en-us/manage/services/hdinsight/debug-error-messages/)

