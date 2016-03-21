<properties
   pageTitle="Traitement des événements de Event Hubs avec Storm sur HDInsight avec Java | Azure"
   description="Découvrez comment traiter les données Event Hubs avec une topologie Storm basée sur Java créée avec Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/01/2016"
   ms.author="larryfr"/>

# Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)

Azure Event Hubs permet de traiter d’énormes quantités de données provenant de sites web, d’applications et d’appareils. Le spout Event Hubs simplifie l’utilisation d’Apache Storm sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données dans les hubs d’événements à partir de Storm à l’aide du bolt des hubs d’événements.

Dans ce didacticiel, vous allez apprendre à utiliser le spout et le bolt Event Hubs pour lire et écrire des données dans une topologie Storm basée sur Java.

## Configuration requise

* Un cluster Apache Storm sur HDInsight. Utilisez l’un des articles de prise en main suivants pour créer un cluster :

    - Un [cluster Linux](hdinsight-apache-storm-tutorial-get-started-linux.md) : sélectionnez cette option si vous souhaitez utiliser le protocole SSH pour travailler avec le cluster à partir des clients Linux, Unix, OS X ou Windows

    - Un [cluster Windows](hdinsight-apache-storm-tutorial-get-started.md) : sélectionnez cette option si vous souhaitez utiliser PowerShell pour travailler avec le cluster à partir d’un client Windows

    > [AZURE.NOTE] Seule différence entre les deux types de cluster : utilisation du protocole SSH pour envoyer la topologie au cluster ou utilisation d’un formulaire web.

