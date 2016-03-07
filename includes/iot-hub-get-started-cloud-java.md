## Création d’une identité d’appareil

Dans cette section, vous allez créer une application console Java qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages Appareil vers cloud à IoT Hub.

1. Créez un dossier vide appelé iot-java-get-started. Dans le dossier iot-java-get-started, créez un projet Maven nommé **create-device-identity** en entrant la commande ci-après au niveau de votre invite de commandes. Notez qu’il s’agit d’une seule commande longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier create-device-identity.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier create-device-identity et ajoutez la dépendance ci-après au nœud **dependencies**. Cette opération vous permet d’utiliser le package iothub-service-sdk dans votre application :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    ```
    
4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Ajoutez les instructions **import** ci-après au fichier :

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe ci-après à la classe **App** en remplaçant les chaînes **{yourhubname}** et **{yourhubkey}** par les valeurs que vous avez notées précédemment :

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Modifiez la signature de la méthode **main** pour y inclure les exceptions ci-après :

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Ajoutez le code ci-après en tant que corps de la méthode **main**. Ce code crée un appareil appelé *javadevice* dans le registre d’identité de votre IoT Hub s’il n’existe pas encore. Il affiche ensuite l’ID et la clé de l’appareil dont vous aurez besoin par la suite :

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Enregistrez et fermez le fichier App.java.

11. Pour générer l’application **create-device-identity** à l’aide de Maven, exécutez la commande ci-après à l’invite de commandes dans le dossier create-device-identity :

    ```
    mvn clean package -DskipTests
    ```

12. Pour exécuter l’application **create-device-identity** à l’aide de Maven, exécutez la commande ci-après à l’invite de commandes dans le dossier create-device-identity :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prenez note de l’**ID de l’appareil** et de la **clé de l’appareil**. Vous en aurez besoin ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

> [AZURE.NOTE] Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité. Par ailleurs, un indicateur activé/désactivé vous permet de désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.

## Recevoir des messages des appareils vers le cloud

Dans cette section, vous allez créer une application console Java qui lit les messages Appareil vers cloud à partir de l’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traitement de messages de type « Appareil vers cloud »][lnk-processd2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des concentrateurs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **read-d2c-messages** en entrant la commande ci-après au niveau de votre invite de commandes. Notez qu’il s’agit d’une seule commande longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier read-d2c-messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier read-d2c-messages et ajoutez la dépendance ci-après au nœud **dependencies**. Cette opération vous permet d’utiliser le package eventhubs-client dans votre application pour effectuer la lecture à partir du point de terminaison compatible Event Hubs :

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Ajoutez les instructions **import** ci-après au fichier :

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Ajoutez les variables de niveau classe ci-après à la classe **App** :

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Imbriquez la classe ci-après dans la classe **App**. L’application crée deux threads pour exécuter l’objet **MessageReceiver** de façon à lire les messages à partir des deux partitions du concentrateur d’événements :

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Ajoutez le constructeur ci-après à la classe **MessageReceiver** :

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Ajoutez la méthode **run** ci-après à la classe **MessageReceiver**. Cette méthode crée une instance **EventHubReceiver** pour effectuer la lecture à partir d’une partition du concentrateur d’événements. Elle effectue une boucle continue et affiche les détails des messages sur la console jusqu’à ce que l’élément **stopThread** présente la valeur true.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Cette méthode utilise un filtre lorsqu’elle crée le récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Cette opération est utile dans un environnement de test, car elle vous permet de voir l’ensemble actuel de messages, mais dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, voir le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-processd2c-tutorial].

11. Modifiez la signature de la méthode **main** pour y inclure les exceptions ci-après :

    ```
    public static void main( String[] args ) throws IOException
    ```

12. Ajoutez le code ci-après à la méthode **main** dans la classe **App**. Ce code crée une instance **EventHubClient** pour se connecter au point de terminaison compatible Event Hubs sur votre IoT Hub. Il crée ensuite deux threads pour effectuer la lecture à partir des deux partitions. Remplacez les chaînes **{youriothubkey}**, **{youreventhubcompatiblenamespace}** et **{youreventhubcompatiblename}** par les valeurs que vous avez notées précédemment. La valeur de l’espace réservé **{youreventhubcompatiblenamespace}** provient du **point de terminaison compatible avec Event Hub**. Elle prend la forme **xxxxnamespace** (en d’autres termes, supprimez le préfixe ****sb://** et le suffixe **.servicebus.windows.net** de la valeur de point de terminaison Event Hub à partir du portail).

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] Ce code repose sur l’hypothèse selon laquelle vous avez créé votre IoT Hub dans le niveau F1 (gratuit). Un IoT Hub gratuit comporte deux partitions nommées « 0 » et « 1 ». Si vous avez créé votre IoT Hub en utilisant l’un des autres niveaux tarifaires, vous devez ajuster le code pour créer un objet **MessageReceiver** pour chaque partition.

13. Enregistrez et fermez le fichier App.java.

14. Pour générer l’application **read-d2c-messages** à l’aide de Maven, exécutez la commande ci-après à l’invite de commandes dans le dossier read-d2c-messages :

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0224_2016-->