<properties
	pageTitle="Traiter les messages des appareils vers le cloud IoT Hub (Java) | Microsoft Azure"
	description="Suivez ce didacticiel Java pour découvrir des modèles utiles pour traiter les messages des appareils vers le cloud IoT Hub."
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
     ms.date="09/01/2016"
     ms.author="dobett"/>

# Didacticiel : traiter les messages des appareils vers le cloud IoT Hub à l’aide de Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Les autres didacticiels ([Prise en main d’IoT Hub] et [Envoyer des messages du cloud vers des appareils avec IoT Hub][lnk-c2d]) vous expliquent comment utiliser la fonctionnalité de base de la messagerie « appareil vers cloud » et « cloud vers appareil » offerte par IoT Hub.

Ce didacticiel s’appuie sur le code illustré dans le didacticiel [Prise en main d’IoT Hub] et décrit deux modèles évolutifs que vous pouvez utiliser pour le traitement des messages de l’appareil vers le cloud :

- Le stockage fiable des messages envoyés de l’appareil vers le cloud dans le [stockage d’objets blob Azure]. Un scénario courant est l’analyse de *chemin à froid*, dans laquelle vous stockez les données de télémétrie dans des objets Blob qui sont ensuite utilisés comme entrée dans les processus d’analyse. Ces processus peuvent être pilotés par des outils tels que [Azure Data Factory] ou la pile [HDInsight (Hadoop)].

- Le traitement fiable des messages *interactifs* des appareils vers le cloud. Les messages envoyés de l’appareil vers le cloud sont considérés comme interactifs lorsqu’ils agissent comme déclencheurs immédiats d’un ensemble d’actions sur le système principal de l’application. Par exemple, un appareil peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM. Par opposition, les messages de *point de données* sont simplement chargés dans un moteur d’analyse. Par exemple, les données de télémétrie de température d’un appareil qui doivent être enregistrées pour analyse ultérieure constituent un message de point de données.

Étant donné qu’IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs] pour recevoir des messages des appareils vers le cloud, ce didacticiel utilise une instance [EventProcessorHost]. Cette instance :

* stocke de manière fiable les messages de *point de données* dans le stockage d’objets blob Azure ;
* transfère les messages *interactifs* appareil-à-cloud vers une [file d’attente Azure Service Bus] en vue de leur traitement immédiat.

Service Bus permet d’assurer un traitement fiable des messages interactifs, car il fournit des points de contrôle par message et la déduplication basée sur la fenêtre temporelle.

> [AZURE.NOTE] Une instance **EventProcessorHost** ne constitue qu’une seule façon de traiter les messages interactifs. Les autres options incluent [Azure Service Fabric][lnk-service-fabric] et [Azure Stream Analytics][lnk-stream-analytics].

À la fin de ce didacticiel, vous exécutez trois applications de console Java :

* **simulated-device**, une version modifiée de l’application créée dans le didacticiel [Prise en main d’IoT Hub] qui envoie des messages de point de données des appareils vers le cloud chaque seconde et des messages interactifs des appareils vers le cloud toutes les 10 secondes. Cette application utilise le protocole AMQPS pour communiquer avec IoT Hub.
* **process-d2c-messages** utilise la classe [EventProcessorHost] pour récupérer des messages à partir du point de terminaison compatible avec Event Hubs. L’instance stocke ensuite les messages de point de données dans le stockage d’objets Blob Azure de façon fiable, puis transfère les messages interactifs à une file d’attente Service Bus.
* **process-interactive-messages** extrait les messages interactifs de la file d’attente Service Bus.

> [AZURE.NOTE] IoT Hub offre la prise en charge de Kit de développement logiciel (SDK) de plusieurs plateformes d’appareils et de plusieurs langages (notamment C, Java et JavaScript). Pour obtenir des instructions expliquant comment remplacer l’appareil simulé de ce didacticiel par un appareil physique et comment connecter vos appareils à IoT Hub, consultez le [Centre de développement Azure IoT].

