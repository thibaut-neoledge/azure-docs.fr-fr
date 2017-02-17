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
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: b19e8b8e6f90ad502799fafd70ad6838d6e6ba4d
ms.openlocfilehash: 215698f2089934eac549e36644f0bfd4247fe2b9


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analyser les données de capteur avec Apache Storm, Event Hub, et HBase dans HDInsight (Hadoop)
Découvrez comment utiliser Apache Storm dans HDInsight pour traiter les données de capteur à partir d’Azure Event Hub, les stocker sous Apache HBase sur HDInsight et les visualiser à l’aide de D3.js s’exécutant en tant qu’application web Azure.

Le modèle Azure Resource Manager utilisé dans ce document montre comment créer plusieurs ressources Azure dans un groupe de ressources. Plus précisément, il crée un réseau virtuel Azure, deux clusters HDInsight (Storm et HBase) et une application web Azure. Une implémentation node.js de tableau de bord web en temps réel est automatiquement déployée sur l’application web.

> [!NOTE]
> Les informations contenues dans ce document et l’exemple fourni ont été testés à l’aide de versions de cluster HDInsight sous Linux 3.3 et 3.4.
>
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis
* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > Vous n’avez pas besoin d’un cluster HDInsight existant ; les étapes décrites dans ce document crée les ressources suivantes :
  > 
  > * un réseau virtuel Azure ;
  > * un cluster Storm sur HDInsight (basé sur Linux, 2 nœuds de travail) ;
  > * un cluster HBase sur HDInsight (basé sur Linux, 2 nœuds de travail) ;
  > * une application web Azure qui héberge le tableau de bord web.
  > 
  > 
