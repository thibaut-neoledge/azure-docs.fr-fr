<properties
	pageTitle="Créer un client Java IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour créer un client IoT Hub Java qui utilise le Kit de développement logiciel (SDK) d’appareil Microsoft Azure IoT pour Java* afin de communiquer avec Azure IoT Hub."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Créer une application cliente Azure IoT Hub à l'aide de Java

Cet article décrit comment créer une application cliente qui utilise le [Kit de développement logiciel (SDK) d’appareil Azure IoT pour Java][lnk-java-sdk] afin de communiquer avec Azure IoT Hub. Ce didacticiel vous montre comment créer et générer le projet à l'aide de l’outil [Maven][apache-maven]. Vous pouvez suivre ces instructions sur une machine Windows ou Linux.

Vous trouverez la documentation de l'API Java [ici][lnk-java-api-docs].

## Installation

Consultez la page [Préparer votre environnement de développement][devbox-setup] pour plus d'informations sur la configuration requise et la configuration de votre environnement de développement sous Windows ou Linux.

> [AZURE.NOTE]Il est important d'effectuer les étapes de la section [Préparer votre environnement de développement][devbox-setup] avant de commencer ce didacticiel pour installer les composants requis et ajouter les fichiers JAR nécessaires à votre référentiel Maven local.

## Création du projet

1. Dans votre outil de ligne de commande, exécutez la commande suivante pour créer un projet Maven vide dans un emplacement approprié sur votre ordinateur de développement :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]Il s'agit d'une commande unique et longue. Veillez à copier la commande complète si vous souhaitez la coller dans votre outil de ligne de commande.

    Cette commande crée un dossier de projet nommé *iot-device* avec la structure de projet Maven standard. Pour plus d'informations, consultez [Maven in 5 Minutes][maven-five-minutes] sur le site Web Apache.

2. Ouvrez le fichier **pom.xml** du dossier iot-device dans un éditeur de texte.

3. Ajoutez la nouvelle section **dependency** suivante après la section existante pour inclure les bibliothèques clientes requises :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. Ajoutez la section **build** suivante après la section **dependency** afin que le fichier JAR final inclue les dépendances et que le manifeste identifie la classe **main**.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Enregistrez le fichier **pom.xml**.

## Création de l'application

1. Ouvrez le fichier **App.java** du dossier iot-device/src/main/java/com/mycompany/app dans un éditeur de texte.

2. Ajoutez les instructions **import** suivantes, notamment les bibliothèques d'appareil IoT, après l’instruction **package** :

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
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. Ajoutez la classe **EventCallback** suivante comme classe imbriquée à l'intérieur de la classe **App**. La méthode **execute** de la classe**EventCallback** est appelée lorsque l’appareil reçoit un accusé de réception en provenance de votre hub IoT en réponse à un message Appareil vers cloud.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. Ajoutez la classe **MessageCallback** suivante comme classe imbriquée à l'intérieur de la classe **App**. La méthode **execute** de la classe**MessageCallback** vous permet d'envoyer un message de commentaires à votre hub IoT en réponse à un message Cloud vers appareil reçu par l’appareil.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. Remplacez la méthode **main** existante par le code suivant qui :

  - Crée une instance **DeviceClient**.
  - Initialise le rappel de message pour les messages Cloud vers appareil.
  - Ouvrez l’instance **DeviceClient** afin qu’elle envoie des messages Appareil vers cloud et de recevoir des messages Cloud vers appareil.
  - Envoie dix exemples de messages à votre hub IoT.

    Remplacez `<your device connection string>` par une chaîne de connexion d’appareil valide. Vous pouvez provisionner un appareil et récupérer sa chaîne de connexion à l'aide de l’outil [DeviceExplorer][lnk-device-explorer] ou [IoT Hub Explorer][lnk-iothub-explorer].

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. Pour compiler le code et l'empaqueter dans un fichier JAR, exécutez la commande suivante à l’aide d’une invite de commande dans le dossier du projet **iot-device** :

    ```
    mvn package
    ```

7. Pour exécuter l'application, exécutez la commande suivante à l’aide d’une invite de commande dans le dossier du projet **iot-device** :

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. Vous pouvez utiliser l’outil [DeviceExplorer][lnk-device-explorer] pour surveiller les messages Appareil vers le cloud que reçoit votre hub IoT et pour envoyer des messages Cloud vers appareil à partir de votre hub IoT.

## Granularité de la journalisation des modifications

Pour modifier la granularité de la journalisation, incluez la ligne suivante dans votre fichier `config.properties` :

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]Vous trouverez [ici](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html) une explication des différents niveaux de journalisation.

Ensuite, définissez la propriété JVM `java.util.logging.config.file={Path to your config.properties file}`.

Pour enregistrer des trames AMQP, définissez la variable d'environnement `PN_TRACE_FRM=1` dans votre environnement de commande.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1203_2015-->