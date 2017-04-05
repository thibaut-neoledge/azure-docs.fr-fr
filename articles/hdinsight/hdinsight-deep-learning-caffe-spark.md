---
title: "Utiliser Caffe sur Azure HDInsight Spark pour une formation approfondie échelonnée | Microsoft Docs"
description: "Utiliser Caffe sur Azure HDInsight Spark pour une formation approfondie échelonnée"
services: hdinsight
documentationcenter: 
author: xiaoyongzhu
manager: asadk
editor: cgronlun
tags: azure-portal
ms.assetid: 71dcd1ad-4cad-47ad-8a9d-dcb7fa3c2ff9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: xiaoyzhu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 14b7808c9534bce3049422d6bce1e8914b2c2fbc
ms.lasthandoff: 03/25/2017


---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Utiliser Caffe sur Azure HDInsight Spark pour une formation approfondie échelonnée


## <a name="introduction"></a>Introduction

La formation approfondie affecte tous les secteurs, de la santé publique au transport en passant par la fabrication, et plus encore. Les entreprises se tournent vers la formation approfondie pour résoudre les problématiques difficiles, comme la [classification des images](http://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), la [reconnaissance vocale](http://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), la reconnaissance des objets et la traduction automatique. 

Il existe de [nombreux frameworks populaires](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), notamment [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), MXNet, Theano, etc. Caffe est l’un des frameworks de réseau neuronal non symbolique (impératif) les plus connus ; il a été largement adopté dans de nombreux secteurs, notamment dans celui de la vision par ordinateur. En outre, [CaffeOnSpark](http://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) combine Caffe et Apache Spark. Dès lors, la formation approfondie échelonnée peut être facilement utilisée sur un cluster Hadoop existant avec des pipelines Spark ETL, pour une complexité du système et une latence moindres pour la prise en charge de l’apprentissage de bout en bout.

[HDInsight](https://azure.microsoft.com/en-us/services/hdinsight/) est la seule offre de cloud Hadoop entièrement gérée qui fournit des clusters d’analyse open source optimisés pour Spark, Hive, Map Reduce, HBase, Storm, Kafka et R-Server, avec un contrat SLA proposant une disponibilité de 99,9 %. Chacune de ces technologies Big Data ainsi que les applications d’éditeurs de logiciels indépendants (ISV) sont facilement déployables sous forme de clusters gérés, avec une surveillance et une sécurité à l’échelle de l’entreprise.

Certains utilisateurs nous interrogent sur l’utilisation de la formation approfondie sur HDInsight, qui est le produit Hadoop PaaS de Microsoft. Si nous reviendrons plus en détail sur cette problématique à l’avenir, nous allons aujourd’hui résumer les articles d’un blog technique s’intéressant à l’utilisation de Caffe sur HDInsight Spark.

Si vous avez déjà installé Caffe par le passé, vous remarquerez que l’installation de ce framework est quelque peu difficile. Dans ce blog, nous décrivons dans un premier temps l’installation de [Caffe on Spark](https://github.com/yahoo/CaffeOnSpark) pour un cluster HDInsight, puis nous appuyons sur la démonstration MNIST intégrée pour illustrer l’utilisation de la formation approfondie échelonnée à l’aide de HDInsight Spark sur les UC.

Dans ce cadre, 4 étapes majeures sont à exécuter sur HDInsight.

1. Installation des dépendances requises sur l’ensemble des nœuds
2. Développer CaffeOnSpark pour HDInsight sur le nœud principal
3. Distribuer les bibliothèques requises sur l’ensemble des nœuds de travail
4. Composer un modèle Caffe et l’exécuter de manière échelonnée

Dans la mesure où HDInsight est une solution PaaS, elle prend en charge de formidables fonctionnalités de plate-forme. Dès lors, certaines tâches sont simples à exécuter. L’[action de script](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) est l’une des fonctions que nous utilisons beaucoup dans cet article. Elle permet d’exécuter des commandes d’interpréteur dédiées à la personnalisation des nœuds de cluster (nœud principal, nœud de travail ou nœud de périmètre).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Étape 1 : Installation des dépendances requises sur l’ensemble des nœuds

Pour commencer, nous devons installer les dépendances dont nous avons besoin. Les sites Caffe et [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) comportent des pages wiki très utiles relatives à l’installation des dépendances pour le mode Spark sur YARN (qui est le mode pour HDInsight Spark). Cependant, nous devons ajouter quelques dépendances supplémentaires pour la plate-forme HDInsight. Nous utiliserons l’action de script ci-dessous. Elle sera exécutée sur l’ensemble des nœuds principaux et de travail. Cette action prendra environ 20 minutes, car ces dépendances sont également associées à d’autres packages. Vous avez intérêt à l’intégrer dans un emplacement accessible par votre cluster HDInsight, tel que l’emplacement GitHub ou le compte de stockage BLOB par défaut.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in http://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose we install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


L’action de script ci-dessus présente deux étapes. La première étape consiste en l’installation de l’ensemble des bibliothèques requises. Nous parlons ici notamment des bibliothèques dédiées à la compilation de Caffe (comme gflags, glog) et à l’exécution de Caffe (comme numpy). Nous recourons à libatlas pour l’utilisation de l’UC. Toutefois, nous vous invitons à consulter la page wiki CaffeOnSpark sur l’installation d’autres bibliothèques d’optimisation, comme MKL ou CUDA (pour GPU).

La seconde étape consiste à télécharger, compiler et installer protobuf 2.5.0 pour Caffe durant l’exécution. Si Protobuf 2.5.0 [est requise](https://github.com/yahoo/CaffeOnSpark/issues/87), cette version n’est pas disponible en tant que package sur Ubuntu 16. Dès lors, nous devons la compiler depuis le code source. D’autres ressources décrivant la procédure de compilation, comme [celle-ci](http://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html), sont également disponibles sur Internet.

Pour démarrer, vous pouvez exécuter cette action de script sur l’ensemble des nœuds de travail et principaux de votre cluster (pour HDInsight 3.5). Vous pouvez exécuter les actions de script sur un cluster en cours d’exécution, ou en cours de configuration. Pour plus d’informations sur les actions de script, consultez la documentation [ici](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#view-history-promote-and-demote-script-actions).

![Actions de script dédiées à l’installation des dépendances](./media/hdinsight-deep-learning-caffe-spark/Script-Action-1.png)


## <a name="step-2-build-caffe-on-spark-for-hdinsight-on-the-head-node"></a>Étape 2 : Génération de CaffeOnSpark pour HDInsight sur le nœud principal

La seconde étape consiste en la génération de Caffe sur le nœud principal, puis en la distribution des bibliothèques compilées sur l’ensemble des nœuds de travail. Dans cette étape, vous devez utiliser [ssh dans votre nœud principal](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), puis suivre le [processus de génération CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Vous trouverez ci-dessous le script à utiliser pour générer CaffeOnSpark, avec quelques étapes supplémentaires. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Il est possible que vous deviez suivre une procédure plus longue que celle décrite dans la documentation de CaffeOnSpark. Les modifications sont les suivantes :
- Modification exclusive des UC et utilisation du package libatlas à cette fin.
- Placez les jeux de données sur le stockage BLOB, qui est un emplacement partagé accessible par l’ensemble des nœuds de travail pour une utilisation ultérieure.
- Placez les bibliothèques Caffe compilées sur le stockage BLOB ; plus tard, vous les copierez sur l’ensemble des nœuds à l’aide d’actions de script, ceci pour éviter tout délai supplémentaire de compilation.


### <a name="troubleshooting-an-ant-buildexception-has-occured-exec-returned-2"></a>Résolution des problèmes : An Ant BuildException has occured: exec returned: 2

Lors de la première tentative de génération CaffeOnSpark, le message suivant peut parfois s’afficher :

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2

Nettoyez simplement le référentiel du code en exécutant successivement les commandes make clean et make build pour résoudre ce problème, sous réserve que vous disposiez des dépendances appropriées.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Résolution des problèmes : Expiration du délai de connexion du référentiel Maven

Parfois, Maven renvoie l’erreur de dépassement du délai de connexion, similaire à ce qui suit :

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Une fois la configuration normale rétablie après quelques minutes, tentez de régénérer le code, de manière à ce que Maven limite le trafic d’une manière quelconque à partir d’une adresse IP donnée.


### <a name="troubleshooting-test-failure-for-caffe"></a>Résolution des problèmes : Défaillance de test Caffe

Lors de la vérification finale de CaffeOnSpark, une défaillance de test similaire à la suivante s’affichera probablement. Cela est probablement lié à l’encodage UTF-8, mais ne doit normalement avoir aucune incidence sur l’utilisation de Caffe.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Étape 3 : Répartition des bibliothèques requises sur l’ensemble des nœuds de travail

L’étape suivante consiste à répartir les bibliothèques (celles de CaffeOnSpark/caffe-public/distribute/lib/ et CaffeOnSpark/caffe-distri/distribute/lib/) sur l’ensemble des nœuds. À l’étape 2, nous plaçons ces bibliothèques sur le stockage d’objets BLOB, et dans cette étape, nous utiliserons les actions de script pour le copier sur tous les nœuds principaux et les nœuds de travail.

Pour ce faire, exécutez simplement une action de script comme ci-dessous (vous devez pointer vers l’emplacement approprié spécifique à votre cluster) :

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Dans la mesure où à l’étape 2, nous procédons à un déplacement dans le stockage BLOB accessible à l’ensemble des nœuds, dans cette étape nous nous contentons d’une copie dans l’ensemble des nœuds.

## <a name="step-4-compose-a-caffe-model-and-run-it-distributely"></a>Étape 4 : Composition d’un modèle Caffe et exécution de ce dernier de manière distribuée.

Une fois la procédure ci-dessus exécutée, Caffe est installé sur le nœud principal. Nous pouvons poursuivre l’opération. Il est ensuite question d’écrire un modèle Caffe. 

Caffe valorise une architecture « expressive », dans laquelle la composition du modèle nécessite simplement la définition d’un fichier de configuration, sans aucun codage (dans la plupart des cas). Jetons un œil sur cette configuration. 

Le modèle formé aujourd’hui est un exemple de modèle de la formation MNIST. La base de données MNIST de chiffres manuscrits présente un ensemble de formation comportant 60 000 exemples, ainsi qu’un ensemble test de 10 000 exemples. Il s’agit d’un sous-ensemble d’un jeu plus volumineux de MNIST. Les chiffres présentent une taille normalisée et sont centrés dans une image à taille fixe. CaffeOnSpark possède certains scripts permettant de télécharger le jeu de données et de la convertir au format approprié.

CaffeOnSpark fournit quelques exemples de topologies de réseau associés à la formation MNIST. La solution affiche une conception efficace de distribution et d’optimisation de l’architecture réseau (la topologie du réseau). Dans ce cas, deux fichiers sont requis : 

Le fichier « solver » (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) est dédié à la supervision de l’optimisation et à la génération des mises à jour de paramètres. Par exemple, il détermine si UC ou GPU sont à utiliser, l’inertie, le nombre d’itérations, etc. Il définit également la topologie de réseau neuronal utilisée par le programme (second fichier requis). Pour plus d’informations sur le fichier solver, consultez la [documentation Caffe](http://caffe.berkeleyvision.org/tutorial/solver.html).

Pour cet exemple, dans la mesure où nous avons préféré UC à GPU, nous devons définir la dernière ligne sur :

    # solver mode: CPU or GPU
    solver_mode: CPU

![Configuration de Caffe](./media/hdinsight-deep-learning-caffe-spark/Caffe-1.png)

Au besoin, vous pouvez modifier d’autres lignes.

Le second fichier (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) définit l’apparence du réseau neuronal, ainsi que le fichier approprié d’entrée et de sortie. Nous devons également mettre à jour le fichier en fonction de l’emplacement des données de formation. Modifiez la portion suivante de lenet_memory_train_test.prototxt (vous devez pointer vers l’emplacement approprié spécifique à votre cluster) :

- Remplacez file:/Users/mridul/bigml/demodl/mnist_train_lmdb par wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb
- Remplacez file:/Users/mridul/bigml/demodl/mnist_test_lmdb/ par wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb

![Configuration de Caffe](./media/hdinsight-deep-learning-caffe-spark/Caffe-2.png)

Pour plus d’informations sur la définition du réseau, consultez la [documentation Caffe sur l’ensemble de données MNIST](http://caffe.berkeleyvision.org/gathered/examples/mnist.html).

Pour les besoins de ce blog, nous utilisons ce simple exemple MNIST. Vous devez exécuter la commande ci-dessous à partir du nœud principal :

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

En fait, elle distribue les fichiers requis (lenet_memory_solver.prototxt et lenet_memory_train_test.prototxt) vers chaque conteneur YARN et définit également l’élément PATH approprié de chaque pilote/exécuteur Spark sur LD_LIBRARY_PATH, qui est paramétré sur l’extrait de code précédent et pointe vers l’emplacement hébergeant les bibliothèques CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Surveillance et dépannage

Puisque nous utilisons le mode de cluster YARN, auquel cas le pilote Spark est planifié sur un conteneur arbitraire (et un nœud de travail arbitraire), la console devrait générer uniquement un contenu similaire à ce qui suit :

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Si vous ne souhaitez pas savoir ce qui est arrivé, il vous suffira de récupérer le journal du pilote Spark, qui comporte davantage d’informations. Dans ce cas, vous devez accéder à l’interface utilisateur YARN et y rechercher les journaux YARN appropriés. L’interface utilisateur YARN est accessible à cet URL : 

    https://yourclustername.azurehdinsight.net/yarnui
   
![Interface utilisateur Yarn](./media/hdinsight-deep-learning-caffe-spark/YARN-UI-1.png)

Vous pouvez découvrir le nombre de ressources allouées pour cette application spécifique. Si vous cliquez sur le lien du planificateur, vous verrez que 9 conteneurs sont exécutés pour cette application. Nous demandons à YARN de fournir 8 exécuteurs ; un autre conteneur est dédié au processus du pilote. 

![Planificateur YARN](./media/hdinsight-deep-learning-caffe-spark/YARN-Scheduler.png)

Peut-être voulez-vous vérifier l’absence de défaillances dans les journaux de conteneurs et de pilotes. Pour les journaux de pilotes, vous pouvez cliquer sur l’ID d’application dans l’interface utilisateur YARN, puis sur le bouton dédié aux journaux. Les journaux des pilotes sont écrits en stderr.

![Interface utilisateur YARN 2](./media/hdinsight-deep-learning-caffe-spark/YARN-UI-2.png)

Par exemple, vous pouvez identifier certaines des erreurs ci-dessous dans les journaux de pilotes. Le cas échéant, elles indiquent que vous avez alloué un nombre trop important d’exécuteurs.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Parfois, le problème survient dans les exécuteurs, non dans les pilotes. Dans ce cas, vous devez examiner les journaux de conteneurs. Vous pouvez toujours obtenir les journaux des conteneurs, puis récupérer le conteneur défaillant. Par exemple, vous pouvez rencontrer cette défaillance lors de l’exécution de Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

Ici, vous devez récupérer l’ID du conteneur défaillant (dans l’exemple ci-dessus, il s’agit de container_1485916338528_0008_05_000005). Vous devez ensuite exécuter 

    yarn logs -containerId container_1485916338528_0008_03_000005

depuis le nœud principal. L’examen de la défaillance du conteneur indique qu’elle a été provoquée par le mode GPU (là où vous devriez utiliser le mode UC) dans lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Obtention des résultats

Dans la mesure où nous allouons 8 exécuteurs et que la topologie du réseau est simple, la génération des résultats ne devrait pas prendre plus de 30 minutes. Dans la ligne de commande, vous pouvez constater que nous avons placé le modèle dans wasb:///mnist.model, puis les résultats dans un dossier nommé wasb:///mnist_features_result.

Pour obtenir les résultats, exécutez

    hadoop fs -cat hdfs:///mnist_features_result/*

Les résultats ressemblent à ceci :

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

L’élément SampleID représente l’ID dans le jeu de données MNIST, tandis que l’élément label correspond au nombre identifié par le modèle.


## <a name="conclusion"></a>Conclusion

Dans cette documentation, vous avez essayé d’installer CaffeOnSpark en exécutant un exemple simple. HDInsight est une plate-forme de calcul distribué entièrement gérée sur le cloud. Il s’agit de l’outil optimal pour l’exécution de charges de travail d’analyse avancée sur des jeux de données volumineux. Vous pouvez par ailleurs valoriser Caffe sr HDInsight Spark pour exécuter les tâches de formation approfondie.


## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température des bâtiments à l’aide des données des systèmes HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


