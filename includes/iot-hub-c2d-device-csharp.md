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

    La méthode `ReceiveAsync` renvoie de façon asynchrone le message reçu au moment où l’appareil le reçoit. Elle renvoie *null* après un délai d’attente pouvant être précisé (dans ce cas, la valeur par défaut est une minute). Dans ce cas, le code doit continuer d’attendre de nouveaux messages. C’est l’objectif de la ligne `if (receivedMessage == null) continue`.

    L’appel à `CompleteAsync()` notifie IoT Hub que le message a été traité avec succès. Le message peut être supprimé en toute sécurité de la file d’attente d’appareils. Si l’application de périphérique n’a pas été en mesure de terminer le traitement du message, IoT Hub le remet à nouveau. Il est alors important que la logique de traitement du message de l’application d’appareil soit *idempotente*, afin qu’un même message reçu plusieurs fois produise le même résultat. Une application peut également abandonner temporairement un message. IoT hub en conserve alors le message dans la file d’attente pour un traitement ultérieur. Une application peut également rejeter un message, ce qui le supprime définitivement de la file d’attente. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, reportez-vous au [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D].

> [AZURE.NOTE] Lorsque vous utilisez HTTP/1 comme moyen de transport au lieu d’AMQP, la méthode `ReceiveAsync` est immédiatement renvoyée. Le modèle de prise en charge des messages cloud-à-appareil avec HTTP/1 est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (moins de toutes les 25 minutes). L’émission d’un nombre plus élevé de réceptions HTTP/1 conduit IoT Hub à limiter les demandes. Pour plus d’informations sur les différences entre la prise en charge d’AMQP et de HTTP/1 et la limitation d’IoT Hub, reportez-vous au [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D] .

2. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

        ReceiveC2dAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN Transient Fault Handling (Gestion des erreurs temporaires).

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=AcomDC_0608_2016-->