Ce didacticiel s’applique directement à d’autres manières de consommer des messages compatibles avec Event Hubs, par exemple des projets [HDInsight (Hadoop)]. Pour plus d’informations, consultez le [Guide du développeur Azure IoT Hub - Appareil vers cloud].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

+ Une version d’utilisation complète du didacticiel [Prise en main d’IoT Hub].

+ Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Java pour ce didacticiel sur Windows ou Linux.

+ Maven 3. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Maven pour ce didacticiel sur Windows ou Linux.

+ Un compte Azure actif. <br/>Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Vous devez avoir une connaissance de base des services [Azure Storage] et [Azure Service Bus].


## Envoi de messages interactifs depuis un appareil simulé

Dans cette section, vous modifiez l’application de l’appareil simulé que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] pour envoyer des messages appareil-à-cloud interactifs au IoT Hub.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java. Ce fichier contient le code pour l’application **simulated-device** que vous avez créée dans le didacticiel [Prise en main d’IoT Hub].

2. Ajoutez la classe imbriquée ci-après dans la classe **App** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Cette classe est semblable à la classe **MessageSender** dans le projet **simulated-device**. Seules différences : à présent, vous définissez la propriété système **MessageId** et une propriété personnalisée appelée **messageType**. Le code assigne un identificateur universel unique (UUID) à la propriété **MessageId**. Service Bus peut l’utiliser pour dédupliquer les messages qu’il reçoit. L’exemple utilise la propriété **messageType** pour distinguer les messages interactifs et les messages de point de données. L’application transmet ces informations dans les propriétés du message, plutôt que dans le corps du message, afin que le processeur d’événements n’ait pas besoin de désérialiser le message pour le router.

    > [AZURE.NOTE] Il est important de créer le **MessageId** utilisé pour dédupliquer les messages interactifs dans le code de l’appareil. Les communications réseau intermittentes ou d’autres défaillances pourraient entraîner une retransmission multiple du même message à partir de cet appareil. Vous pouvez également utiliser un ID de message sémantique, comme un hachage des champs de données de message pertinents, à la place d’un UUID.

3. Modifiez la méthode **main** pour envoyer des messages interactifs et des messages de point de données comme indiqué dans l’extrait de code suivant :

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Enregistrez et fermez le fichier simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] \(Gestion des erreurs temporaires).

5. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :

    ```
    mvn clean package -DskipTests
    ```

## Traitement des messages appareil-à-cloud

Dans cette section, vous allez créer une application de console Java qui traite les messages des appareils vers le cloud dans IoT Hub. IoT Hub expose un point de terminaison compatible avec [Event Hubs] pour permettre à une application de lire les messages appareil-à-cloud. Ce didacticiel utilise la classe [EventProcessorHost] pour traiter ces messages dans une application de console. Pour plus d’informations sur la façon de traiter les messages à partir d’Event Hubs, consultez le didacticiel [Prise en main des hubs d’événements].

Le défi principal auquel vous êtes confronté lors de l’implémentation d’un stockage fiable des messages de point de données ou du transfert des messages interactifs, est que le traitement des événements s’appuie sur le consommateur de message pour contrôler la progression. En outre, pour atteindre un débit élevé, lors de la lecture à partir d’Event Hubs, vous devez effectuer les contrôles par grands lots, créant ainsi une possibilité de traitement en double pour un grand nombre de messages en cas d’erreur où vous devriez revenir au point de contrôle précédent. Dans ce didacticiel, vous verrez comment synchroniser des écritures de stockage Azure et des fenêtres de déduplication Service Bus avec les points de contrôle **EventProcessorHost**.

Pour écrire des messages dans le stockage Azure de manière fiable, l’exemple utilise la fonctionnalité de validation individuelle des blocs des [objets blob de blocs][Azure Block Blobs]. Le processeur d’événements accumule les messages en mémoire jusqu'à ce qu’il soit temps de fournir un point de contrôle. Par exemple, une fois que la mémoire tampon de messages accumulés atteint la taille de bloc maximale de 4 Mo, ou après le délai d’expiration de la déduplication Service Bus. Ensuite, avant d’effectuer le contrôle, le code valide un nouveau bloc dans l’objet blob.

