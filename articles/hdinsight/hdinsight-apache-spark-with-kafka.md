---
title: "Utilisation d’Apache Spark avec Kafka sur HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser Spark sur HDInsight pour lire et écrire des données sur un Kafka dans un cluster HDInsight. Cet exemple utilise Scala dans un bloc-notes Jupyter pour écrire des données aléatoires sur un Kafka dans HDInsight, puis les lire à l’aide de la diffusion en continu Spark."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 535e8fc7503e21eea470a1fdb0a10fbc8a18349c

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>Utilisation d’Apache Spark avec Kafka (version préliminaire) sur HDInsight

Apache Spark peut être utilisé pour diffuser en continu des données dans ou en dehors d’Apache Kafka. Dans ce document, découvrez comment diffuser en continu des données dans et en dehors de Kafka à l’aide d’un bloc-notes Jupyter à partir de Spark sur HDInsight.

> [!NOTE]
> Les étapes décrites dans ce document créent un groupe de ressources Azure qui contient à la fois un Spark sur HDInsight et un Kafka sur un cluster HDInsight. Ces clusters sont tous deux situés dans un réseau virtuel Azure, ce qui permet au cluster Spark de communiquer directement avec le cluster Kafka.
> 
> Lorsque vous avez terminé les étapes décrites dans ce document, n’oubliez pas de supprimer les clusters pour éviter les frais supplémentaires.

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure

