<properties linkid="manage-services-hdinsight-sample-wordcount" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight WordCount sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight WordCount sample" authors="bradsev" />

Exemple WordCount HDInsight
===========================

Cette rubrique présente un exemple qui explique comment exécuter un programme MapReduce qui compte les occurrences de mots dans un fichier texte avec Azure HDInsight et Azure PowerShell. Le programme MapReduce WordCount est écrit en Java et fonctionne sur un cluster HDInsight. Le fichier de texte analysé dans notre exemple est l'édition des Carnets de Léonard de Vinci en livre électronique par le Project Gutenberg.

Le programme Hadoop MapReduce lit le fichier texte et comptabilise les occurrences de chaque mot. Il en résulte un nouveau fichier texte composé de lignes, chacune contenant un mot et le nombre d'occurrences de ce mot dans le document (paire clé/valeur séparée par tabulation). Ce processus se déroule en deux étapes. Le mappeur prend chaque ligne du texte saisi en tant qu'entrée, et la divise en mots. Il émet une paire clé/valeur à chaque occurrence de travail du mot défini suivi par 1. Le raccord de réduction calcule la somme du compte de mots, puis émet une seule paire clé/valeur contenant le mot défini, suivi par la somme de ses occurrences.

**Vous apprendrez à effectuer les opérations suivantes :**

-   utiliser Azure PowerShell pour exécuter un programme MapReduce sur un cluster HDInsight ;
-   écrire des programmes MapReduce en Java.

**Conditions préalables** :

-   Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Vous devez avoir approvisionné un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de clusters, consultez la page [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) ou [Approvisionnement de clusters HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Dans cet article
----------------

Cette rubrique vous montre comment exécuter l'exemple, présente le code Java du programme MapReduce, récapitule ce que vous avez appris, et indique les étapes suivantes. Elle se compose des sections suivantes :

1.  [Exécution de l'exemple avec Azure PowerShell](#run-sample)
2.  [Code Java pour le programme WordCount MapReduce](#java-code)
3.  [Résumé](#summary)
4.  [Étapes suivantes](#next-steps)

Exécution de l'exemple avec Azure PowerShell
--------------------------------------------

**Envoi de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

         $subscriptionName = "<SubscriptionName>"   # Nom de l'abonnement Azure
         $clusterName = "<ClusterName>"             # Nom du cluster HDInsight

3.  Exécutez la commande suivante pour créer une définition de tâche MapReduce :

         # Définissez la tâche MapReduce
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] Le fichier *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

    Le fichier hadoop-examples.jar est fourni avec le cluster HDInsight. Il existe deux arguments pour la tâche MapReduce. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Le fichier source est fourni avec le cluster HDInsight, tandis que le chemin d'accès au fichier de sortie est créé au moment de l'exécution.

4.  Exécutez la commande suivante pour soumettre la tâche MapReduce :

         # Envoyez la tâche
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    En plus de la définition de la tâche MapReduce, fournissez également le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.

5.  Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :

         # Recevez le résultat de la tâche
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Pour récupérer les résultats de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**.
2.  Définissez les trois variables dans les commandes suivantes, puis exécutez-les :

         $subscriptionName = "<SubscriptionName>"   # Nom de l'abonnement Azure
            
         $storageAccountName = "<StorageAccountName>"   # Nom du compte de stockage Azure
         $containerName = "<ContainerName>"              # Nom du conteneur de stockage d'objets blob

    Le compte de stockage Azure est celui que vous avez créé plus tôt dans ce didacticiel. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le conteneur de stockage d'objets blob et le cluster HDInsight partagent généralement le même nom, à moins que vous spécifiiez un nom différent lors de la configuration du cluster.

3.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

         # Sélectionnez l'abonnement en cours
         Select-AzureSubscription $subscriptionName

         # Créez l'objet de contexte du compte de stockage
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* sert à définir l'abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

4.  Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :

         # Téléchargez le résultat de la tâche vers le poste de travail
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Le dossier */example/data/WordCountOutput* est le dossier de résultat spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour le résultat de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C. Le fichier est donc téléchargé dans le dossier *C:\\example\\data\\WordCountOutput\\*.

5.  Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande findstr pour répertorier tous les mots contenant « *there* ».

La sortie du script WordCount doit s'afficher dans la fenêtre cmd :

![HDI.Sample.WordCount.Output](./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png)

Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.

Code Java pour le programme WordCount MapReduce
-----------------------------------------------

    package org.apache.hadoop.examples;
    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

    public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
      
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
      }
    }
    public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }

    } public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
        }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) 
     0 : 1);
    }
    }

Résumé
------

Ce didacticiel vous a présenté un exemple qui explique comment exécuter un programme MapReduce qui compte les occurrences de mots dans un fichier texte avec HDInsight et Azure PowerShell

Étapes suivantes
----------------

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Exemple : GraySort 10 Go](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Exemple : Estimateur Pi](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Exemple : Diffusion en continu en C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

