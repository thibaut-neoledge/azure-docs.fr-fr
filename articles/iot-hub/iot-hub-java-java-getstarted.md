<properties
	pageTitle="Prise en main d’Azure IoT Hub pour Java | Microsoft Azure"
	description="Didacticiel de prise en main d’Azure IoT Hub avec Java. Utilisez Azure IoT Hub et Java avec les kits de développement logiciel (SDK) de Microsoft Azure IoT pour mettre en œuvre une solution Internet des objets."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/06/2016"
     ms.author="dobett"/>

# Prise en main d’Azure IoT Hub pour Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduction

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT (Internet des objets) et un serveur principal de solution. L’une des plus grandes difficultés des projets IoT consiste à connecter des appareils au serveur principal de la solution de manière fiable et sécurisée. Pour relever ce défi, IoT Hub :

- Offre une messagerie évolutive Appareil vers cloud et Cloud vers appareil fiable.
- Assure la sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès.
- Inclut des bibliothèques d’appareils pour les langages et les plateformes les plus courants.

Ce didacticiel vous explique les procédures suivantes :

- Utilisez le portail Azure pour créer un hub IoT.
- Créer une identité de l’appareil dans votre hub IoT.
- Créer un périphérique simulé qui envoie les données de télémétrie au serveur back-end de votre service cloud.

À la fin de ce didacticiel, vous disposerez de trois applications de console Java :

* **create-device-identity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre appareil simulé ;
* **read-d2c-messages**, qui affiche les données de télémétrie envoyées par votre périphérique simulé ;
* **simulated-device**, qui se connecte à votre IoT hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole AMQPS.

> [AZURE.NOTE] L’article [kit de développement logiciel IoT Hub][lnk-hub-sdks] fournit des informations sur les différents kits de développement logiciels que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

+ Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit comment installer Java pour ce didacticiel sur Windows ou Linux.

+ Maven 3. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sous Windows ou sous Linux.

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Pour terminer, cliquez sur **Paramètres** dans le panneau IoT Hub, puis sur **Messagerie** dans le panneau **Paramètres**. Sur le panneau **Messagerie**, notez le **Nom compatible Event Hub** et **Point de terminaison compatible Event Hub**. Ces valeurs sont nécessaires lorsque vous créez votre application **read-d2c-messages**.

![][6]

Maintenant, vous avez créé votre concentrateur IoT et vous disposez d’un nom d’hôte IoT Hub, de la chaîne de connexion IoT Hub, du nom compatible Event Hub et d’un point de terminaison compatible Event Hub pour suivre le reste de ce didacticiel.

## Création d’une identité d’appareil

Dans cette section, vous allez créer une application console Java qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Créez un dossier vide appelé iot-java-get-started. Dans le dossier iot-java-get-started, créez un projet Maven nommé **create-device-identity** en entrant la commande ci-après au niveau de votre invite de commandes. Notez qu’il s’agit d’une seule commande longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier create-device-identity.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier create-device-identity et ajoutez la dépendance ci-après au nœud **dependencies**. Cette opération vous permet d’utiliser le package iothub-service-sdk dans votre application :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
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
    
9. Ajoutez le code ci-après en tant que corps de la méthode **main**. Ce code crée un appareil appelé *javadevice* dans le registre d’identité de votre IoT Hub s’il n’existe pas encore. Il affiche ensuite l’ID et la clé de l’appareil dont vous aurez besoin par la suite :

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