* Un [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Kit de développeur Java (JDK) Oracle version 7 ](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou un équivalent, par exemple [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi) : Maven est un système de génération de projets pour les projets Java

* Un éditeur de texte ou un environnement de développement intégré Java (IDE)

	> [AZURE.NOTE] Votre éditeur ou IDE peut avoir des fonctionnalités spécifiques pour l’utilisation avec Maven, qui ne sont pas traitées dans ce document. Pour plus d’informations sur les capacités de votre environnement d’édition, consultez la documentation du produit que vous utilisez.

 * Un client SSH. Consultez l’un des articles suivants pour plus d’informations sur l’utilisation du protocole SSH avec HDInsight :

    - [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Un client SCP. Il est fourni avec tous les systèmes Linux, Unix et OS X. Pour les clients Windows, nous vous recommandons PSCP, qui est disponible à partir de la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##Vue d’ensemble de l’exemple

L’exemple [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contient deux topologies :

__com.microsoft.example.EventHubWriter__ écrit des données aléatoires dans un Azure Event Hub. Les données sont générées par un spout et comprennent un ID d’appareil aléatoire et une valeur d’appareil. Ainsi, elles simulent certains matériels qui émettent un ID de chaîne et une valeur numérique.

__com.microsoft.example.EventHubReader__ lit les données à partir de l’Event Hub (les données écrites par EventHubWriter) et les stocke dans HDFS (WASB, dans le cas présent, étant donné qu’elles ont été écrites et testées avec Azure HDInsight) dans le répertoire /devicedata.

Les données sont formatées sous forme de document JSON avant d’être écrites dans l’Event Hub ; lors de la lecture par le lecteur, elles sont analysées depuis JSON vers des tuples. Le format JSON est le suivant :

    { "deviceId": "unique identifier", "deviceValue": some value }

Utiliser un document JSON pour stocker les données dans l’Event Hub permet de savoir quel est le format, au lieu de compter sur les mécanismes internes de mise en forme du spot et du bolt de l’Event Hub.

###Configuration du projet

Le fichier **POM.xml** contient des informations de configuration pour ce projet Maven. Éléments intéressants :

####Dépendance du spout Storm EventHubs

    <dependency>
      <groupId>com.microsoft.eventhubs</groupId>
      <artifactId>eventhubs-storm-spout</artifactId>
      <version>0.9.3</version>
    </dependency>

Cela ajoute une dépendance pour le package eventhubs-storm-spout, qui contient à la fois un spout pour la lecture depuis Event Hubs et un bolt pour l’écriture dans Event Hubs.

> [AZURE.NOTE] Ce package n’est pas disponible sur Maven ; il sera installé manuellement dans votre référentiel Maven local dans une étape ultérieure.

####Composants HdfsBolt et WASB

HdfsBolt est normalement utilisé pour stocker des données dans HDFS (Hadoop Distributed File System, système de fichiers DFS Hadoop). Toutefois, les clusters HDInsight utilisent le stockage Azure (WASB) comme magasin de données par défaut. Nous devons donc charger plusieurs composants qui permettent à HdfsBolt de comprendre le système de fichiers WASB.

      <!--HdfsBolt stuff -->
      <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
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
        <version>0.9.3</version>
      </dependency>
      <!--
     This is a temporary workaround to make HdfsBolt work with WASB through hadoop-azure project.
     For now, we have to build hadoop-client, hadoop-hdfs and hadoop-azure from Hadoop trunk
     (which defaults to 3.0.0-SNAPSHOT version). And push those jars and dependencies to local
     mvn repo (take a look at push_lib_mvn.ps1).

     Once Hadoop 2.7 is released, we can just switch to that version.
     Note that hadoop-azure is added to Hadoop on Hadoop 2.7.
     -->
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-client</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-hdfs</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-azure</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-common</artifactId>
       <version>3.0.0-SNAPSHOT</version>
       <exclusions>
         <exclusion>
           <groupId>org.slf4j</groupId>
           <artifactId>slf4j-log4j12</artifactId>
         </exclusion>
       </exclusions>
     </dependency>
     <dependency>
       <groupId>com.microsoft.windowsazure.storage</groupId>
       <artifactId>microsoft-windowsazure-storage-sdk</artifactId>
       <version>0.6.0</version>
     </dependency>

> [AZURE.NOTE] Les packages permettant d’activer WASB ne sont pas disponibles sur le référentiel Maven et seront installés manuellement dans une étape ultérieure.

####maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Il indique à Maven que le projet doit être compilé avec la compatibilité pour Java 7, utilisée par les clusters HDInsight.

####maven-shade-plugin

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <!-- Keep us from getting a can't overwrite file error -->
          <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
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

Il est utilisé pour créer un package de la solution dans un fichier uber jar qui contient à la fois le code de projet et les dépendances requises. Il est également utilisé pour :

* Renommer les fichiers de licence pour les dépendances : si ce n’est pas fait, cela peut entraîner une erreur lors de l’exécution sur les clusters HDInsight Windows.

* Exclure la sécurité/les signatures : si ce n’est pas fait, cela peut entraîner une erreur lors de l’exécution sur le cluster HDInsight.

####exec-maven-plugin

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

Il vous permet d’exécuter la topologie localement sur votre environnement de développement en utilisant la commande suivante :

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Par exemple, `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####Section Ressources

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Elle définit les ressources requises pour le projet :

- **EventHubs.properties** : contient des informations utilisées pour se connecter à un Azure Event Hub
- **core-site.xml** : contient des informations sur le stockage Azure utilisé par le cluster HDInsight.

Vous devez remplir ces deux valeurs avec des informations concernant votre Event Hub et le cluster HDInsight.

##Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA\_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`

* **PATH** :doit contenir les chemins d’accès suivants :

	* **JAVA\_HOME** (ou le chemin d’accès équivalent)

	* **JAVA\_HOME\\bin** (ou le chemin d’accès équivalent)

	* Le répertoire d’installation de Maven

## Configuration du hub d'événements

Event Hubs est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau hub d'événements.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **NOUVEAU** > **Service Bus** > **Event Hub** > **Création personnalisée**.

2. Sur l’écran **Ajouter un nouveau hub d’événements**, entrez un **nom de hub d’événements**, sélectionnez la **région** dans laquelle créer le hub, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la **flèche** pour continuer.

	![page 1 de l’assistant](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que celui de votre serveur Storm sur HDInsight pour réduire la latence et les coûts.

2. Sur l’écran **Configurer un hub d’événements**, entrez les valeurs pour **Nombre de partitions** et **Conservation des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous en aurez besoin ultérieurement.

	![page 2 de l’assistant](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Une fois le hub d’événements créé, sélectionnez l’espace de noms, puis le **hub d’événements**. Enfin, sélectionnez le hub d’événements que vous avez créé.

4. Sélectionnez **Configurer**, puis créez deux nouvelles stratégies d’accès en utilisant les informations suivantes.

	<table>
	<tr><th>Nom</th><th>Autorisations</th></tr>
	<tr><td>Writer</td><td>Envoyer</td></tr>
	<tr><td>Lecteur</td><td>Écouter</td></tr>
	</table>

	Après avoir créé les autorisations, sélectionnez l’icône **Enregistrer** située en bas de page. Cela crée les stratégies d’accès partagé qui seront utilisées pour envoyer (writer) et écouter (reader) ce hub d’événements.

	![stratégies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Après avoir enregistré vos stratégies, utilisez le **Générateur de clés d’accès partagé** situé en bas de page pour récupérer les clés des stratégies **writer** et **reader**. Enregistrez-les, car elles seront utilisées plus tard.

## Télécharger et générer le projet

1. Téléchargez le projet à partir de GitHub : [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Vous pouvez télécharger le package dans une archive .zip ou utiliser [git](https://git-scm.com/) pour cloner le projet localement.

2. Utilisez les commandes suivantes pour installer les packages inclus dans le projet dans votre référentiel Maven local. Ces commandes activent le spout et le bolt du hub d’événements, ainsi que la possibilité d’utiliser HdfsBolt pour écrire dans le stockage Azure (WASB).

		mvn -q install:install-file -Dfile=lib/eventhubs/eventhubs-storm-spout-0.9.3-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9.3 -Dpackaging=jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-azure-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-client-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-hdfs-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.jar -DpomFile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom

	> [AZURE.NOTE] Si vous utilisez Powershell, vous devez peut-être mettre les paramètres `-D` entre guillemets. Par exemple : `"-Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom"`.

	À l’origine, ces fichiers proviennent de https://github.com/hdinsight/hdinsight-storm-examples, vous pouvez donc rechercher les dernières versions à cet emplacement.

3. Pour générer le projet et créer un package, utilisez la commande suivante :

        mvn package

    Cela télécharge les dépendances requises, génère le projet, puis crée un package. Le résultat est stocké dans le répertoire __/target__ dans un fichier __EventHubExample-1.0-SNAPSHOT.jar__.

## Déploiement des topologies

Le fichier jar créé par ce projet contient deux topologies : __com.microsoft.example.EventHubWriter__ et __com.microsoft.example.EventHubReader__. La topologie EventHubWriter doit être démarrée en premier, car elle écrit des événements dans l’Event Hub qui sont ensuite lus par EventHubReader.

###Si vous utilisez un cluster Linux

1. Utilisez SCP pour copier le package jar dans votre cluster HDInsight. Remplacez USERNAME par l’utilisateur SSH de votre cluster. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight :

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si vous avez utilisé un mot de passe pour votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. Par exemple : `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE] Si votre client est une station de travail Windows, vous ne disposez peut-être pas d’une commande SCP installée. Nous vous recommandons PSCP, que vous pouvez télécharger à partir de la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Cette commande copie le fichier sur le répertoire de base de votre utilisateur SSH sur le cluster.

1. Une fois le téléchargement du fichier terminé, utilisez SSH pour vous connecter au cluster HDInsight. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Si vous utilisez PuTTY, entrez `CLUSTERNAME-ssh.azurehdinsight.net` dans le champ __Nom d’hôte (ou Adresse IP)__, puis cliquez sur __Ouvrir__ pour vous connecter. Vous serez invité à entrer le nom de votre compte SSH.

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être suivre les étapes ci-après pour sélectionner la clé :
    >
    > 1. Dans **Catégorie**, développez **Connexion**, puis **SSH** et sélectionnez **Auth**.
    > 2. Cliquez sur **Parcourir**, puis sélectionnez le fichier .ppk qui contient votre clé privée.
    > 3. Cliquez sur __Ouvrir__ pour vous connecter.

2. Utilisez la commande suivante pour démarrer les topologies :

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Cela démarre les topologies et leur affecte un nom convivial de « reader » et de « writer ».

3. Patientez quelques minutes pour permettre aux topologies d’écrire et de lire des événements à partir de l’Event Hub, puis utilisez la commande suivante pour vérifier qu’EventHubReader stocke des données dans votre stockage HDInsight :

        hadoop fs -ls /devicedata

    Vous devez recevoir une liste de fichiers ayant l’aspect suivant :

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] Certains fichiers peuvent indiquer une taille de 0, étant donné qu’ils ont été créés par EventHubReader mais qu’ils ne stockent pas encore de données.

    Vous pouvez afficher le contenu de ces fichiers à l’aide de la commande suivante :

        hadoop fs -text /devicedata/*.txt

    Cette commande renvoie des données semblables aux suivantes :

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    La première colonne contient la valeur de l’ID d’appareil et la deuxième colonne, la valeur de l’appareil.

4. Utilisez les commandes suivantes pour arrêter les topologies :

        storm kill reader
        storm kill writer

###Si vous utilisez un cluster Windows

1. Dans votre navigateur, ouvrez https://CLUSTERNAME.azurehdinsight.net. Lorsque vous y êtes invité, entrez les informations d’identification de l’administrateur de votre cluster HDInsight. Vous arrivez dans le tableau de bord Storm.

2. Utilisez le menu déroulant __Fichier jar__ pour rechercher et sélectionner le fichier EventHubExample-1.0-SNAPSHOT.jar dans votre environnement de génération.

3. Dans le champ __Nom de la classe__, entrez `com.mirosoft.example.EventHubWriter`.

4. Dans le champ __Paramètres supplémentaires__, entrez `writer`. Enfin, cliquez sur __Envoyer__ pour télécharger le fichier jar et démarrer la topologie EventHubWriter.

5. Une fois que la topologie a démarré, utilisez le formulaire pour démarrer EventHubReader :

    * __Fichier jar__ : sélectionnez le fichier EventHubExample-1.0-SNAPSHOT.jar téléchargé précédemment
    * __Nom de la classe__ : entrez `com.microsoft.example.EventHubReader`
    * __Paramètres supplémentaires__ : entrez `reader`

    Cliquez sur Envoyer pour démarrer la topologie EventHubReader.

6. Patientez quelques minutes pour permettre aux topologies de générer des événements, puis de les stocker dans le stockage Azure. Ensuite, sélectionnez l’onglet __Console de requête__ en haut de la page __Tableau de bord Storm__.

7. Sur la __Console de requête__, sélectionnez __Éditeur Hive__ et remplacez la valeur par défaut `select * from hivesampletable` par le code suivant :

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasb:///devicedata/';
        select * from devicedata limit 10;

    Cliquez sur __Sélectionner__ pour exécuter la requête. Cela renvoie 10 lignes des données écrites vers le stockage Azure (WASB) par EventHubReader. Une fois la requête terminée, des données ayant l’aspect suivant doivent s’afficher :

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Sélectionnez le __Tableau de bord Storm__ en haut de la page, puis sélectionnez __Interface utilisateur de Storm__. À partir de l’__interface utilisateur de Storm__, sélectionnez le lien pour la topologie de __reader__, puis utilisez le bouton __Arrêter__ pour arrêter la topologie. Répétez le processus pour la topologie de __writer__.



### Points de contrôle

EventHubSpout contrôle régulièrement son état au nœud Zookeeper, qui enregistre l’offset actuel des messages lus depuis la file d’attente. Il permet au composant de commencer à recevoir des messages à l’offset enregistré dans les scénarios suivants :

* Échec et redémarrage de l’instance du composant.

* Agrandissement ou réduction du cluster en ajoutant ou supprimant des nœuds.

* Arrêt et redémarrage de la topologie **sous le même nom**.

####Sur les clusters HDInsight Windows

Vous pouvez exporter et importer les points de contrôle persistants vers le WASB (le stockage Azure utilisé par votre cluster HDInsight). Les scripts qui permettent de le faire sont situés sur le cluster Storm sur HDInsight, dans **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE] Le numéro de version dans le chemin d’accès peut être différent, car la version de Storm installée sur le cluster peut changer dans le futur.

Les scripts présents dans ce répertoire sont :

* **stormmeta\_import.cmd** : pour importer toutes les métadonnées Storm du conteneur de stockage de clusters par défaut dans Zookeeper.

* **stormmeta\_export.cmd** : pour exporter toutes les métadonnées Storm de Zookeeper vers le conteneur de stockage de clusters par défaut.

* **stormmeta\_delete.cmd** : pour supprimer toutes les métadonnées Storm de Zookeeper.

L’exportation d’une importation vous permet de conserver les données du point de contrôle lorsque vous devez supprimer le cluster, mais que vous souhaitez reprendre le traitement à partir de l’offset actuel dans le hub lorsque vous remettez un nouveau cluster en ligne.

> [AZURE.NOTE] Dans la mesure où les données sont conservées dans le conteneur de stockage par défaut, le nouveau cluster **doit** utiliser le même compte de stockage et le même conteneur que le cluster précédent.

## Supprimer votre cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Résolution des problèmes

Si vous ne voyez pas les fichiers stockés à l’emplacement /devicedata (à l’aide de la commande `hadoop fs -ls /devicedata` ou de la commande Hive dans la Console de requête), utilisez l’interface utilisateur de Storm pour rechercher les erreurs renvoyées par les topologies.

Pour plus d’informations sur l’utilisation de l’interface utilisateur de Storm, consultez les rubriques suivantes :

* Si vous utilisez un cluster HDInsight Storm __Linux__, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Si vous utilisez un cluster HDInsight Storm __Windows__, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##Étapes suivantes

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

<!------HONumber=AcomDC_0309_2016-->