<properties 
	pageTitle="Utilisation des hubs d'événements Azure avec Apache Spark dans HDInsight pour traiter les données de diffusion en continu | Microsoft Azure" 
	description="Des instructions étape par étape sur l'envoi d’un flux de données vers un hub d'événements Azure et la réception de ces événements dans Spark à l’aide d’un bloc-notes Zeppelin" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Diffusion en continu de Spark : traiter les événements d’Azure Event Hubs avec Apache Spark sur HDInsight (Windows)

> [AZURE.NOTE] HDInsight fournit maintenant des clusters Spark sur Linux. Pour plus d’informations sur l’exécution d’une application de diffusion en continu sur des clusters HDInsight Spark Linux, consultez [Diffusion en continu de Spark : traiter les événements d’Azure Event Hubs avec Apache Spark sur HDInsight (Linux)](hdinsight-apache-spark-eventhub-streaming.md).

La diffusion en continu de Spark développe l'API Spark de base pour générer des applications de traitement de flux de données évolutives, à haut débit et à tolérance de pannes. Les données peuvent provenir de nombreuses sources. Dans cet article, nous utilisons Event Hubs pour recevoir les données. Event Hubs est un système de réception hautement évolutif pouvant recevoir des millions d'événements par seconde.

Dans ce didacticiel, vous allez apprendre à créer un hub d'événements Azure, à recevoir des messages dans un hub d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide d'un bloc-notes Zeppelin configuré pour Apache Spark dans HDInsight.

