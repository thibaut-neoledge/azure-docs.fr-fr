<properties
   pageTitle="Analyse des données de capteur avec Apache Storm et HBase | Microsoft Azure"
   description="Découvrez comment vous connecter à Apache Storm à l’aide d’un réseau virtuel. Utilisez Storm avec HBase pour traiter des données de capteur à partir d'un concentrateur d'événements et les visualiser avec D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

# Analyser les données de capteur avec Apache Storm, Event Hub, et HBase dans HDInsight (Hadoop)

Découvrez comment utiliser Apache Storm dans HDInsight pour traiter les données de capteur à partir d'Azure Event Hub et les visualiser à l'aide de D3.js. Ce document décrit également comment utiliser un réseau virtuel Azure pour connecter Storm sur HDInsight avec HBase sur HDInsight et stocker les données de la topologie dans HBase.

> [AZURE.NOTE]Les informations de ce document sont basées sur l'utilisation d'un cluster Storm Windows sur HDInsight. Pour plus d'informations sur l'utilisation d'Azure Event Hub avec Storm Linux sur HDInsight, consultez [Traitement d'événements à partir d'Azure Event Hubs avec Storm dans HDInsight](hdinsight-storm-develop-java-event-hub-topology.md)

## Configuration requise

* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un [cluster Apache Storm sur HDInsight](../hdinsight-storm-getting-started.md)

