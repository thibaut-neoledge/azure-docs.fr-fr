---
title: "Traitement des événements de concentrateurs d’événements avec Storm - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment traiter les données de concentrateurs d’événements Azure avec une topologie Storm C# créée dans Visual Studio à l’aide des outils HDInsight pour Visual Studio."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/22/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 178d7a53ec1f05d79a077a016ef869df2bb014b1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Traitement des événements Azure Event Hubs avec Storm sur HDInsight (C#)

Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hubs simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

Dans ce didacticiel, vous découvrez comment utiliser les modèles Visual Studio installés avec les outils HDInsight pour Visual Studio, afin de créer deux topologies qui fonctionnent avec les concentrateurs d’événements.

* **EventHubWriter** : génère des données de façon aléatoire et les écrit dans les concentrateurs d’événements.
* **EventHubReader** : lit des données à partir des concentrateurs d’événements et enregistre les données dans les journaux de Storm.

> [!NOTE]
> Pour obtenir une version Java de ce projet, consultez [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Ces projets utilisent SCP.NET, un package NuGet qui vous permet de créer des topologies et des composants C# pour les utiliser avec Storm sur HDInsight.

> [!IMPORTANT]
> Bien que les étapes décrites dans ce document reposent sur un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à un cluster Storm sur HDInsight utilisant Linux. Seuls les clusters basés sur Linux créés après le 28 octobre 2016 prennent en charge les topologies SCP.NET.

### <a name="cluster-versioning"></a>Contrôle de version de cluster

Le package NuGet Microsoft.SCP.Net.SDK que vous utilisez pour votre projet doit correspondre à la version principale de Storm installée sur HDInsight. Les versions 3.3 et 3.4 de Storm sur HDInsight utilisent la version 0.10.x de Storm. Vous devez donc utiliser la version 0.10.x.x de SCP.NET avec ces clusters. Les versions 3.5 et 3.6 de HDInsight utilisent Storm 1.x. Vous devez donc utiliser la version 1.0.x.x de SCP.NET avec ces clusters.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

La version 3.4 de HDInsight (et les versions suivantes) utilisent Mono pour exécuter des topologies C#. Consultez le document [Compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) (Compatibilité) pour identifier les éventuelles incompatibilités.

Les topologies C# doivent également cibler la version 4.5 de .NET.

## <a name="how-to-work-with-event-hubs"></a>Utilisation d’Event Hubs

Microsoft fournit un ensemble de composants Java qui peut être utilisé pour communiquer avec les concentrateurs d’événements à partir d’une topologie Storm. Vous pouvez trouver le fichier d’archives Java (JAR) qui contient une version 3.3 et 3.4 compatible de HDInsight pour ces composants sur [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Pour HDInsight 3.5 ou version ultérieure, les composants sont également disponibles sur [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

> [!IMPORTANT]
> Même si les composants sont écrits en Java, vous pouvez les utiliser facilement à partir d’une topologie C#.

Les composants suivants sont utilisés dans cet exemple :

* __EventHubSpout__ : lit les données à partir d’Event Hubs.
* __EventHubBolt__ : écrit des données dans Event Hubs.
* __EventHubSpoutConfig__ : permet de configurer EventHubSpout.
* __EventHubBoltConfig__ : permet de configurer EventHubBolt.
* __UnicodeEventDataScheme__ : permet de configurer le spout afin d’utiliser l’encodage UTF-8 lors de la lecture à partir des concentrateurs d’évènements. L’encodage par défaut est string.

### <a name="example-spout-usage"></a>Exemple d’utilisation du spout

SCP.NET fournit des méthodes pour l’ajout d’un EventHubSpout à votre topologie. Il est plus facile d’utiliser ces méthodes pour ajouter un Spout que d’utiliser les méthodes génériques pour ajouter un composant Java. L’exemple suivant illustre la création d’un spout en utilisant les méthodes __SetEventHubSpout__ et **EventHubSpoutConfig** de SCP.NET :

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the event hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The event hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the event hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

L’exemple précédent crée un composant spout nommé __EventHubSpout__ et le configure pour communiquer avec un concentrateur d’événements. L’indicateur de parallélisme du composant est défini sur le nombre de partitions dans le concentrateur d’événements. Ce paramètre permet à Storm de créer une instance du composant pour chaque partition.

> [!IMPORTANT]
> À partir du 1er janvier 2017, l’utilisation des méthodes **SetEventHubSpout** et **EventHubSpoutConfig** génère un spout qui utilise l’encodage string lors de la lecture des données à partir des concentrateurs d’événements.

Vous pouvez également utiliser la méthode **JavaComponentConstructor** générique lors de la création d’un spout. L’exemple suivant illustre la création d’un spout avec la méthode **JavaComponentConstructor**. Il illustre également la configuration du spout pour qu’il lise des données qui utilisent un encodage UTF-8 au lieu d’un encodage string.

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
        // The namespace that contains the event hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The event hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the event hub
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
> **UnicodeEventDataScheme** est disponible uniquement dans la version 9.5 des composants de concentrateurs d’événements, que vous pouvez trouver dans [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

### <a name="example-bolt-usage"></a>Exemple d’utilisation du bolt

Utilisez la méthode **JavaComponentConstructor** pour créer une instance du bolt. L’exemple suivant illustre la création et la configuration d’une instance de **l’EventHubBolt** :

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
    // The namespace that contains the event hub
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
> Cet exemple utilise une expression Clojure transmise sous forme de chaîne au lieu d’utiliser **JavaComponentConstructor** pour créer un **EventHubBoltConfig**, comme dans l’exemple de spout. Les deux méthodes fonctionnent. Utilisez celle qui vous convient.

## <a name="download-the-completed-project"></a>Télécharger le projet complet

Vous pouvez télécharger une version complète du projet créé dans ce didacticiel à partir de [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Toutefois, vous devrez tout de même fournir des paramètres de configuration en suivant les étapes de ce didacticiel.

### <a name="prerequisites"></a>Composants requis

* Un [cluster Apache Storm sur HDInsight version 3.5](hdinsight-apache-storm-tutorial-get-started.md).

    > [!WARNING]
    > L’exemple utilisé dans ce document requiert Storm sur HDInsight version 3.5. Cela ne fonctionne pas avec les versions antérieures de HDInsight en raison des modifications apportées aux noms de classes. Pour une version de cet exemple fonctionnant avec les anciens clusters, consultez [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Un [concentrateur d’événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Le [kit de développement logiciel (SDK) Azure .NET](http://azure.microsoft.com/downloads/).

* Les [outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.7 ou version ultérieure sur votre environnement de développement. Les téléchargements JDK sont disponibles dans [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * La variable d’environnement **JAVA_HOME** doit pointer vers le répertoire qui contient Java.
  * Le répertoire **%JAVA_HOME%/bin** doit se trouver dans le chemin d’accès.

## <a name="download-the-event-hubs-components"></a>Télécharger les composants des concentrateurs d’événements

Le spout et le bolt sont distribués dans un fichier JAR unique nommé **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, où #.# est la version du fichier.

Pour utiliser cette solution avec HDInsight 3.3 ou 3.4, utilisez le fichier JAR version 0.9.5 dans [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Pour HDInsight 3.5 ou 3.6, utilisez ce fichier JAR dans [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Créez un répertoire nommé `eventhubspout` et enregistrez le fichier dans le répertoire.

## <a name="configure-event-hubs"></a>Configurer les hubs d’événement

Event Hubs est la source de données pour cet exemple. Utilisez les informations contenues dans la section « Création d’un espace de noms de concentrateurs d’événements et d’un concentrateur d’événements » du document [Bien démarrer avec l’envoi de messages vers des concentrateurs d’événements Azure dans .NET Standard](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Après avoir créé le concentrateur d’événements, affichez le panneau **EventHub** dans le portail Azure et sélectionnez **Stratégies d’accès partagé**. Sélectionnez **+ Ajouter** pour ajouter les stratégies suivantes :

   | Nom | Autorisations |
   | --- | --- |
   | writer |Envoyer |
   | reader |Écouter |

    ![Capture d’écran de la fenêtre de stratégies d’accès partagé](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Sélectionnez les stratégies **reader** et **writer**. Copiez et enregistrez la valeur de clé primaire des deux stratégies, car elles sont utilisées ultérieurement.

## <a name="configure-the-eventhubwriter"></a>Configuration de l’EventHubWriter

1. Si vous n’avez pas encore installé la dernière version des outils HDInsight pour Visual Studio, consultez [Se connecter à Azure HDInsight et exécuter des requêtes Hive avec Data Lake Tools pour Visual Studio | Microsoft Docs](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Téléchargez la solution depuis [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Dans le projet **EventHubWriter**, ouvrez le fichier **App.config**. Utilisez les informations à partir du concentrateur d’événements que vous avez configuré précédemment pour renseigner la valeur des clés suivantes :

   | Clé | Valeur |
   | --- | --- |
   | EventHubPolicyName |writer (si vous avez utilisé un nom différent pour la stratégie avec l’autorisation *Send*, utilisez-le à la place.) |
   | EventHubPolicyKey |La clé de la stratégie writer. |
   | eventHubNamespace |L’espace de noms qui contient votre concentrateur d’événements. |
   | eventHubName |Le nom de votre concentrateur d’événements. |
   | EventHubPartitionCount |Le nombre de partitions dans votre concentrateur d’événements. |

4. Enregistrez et fermez le fichier **App.config**.

## <a name="configure-the-eventhubreader"></a>Configuration de l’EventHubReader

1. Ouvrez le projet **EventHubReader**.

2. Ouvrez le fichier **App.config** pour **EventHubReader**. Utilisez les informations à partir du concentrateur d’événements que vous avez configuré précédemment pour renseigner la valeur des clés suivantes :

   | Clé | Valeur |
   | --- | --- |
   | EventHubPolicyName |reader (si vous avez utilisé un nom différent pour la stratégie avec l’autorisation *listen*, utilisez-le à la place.) |
   | EventHubPolicyKey |La clé de la stratégie reader. |
   | eventHubNamespace |L’espace de noms qui contient votre concentrateur d’événements. |
   | eventHubName |Le nom de votre concentrateur d’événements. |
   | EventHubPartitionCount |Le nombre de partitions dans votre concentrateur d’événements. |

3. Enregistrez et fermez le fichier **App.config**.

## <a name="deploy-the-topologies"></a>Déploiement des topologies

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubReader** et sélectionnez **Envoyer à Storm sur HDInsight**.

    ![Capture d’écran de l’Explorateur de solutions, avec l’option Envoyer à Storm sur HDInsight mise en surbrillance](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Dans la boîte de dialogue **Envoyer la topologie**, sélectionnez votre **cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d’accès aux fichiers Java**, sélectionnez **...**, puis choisissez le répertoire qui contient le fichier JAR que vous avez téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.

    ![Capture d’écran de la boîte de dialogue Envoyer la topologie](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Une fois la topologie envoyée, la **Visionneuse de topologies Storm** apparaît. Sélectionnez la topologie **EventHubReader** dans le volet gauche pour afficher des informations sur la topologie.

    ![Capture d’écran de la visionneuse de topologies Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubWriter** et sélectionnez **Envoyer à Storm sur HDInsight**.

5. Dans la boîte de dialogue **Envoyer la topologie**, sélectionnez votre **cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d’accès aux fichiers Java**, sélectionnez **...**, puis choisissez le répertoire qui contient le fichier JAR que vous avez téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.

6. Une fois la topologie envoyée, actualisez la liste des topologies dans la **Visionneuse de topologies Storm** pour vérifier que les deux topologies sont en cours d’exécution sur le cluster.

7. Dans la **Visionneuse de topologies Storm**, sélectionnez la topologie **EventHubReader**.

8. Pour ouvrir le résumé du composant associé au bolt, double-cliquez sur le composant **LogBolt** dans le diagramme.

9. Dans la section **Exécuteurs**, sélectionnez un des liens dans la colonne **Port**. Cela affiche les informations enregistrées par le composant. Les informations enregistrées sont similaires au texte suivant :

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Arrêt des topologies

Pour arrêter les topologies, sélectionnez chaque topologie dans la **Visionneuse de topologies Storm**, puis cliquez sur **Supprimer**.

![Capture d’écran de la visionneuse de topologies Storm avec le bouton Supprimer mis en surbrillance](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment utiliser le spout et le bolt du concentrateur d’événements Java à partir d’une topologie C# pour utiliser des données dans le concentrateur d’événements Azure. Pour en savoir plus sur la création de topologies C#, consultez les documents suivants :

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guide de programmation SCP](hdinsight-storm-scp-programming-guide.md)
* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