* Un client SSH (vous aurez besoin des commandes `ssh` et `scp`) - Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants :

    * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* [cURL](https://curl.haxx.se/) : un utilitaire interplateforme pour effectuer des requêtes HTTP.

* [jq](https://stedolan.github.io/jq/) : un utilitaire interplateforme pour analyser des documents JSON.

## <a name="create-the-clusters"></a>Création des clusters

Apache Kafka sur HDInsight ne donne pas accès aux répartiteurs Kafka via l’internet public. Tout ce qui communique avec Kafka doit se trouver sur le même réseau virtuel Azure que les nœuds du cluster Kafka. Pour cet exemple, les clusters Kafka et Spark sont situés dans un réseau virtuel Azure. Le diagramme suivant illustre le flux de communication entre les clusters :

![Diagramme des clusters Spark et Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Bien que Kafka soit lui-même limité à la communication au sein du réseau virtuel, d’autres services sur le cluster comme SSH et Ambari sont accessibles sur Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Même si vous pouvez créer un réseau virtuel Azure, et des clusters Kafka et Spark manuellement, il est plus facile d’utiliser un modèle Azure Resource Manager. Utilisez les étapes suivantes pour déployer un réseau virtuel Azure et des clusters Kafka et Spark sur votre abonnement Azure.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle Azure Resource Manager se trouve à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json**.

2. Utilisez les informations suivantes pour remplir les entrées sur le panneau **déploiement personnalisé** :
   
    ![Déploiement HDInsight personnalisé](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un. Ce groupe contient le cluster HDInsight.

    * **Emplacement** : choisissez un emplacement proche de vous géographiquement. Cet emplacement doit correspondre à l’emplacement dans la section __PARAMÈTRES__.

    * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Spark et Kafka. Par exemple, si vous entrez **hdi**, cela crée un cluster Spark nommé spark-hdi__ et un cluster Kafka nommé **kafka-hdi**.

    * **Nom d’utilisateur du cluster** : nom utilisateur Admin pour les clusters Spark et Kafka.

    * **Mot de passe du cluster** : mot de passe utilisateur Admin pour les clusters Spark et Kafka.

    * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters Spark et Kafka.

    * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters Spark et Kafka.

    * **Emplacement** : région dans laquelle les clusters seront créés.

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

Une fois les ressources créées, vous êtes redirigé vers un panneau pour le groupe de ressources contenant les clusters et le tableau de bord Web.

![Volet du groupe de ressources pour les clusters et le réseau virtuel](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **spark-BASENAME** et **kafka-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans les étapes ultérieures, lors de la connexion aux clusters.

## <a name="get-the-code"></a>Obtenir le code

Le code de l’exemple décrit dans ce document est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="understand-the-code"></a>Comprendre le code

Cet exemple utilise une application Scala dans un bloc-notes Jupyter. Le code du bloc-notes s’appuie sur les éléments de données suivants :

* __Répartiteurs Kafka__ : le processus de répartiteur s’exécute sur chaque nœud Worker du cluster Kafka. La liste des répartiteurs est requise par le composant producteur, qui écrit les données sur Kafka.

* __Hôtes Zookeeper__ : les hôtes Zookeeper pour le cluster Kafka sont utilisés lors de la consommation (lecture) des données à partir de Kafka.

* __Nom de la rubrique__ : le nom de la rubrique vers laquelle les données sont écrites et à partir de laquelle elles sont lues. Cet exemple attend une rubrique nommée `sparktest`.

Consultez la section [Informations sur l’hôte Kafka](#kafkahosts) pour plus d’informations sur l’obtention du répartiteur Kafka et des informations sur l’hôte Zookeeper.

Le code du bloc-notes effectue les tâches suivantes :

* Crée un consommateur qui lit des données à partir d’une rubrique Kafka nommée `sparktest`, compte chaque mot dans les données et stocke le mot et la quantité dans une table temporaire nommée `wordcounts`.

* Crée un producteur qui écrit des phrases aléatoires dans la rubrique Kafka nommée `sparktest`.

* Sélectionne les données de la table `wordcounts` pour afficher les quantités.

Chaque cellule du projet contient des commentaires ou une section de texte qui explique ce que fait le code.

##<a name="a-idkafkahostsakafka-host-information"></a><a id="kafkahosts"></a>Informations sur l’hôte Kafka

La première chose à faire lorsque vous créez une application qui fonctionne avec Kafka sur HDInsight consiste à obtenir les informations sur le répartiteur Kafka et l’hôte Zookeeper pour le cluster Kafka. Ces informations sont utilisées par les applications clientes pour communiquer avec Kafka.

> [!NOTE]
> Le répartiteur Kafka et les hôtes Zookeeper ne sont pas directement accessibles sur Internet. Toute application qui utilise Kafka doit s’exécuter sur le cluster Kafka ou dans le même réseau virtuel Azure que le cluster Kafka. Dans ce cas, l’exemple s’exécute sur un cluster Spark sur HDInsight dans le même réseau virtuel.

À partir de votre environnement de développement, utilisez les commandes suivantes pour récupérer les informations sur le répartiteur et sur Zookeeper. Remplacez __PASSWORD__ par le mot de passe de connexion (admin) que vous avez utilisé lors de la création du cluster. Remplacez __BASENAME__ par le nom de base que vous avez utilisé lors de la création du cluster.

* Pour obtenir les informations sur le __répartiteur Kafka__ :

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > Lorsque vous utilisez cette commande à partir de Windows PowerShell, vous pouvez recevoir une erreur sur la citation de l’interpréteur de commandes. Dans ce cas, utilisez la commande suivante : `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'

* Pour obtenir les informations sur __l’hôte Zookeeper__ :

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > Lorsque vous utilisez cette commande à partir de Windows PowerShell, vous pouvez recevoir une erreur sur la citation de l’interpréteur de commandes. Dans ce cas, utilisez la commande suivante : `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

Ces deux commandes renvoient des informations semblables aux informations suivantes :

* __Répartiteurs Kafka__ : `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Hôtes Zookeeper__ : `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> Notez ces informations car elles sont utilisées dans plusieurs étapes de ce document.

## <a name="use-the-jupyter-notebook"></a>Utiliser le bloc-notes Jupyter

Pour utiliser l’exemple de bloc-notes Jupyter, vous devez le charger sur le serveur de blocs-notes Jupyter sur le cluster Spark. Utilisez les étapes suivantes pour charger le bloc-notes :

1. Dans votre navigateur web, utilisez l’URL suivante pour vous connecter au serveur de blocs-notes Jupyter sur le cluster Spark. Remplacez __BASENAME__ par le nom de base utilisé lors de la création du cluster.

        https://spark-BASENAME.azurehdinsight.net/jupyter

    Lorsque vous y êtes invité, entrez l’identifiant de connexion (admin) et le mot de passe du cluster utilisés lors de la création du cluster.

2. Dans la partie supérieure droit de la page, utilisez le bouton __Charger__ pour charger le fichier `KafkaStreaming.ipynb`. Sélectionnez le fichier dans la boîte de dialogue de l’Explorateur de fichiers, puis sélectionnez __Ouvrir__. 

    ![Utiliser le bouton Charger pour sélectionner et charger un bloc-notes](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![Sélectionner le fichier KafkaStreaming.ipynb](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Rechercher l’entrée __KafkaStreaming.ipynb__ dans la liste des blocs-notes, puis sélectionnez le bouton __Charger__ en regard de celle-ci.

    ![Utiliser le bouton Charger en regard de l’entrée KafkaStreaming.ipynb pour charger celle-ci vers le serveur de blocs-notes](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. Une fois le fichier chargé, sélectionnez l’entrée __KafkaStreaming.ipynb__ pour ouvrir le bloc-notes. Pour exécuter cet exemple, suivez les instructions du bloc-notes.

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Étant donné que les étapes décrites dans ce document créent deux clusters dans le même groupe de ressources Azure, vous pouvez supprimer le groupe de ressources du portail Azure. La suppression de ce groupe supprime toutes les ressources créées par la suite dans ce document, le réseau virtuel Azure et le compte de stockage utilisé par les clusters.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser Spark pour lire et écrire dans Kafka. Utilisez les liens suivants pour découvrir d’autres façons de travailler avec Kafka :

* [Prise en main d’Apache Kafka sur HDInsight](hdinsight-apache-kafka-get-started.md)
* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Utilisation d’Apache Storm avec Kafka sur HDInsight](hdinsight-apache-storm-with-kafka.md)




<!--HONumber=Nov16_HO3-->


