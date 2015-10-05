<properties
   pageTitle="Topologies Apache Storm avec Visual Studio et C# | Microsoft Azure"
   description="Apprenez à créer des topologies Storm dans C# en créant une topologie de statistiques simple dans Visual Studio à l’aide des outils HDInsight pour Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

# Développement de topologies C# pour Apache Storm sur HDInsight à l’aide des outils Hadoop pour Visual Studio

Apprenez à créer une topologie Storm C# à l’aide des outils HDInsight pour Visual Studio. Ce didacticiel vous guide dans le processus de création d’un nouveau projet Storm dans Visual Studio, de test local et de déploiement vers Apache Storm sur un cluster HDInsight.

Vous apprendrez également à créer des topologies hybrides qui utilisent des composants Java et C#.

##Configuration requise

-	L’une des versions suivantes de Visual Studio :

	-	Visual Studio 2012 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

	-	Visual Studio 2013 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

	-	Visual Studio 2015 ou [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

-	Azure SDK 2.5.1 ou version ultérieure

-	HDInsight Tools pour Visual Studio : consultez la page [Prise en main de HDInsight Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour installer et configurer les outils HDInsight pour Visual Studio.

    > [AZURE.NOTE]Les outils HDInsight pour Visual Studio ne sont pas pris en charge sur Visual Studio Express.

-	Cluster Apache Storm sur HDInsight : consultez [Prise en main d’Apache Storm sur HDInsight](hdinsight-storm-getting-started.md) pour connaître les étapes de création d’un cluster.

	> [AZURE.NOTE]Les outils HDInsight pour Visual Studio n'acceptent actuellement que Storm sur les versions de cluster HDInsight 3.2.

##Modèles

Les outils HDInsight pour Visual Studio fournissent les modèles suivants :

| Type de projet | Illustre le |
| ------------ | ------------- |
| Application Storm | Un projet de topologie Storm vide |
| Exemple d’enregistreur SQL Azure Storm | Écriture dans la base de données SQL Azure |
| Exemple de lecteur DocumentDB Storm | Lecture d’Azure DocumentDB |
| Exemple d’enregistreur DocumentDB Storm | Écriture dans Azure DocumentDB |
| Exemple de lecteur EventHub Storm | Lecture d’Azure Event Hubs |
| Exemple d’enregistreur EventHub Storm | Écriture dans Azure Event Hubs |
| Exemple de lecteur HBase Storm | Lecture des clusters HBase sur HDInsight |
| Exemple d’enregistreur HBase Storm | Écriture dans les clusters HBase sur HDInsight |
| Exemple Storm hybride | Utilisation d’un composant Java |
| Exemple Storm | Une topologie de base des statistiques |

> [AZURE.NOTE]Les exemples de lecteur et d’enregistreur HBase utilisent l’API REST HBase pour communiquer avec un cluster HBase sur HDInsight, pas l’API Java HBase.

Dans les étapes de ce document, vous allez utiliser le type de projet Application Storm de base pour créer une topologie.

##Création d’une topologie C#

1.	Si vous n’avez pas encore installé la dernière version des outils HDInsight pour Visual Studio, consultez [Prise en main des outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2.	Ouvrez Visual Studio, sélectionnez **Fichier** > **Nouveau**, puis **Projet**.

3.	Dans la boîte de dialogue **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Application Storm**. En bas de la boîte de dialogue, entrez **Statistiques** comme nom d’application.

	![image](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4.	Une fois le projet créé, vous devez avoir les fichiers suivants :

	-	**Program.cs** : définit la topologie de votre projet. Notez que, par défaut, une topologie consistant en un seul spout et un seul bolt est créée.

	-	**Spout.cs** : un spout d’exemple émettant des nombres aléatoires.

	-	**Bolt.cs** : un bolt d’exemple qui conserve un décompte des nombres émis par le spout.

	Dans le cadre de la création du projet, les [packages SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) les plus récents sont téléchargés à partir de NuGet.

Dans les sections suivantes, vous allez modifier ce projet en une application de statistiques de base.

###Implémenter le spout

1.	Ouvrez **Spout.cs**. Les spouts sont utilisés pour lire des données dans une topologie à partir d’une source externe. Les principaux composants d’un spout sont :

	-	**NextTuple** : appelé par Storm lorsque le spout est autorisé à émettre de nouveaux tuples.

	-	**Accusé** (topologie transactionnelle uniquement) : gère les accusés de réception initiés par d’autres composants dans la topologie, pour les tuples envoyés depuis ce spout. Un accusé de réception de tuple permet au spout de savoir qu’il a été correctement traité par les composants en aval.

	-	**Fail** (topologie transactionnelle uniquement) : gère les tuples qui ne parviennent pas à traiter d’autres composants de la topologie. Cela permet d’émettre le tuple à nouveau afin qu’il puisse être traité une nouvelle fois.

2.	Remplacez le contenu de la classe **spout** par ce qui suit. Cela créera un spout qui émettra une phrase au hasard dans la topologie.

	```
	private Context ctx;
	private Random r = new Random();
	string[] sentences = new string[] {
	    "the cow jumped over the moon",
	    "an apple a day keeps the doctor away",
	    "four score and seven years ago",
	    "snow white and the seven dwarfs",
	    "i am at two with nature"
	};


	public Spout(Context ctx)
	{
	    // Set the instance context
	    this.ctx = ctx;


	    Context.Logger.Info("Generator constructor called");


	    // Declare Output schema
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // The schema for the default output stream is
	    // a tuple that contains a string field
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
	}


	// Get an instance of the spout
	public static Spout Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Spout(ctx);
	}


	public void NextTuple(Dictionary<string, Object> parms)
	{
	    Context.Logger.Info("NextTuple enter");
	    // The sentence to be emitted
	    string sentence;


	    // Get a random sentence
	    sentence = sentences[r.Next(0, sentences.Length - 1)];
	    Context.Logger.Info("Emit: {0}", sentence);
	    // Emit it
	    this.ctx.Emit(new Values(sentence));


	    Context.Logger.Info("NextTuple exit");
	}


	public void Ack(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}


	public void Fail(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}
	```

	Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

###Implémentation des bolts

1.	Supprimez le fichier **Bolt.cs** du projet.

2.	Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **Nouvel élément**. Dans la liste, sélectionnez **Bolt Storm**, entrez **Splitter.cs** comme nom. Répétez cela pour créer un deuxième bolt nommé **Counter.cs**.

	-	**Splitter.cs**: implémente un bolt qui fractionne les phrases en mots et émet un nouveau flux de mots.

	-	**Counter.cs**: implémente un bolt qui compte chaque mot et émet un nouveau flux de mots ainsi que le décompte de chaque mot.

	> [AZURE.NOTE]Bien que ces bolts lisent et écrivent simplement dans des flux, vous pouvez également utiliser un bolt pour communiquer avec une base de données, un service, etc.

3.	Ouvrez **Splitter.cs**. Notez qu’il n’a qu’une seule méthode par défaut : **Execute**. Elle est appelée lorsque le bolt reçoit un tuple à traiter. Ici, vous pouvez lire et traiter des tuples entrants et émettre des tuples sortants.

4.	Remplacez le contenu de la classe **Splitter** par le code suivant :

	```
	private Context ctx;


	// Constructor
	public Splitter(Context ctx)
	{
	    Context.Logger.Info("Splitter constructor called");
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // Input contains a tuple with a string field (the sentence)
	    inputSchema.Add("default", new List<Type>() { typeof(string) });
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // Outbound contains a tuple with a string field (the word)
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance of the bolt
	public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Splitter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the sentence from the tuple
	    string sentence = tuple.GetString(0);
	    // Split at space characters
	    foreach (string word in sentence.Split(' '))
	    {
	        Context.Logger.Info("Emit: {0}", word);
	        //Emit each word
	        this.ctx.Emit(new Values(word));
	    }


	    Context.Logger.Info("Execute exit");
	}
	```

	Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

5.	Ouvrez le fichier **Counter.cs** et remplacez le code existant par le suivant :

	```
	private Context ctx;


	// Dictionary for holding words and counts
	private Dictionary<string, int> counts = new Dictionary<string, int>();


	// Constructor
	public Counter(Context ctx)
	{
	    Context.Logger.Info("Counter constructor called");
	    // Set instance context
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string field - the word
	    inputSchema.Add("default", new List<Type>() { typeof(string) });


	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string and integer field - the word and the word count
	    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance
	public static Counter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Counter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the word from the tuple
	    string word = tuple.GetString(0);
	    // Do we already have an entry for the word in the dictionary?
	    // If no, create one with a count of 0
	    int count = counts.ContainsKey(word) ? counts[word] : 0;
	    // Increment the count
	    count++;
	    // Update the count in the dictionary
	    counts[word] = count;


	    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
	    // Emit the word and count information
	    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));


	    Context.Logger.Info("Execute exit");
	}
	```

	Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

###Définition de la topologie

Les spouts et les bolts sont organisés dans un graphique, qui définit la circulation des données entre les composants. Dans cette topologie, le graphique est le suivant :

![image de l’organisation des composants](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Des phrases sont émises par le spout, puis distribuées à des instances du bolt de fractionnement. Le bolt de fractionnement fractionne les phrases en mots, qui sont distribués au bolt de décompte.

Étant donné que le nombre de mots est conservé localement dans l'instance de décompte Counter, il convient de s'assurer que des mots donnés sont transmis à la même instance de bolt de décompte Counter, afin de garantir qu'une seule instance suit un mot donné. Toutefois, pour le bolt de fractionnement Splitter, peu importe quel bolt reçoit les phrases, nous souhaitons uniquement équilibrer la charge de phrases entre les instances.

Ouvrez **Program.cs**. La méthode importante ici est **ITopologyBuilder**, qui est utilisée pour définir la topologie qui est envoyée à Storm. Remplacez le contenu de **ITopologyBuilder** par le code suivant pour implémenter la topologie décrite précédemment :

```
    // Create a new topology named 'WordCount'
    TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

    // Add the spout to the topology.
    // Name the component 'sentences'
    // Name the field that is emitted as 'sentence'
    topologyBuilder.SetSpout(
        "sentences",
        Spout.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
        },
        1);
    // Add the splitter bolt to the topology.
    // Name the component 'splitter'
    // Name the field that is emitted 'word'
    // Use suffleGrouping to distribute incoming tuples
    //   from the 'sentences' spout across instances
    //   of the splitter
    topologyBuilder.SetBolt(
        "splitter",
        Splitter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
        },
        1).shuffleGrouping("sentences");

    // Add the counter bolt to the topology.
    // Name the component 'counter'
    // Name the fields that are emitted 'word' and 'count'
    // Use fieldsGrouping to ensure that tuples are routed
    //   to counter instances based on the contents of field
    //   position 0 (the word). This could also have been
    //   List<string>(){"word"}.
    //   This ensures that the word 'jumped', for example, will always
    //   go to the same instance
    topologyBuilder.SetBolt(
        "counter",
        Counter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
        },
        1).fieldsGrouping("splitter", new List<int>() { 0 });

    // Add topology config
    topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
    {
        {"topology.kryo.register","["[B"]"}
    });

    return topologyBuilder;
```

Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

##Envoi de la topologie

1.	Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet et sélectionnez **Envoyer à Storm sur HDInsight**.

	> [AZURE.NOTE]Si vous y êtes invité, entrez les informations d'identification de connexion à votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

2.	Sélectionnez votre Storm sur le cluster HDInsight dans la liste déroulante **Cluster Storm**, puis sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi est réussi ou non à l’aide de la fenêtre **Sortie**.

3.	Une fois la topologie envoyée avec succès, les **Topologies Storm** du cluster doivent apparaître. Sélectionnez la topologie **WordCount** à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.

	> [AZURE.NOTE]Vous pouvez également afficher les **Topologies Storm** à partir de l’**Explorateur de serveurs** en développant **Azure** > **HDInsight**, puis en cliquant avec le bouton droit sur Storm dans un cluster HDInsight et en sélectionnant **Afficher les topologies Storm**.

	Utilisez les liens pour les spouts et les bolts pour afficher des informations sur ces composants. Une nouvelle fenêtre s’ouvre pour chaque élément sélectionné.

4.	À partir de l’affichage **Résumé de la topologie**, cliquez sur **Supprimer** pour arrêter la topologie.

	> [AZURE.NOTE]Les topologies Storm poursuivent leur exécution jusqu'à ce qu'elles soient désactivées ou que le cluster soit supprimé.

##Topologie transactionnelle

La précédente ci-dessus est non transactionnelle. Les composants de la topologie n’implémentent aucune fonctionnalité de relecture des messages en cas d’échec de traitement par un composant dans la topologie. Pour un exemple de topologie transactionnelle, créez un nouveau projet et sélectionnez **Exemple Storm** comme type de projet.

Les topologies transactionnelles implémentent les opérations suivantes pour prendre en charge la relecture des données :

-	**Mise en cache des métadonnées** : le spout doit stocker les métadonnées relatives aux données émises, afin que les données puissent être récupérées et émises à nouveau en cas de défaillance. Dans la mesure où les données émises par l’exemple sont petites, les données brutes de chaque tuple sont stockées dans un dictionnaire pour la relecture.

-	**Accusé** : chaque bolt de la topologie peut appeler `this.ctx.Ack(tuple)` afin de signaler qu’il a traité un tuple avec succès. Lorsque tous les bolts ont signalé le traitement du tuple, la méthode`Ack` du spout est appelée. Cela permet au spout de supprimer les données mises en cache pour la relecture, étant donné que les données ont été entièrement traitées.

-	**Échec** : chaque bolt peut appeler `this.ctx.Fail(tuple)` pour indiquer que le traitement d’un tuple a échoué. L’échec se propage à la méthode `Fail` du spout, où le tuple peut être relu à l’aide des métadonnées mises en cache.

-	**ID de séquence** : lors de l’émission d’un tuple, un ID de séquence peut être spécifié. Il doit s’agir d’une valeur qui identifie le tuple pour le traitement de la relecture (accusé de réception et échec). Par exemple, le spout dans le projet **Exemple Storm** utilise les éléments suivants lors de l’émission de données :

	```
	this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
	```

	Il émet un nouveau tuple contenant une phrase dans le flux par défaut, avec la valeur d’ID de séquence contenue dans **lastSeqId**. Dans cet exemple, **lastSeqId** est simplement incrémenté pour chaque tuple émis.

Comme illustré dans le project **Exemple Storm**, un composant peut être défini comme transactionnel ou non lors de l’exécution, en fonction de la configuration.

##Topologie hybride

Les outils HDInsight pour Visual Studio peuvent également être utilisés pour créer des topologies hybrides, où certains composants sont en C# et d’autres en Java.

Pour un exemple de topologie hybride, créez un nouveau projet, puis sélectionnez **Exemple Storm hybride**. Cela créera un exemple commenté contenant plusieurs topologies qui illustrent les éléments suivants :

-	**Spout Java** et **bolt C#** : définis dans **HybridTopology\_javaSpout\_csharpBolt**

	-	Une version transactionnelle est définie dans **HybridTopologyTx\_javaSpout\_csharpBolt**

-	**Spout C#** et **bolt Java** : définis dans **HybridTopology\_javaSpout\_csharpBolt**

	-	Une version transactionnelle est définie dans **HybridTopologyTx\_csharpSpout\_javaBolt**

		> [AZURE.NOTE]Cette version montre également comment utiliser le code Clojure à partir d’un fichier texte en tant que composant Java.

Pour basculer entre la topologie utilisée lorsque le projet est soumis, il suffit de déplacer l’instruction `[Active(true)]` sur la topologie que vous souhaitez utiliser avant de l’envoyer au cluster.

> [AZURE.NOTE]Tous les fichiers Java requis sont fournis, dans le cadre de ce projet dans le dossier **JavaDependency**.

Les éléments suivants sont utilisés lors de la création et de la soumission d'une topologie hybride :

-	**JavaComponentConstructor** doit être utilisé pour créer une nouvelle instance de la classe Java pour un spout ou un bolt.

-	**microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** doit être utilisé pour sérialiser des données dans et à l’extérieur des composants Java à partir d’objets Java vers JSON.

-	Lors de l’envoi de la topologie au serveur, vous devez utiliser l’option **Configurations supplémentaires** pour spécifier les **chemins d’accès des fichiers Java**. Le chemin d’accès spécifié doit être le répertoire qui contient les fichiers JAR contenant vos classes Java.

###Concentrateurs d'événements Azure

SCP.Net 0.9.4.203 introduit une classe et une méthode inédites propres à l’utilisation du spout Event Hubs (spout Java qui lit à partir d’Event Hubs). Lorsque vous créez une topologie qui utilise ce spout, utilisez les méthodes suivantes :

-	Classe **EventHubSpoutConfig** : crée un objet qui contient la configuration du composant de spout

-	Méthode **TopologyBuilder.SetEventHubSpout** : ajoute le composant spout Event Hubs à la topologie

> [AZURE.NOTE]Bien qu’il soit plus facile d’utiliser le spout Event Hubs que les autres composants Java, vous devez toujours utiliser CustomizedInteropJSONSerializer pour sérialiser les données produites par spout.

##Mise à jour de SCP.NET

Les dernières versions de SCP.NET prennent en charge la mise à niveau du package via NuGet. Vous recevez une notification de mise à niveau dès qu’une nouvelle mise à jour est disponible. Pour rechercher manuellement une mise à niveau, procédez comme suit :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.

2. Dans le Gestionnaire de package, sélectionnez **Mises à jour**. Si une mise à jour est disponible, elle est affichée. Cliquez sur le bouton **Mettre à jour** du package pour l’installer.

> [AZURE.IMPORTANT]Si votre projet a été créé avec l’une des versions précédentes de SCP.NET qui n’utilisaient pas NuGet pour les mises à jour de package, vous devez procéder comme suit pour effectuer la mise à jour vers la nouvelle version :
>
> 1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
> 2. Dans le champ **Rechercher**, recherchez, puis ajoutez **Microsoft.SCP.Net.SDK** au projet.

##Résolution de problèmes

###Test local d’une topologie

Bien qu’il soit facile de déployer une topologie sur un cluster, dans certains cas, il sera peut-être nécessaire de tester une topologie localement. Utilisez les étapes suivantes pour exécuter et tester localement l’exemple de topologie de ce didacticiel localement dans votre environnement de développement.

> [AZURE.WARNING]Le test local fonctionne uniquement pour les topologies de base en C#. N’employez pas le test local pour les topologies hybrides ou celles qui utilisent plusieurs flux de données, car vous recevrez des erreurs.

1.	Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**. Dans les propriétés du projet, modifiez le **Type de sortie** sur **Application Console**.

	![type de sortie](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

	> [AZURE.NOTE]N’oubliez pas de modifier le **Type de sortie** sur **Bibliothèque de classes** avant de déployer la topologie sur un cluster.

2.	Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Ajouter** > **Nouvel élément**. Sélectionnez **Classe** et entrez **LocalTest.cs** comme nom de classe. Enfin, cliquez sur **Ajouter**.

3.	Ouvrez **LocalTest.cs** et ajoutez l’instruction **using** suivante en haut du fichier :

	```
	using Microsoft.SCP;
	```

4.	Utilisez le code suivant comme contenu pour la classe **LocalTest** :

	```
	// Drives the topology components
	public void RunTestCase()
	{
	    // An empty dictionary for use when creating components
	    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();


	    #region Test the spout
	    {
	        Console.WriteLine("Starting spout");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext spoutCtx = LocalContext.Get();
	        // Get a new instance of the spout, using the local context
	        Spout sentences = Spout.Get(spoutCtx, emptyDictionary);


	        // Emit 10 tuples
	        for (int i = 0; i < 10; i++)
	        {
	            sentences.NextTuple(emptyDictionary);
	        }
	        // Use LocalContext to persist the data stream to file
	        spoutCtx.WriteMsgQueueToFile("sentences.txt");
	        Console.WriteLine("Spout finished");
	    }
	    #endregion


	    #region Test the splitter bolt
	    {
	        Console.WriteLine("Starting splitter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext splitterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


	        // Set the data stream to the data created by the spout
	        splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            splitter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        splitterCtx.WriteMsgQueueToFile("splitter.txt");
	        Console.WriteLine("Splitter bolt finished");
	    }
	    #endregion


	    #region Test the counter bolt
	    {
	        Console.WriteLine("Starting counter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext counterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Counter counter = Counter.Get(counterCtx, emptyDictionary);


	        // Set the data stream to the data created by splitter bolt
	        counterCtx.ReadFromFileToMsgQueue("splitter.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            counter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        counterCtx.WriteMsgQueueToFile("counter.txt");
	        Console.WriteLine("Counter bolt finished");
	    }
	    #endregion
	}
	```

	Prenez un moment pour lire les commentaires du code. Ce code utilise **LocalContext** pour exécuter les composants dans l’environnement de développement, en conservant le flux de données entre les composants dans des fichiers texte sur le disque local.

5.	Ouvrez **Program.cs** et ajoutez le code suivant à la méthode **Main** :

	```
	Console.WriteLine("Starting tests");
	System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
	// Initialize the runtime
	SCPRuntime.Initialize();


	//If we are not running under the local context, throw an error
	if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
	{
	    throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
	}
	// Create test instance
	LocalTest tests = new LocalTest();
	// Run tests
	tests.RunTestCase();
	Console.WriteLine("Tests finished");
	Console.ReadKey();
	```

6.	Enregistrez les modifications, puis utilisez **F5** ou sélectionnez **Débogage** > **Démarrer le débogage** pour démarrer le projet. Une fenêtre de console doit apparaître et enregistrer les statuts avec la progression des tests. Lorsque le message **Tests terminés** s’affiche, appuyez sur n’importe quelle touche pour fermer la fenêtre.

7.	Utilisez l’**Explorateur Windows** pour accéder au répertoire qui contient votre projet, par exemple, **C:\\Users < votre\_nom\_utilisateur > \\Documents\\Visual Studio 2013\\Projects\\WordCount\\WordCount**. Dans ce répertoire, ouvrez **Bin**, puis cliquez sur **Débogage**. Vous devez voir les fichiers texte produits lors de l’exécution de tests : sentences.txt, counter.txt et splitter.txt. Ouvrez chaque fichier texte et inspectez les données.

	> [AZURE.NOTE]Les chaînes de données sont conservées sous forme de tableau de valeurs décimales dans ces fichiers. Par exemple, le résultat [[97,103,111]] dans le fichier **splitter.txt** correspond au mot « and ».

Le test local d'une application de statistiques de base est relativement simple. Toutefois la valeur réelle apparaît lorsque vous disposez d'une topologie complexe qui communique avec des sources de données externes ou effectue l'analyse de données complexes. Lorsque vous travaillez sur un tel projet, vous devrez peut-être définir des points d’arrêt et parcourir le code de vos composants afin d’isoler les problèmes.

> [AZURE.NOTE]Veillez à définir le **Type de projet** sur **Bibliothèque de classes** avant le déploiement vers un Storm sur un cluster HDInsight.

###Enregistrement d’informations

Vous pouvez facilement enregistrer des informations à partir de vos composants de topologie à l’aide de `Context.Logger`. Par exemple, le texte suivant crée une entrée de journal d’information :

```
Context.Logger.Info("Component started");
```

Les informations enregistrées peuvent être affichées dans le **journal du service Hadoop**, qui se trouve dans l’**Explorateur de serveurs**. Développez l’entrée pour votre Storm sur le cluster HDInsight , puis le **journal du service Hadoop**. Enfin, sélectionnez le fichier journal à afficher.

> [AZURE.NOTE]Les journaux sont stockés dans le compte de stockage Azure utilisé par votre cluster. S’il s’agit d’un abonnement différent de celui avec lequel vous êtes connecté à Visual Studio, vous devez vous ouvrir une session dans l’abonnement contenant le compte de stockage pour afficher ces informations.

###Afficher les informations relatives aux erreurs

Pour afficher les erreurs qui se sont produites dans une topologie en cours d’exécution, procédez comme suit :

1.	À partir de l’**Explorateur de serveurs**, cliquez avec le bouton droit sur le Storm d’un cluster HDInsight et sélectionnez **Afficher les topologies Storm**.

2.	Pour les **Spouts** et **Bolts**, la colonne **Dernière erreur** contiendra les informations sur la dernière erreur qui s’est produite.

3.	Sélectionnez l’**Id de spout** ou l’**Id de bolt** pour le composant ayant une erreur répertoriée. Sur la page de détails, d’autres informations sont affichées dans la section **Erreurs** en bas de la page.

4.	Pour obtenir plus d’informations, sélectionnez un **Port** à partir de la section **Exécuteurs** de la page afin d’afficher le journal de travail Storm pour les dernières minutes.

##Étapes suivantes

Maintenant que vous avez appris à développer et à déployer des topologies Storm à partir des outils HDInsight pour Visual Studio, découvrez comment [traiter les événements à partir du concentrateur d’événements Azure avec Storm sur HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Pour obtenir un exemple de topologie C# qui fractionne les données de flux en plusieurs flux de données, consultez la rubrique [Exemple c# Storm](https://github.com/Blackmist/csharp-storm-example).

Pour plus d’informations sur la création de topologies C#, accédez à [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Pour plus d’informations sur l’utilisation de HDInsight, ou pour obtenir davantage d’exemples Storm sur HDinsight, consultez les rubriques suivantes :

**Apache Storm sur HDInsight**

-	[Déploiement et analyse des topologies avec Apache Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology.md)

-	[Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop sur HDInsight**

-	[Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

-	[Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

-	[Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase sur HDInsight**

-	[Prise en main de HBase sur HDInsight](../hdinsight-hbase-get-started.md)

<!---HONumber=Sept15_HO4-->