Le processeur d’événements utilise les décalages des messages des hubs d’événements comme ID de bloc. Ce mécanisme permet au processeur d’événements d’effectuer une vérification de la déduplication avant de valider le nouveau bloc dans le stockage, en tenant compte d’un possible incident entre un bloc et le point de contrôle.

> [AZURE.NOTE] Ce didacticiel utilise un même compte de stockage pour enregistrer tous les messages récupérés d’IoT Hub. Pour décider si vous avez besoin d’utiliser plusieurs comptes Azure Storage dans votre solution, consultez [Objectifs de performance et évolutivité d’Azure Storage].

L’application utilise la fonctionnalité de déduplication de Service Bus afin d’éviter les doublons lorsqu’elle traite les messages interactifs. L’appareil simulé affecte à chaque message interactif un **MessageId** unique. Cet ID Service Bus peut ainsi garantir que la fenêtre de temps de déduplication spécifiée ne remettra jamais deux messages possédant le même **MessageId** aux récepteurs. Cette déduplication, conjointement avec la sémantique d’achèvement par message fournie par les files d’attente Service Bus, facilite le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé en dehors de la fenêtre de déduplication, le code synchronise le mécanisme de point de contrôle **EventProcessorHost** avec la fenêtre de déduplication de la file d’attente Service Bus. Pour cette synchronisation, vous devez forcer un point de contrôle au moins une fois par écoulement de fenêtre de déduplication (une heure dans ce didacticiel).

> [AZURE.NOTE] Ce didacticiel utilise une file d’attente Service Bus partitionnée unique pour traiter tous les messages interactifs récupérés d’IoT Hub. Pour plus d’informations sur l’utilisation des files d’attente Service Bus pour répondre aux exigences d’évolutivité de votre solution, consultez la documentation [Azure Service Bus].

### Approvisionner un compte Azure Storage et une file d’attente Service Bus

Pour utiliser la classe [EventProcessorHost], vous devez disposer d’un compte Azure Storage pour permettre à **l’EventProcessorHost** d’enregistrer les informations de point de contrôle. Vous pouvez utiliser un compte de stockage existant ou suivre les instructions figurant dans [À propos des comptes de stockage Azure] pour en créer un. Notez la chaîne de connexion du compte de stockage.

> [AZURE.NOTE] Lorsque vous copiez et collez la chaîne de connexion du compte de stockage, assurez-vous que la chaîne de connexion ne contient aucun espace.

Vous avez également besoin d’une file d’attente Service Bus pour permettre un traitement fiable des messages interactifs. Vous pouvez créer une file d’attente par programmation avec une fenêtre de déduplication de 1 heure, comme expliqué dans [Prise en main des files d’attente Service Bus][Service Bus queue]. Vous pouvez également utiliser le [portail Azure classique][lnk-classic-portal], en procédant comme suit :

1. Cliquez sur **Nouveau** dans l’angle inférieur gauche. Cliquez ensuite sur **App Services** > **Service Bus** > **File d’attente** > **Création personnalisée**. Entrez le nom **d2ctutorial**, sélectionnez une région et utilisez un espace de noms existant ou créez-en un. Prenez note du nom de l’espace de noms, vous en aurez besoin plus loin dans ce didacticiel. Sur la page suivante, sélectionnez **Activer la détection des doublons**, puis définissez la **fenêtre d’heures de l’historique des détections dupliquées** sur une heure. Cliquez ensuite sur la coche dans l’angle inférieur droit pour enregistrer la configuration de votre file d’attente.

    ![Créer une file d’attente dans le portail Azure][30]

2. Dans la liste des files d’attente Service Bus, cliquez sur **d2ctutorial**, puis sur **Configurer**. Créez deux stratégies d’accès partagé, une appelée **send** avec les autorisations **Send** et une autre nommée **listen** avec les autorisations **Listen**. Prenez note des valeurs de la **clé primaire** pour les deux stratégies, vous en aurez besoin plus loin dans ce didacticiel. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.

    ![Configurer une file d’attente dans le portail Azure][31]

