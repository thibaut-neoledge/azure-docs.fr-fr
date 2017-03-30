---
title: "Analyse des données de capteur avec Apache Storm et HBase | Microsoft Docs"
description: "Découvrez comment vous connecter à Apache Storm à l’aide d’un réseau virtuel. Utilisez Storm avec HBase pour traiter des données de capteur à partir d&quot;un hub d&quot;événements et les visualiser avec D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 34445eff89ff7ff513893d5b36b937075b09f599
ms.lasthandoff: 03/22/2017


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analyser les données de capteur avec Apache Storm, Event Hub, et HBase dans HDInsight (Hadoop)

Découvrez comment utiliser Apache Storm dans HDInsight pour traiter les données de capteur à partir d’Azure Event Hub. Les données sont ensuite stockées dans Apache HBase sur HDInsight et visualisées à l’aide de D3.js.

Le modèle Azure Resource Manager utilisé dans ce document montre comment créer plusieurs ressources Azure dans un groupe de ressources. Le modèle crée un réseau virtuel Azure, deux clusters HDInsight (Storm et HBase) et une application web Azure. Une implémentation node.js de tableau de bord web en temps réel est automatiquement déployée sur l’application web.

> [!NOTE]
> Les informations contenues dans ce document et l’exemple dans ce document nécessitent la version 3.5 de HDInsight.
>
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > Vous n’avez pas besoin d’un cluster HDInsight existant. Les étapes décrites dans ce document créent les ressources suivantes :
  > 
  > * un réseau virtuel Azure ;
  > * un cluster Storm sur HDInsight (basé sur Linux, 2 nœuds Worker) ;
  > * un cluster HBase sur HDInsight (basé sur Linux, 2 nœuds Worker) ;
  > * une application web Azure qui héberge le tableau de bord web.

