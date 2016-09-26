<properties
   pageTitle="Analyse des données de capteur avec Apache Storm et HBase | Microsoft Azure"
   description="Découvrez comment vous connecter à Apache Storm à l’aide d’un réseau virtuel. Utilisez Storm avec HBase pour traiter des données de capteur à partir d'un hub d'événements et les visualiser avec D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/05/2016"
   ms.author="larryfr"/>

# Analyser les données de capteur avec Apache Storm, Event Hub, et HBase dans HDInsight (Hadoop) 

Découvrez comment utiliser Apache Storm dans HDInsight pour traiter les données de capteur à partir d’Azure Event Hub, les stocker sous Apache HBase sur HDInsight et les visualiser à l’aide de D3.js s’exécutant en tant qu’application web Azure.

Le modèle Azure Resource Manager utilisé dans ce document montre comment créer plusieurs ressources Azure dans un groupe de ressources. Plus précisément, il crée un réseau virtuel Azure, deux clusters HDInsight (Storm et HBase) et une application web Azure. Une implémentation node.js de tableau de bord web en temps réel est automatiquement déployée sur l’application web.

> [AZURE.NOTE] Les informations contenues dans ce document et l’exemple fourni ont été testés à l’aide de versions de cluster HDInsight sous Linux 3.3 et 3.4.

## Composants requis

* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Vous n’avez pas besoin d’un cluster HDInsight existant ; les étapes décrites dans ce document crée les ressources suivantes :
    >
    > * un réseau virtuel Azure ;
    > * un cluster Storm sur HDInsight (basé sur Linux, 2 nœuds de travail) ;
    > * un cluster HBase sur HDInsight (basé sur Linux, 2 nœuds de travail) ;
    > * une application web Azure qui héberge le tableau de bord web.

