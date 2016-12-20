---
title: "Prise en main d’Apache Kafka sur HDInsight | Microsoft Docs"
description: "Découvrez les principes de base de la création et de l’utilisation de Kafka sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/09/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 70d910f61fa414a0c58a22057133ef3b899dac05

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Prise en main d’Apache Kafka (version préliminaire) sur HDInsight

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source, disponible avec HDInsight. Elle est souvent utilisée comme répartiteur de messages, car elle fournit des fonctionnalités similaires à une file d’attente de messages de publication/d’abonnement. Dans ce document, vous allez apprendre à créer un Kafka sur un cluster HDInsight, puis à envoyer et à recevoir des données à partir d’une application Java.

> [!NOTE]
> Il existe actuellement deux versions de Kafka disponibles avec HDInsight ; 0.9.0 (HDInsight 3.4) et 0.10.0 (HDInsight 3.5). Les étapes décrites dans ce document supposent que vous utilisez Kafka sur HDInsight 3.5.

## <a name="prerequisite"></a>Configuration requise

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vous devez disposer de ce qui suit pour suivre jusqu’au bout ce didacticiel Storm Apache :

* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Des connaissances en SSH et SCP**. Pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez les documents suivants :
  
   * **Clients Linux, Unix ou OS X**: consultez la page [Utilisation de SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   
   * **Clients Windows**: consultez la page [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html), ou un équivalent, par exemple, OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Création d’un cluster Kafka

Utilisez les étapes suivantes pour créer un Kafka sur un cluster HDInsight :

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ et Nouveau**, **Intelligence et analyse**, puis **HDInsight**.
   
    ![Création d'un cluster HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. À partir du panneau **Nouveau cluster HDInsight**, entrez un **nom de cluster** et sélectionnez **l’abonnement** à utiliser avec ce cluster.
   
    ![Sélectionnez un abonnement](./media/hdinsight-apache-kafka-get-started/new-hdinsight-cluster-blade.png)

3. Utilisez **Sélectionner le type de cluster** et sélectionnez les valeurs suivantes sur le panneau **Configuration du type cluster** :
   
    * **Type de cluster** : Kafka

    * **Version** : Kafka 0.10.0 (HDI 3.5)

    * **Niveau cluster** : standard
     
    Enfin, utilisez le bouton **Sélectionner** pour enregistrer les paramètres.
     
    ![Sélectionner un type de cluster](./media/hdinsight-apache-kafka-get-started/cluster-type.png)

4. Utilisez les **informations d’identification** pour configurer la connexion au cluster et les informations d’identification de l’utilisateur SSH.  Utilisez le bouton **Sélectionner** pour enregistrer les paramètres.
   
    > [!NOTE]
    > La connexion au cluster est utilisée lors de l’accès au cluster sur Internet à l’aide de HTTPS. L’utilisateur SSH est utilisé pour se connecter au cluster et exécuter des commandes de manière interactive.
   
    ![Configurer la connexion du cluster](./media/hdinsight-apache-kafka-get-started/cluster-credentials.png)
   
    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants :
   
    * [Utilisation de SSH avec HDInsight à partir d’un client Linux, Unix ou MacOS](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [Utilisation de SSH avec HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

5. Utilisez **Source de données** pour configurer la banque de données principale pour le cluster. À partir du panneau **Source de données**, utilisez les informations suivantes pour créer une banque de données pour le cluster :
   
    * Sélectionnez **Créer**, puis entrez un nom pour le compte de stockage.
    
    * Sélectionnez **Emplacement**, puis sélectionnez un emplacement proche de vous géographiquement. Cet emplacement est utilisé pour créer le compte de stockage et le cluster HDInsight.
     
   Enfin, utilisez le bouton **Sélectionner** pour enregistrer les paramètres.
     
    ![Configurer le stockage](./media/hdinsight-apache-kafka-get-started/configure-storage.png)

6. Utilisez **Tarification**, puis définissez le **Nombre de nœuds Worker** sur 2. L’utilisation de 2 nœuds Worker permet de réduire le coût du cluster et est suffisante pour cet exemple. Utilisez le bouton **Sélectionner** pour enregistrer les paramètres.
   
    ![Tarification](./media/hdinsight-apache-kafka-get-started/pricing.png)
   
    > [!NOTE]
    > Les prix affichés dans le portail peuvent différer des prix dans la capture d’écran.

7. Utilisez **Groupe de ressources** pour créer un groupe, et entrez le nom dans le champ. Sélectionnez également **Épingler au tableau de bord**. Lorsque vous avez terminé, sélectionnez **Créer** pour créer le cluster.
   
    ![Champ Groupe de ressources](./media/hdinsight-apache-kafka-get-started/resource-group.png)
   
    > [!NOTE]
    > La création du cluster peut prendre jusqu’à 20 minutes.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

À partir de votre cluster, utilisez SSH pour la connexion au cluster. Si vous utilisez Linux, Unix, MacOS ou Bash sur Windows 10, utilisez la commande suivante :

    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net

Remplacez **SSHUSER** par le nom d’utilisateur SSH que vous avez fourni lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.

Lorsque vous y êtes invité, entrez le mot de passe utilisé pour le compte SSH.

Pour des informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants :

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Obtention des informations sur Zookeeper et l’hôte du répartiteur

Lorsque vous travaillez avec Kafka, vous devez connaître deux valeurs d’hôte : les hôtes *Zookeeper* et de *répartiteur*. Ces hôtes sont utilisés avec l’API Kafka et la plupart des utilitaires fournis avec Kafka.

Utilisez les étapes suivantes pour créer des variables d’environnement contenant des informations sur l’hôte. Ces variables d’environnement sont utilisées dans les étapes de ce document.

1. À partir d’une connexion SSH avec le cluster, utilisez la commande suivante pour installer l’utilitaire `jq`. Cet utilitaire est utilisé pour analyser des documents JSON et est utile lors de la récupération des informations sur l’hôte du répartiteur :
   
        sudo apt -y install jq

2. utilisez les commandes suivantes pour définir les variables d’environnement avec les informations récupérées à partir d’Ambari. Remplacez __KAFKANAME__ par le nom du cluster Kafka. Remplacez __PASSWORD__ par le mot de passe de connexion (admin) que vous avez utilisé lors de la création du cluster.

        export KAFKAZKHOSTS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

        export KAFKABROKERS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

        echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
        echo '$KAFKABROKERS='$KAFKABROKERS

    Le texte suivant constitue un exemple du contenu de `$KAFKAZKHOSTS` :
   
        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
   
    Le texte suivant constitue un exemple du contenu de `$KAFKABROKERS` :
   
        wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092
   
    > [!WARNING]
    > Les informations renvoyées par cette session ne sont pas toujours précises. Si vous redimensionnez le cluster, de nouveaux répartiteurs sont ajoutés ou supprimés. Si une défaillance se produit et si un nœud est remplacé, le nom d’hôte du nœud peut changer. 
    > 
    > Vous devez toujours récupérer les informations des hôtes Zookeeper et du répartiteur peu de temps avant de les utiliser pour vous assurer de leur validité.

## <a name="create-a-topic"></a>Création d'une rubrique

Kafka stocke les flux de données dans des catégories appelées *rubriques*. À partir d’une connexion SSH à un nœud principal de cluster, utilisez un script fourni avec Kafka pour créer une rubrique :

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

Cette commande se connecte à Zookeeper en utilisant les informations d’hôte stockées dans `$KAFKAZKHOSTS`, puis crée une rubrique Kafka nommée **test**. Vous pouvez vérifier que la rubrique a été créée à l’aide du script suivant pour répertorier les rubriques :

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS

La sortie de cette commande répertorie les rubriques Kafka, contenant la rubrique **test**.

## <a name="produce-and-consume-records"></a>Produire et consommer des enregistrements

Kafka stocke les *enregistrements* dans des rubriques. Les enregistrements sont produits par des *producteurs*, et utilisés par des *consommateurs*. Les producteurs récupèrent des enregistrements à partir des *répartiteurs* Kafka. Chaque nœud Worker dans votre cluster HDInsight est un répartiteur Kafka.

Pour stocker les enregistrements dans la rubrique test créée précédemment, puis les lire à l’aide d’un consommateur, procédez comme suit :

1. À partir de la session SSH, utilisez un script fourni avec Kafka pour écrire des enregistrements dans la rubrique :
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
   
    Vous ne reviendrez pas à l’invite après cette commande. À la place, tapez quelques messages de texte, puis utilisez la combinaison **Ctrl + C** pour arrêter l’envoi à la rubrique. Chaque ligne est envoyée en tant qu’enregistrement distinct.

2. Utilisez un script fourni avec Kafka pour lire les enregistrements à partir de la rubrique :
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
   
    Cela récupérera les enregistrements à partir de la rubrique et les affichera. L’utilisation de `--from-beginning` indique au consommateur de démarrer à partir du début du flux, de manière à récupérer tous les enregistrements.

3. Utilisez la combinaison __Ctrl + C__ pour arrêter le consommateur.

## <a name="producer-and-consumer-api"></a>API de producteur et de consommateur

Vous pouvez également produire et consommer des enregistrements par programme à l’aide des [API Kafka](http://kafka.apache.org/documentation#api). Utilisez les étapes suivantes pour télécharger et générer un producteur et un consommateur basés sur Java :

1. Téléchargez les exemples à partir de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Pour l’exemple de producteur/consommateur, utilisez le projet dans le répertoire `Producer-Consumer`. Veillez à examiner le code pour comprendre cet exemple. Il contient les classes suivantes :
   
    * **Run** : démarre le consommateur ou le producteur en fonction des arguments de ligne de commande.

    * **Producer** : stocke 1 000 000 d’enregistrements dans la rubrique.

    * **Consumer** : lit les enregistrements à partir de la rubrique.

2. À partir de la ligne de commande dans votre environnement de développement, modifiez les répertoires d’accès à l’emplacement du répertoire `Producer-Consumer` de l’exemple, puis utilisez la commande suivante pour créer un package jar :
   
        mvn clean package
   
    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Utilisez les commandes suivantes pour copier le fichier `kafka-producer-consumer-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :
   
        scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
   
    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

4. Une fois que la commande `scp` a fini de copier le fichier, connectez-vous au cluster à l’aide de SSH, puis utilisez les éléments suivants pour écrire des enregistrements dans la rubrique test créée précédemment.
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS
   
    Cela va démarrer le producteur et écrire des enregistrements. Un compteur s’affiche afin de voir le nombre d’enregistrements qui ont été écrits.

    > [!NOTE]
    > Si vous recevez une erreur de refus d’autorisation, pour rendre le fichier exécutable, utilisez la commande suivante : ```chmod +x kafka-producer-consumer.jar```

5. Une fois le processus terminé, utilisez la commande suivante pour lire à partir de la rubrique :
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS
   
    Les enregistrements lus et le nombre d’enregistrements s’affichent. Il est possible qu’un peu plus de 1 000 000 d’enregistrements soient consignés, car nous avons envoyé plusieurs enregistrements à la rubrique à l’aide d’un script dans une étape précédente.

6. Utilisez __Ctrl + C__ pour quitter le consommateur.

### <a name="multiple-consumers"></a>Consommateurs multiples

Un concept important avec Kafka est que les consommateurs utilisent un groupe de consommateurs (défini par un id de groupe) lors de la lecture d’enregistrements. Plusieurs consommateurs qui utilisent le même groupe équilibrent la charge des lectures d’une rubrique ; chaque consommateur reçoit une partie des enregistrements. Pour voir ceci en action, procédez comme suit :

1. Ouvrez une nouvelle session SSH vers le cluster, afin de disposer de deux sessions. Dans chaque session, utilisez les éléments suivants pour démarrer un consommateur avec le même id de groupe de consommateurs :
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup

    > [!NOTE]
    > Étant donné qu’il s’agit d’une nouvelle session SSH, vous devrez utiliser les commandes dans la section [Obtention des informations sur Zookeeper et l’hôte du répartiteur](#getkafkainfo) pour définir `$KAFKABROKERS`.

2. Observez chaque session compter les enregistrements reçus de la rubrique. Le total des deux sessions doit être identique à celui reçu précédemment à partir d’un seul consommateur.

La consommation par les clients au sein du même groupe est gérée par le biais des partitions de la rubrique. Pour la rubrique `test` créée précédemment, il y a 8 partitions. Si vous ouvrez 8 sessions SSH et lancez un consommateur dans toutes les sessions, chaque consommateur va lire les enregistrements à partir d’une seule partition de la rubrique.

> [!IMPORTANT]
> Il ne peut pas y avoir plus d’instances de consommateurs dans un groupe de consommateurs que de partitions. Dans cet exemple, un groupe de consommateurs peut contenir jusqu’à 8 consommateurs puisque c’est le nombre de partitions de la rubrique. Vous pouvez également disposer de plusieurs groupes de consommateurs, chacun ne dépassant pas 8 consommateurs.

Les enregistrements stockés dans Kafka sont stockés dans l’ordre de réception dans une partition. Pour obtenir la livraison chronologique des enregistrements *dans une partition*, créez un groupe de consommateurs où le nombre d’instances de consommateurs correspond au nombre de partitions. Pour obtenir la livraison chronologique des enregistrements *dans la rubrique*, créez un groupe de consommateurs avec une seule instance de consommateur.

## <a name="streaming-api"></a>API de diffusion en continu

L’API de diffusion en continu a été ajoutée à Kafka dans la version 0.10.0 ; les versions antérieures s’appuient sur Apache Spark ou Storm pour le traitement des flux de données.

1. Si ce n’est pas déjà fait, téléchargez les exemples de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Pour l’exemple de diffusion en continu, utilisez le projet dans le répertoire `streaming`. Veillez à examiner le code pour comprendre cet exemple. 
   
    Ce projet contient uniquement une classe, `Stream`, qui lit les enregistrements à partir de la rubrique `test` créée précédemment. Il compte les mots lus et émet chaque mot et quantité dans une rubrique nommée `wordcounts`. La rubrique `wordcounts` est créée dans une étape ultérieure de cette section.

2. À partir de la ligne de commande dans votre environnement de développement, modifiez les répertoires d’accès à l’emplacement du répertoire `Streaming`, puis utilisez la commande suivante pour créer un package jar :
   
        mvn clean package
   
    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Utilisez les commandes suivantes pour copier le fichier `kafka-streaming-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :
   
        scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
   
    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

4. Une fois que la commande `scp` a fini de copier le fichier, connectez-vous au cluster à l’aide de SSH, puis utilisez les éléments suivants pour démarrer le processus de diffusion en continu :
   
        ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
   
    Cette commande démarre le processus de diffusion en continu en arrière-plan.

5. Utilisez la commande suivante pour envoyer des messages à la rubrique `test`. Ces données sont traitées par l’exemple de diffusion en continu :
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &

6. Utilisez la commande suivante pour afficher la sortie écrite dans la rubrique `wordcounts` :
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
   
    > [!NOTE]
    > Nous devons indiquer au consommateur d’imprimer la clé (qui contient la valeur du mot) et le désérialiseur à utiliser pour la clé et la valeur afin d’afficher les données.
   
    Le résultat ressemble à ce qui suit :
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    Notez que le nombre augmente à chaque fois qu’un mot est rencontré.

7. Utilisez la combinaison __Ctrl + C__ pour quitter le client, puis utilisez la commande `fg` pour passer la tâche de diffusion en continu de l’arrière-plan au premier plan. Utilisez la combinaison __Ctrl + C__ pour le quitter également.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris les bases de l’utilisation d’Apache Kafka sur HDInsight. Consultez les articles suivants pour en savoir plus sur l’utilisation de Kafka :

* [Documentation Apache Kafka](http://kafka.apache.org/documentation.html) à l’adresse kafka.apache.org.
* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)




<!--HONumber=Nov16_HO3-->


