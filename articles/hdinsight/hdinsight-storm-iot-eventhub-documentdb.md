<properties
 pageTitle="Traitement des données de capteur de véhicules à l’aide d’Apache Storm dans HDInsight | Microsoft Azure"
 description="Découvrez comment traiter des données de capteur de véhicules à partir d’Event Hubs à l’aide d’Apache Storm dans HDInsight. Ajoutez des données de modèle à partir de DocumentDB et stockez le résultat dans le stockage."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#Traitement des données de capteur de véhicules à partir d’Azure Event Hubs à l’aide d’Apache Storm dans HDInsight

Découvrez comment traiter des données de capteur de véhicule à partir d'Azure Event Hubs à l'aide d'Apache Storm dans HDInsight Cet exemple lit les données de capteur à partir d'Azure Event Hubs, enrichit les données en référençant les données stockées dans Azure DocumentDB et, enfin, stocke les données dans Azure Storage à l'aide du système de fichiers Hadoop (HDFS).

![Schéma sur l’architecture de HDInsight et de l’Internet des objets (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##Vue d'ensemble

L'ajout de capteurs aux véhicules vous permet d'anticiper les problèmes de matériel à partir des tendances des données historiques, et d'apporter des améliorations aux versions futures en fonction de l'analyse du modèle d'utilisation. Alors que le traditionnel traitement par lots MapReduce peut être utilisé pour cette analyse, vous devez pouvoir rapidement et efficacement charger les données de tous les véhicules dans Hadoop avant le traitement MapReduce. Par ailleurs, il peut être utile d'effectuer une analyse pour identifier les chemins d'erreur critique (température du moteur, freins, etc.) en temps réel.

Azure Event Hubs permet de gérer le volume considérable de données générées par les capteurs, et Apache Storm dans HDInsight peut servir à charger et traiter les données avant de les stocker dans HDFS (soutenu par stockage Azure) pour un traitement MapReduce supplémentaire.

##Solution

Les données de télémétrie pour la température du moteur, la température ambiante et la vitesse du véhicule sont enregistrées par des capteurs, puis envoyées aux hubs d'événements avec le numéro d'identification du véhicule (VIN) et un horodatage. À partir de là, une topologie Storm s'exécutant sur un cluster Apache Storm dans HDInsight lit les données, les traite et les stocke dans HDFS.

Pendant le traitement, le VIN est utilisé pour extraire des informations sur le modèle à partir d'Azure DocumentDB. Ces informations sont ajoutées au flux de données avant le stockage.

Les composants utilisés dans la topologie Storm sont les suivants :

* **EventHubSpout** : lit les données à partir d'Azure Event Hubs

* **TypeConversionBolt** : convertit la chaîne JSON à partir des hubs d'événements en un tuple contenant les valeurs de données individuelles de la température du moteur, la température ambiante, la vitesse, le VIN et l'horodatage

* **DataReferencBolt** : recherche dans DocumentDB le modèle du véhicule à l'aide du VIN

* **WasbStoreBolt** : stocke les données dans HDFS (Azure Storage)

Voici un diagramme de cette solution :

![topologie Storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Il s'agit d'un diagramme simplifié et chaque composant de la solution peut avoir plusieurs instances. Par exemple, les différentes instances de chaque composant de la topologie sont réparties entre les nœuds du cluster Storm dans HDInsight.

##Implémentation

Une solution automatisée complète pour ce scénario est disponible dans le référentiel [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) sur GitHub. Pour utiliser cet exemple, suivez les étapes de la page [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## Étapes suivantes

Pour plus d'exemples de topologies Storm, consultez les [exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0914_2016-->