* [Node.js](http://nodejs.org/): utilisé pour le tableau de bord Web et pour envoyer des données de capteur à Event Hub.

* [Java et le Kit de développement Java (JDK) 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE]Java, le Kit de développement Java (JDK), Maven et Git sont également disponibles via le gestionnaire de package [Chocolatey NuGet](http://chocolatey.org/).

## Architecture

![Diagramme d'architecture](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple est constitué des composants suivants :

* **Azure Event Hub** : fournit des données collectées à partir des capteurs. Pour cet exemple, une application est fournie pour générer des données fictives.

* **Storm sur HDInsight** : fournit le traitement en temps réel des données à partir d’Event Hub.

* **HBase sur HDInsight** (facultatif) : fournit un magasin de données NoSQL persistant.

* **Service Azure Virtual Network** (facultatif, requis si vous utilisez HBase) : permet des communications sécurisées entre le Storm sur HDInsight et les clusters HBase sur HDInsight.

* **Site web de tableau de bord** : un exemple de tableau de bord qui suit des données en temps réel.

	* Le site web est implémenté dans Node.js, donc il peut s'exécuter sur n'importe quel système d'exploitation de client de test, ou il peut être déployé sur des sites web Azure.

	* [Socket.io](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie Storm et le site web.

		> [AZURE.NOTE]Il s'agit d'un détail d'implémentation. Vous pouvez utiliser toute infrastructure de communications, telles que WebSockets brut ou SignalR.

	* [D3.js](http://d3js.org/) est utilisé pour représenter par un graphique les données envoyées au site web.

La topologie lit les données à partir du concentrateur d'événements à l'aide de la classe **com.microsoft.eventhubs.spout.EventHubSpout** qui est fournie dans le cluster Storm sur HDInsight. La communication avec le site web s'effectue à l'aide de [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Le cas échéant, la communication avec HBase s'effectue à l'aide de la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) qui est fournie dans le cadre de Storm.

Voici un schéma de cette solution :

![diagramme de topologie](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE]Il s'agit d'un affichage très simplifié de la topologie. Au moment de l'exécution, une instance de chaque composant est créée pour chaque partition pour le concentrateur d'événements en cours de lecture. Ces instances sont réparties entre les nœuds du cluster et les données sont acheminées entre eux comme suit :
>
> * Les données du spout vers l'analyseur sont disposent d’un équilibrage de charges.
> * Les données de l'analyseur vers le tableau de bord et HBase (si utilisé) sont regroupées par ID de périphérique afin que les messages issus du même périphérique passe toujours par le même composant.

### Composants

* **Spout Event Hub** : spout fourni dans le cadre des [exemples HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples) sur GitHub.

* **ParserBolt.java**: les données émises par le spout sont JSON brut, et parfois, plusieurs événements sont émis à la fois. Ce bolt montre comment lire les données émises par le spout et les émet à un nouveau stream sous forme de tuple contenant plusieurs champs.

* **DashboardBolt.java** : cela montre comment utiliser la bibliothèque client Socket.io pour Java pour envoyer des données en temps réel au tableau de bord web.

## Préparation de votre environnement

Avant d'utiliser cet exemple, vous devez créer un concentrateur d'événements Azure, qui peut être lu par la topologie Storm. Vous devez également créer une topologie Storm sur HDInsight, car le composant utilisé pour lire les données à partir d’Event Hub est uniquement disponible dans le cluster.

> [AZURE.NOTE]Le spout Event Hub sera disponible à partir de Maven.

### Configuration du concentrateur d'événements

Le hub d'événements est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau concentrateur d'événements.

1. À partir du [portail Azure](https://manage.windowsazure.com), sélectionnez **NOUVEAU | Service Bus | Concentrateur d'événements | Création personnalisée**.

2. Dans la boîte de dialogue **Ajouter un nouveau concentrateur d'événements**, entrez un **Nom de concentrateur d'événements**, sélectionnez la **Région** dans laquelle créer le concentrateur, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la flèche pour continuer.

2. Dans la boîte de dialogue **Configurer le concentrateur d'événements**, entrez les valeurs pour **Nombre de partitions** et **Conservation des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages.

3. Une fois le concentrateur d'événements créé, sélectionnez l'espace de noms, puis **Concentrateurs d'événements**. Enfin, sélectionnez le concentrateur d'événements créé précédemment.

4. Sélectionnez **Configurer**, puis créez deux nouvelles stratégies d'accès à l’aide des informations suivantes.

	<table>
<tr><th>Nom</th><th>Autorisations</th></tr>
<tr><td>Appareils</td><td>Envoyer</td></tr>
<tr><td>Storm</td><td>Écouter</td></tr>
</table>Après la création des autorisations, sélectionnez l'icône **Enregistrer** au bas de la page. Les stratégies d'accès partagées sont créées. Elles sont utilisées pour envoyer et lire des messages à partir de ce concentrateur.

5. Après l'enregistrement des stratégies, utilisez le **Générateur de clés d'accès partagé** en bas de la page pour récupérer les clés des stratégies **périphériques** et **storm**. Enregistrez-les, car vous en aurez besoin plus tard.

### Création du storm sur le clsuter HDInsight

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).

2. Cliquez sur **HDInsight** dans le volet de gauche, puis sur **+NOUVEAU** dans le coin inférieur gauche de la page.

3. Cliquez sur l'icône HDInsight dans la seconde colonne, puis sélectionnez **Personnaliser**.

4. Sur la page **Détails du cluster**, entrez le nom du nouveau cluster, puis sélectionnez **Storm** pour le **Type de cluster**. Cliquez sur la flèche pour continuer.

5. Entrez 1 pour la valeur de **Nœuds de données** à utiliser pour ce cluster.

	> [AZURE.NOTE]Pour réduire les coûts du cluster utilisé pour cet article, réduisez la **Taille du cluster** sur 1, puis supprimez le cluster après avoir fini de l'utiliser.

6. Entrez le **Nom d'utilisateur** d'administrateur et un **Mot de passe**, puis cliquez sur la flèche pour continuer.

4. Pour **Compte de stockage**, sélectionnez **Créer un nouveau stockage** ou sélectionnez un compte de stockage existant. Sélectionnez ou entrez le **Nom de compte** et le **Conteneur par défaut** à utiliser. Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster Storm.

## Téléchargement et installation de l’agent EventHubSpout

1. Téléchargez le [projet d'exemples de Storm HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/). Une fois téléchargé, recherchez le fichier **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

2. À partir de l'invite de commandes, utilisez la commande suivante pour installer le fichier **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** dans le magasin local Maven. Ceci vous permettra de l'ajouter facilement en tant que référence dans le projet Storm à l'étape suivante.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

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

> [AZURE.NOTE]Ce document ne détaille pas le code inclus dans cet exemple ; toutefois, le code est entièrement commenté.

Ouvrez le fichier **Config.properties** et ajoutez les informations utilisées précédemment lors de la création du concentrateur d'événements. Enregistrez le fichier après avoir ajouté ces informations.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

	## if not provided, will use storm's zookeeper settings
	## zookeeper.connectionstring=localhost:2181

	eventhubspout.checkpoint.interval = 10

	eventhub.receiver.credits = 1024

## Compilation et test local

Avant de tester, vous devez lancer le tableau de bord pour afficher la sortie de la topologie et de générer les données à stocker dans le concentrateur d'événements.

### Démarrage de l’application Web

1. Ouvrez une nouvelle invite de commandes ou un terminal, et remplacez les répertoires par **hdinsight-eventhub-example/dashboard**, puis utilisez la commande suivante pour installer les dépendances requises par l'application Web :

		npm install

2. Utilisez la commande suivante pour démarrer l’application Web.

		node server.js

	Un message similaire à celui ci-dessous doit s'afficher :

		Server listening at port 3000

2. Ouvrez un navigateur web et entrez ****http://localhost:3000/** comme adresse. Une page similaire à celle ci-dessous doit s'afficher :

	![tableau de bord web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Laissez l’invite de commandes ou le terminal ouvert(e). Après le test, utilisez Ctrl-C pour arrêter le serveur Web.

### Démarrage de la génération de données

> [AZURE.NOTE]Les étapes de cette section utilisent Node.js de sorte à pouvoir utiliser n'importe quelle plate-forme. Pour d'autres exemples de langage, consultez le répertoire **SendEvents**.


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

2. Utilisez la commande suivante pour insérer de nouvelles entrées dans le concentrateur d'événements :

		node app.js

	Vous devez normalement voir plusieurs lignes de sortie contenant les données envoyées au concentrateur d'événements. Les informations se présentent alors comme suit :

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### Démarrage de la topologie

2. Démarrez la topologie localement à l’aide de la commande suivante :

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Ceci démarre la topologie, la lecture des fichiers à partir du concentrateur d'événements et leur envoi vers le tableau de bord exécuté dans Sites Web Azure. Vous devriez voir des lignes apparaître dans le tableau de bord web, comme ceci :

	![tableau de bord avec données](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Lorsque le tableau de bord est en cours d'exécution, utilisez la commande `node app.js` des étapes précédentes pour envoyer de nouvelles données au tableau de bord. Les valeurs de température étant générées au hasard, le graphique doit être mis à jour pour afficher les nouvelles valeurs.

3. Après avoir vérifié que cela fonctionne, arrêtez la topologie en tapant Ctrl + C. Pour arrêter l'application SendEvent, sélectionnez la fenêtre et appuyez sur une touche. Vous pouvez également utiliser Ctrl-C pour arrêter le serveur Web.

## Mise en package et déploiement de la topologie vers HDInsight

Dans votre environnement de développement, procédez comme suit pour exécuter la topologie de température sur votre cluster Storm HDInsight.

### Publication du tableau de bord du site Web

1. Pour déployer le tableau de bord sur un site Web Azure, suivez les étapes de la section [Création et déploiement d’un site Web Node.js dans Azure](../web-sites-nodejs-develop-deploy-mac.md). Notez l'URL du site Web, qui doit être similaire à **mywebsite.azurewebsites.net**.

2. Une fois le site Web créé, accédez au site via le portail Azure et sélectionnez l’onglet **Configurer**. Activez les **ports Web**, puis cliquez sur **Enregistrer** en bas de la page.

2. Ouvrez **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts\\DashboardBolt.java** et modifiez la ligne suivante pour pointer vers l'URL du tableau de bord publié :

		socket = IO.socket("http://mywebsite.azurewebsites.net");

3. Enregistrez le fichier **DashboardBolt.java**.

### Mise en package et déploiement de la topologie

1. Utilisez la commande suivante pour créer un package JAR depuis votre projet :

		mvn package

	Ceci va créer un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** dans le répertoire **target** de votre projet.

2. Suivez les étapes de la section [Déploiement et gestion des topologies Storm](hdinsight-storm-deploy-monitor-topology.md) pour télécharger et démarrer la topologie sur votre Storm sur le cluster HDInsight à l'aide du **tableau de bord Storm**.

3. Une fois la topologie lancée, ouvrez un navigateur vers le site Web que vous avez publié sur Azure, puis utiliser la commande `node app.js` pour envoyer des données au concentrateur d'événements. Vous devez mettre à jour le tableau de bord Web pour afficher les informations.

	![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Facultatif : utilisation de HBase

Pour utiliser Storm et HBase conjointement, vous devez créer un réseau virtuel Azure, puis des clusters Storm et HBase sur le réseau.

### Création d'un réseau virtuel Azure (facultatif)

Si vous prévoyez d'utiliser HBase avec cet exemple, vous devez créer un réseau virtuel Azure qui contiendra un Storm sur cluster HDInsight et un HBase sur cluster HDInsight.

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com).

2. Dans le bas de la page, cliquez sur **+NOUVEAU** > **Services de réseau** > **Réseau virtuel** > **Création rapide**.

3. Tapez ou sélectionnez les valeurs suivantes :

	- **Nom** : nom de votre réseau virtuel.

	- **Espace d’adressage** : choisissez un espace d’adressage pour le réseau virtuel qui soit suffisamment grand pour fournir des adresses à tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échouera.

	- **Nombre maximal de machines virtuelles** : choisissez l’un des nombres maximaux de machines virtuelles.

	- **Emplacement** : l’emplacement doit être le même que celui du cluster HBase que vous allez créer.

	- **Serveur DNS** : étant donné que cet article utilise le serveur DNS interne fourni par Azure, vous pouvez choisir **Aucun**. Des configurations de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour obtenir des instructions détaillées, consultez la page [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

4. Cliquez sur **Créer un réseau virtuel**. Le nom du nouveau réseau virtuel apparaît dans la liste. Attendez que la valeur **Créé** apparaisse dans la colonne Statut.

5. Dans le volet principal, cliquez sur le réseau virtuel que vous venez de créer.

6. En haut de la page, cliquez sur **TABLEAU DE BORD**.

7. Sous **aperçu rapide**, prenez note de l'**ID RÉSEAU VIRTUEL**. Vous en aurez besoin lors de l'approvisionnement des clusters Storm et HBase.

8. En haut de la page, cliquez sur **CONFIGURER**.

9. En bas de la page, le nom du sous-réseau par défaut est **Sous-réseau-1**. Utilisez le bouton **Ajouter un sous-réseau** pour ajouter **Subnet-2**. Ces sous-réseaux vont héberger les clusters Storm et HBase.

	> [AZURE.NOTE]Dans cet article, nous allons utiliser des clusters avec un seul nœud. Si vous créez des clusters multi-nœuds, vous devez vérifier la valeur de **CIDR (NOMBRE D'ADRESSES)** du sous-réseau qui sera utilisé pour le cluster. Le nombre d’adresses doit être supérieur au nombre de nœuds de travail plus 7 (passerelle : 2, nœud principal : 2, Zookeeper : 3). Par exemple, si vous avez besoin d'un cluster HBase à 10 nœuds, le nombre d'adresses pour le sous-réseau doit être supérieur à 17 (10+7). Si ce n'est pas le cas, le déploiement échouera.
	>
	> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

11. Cliquez sur **Enregistrer** au bas de la page.

### Création de clusters Storm et HBase sur le réseau virtuel

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).

2. Cliquez sur **HDInsight** dans le volet de gauche, puis sur **+NOUVEAU** dans le coin inférieur gauche de la page.

3. Cliquez sur l'icône HDInsight dans la seconde colonne, puis sélectionnez **Personnaliser**.

4. Sur la page **Détails du cluster**, entrez le nom du nouveau cluster, puis sélectionnez **Storm** pour le **Type de cluster**. Cliquez sur la flèche pour continuer.

5. Entrez 1 pour la valeur de **Nœuds de données** à utiliser pour ce cluster. Pour **Région/Réseau virtuel**, sélectionnez le réseau virtuel Azure créé précédemment. Pour **Sous-réseaux du réseau virtuel**, sélectionnez **Subnet-1**.

	> [AZURE.NOTE]Pour réduire les coûts du cluster utilisé pour cet article, réduisez la **Taille du cluster** sur 1, puis supprimez le cluster après avoir fini de l'utiliser.

6. Entrez le **Nom d'utilisateur** d'administrateur et un **Mot de passe**, puis cliquez sur la flèche pour continuer.

4. Pour **Compte de stockage**, sélectionnez **Créer un nouveau stockage** ou sélectionnez un compte de stockage existant. Sélectionnez ou entrez le **Nom de compte** et le **Conteneur par défaut** à utiliser. Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster Storm.

5. Répétez ces étapes pour créer un nouveau cluster **HBase**. Voici les principales différences :

	* **Type de cluster**: sélectionnez **HBase**

	* **Sous-réseaux du réseau virtuel** : sélectionnez **Subnet-2**.

	* **Compte de stockage**: nous vous conseillons d’utiliser un autre conteneur que celui utilisé pour le cluster Storm.

### Découverte du suffixe DNS de HBase

Pour écrire sur HBase à partir du cluster Storm, vous devez utiliser le nom de domaine qualifié complet (FQDN) du cluster HBase. Utilisez la commande suivante pour découvrir ces informations :

	curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

Dans les données JSON renvoyées, recherchez l’entrée **« host\_name »**. Elle contient le nom de domaine qualifié complet des nœuds du cluster, par exemple :

	...
	"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
	...

La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS, par exemple **mycluster.b1.cloudapp.net**.

### Activation du bolt HBase

1. Ouvrez **hdinsight-eventhub-example\\TemperatureMonitor\\conf\\hbase-site.xml** et remplacez les entrées `suffix` dans la ligne suivante par le suffixe DNS obtenu précédemment pour le cluster HBase. Enregistrez le fichier après avoir apporté ces modifications.

		<value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

	Cela sera utilisé par le bolt HBase pour communiquer avec le cluster HBase.

1. Ouvrez **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts** dans un éditeur de texte et annulez les marques de commentaires dans les lignes suivantes en supprimant le `//` du début. Enregistrez le fichier après avoir apporté ces modifications.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Ceci active le bolt HBase.

	> [AZURE.NOTE]Vous devez uniquement activer le bolt HBase lorsque vous déployez sur le cluster storm, pas lorsque vous testez localement.

### Données HBase et Storm

Avant d'exécuter la topologie, vous devez préparer HBase pour accepter les données.

1. Connectez-vous au cluster HBase en utilisant le Bureau à distance.

2. À partir du bureau, démarrez la ligne de commande HDInsight, puis entrez les commandes suivantes :

    cd %HBASE\_HOME% bin\\hbase shell

3. À partir de l'environnement de ligne de commande HBase, entrez la commande suivante pour créer une table dans laquelle seront stockées les données du capteur :

    create 'SensorData', 'cf'

4. Vérifiez que la table ne contient aucune donnée en entrant la commande suivante.

    scan 'SensorData'

Lorsque vous avez démarré la topologie sur le cluster storm et traité les données, vous pouvez à nouveau utiliser la commande `scan 'SensorData'` pour vérifier que les données ont été insérées dans HBase.


## Étapes suivantes

Vous avez à présent appris à utiliser Storm pour lire des données à partir du concentrateur d'événements et afficher des informations à partir de Storm sur un tableau de bord externe à l’aide de SignalR et D3.js. Si vous avez suivi les étapes facultatives, vous avez également appris comment configurer HDInsight dans un réseau virtuel et communiquer entre une topologie Storm et HBase à l'aide du bolt HBase.

* Pour plus d'exemples de topologies Storm avec HDinsight, consultez :

    * [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

* Pour plus d'informations sur Apache Storm, consultez le [Apache Storm](https://storm.incubator.apache.org/) site.

* Pour plus d'informations sur HBase avec HDInsight, veuillez consulter la [Présentation de HBase avec HDInsight](hdinsight-hbase-overview.md).

* Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/).

* Pour plus d'informations sur D3.js, consultez la page [D3.js : documents pilotés par les données](http://d3js.org/).

* Pour plus d'informations sur la création de topologies en Java, consultez [Développement de topologies Java pour Apache Storm sur HDInsight](hdinsight-storm-develop-java-topology.md).

* Pour plus d'informations sur la création de topologies en .NET, consultez [Développement de topologies C# pour Apache Storm sur HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO1-->