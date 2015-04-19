<properties
   pageTitle="Traitement des événements à partir du hub d'événements avec Storm sur HDInsight | Azure"
   description="Découvrez comment traiter des données du hub d'événements avec une topologie C# Storm créée dans Visual Studio à l'aide de HDInsight Tools pour Visual Studio."
   services="hdinsight"
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
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#Traitement des événements du hub d'événements Azure avec Storm sur HDInsight (C#)

Le hub d'événements Azure vous permet de traiter d'énormes quantités de données de sites web, d'applications et d'appareils. Le spout de hub d'événements simplifie l'utilisation d'Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans le hub d'événements à partir de Storm à l'aide du bolt de hub d'événements. 

Dans ce document, vous allez apprendre à utiliser HDInsight Tools pour Visual Studio et le spout et le bolt de hub d'événements pour créer deux topologies C#/Java hybrides :

* **EventHubWriter** : génère des données de manière aléatoire et les écrit dans le hub d'événements

* **EventHubReader** : lit les données à partir du hub d'événements et les stocke dans le stockage de table Azure

##Configuration requise

* Un <a href="../hdinsight-storm-getting-started/" target="_blank">cluster Apache Storm sur HDInsight</a>

* Un <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">hub d'événements Azure</a>

* Le <a href="http://azure.microsoft.com/downloads/" target="_blank">Kit de développement logiciel SDK Azure .NET</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools pour Visual Studio</a> : la version du 17/02/2015 ou une version plus récente

##Projet terminé

Vous pouvez télécharger une version complète du projet créé dans cet article à l'adresse [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid) ; toutefois, vous devrez quand même fournir des paramètres de configuration en suivant les étapes décrites dans ce document.

> [AZURE.NOTE] Lors de l'utilisation du projet terminé, vous devez utiliser le **Gestionnaire de Package NuGet** pour restaurer les packages requis par cette solution.

##Spout et bolt de hub d'événements

Le spout et le bolt de hub d'événements sont des composants Java qui vous permettent de travailler facilement avec le hub d'événements à partir d'Apache Storm. Bien que ces composants soient écrits en Java, HDInsight Tools pour Visual Studio vous permet de créer des topologies hybrides qui combinent des composants Java et C#.

Le spout et le bolt sont distribués dans un fichier d'archive Java (.jar) unique nommé **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

###Téléchargement du fichier Jar

La version la plus récente du fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** est fournie dans le projet <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> sous le dossier **lib**. Pour télécharger le fichier, appliquez l'une des méthodes suivantes.

> [AZURE.NOTE] Le spout et le bolt ont été soumis pour inclusion dans le projet Apache Storm. Pour plus d'informations, consultez la <a href="https://github.com/apache/storm/pull/336/files">demande d'extraction</a>.

* **Téléchargement du fichier ZIP** : à partir du site <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a>, sélectionnez le bouton **Download ZIP** pour télécharger un fichier .zip contenant le projet.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Une fois le téléchargé effectué, vous pouvez extraire l'archive. Le fichier se trouvera dans le répertoire **lib**.

* **Clonage du projet** : si <a href="http://git-scm.com/" target="_blank">Git</a> est installé, exécutez la commande suivante pour cloner le référentiel localement, puis recherchez le fichier dans le répertoire **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

##Configuration du hub d'événements

Le hub d'événements est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau hub d'événements.