### Créer le processeur d’événements

Dans cette section, vous créez une application Java pour traiter les messages du point de terminaison compatible avec Event Hubs.

La première tâche consiste à ajouter un projet Maven nommé **process-d2c-messages** qui reçoit les messages appareil vers cloud à partir du point de terminaison compatible Event Hubs IoT Hub et achemine ces messages vers d’autres services principaux.

1. Dans le dossier iot-java-get-started que vous avez créé dans le didacticiel [Prise en main d’IoT Hub], créez un projet Maven appelé **process-d2c-messages** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier process-d2c-messages.

3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier process-d2c-messages et ajoutez les dépendances suivantes au nœud **dependencies**. Ces dépendances vous permettent d’utiliser les packages azure-eventhubs, azure-eventhubs-eph et azure-servicebus dans votre application pour interagir avec votre IoT hub et la file d’attente Service Bus :

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

La tâche suivante consiste à ajouter une classe **ErrorNotificationHandler** au projet.

1. Utilisez un éditeur de texte pour créer un fichier process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\ErrorNotificationHandler.java. Ajoutez le code suivant au fichier pour afficher les messages d’erreur à partir de l’instance **EventProcesssorHost** :

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Enregistrez et fermez le fichier ErrorNotificationHandler.java.

Vous pouvez maintenant ajouter une classe qui implémente l’interface **IEventProcessor**. La classe **EventProcessorHost** appelle cette classe pour traiter les messages appareil-à-cloud reçus d’IoT Hub. Le code dans cette classe implémente la logique, afin de stocker les messages de manière fiable dans un conteneur d’objets blob et de transférer les messages interactifs à la file d’attente Service Bus.

La méthode **onEvents** définit la variable **latestEventData** qui suit le décalage et le numéro de séquence du dernier message lu par le processeur d’événements. N’oubliez pas : chaque processeur est chargé d’une partition unique. La méthode **onEvents** reçoit ensuite un lot de messages d’IoT Hub et les traite comme suit : elle envoie des messages interactifs à la file d’attente Service Bus et ajoute les messages de point de données à la mémoire tampon appelée **toAppend**. Si la mémoire tampon atteint la limite de bloc de 4 Mo ou si la fenêtre de temps de déduplication s’est écoulée (une heure depuis le dernier point de contrôle dans ce didacticiel), un point de contrôle est déclenché par la méthode.

La méthode **AppendAndCheckPoint** génère d’abord un **blockId** pour le bloc à ajouter à l’objet blob. Le stockage Azure requiert que tous les ID de bloc aient la même longueur, la méthode remplit donc le décalage avec des zéros non significatifs. Ensuite, si un bloc avec cet ID se trouve déjà dans le blob, la méthode le remplace par le contenu de la mémoire tampon.

> [AZURE.NOTE] Pour simplifier le code, ce didacticiel utilise un fichier blob unique par partition pour stocker les messages. La solution serait d’implémenter une substitution de fichier, en créant des fichiers supplémentaires lorsqu’ils atteignent une certaine taille ou après un certain laps de temps. N’oubliez pas qu’un objet blob de blocs Azure peut contenir au maximum 195 Go de données.

La tâche suivante consiste à implémenter l’interface **IEventProcessor** :

1. Utilisez un éditeur de texte pour créer un fichier process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\EventProcessor.java.

