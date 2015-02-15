<properties 
	pageTitle="Développement d'applications de traitement de données de diffusion avec SCP.NET et C# sur Storm | Azure" 
	description="Découvrez comment développer des applications de traitement de données de diffusion avec SCP.NET et C# sur Storm dans HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="weedqian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="qixia"/>

#Développement d'applications de traitement de données de diffusion en C# avec la plateforme SCP (Stream Computing Platform) et Storm dans HDInsight

La plateforme SCP (Stream Computing Platform) permet de créer des applications de traitement de données en temps réel, fiables, distribuées, cohérentes et à hautes performances avec .NET. Elle est basée sur [Apache Storm](http://storm.incubator.apache.org/), un système de traitement de flux en temps réel Open Source, disponible avec HDInsight.

Cet article vous apprendra les éléments suivants :

* Présentation de SCP et fonctionnement avec Storm

* Création d'une solution SCP

* Test d'une solution SCP

* Déploiement d'une solution SCP vers un cluster Storm sur HDInsight

##Conditions préalables

* Un abonnement Azure

* Un cluster Storm sur HDInsight

* Visual Studio 2010 ou 2013

##Sommaire

* [SCP et Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP et Storm

Apache Storm est un système de calcul distribué qui s'exécute sur les clusters Hadoop et vous permet d'effectuer le traitement des données en temps réel. Bien que Storm s'exécute sur la machine virtuelle Java (JVM), il a été conçu pour que des solutions (connues sous le nom de **topologies**) puissent être implémentées dans différents langages de programmation. Vous pouvez même créer une topologie qui associe des composants écrits dans plusieurs langages.

SCP fournit les bibliothèques qui facilitent la création de solutions Storm avec .NET. Les clusters Storm sur HDInsight comprennent les composants côté serveur nécessaires pour exécuter les solutions SCP que vous créez.

Pour plus d'informations sur Storm sur HDInsight, consultez la rubrique [Présentation de HDInsight Storm](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-storm-overview/).

###Conception d'une application SCP

SCP fournit les interfaces qui vous permettent de créer les composants Storm suivants :

* Spout : consomme les données d'une source (fichier, base de données, API, etc.) et émet un ou plusieurs flux de tuples (une liste ordonnée d'éléments)
* Bolt : consomme (et émet éventuellement) un ou plusieurs flux
* Topologie : définit la manière dont les données circulent entre les spouts et les bolts, le parallélisme des spouts et des bolts, ainsi que les informations de configuration

> [AZURE.NOTE] Les spouts et les bolts implémentent également un traitement générique. Par exemple, un spout peut fractionner un message entrant en plusieurs tuples ou émettre un seul tuple et laisser un bolt extraire les valeurs dont il a besoin. De même, si vous avez besoin d'écrire des données dans un magasin de données, l'implémentation a lieu dans un bolt.

La conception d'une solution implique les éléments suivants :

* Quelle source de données allez-vous consommer ? Vous devez avoir un spout qui implémente ceci.
* Quel traitement doit être effectué ? Vous devez implémenter ceci dans les spouts et/ou les bolts.
* Si le traitement est réparti entre plusieurs bolts, quel est le flux de données entre ces bolts ? Vous devez décrire ceci via la topologie.
* Comment le traitement sera-t-il distribué parmi les nœuds dans le cluster HDInsight ? Vous devez décrire ceci via la topologie.

##Installation du Kit de développement logiciel (SDK) SCP

Le Kit de développement logiciel (SDK) SCP est fourni sur le cluster Storm sur HDInsight. Après avoir [créé un cluster Storm sur HDInsight](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-storm-getting-started/), procédez comme suit pour télécharger le Kit de développement logiciel (SDK) dans votre environnement de développement local.

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).

2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.

3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.

4. En haut de la page, cliquez sur **CONFIGURATION**.

5. En bas de la page, cliquez sur **ACTIVER DISTANT**.

6. Dans l'Assistant Configurer le Bureau à distance, entrez un **nom d'utilisateur** et un **mot de passe** pour le Bureau à distance. Entrez une date d'expiration dans la zone **EXPIRE LE**, puis cliquez sur la coche.

