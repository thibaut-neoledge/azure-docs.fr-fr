<properties linkid="manage-services-hdinsight-sample-wordcount" urlDisplayName="Hadoop Samples in HDInsight" pageTitle="The WordCount Hadoop sample in HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The WordCount Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Exemple Hadoop de comptage de mots dans HDInsight

Cette rubrique propose un exemple qui permet d'exécuter un programme Hadoop MapReduce qui compte les occurrences de mots dans un fichier texte à l'aide d'Azure PowerShell pour exécuter le programme dans HDInsight. Le programme MapReduce de comptage de mots est écrit en Java et fonctionne sur un cluster Hadoop géré par HDInsight. Le fichier texte analysé dans cet exemple est l'édition des Carnets de Léonard de Vinci en livre électronique par le Project Gutenberg.

Le programme Hadoop MapReduce lit le fichier texte et comptabilise les occurrences de chaque mot. Il en résulte un nouveau fichier texte composé de lignes, chacune contenant un mot et le nombre d'occurrences de ce mot dans le document (paire clé/valeur séparée par tabulation). Ce processus se déroule en deux étapes. Le mappeur prend chaque ligne du texte saisi en tant qu'entrée, et la divise en mots. Il émet une paire clé/valeur à chaque occurrence de travail du mot défini suivi par 1. Le raccord de réduction calcule la somme du compte de mots, puis émet une seule paire clé/valeur contenant le mot défini, suivi par la somme de ses occurrences.

**Vous apprendrez à effectuer les opérations suivantes :**

-   utiliser Azure PowerShell pour exécuter un programme MapReduce sur un cluster HDInsight ;
-   écrire des programmes MapReduce en Java.

**Conditions préalables** :

-   Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure][].

-   Vous devez avoir approvisionné un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de clusters, consultez la page [Prise en main d'Azure HDInsight][] ou [Approvisionnement de clusters HDInsight][].

-   Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell][].

## Dans cet article

Cette rubrique vous montre comment exécuter l'exemple, présente le code Java du programme MapReduce, récapitule ce que vous avez appris, et indique les étapes suivantes. Elle se compose des sections suivantes :

1.  [Exécution de l'exemple avec Azure PowerShell][]
2.  [Code Java pour le programme WordCount MapReduce][]
3.  [Résumé][]
4.  [Étapes suivantes][]

## <span id="run-sample"></span></a>Exécution de l'exemple avec Azure PowerShell

**Envoi de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell][].

2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Exécutez la commande suivante pour créer une définition de tâche MapReduce :

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] Le fichier *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

    Le fichier hadoop-examples.jar est fourni avec le cluster HDInsight. Il existe deux arguments pour la tâche MapReduce. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Le fichier source est fourni avec le cluster HDInsight, tandis que le chemin d'accès au fichier de sortie est créé au moment de l'exécution.

4.  Exécutez la commande suivante pour soumettre la tâche MapReduce :

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    En plus de la définition de la tâche MapReduce, fournissez également le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.

5.  Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Pour récupérer les résultats de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**.
2.  Définissez les trois variables dans les commandes suivantes, puis exécutez-les :

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

    Le compte de stockage Azure est celui que vous avez créé plus tôt dans ce didacticiel. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le conteneur de stockage d'objets blob et le cluster HDInsight partagent généralement le même nom, à moins que vous spécifiiez un nom différent lors de la configuration du cluster.

3.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* sert à définir l'abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

4.  Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Le dossier */example/data/WordCountOutput* est le dossier de résultat spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour le résultat de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C. Le fichier est donc téléchargé dans le dossier *C:\\example\\data\\WordCountOutput\\*.

5.  Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande findstr pour répertorier tous les mots contenant « *there* ».

La sortie du script WordCount doit s'afficher dans la fenêtre cmd :

![HDI.Sample.WordCount.Output][]

Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.

## <span id="java-code"></span></a>Code Java pour le programme WordCount MapReduce

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
    }

    public static void main(String[] args) throws Exception {
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
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
    }

## <span id="summary"></span></a>Résumé

Ce didacticiel vous a présenté un exemple qui explique comment exécuter un programme MapReduce qui compte les occurrences de mots dans un fichier texte avec HDInsight et Azure PowerShell

## <span id="next-steps"></span></a>Étapes suivantes

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

-   [Prise en main d'Azure HDInsight][]
-   [Exemple : GraySort 10 Go][]
-   [Exemple : Estimateur Pi][]
-   [Exemple : Diffusion en continu en C#][]
-   [Utilisation de Pig avec HDInsight][]
-   [Utilisation de Hive avec HDInsight][]
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][]

  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Approvisionnement de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Exécution de l'exemple avec Azure PowerShell]: #run-sample
  [Code Java pour le programme WordCount MapReduce]: #java-code
  [Résumé]: #summary
  [Étapes suivantes]: #next-steps
  [HDI.Sample.WordCount.Output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
  [Exemple : GraySort 10 Go]: ../hdinsight-sample-10gb-graysort/
  [Exemple : Estimateur Pi]: ../hdinsight-sample-pi-estimator/
  [Exemple : Diffusion en continu en C#]: ../hdinsight-sample-csharp-streaming/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Documentation du Kit de développement logiciel (SDK) Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
