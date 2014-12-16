<properties pageTitle="Prise en main des concentrateurs d'événements" metaKeywords="Azure Service Bus, Concentrateur d'événements, prise en main concentrateurs d'événements" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Prise en main des concentrateurs d'événements

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Les concentrateurs d'événements représentent un système d'ingestion à l'extensibilité élevée en mesure d'absorber des millions d'événements par seconde, ce qui permet à une application de traiter et d'analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des concentrateurs d'événements, vous pouvez transformer et stocker des données à l'aide de n'importe quel fournisseur d'analyses en temps réel ou d'un cluster de stockage.

Pour plus d'informations, consultez la page [Vue d'ensemble des concentrateurs d'événements].

Dans ce didacticiel, vous allez apprendre comment procéder à la réception des messages dans un concentrateur d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide de la bibliothèque C# d'[Event Processor Host].

Pour effectuer ce didacticiel, vous devrez disposer des éléments suivants :

+ Microsoft Visual Studio Express 2013 pour Windows

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## Création d'un concentrateur d'événements

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **NOUVEAU** en bas de l'écran.

2. Cliquez sur **App Services**, sur **Service Bus**, **Concentrateur d'événements**, puis **Création rapide**.

   	![][1]

3. Tapez le nom de votre concentrateur d'événements, sélectionnez la région de votre choix, puis cliquez sur **Créer un concentrateur d'événements**.

   	![][2]

4. Cliquez sur l'espace de noms que vous venez de créer (en règle générale, ***event hub name*-ns**).

   	![][3]

5. Cliquez sur l'onglet **Concentrateurs d'événements**, puis cliquez sur le concentrateur d'événements que vous venez de créer.

   	![][4]

6. Cliquez sur l'onglet **Configurer** en haut de la page, ajoutez une règle nommée **SendRule** avec les droits *envoi*, ajoutez une autre règle nommée **ReceiveRule** avec les droits *gestion, envoi, écoute*, puis cliquez sur **Enregistrer**.

   	![][5]

7. Cliquez sur l'onglet **Tableau de bord** en haut de la page, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

   	![][6]

Votre concentrateur d'événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Dans Visual Studio, exécutez le projet **Récepteur**, puis attendez qu'il démarre les récepteurs pour toutes les partitions.

   	![][21]

2.	Exécutez le projet **Expéditeur**, appuyez sur **Entrée** dans les fenêtres de console. Les événements s'affichent dans la fenêtre de réception.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d'ensemble des concentrateurs événements]: http://msdn.microsoft.com/fr-fr/library/azure/dn836025.aspx
