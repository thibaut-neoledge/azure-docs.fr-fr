---
title: Prise en main d’Event Hubs avec C et Apache Storm | Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en C et à en recevoir dans un cluster Apache Storm.
services: event-hubs
documentationcenter: ''
author: jtaubensee
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: java
ms.topic: article
ms.date: 08/16/2016
ms.author: jotaub;sethm

---
# <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction
Les hubs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d'informations, consultez [Vue d'ensemble des hubs d'événements].

Dans ce didacticiel, vous allez apprendre comment procéder à la réception des messages dans un hub d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide d'Apache Storm.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un environnement de développement en C. Pour ce didacticiel, nous partirons du principe que la pile GCC se trouve sur une [machine virtuelle Linux Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) dotée du système d’exploitation Ubuntu 14.04. Vous trouverez des instructions pour d’autres environnements en consultant les liens externes.
* Un environnement de développement Java configuré pour exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Exécutez la classe **LogTopology** dans Eclipse, puis attendez qu’elle démarre les récepteurs pour toutes les partitions.
2. Exécutez le programme **Expéditeur**. Les événements s’affichent dans la fenêtre de réception.
   
   ![][23]

> [!NOTE]
> Dans le cadre de ce didacticiel, utilisez exclusivement Storm en mode local destiné au développement. Pour plus d’informations sur les déploiements et les modèles Storm, voir la [Présentation de Storm HDInsight] et la documentation [Apache Storm] officielle.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Les ressources suivantes sont disponibles pour les applications de développement intégrant des hubs d’événements et Storm.

* [Analyse des données de capteur avec Storm et HDInsight][Analyse des données de capteur avec Storm et HDInsight] est un didacticiel présentant un scénario complet qui tire parti des concentrateurs d’événements, de Storm et de HBase pour la réception des données de capteur dans un cluster Hadoop.
* [Développement d’applications de traitement de données de diffusion avec SCP.NET et C# sur Storm et HDInsight][Développement d’applications de traitement de données de diffusion avec SCP.NET et C# sur Storm et HDInsight] décrit la marche à suivre pour écrire des pipelines Storm en C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Portail Azure Classic]: https://manage.windowsazure.com/
[hôte du processeur d’événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des hubs d’événements]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Présentation de Storm HDInsight]: ../hdinsight/hdinsight-storm-overview.md/
[Analyse des données de capteur avec Storm et HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Développement d’applications de traitement de données de diffusion avec SCP.NET et C# sur Storm et HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Oct16_HO2-->


