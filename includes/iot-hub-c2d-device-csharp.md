## Réception de messages sur le périphérique simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans [Prise en main d’IoT Hub] pour recevoir des messages du cloud vers des appareils à partir du concentrateur IoT.

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

    La méthode `ReceiveAsync` renvoie de façon asynchrone le message reçu au moment où l’appareil le reçoit. Elle retourne *null* après un délai d’attente pouvant être précisé (dans ce cas, la valeur par défaut est 1 minute). Dans ce cas, nous voulons que le code continue d’attendre les nouveaux messages. C’est l’objectif de la ligne `if (receivedMessage == null) continue`.

    L’appel à `CompleteAsync()` signale à IoT Hub que le message a été correctement traité et qu’il peut être supprimé en toute sécurité de la file d’attente de l’appareil. Si un événement a empêché l’application de l’appareil de terminer le traitement du message, IoT Hub le relivre. Il est alors important que la logique de traitement du message dans l’application de l’appareil soit *idempotent*, afin que la réception du même message à plusieurs reprises produise le même résultat. Une application peut également temporairement `Abandon` un message, IoT Hub conserve alors le message dans la file d’attente à des fins de consommation future ; ou `Reject` un message, ce qui supprime définitivement le message de la file d’attente. Reportez-vous au [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D] pour plus d’informations sur le cycle de vie des messages du cloud vers des appareils.

> [AZURE.NOTE] Lorsque vous utilisez HTTP/1 comme moyen de transport au lieu d’AMQP, `ReceiveAsync` est immédiatement renvoyé. Le modèle de prise en charge des messages du cloud vers des appareils avec HTTP/1 est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (c’est-à-dire moins de toutes les 25 minutes). L’émission d’un nombre plus élevé de réceptions HTTP/1 conduit IoT Hub à limiter les demandes. Reportez-vous au [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D] pour plus d’informations sur les différences entre la prise en charge AMQP et HTTP/1 et la limitation d’IoT Hub.

2. Ajoutez la méthode suivante dans la méthode **Main** juste avant la ligne `Console.ReadLine()` :

        ReceiveC2dAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, il est recommandé d’implémenter des stratégies de nouvelle tentative (par exemple, d’interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires].

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=AcomDC_0309_2016-->