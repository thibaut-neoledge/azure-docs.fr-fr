<properties linkid="manage-services-hdinsight-sample-10gb-graysort" urlDisplayName="HDInsight Samples" pageTitle="The 10GB GraySort sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a general purpose GraySort with HDInsight using Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The 10GB GraySort sample" authors="bradsev" />

Exemple GraySort 10 Go
======================

Cette rubrique d'exemple explique comment exécuter un programme GraySort généraliste avec Azure HDInsight au moyen d'Azure PowerShell. Un GraySort est un tri de benchmark dont la mesure est le taux de tri (To/minute) obtenu lors du tri de très grandes quantités de données, en général au minimum 100 To.

Cet exemple utilise seulement 10 Go de données afin de pouvoir être exécuté relativement rapidement. Il utilise les applications MapReduce développées par Owen O'Malley et Arun Murthy qui ont remporté en 2009 le benchmark de tri de téraoctets (« daytona ») annuel universel avec un taux de 0,578 To/min (100 To en 173 minutes). Pour plus d'informations à ce sujet et sur d'autres benchmarks de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/).

Cet exemple utilise trois ensembles de programmes MapReduce :

1.  **TeraGen** est un programme MapReduce que vous pouvez utiliser pour générer les lignes de données à trier.
2.  **TeraSort** échantillonne les données d'entrée et utilise MapReduce pour trier les données en une commande totale. TeraSort est un tri standard de fonctions MapReduce, sauf pour un partitionneur personnalisé qui utilise une liste triée de clés échantillonnées N-1 définissant le groupe de clés pour chaque réduction. Plus particulièrement, toutes les clés semblables à cet échantillon [i-1] &lt;= key &lt; sample[i] sont envoyées pour réduire i ; cela garantit que les sorties de réduction i sont toutes inférieures aux sorties de réduction i+1.
3.  **TeraValidate** est un programme MapReduce qui valide que la sortie soit globalement triée. Il crée un mappage par fichier dans le répertoire de sortie et chaque mappage assure que chaque clé est inférieure ou égale à la précédente. La fonction de mappage génère également des enregistrements des première et dernière clés de chaque fichier et la fonction de réduction assure que la première clé du fichier i est supérieure à la dernière clé du fichier i-1. Un problème est signalé comme une sortie de la réduction avec les clés dans le désordre.

Les formats d'entrée et de sortie utilisés par les trois applications lisent et écrivent les fichiers texte dans le bon format. La réplication de la sortie de la réduction est définie sur 1 au lieu de 3 par défaut, car le concours benchmark ne requiert pas la réplication des données de sortie sur plusieurs nœuds.

**Vous apprendrez à effectuer les opérations suivantes :** 
* Utilisation d'Azure PowerShell pour exécuter une série de programmes MapReduce sur Azure HDInsight.
* Identification d'un programme MapReduce écrit en Java.

**Conditions préalables** :

-   Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/).

