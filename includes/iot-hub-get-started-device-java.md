## Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application console Java qui simule un appareil envoyant des messages Appareil vers cloud à un IoT Hub.

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **simulated-device** en entrant la commande ci-après au niveau de votre invite de commandes. Notez qu’il s’agit d’une seule commande longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier simulated-device.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulated-device et ajoutez les dépendances ci-après au nœud **dependencies**. Cette opération vous permet d’utiliser le package iothub-java-client dans votre application pour communiquer avec votre IoT Hub et pour sérialiser les objets Java dans JSON :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Ajoutez les instructions **import** ci-après au fichier :

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Ajoutez les variables de niveau classe ci-après à la classe **App** en remplaçant la chaîne **{youriothubname}** par le nom de votre IoT Hub, et les chaînes **{yourdeviceid}** et **{yourdevicekey}** par les valeurs d’appareil que vous avez générées à la section *Création d’une identité d’appareil* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Cet exemple d’application utilise la variable **protocole** lorsqu’il instancie un objet **DeviceClient**. Vous pouvez utiliser le protocole HTTPS ou AMQPS pour communiquer avec le IoT Hub.

8. Imbriquez la classe **TelemetryDataPoint** ci-après dans la classe **App** pour spécifier les données de télémétrie que votre appareil envoie à votre IoT Hub :

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Imbriquez la classe **EventCallback** ci-après dans la classe **App** pour afficher l’état d’accusé de réception que l’IoT Hub renvoie lorsqu’il traite un message provenant de l’appareil simulé. Cette méthode avertit également le thread principal de l’application lorsque le message a été traité :

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Imbriquez la classe **MessageSender** ci-après dans la classe **App**. La méthode **run** de cette classe génère un échantillon de données de télémétrie à envoyer à votre IoT Hub et attend un accusé de réception avant d’envoyer le message suivant :

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Cette méthode envoie un nouveau message Appareil vers cloud une seconde après que l’IoT Hub a accusé réception du message précédent. Le message contient un objet JSON sérialisé avec l’ID de l’appareil et un nombre généré de manière aléatoire pour simuler un anémomètre.

11. Remplacez la méthode **main** par le code ci-après qui crée un thread pour l’envoi de messages Appareil vers cloud à votre IoT Hub :

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Enregistrez et fermez le fichier App.java.

13. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande ci-après au niveau de l’invite de commandes dans le dossier simulated-device :

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/fr-FR/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0204_2016-->