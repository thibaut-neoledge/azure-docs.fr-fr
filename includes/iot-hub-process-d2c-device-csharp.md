## Envoi de messages interactifs depuis un appareil simulé
Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] pour envoyer des messages cloud-à-appareil interactifs au IoT Hub.

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
   
        Task.Delay(10000).Wait();
      }
    }
    ```
   
    Cette méthode est très similaire à la méthode **SendDeviceToCloudMessagesAsync** dans le projet **SimulatedDevice**. Seules différences : à présent, vous définissez la propriété système **MessageId** et une propriété utilisateur appelée **messageType**. Le code assigne un identificateur global unique (GUID) à la propriété **MessageId**. Service Bus peut l’utiliser pour dédupliquer les messages qu’il reçoit. L’exemple utilise la propriété **messageType** pour distinguer les messages interactifs et les messages de point de données. L’application transmet ces informations dans les propriétés du message, plutôt que dans le corps du message, afin que le processeur d’événements n’ait pas besoin de désérialiser le message pour le router.
   
   > [!NOTE]
   > Il est important de créer le **MessageId** utilisé pour dédupliquer les messages interactifs dans le code de l’appareil. Les communications réseau intermittentes ou d’autres défaillances pourrait entraîner une transmission multiple du même message à partir de cet appareil. Vous pouvez également utiliser un ID de message sémantique, comme un hachage des champs de données de message pertinents, à la place d’un GUID.
   > 
   > 
2. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :
   
    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````
   
   > [!NOTE]
   > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre une stratégie de nouvelle tentative, par exemple une interruption exponentielle, comme indiqué dans l’article MSDN [Gestion des erreurs temporaires].
   > 
   > 

<!-- Links -->
[Gestion des erreurs temporaires]: https://msdn.microsoft.com/library/hh675232.aspx
[Prise en main d’IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0608_2016-->