1. ﻿À partir du [portail Azure](https://manage.windowsazure.com), sélectionnez **Nouveau | Service Bus | hub d'événements | Création personnalisée**.

2. Dans la boîte de dialogue **Ajouter un nouveau hub d'événements**, entrez un **Nom de hub d'événements**, sélectionnez la **Région** dans laquelle créer le hub, puis créez un espace de noms ou sélectionnez-en un existant. Pour terminer, cliquez sur la **Flèche**.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que celui de votre serveur Storm sur HDInsight pour réduire la latence et les coûts.

2. Dans la boîte de dialogue **Configurer le hub d'événements**, entrez les valeurs pour **Nombre de partitions** et **Conservation des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous aurez besoin de cette valeur ultérieurement.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Une fois le hub d'événements créé, sélectionnez l'espace de noms puis **hubs d'événements**. Enfin, sélectionnez le hub d'événements créé précédemment.

4. Sélectionnez **Configurer**, puis créez deux stratégies d'accès en utilisant les informations suivantes.

	<table>
	<tr><th>Nom</th><th>Autorisations</th></tr>
	<tr><td>writer</td><td>Envoyer</td></tr>
	<tr><td>reader</td><td>Écouter</td></tr>
	</table>

	Après avoir créé des autorisations, sélectionnez l'icône **Enregistrer** au bas de la page. Cela crée les stratégies d'accès partagé qui seront utilisées pour envoyer (writer) et écouter (reader) ce hub d'événements.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Après avoir enregistré les stratégies, utilisez le **Générateur de clés d'accès partagé** au bas de la page pour récupérer les clés des stratégies **writer** et **reader**. Enregistrez-les, car elles serviront plus tard.

##Configuration du stockage de table

Le stockage de table sera utilisé pour stocker les valeurs lues à partir du hub d'événements, car il est très facile d'afficher le stockage de table à partir de Visual Studio via l'**Explorateur de serveurs**. Utilisez les étapes suivantes pour créer le stockage de table.

1. Dans le [Portail Azure](https://manage.windowsazure.com), sélectionnez **Nouveau | Services de données | Stockage | Création rapide**.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Entrez un **nom** pour le compte de stockage, sélectionnez un **emplacement**, puis sélectionnez la **coche** pour créer le compte de stockage.

	> [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que celui de votre hub d'événements et votre serveur Storm sur HDInsight pour réduire la latence et les coûts.

3. Une fois le nouveau compte de stockage mis en service, sélectionnez-le, puis utilisez le lien **Gérer les clés d'accès** en bas de la page pour récupérer le **Nom du compte de stockage** et la **Clé d'accès primaire**. Enregistrez ces informations, car vous en aurez besoin ultérieurement.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

##Création de l'EventHubWriter

Dans cette section, vous allez créer une topologie qui écrit des données dans le hub d'événements à l'aide du bolt de hub d'événements.

1. Si vous n'avez pas encore installé la dernière version de HDInsight Tools pour Visual Studio, consultez <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Prise en main de HDInsight Tools pour Visual Studio</a>.

2. Ouvrez Visual Studio, sélectionnez **Fichier**, **Nouveau**, puis **Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, développez **Installé**, **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Application Storm**. En bas de la boîte de dialogue, entrez **EventHubWriter** comme nom d'application.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Une fois le projet créé, vous devez avoir les fichiers suivants :

	* **Program.cs** : définit la topologie de votre projet. Notez que, par défaut, une topologie consistant en un seul spout et un seul bolt est créée

	* **Spout.cs** : exemple de spout

	* **Bolt.cs** : exemple de bolt. Il sera supprimé quand vous utiliserez le bolt de hub d'événements pour écrire dans le hub d'événements

###Configuration

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **EventHubWriter**, puis sélectionnez **Propriétés**.

2. Dans les propriétés du projet, sélectionnez **Paramètres**, puis sélectionnez **Ce projet ne contient pas de fichier de paramètres par défaut. Cliquez ici pour en créer un.**

3. Entrez les paramètres suivants. Utilisez les informations du hub d'événements que vous avez créé précédemment dans la colonne **Valeur**.

	<table>
	<tr><th style="text-align:left">Nom</th><th style="text-align:left">Type</th><th style="text-align:left">Scope</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">eventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">eventHubName</td><td style="text-align:left">string</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Application</td></tr>
	</table>

4. Enregistrez et fermez la page de propriétés.

###Définition de la topologie

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Bolt.cs** et sélectionnez **Supprimer**. Étant donné que vous utilisez le bolt de hub d'événements Java, vous n'avez pas besoin de ce fichier.

2. Ouvrez le fichier **Program.cs** et ajoutez le code suivant juste après la ligne `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	La première ligne lit le nombre de partitions dans les propriétés définies précédemment. La deuxième ligne définit un désérialiseur qui sert à déséraliser les données JSON produites par le spout, dans un  `java.lang.String` afin que le bolt de hub d'événements puisse consommer les données.

4. Recherchez le code suivant.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Il crée un spout et utilise le nombre de partitions du hub d'événements comme indicateur de parallélisme pour ce composant. Cela doit créer une instance du spout pour chaque partition.
	
	Ce code déclare également que le flux de sortie doit utiliser le désérialiseur créé précédemment.

5. Immédiatement après le code précédent, ajoutez le code suivant.

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

	Ce code crée un constructeur pour le bolt Java, qui est utilisé au moment de l'exécution pour configurer une nouvelle instance du bolt. Dans ce cas, vous utilisez l'<a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> configurer le spout avec les informations de configuration du hub d'événements que vous avez ajoutées précédemment. Plus précisément, ce code est utilisé par HDInsight au moment de l'exécution pour effectuer les opérations suivantes :

	* Créer une instance de **com.microsoft.eventhubs.bolt.EventHubBoltConfig** à l'aide des informations de hub d'événements que vous fournissez.
	* Créer une instance de **com.microsoft.eventhubs.bolt.EventHubBolt**, en passant l'instance **EventHubBoltConfig**.

6. Recherchez le code suivant.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Replace it with the following.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Ce code indique à la topologie qu'il faut utiliser le **JavaComponentConstructor** de l'étape précédente comme bolt. Dans cette topologie, il est possible de faire référence à ce composant par le nom convivial " EventHubBolt ". L'indicateur de parallélisme est défini sur le nombre de partitions du hub d'événements et il s'abonne aux données produites par le spout (" spout ").

À ce stade, vous en avez terminé avec **Program.cs**. La topologie a été définie, mais vous devez maintenant modifier **Spout.cs** afin qu'il génère des données dans un format utilisable par le bolt de hub d'événements.

###Modification du spout

Le bolt de hub d'événements attend une valeur de chaîne unique, qu'il acheminera vers le hub d'événements. Dans l'exemple suivant, vous allez modifier le fichier **Spout.cs** par défaut pour générer une chaîne JSON.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **EventHubWriter** et sélectionnez **Gérer les packages Nuget**. Recherchez le package **Json.Net**  et ajoutez-le à la solution. Cela nous permettra de créer facilement des données JSON qui seront envoyées au hub d'événements à l'aide du bolt.

1. Ouvrez **Spout.cs** et ajoutez le code suivant en haut du fichier.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Cela nous permettra de travailler plus facilement avec des données JSON.

3. Recherchez le code suivant.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Remplacez-le par le code suivant.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Cela modifie la définition des données créées par le spout afin d'utiliser des données de type **string** et un **sérialiseur JSON personnalisé**.

2. Remplacez la méthode **NextTuple** par le code suivant.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Ce code génère de façon aléatoire un ID d'appareil, une valeur, puis utilise Json.net pour émettre un objet JSON à l'aide de ces valeurs.

3. Enregistrez le fichier **Spout.cs**.

À ce stade, vous disposez d'une topologie de base qui génère des données aléatoires et les stocke dans le hub d'événements à l'aide du bolt de hub d'événements. Ensuite, vous allez créer le lecteur.

##Création de l'EventHubReader

Dans cette section, vous allez créer une topologie qui lit des données à partir du hub d'événements à l'aide du spout de hub d'événements.

2. Ouvrez Visual Studio, sélectionnez **Fichier**, **Nouveau**, puis **Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, développez **Installé**, **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Application Storm**. En bas de la boîte de dialogue, entrez **EventHubReader** comme nom d'application.

###Configuration

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **EventHubReader**, puis sélectionnez **Propriétés**.

2. Dans les propriétés du projet, sélectionnez **Paramètres**, puis sélectionnez **Ce projet ne contient pas de fichier de paramètres par défaut. Cliquez ici pour en créer un.**

3. Entrez les paramètres suivants. Utilisez les informations du hub d'événements et du compte de stockage que vous avez créés précédemment dans la colonne **Valeur**.

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

	Ces valeurs seront utilisées par la topologie pour communiquer avec le hub d'événements et le stockage de table.

4. Enregistrez et fermez la page de propriétés.

###Définition de la topologie

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Spout.cs** et sélectionnez **Supprimer**. Étant donné que vous utilisez le spout de hub d'événements Java, vous n'avez pas besoin de ce fichier.

2. Ouvrez le fichier **Program.cs** et ajoutez le code suivant juste après la ligne `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	Le nombre de partitions est lu dans (et assigné à) une variable locale, car il sera utilisé plusieurs fois.

	 `JavaComponentConstructor` définit comment le spout Java sera construit au moment de l'exécution. Dans ce cas, vous utilisez l'<a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> configurer le spout avec les informations de configuration du hub d'événements que vous avez ajoutées précédemment. Plus précisément, ce code est utilisé par HDInsight au moment de l'exécution pour effectuer les opérations suivantes :

	* Créez une instance de **com.microsoft.eventhubs.spout.EventHubSpoutConfig** en utilisant les informations du hub d'événements que vous fournissez.
	
	* Créer une instance de **com.microsoft.eventhubs.spout.EventHubSpout**, en passant l'instance **EventHubSpoutConfig**.

5. Recherchez le code suivant.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Ce code indique à la topologie qu'il faut utiliser le **JavaComponentConstructor** de l'étape précédente comme spout et lui donner le nom " EventHubSpout ". Il affecte également le nombre de partitions dans le hub d'événements comme indicateur de parallélisme pour ce composant. 

2. Ajoutez le code suivant juste après le code précédent.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Ce code crée un sérialiseur personnalisé qui sera utilisé pour sérialiser les informations générées par le spout Java au format JSON.

3. Recherchez le code suivant.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Remplacez-le par le code suivant.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Ce code fait en sorte que la topologie utilise un bolt (défini dans Bolt.cs). Les ajouts indiquent à la topologie qu'il faut utiliser le sérialiseur personnalisé pour les données consommées par ce composant, qui proviennent d'**EventHubSpout** - le spout Java.

À ce stade, vous en avez terminé avec **Program.cs**. La topologie a été définie, mais vous devez maintenant créer une classe d'assistance pour écrire des données dans le stockage de table, puis modifier **Bolt.cs** afin qu'il puisse comprendre les données générées par le spout.

###Création d'une classe d'assistance

Lors de l'écriture des données dans le stockage de table, vous devez créer une classe qui décrit les données qui seront écrites.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **EventHubReader** et sélectionnez **Ajouter**, **Nouvelle classe**. Nommez la nouvelle classe **Devices.cs**.

2. Ouvrez le fichier **Devices.cs** et remplacez le code par défaut par le suivant.

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

	Ce code crée des entités dans le stockage de table qui sont constituées d'une clé de partition (qui aura comme valeur l'ID d'appareil lue à partir du hub d'événements), d'une clé de ligne unique et d'une valeur lue à partir du hub d'événements. Chaque entité aura également un horodatage, créé automatiquement lors de l'insertion de l'entité dans la table.

###Modification du bolt

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **EventHubReader** et sélectionnez **Gérer les packages Nuget**. Recherchez le package **Json.Net**  et ajoutez-le à la solution. Cela nous permettra de traiter facilement les données JSON en provenance du spout. Ajoutez également le package **Stockage Windows Azure**, qui nous permettra d'écrire dans le stockage de table.

1. Ouvrez **Bolt.cs** et ajoutez le code suivant en haut du fichier.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Cela nous permettra de travailler plus facilement avec les données JSON du bolt et d'écrire des données dans le stockage de table.

2. Recherchez l'instruction `private int count;` et remplacez-la par l'instruction suivante.

        private CloudTable table;

	Elle sera utilisée lors de la connexion à la table.

4. Recherchez le code suivant.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Remplacez-le par le code suivant.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Ce code indique au bolt qu'il recevra une valeur  de type **string** au lieu d'une valeur de type **int** et que les données doivent être désérialisées à l'aide de **CustomizedInteropJSONDeserialzer**.

3. Immédiatement après le code précédent, ajoutez le code suivant.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Ce code établit une connexion à la table **events** à l'aide de la chaîne de connexion configurée précédemment. Si elle n'existe pas, elle sera créée.

2. Recherchez la méthode **Execute** et remplacez-la par le code suivant.

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
            }
        }

	Ce code utilise Json.net pour analyser les données JSON du spout, puis sélectionne les champs **deviceId** et **deviceValue**. Un nouvel objet **Device** est ensuite créé à l'aide du **deviceId** pendant l'initialisation pour définir la clé de partition pour la table. La valeur est ensuite définie sur **deviceValue**, puis l'entité est insérée dans la table.