* [Node.js](http://nodejs.org/): utilisé pour afficher un aperçu du tableau de bord web localement sur votre environnement de développement.
* [Java et JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): utilisés pour développer la topologie Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): utilisé pour générer et compiler le projet.
* [Git](http://git-scm.com/): utilisé pour télécharger le projet à partir de GitHub.
* Client **SSH** : utilisé pour se connecter aux clusters HDInsight basés sous Linux. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants.
  
  * [Utilisation de SSH avec HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
  * [Utilisation de SSH avec HDInsight à partir d’un client Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    > [!NOTE]
    > Vous devez également avoir accès à la commande `scp`, qui est utilisée pour copier des fichiers entre votre environnement de développement local et le cluster HDInsight à l’aide de SSH.
    > 
    > 

## <a name="architecture"></a>Architecture
![Diagramme d'architecture](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple est constitué des composants suivants :

* **Azure Event Hubs**: contient les données collectées à partir des capteurs. Pour cet exemple, une application est fournie pour générer des données.
* **Storm sur HDInsight**: fournit le traitement en temps réel des données à partir d’Event Hub.
* **HBase sur HDInsight**: fournit un magasin de données NoSQL persistant pour les données une fois celles-ci traitées par Storm.
* **Service Azure Virtual Network**: permet des communications sécurisées entre Storm sur HDInsight et les clusters HBase sur HDInsight.
  
  > [!NOTE]
  > Un réseau virtuel est requis pour pouvoir utiliser l’API cliente Java HBase, car elle n’est pas exposée via la passerelle publique pour les clusters HBase. L’installation de clusters HBase et Storm dans le même réseau virtuel permet au cluster Storm (ou à tout autre système sur le réseau virtuel) d’accéder directement à HBase à l’aide de l’API cliente.
  > 
  > 
* **Site web de tableau de bord**: un exemple de tableau de bord qui suit des données en temps réel.
  
  * Le site web est implémenté dans Node.js, donc il peut s'exécuter sur n'importe quel système d'exploitation de client de test, ou il peut être déployé sur des sites web Azure.
  * [Socket.io](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie Storm et le site web.
    
    > [!NOTE]
    > Il s'agit d'un détail d'implémentation. Vous pouvez utiliser toute infrastructure de communications, telles que WebSockets brut ou SignalR.
    > 
    > 
  * [D3.js](http://d3js.org/) est utilisé pour représenter par un graphique les données envoyées au site web.

> [!IMPORTANT]
> Deux clusters sont nécessaires, car il n’existe aucune méthode prise en charge pour créer un cluster HDInsight pour Storm et HBase.
> 
> 

La topologie lit les données à partir d’Event Hub à l’aide de la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) et écrit des données dans HBase à l’aide de la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). La communication avec le site web s'effectue à l'aide de [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Voici un schéma de cette solution :

![diagramme de topologie](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Il s'agit d'un affichage très simplifié de la topologie. Au moment de l'exécution, une instance de chaque composant est créée pour chaque partition pour le hub d'événements en cours de lecture. Ces instances sont réparties entre les nœuds du cluster et les données sont acheminées entre eux comme suit :
> 
> * Les données du spout vers l'analyseur disposent d’un équilibrage de charges.
> * Les données de l’analyseur vers le tableau de bord et HBase sont regroupées par ID de périphérique afin que les messages issus du même périphérique passent toujours par le même composant.
> 
> 

### <a name="topology-components"></a>Composants de la topologie
* **Spout EventHub**: spout fourni dans le cadre de la version d’Apache Storm 0.10.0 et versions ultérieures.
  
  > [!NOTE]
  > Le spout Event Hubs utilisé dans cet exemple requiert une version de cluster Storm sur HDInsight 3.3 ou 3.4. Pour plus d’informations sur l’utilisation des Event Hubs avec une version précédente de Storm sur HDInsight, voir [Traitement d’événements à partir d’Azure Event Hubs avec Storm dans HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).
  > 
  > 
* **ParserBolt.java**: les données émises par le spout sont JSON brut, et parfois, plusieurs événements sont émis à la fois. Ce bolt montre comment lire les données émises par le spout et les émet à un nouveau stream sous forme de tuple contenant plusieurs champs.
* **DashboardBolt.java**: montre comment utiliser la bibliothèque client Socket.io pour Java pour envoyer des données en temps réel au tableau de bord web.

Cet exemple utilise l’infrastructure [Flux](https://storm.apache.org/releases/0.10.0/flux.html) ; la définition de la topologie est donc contenue dans les fichiers YAML. Ces fichiers sont au nombre de deux :

* **no-hbase.yaml** : utilisez ce fichier lors du test de la topologie dans votre environnement de développement. Il n’utilise pas les composants HBase, étant donné que vous ne pouvez pas accéder à l’API Java HBase depuis l’extérieur du réseau virtuel sur lequel le cluster se trouve.
* **with-hbase.yaml** : utilisez ce fichier lors du déploiement de la topologie sur le cluster Storm. Il utilise les composants HBase, car il s’exécute dans le même réseau virtuel que le cluster HBase.

## <a name="prepare-your-environment"></a>Préparation de votre environnement
Avant d’utiliser cet exemple, vous devez créer un Azure Event Hub, qui peut être lu par la topologie Storm.

### <a name="configure-event-hub"></a>Configuration du hub d'événements
Le hub d'événements est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau hub d'événements.

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ Nouveau** -> **Internet des objets** -> **Hubs d’événements**.
2. Sur le panneau **Créer un espace de noms** , effectuez les tâches suivantes :
   
   1. Entrez un **nom** pour l’espace de noms.
   2. Sélectionnez un niveau tarifaire. **De base** est suffisant pour cet exemple.
   3. Sélectionnez **l’abonnement** Azure à utiliser.
   4. Sélectionnez un groupe de ressources existant ou créez-en un.
   5. Sélectionnez **l’emplacement** du concentrateur d’événements.
   6. Sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.
3. Lorsque le processus de création est terminé, le panneau Hubs d’événements de votre espace de noms s’affiche. À partir d’ici, sélectionnez **+ Ajouter un hub d’événements**. Sur le panneau **Create Event Hub** (Créer un concentrateur d’événements), entrez un nom de **sensordata**, puis sélectionnez **Créer**. Pour les autres champs, utilisez les valeurs par défaut.
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

Une fois la commande terminée, vous aurez la structure de répertoire suivante :

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [!NOTE]
> Ce document ne détaille pas le code inclus dans cet exemple ; toutefois, le code est entièrement commenté.
> 
> 

Ouvrez le fichier **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** et ajoutez les informations de votre concentrateur d’événements pour les lignes suivantes :

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [!NOTE]
> Cet exemple suppose que vous avez utilisé **storm** comme nom de la stratégie qui possède une revendication **Listen**, et que votre concentrateur d’événements se nomme **sensordata**.
> 
> 

 Enregistrez le fichier après avoir ajouté ces informations.

## <a name="compile-and-test-locally"></a>Compilation et test local
Avant de tester, vous devez lancer le tableau de bord pour afficher la sortie de la topologie et de générer les données à stocker dans le hub d'événements.

> [!IMPORTANT]
> Le composant HBase de cette topologie n’est pas actif lors d’un test local, car l’API Java pour le cluster HBase n’est pas accessible depuis l’extérieur du réseau virtuel Azure qui contient les clusters.
> 
> 

### <a name="start-the-web-application"></a>Démarrage de l’application Web
1. Ouvrez une nouvelle invite de commandes ou un terminal, et remplacez les répertoires par **hdinsight-eventhub-example/dashboard**, puis utilisez la commande suivante pour installer les dépendances requises par l'application Web :
   
        npm install
2. Utilisez la commande suivante pour démarrer l’application Web.
   
        node server.js
   
    Un message similaire à celui ci-dessous doit s'afficher :
   
        Server listening at port 3000
3. Ouvrez un navigateur web et entrez **http://localhost:3000/** comme adresse. Une page similaire à celle ci-dessous doit s'afficher :
   
    ![tableau de bord web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Laissez l’invite de commandes ou le terminal ouvert(e). Après le test, utilisez Ctrl-C pour arrêter le serveur Web.

### <a name="start-generating-data"></a>Démarrage de la génération de données
> [!NOTE]
> Les étapes de cette section utilisent Node.js de sorte à pouvoir être utilisées sur n’importe quelle plate-forme. Pour d'autres exemples de langage, consultez le répertoire **SendEvents** .
> 
> 

1. Ouvrez une nouvelle invite de commandes, un interpréteur de commandes ou un terminal, et remplacez les répertoires par **hdinsight-eventhub-example/SendEvents/nodejs**, puis utilisez la commande suivante pour installer les dépendances requises par l’application :
   
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
   
    Vous devez normalement voir plusieurs lignes de sortie contenant les données envoyées au hub d'événements. Les informations se présentent alors comme suit :
   
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
   
        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
   
    Si vous utilisez PowerShell, utilisez plutôt ce qui suit :
   
        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"
   
   > [!NOTE]
   > Si vous êtes sur un système Unix/Linux/OS X et que vous avez [installé Storm dans votre environnement de développement](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), vous pouvez utiliser les commandes suivantes à la place :
   > 
   > `mvn compile package`
   > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`
   > 
   > 
   
    Démarre la topologie définie dans le fichier **no-hbase.yaml** en mode local. Les valeurs contenues dans le fichier **dev.properties** fournissent des informations de connexion pour Event Hubs. Une fois démarrée, la topologie lit les entrées à partir du concentrateur d’événements et les envoie au tableau de bord s’exécutant sur votre ordinateur local. Vous devriez voir des lignes apparaître dans le tableau de bord web, comme ceci :
   
    ![tableau de bord avec données](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)
2. Lorsque le tableau de bord est en cours d’exécution, utilisez la commande `node app.js` des étapes précédentes pour envoyer de nouvelles données aux Event Hubs. Les valeurs de température étant générées au hasard, le graphique doit être mis à jour pour afficher les modifications importantes de température.
   
   > [!NOTE]
   > Vous devez être dans le répertoire **hdinsight-eventhub-example/SendEvents/Nodejs** lorsque vous utilisez la commande `node app.js`.
   > 
   > 
3. Après avoir vérifié que cela fonctionne, arrêtez la topologie en tapant Ctrl + C. Vous pouvez également utiliser Ctrl+C pour arrêter le serveur web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Créer un cluster Storm et HBase
Pour exécuter la topologie sur HDInsight et activer le bolt HBase, vous devez créer un cluster Storm et un cluster HBase. Les étapes décrites dans cette section utilisent un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) pour créer un réseau virtuel Azure et des clusters Storm et HBase sur le réseau virtuel. Le modèle crée également une application web Azure et déploie une copie du tableau de bord dans celle-ci.

> [!NOTE]
> Un réseau virtuel est utilisé afin que la topologie en cours d’exécution sur le cluster Storm puisse communiquer directement avec le cluster HBase à l’aide de l’API Java HBase.
> 
> 

Le modèle Resource Manager utilisé dans ce document se trouve dans un conteneur d’objets blob public, disponible à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Cliquez sur le bouton suivant pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. À partir du panneau **Paramètres** , saisissez les informations suivantes :
   
    ![Paramètres HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **BASECLUSTERNAME**: cette valeur sera utilisée comme nom de base pour les clusters Storm et HBase. Par exemple, si vous entrez **hdi**, cela crée un cluster Storm nommé **storm-hdi** et un cluster HBase nommé **hbase-hdi**.
   * **CLUSTERLOGINUSERNAME**: nom d’utilisateur Admin pour les clusters Storm et HBase.
   * **CLUSTERLOGINPASSWORD**: mot de passe utilisateur Admin pour les clusters Storm et HBase.
   * **SSHUSERNAME**: utilisateur SSH permettant de créer des clusters Storm et HBase.
   * **SSHPASSWORD**: mot de passe de l’utilisateur SSH pour les clusters Storm et HBase.
   * **EMPLACEMENT**: région dans laquelle seront créés les clusters.
     
     Cliquez sur **OK** pour enregistrer les paramètres.
3. Utilisez la section **Groupe de ressources** pour créer un groupe de ressources Azure ou sélectionnez un groupe existant.
4. Dans le menu déroulant **Emplacement du groupe de ressources**, sélectionnez le même emplacement que celui que vous avez sélectionné pour le paramètre **EMPLACEMENT**.
5. Sélectionnez **Conditions juridiques**, puis sélectionnez **Créer**.
6. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Créer**. La création des clusters prendra environ 20 minutes.

Une fois les ressources créées, vous êtes redirigé vers un panneau pour le groupe de ressources contenant les clusters et le tableau de bord web.

![Volet du groupe de ressources pour les clusters et le réseau virtuel](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **storm-BASENAME** et **hbase-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utiliserez ces noms dans les étapes ultérieures, lors de la connexion aux clusters. Notez également que le nom du site du tableau de bord est **basename-dashboard**. Vous l’utiliserez ultérieurement lors de l’affichage du tableau de bord.
> 
> 

## <a name="configure-the-dashboard-bolt"></a>Configurer le bolt de tableau de bord
Pour envoyer des données au tableau de bord déployé en tant qu’application web, vous devez modifier la ligne suivante dans le fichier **dev.properties** :

    dashboard.uri: http://localhost:3000

Modifiez `http://localhost:3000` en `http://BASENAME-dashboard.azurewebsites.net` et enregistrez le fichier. Remplacez **BASENAME** par le nom de base que vous avez fourni à l’étape précédente. Vous pouvez également utiliser le groupe de ressources créé précédemment pour sélectionner le tableau de bord et afficher l’URL.

## <a name="create-the-hbase-table"></a>Créer une table HBase
Afin de stocker des données dans HBase, nous devons tout d’abord créer une table. En général, vous devez créer au préalable les ressources sur lesquelles Storm doit écrire, car la création de ressources dans une topologie Storm peut faire que plusieurs copies distribuées du code essaient de créer la même ressource. Créez les ressources en dehors de la topologie et utilisez simplement Storm pour les opérations de lecture/écriture et d’analyse.

1. Utilisez le protocole SSH pour vous connecter au cluster HBase à l’aide de l’utilisateur et du mot de passe SSH fournis pour le modèle lors de la création du cluster. Par exemple, si vous vous connectez à l’aide de la commande `ssh` , vous devez utiliser la syntaxe suivante :
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    Dans cette commande, remplacez **USERNAME** par le nom d’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.
2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.
   
        hbase shell
   
    Une fois l’interpréteur de commandes chargé, vous verrez une invite `hbase(main):001:0>` .
3. À partir de l'environnement de ligne de commande HBase, entrez la commande suivante pour créer une table dans laquelle seront stockées les données du capteur :
   
        create 'SensorData', 'cf'
4. Vérifiez que la table a été créée à l’aide de la commande suivante :
   
        scan 'SensorData'
   
    Cela renvoie des informations similaires à l’exemple suivant, indiquant que la table comporte 0 ligne.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds
5. Pour quitter l’interpréteur de commandes HBase, utilisez la commande suivante :
   
        exit

## <a name="configure-the-hbase-bolt"></a>Configuration du bolt HBase

Pour écrire dans HBase à partir du cluster Storm , vous devez fournir le bolt HBase avec les détails de configuration de votre cluster HBase. Pour ce faire, le moyen le plus simple consiste à télécharger le fichier **hbase-site.XML** depuis le cluster et à l’inclure dans votre projet. 

> [!IMPORTANT]
> Vous devez aussi télécharger le fichier storm-hbase.jar fourni sur votre cluster Storm sur HDInsight 3.3 ou 3.4 ; cette version est compilée pour fonctionner avec HBase 1.1.x, utilisé pour les clusters HBase sur HDInsight 3.3 et 3.4. Si vous utilisez un composant storm-hbase à partir d’un autre emplacement, il peut être compilé avec une version antérieure de HBase.

### <a name="download-the-hbase-sitexml"></a>Télécharger le fichier hbase-site.xml
À partir d’une invite de commande, utilisez SCP pour télécharger le fichier **hbase-site.XML** depuis le cluster. Dans l’exemple suivant, remplacez **USERNAME** par l’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH. Remplacez `/path/to/TemperatureMonitor/resources/hbase-site.xml` par le chemin d’accès à ce fichier dans le projet TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Cela télécharge le fichier **hbase-site.XML** vers chemin d’accès spécifié.

### <a name="download-and-install-the-storm-hbase-component"></a>Télécharger et installer le composant storm-hbase
1. À partir d’une invite de commande, utilisez SCP pour télécharger le fichier **storm-hbase.jar** depuis le cluster Storm. Dans l’exemple suivant, remplacez **USERNAME** par l’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.
   
        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .
   
    Cela télécharge un fichier nommé `storm-hbase-####.jar`, où ### est le numéro de version de Storm pour ce cluster. Notez ce numéro, car vous en aurez besoin ultérieurement.
2. Utilisez la commande suivante pour installer ce composant dans le référentiel Maven local de votre environnement de développement. Cela permet à Maven de trouver le package lors de la compilation du projet. Remplacez **####** par le numéro de version inclus dans le nom de fichier.
   
        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
   
    Si vous utilisez PowerShell, utilisez la commande suivante :
   
        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

3. Dans le fichier __pom.xml__, recherchez la section de dépendance pour __storm-hbase__. Supprimez les marques de commentaires de la dépendance en supprimant `<!--` et `-->` entourant la dépendance. Modifiez également l’entrée `<version></version>` pour faire correspondre le ### utilisé dans les étapes précédentes. L’entrée ressemblera à l’exemple suivant :

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>0.10.0.2.4.2.4-5</version>
        </dependency>

   Enregistrez le fichier après avoir apporté les modifications.

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Génération, mise en package et déploiement de la solution vers HDInsight

Dans votre environnement de développement, suivez les étapes ci-dessous pour déployer la topologie Storm vers le cluster Storm.

1. Depuis le répertoire **TemperatureMonitor** , utilisez la commande suivante pour exécuter une nouvelle version et créer un package JAR à partir de votre projet :
   
        mvn clean compile package
   
    Ceci va créer un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** in the **target** de votre projet.
2. Utilisez SCP pour télécharger le fichier **TemperatureMonitor-1.0-SNAPSHOT.jar** vers votre cluster Storm. Dans l’exemple suivant, remplacez **USERNAME** par l’utilisateur SSH fourni lors de la création du cluster et **BASENAME** par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
   
   > [!NOTE]
   > Le téléchargement du fichier peut prendre plusieurs minutes en raison de sa taille.
   > 
   > 
   
    Utilisez scp pour charger le fichier **dev.properties** , car il contient les informations utilisées pour se connecter à Event Hubs et au tableau de bord.
   
        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
3. Une fois les fichiers chargés, connectez-vous au cluster à l’aide de SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
4. Dans la session SSH, utilisez la commande suivante pour démarrer la topologie.
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
   
    Cela démarre la topologie à l’aide de la définition de topologie dans le fichier **with-hbase.yaml** et les valeurs de configuration dans le fichier **dev.properties**.
5. Une fois la topologie lancée, ouvrez un navigateur vers le site Web que vous avez publié sur Azure, puis utiliser la commande `node app.js` pour envoyer des données au concentrateur d'événements. Vous devez mettre à jour le tableau de bord Web pour afficher les informations.
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Afficher les données HBase
Une fois que vous avez envoyé des données à la topologie à l’aide de `node app.js`, procédez comme suit pour vous connecter à HBase et vérifiez que les données ont été écrites dans la table que vous avez créée précédemment.

1. Utilisez SSH pour vous connecter au cluster HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.
   
        hbase shell
   
    Une fois l’interpréteur de commandes chargé, vous verrez une invite `hbase(main):001:0>` .
3. Affichez les lignes de la table :
   
        scan 'SensorData'
   
    Cela doit renvoyer des informations similaires à celles ci-dessous, indiquant que la table comporte 0 ligne.
   
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
   > Cette opération d’analyse renvoie uniquement une valeur maximale de 10 lignes pour la table.
   > 
   > 

## <a name="delete-your-clusters"></a>Supprimer vos clusters
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour supprimer simultanément les clusters, le stockage et l’application web, supprimez le groupe de ressources qui les contient.

## <a name="next-steps"></a>Étapes suivantes
Vous savez désormais utiliser Storm pour lire des données à partir d’Event Hubs, les stocker dans HBase et afficher les informations sur un tableau de bord externe en utilisant Socket.io et D3.js.

* Pour plus d’exemples de topologies Storm avec HDinsight, consultez :
  
  * [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)
* Pour plus d'informations sur Apache Storm, consultez le [Apache Storm](https://storm.incubator.apache.org/) site.
* Pour plus d'informations sur HBase avec HDInsight, veuillez consulter la [Présentation de HBase avec HDInsight](hdinsight-hbase-overview.md).
* Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/) .
* Pour plus d'informations sur D3.js, consultez la page [D3.js : documents pilotés par les données](http://d3js.org/).
* Pour plus d'informations sur la création de topologies en Java, consultez [Développement de topologies Java pour Apache Storm sur HDInsight](hdinsight-storm-develop-java-topology.md).
* Pour plus d'informations sur la création de topologies en .NET, consultez [Développement de topologies C# pour Apache Storm sur HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com



<!--HONumber=Jan17_HO3-->