* [Node.js](http://nodejs.org/) : utilisé pour afficher un aperçu du tableau de bord web localement sur votre environnement de développement.
* [Java et JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): utilisés pour développer la topologie Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): utilisé pour générer et compiler le projet.
* [Git](http://git-scm.com/): utilisé pour télécharger le projet à partir de GitHub.
* Client **SSH** : utilisé pour se connecter aux clusters HDInsight basés sous Linux. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants :
  
  * [Utilisation de SSH (PuTTY) avec HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
  * [Utilisation de SSH avec HDInsight à partir d’un client Linux, Unix, OS X ou Bash sous Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    > [!NOTE]
    > Vous devez également avoir accès à la commande `scp`, qui est utilisée pour copier des fichiers entre votre environnement de développement local et le cluster HDInsight à l’aide de SSH.


## <a name="architecture"></a>Architecture

![Diagramme d'architecture](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple est constitué des composants suivants :

* **Azure Event Hubs**: contient les données collectées à partir des capteurs.
* **Storm sur HDInsight**: fournit le traitement en temps réel des données à partir d’Event Hub.
* **HBase sur HDInsight**: fournit un magasin de données NoSQL persistant pour les données une fois celles-ci traitées par Storm.
* **Service Azure Virtual Network**: permet des communications sécurisées entre Storm sur HDInsight et les clusters HBase sur HDInsight.
  
  > [!NOTE]
  > Un réseau virtuel est requis lors de l’utilisation de l’API cliente Java HBase. Il n’est pas exposé via la passerelle publique pour les clusters HBase. L’installation de clusters HBase et Storm dans le même réseau virtuel permet au cluster Storm (ou à tout autre système sur le réseau virtuel) d’accéder directement à HBase à l’aide de l’API cliente.

* **Site web de tableau de bord**: un exemple de tableau de bord qui suit des données en temps réel.
  
  * Le site web est implémenté dans Node.js, donc il peut s'exécuter sur n'importe quel système d'exploitation de client de test, ou il peut être déployé sur des sites web Azure.
  * [Socket.io](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie Storm et le site web.
    
    > [!NOTE]
    > L’utilisation de Socket.io pour la communication est un détail d’implémentation. Vous pouvez utiliser toute infrastructure de communications, telles que WebSockets brut ou SignalR.

  * [D3.js](http://d3js.org/) est utilisé pour représenter par un graphique les données envoyées au site web.

> [!IMPORTANT]
> Deux clusters sont nécessaires, car il n’existe aucune méthode prise en charge pour créer un cluster HDInsight pour Storm et HBase.

La topologie lit les données à partir d’Event Hub à l’aide de la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) et écrit des données dans HBase à l’aide de la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). La communication avec le site web s'effectue à l'aide de [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Le diagramme suivant décrit la disposition de la topologie :

![diagramme de topologie](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Il s'agit d'un affichage très simplifié de la topologie. Au moment de l'exécution, une instance de chaque composant est créée pour chaque partition pour le hub d'événements en cours de lecture. Ces instances sont réparties entre les nœuds du cluster et les données sont acheminées entre eux comme suit :
> 
> * Les données du spout vers l'analyseur disposent d’un équilibrage de charges.
> * Les données de l’analyseur vers le tableau de bord et HBase sont regroupées par ID de périphérique afin que les messages issus du même périphérique passent toujours par le même composant.

### <a name="topology-components"></a>Composants de la topologie

* **Spout EventHub**: spout fourni dans le cadre de la version d’Apache Storm 0.10.0 et versions ultérieures.
  
  > [!NOTE]
  > Le spout Event Hub utilisé dans cet exemple requiert une version de cluster Storm sur HDInsight 3.3 ou 3.4. Pour plus d’informations sur l’utilisation des Event Hubs avec une version précédente de Storm sur HDInsight, voir [Traitement d’événements à partir d’Azure Event Hubs avec Storm dans HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: les données émises par le spout sont JSON brut, et parfois, plusieurs événements sont émis à la fois. Ce bolt montre comment lire les données émises par le spout et les émet à un nouveau stream sous forme de tuple contenant plusieurs champs.
* **DashboardBolt.java** : ce composant montre comment utiliser la bibliothèque client Socket.io pour Java pour envoyer des données en temps réel au tableau de bord web.
* **Temperature.java** : ce composant définit la topologie et charge les données de configuration du fichier**Config.properties**.

## <a name="prepare-your-environment"></a>Préparation de votre environnement

Avant d’utiliser cet exemple, vous devez créer un Azure Event Hub, qui peut être lu par la topologie Storm.

### <a name="configure-event-hub"></a>Configuration du hub d'événements

Le hub d'événements est la source de données pour cet exemple. Procédez comme suit pour créer un Event Hub.

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ Nouveau** -> **Internet des objets** -> **Hubs d’événements**.
2. Sur le panneau **Créer un espace de noms** , effectuez les tâches suivantes :
   
   1. Entrez un **nom** pour l’espace de noms.
   2. Sélectionnez un niveau tarifaire. **De base** est suffisant pour cet exemple.
   3. Sélectionnez **l’abonnement** Azure à utiliser.
   4. Sélectionnez un groupe de ressources existant ou créez-en un.
   5. Sélectionnez **l’emplacement** du concentrateur d’événements.
   6. Sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.

3. Lorsque le processus de création est terminé, le panneau Hubs d’événements de votre espace de noms s’affiche. À partir d’ici, sélectionnez **+ Ajouter un hub d’événements**. Sur le panneau **Créer un Event Hub**, entrez un nom de **sensordata**, puis sélectionnez **Créer**. Pour les autres champs, utilisez les valeurs par défaut.
4. Dans le panneau Hubs d’événements de votre espace de noms, sélectionnez **Hubs d’événements**. Sélectionnez l’entrée **sensordata** .
5. Depuis le panneau pour le hub d’événements sensordata, sélectionnez **Stratégies d’accès partagé**. Utilisez le lien **+ Ajouter** pour ajouter les stratégies suivantes :

    | Nom de la stratégie | Revendications |
    | ----- | ----- |
    | périphériques | Envoyer |
    | storm | Écouter |

1. Sélectionnez les deux stratégies et prenez note de la valeur **CLÉ PRIMAIRE** . Vous aurez besoin de la valeur pour les deux stratégies dans les étapes suivantes.

## <a name="download-and-configure-the-project"></a>Téléchargement et configuration du projet

Utilisez la commande suivante pour télécharger le projet à partir de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Une fois la commande terminée, vous obtenez la structure de répertoire suivante :

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                hbase-site.xml - connection information for the HBase cluster.
                Config.properties - configuration information for the topology. How to read from Event Hub and the URI to the dashboard.
            src/ - the Java bolts and topology definition.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Ce document ne détaille pas le code inclus dans cet exemple. Toutefois, le code est entièrement commenté.

Ouvrez le fichier **hdinsight-eventhub-example/TemperatureMonitor/resources/Config.properties** et ajoutez les informations de votre concentrateur d’événements pour les lignes suivantes :

    eventhubspout.username = <shared access policy name that can read from Event Hub>
    eventhubspout.password = <shared access policy key>
    eventhubspout.namespace = <namespace of your Event Hub
    eventhubspout.entitypath = <name of your event hub>
    eventhubspout.partitions.count = 2

Enregistrez le fichier après avoir ajouté ces informations.

## <a name="compile-and-test-locally"></a>Compilation et test local

Avant de tester, vous devez lancer le tableau de bord pour afficher la sortie de la topologie et de générer les données à stocker dans le hub d'événements.

> [!IMPORTANT]
> Le composant HBase de cette topologie n’est pas actif lors d’un test local. En effet, l’API Java pour le cluster HBase n’est pas accessible depuis l’extérieur du réseau virtuel Azure qui contient les clusters.

### <a name="start-the-web-application"></a>Démarrage de l’application Web

1. Ouvrez une nouvelle invite de commandes ou un terminal et remplacez les répertoires par **hdinsight-eventhub-example/dashboard**. Utilisez la commande suivante pour installer les dépendances requises par l’application web :
   
        npm install

2. Utilisez la commande suivante pour démarrer l’application Web.
   
        node server.js
   
    Un message similaire au texte suivant s’affiche :
   
        Server listening at port 3000

3. Ouvrez un navigateur web et entrez **http://localhost:3000/** comme adresse. Une page similaire à celle ci-dessous doit s’afficher :
   
    ![tableau de bord web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Laissez l’invite de commandes ou le terminal ouvert(e). Après le test, utilisez Ctrl-C pour arrêter le serveur Web.

### <a name="start-generating-data"></a>Démarrage de la génération de données

> [!NOTE]
> Les étapes de cette section utilisent Node.js de sorte à pouvoir être utilisées sur n’importe quelle plate-forme. Pour d'autres exemples de langage, consultez le répertoire **SendEvents** .

1. Ouvrez une invite, un interpréteur de commandes ou un terminal et remplacez les répertoires par **hdinsight-eventhub-example/SendEvents/nodejs**. Utilisez la commande suivante pour installer les dépendances requises par l’application :
   
        npm install

2. Ouvrez le fichier **app.js** dans un éditeur de texte et ajoutez les informations du concentrateur d'événements obtenues précédemment :
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > Cet exemple suppose que vous avez utilisé **sensordata** comme nom pour votre concentrateur d’événements, et **devices** comme nom pour la stratégie qui possède une revendication **Send**.

3. Utilisez la commande suivante pour insérer de nouvelles entrées dans le hub d'événements :
   
        node app.js
   
    Vous pouvez voir plusieurs lignes de sortie contenant les données envoyées au concentrateur d’événements :
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Démarrage de la topologie

1. Ouvrez une nouvelle invite de commandes, un interpréteur de commandes ou un terminal, et remplacez les répertoires par **hdinsight-eventhub-example/TemperatureMonitor**, puis utilisez la commande suivante pour démarrer la topologie :
   
        mvn compile exec:java
   
    Cette commande démarre la topologie en mode local. Les valeurs contenues dans le fichier **Config.properties** fournissent des informations de connexion pour les concentrateurs d’événements et le site web de tableau de bord local. Une fois démarrée, la topologie lit les entrées à partir du concentrateur d’événements et les envoie au tableau de bord s’exécutant sur votre ordinateur local. Vous devriez voir des lignes apparaître dans le tableau de bord web, comme illustré dans l’image suivante :
   
    ![tableau de bord avec données](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. Lorsque le tableau de bord est en cours d’exécution, utilisez la commande `node app.js` des étapes précédentes pour envoyer de nouvelles données aux Event Hubs. Les valeurs de température étant générées au hasard, le graphique doit être mis à jour pour afficher les modifications importantes de température.
   
   > [!NOTE]
   > Vous devez être dans le répertoire **hdinsight-eventhub-example/SendEvents/Nodejs** lorsque vous utilisez la commande `node app.js`.

3. Après avoir vérifié que le tableau de bord est mis à jour, arrêtez la topologie en tapant Ctrl + C. Vous pouvez également utiliser Ctrl+C pour arrêter le serveur web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Créer un cluster Storm et HBase

Les étapes décrites dans cette section utilisent un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) pour créer un réseau virtuel Azure et des clusters Storm et HBase sur le réseau virtuel. Le modèle crée également une application web Azure et déploie une copie du tableau de bord dans celle-ci.

> [!NOTE]
> Un réseau virtuel est utilisé afin que la topologie en cours d’exécution sur le cluster Storm puisse communiquer directement avec le cluster HBase à l’aide de l’API Java HBase.

Le modèle Resource Manager utilisé dans ce document se trouve dans un conteneur d’objets blob public, disponible à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Cliquez sur le bouton suivant pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Dans le panneau **Déploiement personnalisé**, entrez les valeurs suivantes :
   
    ![Paramètres HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Storm et HBase. Par exemple, si vous entrez **abc**, cela crée un cluster Storm nommé **storm-abc** et un cluster HBase nommé **hbase-abc**.
   * **Nom d’utilisateur de connexion au cluster** : nom d’utilisateur Admin pour les clusters Storm et HBase.
   * **Mot de passe de connexion au cluster** : mot de passe d’utilisateur Admin pour les clusters Storm et HBase.
   * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters Storm et HBase.
   * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters Storm et HBase.
   * **Emplacement** : région dans laquelle seront créés les clusters.
     
     Cliquez sur **OK** pour enregistrer les paramètres.

3. Utilisez la section **Concepts de base** pour créer un groupe de ressources ou sélectionnez un groupe existant.
4. Dans le menu déroulant **Emplacement du groupe de ressources**, sélectionnez le même emplacement que celui que vous avez sélectionné pour le paramètre **Emplacement** dans la section **Paramètres**.
5. Lisez les conditions générales, puis cochez la case **J’accepte les conditions générales mentionnées ci-dessus**.
6. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

Une fois les ressources créées, vous êtes redirigé vers un panneau pour le groupe de ressources contenant les clusters et le tableau de bord Web.

![Volet du groupe de ressources pour les clusters et le réseau virtuel](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **storm-BASENAME** et **hbase-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans une étape ultérieure, lors de la connexion aux clusters. Notez également que le nom du site du tableau de bord est **basename-dashboard**. Cette valeur sera utilisée ultérieurement dans ce document.

## <a name="configure-the-dashboard-bolt"></a>Configurer le bolt de tableau de bord

Pour envoyer des données au tableau de bord déployé en tant qu’application web, vous devez modifier la ligne suivante dans le fichier **Config.properties** :

    dashboard.uri: http://localhost:3000

Modifiez `http://localhost:3000` en `http://BASENAME-dashboard.azurewebsites.net` et enregistrez le fichier. Remplacez **BASENAME** par le nom de base que vous avez fourni à l’étape précédente. Vous pouvez également utiliser le groupe de ressources créé précédemment pour sélectionner le tableau de bord et afficher l’URL.

## <a name="create-the-hbase-table"></a>Créer une table HBase

Pour stocker des données dans HBase, nous devons tout d’abord créer une table. Créez au préalable les ressources sur lesquelles Storm doit écrire, car la création de ressources dans une topologie Storm peut faire que plusieurs instances essaient de créer la même ressource. Créez les ressources en dehors de la topologie et utilisez Storm pour les opérations de lecture/écriture et d’analyse.

1. Utilisez le protocole SSH pour vous connecter au cluster HBase à l’aide de l’utilisateur et du mot de passe SSH fournis pour le modèle lors de la création du cluster. Par exemple, si vous vous connectez à l’aide de la commande `ssh` , vous devez utiliser la syntaxe suivante :
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    Dans cette commande, remplacez **USERNAME** par le nom d’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.
   
        hbase shell
   
    Une fois l’interpréteur de commandes chargé, une invite `hbase(main):001:0>` s’affiche.

3. À partir de l'environnement de ligne de commande HBase, entrez la commande suivante pour créer une table dans laquelle seront stockées les données du capteur :
   
        create 'SensorData', 'cf'

4. Vérifiez que la table a été créée à l’aide de la commande suivante :
   
        scan 'SensorData'
   
    Cela renvoie des informations similaires à l’exemple suivant, indiquant que la table comporte 0 ligne.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Entrez `exit` pour quitter le shell HBase :

## <a name="configure-the-hbase-bolt"></a>Configuration du bolt HBase

Pour écrire dans HBase à partir du cluster Storm , vous devez fournir le bolt HBase avec les détails de configuration de votre cluster HBase. Cet exemple utilise le fichier **hbase-site.xml** à partir du cluster HBase.

### <a name="download-the-hbase-sitexml"></a>Télécharger le fichier hbase-site.xml

À partir d’une invite de commande, utilisez SCP pour télécharger le fichier **hbase-site.XML** depuis le cluster. Dans l’exemple suivant, remplacez **USERNAME** par l’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH. Remplacez `/path/to/TemperatureMonitor/resources/hbase-site.xml` par le chemin d’accès à ce fichier dans le projet TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Cette commande télécharge le fichier **hbase-site.xml** vers chemin d’accès spécifié.

### <a name="enable-the-hbase-bolt"></a>Activation du bolt HBase

Pour activer le composant bolt HBase, ouvrez le fichier **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java** et supprimez les marques de commentaire dans les lignes suivantes :

    // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

Enregistrez ensuite le fichier.

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Génération, mise en package et déploiement de la solution vers HDInsight

Dans votre environnement de développement, suivez les étapes ci-dessous pour déployer la topologie Storm vers le cluster Storm.

1. Depuis le répertoire **TemperatureMonitor** , utilisez la commande suivante pour exécuter une nouvelle version et créer un package JAR à partir de votre projet :
   
        mvn clean compile package
   
    Cette commande crée un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** dans le répertoire **cible** de votre projet.

2. Utilisez SCP pour télécharger le fichier **TemperatureMonitor-1.0-SNAPSHOT.jar** vers votre cluster Storm. Dans l’exemple suivant, remplacez **USERNAME** par l’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > Le chargement du fichier peut prendre plusieurs minutes.

3. Une fois le fichier téléchargé, connectez-vous au cluster à l’aide de SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Dans la session SSH, utilisez la commande suivante pour démarrer la topologie :
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature temperature

5. Une fois la topologie lancée, ouvrez un navigateur vers le site Web que vous avez publié sur Azure, puis utiliser la commande `node app.js` pour envoyer des données au concentrateur d'événements. Vous devez mettre à jour le tableau de bord Web pour afficher les informations.
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Afficher les données HBase

Utilisez les étapes suivantes pour vous connecter à HBase et vérifier que les données ont été écrites dans la table :

1. Utilisez SSH pour vous connecter au cluster HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.
   
        hbase shell
   
    Une fois l’interpréteur de commandes chargé, une invite `hbase(main):001:0>` s’affiche.

3. Affichez les lignes de la table :
   
        scan 'SensorData'
   
    Cette commande renvoie des informations similaires au texte suivant, indiquant que la table comporte des données.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Cette opération d’analyse renvoie 10 lignes au maximum pour la table.

## <a name="delete-your-clusters"></a>Supprimer vos clusters
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour supprimer simultanément les clusters, le stockage et l’application web, supprimez le groupe de ressources qui les contient.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples de topologies Storm avec HDInsight, consultez les [exemples de topologies pour Storm sur HDInsight](hdinsight-storm-example-topology.md)

Pour plus d'informations sur Apache Storm, consultez le [Apache Storm](https://storm.incubator.apache.org/) site.

Pour plus d'informations sur HBase avec HDInsight, veuillez consulter la [Présentation de HBase avec HDInsight](hdinsight-hbase-overview.md).

Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/) .

Pour plus d'informations sur D3.js, consultez la page [D3.js : documents pilotés par les données](http://d3js.org/).

Pour plus d'informations sur la création de topologies en Java, consultez [Développement de topologies Java pour Apache Storm sur HDInsight](hdinsight-storm-develop-java-topology.md).

Pour plus d'informations sur la création de topologies en .NET, consultez [Développement de topologies C# pour Apache Storm sur HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

