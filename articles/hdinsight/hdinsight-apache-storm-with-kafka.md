---
title: "Utilisation d’Apache Kafka avec Storm sur HDInsight | Microsoft Docs"
description: "Apache Kafka est installé avec Apache Storm sur HDInsight. Apprenez à écrire sur Kafka et lire à partir de celui-ci, et utiliser les composants KafkaBolt et KafkaSpout fournis avec Storm. Découvrez également comment utiliser l’infrastructure Flux pour définir et envoyer des topologies Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: dcda5e27cbcadff054c8085b72a1b6fb1c07b889
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Utilisation d’Apache Kafka (version préliminaire) avec Storm sur HDInsight

Apache Kafka est une solution de messagerie de type publication/abonnement disponible avec HDInsight. Apache Storm est un système distribué qui peut être utilisé pour analyser les données en temps réel. Ce document montre comment vous pouvez utiliser Storm sur HDInsight pour lire et traiter des données à partir de Kafka sur HDInsight. L’exemple de document utilise une topologie Storm basée sur Java qui s’appuie sur les composants spout et bolt de Kafka disponibles avec Apache Storm.

> [!NOTE]
> Les étapes décrites dans ce document créent un groupe de ressources Azure qui contient à la fois un Storm sur HDInsight et un Kafka sur un cluster HDInsight. Ces clusters sont tous deux situés dans un réseau virtuel Azure, ce qui permet au cluster Storm de communiquer directement avec le cluster Kafka.
> 
> Lorsque vous avez terminé les étapes décrites dans ce document, n’oubliez pas de supprimer les clusters pour éviter les frais supplémentaires.