2. Ajoutez les importations et la définition de classe suivantes dans le fichier EventProcessor.java. La classe **EventProcessor** implémente l’interface **IEventProcessor** qui définit le comportement du client Event Hubs :

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Ajoutez les méthodes suivantes à la classe **EventProcessor** pour implémenter l’interface **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Ajoutez les variables de niveau classe suivantes à la classe **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Ajoutez une méthode **AppendAndCheckPoint** avec la signature suivante à la classe **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Ajoutez le code suivant à la méthode **AppendAndCheckPoint** pour récupérer le décalage et le numéro de séquence du message en cours dans la partition :

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Dans la méthode **AppendAndCheckPoint**, utilisez la valeur de décalage actuelle pour créer une instance **BlockEntry** pour le bloc suivant à enregistrer dans l’objet blob :

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Dans la méthode **AppendAndCheckPoint**, téléchargez la dernière série de messages dans l’objet blob de blocs et récupérez la liste actuelle des blocs :

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Dans la méthode **AppendAndCheckPoint**, créez le premier bloc dans un nouvel objet blob ou ajoutez le bloc à l’objet blob existant :

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Enfin, dans la méthode **AppendAndCheckPoint**, créez un point de contrôle sur la partition et préparez-vous à enregistrer le bloc de messages suivant :

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Dans la méthode **onEvents**, ajoutez le code suivant pour recevoir des messages à partir du point de terminaison IoT Hub et transférer les messages interactifs à la file d’attente Service Bus. Appelez ensuite la méthode **AppendAndCheckPoint** lorsque le bloc est plein ou lorsque le délai d’attente expire :

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Enfin, dans la méthode **onEvents**, ajoutez une clause « else if » pour appeler **AppendAndCheckPoint** si le délai d’attente expire alors qu’aucun message ne provient de IoT Hub :

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Enregistrez les modifications apportées au fichier EventProcessor.java.

La dernière tâche du projet **process-d2c-messages** consiste à ajouter du code dans la méthode **main** qui instancie une instance **EventProcessorHost**.

1. À l’aide d’un éditeur de texte, ouvrez le fichier process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Ajoutez les instructions **import** suivantes au fichier :

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Ajoutez les variables de niveau classe suivantes à la classe **App**. Remplacez **{yourstorageaccountconnectionstring}** par la chaîne de connexion du compte de stockage Azure que vous avez notée précédemment dans la section [Approvisionner un compte Azure Storage et une file d’attente Service Bus](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Ajoutez les variables de niveau classe suivantes à la classe **App** et remplacez **{yourservicebusnamespace}** par votre espace de noms Service Bus et **{yourservicebussendkey}** par la clé **send** de votre file d’attente. Vous aviez noté les valeurs de l’espace de noms et de la clé **listen** dans la section [Approvisionner un compte Azure Storage et une file d’attente Service Bus](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Ajoutez les variables de niveau classe suivantes à la classe **App**. Remplacez **{youreventhubcompatibleendpoint}** par le nom de point de terminaison compatible avec Event Hub. Le nom du point de terminaison ressemble à **ihs....namespace**, vous devez donc supprimer le préfixe **sb://** et le suffixe **.servicebus.windows.net/**. Remplacez **{youreventhubcompatiblename}** par le nom compatible avec Event Hub. Remplacez **{youriothubkey}** par la clé **iothubowner**. Vous avez noté ces valeurs dans la section [Création d’un IoT Hub][lnk-create-an-iot-hub] du didacticiel *Prise en main d’Azure IoT Hub pour Java* :

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modifiez la signature de la méthode **main** comme suit :

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Ajoutez le code suivant à la méthode **main** pour obtenir une référence au conteneur d’objets blob qui stocke les messages :

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Ajoutez le code suivant à la méthode **main** pour obtenir une référence au service Service Bus :

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. Dans la méthode **main**, configurez et créez une instance **EventProcessorHost**. L’option **setInvokeProcessorAfterReceiveTimeout** garantit que l’instance **EventProcessorHost** appelle la méthode **onEvents** dans l’interface **IEventProcessor** même s’il n’existe pas de message à traiter. Ensuite, la méthode **onEvents** appelle toujours la méthode **AppendAndCheckPoint** lors de l’expiration du délai d’attente.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Dans la méthode **main**, enregistrez la mise en œuvre **IEventProcessor** avec l’instance **EventProcessorHost** :

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Enfin, ajoutez une logique à la méthode **main** pour arrêter l’instance **EventProcessorHost** :

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Enregistrez et fermez le fichier process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

13. Pour générer l’application **process-d2c-messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier process-d2c-messages :

    ```
    mvn clean package -DskipTests
    ```

## Recevoir des messages interactifs

Dans cette section, vous écrivez une application de console Java qui reçoit les messages interactifs à partir de la file d’attente Service Bus.

La première tâche consiste à ajouter un projet Maven nommé **process-interactive-messages** qui reçoit les messages envoyés sur la file d’attente Service Bus à partir des instances **EventProcessor**.

1. Dans le dossier iot-java-get-started que vous avez créé dans le didacticiel [Prise en main d’IoT Hub], créez un projet Maven appelé **process-interactive-messages** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier process-interactive-messages.

3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier process-interactive-messages et ajoutez la dépendance suivante au nœud **dependencies**. Cette dépendance vous permet d’utiliser le package azure-servicebus dans votre application pour interagir avec votre file d’attente Service Bus :

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

La tâche suivante consiste à ajouter du code pour récupérer les messages à partir de la file d’attente Service Bus.

1. À l’aide d’un éditeur de texte, ouvrez le fichier process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Ajoutez les instructions `import` suivantes au fichier :

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Ajoutez les variables de niveau classe suivantes à la classe **App** et remplacez **{yourservicebusnamespace}** par votre espace de noms Service Bus et **{yourservicebuslistenkey}** par la clé **listen** de votre file d’attente. Vous aviez noté les valeurs de l’espace de noms et de la clé **listen** dans la section [Approvisionner un compte Azure Storage et une file d’attente Service Bus](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Ajoutez la classe imbriquée suivante à la classe **App** pour recevoir des messages de la file d’attente :

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modifiez la signature de la méthode **main** comme suit :

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Dans la méthode **main**, ajoutez le code suivant pour commencer à écouter les nouveaux messages :

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Enregistrez et fermez le fichier process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

8. Pour générer l’application **process-interactive-messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier process-interactive-messages :

    ```
    mvn clean package -DskipTests
    ```

## Exécution des applications

Vous êtes maintenant prêt à exécuter les trois applications.

1.	Pour exécuter l’application **process-interactive-messages**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier process-interactive-messages et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter process-interactive-messages][processinteractive]

2.	Pour exécuter l’application **process-d2c-messages**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier process-d2c-messages et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter process-d2c-messages][processd2c]

3.	Pour exécuter l’application **simulated-device**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier simulated-device et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter simulated-device][simulateddevice]

