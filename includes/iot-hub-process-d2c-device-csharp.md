## Envoi de messages interactifs de l’appareil simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] pour envoyer des messages interactifs Cloud vers appareil au IoT Hub.

1. Dans Visual Studio, dans le projet **SimulatedDevice**, ajoutez la méthode suivante à la classe **Program**.

    ```
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
    ```

    Cette méthode est très similaire à la méthode **SendDeviceToCloudMessagesAsync** dans le projet **SimulatedDevice**. Seules différences : à présent, vous définissez la propriété système **MessageId** et une propriété utilisateur appelée **messageType**. Le code affecte un identificateur global unique (GUID) à la propriété **MessageId**, que Service Bus peut utiliser pour dédupliquer les messages qu’il reçoit. L’exemple utilise la propriété **messageType** pour distinguer les messages interactifs et les messages de point de données. L’application transmet ces informations dans les propriétés du message, plutôt que dans le corps du message, afin que le processeur d’événements n’ait pas besoin de désérialiser le message pour le router.

    > [AZURE.NOTE]Il est important de créer la propriété **MessageId** utilisée pour dédupliquer les messages interactifs dans le code de l’appareil, car des communications réseau intermittentes ou d’autres défaillances pourraient entraîner plusieurs retransmissions du même message à partir de cet appareil. Vous pouvez également utiliser un ID de message sémantique, comme un hachage des champs pertinents de données de message, à la place d’un GUID.

2. Ajoutez la méthode suivante dans la méthode **Main** juste avant la ligne `Console.ReadLine()` :

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE]Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre une stratégie de nouvelle tentative, par exemple une interruption exponentielle, comme indiqué dans l’article MSDN [Gestion des erreurs temporaires].

<!-- Links -->
[Gestion des erreurs temporaires]: https://msdn.microsoft.com/fr-FR/library/hh675232.aspx
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0107_2016-->