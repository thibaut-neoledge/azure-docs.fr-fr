<properties
	pageTitle="Envoi de messages cloud-à-appareil avec IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour découvrir comment envoyer des messages cloud-à-appareil à l’aide d’Azure IoT Hub avec C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# Didacticiel : envoi de messages cloud-à-appareil avec IoT Hub et .Net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introduction

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Le didacticiel [Prise en main d’IoT Hub] explique comment créer un concentrateur IoT, l’utiliser pour configurer une identité d’appareil et coder une simulation d’appareil qui envoie des messages d’appareils à cloud.

Ce didacticiel s’appuie sur l’article [Prise en main d’Azure IoT Hub]. Il explique comment envoyer des messages cloud-à-appareil à un seul appareil, demander un accusé de réception (*commentaire*) d’IoT Hub et le recevoir à partir du serveur principal basé sur le cloud de votre application.

Vous trouverez des informations supplémentaires sur les messages du cloud vers les appareils dans le [Guide du développeur d’IoT Hub][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous exécuterez deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans [Prise en main d’IoT Hub] qui se connecte à votre hub IoT et reçoit les messages entre cloud et appareils.
* **SendCloudToDevice**, qui envoie un message cloud-à-appareil à l’appareil simulé par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [AZURE.NOTE] IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit d’Azure][lnk-free-trial].

## Réception de messages sur le périphérique simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans [Prise en main d’Azure IoT Hub] pour recevoir des messages cloud-à-appareil à partir du concentrateur IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice**, ajoutez la méthode suivante à la classe **Program**.

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    La méthode `ReceiveAsync` renvoie de façon asynchrone le message reçu au moment où l’appareil le reçoit. Elle renvoie *null* après un délai d’attente pouvant être précisé (dans ce cas, la valeur par défaut est une minute). Dans ce cas, le code doit continuer d’attendre de nouveaux messages. C’est l’objectif de la ligne `if (receivedMessage == null) continue`.

    L’appel à `CompleteAsync()` notifie IoT Hub que le message a été traité avec succès. Le message peut être supprimé en toute sécurité de la file d’attente d’appareils. Si l’application de périphérique n’a pas été en mesure de terminer le traitement du message, IoT Hub le remet à nouveau. Il est alors important que la logique de traitement du message de l’application d’appareil soit *idempotente*, afin qu’un message identique reçu plusieurs fois produise le même résultat. Une application peut également abandonner temporairement un message. IoT hub en conserve alors le message dans la file d’attente pour un traitement ultérieur. Une application peut également rejeter un message, ce qui le supprime définitivement de la file d’attente. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, consultez le [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Lorsque vous utilisez HTTP/1 comme moyen de transport au lieu d’AMQP, la méthode `ReceiveAsync` est immédiatement renvoyée. Le modèle de prise en charge des messages cloud-à-appareil avec HTTP/1 est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (moins de toutes les 25 minutes). L’émission d’un nombre plus élevé de réceptions HTTP/1 conduit IoT Hub à limiter les demandes. Pour plus d’informations sur les différences entre la prise en charge d’AMQP et de HTTP/1 et la limitation d’IoT Hub, consultez le [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D].

2. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

        ReceiveC2dAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] (Gestion des erreurs temporaires).

## Envoi d’un message cloud-à-appareil à partir du serveur principal de l’application

Dans cette section, vous allez écrire une application console Windows qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé.

1. Dans la solution Visual Studio actuelle, créez un projet d’application de bureau Visual C# à l’aide du modèle de projet **d’application de console**. Nommez le projet **SendCloudToDevice**.

    ![Nouveau projet dans Visual Studio][20]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**.

	La fenêtre **Gérer les packages NuGet** s’ouvre.

3. Recherchez `Microsoft Azure Devices`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

	Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure IoT - Service SDK NuGet].

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices;

5. Ajoutez les champs suivants à la classe **Program**. Remplacez la valeur d’espace réservé par la chaîne de connexion du concentrateur IoT de la section [Prise en main d’Azure IoT Hub] \:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Cette méthode envoie un nouveau message cloud-à-appareil à l’appareil avec l’ID `myFirstDevice`. Modifiez ce paramètre en conséquence, au cas où vous avez modifié celui utilisé dans [Prise en main d’Azure IoT Hub].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage...**. Sélectionnez **Plusieurs projets de démarrage**, puis l’action **Démarrer** pour les applications **ProcessDeviceToCloudMessages**, **SimulatedDevice** et **SendCloudToDevice**.

9.  Appuyez sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application simulée.

    ![Application recevant le message][21]

## Réception des commentaires de remise
Il est possible de demander des accusés de réception (ou d’expiration) à IoT Hub pour chaque message cloud-à-appareil. Cela permet au serveur principal cloud d’instruire facilement une nouvelle tentative ou une logique de compensation. Consultez le [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D] pour plus d’informations sur les commentaires de messages cloud-à-appareil.

Dans cette section, vous allez modifier l’application **SendCloudToDevice** de manière à exiger des commentaires et à en recevoir d’IoT Hub.

1. Dans Visual Studio, dans le projet **SendCloudToDevice**, ajoutez la méthode suivante à la classe **Program**.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

2. Ajoutez la méthode suivante dans la méthode **Main** juste après la ligne `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` :

        ReceiveFeedbackAsync();

3. Pour obtenir des commentaires sur la remise de votre message cloud-à-appareil, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync**. Ajoutez la ligne suivante, immédiatement après la ligne `var commandMessage = new Message(...);` :

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Exécutez les applications en appuyant sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application simulée et, après quelques secondes, le message de commentaires reçu par votre application **SendCloudToDevice**.

    ![Application recevant le message][22]

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] (Gestion des erreurs temporaires).

## Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil. Vous pouvez continuer à explorer les scénarios et les fonctionnalités d’IoT Hub avec les didacticiels suivants :

- [Traiter les messages des appareils vers le cloud] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.
- [Téléchargement de fichiers à partir d’appareils] décrit un modèle qui utilise les messages cloud vers appareil pour faciliter les téléchargements de fichiers à partir d’appareils.

Informations supplémentaires sur IoT Hub :

* [Vue d’ensemble d’IoT Hub]
* [Guide du développeur d’IoT Hub]
* [Conseils pour IoT Hub]
* [Langages et plateformes d’appareils pris en charge]
* [Centre de développement Azure IoT]

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[package Azure IoT - Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Traiter les messages des appareils vers le cloud]: iot-hub-csharp-csharp-process-d2c.md
[Téléchargement de fichiers à partir d’appareils]: iot-hub-csharp-csharp-file-upload.md

[Vue d’ensemble d’IoT Hub]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[Langages et plateformes d’appareils pris en charge]: iot-hub-supported-devices.md
[Prise en main d’Azure IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0629_2016-->