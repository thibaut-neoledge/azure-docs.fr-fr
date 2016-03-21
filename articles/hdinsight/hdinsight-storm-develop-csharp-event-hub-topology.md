<properties
   pageTitle="Traitement des événements de hubs d’événements avec Storm sur HDInsight | Microsoft Azure"
   description="Découvrez comment traiter les données de hubs d’événements avec une topologie Storm C# créée dans Visual Studio à l’aide de HDInsight Tools pour Visual Studio."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# Traitement des événements Azure Event Hubs avec Storm sur HDInsight (C#)

Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hubs simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

Dans ce didacticiel, vous allez apprendre à utiliser HDInsight Tools pour Visual Studio ainsi que les spout et le bolt Event Hubs pour créer deux topologies C#/Java hybrides :

* **EventHubWriter** : génère des données de façon aléatoire et les écrit dans les hubs d’événements

* **EventHubReader** : lit les données à partir de Event Hubs et les stocke dans le stockage de table Azure

[AZURE.NOTE] Les étapes décrites dans ce document fonctionnent uniquement avec un cluster HDInsight Windows. Pour obtenir une version Java de ce projet, qui fonctionne avec un cluster basé sur Windows ou un cluster basé sur Linux, consultez [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## Configuration requise

* Un [cluster Apache Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

* Un [hub d'événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Le [kit de développement logiciel Azure .NET](http://azure.microsoft.com/downloads/)

* Les [outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## Projet terminé

Vous pouvez télécharger une version complète du projet créé dans ce didacticiel à partir de GitHub : [eventhub-storm-hybride](https://github.com/Blackmist/eventhub-storm-hybrid). Toutefois, vous devrez tout de même fournir des paramètres de configuration en suivant les étapes de ce didacticiel.

> [AZURE.NOTE] Lors de l’utilisation du projet terminé, vous devez utiliser le **Gestionnaire de Package NuGet** pour restaurer les packages requis par cette solution.

## Spout et bolt de hubs d’événements

Le spout et le bolt Event Hubs sont les composants Java qui vous permettent de travailler facilement avec Event Hubs à partir d’Apache Storm. Bien que ces composants soient écrits en Java, HDInsight Tools pour Visual Studio vous permet de créer des topologies hybrides qui combinent des composants Java et C#.

Le spout et le bolt sont distribués dans un fichier d’archive Java unique (.jar) nommé **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

### Téléchargement du fichier .jar

La version la plus récente du fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** est fournie dans le projet <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> dans le dossier **lib**. Pour télécharger le fichier, appliquez l'une des méthodes suivantes.

> [AZURE.NOTE] Le spout et le bolt ont été soumis pour inclusion dans le projet Apache Storm. Pour plus d’informations, consultez la rubrique <a href="https://github.com/apache/storm/pull/336/files">STORM-583: Initial check-in for storm-event hubs</a> dans GitHub.

* **Téléchargement du fichier ZIP** : à partir du site <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm exemples</a>, sélectionnez le bouton **Download ZIP** dans le volet droit pour télécharger un fichier .zip contenant le projet.

	![bouton de téléchargement zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Une fois le fichier téléchargé, vous pouvez extraire l’archive et le fichier sera copié dans le répertoire **lib**.

* **Clonage du projet** : si <a href="http://git-scm.com/" target="_blank">Git</a> est installé, exécutez la commande suivante pour cloner le référentiel localement, puis recherchez le fichier dans le répertoire **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Configuration du hub d'événements

Event Hubs est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau hub d'événements.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **NOUVEAU** > **Service Bus** > **Event Hub** > **Création personnalisée**.

2. Sur l’écran **Ajouter un nouveau hub d’événements**, entrez un **nom de hub d’événements**, sélectionnez la **région** dans laquelle créer le hub, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la **flèche** pour continuer.

	![page 1 de l’assistant](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que celui de votre serveur Storm sur HDInsight pour réduire la latence et les coûts.

2. Sur l’écran **Configurer un hub d’événements**, entrez les valeurs pour **Nombre de partitions** et **Conservation des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous en aurez besoin ultérieurement.

	![page 2 de l’assistant](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Une fois le hub d’événements créé, sélectionnez l’espace de noms, puis le **hub d’événements**. Enfin, sélectionnez le hub d’événements que vous avez créé.

4. Sélectionnez **Configurer**, puis créez deux nouvelles stratégies d’accès en utilisant les informations suivantes.

	<table>
	<tr><th>Nom</th><th>Autorisations</th></tr>
	<tr><td>Writer</td><td>Envoyer</td></tr>
	<tr><td>Lecteur</td><td>Écouter</td></tr>
	</table>

	Après avoir créé les autorisations, sélectionnez l’icône **Enregistrer** située en bas de page. Cela crée les stratégies d’accès partagé qui seront utilisées pour envoyer (writer) et écouter (reader) ce hub d’événements.

	![stratégies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Après avoir enregistré vos stratégies, utilisez le **Générateur de clés d’accès partagé** situé en bas de page pour récupérer les clés des stratégies **writer** et **reader**. Enregistrez-les, car elles seront utilisées plus tard.

## Configuration du stockage de table

Le stockage de table sera utilisé pour conserver les valeurs lues à partir de Event Hubs, car il est très facile d’afficher le stockage de table à partir de Visual Studio via l’**Explorateur de serveurs**. Pour créer un nouveau stockage de table, procédez comme suit :

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **NOUVEAU** > **Data Services** > **Stockage** > **Création rapide**.

	![création rapide de stockage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Entrez un **nom** pour le compte de stockage, sélectionnez un **emplacement**, puis cliquez sur la **coche** pour créer le compte de stockage.

	> [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que celui de votre hub d’événements et de serveur Storm sur HDInsight pour réduire la latence et les coûts.

3. Lorsque le nouveau compte de stockage est configuré, sélectionnez le compte, puis utilisez le lien**Gérer les clés d’accès** situé en bas de page pour récupérer le **Nom du compte de stockage** et la **Clé d’accès primaire**. Enregistrez ces informations, car elles vous en aurez besoin ultérieurement.

	![clés d'accès](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Création de EventHubWriter

Dans cette section, vous allez créer une topologie qui écrit des données vers Event Hubs à l’aide du bolt Event Hubs.

1. Si vous n’avez pas encore installé la dernière version de HDInsight Tools pour Visual Studio, consultez la rubrique<a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Prise en main de HDInsight Tools pour Visual Studio</a>.

2. Ouvrez Visual Studio, sélectionnez **Fichier** > **Nouveau**, puis **Projet**.

3. Dans l’écran **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Application Storm**. En bas de l’écran, entrez **EventHubWriter** comme nom de l’application.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Une fois le projet créé, vous devez avoir les fichiers suivants :

	* **Program.cs** : définit la topologie de votre projet. Notez que, par défaut, une topologie qui se compose d’un seul spout et d’un seul bolt est créée.

	* **Spout.cs** : exemple de spout.

	* **Bolt.cs** : exemple de bolt. Il sera supprimé lorsque vous utiliserez le bolt Event Hubs pour écrire dans le hub d’événements.

### Configuration

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur **EventHubWriter**, puis sélectionnez **Propriétés**.

2. Dans les propriétés du projet, sélectionnez **Paramètres**, puis sélectionnez **Ce projet ne contient pas de fichier de paramètres par défaut. Cliquez ici pour en créer un.**

3. Entrez les paramètres suivants. Utilisez les informations du hub d’événements que vous avez créé précédemment dans la colonne **Valeur**.

	<table>
	<tr><th style="text-align:left">Nom</th><th style="text-align:left">Type</th><th style="text-align:left">Scope</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">eventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">eventHubName</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Application</td></tr>
	</table>

4. Enregistrez et fermez la page **Propriétés**.

### Définition de la topologie

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur **Bolt.cs**, puis sélectionnez **Supprimer**. Étant donné que vous utilisez le bolt Event Hubs Java, vous n’avez pas besoin de ce fichier.

2. Ouvrez le fichier **Program.cs** et ajoutez le code suivant code immédiatement après la ligne `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	La première ligne lit le nombre de partitions dans les propriétés définies précédemment. La deuxième ligne définit un désérialiseur qui sert à désérialiser les données JSON produites par le spout, dans un `java.lang.String` afin que les composants Java puissent consommer les données.

4. Recherchez le code suivant :

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Remplacez-le par celui-ci :

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Il crée un spout et utilise le nombre de partitions d’Event Hubs comme indicateur de parallélisme pour ce composant. Cela doit créer une instance du spout pour chaque partition.

	Ce code associe également le désérialiseur créé précédemment avec le flux de sortie de ce composant. Le composant EventHubSpout en aval peut ainsi consommer les données générées à partir du spout C#.

5. Immédiatement après le code précédent, ajoutez le code suivant :

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
			"true"));

	Ce code crée constructeur pour le bolt Java, qui est utilisé au moment de l’exécution pour configurer une nouvelle instance du bolt. Dans ce cas, vous utilisez l’<a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> pour configurer le spout avec les informations de configuration d’Event Hubs que vous avez ajoutées précédemment. Plus précisément, ce code est utilisé par HDInsight au moment de l’exécution pour effectuer les opérations suivantes :

	* Créer une nouvelle instance de **com.microsoft.eventhubs.bolt.EventHubBoltConfig** en utilisant les informations Event Hubs que vous fournissez.
	* Créer une nouvelle instance de **com.microsoft.eventhubs.bolt.EventHubBolt** en passant l’instance **EventHubBoltConfig**.

6. Recherchez le code suivant :

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Remplacez-le par celui-ci :

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Ce code indique à la topologie qu’il faut utiliser le **JavaComponentConstructor** de l’étape précédente comme bolt. Dans cette topologie, il est possible de faire référence à ce composant par le nom convivial « EventHubBolt ». L'indicateur de parallélisme est défini sur le nombre de partitions du hub d'événements et il s'abonne aux données produites par le spout (« spout »).

À ce stade, vous en avez terminé avec le **Program.cs**. La topologie a été définie, mais vous devez maintenant modifier **Spout.cs** afin qu’il génère des données dans un format utilisable par le bolt de Event Hubs.

> [AZURE.NOTE] Par défaut, cette topologie va créer un seul processus de travail, ce qui est suffisant pour notre exemple. Si vous l’adaptez pour un cluster de production, vous devez ajouter le code suivant pour modifier le nombre de processus de travail :

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Modification du spout

Le bolt de Event Hubs attend une valeur de chaîne unique, qu’il acheminera vers le hub d’événements. Dans l’exemple suivant, vous allez modifier le fichier **Spout.cs** par défaut pour générer une chaîne JSON.

1. Dans l'**Explorateur de solutions**, ouvrez **Spout.cs** et ajoutez le code suivant en haut du fichier :

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Cela nous permettra de travailler plus facilement avec des données JSON.
    
    > [AZURE.NOTE] Le package JSON.NET doit être déjà installé, comme requis par l'infrastructure SCP.NET utilisée pour les topologies C# Storm.

3. Recherchez le code suivant :

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Remplacez-le par celui-ci :

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Cela modifie la définition de données créées par le spout afin d’utiliser les données de type **string** et le **CustomizedInteropJSONSerializer** déclaré plus tôt dans la topologie (dans le fichier program.cs).

2. Remplacez la méthode **NextTuple** par le code suivant :

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Ce code génère de manière aléatoire un ID d’appareil, une valeur, puis utilise Json.NET pour émettre un objet JSON à l’aide de ces valeurs.

3. Enregistrez le fichier **Spout.cs**.

À ce stade, vous disposez d’une topologie de base qui génère des données aléatoires et les stocke dans Event Hubs à l’aide du bolt de hubs d’événements. Ensuite, vous allez créer le lecteur.

## Création de EventHubReader

Dans cette section, vous allez créer une topologie qui lit des données à partir de Event Hubs en utilisant le spout Event Hubs.

2. Ouvrez Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

3. Dans l’écran **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Application Storm**. En bas de l’écran, entrez **EventHubReader** comme nom d’application.

### Configuration

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur **EventHubReader**, puis sélectionnez **Propriétés**.

2. Dans les propriétés du projet, sélectionnez **Paramètres**, puis sélectionnez **Ce projet ne contient pas de fichier de paramètres par défaut. Cliquez ici pour en créer un.**

3. Entrez les paramètres suivants. Utilisez les informations du hub d’événements et du compte de stockage que vous avez créés précédemment dans la colonne **Valeur**.

	<table>
	<tr><th style="text-align:left">Nom</th><th style="text-align:left">Type</th><th style="text-align:left">Scope</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">eventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">eventHubName</th><th style="text-align:left">string</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(Chaîne de connexion)</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">Application</th></tr>
	</table>

	Pour **TableName**, entrez le nom de la table dans laquelle vous souhaitez stocker les événements.

    Pour **StorageConnection**, entrez une valeur de `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;`. Remplacez **myAccount** et **myKey** par le nom du compte de stockage et la clé obtenue précédemment.

	Ces valeurs seront utilisées par la topologie pour communiquer avec Event Hubs et le stockage de table.

4. Enregistrez et fermez la page **Propriétés**.

### Définition de la topologie

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur **Spout.cs** et sélectionnez **Supprimer**. Étant donné que vous utilisez le spout Event Hubs Java, vous n’avez pas besoin de ce fichier.

2. Ouvrez le fichier **Program.cs** et ajoutez le code suivant code immédiatement après la ligne`TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` :

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);

	Le nombre de partitions est lu et affecté à une variable locale. Il sera utilisé plusieurs fois.

	`EventHubSpoutConfig` définit la configuration de spout Event Hubs. Dans ce cas, il s’agit des informations de configuration Event Hubs que vous avez ajoutées précédemment. En arrière-plan, ceci utilise le spout Java Event Hubs et crée une instance de **com.microsoft.eventhubs.spout.EventHubSpoutConfig** à l’aide des informations Event Hubs que vous fournissez.

5. Recherchez le code suivant :

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Remplacez-le par celui-ci :

        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 

	Cela indique à la topologie de créer un spout Event Hubs et d’utiliser le `EventHubSpoutConfig` de l’étape précédente en tant que configuration. « EventHubSpout » définit le nom convivial du spout, et `partitionCount` est utilisé pour définir l’indicateur de parallélisme. En arrière-plan, ceci crée une instance du composant Java **com.microsoft.eventhubs.spout.EventHubSpout** à l’aide des informations de configuration fournies.

2. Ajoutez le code suivant juste après le code précédent :

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Ce code crée un sérialiseur personnalisé qui sera utilisé pour sérialiser les informations générées par les composants Java (par exemple l’EventHubSpout) au format JSON que les composants C# en aval peuvent utiliser.

3. Recherchez le code suivant :

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Remplacez-le par celui-ci :

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Ce code fait en sorte que la topologie utilise un bolt (défini dans Bolt.cs). Le sérialiseur personnalisé défini précédemment est utilisé ici pour que ce bolt puisse consommer les données produites par les composants Java en amont. En l’occurrence, EventHubSpout.

    > [AZURE.IMPORTANT] Le dernier paramètre pour SetBolt, (dont la valeur est `true`,) active la fonctionnalité d’accusé de réception pour ce bolt. Cela est nécessaire, car le composant EventHubSpout attend un accusé de réception pour les données qu’il émet. Si les accusés de réception ne sont pas retournés par les composants en aval, le spout cesse de recevoir après le traitement de 1000 messages environ.

À ce stade, vous en avez terminé avec **Program.cs**. La topologie a été définie, mais vous devez maintenant créer une classe d’assistance pour écrire des données dans le stockage de table, puis modifier **Bolt.cs** afin qu’il puisse comprendre les données générées par le spout.

> [AZURE.NOTE] Par défaut, cette topologie va créer un seul processus de travail, ce qui est suffisant pour notre exemple. Si vous l’adaptez pour un cluster de production, vous devez ajouter le code suivant pour modifier le nombre de processus de travail :

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Création d'une classe d'assistance

Lors de l’écriture des données dans le stockage de table, vous devez créer une classe qui décrit les données qui seront écrites.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubReader** et sélectionnez **Ajouter**, puis **Nouvelle classe**. Nommez la nouvelle classe **Devices.cs**.

2. Ouvrez **Devices.cs** et remplacez le code par défaut par le code suivant :

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;

		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }

		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	Ce code crée des entités dans le stockage de table qui sont constituées d’une clé de partition (qui aura comme valeur l’ID d’appareil lue à partir du hub d’événements), d’une clé de ligne unique et d’une valeur lue à partir du hub d’événements. Chaque entité aura également un horodatage, qui sera créé automatiquement lors de l’insertion de l’entité dans la table.

### Modification du bolt

1. Dans l'**Explorateur de solutions**, développez le projet **EventHubReader** et ouvrez le fichier **Bolt.cs**. Ajoutez le code suivant au début du fichier :

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Cela nous permettra de travailler plus facilement avec les données JSON du bolt et d'écrire des données dans le stockage de table.

2. Recherchez l’instruction `private int count;` et remplacez-la par l’instruction suivante :

        private CloudTable table;

	Elle sera utilisée lors de la connexion à la table.

4. Recherchez le code suivant :

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Remplacez-le par celui-ci :

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Ce code indique au bolt qu’il recevra une valeur de type **string** au lieu d’une valeur de type **int**, et que les données doivent être désérialisées à l’aide de **CustomizedInteropJSONDeserialzer** qui a été précédemment déclaré dans la topologie (dans le fichier program.cs).

3. Immédiatement après le code précédent, ajoutez le code suivant :

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Ce code établit une connexion à la table **events** à l’aide de la chaîne de connexion configurée précédemment. Si elle n'existe pas, elle sera créée.

2. Recherchez la méthode **Execute** et remplacez-la par la méthode suivante :

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }

	Ce code utilise Json.NET pour analyser les données JSON du spout, puis sélectionne les champs **deviceId** et **deviceValue**. Un nouvel objet **Device** est ensuite créé à l’aide de **deviceId** pendant l’initialisation pour définir la clé de partition de la table. La valeur est ensuite définie sur **deviceValue**, puis l’entité est insérée dans la table.

    Une fois l’entité insérée dans la table, `Ack()` est appelé pour le tuple, afin d’informer le spout que les données ont été traitées correctement.

    > [AZURE.IMPORTANT] Le composant EventHubSpout attend, pour chaque tuple, un accusé de réception provenant des composants en aval comme ce bolt. Si aucun accusé de réception n’est reçu, le EventHubSpout supposera que le traitement du tuple a échoué.

À ce stade, vous disposez d’une topologie complète qui lit des données du hub d’événements et qui les stocke dans le stockage de table dans une table nommée **events**.

## Déploiement des topologies

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubReader** et sélectionnez **Envoyer à Storm sur HDInsight**.

	![envoyer à Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Dans l’écran **Envoyer la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d’accès aux fichiers Java**, sélectionnez **...**, puis sélectionnez le répertoire qui contient le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que vous avez téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.

	![Image de boîte de dialogue de soumission](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Une fois la topologie envoyée, la **Visionneuse de topologies Storm** apparaît. Sélectionnez la topologie **EventHubReader** dans le volet gauche pour afficher les statistiques de la topologie. Rien ne doit se produire pour le moment car aucun événement n’a encore été écrit dans les hubs d’événements.

	![affichage d’un exemple de stockage](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubWriter** et sélectionnez **Envoyer à Storm sur HDInsight**.

2. Dans l’écran **Envoyer la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d’accès aux fichiers Java**, puis **...**, puis sélectionnez le répertoire qui contient le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.

5. Une fois la topologie envoyée, actualisez la liste des topologies dans la **Visionneuse de topologies Storm** pour vérifier que les deux topologies sont en cours d’exécution sur le cluster.

6. Lorsque les deux topologies sont en cours d’exécution, sélectionnez **Explorateur de serveurs**, puis développez **Azure** > **Stockage**, puis sélectionnez le compte de stockage que vous avez créé précédemment. Sous le compte de stockage, développez **Tables**. Enfin, double-cliquez sur la table **events** pour ouvrir la table. Vous devez voir les données qui ont été stockées dans la table à partir de la topologie **EventHubReader**.

	* Les événements sont générés par la topologie **EventHubWriter**, qui les écrit dans le hub d’événements.

	* **EventHubReader** lit ensuite les événements à partir des hubs d’événements et les stocke dans le stockage de table, dans la table **events**.

## Arrêt des topologies

Pour arrêter les topologies, sélectionnez chaque topologie dans la **Visionneuse de topologies Storm**, puis cliquez sur **Supprimer**.

![image d’arrêt d’une topologie](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##Supprimer votre cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Remarques

### Points de contrôle

EventHubSpout contrôle régulièrement son état au nœud Zookeeper, qui enregistre l’offset actuel des messages lus depuis la file d’attente. Il permet au composant de commencer à recevoir des messages à l’offset enregistré dans les scénarios suivants :

* Échec et redémarrage de l’instance du composant.

* Agrandissement ou réduction du cluster en ajoutant ou supprimant des nœuds.

* Arrêt et redémarrage de la topologie **sous le même nom**.

Vous pouvez également exporter et importer les points de contrôle persistants vers le WASB (le stockage Azure utilisé par votre cluster HDInsight.) Les scripts qui permettent de le faire sont situés sur le cluster Storm sur HDInsight, dans **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE] Le numéro de version dans le chemin d’accès peut être différent, car la version de Storm installée sur le cluster peut changer dans le futur.

Les scripts présents dans ce répertoire sont :

* **stormmeta\_import.cmd** : pour importer toutes les métadonnées Storm du conteneur de stockage de clusters par défaut dans Zookeeper.

* **stormmeta\_export.cmd** : pour exporter toutes les métadonnées Storm de Zookeeper vers le conteneur de stockage de clusters par défaut.

* **stormmeta\_delete.cmd** : pour supprimer toutes les métadonnées Storm de Zookeeper.

L’exportation d’une importation vous permet de conserver les données du point de contrôle lorsque vous devez supprimer le cluster, mais que vous souhaitez reprendre le traitement à partir de l’offset actuel dans le hub lorsque vous remettez un nouveau cluster en ligne.

> [AZURE.NOTE] Dans la mesure où les données sont conservées dans le conteneur de stockage par défaut, le nouveau cluster **doit** utiliser le même compte de stockage et le même conteneur que le cluster précédent.

## Étapes suivantes

Dans ce document, vous avez découvert comment utiliser le spout et le bolt du hub d'événements Java à partir d'une topologie C# pour travailler avec des données dans le hub d'événements Azure. Pour en savoir plus sur la création de topologies C#, consultez les rubriques suivantes.

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)
 

<!------HONumber=AcomDC_0309_2016-->