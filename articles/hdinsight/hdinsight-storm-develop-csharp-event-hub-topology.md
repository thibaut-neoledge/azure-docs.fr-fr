---
title: "Traitement des événements d’Event Hubs avec Storm sur HDInsight | Microsoft Docs"
description: "Découvrez comment traiter les données de hubs d’événements avec une topologie Storm C# créée dans Visual Studio à l’aide de HDInsight Tools pour Visual Studio."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c9a5091973395dd888939432292fbd06dcbf0680
ms.openlocfilehash: c0349b5890a75c6ffaa6b7eca93baa3101912cf6


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Traitement des événements Azure Event Hubs avec Storm sur HDInsight (C#)

Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hubs simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

Dans ce didacticiel, vous découvrirez comment utiliser les modèles Visual Studio installés avec les Outils HDInsight pour Visual Studio afin de créer deux topologies qui fonctionnent avec les hubs d’événements Azure.

* **EventHubWriter**: génère des données de façon aléatoire et les écrit dans les hubs d’événements
* **EventHubReader** : lit les données à partir d’Event Hubs et enregistre les données dans les journaux de Storm

> [!NOTE] 
> Pour obtenir une version Java de ce projet, consultez [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Ces projets utilisent SCP.NET, un package NuGet qui vous permet de créer des topologies et des composants C# pour les utiliser avec Storm sur HDInsight.

> [!IMPORTANT]
> Bien que les étapes décrites dans ce document reposent sur un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à un cluster Storm sur HDInsight utilisant Linux. __Seuls les clusters basés sur Linux créés après le 28/10/2016 prennent en charge les topologies SCP.NET.__

### <a name="cluster-versioning"></a>Contrôle de version de cluster

Le package NuGet Microsoft.SCP.Net.SDK utilisé par votre projet doit correspondre à la version principale de Storm installée sur HDInsight. Les versions 3.3 et 3.4 de Storm sur HDInsight utilisent la version 0.10.x de Storm. Vous devez donc utiliser la version 0.10.x.x de SCP.NET avec ces clusters. La version 3.5 de HDInsight utilise la version 1.0.x de Storm. Vous devez donc utiliser la version 1.0.x.x de SCP.NET avec cette version de cluster.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

La version 3.4 de HDInsight (et les versions suivantes) utilisent Mono pour exécuter des topologies C#. La plupart des éléments fonctionneront avec Mono. Cependant, vous devez consulter le document [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/) pour identifier les éventuelles incompatibilités.

Les topologies C# doivent également cibler la version 4.5 de .NET.

## <a name="how-to-work-with-event-hubs"></a>Utilisation d’Event Hubs

Microsoft fournit un ensemble de composants Java qui peut être utilisé pour communiquer avec Azure Event Hubs à partir d’une topologie Storm. Le fichier jar contenant la dernière version de ces composants est disponible sur [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

> [!IMPORTANT]
> Même si les composants sont écrits en Java, vous pouvez les utiliser facilement à partir d’une topologie C#.

Vous utiliserez majoritairement les composants suivants :

* __EventHubSpout__ : lit les données à partir d’Event Hubs.
* __EventHubBolt__ : écrit des données dans Event Hubs.
* __EventHubSpoutConfig__ : permet de configurer EventHubSpout.
* __EventHubBoltConfig__ : permet de configurer EventHubBolt.
* __UnicodeEventDataScheme__ : permet de configurer le Spout afin d’utiliser l’encodage UTF-8 lors de la lecture à partir d’Event Hubs. Si vous ne l’utilisez pas, le Spout définit l’encodage par défaut sur String.

### <a name="example-spout-usage"></a>Exemple d’utilisation du Spout

SCP.NET fournit des méthodes spécifiquement pour l’ajout d’un EventHubSpout à votre topologie. Il est plus facile d’utiliser ces méthodes pour ajouter un Spout que d’utiliser les méthodes génériques pour ajouter un composant Java. L’exemple suivant illustre la création d’un Spout en utilisant les méthodes __SetEventHubSpout__ et EventHubSpoutConfig de SCP.NET :

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

L’exemple précédent crée un composant Spout nommé __EventHubSpout__ et le configure pour communiquer avec un Event Hub. Notez que l’indicateur de parallélisme du composant est défini sur le nombre de partitions dans l’Event Hub. Cela permet à Storm de créer une instance du composant pour chaque partition.

> [!WARNING]
> À partir du 1er janvier 2017, l’utilisation des méthodes SetEventHubSpout et EventHubSpoutConfig génère un Spout qui utilise l’encodage String lors de la lecture des données à partir d’Event Hubs. Si vous devez utiliser l’encodage UTF-8, consultez l’exemple suivant.

Vous pouvez également utiliser la méthode JavaComponentConstructor générique lors de la création d’un Spout. L’exemple suivant illustre la création d’un Spout avec la méthode JavaComponentConstructor. Il illustre également la configuration du Spout pour qu’il lise des données en utilisant l’encodage UTF-8 au lieu de l’encodage String :

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme est uniquement disponible dans la version 9.5 des composants Event Hub, disponible sur [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

### <a name="example-bolt-usage"></a>Exemple d’utilisation du bolt

Vous devez utiliser la méthode JavaComponmentConstructor pour créer une instance du bolt. L’exemple suivant illustre la création et la configuration d’une instance d’EventHubBolt :

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> Cet exemple utilise une expression Clojure transmise sous forme de chaîne au lieu d’utiliser JavaComponentConstructor pour créer un EventHubBoltConfig distinct, comme dans l’exemple Spout. Les deux méthodes fonctionnent ; utilisez celle qui vous convient.

## <a name="download-the-completed-project"></a>Télécharger le projet complet

Vous pouvez télécharger une version complète du projet créé dans ce didacticiel à partir de GitHub : [eventhub-storm-hybride](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Toutefois, vous devrez tout de même fournir des paramètres de configuration en suivant les étapes de ce didacticiel.

### <a name="prerequisites"></a>Composants requis

* Un [cluster Apache Storm sur HDInsight version 3.5](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > L’exemple utilisé dans ce document requiert Storm sur HDInsight version 3.5. Des modifications ont été apportées aux noms de classes utilisés pour les principaux composants Storm entre les versions de clusters plus anciens et la version de Storm incluse avec HDInsight 3.5. Pour obtenir une version de cet exemple compatible avec les clusters plus anciens, consultez [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Un [hub d'événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* Le [kit de développement logiciel Azure .NET](http://azure.microsoft.com/downloads/)

* Les [outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* Java JDK 1.7 ou version ultérieure sur votre environnement de développement. Vous pouvez télécharger JDK sur [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * La variable d’environnement **JAVA_HOME** doit pointer vers le répertoire qui contient Java.
  * Le répertoire **%JAVA_HOME%/bin** doit se trouver dans le chemin d’accès

## <a name="download-the-event-hub-components"></a>Télécharger les composants Event Hub

Le spout et le bolt sont distribués dans un fichier d’archive Java unique (.jar) nommé **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, où #.# est la version du fichier.

Une version du fichier jar qui fonctionne avec Storm sur la version 3.5 de HDInsight est disponible sur [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Créez un répertoire nommé `eventhubspout` et enregistrez le fichier dans le répertoire.

## <a name="configure-event-hubs"></a>Configurer les hubs d’événement

Event Hubs est la source de données pour cet exemple. Utilisez les informations contenues dans la section **Créer un Event Hub** du document [Prise en main des Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Après avoir créé l’Event Hub, affichez le panneau de l’Event Hub dans le portail Azure et sélectionnez **Stratégies d’accès partagé**. Utilisez l’entrée **+ Ajouter** pour ajouter les stratégies suivantes :
   
   | Nom | Autorisations |
   | --- | --- |
   | writer |Envoyer |
   | reader |Écouter |
   
    ![stratégies](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Sélectionnez les stratégies **reader** et **writer**. Copiez et enregistrez la valeur **CLÉ PRIMAIRE** des deux stratégies, car elles seront utilisées ultérieurement.

## <a name="configure-the-eventhubwriter"></a>Configuration de l’EventHubWriter

1. Si vous n’avez pas encore installé la dernière version des outils HDInsight pour Visual Studio, consultez [Prise en main de HDInsight Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Téléchargez la solution depuis [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Ouvrez la solution et prenez le temps d’examiner le code du projet **EventHubWriter**.

3. Dans le projet **EventHubWriter**, ouvrez le fichier **App.config**. Utilisez les informations à partir de l’Event Hub que vous avez configuré précédemment pour renseigner la valeur pour les clés suivantes :
   
   | Clé | Valeur |
   | --- | --- |
   | EventHubPolicyName |writer (si vous avez utilisé un nom différent pour la stratégie avec l’autorisation *Send*, utilisez-le à la place.) |
   | EventHubPolicyKey |La clé de la stratégie writer |
   | eventHubNamespace |L’espace de noms qui contient votre Event Hub |
   | eventHubName |Nom de votre Event Hub |
   | EventHubPartitionCount |Nombre de partitions dans votre Event Hub |

4. Enregistrez et fermez le fichier **App.config**.

## <a name="configure-the-eventhubreader"></a>Configuration de l’EventHubReader

1. Ouvrez le projet **EventHubReader** et prenez quelques moments pour examiner le code.

2. Ouvrez **App.config** pour **EventHubWriter**. Utilisez les informations à partir de l’Event Hub que vous avez configuré précédemment pour renseigner la valeur pour les clés suivantes :
   
   | Clé | Valeur |
   | --- | --- |
   | EventHubPolicyName |reader (si vous avez utilisé un nom différent pour la stratégie avec l’autorisation *listen*, utilisez-le à la place.) |
   | EventHubPolicyKey |La clé de la stratégie reader |
   | eventHubNamespace |L’espace de noms qui contient votre Event Hub |
   | eventHubName |Nom de votre Event Hub |
   | EventHubPartitionCount |Nombre de partitions dans votre Event Hub |

3. Enregistrez et fermez le fichier **App.config**.

## <a name="deploy-the-topologies"></a>Déploiement des topologies

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubReader** et sélectionnez **Envoyer à Storm sur HDInsight**.
   
    ![envoyer à Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Dans l’écran **Envoyer la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d'accès aux fichiers Java**, sélectionnez **...**, puis choisissez le répertoire qui contient le fichier jar que vous avez téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.
   
    ![Image de boîte de dialogue de soumission](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Une fois la topologie envoyée, la **Visionneuse de topologies Storm** apparaît. Sélectionnez la topologie **EventHubReader** dans le volet gauche pour afficher les statistiques de la topologie. Rien ne doit se produire pour le moment car aucun événement n’a encore été écrit dans les hubs d’événements.
   
    ![affichage d’un exemple de stockage](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubWriter** et sélectionnez **Envoyer à Storm sur HDInsight**.

5. Dans l’écran **Envoyer la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d'accès aux fichiers Java**, sélectionnez **...**, puis choisissez le répertoire qui contient le fichier jar que vous avez téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.

6. Une fois la topologie envoyée, actualisez la liste des topologies dans la **Visionneuse de topologies Storm** pour vérifier que les deux topologies sont en cours d’exécution sur le cluster.

7. Dans la **visionneuse de topologies Storm**, sélectionnez la topologie **EventHubReader**.

8. Dans l’affichage Graph, double-cliquez sur le composant **LogBolt**. Cette opération ouvre la page **Résumé du composant** pour bolt.

9. Dans la section **Exécuteurs**, sélectionnez un des liens dans la colonne **Port**. Ceci affiche les informations enregistrées par le composant. Les informations enregistrées sont similaires à ce qui suit :
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Arrêt des topologies

Pour arrêter les topologies, sélectionnez chaque topologie dans la **Visionneuse de topologies Storm**, puis cliquez sur **Supprimer**.

![image d’arrêt d’une topologie](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment utiliser le spout et le bolt du hub d'événements Java à partir d'une topologie C# pour travailler avec des données dans le hub d'événements Azure. Pour en savoir plus sur la création de topologies C#, consultez les rubriques suivantes.

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guide de programmation SCP](hdinsight-storm-scp-programming-guide.md)
* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Jan17_HO3-->