> [AZURE.NOTE] Pour suivre les instructions de cet article, vous devez utiliser les deux versions du portail Azure. Pour créer un hub d’événements, vous utilisez le [portail Azure](https://manage.windowsazure.com). Pour travailler avec le cluster HDInsight Spark, vous allez utiliser le [portail Azure en version préliminaire](https://ms.portal.azure.com/).

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Un [hub d’événements Azure](service-bus-event-hubs-csharp-ephcs-getstarted.md).
- Une station de travail avec Microsoft Visual Studio 2013. Pour obtenir des instructions, consultez [Installation de Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Créer un hub d’événements Azure

1. À partir du [portail Azure](https://manage.windowsazure.com), sélectionnez **NOUVEAU** > **Service Bus** > **concentrateur d’événements** > **Création personnalisée**.

2. Sur l’écran **Ajouter un nouvel hub d’événements**, entrez un **nom de hub d’événements**, sélectionnez la **région** dans laquelle créer le hub, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la **flèche** pour continuer.

	![page 1 de l’assistant](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub.png "Création d'un hub d'événements Azure")

	> [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que celui de votre cluster Apache Spark dans HDInsight pour réduire la latence et les coûts.

3. Sur l'écran **Configurer un hub d'événements**, entrez le **nombre de partitions** et les valeurs de **rétention des messages**, puis cliquez sur la coche. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous en aurez besoin ultérieurement.

	![page 2 de l’assistant](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Spécifiez la taille des partitions et les jours de rétention du hub d'événements")

4. Cliquez sur le hub d’événements que vous avez créé, cliquez sur **Configurer**, puis créez deux stratégies d’accès pour le hub d’événements.

	<table>
<tr><th>Nom</th><th>Autorisations</th></tr>
<tr><td>mysendpolicy</td><td>Envoyer</td></tr>
<tr><td>myreceivepolicy</td><td>Écouter</td></tr>
</table>Après avoir créé les autorisations, sélectionnez l’icône **Enregistrer** située en bas de page. Ceci crée des stratégies d’accès partagé qui seront utilisées pour envoyer vers (**mysendpolicy**) et écouter (**myreceivepolicy**) ce hub d’événements.

	![stratégies](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Créer des stratégies de hub d'événements")

	
5. Sur la même page, notez les clés de stratégie générées pour les deux stratégies. Enregistrez ces clés, car elles seront utilisées plus tard.

	![clés de stratégies](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Enregistrer les clés de stratégie")

6. Sur la page **Tableau de bord**, cliquez sur **Informations de connexion** en bas pour récupérer et enregistrer les chaînes de connexion pour le hub d’événements utilisant les deux stratégies.

	![clés de stratégies](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Enregistrer les chaînes de connexion de stratégie")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Recevoir des messages dans Spark sur HDInsight en utilisant Zeppelin

Dans cette section, vous allez créer un bloc-notes [Zeppelin](https://zeppelin.incubator.apache.org) pour recevoir des messages du hub d’événements dans le cluster Spark dans HDInsight.

### Allocation de ressources à Zeppelin pour une application de diffusion en continu

Vous devez prendre en considération les points suivants quand vous créez une application de diffusion en continu à l’aide de Zeppelin :

* **Partitions et cœurs de hub d’événements alloués à Zeppelin**. Dans les étapes précédentes, vous avez créé un hub d’événements avec des partitions. Dans l’application de diffusion en continu Zeppelin que vous créez ci-après, vous spécifierez à nouveau le même nombre de partitions. Pour diffuser en continu les données à partir du hub d’événements à l’aide de Zeppelin, le nombre de cœurs que vous allouez à Zeppelin doit être le double du nombre de partitions dans le hub d’événements.
* **Nombre minimal de cœurs à allouer à Zeppelin**. Dans votre application de diffusion en continu que vous créez ci-après, vous créez une table temporaire dans laquelle vous stockez les messages diffusés par votre application. Vous utilisez ensuite une instruction SQL Spark pour lire les messages à partir de cette table temporaire. Pour exécuter l’instruction SQL Spark, vous devez vous assurer qu’au moins deux cœurs sont alloués à Zeppelin.

Combinées, les deux conditions ci-dessus aboutissent à la configuration suivante :

* Le nombre minimal de cœurs que vous devez allouer à Zeppelin est 2.
* Le nombre de cœurs alloués doit toujours être le double du nombre de partitions dans le hub d’événements. 

Pour obtenir des instructions sur la façon d’allouer des ressources dans un cluster Spark, consultez [Gérer des ressources pour le cluster Apache Spark dans HDInsight](hdinsight-apache-spark-resource-manager-v1.md).

### Créer une application de diffusion en continu à l’aide de Zeppelin

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Zeppelin Notebook**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Zeppelin pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Créer un nouveau bloc-notes. Dans le volet d’en-tête, cliquez sur **Bloc-notes** et, à partir de la liste déroulante, cliquez sur **Créer un nouveau bloc-notes**.

	![Créer un nouveau bloc-notes Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.createnewnote.png "Créer un nouveau bloc-notes Zeppelin")

	Dans la même page, sous le titre **Bloc-notes**, un nouveau bloc-notes dont le nom commence par **Note XXXXXXXXX** doit s’afficher. Cliquez sur le nouveau bloc-notes.

3. Sur la page web du nouveau bloc-notes, cliquez sur l'en-tête et modifiez le nom du bloc-notes si vous le souhaitez. Appuyez sur ENTRÉE pour enregistrer la modification de nom. Vérifiez également que l’en-tête du bloc-notes indique l’état **Connected** dans le coin supérieur droit.

	![État du bloc-notes Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.newnote.connected.png "État du bloc-notes Zeppelin")

4. Dans le paragraphe vide qui est créé par défaut dans le nouveau bloc-notes, collez l’extrait de code suivant et remplacez les espaces réservés pour utiliser la configuration de votre hub d’événements. Dans cet extrait de code, vous recevez le flux du hub d’événements et enregistrez ce flux dans une table temporaire nommée **mytemptable**. Dans la section suivante, nous allons commencer l'application expéditrice. Vous pouvez ensuite lire les données directement à partir de la table.

	> [AZURE.NOTE] Dans l’extrait de code ci-dessous, **eventhubs.checkpoint.dir** doit être défini sur un répertoire de votre conteneur de stockage par défaut. Si le répertoire n’existe pas, l’application de diffusion en continu le crée. Vous pouvez spécifier le chemin complet du répertoire, par exemple « **wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/** » ou simplement le chemin relatif du répertoire, par exemple « **/mycheckpointdir** ».

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>Exécution des applications

1. À partir du bloc-notes Zeppelin, exécutez le paragraphe avec l'extrait de code. Appuyez sur **Maj+Entrée** ou sur le bouton **Lire** en haut à droite.

	L'état en haut à droite du paragraphe doit progresser de PRÊT, EN ATTENTE, EN COURS D'EXÉCUTION jusqu’à TERMINÉ. La sortie s'affiche en bas du même paragraphe. La capture d'écran ressemble à ceci :

	![Sortie de l'extrait de code](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.code.output.png "Sortie de l’extrait de code")

2. Exécutez le projet **Sender** et appuyez sur **Entrée** dans la fenêtre de console pour commencer à envoyer des messages au hub d’événements.

3. À partir du bloc-notes Zeppelin, dans un nouveau paragraphe, entrez l'extrait de code suivant pour lire les messages reçus dans Spark.

		%sql 
		select * from mytemptable limit 10

	La capture d’écran suivante montre les messages reçus dans **mytemptable**.

	![Recevoir les messages dans Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.output.png "Recevoir des messages dans le bloc-notes Zeppelin")

4. Redémarrez l'interpréteur SQL de Spark pour quitter l'application. Cliquez sur l’onglet **Interpreter** en haut de l’écran et, pour l’interpréteur Spark, cliquez sur **Restart**.

	![Redémarrez l'interpréteur Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.zeppelin.restart.interpreter.png "Redémarrez l'interpréteur Zeppelin")

##<a name="sparkstreamingha"></a>Exécuter l’application de diffusion en continu avec une haute disponibilité

L’utilisation de Zeppelin pour recevoir des données en continu dans le cluster Spark sur HDInsight est une bonne approche pour réaliser un prototype de votre application. Toutefois, pour exécuter votre application de diffusion en continu dans une configuration de production avec une haute disponibilité et la résilience, vous devez effectuer les opérations suivantes :

1. Copier le fichier jar de dépendance sur le compte de stockage associé au cluster.
2. Créer une application de diffusion en continu.
3. Ouvrez une session RDP sur le cluster et copiez le fichier jar d'application sur le nœud principal du cluster.
3. Ouvrez une session RDP sur le cluster et exécutez l'application sur le nœud de cluster.

Les instructions sur la procédure de réalisation de ces étapes et un exemple d'application de diffusion en continu peuvent être téléchargés à partir de GitHub à [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Démarrage rapide : Créer Apache Spark sur HDInsight et exécuter des requêtes interactives en utilisant Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Utiliser Spark sur HDInsight pour créer des applications d’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Effectuez une analyse interactive des données à l'aide de Spark dans HDInsight avec les outils décisionnels](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0128_2016-->