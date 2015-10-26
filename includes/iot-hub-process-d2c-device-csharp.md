## Envoi de messages interactifs de l’appareil simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé pour envoyer des messages du cloud vers des appareils au concentrateur IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice**, ajoutez la méthode suivante à la classe **Program**.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    Cette méthode est très similaire à la méthode `SendDeviceToCloudMessagesAsync()` qui a été créée dans [Prise en main d’IoT Hub], les seules différences étant que la propriété système `MessageId` et une propriété de l’utilisateur appelée `messageType` sont maintenant définies. La propriété `MessageId` est définie sur un identificateur global unique (guid), qui permet de dédupliquer les réceptions de messages. La propriété `messageType` est utilisée pour distinguer les messages interactifs et les messages de point de données. Ces informations sont transmises dans les propriétés du message, plutôt que dans le corps du message, afin que le processeur d’événements du serveur principal ne soit pas obligé de désérialiser le message entier simplement pour effectuer le routage.

> [AZURE.NOTE]Il est important que `MessageId`, utilisé pour dédupliquer les messages interactifs, soit créé dans l’appareil, car les communications réseau intermittentes (ou d’autres défaillances) pourraient alors entraîner plusieurs retransmissions du même message à partir de l’appareil. Un id de message sémantique (par exemple, un hachage des champs de données de message appropriés) peut également être utilisé, par opposition à un identificateur global unique.

2. Ajoutez la méthode suivante dans la méthode **Main** juste avant la ligne `Console.ReadLine()` :

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, il est recommandé d’implémenter des stratégies de nouvelle tentative (par exemple, d’interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires].

<!-- Links -->
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO3-->