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
ms.date: 04/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: e650731c3186b47adeb0e799a852961c30338550
ms.lasthandoff: 04/12/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)

Découvrez comment utiliser Azure Event Hubs avec Storm sur HDInsight. Cet exemple utilise des composants basés sur Java pour lire et écrire des données dans Azure Event Hubs.

Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hub simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

## <a name="prerequisites"></a>Composants requis

* Un cluster Apache Storm sur HDInsight version 3.5. Pour plus d’informations, voir [Prise en main de Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Dépréciation d’HDInsight 3.3 et 3.4](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Un [hub d’événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Kit de développeur Java (JDK) Oracle version 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou un équivalent, par exemple, [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi) : Maven est un système de génération de projet pour les projets Java.

* Un éditeur de texte ou un environnement de développement intégré (IDE).

    > [!NOTE]
    > Votre éditeur ou IDE peut avoir des fonctionnalités spécifiques pour l’utilisation avec Maven, qui ne sont pas traitées dans ce document. Pour plus d’informations sur les capacités de votre environnement d’édition, consultez la documentation du produit que vous utilisez.

    * Un client SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un client SCP. La commande `scp` est fournie avec tous les systèmes Linux, Unix et OS X (y compris Bash sur Windows 10.) Concernant les systèmes Windows non équipés de la commande `scp`, nous recommandons d’utiliser PSCP. PSCP est disponible à partir de la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="understanding-the-example"></a>Vue d’ensemble de l’exemple

L’exemple [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contient deux topologies :

**com.microsoft.example.EventHubWriter** écrit des données aléatoires dans un Azure Event Hub. Les données sont générées par un spout et comprennent un ID d’appareil aléatoire et une valeur d’appareil. Ainsi, elles simulent certains matériels qui émettent un ID de chaîne et une valeur numérique.

**com.microsoft.example.EventHubReader** lit les données à partir de l’Event Hub et les stocke dans le stockage par défaut des clusters, dans le répertoire /devicedata.

Les données sont formatées sous forme de document JSON avant d’être écrites dans l’Event Hub ; lors de la lecture par le lecteur, elles sont analysées depuis JSON vers des tuples. Le format JSON est le suivant :

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configuration du projet

Le fichier `POM.xml` contient des informations de configuration pour ce projet Maven. Éléments intéressants :

#### <a name="hortonworks-repository"></a>Référentiel Hortonworks

HDInsight est basée sur Hortonworks Data Platform. Pour que votre projet soit compatible avec les versions de Storm et d’Hadoop utilisées avec HDInsight 3.5, la section suivante configure le projet pour utiliser des exécutables d’Hortonworks :

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>Dépendance du spout Storm EventHubs

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Ce fichier XML définit une dépendance pour le package eventhubs. Il contient à la fois un spout pour la lecture à partir d’Event Hubs et un bolt pour l’écriture dans Event Hubs.

> [!NOTE]
> Nous abordons l’installation de ce package ultérieurement dans ce document.

#### <a name="the-hdfsbolt-and-wasb-components"></a>Composants HdfsBolt et WASB

HdfsBolt est normalement utilisé pour stocker des données dans HDFS (Hadoop Distributed File System, système de fichiers DFS Hadoop). Toutefois, les clusters HDInsight utilisent le stockage Azure (WASB) comme magasin de données par défaut. Nous devons donc charger plusieurs composants qui permettent à HdfsBolt de comprendre le système de fichiers WASB.

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <!-- exclude these storm-hdfs dependencies since they are on the server -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
        </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> Lorsque vous travaillez avec une version antérieure de HDInsight, telles que la version 3.2, vous devez enregistrer manuellement ces composants. Pour un exemple, voir la branche [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) du référentiel fourni à titre d’exemple.

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Ce paramètre configure le projet de sorte à générer un résultat pour Java 8, résultat qui est ensuite utilisé par HDInsight 3.5.

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
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
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

Ce paramètre configure la solution pour empaqueter la sortie dans une sorte de super fichier jar. Ce fichier jar contient le code de projet et les dépendances requises. Il est également utilisé pour :

* Renommer les fichiers de licence pour les dépendances.
* Exclure les sécurités/signatures.
* Assurez-vous que plusieurs implémentations de la même interface soient fusionnées dans une entrée.

Ces paramètres de configuration évitent les erreurs au moment de l’exécution.

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

Cette configuration se révèle utile, car elle permet d’exécuter plus facilement la topologie en local, dans votre environnement de développement. Exécutez la topologie en local en utilisant la syntaxe suivante :

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Par exemple, `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>Section Ressources

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

Cette configuration définit les ressources non Java qui figurent dans le projet. Par exemple, le fichier **EventHubs.properties** contient des informations utilisées pour se connecter à Azure Event Hub.

## <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`
* **PATH** :doit contenir les chemins d’accès suivants :

  * **JAVA_HOME** (ou le chemin d’accès équivalent)
  * **JAVA_HOME\bin** (ou le chemin d’accès équivalent)
  * Le répertoire d’installation de Maven

## <a name="download-and-register-the-eventhub-components"></a>Téléchargement et inscription des composants Event Hub

1. Téléchargez `storm-eventhubs-1.0.2-jar-with-dependencies.jar` depuis [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar). Ce fichier contient un composant spout et bolt pour lire et écrire à partir de Event Hubs.

2. Utilisez la commande suivante pour inscrire les composants dans votre référentiel maven local :

        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar

    Modifiez le paramètre `-Dfile=` pour pointer vers l’emplacement du fichier téléchargé.

    Cette commande installe le fichier dans le référentiel Maven local, où il est accessible au moment de la compilation par Maven.

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

2. Pour générer le projet et créer un package, utilisez la commande suivante :

        mvn package

    Cette commande télécharge les dépendances requises, génère le projet, puis crée le package. Le résultat est stocké dans le répertoire **/target** dans un fichier **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="deploy-the-topologies"></a>Déploiement des topologies

Le fichier jar créé par ce projet contient deux topologies : **com.microsoft.example.EventHubWriter** et **com.microsoft.example.EventHubReader**. La topologie EventHubWriter doit être démarrée en premier, car elle écrit des événements dans l’Event Hub qui sont ensuite lus par EventHubReader.

1. Utilisez SCP pour copier le package jar dans votre cluster HDInsight. Remplacez USERNAME par l’utilisateur SSH de votre cluster. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight :

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. Par exemple, `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    Cette commande copie le fichier sur le répertoire de base de votre utilisateur SSH sur le cluster.

2. Une fois le téléchargement du fichier terminé, utilisez SSH pour vous connecter au cluster HDInsight. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Utilisez la commande suivante pour démarrer les topologies :

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Ces commandes démarrent les topologies en utilisant les noms conviviaux de « reader » et « writer ».

4. Patientez une minute le temps que les topologies génèrent les données. Utilisez la commande suivante pour vérifier que les données sont écrites dans le stockage HDInsight :

        hdfs dfs fs -ls /devicedata

    Cette commande renvoie une liste de fichiers qui ressemble à celle décrite ici :

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

   > [!NOTE]
   > Certains fichiers peuvent indiquer une taille de 0, étant donné qu’ils ont été créés par EventHubReader mais qu’ils ne stockent pas encore de données.

    Vous pouvez afficher le contenu de ces fichiers à l’aide de la commande suivante :

        hdfs dfs -text /devicedata/*.txt

    Cette commande renvoie des données semblables à ce qui suit :

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    La première colonne contient la valeur de l’ID d’appareil et la deuxième colonne, la valeur de l’appareil.

5. Utilisez les commandes suivantes pour arrêter les topologies :

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne voyez pas les fichiers dans le répertoire /devicedata, utilisez l’interface utilisateur Storm pour rechercher les éventuelles erreurs renvoyées par les topologies.

Pour plus d’informations sur l’utilisation de l’interface utilisateur de Storm, consultez les rubriques suivantes :

* Si vous utilisez un cluster HDInsight Storm **Linux** , consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Si vous utilisez un cluster HDInsight Storm **Windows** , consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>Étapes suivantes

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

