<properties linkid="manage-services-hdinsight-sample-pi-estimator" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight Pi estimator sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight Pi estimator sample" authors="bradsev" />

Exemple HDInsight Estimateur de la valeur de Pi
===============================================

Cette rubrique explique comment exécuter un programme MapReduce simple qui estime la valeur de la constante mathématique Pi avec Azure HDInsight en utilisant Azure PowerShell.

Le programme utilise une méthode statistique (quasi-Monte-Carlo) pour estimer la valeur de Pi. Des points placés aléatoirement à l'intérieur d'un carré unitaire se retrouvent également à l'intérieur d'un cercle inscrit dans ce carré avec une probabilité égale à l'aire du cercle, Pi/4. La valeur de Pi peut être estimée à partir de la valeur de 4R, où R est le rapport entre le nombre de points situés à l'intérieur du cercle et le nombre total de points situés à l'intérieur du carré. Plus l'échantillon de points est grand, plus l'estimation est précise.

Le code Java PiEstimator contenant les fonctions de mappage et de réduction est disponible pour inspection ci-dessous. Le programme de mappage génère un nombre spécifié de points placés aléatoirement à l'intérieur d'un carré unitaire, puis compte le nombre de ces points se trouvant à l'intérieur du cercle. Le programme de réduction cumule les points comptés par les mappeurs, puis estime la valeur de Pi à partir de la formule 4R, où R est le rapport entre le nombre de points comptés à l'intérieur du cercle et le nombre total de points situés à l'intérieur du carré.

Le script fourni pour cet exemple envoie une tâche Hadoop Java Archive (JAR) et il est configuré pour fonctionner avec une valeur de 16 mappages qui sont tous requis pour le calcul de 10 millions de points d'échantillonnage par les valeurs de paramètre. Ces dernières peuvent être modifiées pour améliorer la valeur estimée de Pi. Pour référence, voici les 10 premières décimales de Pi : 3,1415926535.

Le fichier .jar contenant les fichiers requis par Hadoop sur Azure pour déployer l'application est un fichier .zip disponible en téléchargement. Vous pouvez le décompresser grâce à différents utilitaires de compression, puis explorer les fichiers comme vous l'entendez.

La liste des autres exemples disponibles pour vous aider à vous habituer à une utilisation rapide de HDInsight pour exécuter des tâches MapReduce se trouve sur la page [Exécution des exemples HDInsight](/en-us/manage/services/hdinsight/howto-run-samples) où vous trouverez également des liens vers des instructions sur leur exécution.

**Vous apprendrez à effectuer les opérations suivantes :**

-   utiliser Azure PowerShell pour exécuter le programme MapReduce de l'Estimateur de la valeur de Pi sur Azure HDInsight ;
-   identification d'un programme MapReduce écrit en Java.

**Conditions préalables** :

