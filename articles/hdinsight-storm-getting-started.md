<properties 
	pageTitle="Prise en main d'Apache Storm avec Microsoft Azure HDInsight (Hadoop)" 
	description="Découvrez comment utiliser  Apache Storm pour traiter des données en temps réel avec HDInsight (Hadoop)." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# Prise en main de Storm avec HDInsight (Hadoop)

Apache Storm est un système de calcul en temps réel, évolutif, distribué, à tolérance de panne, qui permet de traiter des flux de données. Avec Azure HDInsight, vous pouvez créer un cluster Hadoop basé sur le cloud qui effectue des analyses de données en temps réel avec Storm.

## Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* [Approvisionnement d'un cluster Storm sur HDInsight](#provision)

* [Connexion au cluster](#connect)

* [Exécution d'une topologie Storm](#run)

* [Surveillance d'une topologie Storm](#monitor)

* [Arrêt d'une topologie Storm](#stop)

* [Étapes suivantes](#next)

## Avant de commencer

Ce didacticiel requiert les éléments suivant :

* Un abonnement Azure

* Windows Azure PowerShell

* Si vous ne connaissez pas Apache Storm, nous vous conseillons de commencer par lire l'article [Présentation de Storm HDInsight](hdinsight-storm-overview.md).

## <a id="provision"></a>Approvisionnement d'un cluster Storm dans le portail Azure

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Connectez-vous au [portail de gestion Azure][azureportal]

2. Cliquez sur **HDInsight** sur la gauche, puis sur **+Nouveau** dans le coin inférieur gauche de la page.

3. Cliquez sur l'icône HDInsight dans la deuxième colonne, puis sélectionnez **Personnalisé**.

4. Sur la page **Détails du cluster**, entrez le nom du nouveau cluster, puis sélectionnez **Storm** comme **Type de cluster**. Cliquez sur la flèche pour continuer.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. Entrez le nombre de **Nœuds de données** à utiliser pour ce cluster, ainsi que la **Région et/ou le réseau virtuel** où le cluster sera créé. Cliquez sur la flèche pour continuer.

	> [AZURE.NOTE] Pour réduire les coûts du cluster utilisé pour cet article, réduisez la **Taille du cluster** sur 1, puis supprimez le cluster après avoir fini de l'utiliser.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. Entrez le **Nom d'utilisateur** d'administrateur et un **Mot de passe**, puis cliquez sur la flèche pour continuer.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. Pour **Compte de stockage**, sélectionnez **Créer un nouveau stockage** ou sélectionnez un compte de stockage existant. Sélectionnez ou entrez le **Nom de compte** et le **Conteneur par défaut** à utiliser.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. Dans la page **Actions de Script**, cliquez sur **Ajouter une action de script** pour fournir des détails sur le script personnalisé que vous souhaitez exécuter pour personnaliser un cluster, pendant la création du cluster. Par exemple, vous pouvez utiliser une action de Script pour personnaliser un cluster et installer <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Pour plus d'informations, consultez [Personnalisation de clusters HDInsight à l'aide d'une action de script](hdinsight-hadoop-customize-cluster.md). 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom</td>
			<td>Entrez un nom pour l'action du script.</td></tr>
		<tr><td>URI du script</td>
			<td>Spécifiez l'URI du script appelé pour personnaliser le cluster.</td></tr>
		<tr><td>Type de nœud</td>
			<td>Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Vous pouvez choisir <b>Tous les nœuds</b>, <b>Uniquement les nœuds principaux</b> ou <b>Nœuds de travail</b> uniquement.
		<tr><td>Paramètres</td>
			<td>Spécifiez les paramètres, si le script le demande.</td></tr>
	</table>

	Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.

## Utilisation de Storm sur HDInsight

Pour obtenir la version préliminaire de Storm sur HDInsight, vous devez vous connecter au cluster en utilisant le Bureau à distance pour utiliser Storm. Pour vous connecter au cluster HDInsight et utiliser la commande Storm, procédez comme suit :

###<a id="connect"></a>Connexion au cluster

1. Activez la connectivité Bureau à distance sur votre cluster HDInsight en utilisant le [portail de gestion Azure][management]. Dans le portail, sélectionnez votre cluster HDInsight, puis choisissez __Activer Distant__ en bas de la page __Configuration__.

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    Lorsque vous y êtes invité, saisissez un nom d'utilisateur et un mot de passe pour les sessions à distance. Vous devez également sélectionner une date d'expiration pour l'accès distant.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. Une fois l'accès à distance activé, sélectionnez __Connecter__ pour lancer la connexion. Un fichier __.rdp__ sera alors téléchargé afin de démarrer une session Bureau à distance.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] Vous pouvez recevoir plusieurs invites de commandes pour approuver des certificats durant la connexion à l'ordinateur distant.

3. Une fois la connexion établie, utilisez l'icône de __ligne de commande Hadoop__ sur le Bureau pour ouvrir la ligne de commande Hadoop.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. Dans la ligne de commande Hadoop, utilisez la commande suivante pour remplacer les répertoires par le répertoire contenant la commande Storm.

	cd %storm_home%\bin

5. Pour afficher une liste des commandes disponibles, entrez `storm` sans paramètres.

Le cluster HDInsight s'affiche avec plusieurs exemples de topologies Storm. L'exemple **WordcountTopology** est utilisé dans les étapes suivantes. Pour plus d'informations sur les exemples fournis avec Storm sur HDInsight, consultez la section [Étapes suivantes)]#next).

### <a id="run"></a>Exécution d'une topologie Storm

Pour exécuter la topologie **WordCountTopology**, utilisez les commandes suivantes :

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Ceci indique à Storm que nous voulons exécuter la topologie **wordcount** à partir de la classe **storm.starter.WordCountTopology**, qui est contenue dans le fichier **storm-starter-&lt;version>-jar-with-dependencies.jar**. Ce fichier est situé dans le dossier contrib sous le répertoire %storm_home%, avec les autres exemples Storm.

> [AZURE.NOTE] La version du fichier JAR contenant les exemples peut changer régulièrement. Indiquez la version du fichier fournie avec votre cluster lors de l'exécution de cette commande.

Notez qu'aucun élément n'est renvoyé lorsque vous entrez la commande. **La topologie Storm, une fois démarrée, s'exécute jusqu'à ce que vous l'arrêtiez.** La topologie WordCountTopology va générer des phrases aléatoires tout en comptant le nombre de fois qu'elle rencontre chaque mot, jusqu'à ce que vous l'arrêtiez.

### <a id="monitor"></a>Surveillance de l'état d'une topologie Storm

L'exemple WordCountTopology n'écrit pas de résultat dans un répertoire, mais nous pouvons utiliser les pages web de l'interface utilisateur de Storm pour afficher l'état de la topologie, ainsi que le résultat.

1. Connectez-vous à votre cluster HDInsight en utilisant le Bureau à distance.

2. Cliquez sur le lien **Storm UI** situé sur le bureau. La page web de l'interface utilisateur de Storm s'affiche.  Sous **Topology summary**, sélectionnez l'entrée **wordcount**.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	Les statistiques de la topologie s'affichent. Elles incluent les composants constituant la topologie, les **spouts** et les **bolts**.

3. Sélectionnez le lien **spout** sur la page, puis, dans l'une des entrées dans la liste **Executors (All time)**, sélectionnez un nombre de **Port** supérieur à 0 dans les colonnes **Émis** et **Transférés**.

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] Le traitement de la topologie peut prendre quelques minutes avant de démarrer, en fonction du nombre de nœuds de votre cluster et de la topologie que vous exécutez. Actualisez régulièrement la page jusqu'à ce que les valeurs **Émis** et **Transférés** commencent à augmenter.

4. Une page contenant des informations semblables à celles ci-dessous doit s'afficher.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	À partir de cet extrait de code, vous pouvez voir le spout émis, 'snow white and the seven dwarfs', qui a été divisé en plusieurs mots. De même, un compte du nombre de traitements de chaque mot par la topologie depuis son démarrage est conservé. Par exemple, le mot  'dwarfs' a été émis 8 360 fois par le spout lorsque le résultat précédent a été consulté.

### <a id="stop"></a>Arrêt d'une topologie Storm

La topologie **WordCountTopology** continuera son exécution jusqu'à ce que vous l'arrêtiez. Pour ce faire, utilisez la commande suivante.

	storm kill wordcount

Si vous affichez la page web d'interface utilisateur Storm immédiatement après cette commande, vous remarquerez que l'état du **wordcount** du **Topology summary** est remplacé par la mention KILLED. Après quelques secondes, la topologie ne sera plus répertoriée dans la section **Topology summary**.

## <a id="next"></a>Étapes suivantes

* **Exemples de fichiers** : le cluster Storm contient plusieurs exemples dans le répertoire **%storm_home%\contrib**. Chaque exemple doit contenir les éléments suivants :

	* Le code source : par exemple, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Les documents Java : par exemple, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* L'exemple : par exemple, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Utilisez la commande 'jar' pour extraire le code source ou les documents Java. Par exemple, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE] Les documents Java se composent de pages web. Lorsque vous les avez extraits, utilisez un navigateur pour afficher le fichier **index.html**.

* [Analyse des données du capteur avec Storm et HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Développement d'applications de traitement des données de diffusion avec SCP.NET et C# sur Storm dans HDInsight](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
<!--HONumber=42-->
