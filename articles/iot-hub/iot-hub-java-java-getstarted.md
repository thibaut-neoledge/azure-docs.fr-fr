---
title: Prise en main avec Azure IoT Hub pour Java | Microsoft Docs
description: "Envoi de messages appareil-vers-cloud d’un appareil vers un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Java. Vous créez une application de périphérique simulé pour envoyer des messages, une application de service pour inscrire votre appareil dans le registre des identités et une application de service pour lire les messages appareil-vers-cloud à partir du IoT Hub."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b7dbfff716806e8b91488d3eb5eafab582e173ba
ms.lasthandoff: 03/15/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-java"></a>Connecter votre appareil simulé à votre IoT hub à l’aide de Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous disposerez de trois applications console Java :

* **create-device-identity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter application d’appareil simulé ;
* **read-d2c-messages**, qui affiche les données de télémétrie envoyées par votre application d’appareil simulé ;
* **simulated-device**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole MQTT.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Java pour ce didacticiel sur Windows ou Linux.
* Maven 3.  <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de [Maven][lnk-maven] pour ce didacticiel sur Windows ou Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Dans la dernière étape, prenez note de la valeur de la **Clé primaire**. Cliquez sur **Points de terminaison**, puis sur le point de terminaison intégré **Événements**. Dans le panneau **Propriétés**, notez le **Nom compatible avec le hub d’événements** et l’**adresse du point de terminaison compatible avec le hub d’événements**. Ces trois valeurs sont nécessaires pour créer votre application **read-d2c-messages**.

![Panneau de messagerie IoT Hub du portail Azure][6]

Votre IoT Hub est maintenant créé. Vous disposez du nom d’hôte IoT Hub, de la chaîne de connexion IoT Hub, de la clé primaire IoT Hub, du nom compatible avec le hub d’événements et du point de terminaison compatible avec le hub d’événements, dont vous avez besoin pour terminer ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité d’appareil
Dans cette section, vous allez créer une application console Java qui crée une identité d’appareil dans le registre d’identités de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Reportez-vous à la section **Registre d’identité** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application de console, elle génère un ID d’appareil et une clé uniques que votre appareil peut utiliser pour s’identifier pour lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Créez un dossier vide appelé iot-java-get-started. Dans le dossier iot-java-get-started, créez un projet Maven nommé **create-device-identity** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. À l’invite de commandes, accédez au dossier create-device-identity.
3. Dans un éditeur de texte, ouvrez le fichier pom.xml situé dans le dossier create-device-identity et ajoutez la dépendance suivante au nœud **dependencies** . Cette dépendance permet d’utiliser le package client du service IoT dans votre application :
   
    ```
    </dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.2.18</version>
    </dependency>
    ```
    
    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec la [recherche Maven][lnk-maven-service-search].

4. Enregistrez et fermez le fichier pom.xml.
5. À l’aide d’un éditeur de texte, ouvrez le fichier create-device-identity\src\main\java\com\mycompany\app\App.java.
6. Ajoutez les instructions **import** suivantes au fichier :
   
    ```
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Ajoutez les variables de niveau classe suivantes à la classe **App**, en remplaçant **{yourhubconnectionstring}** par la valeur que vous avez notée précédemment :
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Modifiez la signature de la méthode **main** pour inclure les exceptions suivantes :
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Ajoutez le code suivant comme corps de la méthode **main** . Si ce n’est déjà fait, ce code crée un appareil appelé *javadevice* dans votre registre d’identités IoT Hub. Il affiche ensuite l’ID et la clé de l’appareil dont vous aurez besoin par la suite :
   
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
    System.out.println("Device ID: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Enregistrez et fermez le fichier App.java.
11. Pour générer l’application **create-device-identity** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier create-device-identity :
    
    ```
    mvn clean package -DskipTests
    ```
12. Pour exécuter l’application **create-device-identity** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier create-device-identity :
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Prenez note de **l’ID de l’appareil** et de la **clé de l’appareil**. Vous aurez besoin de ces valeurs ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID et les clés des appareils à utiliser comme informations d’identification de sécurité, et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages appareil-à-cloud
Dans cette section, vous allez créer une application console Java qui lit les messages des appareils vers le cloud dans l’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec les [hubs d’événements][lnk-event-hubs-overview] pour vous permettre de lire les messages des appareils vers le cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [!NOTE]
> Le point de terminaison compatible Event Hub pour lire des messages de l’appareil vers le cloud utilise toujours le protocole AMQP.
> 
> 

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **read-d2c-messages** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. À l’invite de commandes, accédez au dossier read-d2c-messages.
3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier read-d2c-messages et ajoutez la dépendance suivante au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package eventhubs-client dans votre application pour effectuer la lecture à partir du point de terminaison compatible Event Hub :
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.13.0</version> 
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **azure-eventhubs** avec la [recherche Maven][lnk-maven-eventhubs-search].

4. Enregistrez et fermez le fichier pom.xml.
5. À l’aide d’un éditeur de texte, ouvrez le fichier read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Ajoutez les instructions **import** suivantes au fichier :
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```
7. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez les chaînes **{youriothubkey}**, **{youreventhubcompatibleendpoint}** et **{youreventhubcompatiblename}** par les valeurs que vous avez notées précédemment :
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Ajoutez la méthode **receiveMessages** suivante à la classe **App**. Cette méthode crée une instance **EventHubClient** permettant de vous connecter au point de terminaison compatible Event Hub, puis crée une instance **PartitionReceiver** de façon asynchrone pour lire les données à partir d’une partition Event Hub. La méthode boucle en continu et imprime les détails du message jusqu’à ce que l’application s’arrête.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Cette méthode utilise un filtre lorsqu’elle crée le récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Dans un environnement de test, cette technique permet de voir l’ensemble des messages en cours. Dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, consultez le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial].
   > 
   > 
