
<properties
pageTitle="Guide de programmation SCP.NET | Azure"
description="Découvrez comment utiliser SCP.NET pour créer des topologies Storm basées sur .NET en vue d’une utilisation avec Storm sur HDInsight."
services="hdinsight"
documentationCenter=""
authors="raviperi"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="dotnet"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="05/16/2016"
ms.author="raviperi"/>

#Guide de programmation SCP

SCP est une plateforme permettant de développer des applications de traitement de données en temps réel, fiables, cohérentes et aux performances élevées. Elle est basée sur [Apache Storm](http://storm.incubator.apache.org/), système de traitement par flux conçu par la communauté OSS. Storm est conçu par Nathan Marz et diffusé en open source par Twitter. Il exploite [Apache ZooKeeper](http://zookeeper.apache.org/), un autre projet Apache pour fournir une coordination et une gestion d’état très fiables.

Le projet SCP ne se contente pas de faire migrer Storm sur Windows : il permet également d'étendre et de personnaliser l'écosystème Windows. Ses extensions améliorent l’expérience des développeurs .NET et les bibliothèques, tandis que sa capacité de personnalisation permet un déploiement basé sur Windows.

Grâce à ces capacités d'extension et de personnalisation, vous n'aurez pas à répliquer vos projets OSS tout en tirant partie des écosystèmes dérivés basés sur Storm.

## Modèle de traitement 

Les données de SCP sont modélisées en tant que flux de tuples continus. Généralement, les tuples sont d'abord diffusés en file d'attente, puis récupérés et transformés par une logique métier dans une topologie Storm. Puis le résultat peut être redirigé en tant que tuples vers un autre système SCP ou être validés pour des magasins tels qu'un système de fichiers distribués ou des bases de données comme SQL Server.

![Diagramme d’une file d’attente fournissant des données à traiter, destinées à une banque de données](media/hdinsight-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Dans Storm, une topologie d'application définit un graphique de calcul. Chaque nœud d'une topologie contient une logique de traitement et les liens entre ces nœuds indiquent les flux de données. Les nœuds permettant d'injecter des données d'entrée dans la topologie sont nommés des « Spouts ». Ils permettent de séquencer les données. Les données d’entrée peuvent résider dans des fichiers journaux, une base de données transactionnelle, un compteur de performances système, etc. Les nœuds avec à la fois des flux de données d’entrée et de sortie sont appelés bolts ; ces derniers assurent les opérations de filtrage et de sélection des données, ainsi que d’agrégation.

SCP prend en charge les types de traitement de données Meilleur effort, Une fois au minimum et Exactement une. Dans une application distribuée de traitement de diffusion en continu, diverses erreurs peuvent se produire pendant le traitement des données, telles qu’une erreur de code utilisateur, la défaillance d’un ordinateur ou une panne du réseau. Le type de traitement Une fois au minimum garantit que toutes les données sont traitées au moins une fois en relisant automatiquement les mêmes données quand une erreur se produit. Le type de traitement Une fois au minimum est simple et fiable et s'adapte à de nombreuses applications. Cependant, lorsqu'une application requiert un comptage exact, le traitement Une fois au minimum n'est pas suffisant, car les mêmes données peuvent potentiellement être lues dans la topologie de l'application. Dans ce cas, le type de traitement Exactement une est conçu pour garantir l'exactitude du résultat, même lorsque les données peuvent être relues et traitées plusieurs fois.

SCP permet aux développeurs .NET de développer des applications de traitement de données en temps réel tout en exploitant le projet Storm basé sur JVM (Java Virtual Machine). .NET et JVM communiquent via un socket local TCP. Fondamentalement, chaque Spout/Bolt constitue une paire de processus .Net/Java, où la logique utilisateur s'exécute dans un processus .Net en tant que plug-in.

Pour générer une application de traitement de données sur SCP, vous devez suivre plusieurs étapes :

-   Concevez et implémentez les Spouts pour intégrer des données en file d'attente.

-   Concevez et implémentez des Bolts pour traiter les données d'entrée, puis enregistrez les données vers des magasins externes tels que des bases de données.

-   Concevez la topologie, puis validez-la et exécutez-la. La topologie définit les points et les flux de données entre ces points. SCP récupère alors la spécification de la topologie et la déploie dans un cluster Storm, où chaque point est exécuté sur un nœud logique. Le planificateur de tâches de Storm s'occupe du basculement et de la mise à l'échelle.

Ce document présente quelques exemples pour illustrer le développement d'applications de traitement des données avec SCP.

## Interface de plug-in SCP 

Les plug-ins (ou applications) SCP sont des exécutables (.EXE) autonomes que vous pouvez exécuter dans Visual Studio durant le développement, puis brancher sur le pipeline Storm après le déploiement dans un environnement de production. L'écriture d'un plug-in SCP est identique à celle de n'importe quelle application console Windows standard. La plateforme SCP.NET déclare certaines interfaces pour spout/bolt, et le code du plug-in utilisateur doit implémenter ces interfaces. L'objectif principal de cette conception est de permettre à l'utilisateur de se concentrer sur ses propres logiques métier, en laissant la plateforme SCP.NET s'occuper du reste.

Le code du plug-in utilisateur doit implémenter l'une des interfaces suivantes, selon que la topologie soit transactionnelle ou non, et que le composant soit spout ou bolt.

-   ISCPSpout

-   ISCPBolt

-   ISCPTxSpout

-   ISCPBatchBolt

### ISCPPlugin

ISCPPlugin est l'interface la plus répandue pour tous les types de plug-in. Actuellement, c'est une interface factice.

	public interface ISCPPlugin 
	{
	}

### ISCPSpout

ISCPSpout est l'interface pour les composants spout non transactionnels.

	 public interface ISCPSpout : ISCPPlugin                    
	 {
		 void NextTuple(Dictionary<string, Object> parms);         
		 void Ack(long seqId, Dictionary<string, Object> parms);   
		 void Fail(long seqId, Dictionary<string, Object> parms);  
	 }

Quand `NextTuple()` est appelé, le code utilisateur C# peut émettre un ou plusieurs tuples. S'il n'y a rien à émettre, cette méthode doit effectuer un renvoi sans émettre quoi que ce soit. Notez que `NextTuple()`, `Ack()` et `Fail()` sont toutes appelées dans une boucle étroite d’un thread unique dans un processus C#. Quand il n'y a aucun tuple à émettre, il est recommandé de mettre NextTuple en veille pour un bref instant (10 millisecondes par exemple) pour ne pas gaspiller trop de ressources processeur.

`Ack()` et `Fail()` sont appelées uniquement quand le mécanisme d’accusé de réception (ack) est activé dans le fichier spec. `seqId` est utilisé pour identifier le tuple qui a été reçu ou qui a échoué. Donc, si l'accusé de réception est activé dans une topologie non transactionnelle, la fonction d'émission suivante doit être utilisée dans Spout :

	public abstract void Emit(string streamId, List<object> values, long seqId); 

Si l’accusé de réception n’est pas pris en charge dans une topologie non transactionnelle, les fonctions `Ack()` et `Fail()` peuvent être laissées vides.

Les paramètres d’entrée `parms` de ces fonctions correspondent simplement à un Dictionnaire vide, ils seront utilisés plus tard.

### ISCPBolt

ISCPBolt est l'interface pour les composants bolt non transactionnels.

	public interface ISCPBolt : ISCPPlugin 
	{
	void Execute(SCPTuple tuple);           
	}

Quand un nouveau tuple est disponible, la fonction `Execute()` est appelée pour son traitement.

### ISCPTxSpout

ISCPTxSpout est l'interface pour les composants spout transactionnels.

	public interface ISCPTxSpout : ISCPPlugin
	{
		void NextTx(out long seqId, Dictionary<string, Object> parms);  
		void Ack(long seqId, Dictionary<string, Object> parms);         
		void Fail(long seqId, Dictionary<string, Object> parms);        
	}

Tout comme leurs équivalentes non-transactionnelles, les fonctions `NextTx()`, `Ack()` et `Fail()` sont toutes appelées dans une boucle étroite d’un thread unique dans un processus C#. Quand il n’y a aucune donnée à émettre, il est recommandé de mettre `NextTx` en veille pour un bref instant (10 millisecondes par exemple) pour ne pas gaspiller trop de ressources processeur.

`NextTx()` est appelée pour démarrer une nouvelle transaction, le paramètre de sortie `seqId` est utilisé pour identifier la transaction, qui est également utilisée dans les fonctions `Ack()` et `Fail()`. Dans `NextTx()`, l’utilisateur peut émettre des données vers le côté Java. Les données seront stockées dans ZooKeeper pour prendre en charge la relecture. Comme la capacité de ZooKeeper est très limitée, l'utilisateur doit seulement émettre des métadonnées, et non pas des données en bloc dans un spout transactionnel.

Si une transaction échoue, Storm la relit automatiquement. Il ne faut donc pas appeler la fonction `Fail()` dans un cas normal. Mais si SCP peut contrôler les métadonnées émises par un spout transactionnel, il peut appeler la fonction `Fail()` quand les métadonnées ne sont pas correctes.

Les paramètres d’entrée `parms` de ces fonctions correspondent simplement à un Dictionnaire vide, ils seront utilisés plus tard.

### ISCPBatchBolt

ISCPBatchBolt est l'interface pour les composants bolt transactionnels.

	public interface ISCPBatchBolt : ISCPPlugin           
	{
		void Execute(SCPTuple tuple);
		void FinishBatch(Dictionary<string, Object> parms);  
	}

`Execute()` est appelé quand un nouveau tuple arrive sur le bolt. `FinishBatch()` est appelé quand cette transaction est terminée. Le paramètre d’entrée `parms` est réservé à un usage futur.

Il existe un concept important pour la topologie transactionnelle : `StormTxAttempt`. Il a deux champs, `TxId` et `AttemptId` : `TxId` est utilisé pour identifier une transaction spécifique. Si une transaction donnée échoue et est relue, il peut y avoir plusieurs tentatives. SCP.NET va créer un objet ISCPBatchBolt différent pour traiter chaque `StormTxAttempt`, tout comme le ferait Storm côté Java. Cette conception permet de prendre en charge le traitement des transactions parallèles. L'utilisateur ne doit pas oublier que si la tentative de transaction est terminée, l'objet ISCPBatchBolt correspondant sera détruit et récupéré par le garbage collector.

## Modèle objet

SCP.NET fournit également un ensemble d'objets clés pour les développeurs. Il s'agit des objets **Context**, **StateStore** et **SCPRuntime**. Ils seront présentés dans la suite de cette section.

### Context

L'objet Context fournit un environnement d'exécution pour l'application. Chaque instance ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) dispose d'une instance Context correspondante. La fonctionnalité fournie par un objet Context peut être divisée en deux : (1) une partie statique qui est disponible dans l’ensemble du processus C#, (2) une partie dynamique qui est uniquement disponible pour l’instance Context spécifique.

### Partie statique

	public static ILogger Logger = null;
	public static SCPPluginType pluginType;                      
	public static Config Config { get; set; }                    
	public static TopologyContext TopologyContext { get; set; }  

`Logger` est fourni à des fins de journalisation.

`pluginType` permet d’indiquer le type de plug-in du processus C#. Si le processus C# est exécuté dans un mode de test local (sans Java), le type de plug-in est `SCP_NET_LOCAL`.

	public enum SCPPluginType 
	{
		SCP_NET_LOCAL = 0,       
		SCP_NET_SPOUT = 1,       
		SCP_NET_BOLT = 2,        
		SCP_NET_TX_SPOUT = 3,   
		SCP_NET_BATCH_BOLT = 4  
	}

`Config` est fourni pour obtenir des paramètres de configuration côté Java. Les paramètres sont transférés à partir du côté Java lors de l'initialisation du plug-in C#. Les paramètres `Config` sont divisés en deux parties : `stormConf` et `pluginConf`.

	public Dictionary<string, Object> stormConf { get; set; }  
	public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` correspond aux paramètres définis par Storm, tandis que `pluginConf` correspond aux paramètres définis par SCP. Par exemple :

	public class Constants
	{
		… …

		// constant string for pluginConf
		public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

		// constant string for stormConf
		public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
		public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
	}

`TopologyContext` est fourni pour obtenir le contexte de topologie et est plus utile pour les composants dotés de plusieurs parallélismes. Voici un exemple :

	//demo how to get TopologyContext info
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

### Partie dynamique

Les interfaces suivantes sont pertinentes pour une instance de l'objet Context bien précise. Cette instance de l'objet Context est créée par la plateforme SCP.NET, puis transmise vers le code utilisateur :

	* Declare the Output and Input Stream Schemas *                

	public void DeclareComponentSchema(ComponentStreamSchema schema);   

	* Emit tuple to default stream. *
	public abstract void Emit(List<object> values);                   

	* Emit tuple to the specific stream. *
	public abstract void Emit(string streamId, List<object> values);  

La méthode suivante est fournie pour les spouts non transactionnels prenant en charge le mécanisme d'accusé de réception (ack) :

	* for non-transactional Spout which supports ack *
	public abstract void Emit(string streamId, List<object> values, long seqId);  

Dans le cas des bolts non transactionnels prenant en charge le mécanisme d’accusé de réception (ack), la fonction `Ack()` ou `Fail()` est appliquée de façon explicite au tuple reçu. Puis, lors de l'émission d'un nouveau tuple, les signets du nouveau tuple doivent également être spécifiés. Les méthodes suivantes sont fournies.

	public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
	public abstract void Ack(SCPTuple tuple);
	public abstract void Fail(SCPTuple tuple);

### StateStore

`StateStore` fournit des services de métadonnées, une génération de séquence unitone et une coordination sans attente. Il est possible de développer des abstractions concurrentielles distribuées et globales sur `StateStore`, notamment des verrous distribués, des files d’attente distribuées, des barrières et des services de transaction.

Les applications SCP peuvent utiliser l’objet `State` pour conserver des informations dans ZooKeeper, notamment la topologie transactionnelle. Ainsi, si un spout transactionnel se bloque et redémarre, il peut récupérer les informations nécessaires à partir de ZooKeeper, puis redémarrer le pipeline.

L’objet `StateStore` dispose principalement de ces méthodes :

	/// <summary>
	/// Static method to retrieve a state store of the given path and connStr 
	/// </summary>
	/// <param name="storePath">StateStore Path</param>
	/// <param name="connStr">StateStore Address</param>
	/// <returns>Instance of StateStore</returns>
	public static StateStore Get(string storePath, string connStr);

	/// <summary>
	/// Create a new state object in this state store instance
	/// </summary>
	/// <returns>State from StateStore</returns>
	public State Create();

	/// <summary>
	/// Retrieve all states that were previously uncommitted, excluding all aborted states 
	/// </summary>
	/// <returns>Uncommited States</returns>
	public IEnumerable<State> GetUnCommitted();

	/// <summary>
	/// Get all the States in the StateStore
	/// </summary>
	/// <returns>All the States</returns>
	public IEnumerable<State> States();

	/// <summary>
	/// Get state or registry object
	/// </summary>
	/// <param name="info">Registry Name(Registry only)</param>
	/// <typeparam name="T">Type, Registry or State</typeparam>
	/// <returns>Return Registry or State</returns>
	public T Get<T>(string info = null);

	/// <summary>
	/// List all the committed states
	/// </summary>
	/// <returns>Registries contain the Committed State </returns> 
	public IEnumerable<Registry> Commited();

	/// <summary>
	/// List all the Aborted State in the StateStore
	/// </summary>
	/// <returns>Registries contain the Aborted State</returns>
	public IEnumerable<Registry> Aborted();

	/// <summary>
	/// Retrieve an existing state object from this state store instance 
	/// </summary>
	/// <returns>State from StateStore</returns>
	/// <typeparam name="T">stateId, id of the State</typeparam>
	public State GetState(long stateId)

L’objet `State` dispose principalement de ces méthodes :

	/// <summary>
	/// Set the status of the state object to commit 
	/// </summary>
	public void Commit(bool simpleMode = true); 

	/// <summary>
	/// Set the status of the state object to abort 
	/// </summary>
	public void Abort();

	/// <summary>
	/// Put an attribute value under the give key 
	/// </summary>
	/// <param name="key">Key</param> 
	/// <param name="attribute">State Attribute</param> 
	public void PutAttribute<T>(string key, T attribute); 

	/// <summary>
	/// Get the attribute value associated with the given key 
	/// </summary>
	/// <param name="key">Key</param> 
	/// <returns>State Attribute</returns>               
	public T GetAttribute<T>(string key);                    

Dans le cas de la méthode `Commit()`, quand simpleMode est défini sur true, cela entraîne simplement la suppression du ZNode correspondant dans ZooKeeper. Sinon, le ZNode en cours est supprimé, puis un nouveau nœud est ajouté au COMMITTED\_PATH.

### SCPRuntime

SCPRuntime fournit les deux méthodes suivantes.

	public static void Initialize();
	
	public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` permet d’initialiser l’environnement de runtime de SCP. Dans cette méthode, le processus C# se connecte au côté Java et obtient les paramètres de configuration ainsi que le contexte de topologie.

`LaunchPlugin()` permet d’envoyer la boucle de traitement du message. Dans cette boucle, le plug-in C# reçoit des messages depuis le côté Java (notamment les tuples et les signaux de contrôle), puis il traite les messages, en appelant par exemple la méthode d'interface fournie par le code utilisateur. Le paramètre d’entrée pour la méthode `LaunchPlugin()` est un délégué qui peut renvoyer un objet implémentant l’interface ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

	public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms); 

Pour ISCPBatchBolt, nous pouvons obtenir `StormTxAttempt` à partir de `parms` et l’utiliser pour savoir s’il s’agit d’une tentative relue. Ceci est généralement fait sur le bolt de validation, puis il est montré dans l’exemple `HelloWorldTx`.

De façon générale, les plug-ins peuvent s'exécuter dans les deux modes suivants :

1. Mode de test local : dans ce mode, les plug-ins SCP (le code utilisateur C#) s’exécutent dans Visual Studio durant la phase de développement. Vous pouvez utiliser `LocalContext` dans ce mode, qui fournit une méthode pour sérialiser les tuples émis vers des fichiers locaux, puis les lire une fois qu’ils retournent à la mémoire.

        public interface ILocalContext
        {
            List<SCPTuple> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Mode ordinaire : dans ce mode, les plug-ins SCP sont démarrés par un processus Java Storm.

    Voici un exemple de démarrage de plug-in SCP :

        namespace Scp.App.HelloWorld
        {
        public class Generator : ISCPSpout
        {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
            return new Generator(ctx);
            }
        }
    
        class HelloWorld
        {
            static void Main(string[] args)
            {
            /* Setting the environment variable here can change the log file name */
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");
    
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
            }
        }
        }


## Langage de spécification de topologie 

La spécification de topologie SCP est un langage de domaine spécifique pour décrire et configurer les topologies SCP. Elle est basée sur le Clojure DSL de Storm (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) et est étendue par SCP.

Vous pouvez envoyer directement des spécifications de topologie vers le cluster Storm pour les exécuter via la commande ***runspec***.

SCP.NET a ajouté les fonctions suivantes pour définir la topologie transactionnelle :

|**Nouvelles fonctions**|**Paramètres**|**Description**|
|---|---|---|
|**tx-topolopy**|topology-name<br />spout-map<br />bolt-map|Permet de définir une topologie transactionnelle avec un nom de topologie,&nbsp;une carte de définition de spouts et une carte de définition de bolts.|
|**scp-tx-spout**|exec-name<br />args<br />fields|Permet de définir un spout transactionnel. Cette fonction exécute l'application avec ***exec-name*** en utilisant ***args***.<br /><br />Le paramètre ***fields*** correspond aux champs de sortie du spout.|
|**scp-tx-batch-bolt**|exec-name<br />args<br />fields|Permet de définir un lot bolt transactionnel. Cette fonction exécute l’application avec ***exec-name*** en utilisant ***args***.<br /><br />Le paramètre Fields correspond aux champs de sortie du bolt.|
|**scp-tx-commit-bolt**|exec-name<br />args<br />fields|Permet de définir un validateur bolt transactionnel. Cette fonction exécute l’application avec ***exec-name*** en utilisant ***args***.<br /><br />Le paramètre ***fields*** correspond aux champs de sortie du bolt.|
|**nontx-topolopy**|topology-name<br />spout-map<br />bolt-map|Permet de définir une topologie non transactionnelle avec un nom de topologie,&nbsp;une carte de définition de spouts et une carte de définition de bolts.|
|**scp-spout**|exec-name<br />args<br />fields<br />parameters|Permet de définir un spout non transactionnel. Cette fonction exécute l'application avec ***exec-name*** en utilisant ***args***.<br /><br />Le paramètre ***fields*** correspond aux champs de sortie du spout.<br /><br />Le paramètre ***parameters*** est facultatif, il permet de spécifier certains paramètres tels que « nontransactional.ack.enabled ».|
|**scp-bolt**|exec-name<br />args<br />fields<br />parameters|Permet de définir un bolt non transactionnel. Cette fonction exécute l'application avec ***exec-name*** en utilisant ***args***.<br /><br />Le paramètre ***fields*** correspond aux champs de sortie du bolt.<br /><br />Le paramètre ***parameters*** est facultatif, il permet de spécifier certains paramètres tels que « nontransactional.ack.enabled ».|

Les mots clés suivants sont définis pour SCP.NET :

|**Mots clés**|**Description**|
|---|---|
|**:name**|Permet de définir le nom de la topologie.|
|**:topology**|Permet de définir la topologie en utilisant les fonctions précédentes et d'en intégrer d'autres.|
|**:p**|Permet de définir l'indicateur de parallélisme pour chaque spout ou bolt.|
|**:config**|Permet de définir le paramètre de configuration ou de mettre à jour celui existant.|
|**:schema**|Permet de définir le schéma de flux.|

Et voici des paramètres fréquemment utilisés :

|**Paramètre**|**Description**|
|---|---|
|**« plugin.name »**|Nom du fichier .exe du plug-in C#|
|**« plugin.args »**|Arguments du plug-in|
|**« output.schema »**|Schéma de sortie|
|**« nontransactional.ack.enabled »**|Indique si le mécanisme d'accusé de réception (ack) est activé pour la topologie non transactionnelle.|

La commande runspec sera déployée avec les octets. Elle s'utilise comme ceci :

	.\bin\runSpec.cmd
	usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
	ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Le paramètre ***resource-dir*** est facultatif, vous devez le spécifier lorsque vous voulez brancher une application C#. Ce répertoire contient l'application, ses dépendances et sa configuration.

Le paramètre ***classpath*** est également facultatif. Il permet de spécifier le chemin d'accès des classes Java si le fichier spec contient un spout ou un bolt Java.

## Fonctionnalités diverses

### Déclaration de schéma d’entrée et de sortie

L’utilisateur peut émettre un tuple dans le processus C#, la plateforme doit sérialiser ce tuple dans byte, le transférer côté Java, avant que Storm ne le transfère vers les cibles. Pendant ce temps dans le composant en aval, le processus C# reçoit le tuple à partir du côté Java, puis le convertit vers les types d'origine de chaque plateforme. Toutes ces opérations sont masquées par la plateforme.

Pour prendre en charge la sérialisation et la désérialisation, le code utilisateur doit déclarer le schéma des entrées et sorties.

Le schéma de flux d'entrée/sortie est défini en tant que dictionnaire, la clé est le StreamId et la valeur correspond aux types des colonnes. Le multiflux peut être déclaré pour le composant.

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


Les API suivantes sont ajoutées à l'objet Context :

	public void DeclareComponentSchema(ComponentStreamSchema schema)

Le code utilisateur doit garantir que les tuples émis respectent le schéma défini pour ce flux, ou le système génère une exception de runtime.

### Prise en charge multiflux

SCP prend en charge le code utilisateur pour émettre ou recevoir depuis plusieurs flux en même temps. La prise en charge s'affiche dans l'objet Context lorsque la méthode Emit possède un paramètre ID de flux facultatif.

Deux méthodes ont été ajoutées dans l'objet Context de SCP.NET. Elles permettent d'émettre un ou plusieurs tuples pour spécifier StreamId. StreamId est une chaîne et elle doit être cohérente dans C# et la spécification de définition de topologie.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

L'émission vers un flux qui n'existe pas entraînera des exceptions de runtime.

### Regroupement de champs

Le regroupement de champs intégré à Storm ne fonctionne pas correctement dans SCP.NET. Sur le côté du proxy Java, tous les types de données de champs correspondent à la sémantique byte, et le regroupement de champs utilise le code de hachage d’objet byte pour effectuer le regroupement. Le code de hachage d’objet byte correspond à l’adresse de cet objet en mémoire. Le regroupement est donc incorrect pour deux objets byte partageant le même contenu mais pas la même adresse.

SCP.NET ajoute une méthode de regroupement personnalisée et utilise le contenu de byte pour procéder au regroupement. Dans le fichier **SPEC**, la syntaxe correspond à :

	(bolt-spec
	    {
	        "spout_test" (scp-field-group :non-tx [0,1])
	    }
	    …
	)


Ici,

1.  « scp-field-group » signifie « Regroupement de champs personnalisé implémenté par SCP ».

2.  « :tx » ou « :non-tx » signifie qu’il s’agit d’une topologie transactionnelle. Nous avons besoin de cette information, car l'index de démarrage est différent si la topologie est tx ou non-tx.

3.  [0,1] représente un hashset d'ID de champ, commençant à 0.

### Topologie hybride

Le Storm natif est écrit en Java. En outre, SCP.Net l’a amélioré pour permettre à nos clients d’écrire du code C# pour gérer leur logique métier. Mais nous prenons également en charge les topologies hybrides, qui contiennent des spouts/bolts C#, ainsi que des spouts/bolts Java.

### Indiquer le spout/bolt Java dans le fichier spec

Vous pouvez également utiliser « scp-spout » et « scp-bolt » dans le fichier spec pour spécifier les spouts et les bolts Java. Par exemple :

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Ici `microsoft.scp.example.HybridTopology.Generator` est le nom de la classe Spout Java.

### Spécifier le chemin d’accès des classes Java dans une commande runSpec

Si vous voulez envoyer une topologie contenant des spouts ou des bolts Java, vous devez d’abord compiler les spouts ou bolts Java et récupérer les fichiers Jar. Puis, vous devez spécifier le chemin d’accès des classes Java contenant les fichiers Jar au moment de l’envoi de la topologie. Voici un exemple :

	bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target*

Ici **examples\\HybridTopology\\java\\target\** correspond au dossier contenant le fichier Jar spout/bolt Java.

### Sérialisation et désérialisation entre Java et C#

Notre composant SCP a un côté Java et un côté C# . Pour interagir avec des spouts/bolts Java natifs, la sérialisation/désérialisation doit intervenir entre le côté Java et le côté C#, comme l’illustre le graphique suivant.

![Diagramme d’un flux entre un composant Java et un composant SCP, et inversement](media/hdinsight-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1.  **Sérialisation côté Java et désérialisation côté C#**
	
	Tout d'abord, nous fournissons une implémentation par défaut pour la sérialisation côté Java et la désérialisation côté C#. La méthode de sérialisation côté Java peut être spécifiée dans le fichier SPEC :
	
        (scp-bolt
            {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
            })
	
	La méthode de désérialisation côté C# peut être spécifiée dans le code utilisateur C# :
	
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(Person) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());	        
	
    Cette implémentation par défaut peut gérer la plupart des cas si le type de données n'est pas trop complexe. Dans certains cas, soit parce que le type de données utilisateur est trop complexe, soit parce que les performances de notre implémentation par défaut ne répondent pas aux besoins de l'utilisateur, ce dernier peut utiliser sa propre implémentation en plug-in.

    L'interface de sérialisation côté Java se définit comme suit :
	
        public interface ICustomizedInteropJavaSerializer {
            public void prepare(String[] args);
            public List<ByteBuffer> serialize(List<Object> objectList);
        }
	
	L'interface de désérialisation côté C# se définit comme suit :
	
	public interface ICustomizedInteropCSharpDeserializer
	
	    public interface ICustomizedInteropCSharpDeserializer
	    {
	        List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
	    }

2.  **Sérialisation côté C# et désérialisation côte Java**

	La méthode de sérialisation côté C# doit être spécifiée dans le code utilisateur C# :
	
		this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
	
	La méthode de désérialisation côté Java doit être spécifiée dans le fichier SPEC :
	
	  (scp-spout { "plugin.name" "HybridTopology.exe" "plugin.args" ["generator"] "output.schema" {"default" ["person"]} "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"] })
	
	Ici « microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer » est le nom du désérialiseur, tandis que « microsoft.scp.example.HybridTopology.Person » est la classe cible vers laquelle les données sont désérialisées.
	
	L’utilisateur peut également appliquer sa propre implémentation du sérialiseur C# et du désérialiseur Java. Voici l’interface du sérialiseur C# :
	
	    public interface ICustomizedInteropCSharpSerializer
	    {
	        List<byte[]> Serialize(List<object> dataList);
	    }
	
	Voici l’interface du désérialiseur Java :
	
	    public interface ICustomizedInteropJavaDeserializer {
	        public void prepare(String[] targetClassNames);
	        public List<Object> Deserialize(List<ByteBuffer> dataList);
	    }

## Mode d’hébergement SCP

Dans ce mode, l'utilisateur peut compiler ses codes en DLL, puis utiliser le fichier SCPHost.exe fourni par SCP pour l'envoi de topologie. Le fichier spec ressemble à ceci :

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Ici, `plugin.name` est spécifié en tant que `SCPHost.exe` fourni par le Kit de développement logiciel (SDK) SCP. SCPHost.exe accepte exactement trois paramètres :

1.  Le premier est le nom DLL (`"HelloWorld.dll"` dans notre exemple).

2.  Le second est le nom de la classe (`"Scp.App.HelloWorld.Generator"` dans notre exemple).

3.  Le troisième est le nom de la méthode statique publique, qui peut être appelée pour obtenir une instance de ISCPPlugin.

En mode d'hébergement, le code utilisateur est compilé en tant que DLL, puis appelé par la plateforme SCP. La plateforme SCP peut donc contrôler complètement l'ensemble de la logique de traitement. Nous recommandons donc à nos clients d'envoyer la topologie en mode d'hébergement SCP, car elle peut simplifier le développement et améliorer la flexibilité et la compatibilité descendante pour les versions ultérieures.

## Exemples de programmation SCP 

### HelloWorld

**HelloWorld** est un exemple très simple qui offre un avant-goût de SCP.Net. Il emploie une topologie non transactionnelle, avec un spout appelé **generator**, et deux bolts appelés **splitter** et **counter**. Le spout **generator** va générer des phrases aléatoirement, avant de les émettre vers **splitter**. Le bolt **splitter** va diviser les phrases en mots et les émettre vers le bolt **counter**. Le bolt « counter » utilise un dictionnaire pour enregistrer le nombre d’occurrences de chaque mot.

L’exemple contient deux fichiers spec, **HelloWorld.spec** et **HelloWorld\_EnableAck.spec** . Dans le code C#, vous pouvez savoir si le mécanisme d'accusé de réception (ack) est activé en récupérant le pluginConf côté Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Dans le spout, si le ack est activé, un dictionnaire est utilisé pour mettre en cache les tuples n'ayant pas été reçus. Si Fail() est appelée, les tuples ayant échoué sont relus :

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### HelloWorldTx

L’exemple **HelloWorldTx** illustre l’implémentation d’une topologie transactionnelle. Il contient un spout nommé **generator**, un bolt par lots nommé **partial-count** et un bolt de validation nommé **count-sum**. Il contient également trois fichiers txt : **DataSource0.txt**, **DataSource1.txt** et **DataSource2.txt**.

Dans chaque transaction, le spout **generator** sélectionne deux des trois fichiers txt de façon aléatoire, avant de les émettre vers le bolt **partial-count**. Le bolt **partial-count** va d’abord récupérer le nom du fichier à partir du tuple reçu, puis ouvrir le fichier et compter le nombre de mots qu’il contient, avant d’émettre ce résultat vers le bolt **count-sum**. Le bolt **count-sum** résume le résultat total.

Pour mener à bien la sémantique **Une fois au minimum**, le bolt de validation **count-sum** doit décider s’il s’agit d’une transaction relue. Dans cet exemple, il est doté d'une variable de membre statique :

	public static long lastCommittedTxId = -1; 

Au moment de la création d’une instance ISCPBatchBolt, `txAttempt` est récupéré à partir des paramètres d’entrée :

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

Quand `FinishBatch()` est appelé, `lastCommittedTxId` est mis à jour s’il ne s’agit pas d’une transaction relue.

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
        … …
    }


