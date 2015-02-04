<properties pageTitle="Prise en main des concentrateurs d'événements" metaKeywords="Azure Service Bus, Concentrateur d'événements, prise en main concentrateurs d'événements" description="Suivez ce didacticiel pour commencer à utiliser les concentrateurs d'événements Azure envoyant des événements avec C et les recevant dans un cluster Storm Apache" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="c" ms.devlang="java" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Prise en main des concentrateurs d'événements

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Les concentrateurs d'événements représentent un système d'ingestion à l'extensibilité élevée en mesure d'absorber des millions d'événements par seconde, ce qui permet à une application de traiter et d'analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des concentrateurs d'événements, vous pouvez transformer et stocker des données à l'aide de n'importe quel fournisseur d'analyses en temps réel ou d'un cluster de stockage.

Pour plus d'informations, consultez la page [Vue d'ensemble des concentrateurs d'événements].

Dans ce didacticiel, vous allez apprendre comment procéder à la réception des messages dans un concentrateur d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide d'Apache Storm.

Pour effectuer ce didacticiel, vous devrez disposer des éléments suivants :

+ Un environnement de développement en C. Pour ce didacticiel, nous partirons du principe que la pile GCC est sur une [machine virtuelle Linux Azure](http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-tutorial/) dotée du système d'exploitation Ubuntu 14.04. Vous trouverez des instructions pour d'autres environnements en consultant les liens externes.

+ Un environnement de développement Java configuré pour exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous partirons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## Création d'un concentrateur d'événements

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **NOUVEAU** en bas de l'écran.

2. Cliquez sur **App Services**, sur **Service Bus**, **Event Hub**, puis **Création rapide**.

   	![][1]

3. Tapez le nom de votre concentrateur d'événements, sélectionnez la région de votre choix, puis cliquez sur **Créer un concentrateur d'événements**.

   	![][2]

4. Cliquez sur l'espace de noms que vous venez de créer (en règle générale, ***event hub name*-ns**).

   	![][3]

5. Cliquez sur l'onglet**Concentrateurs d'événements** en haut de la page, puis cliquez sur le concentrateur d'événements que vous venez de créer.

   	![][4]

6. Cliquez sur l'onglet**Configurer** en haut de la page, ajoutez une règle nommée **SendRule** avec les droits *gestion, envoi, écoute*, puis cliquez sur **ReceiveRule** avec les droits *gestion, envoi, écoute*, puis cliquez sur **Enregistrer**.

   	![][5]

7. Sur la même page, notez les clés générées pour **SendRule** et **ReceiveRule**.

   	![][6c]

Votre concentrateur d'événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Exécutez la classe **LogTopology** dans Eclipse, puis attendez qu'elle démarre les récepteurs pour toutes les partitions.

2.	Exécutez le programme **Expéditeur**. Les événements s'affichent dans la fenêtre de réception.

   	![][23]

> [AZURE.NOTE] Dans le cadre de ce didacticiel, utilisez exclusivement Storm en mode local destiné au développement. Reportez-vous à la [Vue d'ensemble HDInsight Storm] et à la documentation [Apache Storm] officielle pour plus d'informations sur les déploiements et les modèles Storm.

## Étapes suivantes
Les ressources suivantes sont disponibles pour les applications de développement intégrant des concentrateurs d'événements et Storm.

- [Le didacticiel Analyse des données de capteur avec Storm et HDInsight] est un didacticiel de scénario complet tirant parti des concentrateurs d'événements, de Storm et de HBase pour la réception des données du capteur dans un cluster Hadoop.
- [Le didacticiel Développement d'applications de traitement de données de diffusion avec SCP.NET et C# sur Storm dans HDInsight] décrit la marche à suivre pour écrire des pipelines Storm en C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[23]: ./media/service-bus-event-hubs-getstarted/receive-storm3.png

<!-- Links -->
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d'ensemble des concentrateurs d'événements]: http://msdn.microsoft.com/fr-fr/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Vue d'ensemble HDInsight Storm]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-storm-overview/
[Le didacticiel Analyse des données de capteur avec Storm et HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Le didacticiel Développement d'applications de traitement de données de diffusion avec SCP.NET et C# sur Storm dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