À ce stade, vous disposez d'une topologie complète qui lit des données du hub d'événements et les stocke dans le stockage de table dans une table nommée **events**.

##Déploiement des topologies

1. Ouvrez la solution **EventHubReader**. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **EventHubReader** et sélectionnez **Envoyer à Storm sur HDInsight**.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Dans la boîte de dialogue **Soumettre la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d'accès aux fichiers Java**, sélectionnez **...**, puis sélectionnez le répertoire qui contient le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** téléchargé précédemment. Pour finir, sélectionnez **Soumettre**.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Une fois la topologie soumise, la **Visionneuse de topologies Storm** apparaît. Sélectionnez la topologie **EventHubReader** à gauche de la boîte de dialogue pour afficher les statistiques de la topologie. Rien ne doit se produire actuellement car aucun événement n'a encore été écrit dans le hub d'événements.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Ouvrez la solution **EventHubWriter**. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **EventHubWriter** et sélectionnez **Envoyer à Storm sur HDInsight**.

2. Dans la boîte de dialogue **Soumettre la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d'accès aux fichiers Java**, sélectionnez **...**, puis sélectionnez le répertoire qui contient le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** téléchargé précédemment. Pour finir, sélectionnez **Soumettre**.

5. Une fois la topologie soumise, actualisez la liste de topologies dans la **Visionneuse de topologies Storm** pour vérifier que les deux sont en cours d'exécution sur le cluster.

6. Une fois que les deux sont en cours d'exécution, sélectionnez **Explorateur de serveurs**, puis développez **Azure**, **Stockage**, puis le compte de stockage que vous avez créé précédemment. Sous le compte de stockage, développez **Tables**. Pour finir, double-cliquez sur la table **events** pour l'ouvrir. Vous devriez voir les données qui ont été stockées dans la table à partir de la topologie **EventHubReader**.

	* Les événements sont générés par la topologie **EventHubWriter**, qui les écrit dans le hub d'événements.

	* **EventHubReader** lit ensuite les événements à partir du hub d'événements et les stocke dans le stockage de table, dans la table **events**.

##Arrêt des topologies

Pour arrêter les topologies, sélectionnez chaque topologie dans la **Visionneuse de topologies Storm**, puis sélectionnez **Supprimer**.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##Résumé

Dans ce document, vous avez découvert comment utiliser le spout et le bolt du hub d'événements Java à partir d'une topologie C# pour travailler avec des données dans le hub d'événements Azure. Pour en savoir plus sur la création de topologies C#, consultez les rubriques suivantes.

* [Développement de topologies C# pour Apache Storm sur HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Exemples Storm HDInsight](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
