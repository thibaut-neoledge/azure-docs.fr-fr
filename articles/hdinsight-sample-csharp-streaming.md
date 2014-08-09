<properties linkid="manage-services-hdinsight-sample-csharp-streaming" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight C# streaming wordcount sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight C# streaming wordcount sample" authors="bradsev" />

Exemple de comptage de mots en diffusion C\# HDInsight
======================================================

Hadoop fournit une API de diffusion en continu pour MapReduce qui vous permet d'écrire des fonctions de mappage et de réduction dans d'autres langages que Java. Ce didacticiel montre comment écrire des programmes MapReduce en langage C\# qui utilise l'interface de diffusion Hadoop et comment exécuter ces programmes sur Azure HDInsight en utilisant Azure PowerShell.

Dans cet exemple, le mappeur et le raccord de réduction sont des exécutables qui lisent les saisies depuis [stdin](http://msdn.microsoft.com/fr-fr/library/3x292kth(v=vs.110).aspx) (ligne par ligne), puis émettent leur résultat vers [stdout](http://msdn.microsoft.com/fr-fr/library/3x292kth(v=vs.110).aspx). Le programme compte tous les mots dans le texte.

Lorsqu'un exécutable est spécifié pour les **mappeurs**, chaque tâche de mappeur lance l'exécutable en tant que processus distinct lorsque le mappeur est initialisé. Durant son exécution, la tâche du mappeur convertit votre saisie en lignes et les utilise pour alimenter le [stdin](http://msdn.microsoft.com/fr-fr/library/3x292kth(v=vs.110).aspx) du processus. Dans le même temps, le mappeur collecte les résultats en forme de lignes depuis le stdout du processus, puis convertit chaque ligne en une paire clé/valeur, qui est collectée en tant que résultat du mappeur. Par défaut, la valeur va du début d'une ligne jusqu'à son premier caractère de tabulation, tandis que la valeur occupe le reste de la ligne (à l'exception du caractère de tabulation). Si la ligne ne contient pas de caractère de tabulation, elle constitue intégralement la clé, tandis que la valeur est nulle.

Lorsqu'un exécutable est spécifié pour les **raccords de réduction**, chaque tâche de raccord de réduction lance l'exécutable en tant que processus distinct lorsque le raccord de réduction est initialisé. Durant son exécution, la tâche du raccord de réduction convertit ses paires clé/valeur en lignes et les utilise pour alimenter le [stdin](http://msdn.microsoft.com/fr-fr/library/3x292kth(v=vs.110).aspx) du processus. Dans le même temps, le raccord de réduction collecte les résultats en forme de lignes depuis le [stdout](http://msdn.microsoft.com/fr-fr/library/3x292kth(v=vs.110).aspx) du processus, puis convertit chaque ligne en une paire clé/valeur, qui est collectée en tant que résultat du raccord de réduction. Par défaut, la valeur va du début d'une ligne jusqu'à son premier caractère de tabulation, tandis que la valeur occupe le reste de la ligne (à l'exception du caractère de tabulation).

Pour plus d'informations sur l'interface de diffusion Hadoop, consultez la page [Diffusion Hadoop](http://wiki.apache.org/hadoop/HadoopStreaming).

**Vous apprendrez à effectuer les opérations suivantes :**

-   utiliser Azure PowerShell pour exécuter un programme de diffusion C\# pour analyser les données contenues dans un fichier sur HDInsight ;
-   écrire du code C\# utilisant l'interface de diffusion Hadoop.

**Conditions préalables** :

-   Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/).

-   Vous devez avoir approvisionné un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de clusters, consultez la page [Approvisionnement de clusters HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

Dans cet article
----------------

Cette rubrique vous montre comment exécuter l'exemple, présente le code Java du programme MapReduce, récapitule ce que vous avez appris, et indique les étapes suivantes. Elle se compose des sections suivantes :

1.  [Exécution de l'exemple avec Azure PowerShell](#run-sample)
2.  [Code C\# pour la diffusion Hadoop](#java-code)
3.  [Résumé](#summary)
4.  [Étapes suivantes](#next-steps)

Exécution de l'exemple avec Azure PowerShell
--------------------------------------------

**Pour exécuter la tâche MapReduce, procédez comme suit :**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

         $subscriptionName = "<SubscriptionName>"       # Nom de l'abonnement Azure
         $clusterName = "<ClusterName>"             # Nom du cluster HDInsight

3.  Exécutez la commande suivante pour définir la tâche MapReduce :

         # Créez une définition de tâche MapReduce pour la tâche de diffusion.
         $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    Les paramètres indiquent les fonctions de mappeur et de raccord de réduction, ainsi que le fichier d'entrée et les fichiers de sortie.

4.  Exécutez les commandes suivantes pour exécuter la tâche MapReduce, attendre la fin de la tâche, puis imprimer l'erreur standard :

         # Exécutez la tâche MapReduce de diffusion C#.
         # Attendez la fin de la tâche.
         # Imprimez le résultat et le fichier d'erreurs standard de la tâche MapReduce.
         Select-AzureSubscription $subscriptionName
         $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  Exécutez les commandes suivantes pour afficher les résultats du comptage de mots.

         $subscriptionName = "<SubscriptionName>"   
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<ContainerName>

     "

         # Sélectionnez l'abonnement en cours
         Select-AzureSubscription $subscriptionName
                  
         # Nom du conteneur de stockage d'objets blob et du compte

    $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

         # Récupérez le résultat
         Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

         # Le nombre de mots dans le texte est :
         cat ./example/data/StreamingOutput/wc.txt/part-00000

    Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.

Code C\# pour la diffusion Hadoop
---------------------------------

Le programme MapReduce utilise l'application cat.exe en tant qu'interface de mappage pour diffuser le texte dans la console et l'application wc.exe en tant qu'interface de réduction pour compter le nombre de mots diffusés à partir d'un document. Le mappeur et le raccord de réduction peuvent tous les deux lire les caractères à partir du flux d'entrée standard (stdin), puis écrire dans le flux de sortie standard (stdout).

    // Voici le code source de l'application cat.exe (mappeur). 
     
    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0])); 
                }

                string line;
                while ((line = Console.ReadLine()) != null) 
                {
                    Console.WriteLine(line);
                }
            }
        }
    }

Le code de mappeur du fichier cat.cs utilise un objet StreamReader pour lire les caractères du flux entrant dans la console, qui, de son côté, écrit le flux de sortie standard avec la méthode Console.Writeline statique.

    // Voici le code source de l'application we.exe (raccord de réduction).

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0])); 
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }

Le code du raccord de réduction du fichier wc.cs utilise un objet [StreamReader](http://msdn.microsoft.com/fr-fr/library/system.io.streamreader.aspx) pour lire les caractères du flux d'entrée standard écrit par le mappeur cat.exe. Comme il lit les caractères avec la méthode [Console.Writeline](http://msdn.microsoft.com/fr-fr/library/system.console.writeline), il délimite les mots en se basant sur les espaces et les caractères de fin de ligne situés à la fin de chaque mot, puis il écrit le total dans le flux de sortie standard avec la méthode [Console.Writeline](http://msdn.microsoft.com/fr-fr/library/system.console.writeline).

Résumé
------

Dans ce didacticiel, vous avez vu comment déployer une tâche MapReduce sur HDInsight avec la diffusion Hadoop.

Étapes suivantes
----------------

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Exemple : estimateur de la valeur de Pi](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Exemple : comptage de mots](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Exemple : GraySort de 10 Go](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

