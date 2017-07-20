---
title: "Utilisation des méthodes directes Azure IoT Hub (.NET/Node) | Microsoft Docs"
description: "Utilisation des méthodes directes Azure IoT Hub. Vous utilisez Azure IoT device SDK pour Node.js afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui appelle la méthode directe."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: fe804cc01925cee58a1d694bdb94b85a8f994cef
ms.contentlocale: fr-fr
ms.lasthandoff: 05/19/2017


---
# <a name="use-direct-methods-java"></a>Utilisation des méthodes directes (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Dans ce tutoriel, vous allez créer deux applications de console Java :

* **invoke-direct-method**, une application principale Java qui appelle une méthode dans l’application d’appareil simulé et affiche la réponse.
* **simulated-device**, une application Java qui simule un appareil connecté à votre IoT Hub avec l’identité de l’appareil que vous créez et qui répond à la méthode directe appelée à partir du serveur principal.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Java pour ce didacticiel sur Windows ou Linux.
* Maven 3.  <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de [Maven][lnk-maven] pour ce didacticiel sur Windows ou Linux.
* [Node.js version 0.10.0 ou supérieure](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application de console Java qui répond à une méthode appelée par le serveur principal de la solution.

1. Créez un dossier vide appelé iot-java-direct-method. Dans le dossier iot-java-direct-method, créez un projet Maven nommé **simulated-device** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

1. Dans le dossier iot-java-direct-method, créez un projet Maven nommé **simulated-device** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. À l’invite de commandes, accédez au dossier simulated-device.

1. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulated-device et ajoutez les dépendances suivantes au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package iot-device-client dans votre application pour communiquer avec votre hub IoT :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
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

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez la chaîne **{youriothubname}** par le nom de votre IoT Hub, et **{yourdevicekey}** par la valeur clé d’appareil que vous avez générée dans la section *Création d’une identité d’appareil* :

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Cet exemple d’application utilise la variable **protocol** lorsqu’il instancie un objet **DeviceClient**. Actuellement, pour utiliser les méthodes directes, vous devez utiliser le protocole MQTT.

1. Ajoutez la classe imbriquée suivante à la classe **App** pour retourner un code d’état à votre IoT Hub :

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Ajoutez la classe imbriquée suivante à la classe **App** pour gérer les appels de méthode directe depuis le serveur de solution principal :

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
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

1. Ajoutez une méthode **principale** à la classe **App** pour créer un **DeviceClient** et écoutez les appels de méthode directe :

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java

1. Générez l’application **simulated-device** et corrigez les erreurs. À l’invite de commandes, accédez au dossier simulated-device et exécutez la commande suivante :

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>Appeler une méthode directe sur un appareil

Dans cette section, vous allez créer une application de console Java qui appelle une méthode directe sur l’application d’appareil simulé, puis affiche la réponse. Cette application console se connecte à votre IoT Hub pour appeler la méthode directe.

1. Dans le dossier iot-java-direct-method, créez un projet Maven nommé **invoke-direct-method** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. À l’invite de commandes, accédez au dossier invoke-direct-method.

1. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier invoke-direct-method et ajoutez la dépendance suivante au nœud **dependencies**. Cette dépendance vous permet d’utiliser le package iot-device-client dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. À l’aide d’un éditeur de texte, ouvrez le fichier invoke-direct-method\src\main\java\com\mycompany\app\App.java.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez **{youriothubconnectionstring}** par la chaîne de connexion IoT Hub que vous avez notée dans la section *Créer un hub IoT* :

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. Ajoutez le code suivant à la méthode **main** pour appeler la méthode sur l’appareil simulé :

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. Enregistrez et fermez le fichier invoke-direct-method\src\main\java\com\mycompany\app\App.java.

1. Générez l’application **invoke-direct-method** et corrigez les erreurs éventuelles. À l’invite de commandes, accédez au dossier invoke-direct-method et exécutez la commande suivante :

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications de console.

1. À l’invite de commandes, dans le dossier simulated-device, exécutez la commande suivante pour commencer à écouter les appels de méthode à partir de votre IoT Hub :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Application d’appareil simulé Java IoT Hub pour écouter les appels de méthode directe][8]

1. À l’invite de commandes, dans le dossier invoke-direct-method, exécutez la commande suivante pour appeler une méthode sur votre appareil simulé à partir de votre IoT Hub :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Application de service Java IoT Hub pour appeler une méthode directe][7]

1. L’appareil simulé répond à l’appel de méthode directe :

    ![L’application d’appareil simulé Java IoT Hub répond à l’appel de méthode directe][9]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’application d’appareil simulé de réagir à des méthodes appelées par le cloud. Vous avez également créé une application qui appelle des méthodes sur l’appareil et affiche la réponse de celui-ci.

Pour explorer d’autres scénarios IoT, consultez [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs].

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22

