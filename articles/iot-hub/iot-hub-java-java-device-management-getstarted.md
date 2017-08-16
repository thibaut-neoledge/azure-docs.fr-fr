---
title: "Bien démarrer avec la gestion des appareils Azure IoT Hub (Java) | Microsoft Docs"
description: "Guide d’utilisation de la gestion des appareils Azure IoT Hub pour lancer un redémarrage d’appareil à distance. Vous utilisez Azure IoT device SDK pour Java afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et Azure IoT service SDK pour Java afin d’implémenter une application de service qui appelle la méthode directe."
services: iot-hub
documentationcenter: .java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5abf1d80a62344f93d4c5491adba65a3e7400258
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

# <a name="get-started-with-device-management-java"></a>Prise en main de la gestion d’appareils (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ce didacticiel vous explique les procédures suivantes :

* Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.
* Créer une application d’appareil simulé qui implémente une méthode directe pour redémarrer l’appareil. Les méthodes directes sont appelées à partir du cloud.
* Créer une application qui appelle une méthode directe de redémarrage sur l’application d’appareil simulé via votre IoT Hub. Cette application surveille ensuite les propriétés signalées à partir de l’appareil quand le redémarrage est terminé.

À la fin de ce didacticiel, vous disposerez de deux applications console Java :

**simulated-device**. Cette application :

* se connecte à votre hub IoT avec l’identité d’appareil créée précédemment ;
* reçoit un appel de méthode directe de redémarrage ;
* simule un redémarrage physique ;
* indique l’heure du dernier redémarrage par le biais d’une propriété signalée.

**trigger-reboot**. Cette application :

* appelle une méthode directe dans l’application d’appareil simulé ;
* affiche la réponse à l’appel de méthode directe envoyé par l’appareil simulé ;
* affiche les propriétés signalées mises à jour.

> [!NOTE]
> Pour plus d’informations sur les SDK que vous pouvez utiliser pour générer des applications qui s’exécutent sur les appareils et sur le serveur de solution principal, consultez l’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks].

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Java pour ce didacticiel sur Windows ou Linux.
* Maven 3.  <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de [Maven][lnk-maven] pour ce didacticiel sur Windows ou Linux.
* [Node.js version 0.10.0 ou supérieure](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher un redémarrage à distance sur l’appareil à l’aide d’une méthode directe

Dans cette section, vous allez créer une application console Java qui :

1. Appelle la méthode directe de redémarrage dans l’application d’appareil simulé
1. Affiche la réponse
1. Interroge les propriétés signalées envoyées par l’appareil pour déterminer si le redémarrage est terminé

Cette application console se connecte à votre hub IoT pour appeler la méthode directe et lire les propriétés signalées.

1. Créez un dossier vide appelé dm-get-started.

1. Dans le dossier dm-get-started, créez un projet Maven nommé **trigger-reboot** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. À l’invite de commandes, accédez au dossier trigger-reboot.

1. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier trigger-reboot et ajoutez la dépendance suivante au nœud **dependencies**. Cette dépendance vous permet d’utiliser le package iot-device-client dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec la [recherche Maven][lnk-maven-service-search].

1. Ajoutez le nœud **build** suivant après le nœud **dependencies**. Cette configuration ordonne à Maven d’utiliser Java 1.8 pour générer l’application :

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Enregistrez et fermez le fichier pom.xml.

1. À l’aide d’un éditeur de texte, ouvrez le fichier source trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez `{youriothubconnectionstring}` par la chaîne de connexion de votre IoT Hub, que vous avez notée dans la section *Créer un IoT Hub* :

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Pour implémenter un thread qui lit les propriétés signalées à partir du jumeau d’appareil toutes les 10 secondes, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Pour appeler la méthode directe de redémarrage sur l’appareil simulé, ajoutez le code suivant à la méthode **main** :

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Pour démarrer le thread afin d’interroger les propriétés signalées à partir de l’appareil simulé, ajoutez le code suivant à la méthode **main** :

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Pour être en mesure d’arrêter l’application, ajoutez le code suivant à la méthode **main** :

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Enregistrez et fermez le fichier trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Générez l’application principale **trigger-reboot** et corrigez les erreurs éventuelles. À l’invite de commandes, accédez au dossier trigger-reboot et exécutez la commande suivante :

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous créez une application console Java qui simule un appareil. L’application écoute l’appel de méthode directe de redémarrage à partir de votre hub IoT et répond immédiatement à cet appel. L’application se met ensuite en veille pendant un certain temps pour simuler le processus de redémarrage, puis utilise une propriété signalée pour indiquer à l’application principale **trigger-reboot** que le redémarrage est terminé.

1. Dans le dossier dm-get-started, créez un projet Maven nommé **simulated-device** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. À l’invite de commandes, accédez au dossier simulated-device.

1. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulated-device et ajoutez la dépendance suivante au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package iot-device-client dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-device-client** avec la [recherche Maven][lnk-maven-device-search].

1. Ajoutez le nœud **build** suivant après le nœud **dependencies**. Cette configuration ordonne à Maven d’utiliser Java 1.8 pour générer l’application :

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Enregistrez et fermez le fichier pom.xml.

1. À l’aide d’un éditeur de texte, ouvrez le fichier source simulated-device\src\main\java\com\mycompany\app\App.java.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez `{yourdeviceconnectionstring}` par la chaîne de connexion de l’appareil que vous avez notée dans la section *Créer une identité d’appareil* :

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Pour implémenter un gestionnaire de rappel pour les événements d’état de méthode directe, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Pour implémenter un gestionnaire de rappel pour les événements d’état de jumeau d’appareil, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Pour implémenter un gestionnaire de rappel pour les événements de propriété, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Pour implémenter un thread afin de simuler le redémarrage de l’appareil, ajoutez la classe imbriquée suivante à la classe **App**. Le thread se met en veille pendant cinq secondes, puis définit la propriété signalée **lastReboot** :

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Pour implémenter la méthode directe sur l’appareil, ajoutez la classe imbriquée suivante à la classe **App**. Quand l’application simulée reçoit un appel à la méthode directe **reboot**, elle retourne un accusé de réception à l’appelant, puis démarre un thread pour traiter le redémarrage :

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Modifiez la signature de la méthode **main** pour déclencher les exceptions suivantes :

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Pour instancier un **DeviceClient**, ajoutez le code suivant à la méthode **main** :

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Pour commencer à écouter les appels de méthode directe, ajoutez le code suivant à la méthode **main** :

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Pour arrêter le simulateur d’appareil, ajoutez le code suivant à la méthode **main** :

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

1. Générez l’application principale **simulated-device** et corrigez les erreurs éventuelles. À l’invite de commandes, accédez au dossier simulated-device et exécutez la commande suivante :

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier simulated-device, exécutez la commande suivante pour commencer à écouter les appels de méthode de redémarrage à partir de votre IoT Hub :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Application d’appareil simulé Java IoT Hub pour écouter les appels de méthode directe de redémarrage][1]

1. À l’invite de commandes, dans le dossier trigger-reboot, exécutez la commande suivante pour appeler la méthode de redémarrage sur votre appareil simulé à partir de votre IoT Hub :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Application de service Java IoT Hub pour appeler la méthode directe de redémarrage][2]

1. L’appareil simulé répond à l’appel de méthode directe de redémarrage :

    ![L’application d’appareil simulé Java IoT Hub répond à l’appel de méthode directe][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