### HybridTopology

Cette topologie contient un spout Java et un bolt C#. Elle utilise l'implémentation de sérialisation et de désérialisation par défaut fournie par la plateforme SCP. Consultez **HybridTopology.spec** dans le dossier **examples\\HybridTopology** pour obtenir les détails du fichier spec, et **SubmitTopology.bat** pour savoir comment spécifier le chemin d’accès des classes Java.

### SCPHostDemo

Cet exemple est sensiblement identique à HelloWorld. Les seules différences sont que le code utilisateur est compilé en tant que DLL et que la topologie est envoyée en utilisant SCPHost.exe. Pour en savoir plus, consultez la section « Mode d’hébergement SCP ».

##Étapes suivantes

Pour obtenir des exemples de topologies Storm créées à l’aide de SCP, consultez les articles suivants :

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Traitement d'événements à partir d'Azure Event Hubs avec Storm dans HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md)
* [Création de plusieurs flux de données dans une topologie C# Storm](hdinsight-storm-twitter-trending.md)
* [Utiliser Power BI pour visualiser les données d’une topologie Storm](hdinsight-storm-power-bi-topology.md)
* [Traitement des données de capteur de véhicule à partir d'Event Hubs à l'aide de Storm dans HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extraction, transformation et chargement (ETL) à partir d’Azure Event Hubs dans HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
* [Corrélation des événements à l’aide de Storm et HBase sur HDInsight](hdinsight-storm-correlation-topology.md)

<!---HONumber=AcomDC_0914_2016-->