7. Une fois le Bureau à distance activé, cliquez sur **CONNECTER** en bas de la page et suivez les instructions.

8. Une fois connecté au cluster via le Bureau à distance, ouvrez l'**Explorateur de fichiers** et entrez **%storm_home%\examples** dans la barre d'adresse.

9. Cliquez avec le bouton droit sur le dossier **SDK**, puis sélectionnez **Copier**.

10. Dans votre environnement de développement local, ouvrez l'**Explorateur de fichiers** et accédez à l'emplacement où vous voulez stocker le Kit de développement logiciel (SDK). Cliquez avec le bouton droit et sélectionnez **Coller**.

##Création d'une application SCP

1. Dans Visual Studio, créez un projet pour une **application console**. Nommez cette solution **WordCount**.

2. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Ajouter une référence**.

3. Cliquez sur le bouton **Parcourir** en bas du **Gestionnaire de références**, puis accédez au dossier SDK que vous avez téléchargé précédemment. Sélectionnez **Microsoft.SCPNet.dll** et **Microsoft.SCPLogger.dll**, puis cliquez sur **Ajouter**. Cliquez sur **OK** pour fermer la fenêtre Gestionnaire de références.

###Création du spout

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **WordCount**, puis sélectionnez **Ajouter | Nouvel élément**. Sélectionnez **Classe** et entrez ensuite **WordSpout.cs** comme nom. Pour terminer, cliquez sur **Ajouter**.

2. Ouvrez le fichier **WordSpout.cs** et remplacez le code existant par le suivant : Veillez à lire les commentaires pour comprendre comment fonctionne ce code.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###Création de tests

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **WordCount**, puis sélectionnez **Ajouter | Nouvel élément**. Sélectionnez **Classe** et entrez **LocalTest.cs** comme nom. Pour terminer, cliquez sur **Ajouter**.

2. Ouvrez le fichier **LocalTest.cs** et remplacez le code existant par le suivant : Le code utilisé pour tester le spout est très similaire à celui qui sera utilisé pour tester les bolts.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. Ouvrez le fichier **Program.cs** et remplacez le code existant par le suivant :

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. Exécutez l'application. Une fois l'exécution terminée, accédez au répertoire **WordCount\bin\debug** sous votre projet Visual Studio. Vous trouverez normalement à cet emplacement un fichier nommé **spout.txt**, contenant les données émises par le spout pendant ce test. Le contenu devrait être semblable au texte suivant.

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] Dans les lignes ci-dessus, " Data " est la chaîne émise par le spout, mais stockée sous la forme d'un tableau d'octets. Par exemple, `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` correspond à "an apple a day keeps the doctor away".

###Création des bolts

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **WordCount**, puis sélectionnez **Ajouter | Nouvel élément**. Sélectionnez **Classe** et entrez **SplitterBolt.cs** comme nom. Pour terminer, cliquez sur **Ajouter**. Répétez l'opération pour ajouter une classe nommée **CounterBolt.cs**.

2. Ouvrez le fichier **SplitterBolt.cs** et remplacez le code existant par le suivant :

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. Ouvrez le fichier **CounterBolt.cs** et remplacez le contenu par le suivant :

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] Ce bolt émet un flux utile pour les tests. Dans une solution du monde réel, vous stockeriez les données dans une base de données, une file d'attente ou un autre magasin persistant à la fin du traitement.

3. Ouvrez **LocalTest.cs** et ajoutez les tests suivants pour SplitterBolt et CounterBolt après le précédent bloc de tests de spout.

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. Exécutez la solution. Une fois l'exécution terminée, les fichiers suivants doivent normalement se trouver dans le répertoire **WordCount\bin\debug**.

    * **spout.txt** - Données émises par WordSpout
    * **splitter.txt** - Données émises par SplitterBolt
    * **counter.txt** - Données émises par CounterBolt

###Ajout de code à un cluster

