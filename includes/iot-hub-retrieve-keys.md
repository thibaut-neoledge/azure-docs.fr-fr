## Récupérer des clés IoT Hub
Afficher les clés d'authentification pour le nouveau IoT Hub.

1. Ajoutez la méthode suivante au fichier Program.cs :
   
    ```
    static void ShowIoTHubKeys(ResourceManagementClient client, string token)
    {
   
    }
    ```
2. Ajoutez le code suivant à la méthode **ShowIoTHubKeys** pour imprimer les clés d'authentification vers la console :
   
    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    var httpsRepsonse = client.HttpClient.PostAsync(
        string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}/listKeys?api-version=2015-08-15-preview", 
        subscriptionId, rgName, iotHubName),
        null).Result;
   
    Console.WriteLine("Keys: {0}, 
        httpsRepsonse.Content.ReadAsStringAsync().Result);
    ```

<!---HONumber=AcomDC_1203_2015-->