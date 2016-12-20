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
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Traitement des événements Azure Event Hubs avec Storm sur HDInsight (C#)
Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hubs simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

Dans ce didacticiel, vous découvrirez comment utiliser les modèles Visual Studio installés avec les Outils HDInsight pour Visual Studio afin de créer deux topologies qui fonctionnent avec les hubs d’événements Azure.

* **EventHubWriter**: génère des données de façon aléatoire et les écrit dans les hubs d’événements
* **EventHubReader** : lit les données à partir d’Event Hubs et enregistre les données dans les journaux de Storm

> [!NOTE]
> Bien que les étapes décrites dans ce document reposent sur un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à un cluster HDInsight basé sur Linux ou sur Windows. Seuls les clusters basés sur Linux créés après le 28/10/2016 prennent en charge les topologies SCP.NET.
> 
> Pour utiliser une topologie C# avec un cluster basé sur Linux, vous devez mettre à jour le package NuGet de Microsoft.SCP.Net.SDK utilisé par votre projet vers la version 0.10.0.6 ou une version supérieure. La version du package doit également correspondre à la version principale de Storm installée sur HDInsight. Par exemple, Storm sur les versions 3.3 et 3.4 de HDInsight utilise Storm 0.10.x, tandis que HDInsight 3.5 utilise Storm 1.0.x.
> 
> Les topologies C# sur les clusters basés sur Linux doivent utiliser .NET 4.5, et utiliser Mono pour s’exécuter sur le cluster HDInsight. La plupart des éléments fonctionneront, vous devez toutefois consulter le document [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/) pour identifier les éventuelles incompatibilités.
> 
> Pour obtenir une version Java de ce projet, qui fonctionnera également avec un cluster basé sur Windows ou un cluster basé sur Linux, consultez [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
> 
> 

## <a name="prerequisites"></a>Conditions préalables
* Un [cluster Apache Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
* Un [hub d'événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* Le [kit de développement logiciel Azure .NET](http://azure.microsoft.com/downloads/)
* Les [outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>Projet terminé
Vous pouvez télécharger une version complète du projet créé dans ce didacticiel à partir de GitHub : [eventhub-storm-hybride](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Toutefois, vous devrez tout de même fournir des paramètres de configuration en suivant les étapes de ce didacticiel.

## <a name="event-hubs-spout-and-bolt"></a>Spout et bolt de hubs d’événements
Le spout et le bolt Event Hubs sont les composants Java qui vous permettent de travailler facilement avec Event Hubs à partir d’Apache Storm. Bien que ces composants soient écrits en Java, HDInsight Tools pour Visual Studio vous permet de créer des topologies hybrides qui combinent des composants Java et C#.

Le spout et le bolt sont distribués dans un fichier d’archive Java unique (.jar) nommé **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, où #.# est la version du fichier.

### <a name="download-the-jar-file"></a>Téléchargement du fichier .jar
La version la plus récente du fichier jar est fournie dans le projet [HDInsight Storm examples](https://github.com/hdinsight/hdinsight-storm-examples) dans le dossier **lib/eventhubs**. Pour télécharger le fichier, appliquez l'une des méthodes suivantes.

> [!NOTE]
> Le spout et le bolt ont été soumis pour inclusion dans le projet Apache Storm. Pour plus d’informations, consultez la rubrique [STORM-583: Initial check-in for storm-event hubs](https://github.com/apache/storm/pull/336/files) dans GitHub.
> 
> 

* **Téléchargement du fichier ZIP** : à partir du site [HDInsight Storm examples](https://github.com/hdinsight/hdinsight-storm-examples), sélectionnez le bouton **Download ZIP** dans le volet droit pour télécharger un fichier .zip contenant le projet.
  
    ![bouton de téléchargement zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    Une fois le fichier téléchargé, vous pouvez extraire l’archive et le fichier sera copié dans le répertoire **lib** .
* **Clonage du projet** : si [Git](http://git-scm.com/) est installé, exécutez la commande suivante pour cloner le dépôt localement, puis recherchez le fichier dans le répertoire **lib**.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

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
2. Dans l’écran **Envoyer la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d’accès aux fichiers Java**, sélectionnez **…**, puis sélectionnez le répertoire qui contient le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que vous avez téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.
   
    ![Image de boîte de dialogue de soumission](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. Une fois la topologie envoyée, la **Visionneuse de topologies Storm** apparaît. Sélectionnez la topologie **EventHubReader** dans le volet gauche pour afficher les statistiques de la topologie. Rien ne doit se produire pour le moment car aucun événement n’a encore été écrit dans les hubs d’événements.
   
    ![affichage d’un exemple de stockage](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **EventHubWriter** et sélectionnez **Envoyer à Storm sur HDInsight**.
5. Dans l’écran **Envoyer la topologie**, sélectionnez votre **Cluster Storm**. Développez **Configurations supplémentaires**, sélectionnez **Chemins d’accès aux fichiers Java**, puis **…**, puis sélectionnez le répertoire qui contient le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** téléchargé précédemment. Pour finir, cliquez sur **Envoyer**.
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

## <a name="notes"></a>Remarques
### <a name="checkpointing"></a>Points de contrôle
EventHubSpout contrôle régulièrement son état au nœud Zookeeper, qui enregistre l’offset actuel des messages lus depuis la file d’attente. Il permet au composant de commencer à recevoir des messages à l’offset enregistré dans les scénarios suivants :

* Échec et redémarrage de l’instance du composant.
* Agrandissement ou réduction du cluster en ajoutant ou supprimant des nœuds.
* Arrêt et redémarrage de la topologie **sous le même nom**.

Vous pouvez également exporter et importer les points de contrôle persistants vers le WASB (le stockage Azure utilisé par votre cluster HDInsight.) Les scripts qui permettent de le faire sont situés sur le cluster Storm sur HDInsight, dans **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

> [!NOTE]
> Le numéro de version dans le chemin d’accès peut être différent, car la version de Storm installée sur le cluster peut changer dans le futur.
> 
> 

Les scripts présents dans ce répertoire sont :

* **stormmeta_import.cmd** : pour importer toutes les métadonnées Storm du conteneur de stockage de clusters par défaut dans Zookeeper.
* **stormmeta_export.cmd** : pour exporter toutes les métadonnées Storm de Zookeeper vers le conteneur de stockage de clusters par défaut.
* **stormmeta_delete.cmd** : pour supprimer toutes les métadonnées Storm de Zookeeper.

L’exportation d’une importation vous permet de conserver les données du point de contrôle lorsque vous devez supprimer le cluster, mais que vous souhaitez reprendre le traitement à partir de l’offset actuel dans le hub lorsque vous remettez un nouveau cluster en ligne.

> [!NOTE]
> Dans la mesure où les données sont conservées dans le conteneur de stockage par défaut, le nouveau cluster **doit** utiliser le même compte de stockage et le même conteneur que le cluster précédent.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découvert comment utiliser le spout et le bolt du hub d'événements Java à partir d'une topologie C# pour travailler avec des données dans le hub d'événements Azure. Pour en savoir plus sur la création de topologies C#, consultez les rubriques suivantes.

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guide de programmation SCP](hdinsight-storm-scp-programming-guide.md)
* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