> [AZURE.NOTE] Pour afficher les mises à jour dans votre fichier blob, vous devrez peut-être définir la constante **MAX\_BLOCK\_SIZE** dans la classe **StoreEventProcessor** sur une valeur inférieure, telle que **1024**. Cette modification est utile car il faut un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par l’appareil simulé. Avec une plus petite taille de bloc, vous n’avez pas attendre si longtemps avant la création et la mise à jour de l’objet blob. Sachez toutefois qu’une plus grande taille de bloc favorise l’évolutivité de l’application.

## Étapes suivantes

Dans ce didacticiel, vous avez appris à traiter de manière fiable des messages de point de données et des messages interactifs appareil-à-cloud à l’aide de la classe [EventProcessorHost].

Le didacticiel [Envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d] vous montre comment envoyer des messages à vos appareils depuis votre serveur principal.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[stockage d’objets blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[file d’attente Azure Service Bus]: ../service-bus/service-bus-dotnet-get-started-with-queues.md

[Guide du développeur Azure IoT Hub - Appareil vers cloud]: iot-hub-devguide.md#d2c

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT Hub]: iot-hub-devguide.md
[Prise en main d’IoT Hub]: iot-hub-java-java-getstarted.md
[Prise en main d’IoT Hub]: iot-hub-java-java-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[À propos des comptes de stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Prise en main des hubs d’événements]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Objectifs de performance et évolutivité d’Azure Storage]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Event Hubs Programming Guide]: https://github.com/Azure/azure-event-hubs/blob/master/java/readme.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

<!---HONumber=AcomDC_0907_2016-->