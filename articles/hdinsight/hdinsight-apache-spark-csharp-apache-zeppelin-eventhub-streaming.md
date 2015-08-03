<properties 
	pageTitle="Utilisez les concentrateurs d'événements Azure avec Apache Spark dans HDInsight pour traiter les données de diffusion en continu | Azure" 
	description="Des instructions étape par étape sur l'envoi d’un flux de données vers un concentrateur d'événements Azure et la réception de ces événements dans Spark à l’aide d’un bloc-notes Zeppelin" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# Diffusion en continu de Spark : traiter les événements de concentrateurs d'événements Azure avec Apache Spark sur HDInsight

La diffusion en continu de Spark développe l'API Spark de base pour générer des applications de traitement de flux de données évolutives, à haut débit et à tolérance de pannes. Les données peuvent provenir de nombreuses sources. Dans cet article, nous utilisons des concentrateurs d'événements pour recevoir des données. Les concentrateurs d'événements sont un système de réception hautement évolutive pouvant recevoir des millions d'événements par seconde.

Dans ce didacticiel, vous allez apprendre à créer un concentrateur d'événements Azure, à recevoir des messages dans un concentrateur d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide d'un bloc-notes Zeppelin configuré pour Apache Spark dans HDInsight.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Pour obtenir des instructions, consultez[Approvisionnement de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Un [concentrateur d’événements Azure](service-bus-event-hubs-csharp-ephcs-getstarted.md)
- Une station de travail avec Microsoft Visual Studio 2013. Pour les instructions, consultez [Installation de Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Créer le concentrateur d'événements Azure

1. À partir du [portail Azure](https://manage.windowsazure.com), sélectionnez **NOUVEAU** > **Service Bus** > **concentrateur d’événements** > **Création personnalisée**.

2. Sur l’écran **Ajouter un nouveau concentrateur d’événements**, entrez un **nom de concentrateur d’événements**, sélectionnez la **région** dans laquelle créer le concentrateur, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la **flèche** pour continuer.

	![page 1 de l’assistant](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Création d'un concentrateur d'événements Azure")

	> [AZURE.NOTE]Vous devez sélectionner le même**emplacement** que celui de votre cluster Apache Spark dans HDInsight pour réduire la latence et les coûts.

3. Sur l’écran **Configuration du concentrateur d'événements**, entrez le**nombre de partitions** et les valeurs de **rétention des messages**, puis cliquez sur la coche. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous en aurez besoin ultérieurement.

	![page 2 de l’assistant](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Spécifiez la taille des partitions et les jours de rétention du concentrateur d'événements")

4. Cliquez sur le concentrateur d'événements que vous avez créé, cliquez sur**Configurer**, puis créez deux stratégies d'accès pour le concentrateur d'événements.

	<table>
<tr><th>Nom</th><th>Autorisations</th></tr>
<tr><td>mysendpolicy</td><td>Envoyer</td></tr>
<tr><td>myreceivepolicy</td><td>Écouter</td></tr>
</table>Après avoir créé les autorisations, sélectionnez l’icône **Enregistrer** située en bas de page. Cela crée des stratégies d'accès partagé qui seront utilisées pour envoyer vers (**mysendpolicy**) et écouter (**myreceivepolicy**) ce concentrateur d'événements.

	![stratégies](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Créer des stratégies de concentrateur d'événements")

	
5. Sur la même page, notez les clés de stratégie générées pour les deux stratégies. Enregistrez ces clés, car elles seront utilisées plus tard.

	![clés de stratégies](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "Enregistrer les clés de stratégie")

6. Sur la page **Tableau de bord**, cliquez sur **Informations de connexion** en bas pour récupérer et enregistrer les chaînes de connexion pour le concentrateur d'événements à l’aide des deux stratégies.

	![clés de stratégies](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "Enregistrer les chaînes de connexion de stratégie")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Recevoir des messages dans Spark sur HDInsight à l'aide de Zeppelin

Dans cette section, vous allez créer un bloc-notes [Zeppelin](https://zeppelin.incubator.apache.org) pour recevoir des messages à partir du concentrateur d'événements dans le cluster Spark dans HDInsight.

1. Lancez le bloc-notes Zeppelin. Sélectionnez votre cluster Spark sur le portail Azure et, à partir de la barre des tâches du portail en bas, cliquez sur **bloc-notes Zeppelin**. Lorsque vous y êtes invité, entrez les informations d'identification d'administrateur pour le cluster Spark. Suivez les instructions sur la page qui s'ouvre pour lancer le bloc-notes.

2. Créer un nouveau bloc-notes. Dans le volet d'en-tête, cliquez sur **Bloc-notes** et, à partir de la liste déroulante, cliquez sur **Créer un nouveau bloc-notes**.

	![Créer un nouveau bloc-notes Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "Créer un nouveau bloc-notes Zeppelin")

	Sur la même page, sous l’en-tête du **Bloc-notes**, vous devez voir un nouveau bloc-notes dont le nom commence par **Note XXXXXXXXX**. Cliquez sur le nouveau bloc-notes.

3. Sur la page web du nouveau bloc-notes, cliquez sur l'en-tête et modifiez le nom du bloc-notes si vous le souhaitez. Appuyez sur ENTRÉE pour enregistrer la modification de nom. Vérifiez également que l'en-tête du bloc-notes indique un statut **Connecté** en haut à droite.

	![État du bloc-notes Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "État du bloc-notes Zeppelin")

4. Dans le paragraphe vide qui est créé par défaut dans le nouveau bloc-notes, collez l’extrait de code suivant et remplacez les espaces réservés pour utiliser la configuration de votre concentrateur d’événements. Dans cet extrait de code, vous recevez le flux du concentrateur d'événements et enregistrez ce flux dans une table temporaire nommée **mytemptable**. Dans la section suivante, nous allons commencer l'application expéditrice. Vous pouvez ensuite lire les données directement à partir de la table.

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

1. À partir du bloc-notes Zeppelin, exécutez le paragraphe avec l'extrait de code. Appuyez sur **MAJ + ENTRÉE** ou sur le bouton **Lire** en haut à droite.

	L'état en haut à droite du paragraphe doit progresser de PRÊT, EN ATTENTE, EN COURS D'EXÉCUTION jusqu’à TERMINÉ. La sortie s'affichera en bas du même paragraphe. La capture d'écran ressemble à ceci :

	![Sortie de l'extrait de code](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "Sortie de l’extrait de code")

2. Exécutez le projet **Sender** et appuyez sur **Entrée** dans la fenêtre de console pour commencer à envoyer des messages au concentrateur d'événements.

3. À partir du bloc-notes Zeppelin, dans un nouveau paragraphe, entrez l'extrait de code suivant pour lire les messages reçus dans Spark.

		%sql select * from mytemptable limit 10

	La capture d'écran suivante montre les messages reçus dans **mytemptable**.

	![Recevoir les messages dans Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Recevoir des messages dans le bloc-notes Zeppelin")

4. Redémarrez l'interpréteur SQL de Spark pour quitter l'application. Cliquez sur l’onglet **Interpréteur** en haut et, pour l'interpréteur Spark, cliquez sur **Redémarrer**.

	![Redémarrez l'interpréteur Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Redémarrez l'interpréteur Zeppelin")

##<a name="sparkstreamingha"></a>Exécution de l'application de diffusion en continu avec une haute disponibilité

L’utilisation de Zeppelin pour recevoir des données en continu dans le cluster Spark sur HDInsight est une bonne approche pour réaliser un prototype de votre application. Toutefois, pour exécuter votre application de diffusion en continu dans une configuration de production avec une haute disponibilité et la résilience, vous devez effectuer les opérations suivantes :

1. Copier le fichier jar de dépendance sur le compte de stockage associé au cluster.
2. Créer une application de diffusion en continu.
3. Ouvrez une session RDP sur le cluster et copiez le fichier jar d'application sur le nœud principal du cluster.
3. Ouvrez une session RDP sur le cluster et exécutez l'application sur le nœud de cluster.

Les instructions sur la procédure de réalisation de ces étapes et un exemple d'application de diffusion en continu peuvent être téléchargés à partir de GitHub à [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Voir aussi


* [Vue d'ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Démarrage rapide : approvisionnement Apache Spark sur HDInsight et exécution des requêtes interactives à l'aide de Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Utilisez Spark dans HDInsight pour la création d'applications pour l’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Effectuez une analyse interactive des données à l'aide de Spark dans HDInsight avec les outils décisionnels](hdinsight-apache-spark-use-bi-tools.md)
* [Gérez les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO4-->