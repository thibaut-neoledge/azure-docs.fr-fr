---
title: Envoi de messages cloud-à-appareil avec IoT Hub | Microsoft Docs
description: Suivez ce didacticiel pour découvrir comment envoyer des messages cloud-à-appareil à l’aide d’Azure IoT Hub avec Java.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: dobett

---
# Didacticiel : Envoi de messages cloud-à-appareil avec IoT Hub et Java
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introduction
Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Le didacticiel [Prise en main d’IoT Hub] explique comment créer un concentrateur IoT, l’utiliser pour configurer une identité d’appareil et coder une simulation d’appareil qui envoie des messages d’appareils à cloud.

Ce didacticiel s’appuie sur l’article [Prise en main d’Azure IoT Hub]. Cette rubrique vous explique les procédures suivantes :

* À partir du back end de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.
* Recevez des messages cloud-à-appareil sur un appareil.
* À partir du back end de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages du cloud vers les appareils dans le [Guide du développeur d’IoT Hub][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous exécutez deux applications de console Java :

* **simulated-device**, une version modifiée de l’application créée dans [Prise en main d’Azure IoT Hub] qui se connecte à votre IoT Hub et reçoit les messages cloud-à-appareil.
* **send-c2d-messages**, qui envoie un message cloud-à-appareil à l’appareil simulé par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT].
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Java pour ce didacticiel sur Windows ou Linux.
* Maven 3. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Maven pour ce didacticiel sur Windows ou Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Version d’évaluation gratuite d’Azure][lnk-free-trial].

## Réception de messages sur le périphérique simulé
Dans cette section, vous modifiez l’application de l’appareil simulé que vous avez créée dans [Prise en main d’Azure IoT Hub] pour recevoir des messages cloud-à-appareil à partir d’IoT Hub.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.
2. Ajoutez la classe **MessageCallback** suivante comme classe imbriquée à l'intérieur de la classe **App**. La méthode **execute** est appelée lorsque l’appareil reçoit un message d’IoT Hub. Dans cet exemple, l’appareil notifie toujours le concentrateur qu’il a terminé le message :
   
    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
   
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Modifiez la méthode **main** pour créer une instance **MessageCallback** et appelez la méthode **setMessageCallback** avant l’ouverture du client comme suit :
   
    ```
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```
   
   > [!NOTE]
   > Si vous utilisez HTTP/1 au lieu d’AMQP comme moyen de transport, l’instance **DeviceClient** vérifie les messages à partir d’IoT Hub peu fréquemment (moins de toutes les 25 minutes). Pour plus d’informations sur les différences entre la prise en charge d’AMQP et de HTTP/1 et la limitation d’IoT Hub, consultez le [Guide du développeur Azure IoT Hub][IoT Hub Developer Guide - C2D].
   > 
   > 

## Envoi d’un message cloud vers appareil
Dans cette section, vous créez une application de console Java qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le didacticiel [Prise en main d’IoT Hub]. Vous avez également besoin de la chaîne de connexion pour votre IoT Hub que vous trouverez dans le [portail Azure].

1. Créez un projet Maven nommé **send-c2d-messages** à l’aide de la commande ci-dessous, à l’invite de commandes. Il s’agit d’une commande unique et longue :
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. À l’invite de commandes, accédez au nouveau dossier send-c2d-messages.
3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier send-c2d-messages et ajoutez la dépendance suivante au nœud **dependencies**. L’ajout de la dépendance vous permet d’utiliser le package **iothub-java-service-client** dans votre application pour communiquer avec votre service d’IoT Hub :
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```
4. Enregistrez et fermez le fichier pom.xml.
5. À l’aide d’un éditeur de texte, ouvrez le fichier send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.
6. Ajoutez les instructions **import** ci-après au fichier :
   
    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Ajoutez les variables de niveau classe suivantes à la classe **App** en remplaçant **{yourhubconnectionstring}** et **{yourdeviceid}** par les valeurs que vous avez notées précédemment :
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
8. Remplacez la méthode **main** par le code suivant qui se connecte à votre IoT Hub, envoie un message à votre appareil, puis attend un accusé de réception indiquant que l’appareil a reçu le message et l’a traité :
   
    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```
   
   > [!NOTE]
   > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] \(Gestion des erreurs temporaires).
   > 
   > 

## Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier simulated-device, exécutez la commande suivante pour commencer à envoyer la télémétrie à votre IoT Hub et écouter les messages cloud-à-appareil envoyés depuis ce dernier :
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Exécution de l’application de périphérique simulé][img-simulated-device]
2. À l’invite de commandes dans le dossier send-c2d-messages, exécutez la commande suivante pour envoyer un message cloud-à-appareil, puis attendez de recevoir l’accusé de réception :
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Exécutez la commande pour envoyer le message cloud-à-appareil][img-send-command]

## Étapes suivantes
Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]: media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Prise en main d’Azure IoT Hub]: iot-hub-java-java-getstarted.md
[Prise en main d’IoT Hub]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Guide du développeur IoT Hub]: iot-hub-devguide.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portail Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0914_2016-->