1. Ouvrez le fichier **Program.cs** et remplacez la ligne `//Code to run on HDInsight cluster will go here` par la suivante, puis régénérez le projet.

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. Créez un fichier nommé **WordCount.spec** et utilisez le code suivant comme contenu. Ce fichier définit la topologie : les composants, le nombre d'instances à créer à travers les nœuds dans le cluster et la façon dont les données circulent entre elles. Il est écrit à l'aide du [langage de spécification de la topologie](#spec).

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. Connectez-vous au cluster Storm à l'aide du Bureau à distance et copiez le dossier **bin\debug** de votre projet WordCount local dans le cluster Storm. Par exemple, copiez-le dans le dossier **%storm_home%\examples** et renommez-le **WordCount**.

3. Copiez également le fichier **WordCount.spec** sur le serveur HDInsight. Placez-le dans le répertoire **%storm_home%\examples**.

4. Dans le cluster Storm, utilisez l'icône **Ligne de commande Storm** du Bureau pour ouvrir une ligne de commande, puis utilisez la commande suivante pour commencer la topologie WordCount.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    Cette commande crée un dossier intitulé **temp**, puis utilise la spécification et les fichiers de la solution WordCount pour créer un fichier **WordCount.jar**, lequel est ensuite envoyé à Storm.

5. Une fois l'exécution de la commande terminée, ouvrez l'**Interface utilisateur de Storm** à partir du Bureau. La topologie **WordCount** doit normalement être répertoriée. Vous pouvez sélectionner la topologie à partir de l'**Interface utilisateur de Storm** pour afficher les statistiques.

6. Pour arrêter la topologie, sélectionnez **WordCount** dans l'**interface utilisateur de Storm**, puis sélectionnez **Supprimer** dans **Actions de topologie**.

##Résumé

Aux étapes précédentes, vous avez appris à créer, à tester et à déployer une topologie de comptage de mots de base, créée à l'aide de la plateforme SCP (Stream Computing Platform). Pour consulter d'autres exemples, accédez au répertoire **%storm_home%\examples** sur votre cluster HDInsight. Pour des informations plus détaillées sur SCP, reportez-vous à la référence suivante.

##Référence de SCP

###Interface de plug-in SCP

Les applications SCP (appelées plug-ins) sont branchées sur le pipeline Storm. Un plug-in est une application console .NET qui implémente une ou plusieurs des interfaces suivantes.  

-	**ISCPSpout** : à utiliser lors de l'implémentation d'un spout non transactionnel.
- 	**ISCPTxSpout** : à utiliser lors de l'implémentation d'un spout transactionnel.
-	**ISCPBolt** : à utiliser lors de l'implémentation d'un bolt non transactionnel.
-	**ISCPBatchBolt** : à utiliser lors de l'implémentation d'un bolt transactionnel.

####ISCPPlugin

ISCPPlugin est l'interface de base dont héritent toutes les autres interfaces SCP. Actuellement, c'est une interface factice.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout est l'interface pour les composants spout non transactionnels.  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Lorsque `NextTuple()` est appelé, le code peut émettre un ou plusieurs tuples. S'il n'y a rien à émettre, cette méthode doit effectuer un renvoi sans émettre quoi que ce soit.

`Ack()` et  `Fail()` sont appelés uniquement lorsque l'accusé de réception (ack) est activé dans le fichier spec.  `seqId` est utilisé pour identifier le tuple qui a été reçu ou qui a échoué. Si le mécanisme d'accusé de réception (ack) est activé dans une topologie non transactionnelle, la fonction d'émission suivante doit être utilisée dans le spout :  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

Si l'accusé de réception n'est pas pris en charge dans une topologie non transactionnelle, les fonctions `Ack()` et `Fail()` peuvent être laissées vides.  

Le paramètre `parms` de ces fonctions est un objet Dictionary vide, réservé pour un usage ultérieur.  

> [AZURE.NOTE] `NextTuple()`, `Ack()` et `Fail()` sont appelés dans une boucle étroite sur un seul thread. S'il n'y a aucun tuple à émettre, pensez à utiliser `sleep` pour un court laps de temps (de l'ordre de 10 millisecondes) afin de conserver les cycles de processeur.

####ISCPTxSpout

ISCPTxSpout est l'interface pour les spouts transactionnels.  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` est appelé pour commencer une nouvelle transaction.  `seqId` est utilisé pour identifier la transaction, également utilisée dans  `Ack()` et  `Fail()`. Les données émises par  `NextTx()`seront stockées dans ZooKeeper pour prendre en charge la relecture. Comme la capacité de stockage de ZooKeeper est très limitée, l'utilisateur doit seulement émettre des métadonnées, et non des données en bloc, dans un spout transactionnel.  

Si une transaction échoue, Storm la relit automatiquement. Normalement, il ne faut donc pas appeler la fonction Fail(). Mais si SCP peut contrôler les métadonnées émises par un spout transactionnel, il peut appeler la fonction Fail() si les métadonnées ne sont pas correctes.

Le paramètre  `parms` de ces fonctions est un objet Dictionary vide, réservé pour un usage ultérieur.

> [AZURE.NOTE] `NextTx()`, `Ack()` et `Fail()` sont appelés dans une boucle étroite sur un seul thread. S'il n'y a aucun tuple à émettre, pensez à utiliser `sleep` pour un court laps de temps (de l'ordre de 10 millisecondes) afin de conserver les cycles de processeur.

####ISCPBolt

ISCPBolt est l'interface pour les composants bolt non transactionnels.  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Lorsqu'un nouveau tuple est disponible, la fonction  `Execute()` est appelée pour son traitement.

####ISCPBatchBolt

ISCPBatchBolt est l'interface pour les composants bolt transactionnels.  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Lorsqu'un nouveau tuple est disponible, la fonction  `Execute()` est appelée pour son traitement. `FinishBatch()` est appelé dès que cette transaction est terminée.

Le paramètre  `parms` de ces fonctions est un objet Dictionary vide, réservé pour un usage ultérieur.

> [AZURE.NOTE] Les bolts qui implémentent  `ISCPBatchBolt`, peuvent obtenir  `StormTxAttempt` à partir de  `parms`. `StormTxAttempt` peut être utilisé pour déterminer si un tuple est l'original ou s'il s'agit d'une tentative de relecture. Cela se fait généralement au niveau du bolt de validation. Pour une démonstration, consultez l'exemple **HelloWorldTx**.  

###Modèle d'objet

SCP.NET fournit également un ensemble d'objets clés pour les développeurs.

* `Context` : fournit des informations sur l'environnement en cours d'exécution à l'application.

* `StateStore` : fournit des services de métadonnées, une génération de séquence unitone et une coordination sans attente. Il est possible de développer des abstractions concurrentielles distribuées et globales sur  `StateStore`, notamment des verrous distribués, des files d'attente distribuées, des barrières et des services de transaction.

* `SCPRuntime` : initialise l'environnement d'exécution et lance les plug-ins lorsqu'une solution est exécutée sur Storm.

* `LocalContext` : fournit des méthodes pour sérialiser et désérialiser les tuples émis vers des fichiers locaux lorsqu'une application est testée en local dans Visual Studio.

####Context

Chaque instance ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) dispose d'une instance Context correspondante. La fonctionnalité fournie par un objet Context peut être divisée en deux parties

* **Statique** : disponible dans l'intégralité du processus C#.
* **Dynamique** :  disponible pour l'instance spécifique.  

**Contexte statique**

* `public static ILogger Logger = null;` : fourni pour la journalisation.  

* `public static SCPPluginType pluginType;` : obtient le type de plug-in du processus C#. Si le processus C# est exécuté dans un mode de test local (sans Java), le type de plug-in est " SCP_NET_LOCAL ".

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` : obtient les paramètres de configuration de la machine virtuelle Java (JVM). Les paramètres sont transférés à partir de la machine virtuelle Java (JVM) lors de l'initialisation du plug-in. `Config` contient deux dictionnaires :

    * `public Dictionary<string, Object> stormConf { get; set; }` :  contient des paramètres définis par Storm.

    * `public Dictionary<string, Object> pluginConf { get; set; }` : contient des paramètres définis par SCP.

* `public static TopologyContext TopologyContext { get; set; } `  : obtient le contexte de la topologie. C'est utile surtout pour les composants avec multiparallélisme. La suite montre comment accéder au contexte de la topologie

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**Contexte dynamique**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` :  déclare les schémas de sortie et d'entrée pour les flux. L'exemple suivant déclare un schéma d'entrée constitué d'un unique tuple de chaîne et d'un schéma de sortie constitué à la fois d'un tuple de chaîne et d'un tuple d'entier

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` : émet un ou plusieurs tuples vers le flux par défaut. L'exemple suivant émet deux tuples vers le flux par défaut

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` : émet un ou plusieurs tuples vers le flux spécifié. L'exemple suivant émet deux tuples vers un flux nommé  'mystream'

        this.ctx.Emit("mystream", new Values(word, count));

Lorsque vous employez des spouts et des bolts non transactionnels pour lesquels ack a été activé, utilisez le code suivant.

* `public abstract void Emit(string streamId, List<object> values, long seqId);` : émet un ou plusieurs tuples et un identificateur de séquence **depuis un spout** vers le flux spécifié. L'exemple suivant émet un tuple et un identificateur de séquence vers le flux par défaut

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` : émet un ou plusieurs tuples et un identificateur de séquence **depuis un bolt** vers le flux spécifié. Les tuples émis sont ancrés aux tuples entrants spécifiés en tant que  `anchors`. Cela permet aux acks de refluer vers le pipeline en suivant la chaîne des tuples entrants/sortants. L'exemple suivant émet un tuple et un identificateur de séquence vers le flux par défaut, et ancre les tuples émis sur les tuples entrants contenus dans `tuple`.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` : accuse réception d'un tuple. Appelle la fonction `ISCPSpout.Ack` du spout qui a produit initialement le tuple.

* `public abstract void Fail(SCPTuple tuple);` : fait échouer un tuple. Appelle la fonction  `ISCPSpout.Fail` du spout qui a produit initialement le tuple.

####StateStore

Les applications SCP peuvent utiliser l'objet `State` pour conserver des informations dans ZooKeeper, notamment pour la topologie atransactionnelle. Ceci permet aux spouts transactionnels de récupérer l'état à partir de ZooKeeper et de redémarrer le pipeline en cas d'incident.  

L'objet `StateStore` fournit les méthodes suivantes.

* `public static StateStore Get(string storePath, string connStr);` : obtient un  `StateStore` pour le chemin d'accès et la chaîne de connexion donnés.

* `public State Create();` : crée un objet  `State` dans cette instance de magasin d'états.

* `public IEnumerable<State> GetUnCommitted();` : obtient tous les objets `State` qui ne sont pas validés, à l'exclusion des états abandonnés.

* `public IEnumerable<State> States();` : obtient tous les objets 'State' du `StateStore`


* `public T Get<T>(string info = null);` : obtient un objet `State` ou `Registry`.

    * `info` : nom du  `Registry` à obtenir. Utilisé seulement pour la récupération d'un objet  `Registry`.

    * `T` : type de `State` ou `Registry`.

* `public IEnumerable<Registry> Commited();` : obtient les objets `Registry` qui contiennent un  `State` validé.

* `public IEnumerable<Registry> Aborted();` : obtient les objets `Registry` qui contiennent un  `State` abandonné.

* `public State GetState(long stateId)` : obtient l'objet  `State` pour l'ID d'état spécifié.

L'objet **State** fournit les méthodes suivantes.

* `public void Commit(bool simpleMode = true);` : définit l'état de l'objet `State` à valider.

    > [AZURE.NOTE] Lorsque `simpleMode` est défini sur true, cela entraîne simplement la suppression du ZNode correspondant dans ZooKeeper. Sinon, le ZNode en cours est supprimé, puis un nouveau nœud est ajouté au  `COMMITTED_PATH`.

* `public void Abort();` : définit l'état de l'objet `State` à abandonner.

* `public void PutAttribute<T>(string key, T attribute);` : définit une valeur d'attribut pour la clé donnée.

    * `key` : clé pour laquelle l'attribut est défini.

    * `attribute` : valeur de l'attribut.

* `public T GetAttribute<T>(string key);` : obtient la valeur de l'attribut pour la clé donnée.

####SCPRuntime

SCPRuntime fournit les méthodes suivantes.

* `public static void Initialize();` : initialise l'environnement d'exécution SCP. Lorsque cette méthode est appelée, le processus C# se connecte à la machine virtuelle Java (JVM) et obtient les paramètres de configuration et le contexte de la topologie

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` : lance la boucle de traitement des messages Dans cette boucle, le plug-in C# reçoit les messages de la machine virtuelle Java (JVM) (y compris les tuples et les signaux de contrôle), puis traite les messages

    * `pluginDelegate` : délégué qui peut renvoyer un objet qui implémente ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` : reçoit des tuples de la file d'attente.

* `void WriteMsgQueueToFile(string filepath, bool append = false);` : fait persister les tuples dans un fichier.

* `void ReadFromFileToMsgQueue(string filepath);` : lit les tuples à partir d'un fichier.


###<a id="spec"></a>Langage de spécification de topologie

La spécification de topologie SCP est un langage de domaine spécifique pour décrire et configurer les topologies SCP. Elle est basée sur le [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html) de Storm.  

Utilisez les éléments suivants pour définir une topologie.

|Nouvelles fonctions|	Paramètres|	Description
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 Permet de définir une topologie transactionnelle avec un nom de topologie, une carte de définition de spouts et une carte de définition de bolts.
|**scp-tx-spout**|	exec-name<br> args<br> fields|	Permet de définir un spout transactionnel. Exécutera l'application avec ***exec-name*** en utilisant ***args***.<br><br>Le paramètre ***fields*** correspond aux champs de sortie du spout.
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	Permet de définir un lot bolt transactionnel. Exécutera l'application avec ***exec-name*** en utilisant ***args***.<br><br>Le paramètre *fields* correspond aux champs de sortie du bolt
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	Permet de définir un validateur bolt transactionnel. Exécutera l'application avec ***exec-name*** en utilisant args.<br><br>Le paramètre ***fields*** correspond aux champs de sortie du bolt.
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	Permet de définir une topologie non transactionnelle avec un nom de topologie,  une carte de définition de spouts et une carte de définition de bolts.
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	Permet de définir un spout non transactionnel. Exécutera l'application avec ***exec-name*** en utilisant ***args***.<br><br>Le paramètre ***fields*** correspond aux champs de sortie du spout.<br><br>Le paramètre ***parameters*** est facultatif ; vous pouvez l'utiliser pour spécifier certains paramètres tels que " nontransactional.ack.enabled ".
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	Permet de définir un bolt non transactionnel. Exécutera l'application avec ***exec-name*** en utilisant ***args***.<br><br>Le paramètre ***fields*** correspond aux champs de sortie du bolt.<br><br>Le paramètre ***parameters*** est facultatif ; vous pouvez l'utiliser pour spécifier certains paramètres tels que " nontransactional.ack.enabled ".

Vous pouvez utiliser les mots clés suivants lors de la définition d'une topologie

|Mots clés|	Description
|---------|------------
|**:name**|	Permet de définir le nom de la topologie.
|**:topology**|	Permet de définir la topologie en utilisant les fonctions précédentes et d'en intégrer d'autres.
|**:p**|	Permet de définir l'indicateur de parallélisme pour chaque spout ou bolt.
|**:config**|	Permet de définir le paramètre de configuration ou de mettre à jour celui existant.
|**:schema**|	Permet de définir le schéma de flux.

Paramètres fréquemment utilisés

|Paramètre|	Description
|---------|------------
|**"plugin.name"**|	Nom du fichier .exe du plug-in C#
|**"plugin.args"**|	Arguments du plug-in
|**"output.schema"**|	Schéma de sortie
|**"nontransactional.ack.enabled"**|	Indique si le mécanisme d'accusé de réception (ack) est activé pour la topologie non transactionnelle.

Les spécifications de topologie peuvent être soumises directement au cluster storm pour une exécution via la commande ***runspec***, qui se trouve dans le répertoire **%storm_home%\bin** des clusters Storm.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] Le paramètre ***resource-dir*** est facultatif. Vous devez le spécifier lorsque vous voulez exécuter une application C#. Ce répertoire contient l'application, ses dépendances et ses configurations.  

Le paramètre ***classpath*** est également facultatif. Il permet de spécifier le chemin d'accès des classes Java si le fichier spec contient un spout ou un bolt Java.  

###Fonctions diverses

####Déclaration de schéma d'entrée et de sortie

Lorsque vous appelez  `Emit()`, la plate-forme sérialise le tuple dans un tableau d'octets et le transfère vers la machine virtuelle Java (JVM). Storm transfère alors ce tuple vers les cibles. Les bolts reçoivent ensuite le tuple. Pour les bolts C#, le tuple est reçu de la machine virtuelle Java (JVM) et reconverti dans le type d'origine.

Pour prendre en charge cette sérialisation et cette désérialisation, vous devez déclarer le schéma des entrées et sorties. Ces dernières sont définies comme des objets  `Dictionary<string, List<Type>`, où la clé est l'ID du flux et la valeur, les  `Types` des tuples qui seront émis. Le composant peut déclarer plusieurs flux.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

L'objet `Context` fournit `DeclareComponentSchema`, qui peut être utilisé pour déclarer le schéma pour la sérialisation/désérialisation.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] Vous devez vous assurer que les tuples émis respectent le schéma défini pour ce flux, sinon vous recevrez une exception de runtime.  

####Prise en charge multiflux

Vous pouvez émettre vers plusieurs flux en appelant plusieurs fois `Emit()` et en spécifiant le paramètre `streamId` pour chaque flux dans lequel vous voulez écrire.

> [AZURE.NOTE] L'émission vers un flux qui n'existe pas entraînera des exceptions de runtime.

####Regroupement de champs

Le regroupement de champs intégré à Storm ne fonctionne pas correctement dans SCP.NET. Sur le côté du proxy Java, tous les types de données de champs correspondent à la sémantique `byte[]` et le regroupement de champs utilise le code de hachage d'objet  `byte[]` pour effectuer le regroupement. Le code de hachage d'objet `byte[]` correspond à l'adresse de cet objet en mémoire. Le regroupement sera donc incorrect pour deux objets `byte[]` partageant le même contenu, mais pas la même adresse.

Pour contourner ce problème, utilisez  `scp-field-group` dans votre spec. Le contenu de `byte[]` sera alors utilisé pour le regroupement. Voici un exemple d'utilisation de scp-field-group dans un spec.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

L'exemple effectue les actions suivantes.

* `scp-field-group` : utiliser le regroupement personnalisé.  
* `:tx` ou`:non-tx` : indique s'il s'agit d'une topologie transactionnelle ou non transactionnelle.
* `[0,1]` : utiliser un hashset d'ID de champ, commençant à 0.  

####Topologie hybride

La plupart des spouts, bolts et topologies Storm natifs sont implémentés en Java. Pour prendre en charge la réutilisation de ces composants dans les solutions qui utilisent des composants C#, SCP vous permet de créer et définir des topologies hybrides dans le spec.

* **Spout ou bolt Java** : dans le fichier spec, `scp-spout` et `scp-bolt` peuvent également être utilisés pour spécifier des spouts et des bolts Java. Le code suivant illustre un spout Java avec un nom de classe `microsoft.scp.example.HybridTopology.Generator`.

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Java Classpath** : lorsque vous utilisez des spouts ou des bolts Java, vous devez d'abord compiler le spout ou le bolt dans un fichier JAR. Ajoutez ensuite le chemin d'accès des classes Java avec le paramètre`-cp` lors de l'utilisation de la commande **runSpec**. L'exemple suivant inclut les fichiers JAR situés dans le répertoire **examples\HybridTopology\java\target**.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Sérialisation et désérialisation entre Java et C#** - Pour sérialiser des objets entre Java et C#, utilisez  `CustomizedInteropJSONSerializer`

    > [AZURE.NOTE] Actuellement,  `CustomizedInteropJSONSerializer` ne prend en charge que les **spouts Java** et les **bolts C#**.

    * Spécifiez le sérialiseur pour les composants Java dans le fichier spec

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * Utilisez le sérialiseur dans vos composants C#

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    L'implémentation par défaut peut gérer la plupart des cas si le type de données n'est pas trop complexe.  Si le type de données est trop complexe ou que les performances de l'implémentation par défaut ne répondent pas à vos exigences, vous pouvez créer une implémentation personnalisée adaptée à vos besoins en utilisant les interfaces suivantes

    **Interface de sérialisation en Java**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **Interface de désérialisation en C#**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###Mode hôte

Le mode hôte vous permet de compiler votre projet dans une DLL et de l'héberger avec **SCPHost.exe**. Il s'agit de la méthode recommandée pour héberger votre solution dans un environnement de production. Lorsque vous utilisez le mode hôte, le fichier spec répertorie  `SCPHost.exe` en tant que plug-in.

Voici un exemple de fichier spec ayant utilisé le mode hôte pour l'exemple HelloWorld.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** : assembly qui contient les spouts et les bolts.
* **Scp.App.HelloWorld.Generator** : nom de classe pour un spout contenu dans **HelloWorld.dll**.
* **Get** : méthode à appeler pour obtenir une instance du spout.


##Exemples de programmation SCP

Les exemples d'application suivants, écrits avec SCP, se trouvent dans votre cluster Storm sur HDInsight à l'emplacement suivant : **%storm_home%\examples**.

* **HelloWorld** : HelloWorld est un exemple très simple de SCP.Net, similaire à l'exemple concernant le comptage de mots utilisé précédemment dans cet article. Il emploie une topologie non transactionnelle, avec un spout nommé **generator**, et deux bolts nommés **splitter** et **counter**. Le spout **generator** va générer des phrases de manière aléatoire, avant de les émettre vers **splitter**. Le bolt **splitter** va diviser les phrases en mots et émettre ces derniers vers le bolt **counter**. Le bolt **counter** utilise un dictionnaire pour enregistrer le nombre d'occurrences de chaque mot.

    L'exemple contient deux fichiers spec : **HelloWorld.spec** et **HelloWorld_EnableAck.spec**. Lorsque vous utilisez **HelloWorld_EnableAck.spec**, l'exemple accuse réception (ack) des tuples qui passent par les bolts ; cependant, **splitter** est conçu pour faire échouer de façon aléatoire quelques bolts afin d'illustrer la gestion des échecs dans les topologies non transactionnelles.

* **HelloWorldTx** : exemple d'implémentation d'une topologie transactionnelle. Il possède un spout transactionnel nommé **generator**, un bolt par lots nommé **partial-count** et un bolt de validation nommé **count-sum**. Trois fichiers texte sont également précréés pour être utilisés avec cette topologie : **DataSource0.txt**, **DataSource1.txt** et **DataSource2.txt**.

    Dans chaque transaction, le spout sélectionne deux des fichiers de façon aléatoire, puis émet les deux noms de fichier vers le bolt **partial-count**. Le bolt récupère le nom du fichier à partir du tuple reçu, puis ouvre le fichier et compte le nombre de mots qu'il contient. Enfin, il émet les mots et les nombres vers le bolt **count-sum**. Le bolt **count-sum** résume le résultat total.  

    Pour mener à bien la sémantique " Une fois exactement ", le bolt **count-sum** doit décider s'il traite une transaction relue. Dans cet exemple, il est doté d'une variable de membre statique :  

        public static long lastCommittedTxId = -1;  

    Lorsqu'une instance du bolt est créée, elle obtient la tentative de transaction (`txAttempt`) des paramètres d'entrée :  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    Lorsque  `FinishBatch()` est appelé,  `lastCommittedTxId` est mis à jour s'il ne s'agit pas d'une transaction relue.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** : cette topologie contient un spout Java et un bolt C#. Elle utilise l'implémentation de sérialisation et de désérialisation par défaut fournie par SCP. Consultez **%storm_home%examples\HybridTopology\HybridTopology.spec** pour des informations détaillées et **SubmitTopology.bat** pour savoir comment spécifier le chemin des classes Java lors de la soumission de la topologie.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png

<!--HONumber=42-->
