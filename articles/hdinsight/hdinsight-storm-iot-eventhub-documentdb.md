---
title: "Traitement des données de capteur de véhicules à l’aide d’Apache Storm dans HDInsight | Microsoft Docs"
description: "Découvrez comment traiter des données de capteur de véhicules à partir d’Event Hubs à l’aide d’Apache Storm dans HDInsight. Ajoutez des données de modèle à partir d’Azure Cosmos DB et stockez le résultat dans le stockage."
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Traitement des données de capteur de véhicules à partir d’Azure Event Hubs à l’aide d’Apache Storm dans HDInsight

Découvrez comment traiter des données de capteur de véhicule à partir d'Azure Event Hubs à l'aide d'Apache Storm dans HDInsight Cet exemple lit les données de capteur à partir d’Azure Event Hubs, enrichit ces données en faisant référence à des données stockées dans Azure Cosmos DB. Les données sont stockées dans le stockage Azure à l’aide du système de fichiers Hadoop (HDFS).

![Schéma sur l’architecture de HDInsight et de l’Internet des objets (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>Vue d'ensemble

L’ajout de capteurs aux véhicules vous permet de prédire les problèmes d’équipement en fonction de l’évolution des données historiques. Cela permet également d’améliorer les versions futures en fonction de l’analyse du modèle d’utilisation. Vous devez pouvoir charger rapidement et efficacement les données de tous les véhicules dans Hadoop avant le traitement MapReduce. Par ailleurs, il peut être utile d'effectuer une analyse pour identifier les chemins d'erreur critique (température du moteur, freins, etc.) en temps réel.

Azure Event Hubs est conçu pour gérer le volume considérable de données générées par les capteurs. Apache Storm permet de charger et traiter les données avant leur stockage dans HDFS.

## <a name="solution"></a>Solution

Les données de télémétrie concernant la température du moteur, la température ambiante et la vitesse du véhicule sont enregistrées par des capteurs. Elles sont ensuite envoyées à Event Hubs avec le numéro d’identification du véhicule (VIN) et un horodatage. À partir de là, une topologie Storm s'exécutant sur un cluster Apache Storm dans HDInsight lit les données, les traite et les stocke dans HDFS.

Pendant le traitement, le VIN est utilisé pour récupérer les informations du modèle à partir d’Azure Cosmos DB. Ces informations sont ajoutées au flux de données avant le stockage.

Les composants utilisés dans la topologie Storm sont les suivants :

* **EventHubSpout** : lit les données à partir d'Azure Event Hubs
* **TypeConversionBolt** : convertit la chaîne JSON provenant d’Event Hubs en un tuple qui contient les données de capteur suivantes :
    * Engine temperature
    * Température ambiante
    * Vitesse
    * VIN
    * Timestamp
* **DataReferencBolt** : verrouille le modèle de véhicule issu d’Azure Cosmos DB à l’aide du VIN.
* **WasbStoreBolt** : stocke les données dans HDFS (Azure Storage)

Voici un diagramme de cette solution :

![topologie Storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>Implémentation

Une solution automatisée complète pour ce scénario est disponible dans le référentiel [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) sur GitHub. Pour utiliser cet exemple, suivez les étapes de la page [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'exemples de topologies Storm, consultez les [exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md).