9. Modifiez la signature de la méthode **main** en y incluant l’exception suivante :
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Ajoutez le code suivant à la méthode **main** dans la classe **App**. Ce code crée les deux instances **EventHubClient** et **PartitionReceiver** et vous permet de fermer l’application lorsque vous avez terminé le traitement des messages :
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > Ce code repose sur l’hypothèse selon laquelle vous avez créé votre IoT Hub dans le niveau F1 (gratuit). Un IoT Hub gratuit comporte deux partitions nommées « 0 » et « 1 ».
    > 
    > 
11. Enregistrez et fermez le fichier App.java.
12. Pour générer l’application **read-d2c-messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier read-d2c-messages :
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez créer une application console Java qui simule un appareil envoyant des messages des appareils vers le cloud à un IoT Hub.

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **simulated-device** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. À l’invite de commandes, accédez au dossier simulated-device.
3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulated-device et ajoutez les dépendances suivantes au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package iothub-java-client dans votre application pour communiquer avec votre IoT Hub et pour sérialiser les objets Java dans JSON :
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.1.24</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-device-client** avec la [recherche Maven][lnk-maven-device-search].

4. Enregistrez et fermez le fichier pom.xml.
5. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.
6. Ajoutez les instructions **import** suivantes au fichier :
   
    ```
    import com.microsoft.azure.sdk.iot.device.DeviceClient;
    import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;
    import com.microsoft.azure.sdk.iot.device.Message;
    import com.microsoft.azure.sdk.iot.device.IotHubStatusCode;
    import com.microsoft.azure.sdk.iot.device.IotHubEventCallback;
    import com.microsoft.azure.sdk.iot.device.MessageCallback;
    import com.microsoft.azure.sdk.iot.device.IotHubMessageResult;
    import com.google.gson.Gson;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez la chaîne **{youriothubname}** par le nom de votre IoT Hub, et **{yourdevicekey}** par la valeur clé d’appareil que vous avez générée dans la section *Création d’une identité d’appareil* :
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Cet exemple d’application utilise la variable **protocol** lorsqu’il instancie un objet **DeviceClient**. Vous pouvez utiliser le protocole MQTT, AMQP ou HTTP pour communiquer avec le IoT Hub.
8. Ajoutez la classe imbriquée **TelemetryDataPoint** suivante dans la classe **App** pour spécifier les données de télémétrie envoyées par votre appareil à votre IoT Hub :
   
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
9. Ajoutez la classe imbriquée **EventCallback** suivante dans la classe **App** pour afficher l’état d’accusé de réception que IoT Hub renvoie lorsqu’il traite un message provenant de l’appareil simulé. Cette méthode avertit également le thread principal de l’application lorsque le message a été traité :
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. Ajoutez la classe imbriquée **MessageSender** suivante dans la classe **App**. La méthode **run** de cette classe génère un échantillon de données de télémétrie à envoyer à votre IoT Hub et attend un accusé de réception avant d’envoyer le message suivant :
    
    ```
    private static class MessageSender implements Runnable {
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (true) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```
    
    Cette méthode envoie un nouveau message Appareil vers cloud une seconde après que l’IoT Hub a accusé réception du message précédent. Le message contient un objet JSON sérialisé avec l’ID de l’appareil et un nombre généré de manière aléatoire pour simuler un anémomètre.
11. Remplacez la méthode **main** par le code suivant qui crée un thread pour envoyer des messages des appareils vers le cloud à votre IoT Hub :
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Enregistrez et fermez le fichier App.java.
13. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier read-d2c dossier, exécutez la commande suivante pour commencer à analyser la première partition de votre IoT Hub :
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Application Java du service IoT Hub pour surveiller les messages appareil-à-cloud][7]
2. À l’invite de commande dans le dossier simulated-device, exécutez la commande suivante pour commencer à envoyer les données de télémétrie à votre IoT Hub :
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Application Java de l’appareil IoT Hub pour envoyer les messages appareil-à-cloud][8]
3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au IoT Hub :
   
    ![Vignette Utilisation du portail Azure indiquant le nombre de messages envoyés à IoT Hub][43]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’appareil simulé d’envoyer des messages Appareil vers cloud à l’IoT Hub. Vous avez également créé une application qui affiche les messages reçus par l’IoT Hub. 

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Connexion de votre appareil][lnk-connect-device]
* [Prise en main de la gestion d’appareils][lnk-device-management]
* [Prise en main du Kit de développement logiciel (SDK) IoT Gateway][lnk-gateway-SDK]

Pour découvrir comment étendre votre solution IoT et traiter les messages appareil-à-cloud à grande échelle, consultez le didacticiel [Traitement des messages appareil-à-cloud][lnk-process-d2c-tutorial].

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