* [Node.js](http://nodejs.org/) : utilisé pour afficher un aperçu du tableau de bord web localement sur votre environnement de développement.

* [Java et JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) : utilisés pour développer la topologie Storm.

* [Maven](http://maven.apache.org/what-is-maven.html) : utilisé pour générer et compiler le projet.

* [Git](http://git-scm.com/) : utilisé pour télécharger le projet à partir de GitHub.

* Client __SSH__ : utilisé pour se connecter aux clusters HDInsight basés sous Linux. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants.

    * [Utilisation de SSH avec HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utilisation de SSH avec HDInsight à partir d’un client Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] Vous devez également avoir accès à la commande `scp`, qui est utilisée pour copier des fichiers entre votre environnement de développement local et le cluster HDInsight à l’aide de SSH.

## Architecture

![Diagramme d'architecture](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple est constitué des composants suivants :

* **Azure Event Hubs** : contient les données collectées à partir des capteurs. Pour cet exemple, une application est fournie pour générer des données.

* **Storm sur HDInsight** : fournit le traitement en temps réel des données à partir d’Event Hub.

* **HBase sur HDInsight** : fournit un magasin de données NoSQL persistant pour les données une fois celles-ci traitées par Storm.

* **Service Azure Virtual Network** : permet des communications sécurisées entre Storm sur HDInsight et les clusters HBase sur HDInsight.

    > [AZURE.NOTE] Un réseau virtuel est requis pour pouvoir utiliser l’API cliente Java HBase, car elle n’est pas exposée via la passerelle publique pour les clusters HBase. L’installation de clusters HBase et Storm dans le même réseau virtuel permet au cluster Storm (ou à tout autre système sur le réseau virtuel) d’accéder directement à HBase à l’aide de l’API cliente.

* **Site web de tableau de bord** : un exemple de tableau de bord qui suit des données en temps réel.

	* Le site web est implémenté dans Node.js, donc il peut s'exécuter sur n'importe quel système d'exploitation de client de test, ou il peut être déployé sur des sites web Azure.

	* [Socket.io](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie Storm et le site web.

		> [AZURE.NOTE] Il s'agit d'un détail d'implémentation. Vous pouvez utiliser toute infrastructure de communications, telles que WebSockets brut ou SignalR.

	* [D3.js](http://d3js.org/) est utilisé pour représenter par un graphique les données envoyées au site web.

La topologie lit les données à partir d’Event Hub à l’aide de la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) et écrit des données dans HBase à l’aide de la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). La communication avec le site web s'effectue à l'aide de [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Voici un schéma de cette solution :

![diagramme de topologie](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Il s'agit d'un affichage très simplifié de la topologie. Au moment de l'exécution, une instance de chaque composant est créée pour chaque partition pour le hub d'événements en cours de lecture. Ces instances sont réparties entre les nœuds du cluster et les données sont acheminées entre eux comme suit :
>
> * Les données du spout vers l'analyseur sont disposent d’un équilibrage de charges.
> * Les données de l’analyseur vers le tableau de bord et HBase sont regroupées par ID de périphérique afin que les messages issus du même périphérique passent toujours par le même composant.

### Composants de la topologie

* **Spout EventHub** : spout fourni dans le cadre de la version d’Apache Storm 0.10.0 et versions ultérieures.

    > [AZURE.NOTE] Le spout EventHub utilisé dans cet exemple requiert une version de cluster Storm sur HDInsight 3.3 ou 3.4. Pour plus d’informations sur l’utilisation des Event Hubs avec une version précédente de Storm sur HDInsight, voir [Traitement d’événements à partir d’Azure Event Hubs avec Storm dans HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: les données émises par le spout sont JSON brut, et parfois, plusieurs événements sont émis à la fois. Ce bolt montre comment lire les données émises par le spout et les émet à un nouveau stream sous forme de tuple contenant plusieurs champs.

* **DashboardBolt.java** : montre comment utiliser la bibliothèque client Socket.io pour Java pour envoyer des données en temps réel au tableau de bord web.

## Préparation de votre environnement

Avant d’utiliser cet exemple, vous devez créer un Azure Event Hub, qui peut être lu par la topologie Storm.

### Configuration du hub d'événements

Le hub d'événements est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau hub d'événements.

1. Depuis le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **NOUVEAU| Service Bus | Event Hub | Custom Create**.

2. Dans la boîte de dialogue **Ajouter un nouvel hub d'événements**, entrez un **Nom du hub d'événements**, sélectionnez la **Région** dans laquelle créer le concentrateur, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la flèche pour continuer.

2. Dans la boîte de dialogue **Configurer un hub d'événements**, entrez les valeurs pour **Nombre de partitions** et **Conservation des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages.

3. Une fois le concentrateur d'événements créé, sélectionnez l'espace de noms, puis **Concentrateurs d'événements**. Enfin, sélectionnez le hub d'événements créé précédemment.

4. Sélectionnez **Configurer**, puis créez deux nouvelles stratégies d'accès à l’aide des informations suivantes.

	| Nom | Autorisations |
    | ----- | ----- |
	| périphériques | Envoyer |
	| storm | Écouter |

	Après la création des autorisations, sélectionnez l'icône **Enregistrer** au bas de la page. Les stratégies d'accès partagées sont créées. Elles sont utilisées pour envoyer et lire des messages à partir de ce hub.

5. Après l'enregistrement des stratégies, utilisez le **Générateur de clés d'accès partagé** en bas de la page pour récupérer les clés des stratégies **périphériques** et **storm**. Enregistrez-les, car vous en aurez besoin plus tard.

## Téléchargement et configuration du projet

Utilisez la commande suivante pour télécharger le projet à partir de GitHub.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

Une fois la commande terminée, vous aurez la structure de répertoire suivante :

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this is the Java topology
			conf/
				Config.properties
				hbase-site.xml
			src/
			test/
			dashboard/ - this is the node.js web dashboard
			SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Ce document ne détaille pas le code inclus dans cet exemple ; toutefois, le code est entièrement commenté.

Ouvrez le fichier **Config.properties** et ajoutez les informations utilisées précédemment lors de la création de l’Event Hub pour les entrées répertoriées ci-dessous. Enregistrez le fichier après avoir ajouté ces informations.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

## Compilation et test local

Avant de tester, vous devez lancer le tableau de bord pour afficher la sortie de la topologie et de générer les données à stocker dans le hub d'événements.

> [AZURE.IMPORTANT] Le composant HBase de cette topologie n’est pas actif lors d’un test local, car l’API Java pour le cluster HBase n’est pas accessible depuis l’extérieur du réseau virtuel Azure qui contient les clusters.

### Démarrage de l’application Web

1. Ouvrez une nouvelle invite de commandes ou un terminal, et remplacez les répertoires par **hdinsight-eventhub-example/dashboard**, puis utilisez la commande suivante pour installer les dépendances requises par l'application Web :

		npm install

2. Utilisez la commande suivante pour démarrer l’application Web.

		node server.js

	Un message similaire à celui ci-dessous doit s'afficher :

		Server listening at port 3000

2. Ouvrez un navigateur web et entrez **http://localhost:3000/** comme adresse. Une page similaire à celle ci-dessous doit s'afficher :

	![tableau de bord web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Laissez l’invite de commandes ou le terminal ouvert(e). Après le test, utilisez Ctrl-C pour arrêter le serveur Web.

### Démarrage de la génération de données

> [AZURE.NOTE] Les étapes de cette section utilisent Node.js de sorte à pouvoir être utilisées sur n’importe quelle plate-forme. Pour d'autres exemples de langage, consultez le répertoire **SendEvents**.

1. Ouvrez une nouvelle invite de commandes ou un terminal, et remplacez les répertoires par **hdinsight-eventhub-example/dashboard**, puis utilisez la commande suivante pour installer les dépendances requises par l'application :

		npm install

2. Ouvrez le fichier **app.js** dans un éditeur de texte et ajoutez les informations du concentrateur d'événements obtenues précédemment :

		// ServiceBus Namespace
		var namespace = 'servicebusnamespace';
		// Event Hub Name
		var hubname ='eventhubname';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'key';

2. Utilisez la commande suivante pour insérer de nouvelles entrées dans le hub d'événements :

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

### Démarrage de la topologie

2. Démarrez la topologie localement à l’aide de la commande suivante :

        mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Ceci démarre la topologie, la lecture des fichiers à partir du hub d'événements et leur envoi vers le tableau de bord exécuté dans Sites Web Azure. Vous devriez voir des lignes apparaître dans le tableau de bord web, comme ceci :

	![tableau de bord avec données](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

    > [AZURE.NOTE] Si vous exécutez cette commande à partir d’une invite PowerShell, vous devez utiliser des guillemets doubles autour du paramètre `-Dstorm.topology=com.microsoft.examples.Temperature`. Par exemple : `mvn compile exec:java "-Dstorm.topology=com.microsoft.examples.Temperature"`.

3. Lorsque le tableau de bord est en cours d’exécution, utilisez la commande `node app.js` des étapes précédentes pour envoyer de nouvelles données aux Event Hubs. Les valeurs de température étant générées au hasard, le graphique doit être mis à jour pour afficher les modifications importantes de température.

3. Après avoir vérifié que cela fonctionne, arrêtez la topologie en tapant Ctrl + C. Pour arrêter l'application SendEvent, sélectionnez la fenêtre et appuyez sur une touche. Vous pouvez également utiliser Ctrl-C pour arrêter le serveur Web.

## Créer un cluster Storm et HBase

Pour exécuter la topologie sur HDInsight et activer le bolt HBase, vous devez créer un cluster Storm et un cluster HBase. Les étapes décrites dans cette section utilisent un [modèle Azure Resource Manager](../resource-group-template-deploy.md) pour créer un réseau virtuel Azure et des clusters Storm et HBase sur le réseau virtuel. Le modèle crée également une application web Azure et déploie une copie du tableau de bord dans celle-ci.

> [AZURE.NOTE] Un réseau virtuel est utilisé afin que la topologie en cours d’exécution sur le cluster Storm puisse communiquer directement avec le cluster HBase à l’aide de l’API Java HBase.

Le modèle Resource Manager utilisé dans ce document est situé dans un conteneur blob public sous \_\_https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Cliquez sur le bouton suivant pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/fr-FR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir du panneau **Paramètres**, saisissez les informations suivantes :

    ![Paramètres HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME** : cette valeur sera utilisée comme nom de base pour les clusters Storm et HBase. Par exemple, si vous entrez __hdi__, cela crée un cluster Storm nommé __storm hdi__ et un cluster HBase nommé __hbase-hdi__.
    * __CLUSTERLOGINUSERNAME__ : nom d’utilisateur Admin pour les clusters Storm et HBase.
    * __CLUSTERLOGINPASSWORD__ : mot de passe utilisateur Admin pour les clusters Storm et HBase.
    * __SSHUSERNAME__ : utilisateur SSH permettant de créer des clusters Storm et HBase.
    * __SSHPASSWORD__ : mot de passe de l’utilisateur SSH pour les clusters Storm et HBase.
    * __EMPLACEMENT__ : région dans laquelle seront créés les clusters.
    
    Cliquez sur __OK__ pour enregistrer les paramètres.
    
3. Utilisez la section __Groupe de ressources__ pour créer un groupe de ressources Azure ou sélectionnez un groupe existant.

4. Dans le menu déroulant __Emplacement du groupe de ressources__, sélectionnez le même emplacement que celui que vous avez sélectionné pour le paramètre __EMPLACEMENT__.

5. Sélectionnez __Mentions légales__, puis sélectionnez __Créer__.

6. Pour finir, cochez __Épingler au tableau de bord__, puis sélectionnez __Créer__. La création des clusters prendra environ 20 minutes.

Une fois les ressources créées, vous êtes redirigé vers un panneau pour le groupe de ressources contenant les clusters et le tableau de bord web.

![Volet du groupe de ressources pour les clusters et le réseau virtuel](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Les noms des clusters HDInsight sont __storm-BASENAME__ et __hbase-BASENAME__, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utiliserez ces noms dans les étapes ultérieures, lors de la connexion aux clusters. Notez également que le nom du site du tableau de bord est __basename-dashboard__. Vous l’utiliserez ultérieurement lors de l’affichage du tableau de bord.

## Configurer le bolt de tableau de bord

Pour envoyer des données au tableau de bord déployé en tant qu’application web, vous devez modifier la ligne suivante dans le fichier __DashboardBolt.java__ :

    socket = IO.socket("http://localhost:3000");

Modifiez `http://localhost:3000` en `http://BASENAME-dashboard.azurewebsites.net` et enregistrez le fichier. Remplacez __BASENAME__ par le nom de base que vous avez fourni à l’étape précédente. Vous pouvez également utiliser le groupe de ressources créé précédemment pour sélectionner le tableau de bord et afficher l’URL.

## Créer une table HBase

Afin de stocker des données dans HBase, nous devons tout d’abord créer une table. En général, vous devez créer au préalable les ressources sur lesquelles Storm doit écrire, car la création de ressources dans une topologie Storm peut faire que plusieurs copies distribuées du code essaient de créer la même ressource. Créez les ressources en dehors de la topologie et utilisez simplement Storm pour les opérations de lecture/écriture et d’analyse.

1. Utilisez le protocole SSH pour vous connecter au cluster HBase à l’aide de l’utilisateur et du mot de passe SSH fournis pour le modèle lors de la création du cluster. Par exemple, si vous vous connectez à l’aide de la commande `ssh`, vous devez utiliser la syntaxe suivante :

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    Dans cette commande, remplacez __USERNAME__ par le nom d’utilisateur SSH fourni lors de la création du cluster et __BASENAME__ par le nom de base que vous avez fourni. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.

    	hbase shell
	
	Une fois l’interpréteur de commandes chargé, vous verrez une invite `hbase(main):001:0>`.

3. À partir de l'environnement de ligne de commande HBase, entrez la commande suivante pour créer une table dans laquelle seront stockées les données du capteur :

    	create 'SensorData', 'cf'

4. Vérifiez que la table a été créée à l’aide de la commande suivante :

    	scan 'SensorData'
		
	Cela doit renvoyer des informations similaires à celles ci-dessous, indiquant que la table comporte 0 ligne.
	
		ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Pour quitter l’interpréteur de commandes HBase, utilisez la commande suivante :

		exit

## Configuration du bolt HBase

Pour écrire dans HBase à partir du cluster Storm , vous devez fournir le bolt HBase avec les détails de configuration de votre cluster HBase. Pour ce faire, le moyen le plus simple consiste à télécharger le fichier __hbase-site.XML__ depuis le cluster et à l’inclure dans votre projet. Vous devez également supprimer les commentaires de plusieurs dépendances dans le fichier __pom.xml__, qui chargent le composant storm-hbase et les dépendances requises.

> [AZURE.IMPORTANT] Vous devez aussi télécharger le fichier storm-hbase.jar fourni sur votre cluster Storm sur HDInsight 3.3 ou 3.4 ; cette version est compilée pour fonctionner avec HBase 1.1.x, utilisé pour les clusters HBase sur HDInsight 3.3 et 3.4. Si vous utilisez un composant storm-hbase à partir d’un autre emplacement, il peut être compilé avec une version antérieure de HBase.

### Télécharger le fichier hbase-site.xml

À partir d’une invite de commande, utilisez SCP pour télécharger le fichier __hbase-site.XML__ depuis le cluster. Dans l’exemple suivant, remplacez __USERNAME__ par l’utilisateur SSH fourni lors de la création du cluster et __BASENAME__ par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH. Remplacez `/path/to/TemperatureMonitor/conf/hbase-site.xml` par le chemin d’accès à ce fichier dans le projet TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/conf/hbase-site.xml

Cela télécharge le fichier __hbase-site.XML__ vers chemin d’accès spécifié.

### Télécharger et installer le composant storm-hbase

1. À partir d’une invite de commande, utilisez SCP pour télécharger le fichier __storm-hbase.jar__ depuis le cluster Storm. Dans l’exemple suivant, remplacez __USERNAME__ par l’utilisateur SSH fourni lors de la création du cluster et __BASENAME__ par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Cela télécharge un fichier nommé `storm-hbase-####.jar`, où ### est le numéro de version de Storm pour ce cluster. Notez ce numéro, car vous en aurez besoin ultérieurement.

2. Utilisez la commande suivante pour installer ce composant dans le référentiel Maven local de votre environnement de développement. Cela permet à Maven de trouver le package lors de la compilation du projet. Remplacez __###__ par le numéro de version inclus dans le nom de fichier.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar

### Activer le composant storm-hbase dans le projet

1. Ouvrez le fichier __TemperatureMonitor/pom.xml__ et supprimez les lignes suivantes :

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Supprimez uniquement ces deux lignes ; ne supprimez pas les lignes comprises entre ces dernières.
    
    Cela permet d’activer plusieurs composants nécessaires à la communication avec HBase à l’aide du bolt hbase.

2. Recherchez les lignes suivantes, puis remplacez __###__ par le numéro de version du fichier storm-hbase que vous avez téléchargé précédemment.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] Le numéro de version doit correspondre à la version que vous avez utilisée lors de l’installation du composant dans le référentiel Maven local, car Maven utilise ces informations pour charger le composant lors de la génération du projet.

2. Enregistrez le fichier __pom.xml__.

3. Ouvrez **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.jav** dans un éditeur de texte et annulez les marques de commentaires dans les lignes suivantes en supprimant le `//` du début.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Ceci active le bolt HBase.

	> [AZURE.NOTE] Vous devez uniquement activer le bolt HBase lorsque vous déployez sur le cluster storm, pas lorsque vous testez localement.

4. Enregistrez le fichier __Temperature.java__.
    
## Génération, mise en package et déploiement de la solution vers HDInsight

Dans votre environnement de développement, suivez les étapes ci-dessous pour déployer la topologie Storm vers le cluster Storm.

1. Utilisez la commande suivante pour exécuter la nouvelle version et créer un package JAR depuis votre projet :

		mvn clean compile package

	Ceci va créer un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** dans le répertoire **target** de votre projet.

2. Utilisez SCP pour télécharger le fichier __TemperatureMonitor-1.0-SNAPSHOT.jar__ vers votre cluster Storm. Dans l’exemple suivant, remplacez __USERNAME__ par l’utilisateur SSH fourni lors de la création du cluster et __BASENAME__ par le nom de base que vous avez fourni précédemment. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:
    
    > [AZURE.NOTE] Le téléchargement du fichier peut prendre plusieurs minutes en raison de sa taille.

3. Une fois le fichier téléchargé, connectez-vous au cluster à l’aide de SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Dans la session SSH, utilisez la commande suivante pour démarrer la topologie.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature tempmonitor
    
    Cela démarre la topologie à l’aide de la classe __com.microsoft.examples.Temperature__ contenue dans le package, en utilisant __tempmonitor__ comme nom convivial pour cette instance de la topologie.

3. Une fois la topologie lancée, ouvrez un navigateur vers le site Web que vous avez publié sur Azure, puis utiliser la commande `node app.js` pour envoyer des données au concentrateur d'événements. Vous devez mettre à jour le tableau de bord Web pour afficher les informations.

	![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Afficher les données HBase

Une fois que vous avez envoyé des données à la topologie à l’aide de `node app.js`, procédez comme suit pour vous connecter à HBase et vérifiez que les données ont été écrites dans la table que vous avez créée précédemment.

1. Utilisez SSH pour vous connecter au cluster HBase.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.

    	hbase shell
	
	Une fois l’interpréteur de commandes chargé, vous verrez une invite `hbase(main):001:0>`.

2. Affichez les lignes de la table :

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

    > [AZURE.NOTE] Cette opération d’analyse renvoie uniquement une valeur maximale de 10 lignes pour la table.

## Supprimer vos clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour supprimer simultanément les clusters, le stockage et l’application web, supprimez le groupe de ressources qui les contient.

## Étapes suivantes

Vous avez à présent appris à utiliser Storm pour lire des données à partir des Event Hubs, à stocker des données dans HBase et à afficher des informations sur un tableau de bord externe en utilisant Socket.io et D3.js.

* Pour plus d'exemples de topologies Storm avec HDinsight, consultez :

    * [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

* Pour plus d'informations sur Apache Storm, consultez le [Apache Storm](https://storm.incubator.apache.org/) site.

* Pour plus d'informations sur HBase avec HDInsight, veuillez consulter la [Présentation de HBase avec HDInsight](hdinsight-hbase-overview.md).

* Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/).

* Pour plus d'informations sur D3.js, consultez la page [D3.js : documents pilotés par les données](http://d3js.org/).

* Pour plus d'informations sur la création de topologies en Java, consultez [Développement de topologies Java pour Apache Storm sur HDInsight](hdinsight-storm-develop-java-topology.md).

* Pour plus d'informations sur la création de topologies en .NET, consultez [Développement de topologies C# pour Apache Storm sur HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->