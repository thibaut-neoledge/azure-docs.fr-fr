---
title: Exemples de topologies Apache Storm sur HDInsight | Microsoft Docs
description: "Liste d’exemples de topologies Storm créées et testées avec Apache Storm sur HDInsight, y compris les topologies C# et Java de base et l’utilisation d’Event Hubs."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: b68b723aaff1f47dbd2d36503ce1a5db282ed9b4
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---
# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Exemples de topologie et de composant Storm pour Apache Storm sur HDInsight

Voici une liste d'exemples créés et gérés par Microsoft à utiliser avec Apache Storm dans HDInsight. Ces exemples couvrent une multitude de sujets, depuis la création de topologies de base en C# et Java jusqu’à l’utilisation de services Azure tels qu’Event Hubs, Cosmos DB, Power BI, SQL Database, HBase sur HDInsight et Azure Storage. Certains exemples montrent également comment utiliser des technologies non Azure, ou même non Microsoft, telles que SignalR et Socket.IO

| Description | Illustre le | Langage/structure |
|:--- |:--- |:--- |
| [Écrire dans Azure Data Lake Store à partir d’Apache Storm](hdinsight-storm-write-data-lake-store.md) |Écrire dans Azure Data Lake Store |Java |
| [Source de spout et bolt Event Hub](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Source de spout et bolt Event Hub |Java |
| [Développement de topologies basées sur Java pour Apache Storm dans HDInsight][5797064f] |Maven |Java |
| [Développement de topologies C# pour Apache Storm dans HDInsight à l’aide de Visual Studio][16fce2d1] |Outils HDInsight pour Visual Studio |C#, Java |
| [Création de plusieurs flux de données dans une topologie C# Storm][ec5a4064] |Plusieurs flux de données |C# |
| [Détermination de rubriques tendances Twitter avec Apache Storm dans HDInsight][3c86c7c8] |Trident |Java, Trident |
| [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (C#)][844d1d81] |Event Hubs |C# et Java |
| [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) |Event Hubs |Java |
| [Utilisation de Power BI pour visualiser les données d’une topologie Storm][94d15238] |Power BI |C# |
| [Analyse des données de capteur avec Storm et HBase dans HDInsight][ab894747] |Event Hubs, HBase, Socket.IO, tableau de bord web |C#, Java, JavaScript, HTML |
| [Traitement des données de capteur de véhicule à partir d’Event Hubs à l’aide de Storm dans HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Extraction, transformation et chargement (ETL) à partir d’Azure Event Hubs dans HBase, à l’aide de Storm sur HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Modèle de projet de topologie Storm C# pour l’utilisation des services Azure à partir de Storm sur HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, SignalR |C#, Java |
| [Tests d’extensibilité pour la lecture à partir d’Azure Event Hubs à l’aide de Storm sur HDInsight][d6c540e3] |Débit des messages, Event Hubs, SQL Database |C#, Java |
| [Corrélation des événements à l’aide de Storm et HBase sur HDInsight](hdinsight-storm-correlation-topology.md) |HBase |C# |
| [Utilisation de Python avec Storm sur HDInsight](hdinsight-storm-develop-python-topology.md) |Composants de Python avec une topologie Flux |Python |
| [Utilisation de Kafka avec Storm sur HDInsight](hdinsight-apache-storm-with-kafka.md) | Lecture et écriture Apache Storm dans Apache Kafka | Java |

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main d’Apache Storm sur HDInsight][2b8c3488]
* [Découvrez comment déployer et gérer des topologies Storm avec Storm sur HDInsight][6eb0d3b8]

[2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Découvrez comment créer un cluster Storm sur HDInsight et utiliser le tableau de bord Storm pour déployer des exemples de topologies."
[6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Découvrez comment déployer et gérer des topologies à l’aide du tableau de bord web de Storm et de l’IU Storm, ou des outils HDInsight pour Visual Studio."
[16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Découvrez comment créer une topologie Storm en C# à l’aide des outils HDInsight pour Visual Studio."
[5797064f]: hdinsight-storm-develop-java-topology.md "Découvrez comment créer des topologies Storm dans Java à l’aide de Maven en créant une topologie de comptage de mots de base."
[94d15238]: hdinsight-storm-power-bi-topology.md "Montre comment écrire des données dans Power BI à partir d’une topologie C#, puis créer un graphique et un tableau de bord à partir de ces données."
[ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Illustre une topologie Storm simple qui effectue un comptage de mots, implémentée en C#. Cet exemple montre également comment créer plusieurs flux de données dans une topologie C#."
[844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Découvrez comment lire et écrire des données à partir d’Azure Event Hubs avec Storm sur HDInsight."
[ab894747]: hdinsight-storm-sensor-data-analysis.md "Découvrez comment utiliser Apache Storm sur HDInsight pour traiter les données de capteur à partir d’Azure Event Hubs, les visualiser à l’aide de D3.js et (éventuellement) les stocker dans HBase."
[3c86c7c8]: hdinsight-storm-twitter-trending.md "Découvrez comment utiliser Trident pour créer une topologie Storm qui détermine les sujets tendances (à partir des hashtags), sur Twitter."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Découvrez comment utiliser une topologie Storm pour lire des messages à partir d’Azure Event Hubs, lire des documents à partir d’Azure Cosmos DB pour le référencement des données et enregistrer des données dans le stockage Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Plusieurs topologies pour illustrer le débit lors de la lecture à partir d’Azure Event Hubs et du stockage dans SQL Database à l’aide d’Apache Storm sur HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Découvrez comment lire des données à partir d’Azure Event Hubs, les agréger et les transformer, puis les stocker dans HBase sur HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ce projet contient des modèles de spouts, bolts et topologies permettant d’interagir avec divers services Azure tels que Event Hubs, Cosmos DB et SQL Database."


