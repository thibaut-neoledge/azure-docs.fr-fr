---
title: "Traitement des événements d’Event Hubs avec Storm sur HDInsight avec Java | Microsoft Docs"
description: "Découvrez comment traiter les données Event Hubs avec une topologie Storm basée sur Java créée avec Maven."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)

Découvrez comment utiliser Azure Event Hubs avec Storm sur HDInsight. Cet exemple utilise des composants basés sur Java pour lire et écrire des données dans Azure Event Hubs.

Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hub simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

## <a name="prerequisites"></a>Composants requis

* Un cluster Apache Storm sur HDInsight version 3.6. Pour plus d’informations, voir [Prise en main de Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un [hub d’événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Kit de développeur Java (JDK) Oracle version 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou un équivalent, par exemple, [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi) : Maven est un système de génération de projet pour les projets Java.

* Un éditeur de texte ou un environnement de développement intégré (IDE).

    > [!NOTE]
    > Votre éditeur ou IDE peut avoir des fonctionnalités spécifiques pour l’utilisation avec Maven, qui ne sont pas traitées dans ce document. Pour plus d’informations sur les capacités de votre environnement d’édition, consultez la documentation du produit que vous utilisez.

    * Un client SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* Les commandes `ssh` et `scp`. Elles servent à copier des fichiers vers le cluster HDInsight. Sous Windows, vous pouvez les obtenir via Bash dans Windows 10.

## <a name="understanding-the-example"></a>Vue d’ensemble de l’exemple

L’exemple [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contient deux topologies :

La topologie `resources/writer.yaml` écrit des données aléatoires dans Azure Event Hub. Les données sont générées par le composant `DeviceSpout` et comprennent un ID d’appareil aléatoire et une valeur d’appareil. Ainsi, elles simulent certains matériels qui émettent un ID de chaîne et une valeur numérique.

La topologie `resources/reader.yaml` lit les données Event Hub (les données écrites par EventHubWriter), analyse les données JSON, puis enregistre les données `deviceId` et `deviceValue`.

Les données sont formatées sous forme de document JSON avant d’être écrites dans l’Event Hub ; lors de la lecture par le lecteur, elles sont analysées depuis JSON vers des tuples. Le format JSON est le suivant :

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configuration du projet

Le fichier `POM.xml` contient des informations de configuration pour ce projet Maven. Éléments intéressants :

#### <a name="event-hub-components"></a>Composants Event Hub

Le composant qui lit et écrit dans Azure Event Hubs se trouve dans le [référentiel HDInsight](https://github.com/hdinsight/mvn-rep). Les sections suivantes du fichier `POM.xml` chargent les composants à partir de ce référentiel.

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>Dépendance du spout Storm EventHubs

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

Ce fichier XML définit une dépendance pour le package eventhubs. Il contient à la fois un spout pour la lecture à partir d’Event Hubs et un bolt pour l’écriture dans Event Hubs.

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Ce fichier XML configure le projet de sorte à générer un résultat pour Java 8, résultat qui est ensuite utilisé par HDInsight 3.5 ou une version ultérieure.

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

Ce fichier XML configure la solution pour empaqueter la sortie dans une sorte de super fichier jar. Ce fichier jar contient le code de projet et les dépendances requises. Il est également utilisé pour :

* Renommer les fichiers de licence pour les dépendances.
* Exclure les sécurités/signatures.
* Assurez-vous que plusieurs implémentations de la même interface soient fusionnées dans une entrée.

Ces paramètres de configuration évitent les erreurs au moment de l’exécution.

#### <a name="topology-definitions"></a>Définitions de topologie

Cet exemple utilise le framework [Flux](https://storm.apache.org/releases/1.1.0/flux.html). Ce framework utilise le langage YAML pour définir les topologies. Le principal avantage, c’est que vous ne codez pas la topologie en dur dans le code Java. Étant donné que la définition est rédigée en YAML, vous pouvez la modifier avant de soumettre la topologie, sans devoir tout recompiler.

__writer.yaml__ :

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__ :

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Transmettre les informations Event Hub à la topologie

Au moment de l’exécution, le fichier `dev.properties` est utilisé pour transmettre la configuration Event Hub à la topologie. L’exemple suivant montre le contenu par défaut du fichier :

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`
* **PATH** :doit contenir les chemins d’accès suivants :

  * **JAVA_HOME** (ou le chemin d’accès équivalent)
  * **JAVA_HOME\bin** (ou le chemin d’accès équivalent)
  * Le répertoire d’installation de Maven

## <a name="configure-event-hub"></a>Configuration du hub d'événements

Event Hubs est la source de données pour cet exemple. Procédez comme suit pour créer un Event Hub.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **NEW** > **Service Bus** > **Event Hub** > **Création personnalisée**.

2. Dans l’écran **Ajouter un nouvel hub d’événements**, entrez le **Nom du hub d’événements**. Sélectionnez la **région** pour créer le hub, puis créez un espace de noms ou sélectionnez-en un existant. Enfin, cliquez sur la **flèche** pour continuer.

    ![page 1 de l’assistant](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > Vous devez sélectionner le même **emplacement** que celui de votre serveur Storm sur HDInsight pour réduire la latence et les coûts.

3. Dans l’écran **Configurer un hub d’événements**, entrez les valeurs pour **Nombre de partitions** et **Rétention des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous en aurez besoin ultérieurement.

    ![page 2 de l’assistant](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. Une fois le hub d’événements créé, sélectionnez l’espace de noms, **Event Hubs**, puis le hub d’événements créé auparavant.
5. Sélectionnez **Configurer**, puis créez deux nouvelles stratégies d’accès en utilisant les informations suivantes :

    <table>
    <tr><th>Nom</th><th>Autorisations</th></tr>
    <tr><td>Writer</td><td>Envoyer</td></tr>
    <tr><td>Lecteur</td><td>Écouter</td></tr>
    </table>

    Après avoir créé les autorisations, sélectionnez l’icône **Enregistrer** située en bas de page. Ces stratégies d’accès partagé sont utilisées pour lire et écrire dans Event Hub.

    ![stratégies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. Après avoir enregistré vos stratégies, utilisez le **Générateur de clés d’accès partagé** situé en bas de page pour récupérer les clés des stratégies **writer** et **reader**. Enregistrez ces clés.

## <a name="download-and-build-the-project"></a>Télécharger et générer le projet

1. Téléchargez le projet à partir de GitHub : [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Vous pouvez télécharger le package dans une archive .zip ou utiliser [git](https://git-scm.com/) pour cloner le projet localement.

2. Modifiez le fichier `dev.properties` et intégrez-y la configuration de votre Event Hub.

3. Pour générer le projet et créer un package, utilisez la commande suivante :

        mvn package

    Cette commande télécharge les dépendances requises, génère le projet, puis crée le package. Le résultat est stocké dans le répertoire **/target** dans un fichier **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="test-locally"></a>Tester les topologies localement

Ces topologies lisent et écrivent uniquement dans Event Hubs, vous pouvez les tester localement si vous utilisez un [environnement de développement Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html). Effectuez les étapes suivantes pour exécuter les topologies localement dans l’environnement de développement :

1. Exécutez le writer :

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. Exécutez le reader :

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local` : exécuter la topologie en mode local (non distribué).
> * `-R /writer.yaml` : charger la définition de la topologie à partir de l’élément `resources` empaqueté dans le fichier jar. Si la topologie est un fichier sur le système de fichiers local, spécifiez-en alors le chemin d’accès (dernier paramètre).
> * `--filter dev.properties` : utiliser le contenu de `dev.properties` pour renseigner les valeurs dans les définitions de topologie. Par exemple, `${eventhub.read.policy.name}`.

La sortie est enregistrée dans la console en cas d’exécution locale. Utilisez la combinaison de touches __Ctrl+C__ pour arrêter la topologie.

## <a name="deploy-the-topologies"></a>Déploiement des topologies

1. Utilisez SCP pour copier le package jar dans votre cluster HDInsight. Remplacez USERNAME par l’utilisateur SSH de votre cluster. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight :

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. Par exemple, `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    Cette commande copie le fichier sur le répertoire de base de votre utilisateur SSH sur le cluster.

2. Une fois le téléchargement du fichier terminé, utilisez SSH pour vous connecter au cluster HDInsight. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Utilisez la commande suivante pour démarrer les topologies :

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote` : envoie la topologie au service Nimbus, qui la démarre sur les nœuds de travail du cluster.

4. Pour afficher les données enregistrées, accédez à l’adresse https://CLUSTERNAME.azurehdinsight.net/stormui, où __CLUSTERNAME__ est le nom de votre cluster HDInsight. Sélectionnez les topologies et explorez-en les composants. Sélectionnez l’entrée de __port__ d’une instance de composant pour afficher les informations enregistrées.

5. Utilisez les commandes suivantes pour arrêter les topologies :

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