-   Vous devez avoir approvisionné un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de clusters, consultez la page [Approvisionnement de clusters HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

Dans cet article
----------------

Cette rubrique vous explique comment exécuter la série de programmes MapReduce qui compose l'exemple, présente le code Java du programme MapReduce, résume ce que vous avez appris et décrit quelques étapes suivantes. Elle se compose des sections suivantes :

1.  [Exécution de l'exemple avec Azure PowerShell](#run-sample)
2.  [Code Java du programme MapReduce TeraSort](#java-code)
3.  [Résumé](#summary)
4.  [Étapes suivantes](#next-steps)

Exécution de l'exemple avec Azure PowerShell
--------------------------------------------

Trois tâches sont requises par l'exemple, chacune correspondant à un des programmes MapReduce décrits dans l'introduction :

1.  Générez les données à trier en exécutant la tâche MapReduce **TeraGen**.
2.  Triez les données en exécutant la tâche MapReduce **TeraSort**.
3.  Confirmez que les données ont été correctement triées en exécutant la tâche MapReduce **TeraValidate**.

**Exécution du programme TeraGen**

1.  Ouvrez Azure PowerShell. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).
2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

         # Fournissez le nom d'abonnement Azure et le nom du cluster HDInsight.
         $subscriptionName = "myAzureSubscriptionName"   
         $clusterName = "myClusterName"

3.  Exécutez la commande suivante pour créer une définition de tâche MapReduce :

         # Créez une définition de tâche MapReduce pour le programme MapReduce TeraGen
         $teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input" 

    > [WACOM.NOTE] Le fichier *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

    L'argument *"-Dmapred.map.tasks=50"* spécifie que 50 mappages sont créés pour exécuter la tâche. L'argument *100000000* spécifie la quantité de données à générer. Le dernier argument, */example/data/10GB-sort-input*, spécifie le répertoire de sortie dans lequel les résultats sont enregistrés (et qui contient les entrées pour l'étape de tri suivante).

4.  Exécutez les commandes suivantes pour envoyer la tâche, attendez qu'elle soit terminée, puis imprimez l'erreur standard :

         # Exécutez la tâche MapReduce TeraGen.
         # Attendez la fin de la tâche.
         # Imprimez la sortie et le fichier d'erreur standard de la tâche MapReduce
         Select-AzureSubscription $subscriptionName         
         $teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**Exécution du programme TeraSort**

1.  Ouvrez Azure PowerShell.
2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

         # Fournissez le nom d'abonnement Azure et le nom du cluster HDInsight.
         $subscriptionName = "myAzureSubscriptionName"   
         $clusterName = "myClusterName"

3.  Exécutez la commande suivante pour définir la tâche MapReduce :

         # Créez une définition de tâche MapReduce pour le programme MapReduce TeraSort
         $terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output" 

    L'argument *"-Dmapred.map.tasks=50"* spécifie que 50 mappages sont créés pour exécuter la tâche. L'argument *100000000* spécifie la quantité de données à générer. Les deux derniers arguments spécifient les répertoires d'entrée et de sortie.

4.  Exécutez la commande suivante pour envoyer la tâche, attendez qu'elle soit terminée, puis imprimez l'erreur standard :

         # Exécutez la tâche MapReduce TeraSort.
         # Attendez la fin de la tâche.
         # Imprimez la sortie et le fichier d'erreur standard de la tâche MapReduce 
         Select-AzureSubscription $subscriptionName        
         $terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**Exécution du programme TeraValidate**

1.  Ouvrez Azure PowerShell.
2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

         # Fournissez le nom d'abonnement Azure et le nom du cluster HDInsight.
         $subscriptionName = "myAzureSubscriptionName"   
         $clusterName = "myClusterName"

3.  Exécutez la commande suivante pour définir la tâche MapReduce :

         #   Créez une définition de tâche MapReduce pour le programme MapReduce TeraValidate
         $teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate" 

    L'argument *"-Dmapred.map.tasks=50"* spécifie que 50 mappages sont créés pour exécuter la tâche. L'argument *"-Dmapred.reduce.tasks=25"* spécifie que 25 réductions sont créées pour exécuter la tâche. Les deux derniers arguments spécifient les répertoires d'entrée et de sortie.

4.  Exécutez les commandes suivantes pour envoyer la tâche MapReduce, attendez qu'elle soit terminée, puis imprimez l'erreur standard :

         # Exécutez la tâche MapReduce TeraSort.
         # Attendez la fin de la tâche.
         # Imprimez la sortie et le fichier d'erreur standard de la tâche MapReduce 
         Select-AzureSubscription $subscriptionName 
         $teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

Code Java du programme MapReduce TeraSort
-----------------------------------------

Le code du programme MapReduce TeraSort est présenté pour l'inspection dans cette section.

    /**
     * Licence pour Apache Software Foundation (ASF) sous un    
     * ou plusieurs contrats de licence de collaborateurs.  Consultez le fichier NOTICE 
     * distribué avec ce travail pour plus d'informations    
     * concernant la propriété des droits d'auteur.  La licence de ce fichier 
     * vous est cédée par ASF sous la licence Apache, version 2.0 (la    
     * « Licence ») ; vous êtes autorisé à utiliser ce fichier uniquement conformément    
     * à la Licence.  Vous pouvez obtenir une copie de la Licence à la page   
     *  
     *     http://www.apache.org/licenses/LICENSE-2.0   
     *  
     * Sauf si la loi applicable l'exige ou si cela a été convenu dans la rédaction, le logiciel   
     * distribué sous la Licence est distribué « TEL QUEL »,    
     * SANS GARANTIES NI CONDITIONS D'AUCUNE SORTE, que ce soit expressément ou implicitement.   
     * Consultez la Licence pour connaître le langage spécifique régissant les permissions et   
     * les limitations sous la Licence. 
     */ 

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /** 
     * Génère les points de fractionnement échantillonnés, lance la tâche     
     * et attend qu'elle soit terminée.  
     * <p>
     * Pour exécuter le programme :     
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>    
     */ 

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**   
       * Un partitionneur qui fractionne les clés de texte en partitions     
       * approximativement égales dans un ordre global trié.  
       */   

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * Un nœud de structure générique
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * Un nœud de structure interne contenant 256 enfants basés sur le 
         * caractère suivant.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];
          
          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * Un nœud de structure terminal qui effectue la comparaison des chaînes pour calculer où la clé 
         * donnée se situe entre lower et upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Lisez les points de coupure à partir du fichier de séquence donné.
         * @param fs le système de fichiers
         * @param p le chemin d'accès à lire
         * @param job la configuration de la tâche
         * @return les chaînes pour fractionner les partitions
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p, 
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * À partir d'un ensemble trié de points de coupure, créez une structure qui trouve rapidement
         * la partition correcte.
         * @param splits la liste des points de coupure
         * @param lower la limite inférieure des partitions 0..numPartitions-1
         * @param upper la limite supérieure des partitions 0..numPartitions-1
         * @param prefix le préfixe que nous avons déjà vérifié
         * @param maxDepth la profondeur maximum pour laquelle nous créons une structure
         * @return le nœud de structure qui divise correctement les fractionnements
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper, 
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // ajoutez un octet supplémentaire au préfixe
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial, 
                                         maxDepth);
          }
          // récupérez le reste
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }
        
      }
      
      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }

    }

Résumé
------

Cet exemple a montré comment exécuter une série de tâches MapReduce en utilisant Azure HDInsight où la sortie de données d'une tâche devient l'entrée de la tâche suivante dans la série.

Étapes suivantes
----------------

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Exemple : Estimateur de la valeur de Pi](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Exemple : Comptage de mots](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Exemple : Diffusion en continu en C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