## <a name="prerequisites"></a>Composants requis

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 ou version ultérieure. Ou un équivalent, par exemple, [OpenJDK](http://openjdk.java.net/).
  
    > [!NOTE]
    > Les étapes de ce document utilisent un cluster HDInsight 3.5, qui utilise Java 8.

* [Maven 3.x](http://maven.apache.org/) : un package de gestion des builds des applications Java.

* Un éditeur de texte ou un IDE Java

* Un client SSH (vous avez besoin des commandes `ssh` et `scp`) : pour plus d’informations, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-clusters"></a>Création des clusters

Apache Kafka sur HDInsight ne donne pas accès aux répartiteurs Kafka via l’internet public. Tout ce qui communique avec Kafka doit se trouver sur le même réseau virtuel Azure que les nœuds du cluster Kafka. Pour cet exemple, les clusters Kafka et Storm sont situés dans un réseau virtuel Azure. Le diagramme suivant illustre le flux de communication entre les clusters :

![Diagramme des clusters Storm et Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Bien que Kafka est lui-même limité à la communication au sein du réseau virtuel, d’autres services sur le cluster comme SSH et Ambari sont accessibles sur Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).


Même si vous pouvez créer un réseau virtuel Azure, et des clusters Kafka et Storm manuellement, il est plus facile d’utiliser un modèle Azure Resource Manager. Utilisez les étapes suivantes pour déployer un réseau virtuel Azure et des clusters Kafka et Storm sur votre abonnement Azure.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Le modèle Azure Resource Manager se trouve à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**.

2. Utilisez les instructions suivantes pour remplir les entrées sur le panneau **déploiement personnalisé** :
   
    ![Déploiement HDInsight personnalisé](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un. Ce groupe contient le cluster HDInsight.
   
    * **Emplacement** : choisissez un emplacement proche de vous géographiquement. Cet emplacement doit correspondre à l’emplacement dans la section __PARAMÈTRES__.

    * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Storm et HBase. Par exemple, si vous entrez **hdi**, cela crée un cluster Storm nommé **storm-hdi** et un cluster Kafka nommé **kafka-hdi**.
   
    * **Nom d’utilisateur du cluster** : nom utilisateur Admin pour les clusters Storm et Kafka.
   
    * **Mot de passe du cluster** : mot de passe utilisateur Admin pour les clusters Storm et Kafka.
    
    * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters Storm et Kafka.
    
    * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters Storm et Kafka.
    
    * **Emplacement** : région dans laquelle seront créés les clusters.

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

Une fois les ressources créées, vous êtes redirigé vers un panneau pour le groupe de ressources contenant les clusters et le tableau de bord Web.

![Volet du groupe de ressources pour les clusters et le réseau virtuel](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **storm-BASENAME** et **kafka-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans les étapes ultérieures, lors de la connexion aux clusters.

## <a name="get-the-code"></a>Obtenir le code

Le code de l’exemple décrit dans ce document est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

## <a name="understanding-the-code"></a>Présentation du code

Ce projet contient deux topologies :

* **KafkaWriter** : définie par le fichier **writer.yaml**, cette topologie écrit des phrases aléatoires dans Kafka en utilisant le KafkaBolt fourni avec Apache Storm.
  
    Cette topologie utilise un composant **SentenceSpout** personnalisé pour générer des phrases aléatoires.

* **KafkaReader** : définie par le fichier **reader.yaml**, cette topologie lit les données à partir de Kafka à l’aide du KafkaSpout fourni avec Apache Storm, puis écrit les données dans stdout.
  
    Cette topologie utilise un composant **PrinterBolt** personnalisé pour enregistrer des données lues à partir de Kafka.

### <a name="flux"></a>Flux

Les topologies sont définies à l’aide de [Flux](https://storm.apache.org/releases/1.0.1/flux.html). Flux a été introduit dans Storm 0.10.x et vous permet de séparer la configuration de topologie du code. Pour les topologies qui utilisent l’infrastructure Flux, la topologie est définie dans un fichier YAML. Le fichier YAML peut être inclus dans le cadre de la topologie, ou peut être spécifié lorsque vous envoyez la topologie au serveur Storm. Flux prend également en charge la substitution des variables au moment de l’exécution, ce qui est utilisé dans cet exemple.

Les deux topologies attendent les variables d’environnement suivantes :

* **KAFKATOPIC** : le nom de la rubrique Kafka que les topologies utilisent pour la lecture/écriture.

* **KAFKABROKERS** : les hôtes sur lesquels les répartiteurs Kafka s’exécutent. Les informations sur les répartiteurs sont utilisées par le KafkaBolt lors de l’écriture sur Kafka.

* **KAFKAZKHOSTS** : les hôtes sur lesquels Zookeeper s’exécute.

Les étapes décrites dans ce document montrent comment définir ces variables d’environnement.

## <a name="create-a-kafka-topic"></a>Création d’une rubrique Kafka

1. Connectez-vous au cluster Kafka à l'aide de SSH. Remplacez `USERNAME` par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez `BASENAME` par le nom de base que vous avez utilisé lors de la création du cluster.
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    À l'invite, saisissez le mot de passe que vous avez utilisé lors de la création du cluster.
   
    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. À partir de la connexion SSH au cluster Kafka, utilisez les commandes suivantes pour définir des variables pour la connexion HTTP et le nom du cluster. Ces valeurs sont utilisées dans les autres étapes de cette section.

  ```bash
  ADMIN='admin' #replace with the name of the admin account for the cluster
  PASSWORD='password' #replace with the password for the admin account
  ```

3. Utilisez les commandes suivantes pour installer l’utilitaire `jq`, récupérer le nom du cluster et définir la variable `KAFKAZKHOSTS` :

  ```bash
  sudo apt -y install jq
  CLUSTERNAME=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name'`
  KAFKAZKHOSTS=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
  ```

    Utilisez la commande suivante pour récupérer le nom du cluster :

  ```bash
  echo $CLUSTERNAME
  ```

    Le résultat de la commande ressemble à l’exemple suivant :

  ```bash
  kafka-myhdi
  ```

    Utilisez la commande suivante pour vérifier que `KAFKAZKHOSTS` est défini correctement :

  ```bash
  echo $KAFKAZKHOSTS
  ```

    Le résultat de la commande ressemble à l’exemple suivant :

  ```bash
  zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
  ```

    Enregistrez le nom de cluster Kafka et les informations d’hôte Zookeeper, car ces valeurs sont utilisées lors du démarrage de la topologie sur le cluster Storm.

    > [!NOTE]
    > Les commandes précédentes utilisent __http://headnodehost:8080/__, qui se connecte directement à Ambari. Si vous avez besoin de récupérer ces informations à partir de l’extérieur du cluster, sur Internet, vous devez utiliser __https://kafka-BASENAME.azurehdinsight.net/__ à la place.

4. Pour créer une rubrique dans Kafka, utilisez la commande suivante :

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
  ```

    Cette commande se connecte à Zookeeper en utilisant les informations d’hôte stockées dans `$KAFKAZKHOSTS`, puis crée une rubrique Kafka nommée **stormtest**. Vous pouvez vérifier que la rubrique a été créée à l’aide de la commande suivante pour répertorier les rubriques :

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
  ```

    La sortie de cette commande répertorie les rubriques Kafka, qui doivent contenir la nouvelle rubrique **stormtest**.

Laissez la connexion SSH au cluster Kafka active, vous pouvez l’utiliser pour vérifier que la topologie Storm écrit des messages dans la rubrique.

## <a name="download-and-compile-the-project"></a>Téléchargez et compilez le projet

1. Dans votre environnement de développement, téléchargez le projet sur [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), ouvrez une interface de ligne de commande et accédez à l’emplacement auquel vous avez téléchargé le projet.

    Prenez quelques instants pour observer le code et comprendre comment fonctionne le projet.

2. À partir du répertoire **hdinsight-storm-java-kafka**, utilisez la commande suivante pour compiler le projet et créer un package de déploiement :

  ```bash
  mvn clean package
  ```

    Le processus de création de package crée un fichier nommé `KafkaTopology-1.0-SNAPSHOT.jar` dans le répertoire `target`.

3. Utilisez la commande suivante pour copier le package sur votre Storm dans votre cluster HDInsight. Remplacez **USERNAME** par le nom d’utilisateur SSH pour le cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    À l'invite, saisissez le mot de passe que vous avez utilisé lors de la création du cluster.

4. Utilisez la commande suivante pour copier le fichier `set-env-variables.sh` à partir du répertoire `scripts` du projet dans le cluster Storm :

  ```bash
  scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
  ```

    Ce script est utilisé pour définir les variables d’environnement que les topologies Storm utilisent pour communiquer avec le cluster Kafka.

## <a name="start-the-writer"></a>Démarrer l’enregistreur

1. Utilisez ce qui suit pour vous connecter au cluster Storm à l’aide de SSH. Remplacez **USERNAME** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    À l'invite, saisissez le mot de passe que vous avez utilisé lors de la création du cluster.
   
    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. À partir de la connexion SSH avec le cluster Store, utilisez la commande suivante pour exécuter le script `set-env-variables.sh` :

  ```bash
  chmod +x set-env-variables.sh
  . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD
  ```

    Remplacez __KAFKACLUSTERNAME__ par le nom du cluster Kafka. Remplacez __PASSWORD__ par le mot de passe de connexion de l’administrateur du cluster Kafka.

    Le script se connecte au cluster Kafka et récupère une liste des répartiteurs Kafka et hôtes Zookeeper. Les informations sont ensuite stockées dans des variables d’environnement qui sont utilisées par les topologies Storm.

    Le résultat du script ressemble à l’exemple suivant :

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Dans la connexion SSH sur le cluster Storm, utilisez la commande suivante pour démarrer la topologie d’enregistreur :

        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e

    Les paramètres utilisés avec cette commande sont les suivants :

    * `org.apache.storm.flux.Flux` : utilisez Flux pour configurer et exécuter cette topologie.

    * `--remote` : envoyez la topologie à Nimbus. La topologie est distribuée sur les nœuds de travail dans le cluster.

    * `-R /writer.yaml` : utilisez le fichier `writer.yaml` pour configurer la topologie. `-R` indique que cette ressource est incluse dans le fichier jar. Il se trouve à la racine du fichier jar, donc `/writer.yaml` est le chemin d’accès à celui-ci.

    * `-e` : utilisez la substitution de variable d’environnement. Flux récupère les valeurs $KAFKABROKERS et $KAFKATOPIC précédemment définies et les utilise dans le fichier reader.yaml à la place des entrées `${ENV-KAFKABROKER}` et `${ENV-KAFKATOPIC}`.

5. Une fois la topologie démarrée, basculez vers la connexion SSH au cluster Kafka et utilisez la commande suivante pour afficher les messages écrits dans la rubrique **stormtest** :

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
  ```

    Cette commande utilise un script fourni avec Kafka pour surveiller la rubrique. Après quelques instants, elle doit commencer à retourner des phrases aléatoires qui ont été écrites dans la rubrique. Le résultat ressemble à l’exemple suivant :

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Pour arrêter le script, utilisez les touches Ctrl + C.

## <a name="start-the-reader"></a>Démarrer le lecteur

1. Dans la session SSH sur le cluster Storm, utilisez la commande suivante pour démarrer la topologie de lecteur :

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. Une fois que la topologie commence, ouvrez l’interface utilisateur Storm. Cette interface utilisateur web est située dans https://storm-BASENAME.azurehdinsight.net/stormui. Remplacez __BASENAME__ par le nom de base que vous avez utilisé lors de la création du cluster. 

    Lorsque vous y êtes invité, utilisez le nom d’utilisateur administrateur (par défaut `admin`) et le mot de passe utilisés lors de la création du cluster. Vous verrez une page web similaire à l’image suivante :

    ![Interface utilisateur de Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Dans l’interface utilisateur de Storm, sélectionnez le lien __kafka-lecteur__ dans la section __Résumé de la topologie__ pour afficher des informations sur la topologie __kafka-reader__.

    ![Section Résumé de topologie l'interface utilisateur web de Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Sélectionnez le lien __logger-bolt__ dans la section __Bolts (All time)__ pour afficher des informations sur les instances du composant logger-bolt.

    ![Lien logger-bolt dans la section bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. Dans la section __Executors__, sélectionnez un lien dans la colonne __Port__ pour afficher des informations de journalisation sur cette instance du composant.

    ![Exécuteurs de lien](./media/hdinsight-apache-storm-with-kafka/executors.png)

    Le journal contient un journal des données lues à partir de la rubrique Kafka. Les informations dans le journal sont similaires à ce qui suit :

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Arrêt des topologies

Dans une session SSH sur le cluster Storm, utilisez la commande suivante pour arrêter les topologies Storm :

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Étant donné que les étapes décrites dans ce document créent deux clusters dans le même groupe de ressources Azure, vous pouvez supprimer le groupe de ressources du portail Azure. Cette opération supprime toutes les ressources créées par la suite dans ce document, le réseau virtuel Azure et le compte de stockage utilisé par les clusters.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples de topologies qui peuvent être utilisées avec Storm sur HDInsight, consultez [Exemples de topologies et composants Storm](hdinsight-storm-example-topology.md).

Pour plus d’informations sur le déploiement et la surveillance des topologies sur HDInsight Linux, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md)