Dans cette section, vous allez créer une application console Java qui lit les messages appareil-à-cloud à partir de l’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hub][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [AZURE.NOTE] Le point de terminaison compatible Event Hub pour lire des messages de l’appareil vers le cloud utilise toujours le protocole AMQPS.

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **read-d2c-messages** en entrant la commande ci-après au niveau de votre invite de commandes. Notez qu’il s’agit d’une seule commande longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier read-d2c-messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier read-d2c-messages et ajoutez la dépendance ci-après au nœud **dependencies**. Cette opération vous permet d’utiliser le package eventhubs-client dans votre application pour effectuer la lecture à partir du point de terminaison compatible Event Hub :

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Ajoutez les instructions **import** ci-après au fichier :

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Ajoutez les variables de niveau classe ci-après à la classe **App**. Remplacez les chaînes **{youriothubkey}**, **{youreventhubcompatiblenamespace}** et **{youreventhubcompatiblename}** par les valeurs que vous avez notées précédemment. La valeur de l’espace réservé **{youreventhubcompatiblenamespace}** provient du **point de terminaison compatible avec Event Hub**. Elle prend la forme **xyznamespace** (en d’autres termes, supprimez le préfixe **sb://** et le suffixe **.servicebus.windows.net** de la valeur de point de terminaison Event Hub à partir du portail) :

    ```
    private static String namespaceName = "{youreventhubcompatiblenamespace}";
    private static String eventHubName = "{youreventhubcompatiblename}";
    private static String sasKeyName = "iothubowner";
    private static String sasKey = "{youriothubkey}";
    private static long now = System.currentTimeMillis();
    ```

8. Ajoutez la méthode **receiveMessages** suivante à la classe **App**. Cette méthode crée une instance **EventHubClient** permettant de vous connecter au point de terminaison compatible Event Hub, puis crée une instance **PartitionReceiver** de façon asynchrone pour lire les données à partir d’une partition Event Hub. La méthode boucle en continu et imprime les détails du message jusqu’à ce que l’application s’arrête.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
        client = EventHubClient.createFromConnectionString(connStr.toString()).get();
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
                Iterable<EventData> receivedEvents = receiver.receive().get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
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

    > [AZURE.NOTE] Cette méthode utilise un filtre lorsqu’elle crée le récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Cette opération est utile dans un environnement de test, car elle vous permet de voir l’ensemble actuel de messages, mais dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, voir le didacticiel [Traiter les messages appareil-à-cloud IoT Hub][lnk-process-d2c-tutorial].

9. Modifiez la signature de la méthode **main** pour y inclure l’exception ci-après :

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Ajoutez le code ci-après à la méthode **main** dans la classe **App**. Ce code crée les deux instances **EventHubClient** et **PartitionReceiver** et vous permet de fermer l’application lorsque vous avez terminé le traitement des messages :

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

    > [AZURE.NOTE] Ce code repose sur l’hypothèse selon laquelle vous avez créé votre IoT Hub dans le niveau F1 (gratuit). Un IoT Hub gratuit comporte deux partitions nommées « 0 » et « 1 ».

11. Enregistrez et fermez le fichier App.java.

12. Pour générer l’application **read-d2c-messages** à l’aide de Maven, exécutez la commande ci-après à l’invite de commandes dans le dossier read-d2c-messages :

    ```
    mvn clean package -DskipTests
    ```

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
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Ajoutez les instructions **import** ci-après au fichier :

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

7. Ajoutez les variables de niveau classe ci-après à la classe **App** en remplaçant la chaîne **{youriothubname}** par le nom de votre IoT Hub, et les chaînes **{yourdeviceid}** et **{yourdevicekey}** par les valeurs d’appareil que vous avez générées à la section *Création d’une identité d’appareil* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Cet exemple d’application utilise la variable **protocole** lorsqu’il instancie un objet **DeviceClient**. Vous pouvez utiliser le protocole HTTPS ou AMQPS pour communiquer avec le IoT Hub.

8. Imbriquez la classe **TelemetryDataPoint** ci-après dans la classe **App** pour spécifier les données de télémétrie que votre appareil envoie à votre IoT Hub :

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

10. Imbriquez la classe **MessageSender** ci-après dans la classe **App**. La méthode **run** de cette classe génère un échantillon de données de télémétrie à envoyer à votre IoT Hub et attend un accusé de réception avant d’envoyer le message suivant :

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

11. Remplacez la méthode **main** par le code ci-après qui crée un thread pour l’envoi de messages Appareil vers cloud à votre IoT Hub :

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

13. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande ci-après au niveau de l’invite de commandes dans le dossier simulated-device :

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier read-d2c dossier, exécutez la commande suivante pour commencer à analyser la première partition de votre IoT Hub :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="0"
    ```

    ![][7]

1. À l’invite de commandes du dossier read-d2c dossier, exécutez la commande suivante pour commencer à analyser la seconde partition de votre IoT Hub :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="1"
    ```

    ![][7]

2. À l’invite de commandes du dossier simulated-device, exécutez la commande suivante pour commencer à analyser votre IoT hub :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. La mosaïque **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au hub :

    ![][43]

## Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau concentrateur IoT dans le portail, puis créé une identité d’appareil dans le registre d’identités du concentrateur. Vous avez utilisé cette identité d’appareil pour activer l'appareil simulé qui envoie des messages de l’appareil vers le cloud au concentrateur et créé une application qui affiche les messages reçus par le concentrateur. Vous pouvez continuer à explorer les fonctionnalités d’IoT Hub et d’autres scénarios IoT dans les didacticiels suivants :

- [Envoyer des messages du cloud vers des appareils avec IoT Hub][lnk-c2d-tutorial] montre comment envoyer des messages aux appareils et traiter les informations de remise générées par IoT Hub.
- [Traiter les messages des appareils vers le cloud][lnk-process-d2c-tutorial] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.
- [Téléchargement de fichiers à partir d’appareils][lnk-upload-tutorial], qui décrit un modèle qui utilise les messages du cloud vers les appareils pour faciliter les téléchargements de fichiers à partir d’appareils.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->