-   Vous devez disposer d'un compte Azure. Pour connaître les options disponibles lors de la création d'un compte, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Vous devez avoir approvisionné un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de clusters, consultez la page [Approvisionnement de clusters HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Vous devez avoir installé Azure PowerShell et l'avoir configuré pour une utilisation avec votre compte. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Dans cet article
----------------

Cette rubrique vous explique comment exécuter l'exemple, présente le code Java du programme MapReduce de l'Estimateur de la valeur de Pi, résume ce que vous avez appris et décrit les étapes suivantes. Elle se compose des sections suivantes :

1.  [Exécution de l'exemple avec Azure PowerShell](#run-sample)
2.  [Code Java du programme MapReduce de l'Estimateur de la valeur de Pi](#java-code)
3.  [Résumé](#summary)
4.  [Étapes suivantes](#next-steps)

Exécution de l'exemple avec Azure PowerShell
--------------------------------------------

**Envoi de la tâche MapReduce**

1.  Ouvrez Azure PowerShell. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

         $subscriptionName = "<SubscriptionName>"   # nom d'abonnement Azure
         $clusterName = "<ClusterName>"             # nom de cluster HDInsight

3.  Exécutez la commande suivante pour créer une définition MapReduce :

         $piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

    > [WACOM.NOTE] Le fichier *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

    Le premier argument indique le nombre de mappages à créer (16 par défaut). Le deuxième argument indique le nombre d'échantillons générés par mappage (10 millions par défaut). Ce programme utilise ainsi 16\*10 millions = 160 millions de points aléatoires pour réaliser son estimation de Pi.

4.  Exécutez les commandes suivantes pour envoyer la tâche MapReduce et attendez qu'elle soit terminée :

         # Exécutez la tâche MapReduce Estimateur de la valeur de Pi.
         Select-AzureSubscription $subscriptionName
         $piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName

         # Attendre la fin de la tâche.  
         $piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

5.  Exécutez la commande suivante pour extraire la sortie standard de la tâche MapReduce :

         # Imprimez le résultat et le fichier d'erreurs standard de la tâche MapReduce
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

    En comparaison, voici les 10 premières décimales de Pi : 3,1415926535.

Code Java du programme MapReduce de l'Estimateur de la valeur de Pi
-------------------------------------------------------------------

	/**	
    * Licence pour Apache Software Foundation (ASF) sous un 
    * ou plusieurs contrats de licence de collaborateurs. Consultez le fichier NOTICE 
    * distribué avec ce travail pour plus d'informations 
    * concernant la propriété des droits d'auteur. La licence de ce fichier 
    * vous est cédée par ASF sous la licence Apache, version 2.0 (la 
    * « Licence ») ; vous êtes autorisé à utiliser ce fichier uniquement conformément 
    * à la Licence. Vous pouvez obtenir une copie de la Licence à la page 
    * 
    * http://www.apache.org/licenses/LICENSE-2.0 
    *
    * Sauf si la loi applicable l'exige ou si cela a été convenu dans la rédaction, le logiciel 
    * distribué sous la Licence est distribué « TEL QUEL »,
    * SANS GARANTIES NI CONDITIONS D'AUCUNE SORTE, que ce soit expressément ou implicitement.
    * Consultez la Licence pour connaître le langage spécifique régissant les permissions et 
    * les limitations sous la Licence. 
    * \
    * 
     package org.apache.hadoop.examples;

     mport java.io.IOException; 
     import java.math.BigDecimal;
     import java.util.Iterator;

     import org.apache.hadoop.conf.Configured;
     import org.apache.hadoop.fs.FileSystem;
     import org.apache.hadoop.fs.Path; 
     import org.apache.hadoop.io.BooleanWritable;
     import org.apache.hadoop.io.LongWritable; 
     import org.apache.hadoop.io.SequenceFile; 
     import org.apache.hadoop.io.Writable; 
     import org.apache.hadoop.io.WritableComparable; 
     import org.apache.hadoop.io.SequenceFile.CompressionType;
     import org.apache.hadoop.mapred.FileInputFormat;
     import org.apache.hadoop.mapred.FileOutputFormat;
     import org.apache.hadoop.mapred.JobClient; 
     import org.apache.hadoop.mapred.JobConf; 
     import org.apache.hadoop.mapred.MapReduceBase;
     import org.apache.hadoop.mapred.Mapper; 
     import org.apache.hadoop.mapred.OutputCollector;
     import org.apache.hadoop.mapred.Reducer;
     import org.apache.hadoop.mapred.Reporter; 
     import org.apache.hadoop.mapred.SequenceFileInputFormat;
     import org.apache.hadoop.mapred.SequenceFileOutputFormat; 
     import org.apache.hadoop.util.Tool;
     import org.apache.hadoop.util.ToolRunner;

    //Un programme de mappage et de réduction pour estimer la valeur de Pi    
    //en utilisant la méthode quasi-Monte-Carlo. 
    //  
    //Mappeur :    
    //Générez des points dans un carré unitaire    
    //puis comptez les points à l'intérieur/extérieur du cercle inscrit dans le carré.  
    //  
    //Réduction :   
    //Cumulez les résultats de points à l'intérieur/extérieur à partir des mappages. 
    //Posons numTotal = numInside + numOutside. 
    //La fraction numInside/numTotal est une approximation rationnelle de   
    //la valeur (aire du cercle)/(aire du carré) 
    //où l'aire du cercle inscrit est Pi/4   
    //et l'aire du carré unitaire est 1. 
    //La valeur de Pi est alors estimée à 4(numInside/numTotal).  
    //  

    public class PiEstimator extends Configured implements Tool {
    //répertoire tmp pour entrée/sortie    
    static private final Path TMP\_DIR = new Path(
    PiEstimator.class.getSimpleName() + "\_TMP\_3\_141592654");

    //suite Halton en 2 dimensions {H(i)},       
    //où H(i) est un point en 2 dimensions et i >= 1 est l'index.       
    //La suite Halton est utilisée pour générer des exemples de points pour une estimation de Pi.   
    private static class HaltonSequence {
    // Bases 
    static final int[] P = {2, 3};
    //Nombre maximum de chiffres autorisé 
    static final int[] K = {63, 40}; 
   
    private long index;
    private double[] x;
    private double[][] q;
    private int[][] d;

    //Initialisez à H(startindex) 
    //afin que la séquence commence par H(startindex+1). 
    HaltonSequence(long startindex) { 
    index = startindex;
    x = new double[K.length]; 
    q = new double[K.length][]; 
    d = new int[K.length][];
    for(int i = 0; i &lt; K.length; i++) {
    q[i] = new double[K[i]];
    d[i] = new int[K[i]]; 
    }

    for(int i = 0; i < K.length; i++) 
    { long k = index;
    x[i] = 0;

    for(int j = 0; j < K[i]; j++) { 
    q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
    d[i][j] = (int)(k % P[i]);
    k = (k - d[i][j])/P[i];
    x[i] += d[i][j] \* q[i][j]; 
    }
    } 
    }

    //Calculez le point suivant. 
    //Partez du principe que le point actuel est H(index).  
    //Calculez H(index+1).  
    //@return un point en 2 dimensions ayant pour coordonnées [0,1)^2        
    double[] nextPoint() {
    index++; 
    for(int i = 0; i < K.length; i++) {
    for(int j = 0; j < K[i]; j++) {
    d[i][j]++;
    x[i] += q[i][j];
    if (d[i][j] < P[i]) {
    break;
    } 
    d[i][j] = 0; 
    x[i] -= (j == 0? 1.0: q[i][j-1]);
    }
    } 
    return x;
    }
    }

    //Classe de mappeur pour l'estimation de Pi. 
    //Générez des points dans un carré unitaire, puis  
    //comptez les points à l'intérieur/extérieur du cercle inscrit dans le carré.   
    public static class PiMapper extends MapReduceBase
    implements Mapper&lt;LongWritable, LongWritable, BooleanWritable, LongWritable\> {

    //Méthode map.    
    //@param offset les exemples en commençant par l'exemple numéro (offset+1).   
    //@param size le nombre d'exemples pour ce mappage  
    //@param out la sortie {vrai->numInside, faux->numOutside}    
    //@param reporter   
    public void map(LongWritable offset,
    LongWritable size,
    OutputCollector&lt;BooleanWritable, LongWritable\> out,
    Reporter reporter) throws IOException {
  
    final HaltonSequence haltonsequence = new HaltonSequence(offset.get()); 
    long numInside = 0L; 
    long numOutside = 0L; 

    for(long i = 0; i < size.get(); ) {
    //générez des points dans un carré unitaire
    final double[] point = haltonsequence.nextPoint();

    //comptez les points à l'intérieur/extérieur du cercle inscrit dans le carré 
    final double x = point[0] - 0.5; 
    final double y = point[1] - 0.5;
    if (x*x + y*y > 0.25) {
    numOutside++; 
    } else {
    numInside++; 
    }

    //état du rapport 
    i++;
    if (i % 1000 == 0) {
    reporter.setStatus("Generated " + i + " samples."); 
    } 
    }

    //résultats du mappage de sortie 
    out.collect(new BooleanWritable(true), new LongWritable(numInside));
    out.collect(new BooleanWritable(false), new LongWritable(numOutside));
    } 
    }

    //Classe de réduction pour l'estimation de Pi.        
    //Cumulez les résultats de points à l'intérieur/extérieur à partir des mappages.     
    public static class PiReducer extends MapReduceBase 
    implements Reducer&lt;BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

    private long numInside = 0; 
    private long numOutside = 0;
    private JobConf conf; //configuration pour accéder au système de fichiers

    //Stockez la configuration des tâches. 
    @Override 
    public void configure(JobConf job) {
    conf = job; 
    }

    // Cumulez les résultats de nombre de points à l'intérieur/extérieur à partir des mappeurs.       
    // @param isInside Le point est-il à l'intérieur        
    // @param values L'itérateur d'une liste de nombres de points    
    // @param output factice, non utilisé ici.   
    // @param reporter  

    public void reduce(BooleanWritable isInside, 
    Iterator values,
    OutputCollector&lt;WritableComparable<?>, Writable\> output,
    Reporter reporter) throws IOException { 
    if (isInside.get()) {
    for(; values.hasNext(); numInside += values.next().get());
    } else { 
    for(; values.hasNext(); numOutside += values.next().get());
    }
    } 

    //Réduction de la tâche effectuée, écrivez la sortie vers un fichier.
    @Override 
    public void close() throws IOException { 
    //écrivez la sortie vers un fichier
    Path outDir = new Path(TMP\_DIR, "out");
    Path outFile = new Path(outDir, "reduce-out");
    FileSystem fileSys = FileSystem.get(conf); 
    SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
    outFile, LongWritable.class,
    LongWritable.class,
    CompressionType.NONE); 
    writer.append(new LongWritable(numInside), new LongWritable(numOutside));
    writer.close();
    } 
    }

    //Exécutez une tâche de mappage/réduction pour l'estimation de Pi.   
    //@return la valeur estimée de Pi.       
    public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf 
    )
    throws IOException {
    //configurez la tâche 
    jobConf.setJobName(PiEstimator.class.getSimpleName());

    jobConf.setInputFormat(SequenceFileInputFormat.class);

    jobConf.setOutputKeyClass(BooleanWritable.class); 
    jobConf.setOutputValueClass(LongWritable.class);
    jobConf.setOutputFormat(SequenceFileOutputFormat.class);

    jobConf.setMapperClass(PiMapper.class);
    jobConf.setNumMapTasks(numMaps);

    jobConf.setReducerClass(PiReducer.class);
    jobConf.setNumReduceTasks(1);

    // Désactivez l'exécution spéculative car le système de fichiers distribués (DFS) ne gère pas 
    // plusieurs éditeurs pour un même fichier.
    jobConf.setSpeculativeExecution(false);
 
    //configurez les répertoires d'entrée/de sortie 
    final Path inDir = new Path(TMP\_DIR, "in"); 
    final Path outDir = new Path(TMP\_DIR, "out"); 
    FileInputFormat.setInputPaths(jobConf, inDir);
    FileOutputFormat.setOutputPath(jobConf, outDir); 

    final FileSystem fs = FileSystem.get(jobConf); 
    if (fs.exists(TMP\_DIR)) {
    throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
     + " already exists. Please remove it first.");   
     }  
     if (!fs.mkdirs(inDir)) {   
     throw new IOException("Cannot create input directory " + inDir); 
     }  

     //générez un fichier d'entrée pour chaque tâche de mappage      
     try {
     for(int i=0; i < numMaps; ++i) {
     final Path file = new Path(inDir, "part"+i); 
     final LongWritable offset = new LongWritable(i * numPoints);   
     final LongWritable size = new LongWritable(numPoints); 
     final SequenceFile.Writer writer = SequenceFile.createWriter(
     fs, jobConf, file,
     LongWritable.class, LongWritable.class, CompressionType.NONE);
     try {
     writer.append(offset, size);
     } finally {
     writer.close();    
     }
     System.out.println("Wrote input for Map #"+i);
     }

     //démarrez une tâche de mappage/réduction      
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //lisez les sorties        
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //calculez la valeur estimée
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Analysez les arguments, puis exécutez une tâche de mappage/réduction. 
    //Imprimez la sortie en standard.       
    //@return une valeur différente de zéro s'il y a une erreur. Sinon, renvoyez 0.   
     public int run(String[] args) throws Exception {
     if (args.length != 2) {
     System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
     ToolRunner.printGenericCommandUsage(System.err);
     return -1;
     }

     final int nMaps = Integer.parseInt(args[0]);
     final long nSamples = Long.parseLong(args[1]);

     System.out.println("Number of Maps = " + nMaps);
     System.out.println("Samples per Map = " + nSamples);

     final JobConf jobConf = new JobConf(getConf(), getClass());
     System.out.println("Estimated value of Pi is "
     + estimate(nMaps, nSamples, jobConf));
     return 0;
     }

     //méthode main pour l'exécution en tant que commande autonome.       
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }

Résumé
------

Dans ce didacticiel, vous avez appris à exécuter une tâche MapReduce sur HDInsight et à utiliser des méthodes Monte-Carlo qui nécessitent et génèrent des ensembles de données volumineux pouvant être gérés par ce service.

Étapes suivantes
----------------

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Exemple : GraySort 10 Go](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Exemple : Comptage de mots](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Exemple : Diffusion en continu en C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

