<properties 
	pageTitle="Utilisation de blocs-notes Zeppelin avec un cluster Spark sur HDInsight Linux | Azure" 
	description="Instructions détaillées sur l'utilisation des blocs-notes Zeppelin avec les clusters Spark sur HDInsight Linux." 
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
	ms.date="07/25/2016" 
	ms.author="nitinme"/>


# Utilisation de blocs-notes Zeppelin avec un cluster Apache Spark sur HDInsight Linux

Découvrez comment installer des blocs-notes Zeppelin sur des clusters Apache Spark et comment utiliser les blocs-notes Zeppelin pour exécuter des tâches Spark.

> [AZURE.IMPORTANT] Le bloc-notes Zeppelin pour le cluster Spark sur HDInsight permet simplement d’illustrer l’utilisation de Zeppelin dans un environnement Azure HDInsight Spark. Si vous souhaitez utiliser des ordinateurs portables pour travailler avec HDInsight Spark, nous vous recommandons d’utiliser plutôt des blocs-notes Jupyter. Les blocs-notes Jupyter fournissent également des options de noyau différentes, telles que Scala, et continuent à bénéficier d’améliorations de fonctionnalités. Pour obtenir des instructions sur l’utilisation des blocs-notes Jupyter avec HDInsight Spark, consultez la rubrique [Exécuter des requêtes Spark SQL à l’aide d’un bloc-notes Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter).

**Configuration requise :**

* Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un client SSH. Pour les distributions Linux et Unix ou pour Macintosh OS X, la commande `ssh` est fournie avec le système d'exploitation. Pour Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Si vous souhaitez utiliser un client SSH autre que `ssh` ou PuTTY, veuillez consulter la documentation de votre client qui explique comment établir un tunnel SSH.

* Un navigateur Web qui peut être configuré pour utiliser un proxy SOCKS

* __(facultatif)__ : un plug-in comme [FoxyProxy](http://getfoxyproxy.org/,) pouvant appliquer des règles qui acheminent uniquement les demandes spécifiques via le tunnel.

	> [AZURE.WARNING] Sans un plug-in comme FoxyProxy, toutes les demandes effectuées via le navigateur peuvent être routées via le tunnel. Cela peut entraîner un chargement plus lent des pages Web dans votre navigateur.

## Installation de Zeppelin sur un cluster Spark

Vous pouvez installer Zeppelin sur un cluster Spark à l’aide d’une action de script. L’action de script utilise des scripts personnalisés pour installer sur le cluster des composants qui ne sont pas disponibles par défaut. Vous pouvez utiliser le script personnalisé pour installer Zeppelin à partir du portail Azure, à l’aide du Kit de développement logiciel (SDK) .NET HDInsight ou à l’aide d’Azure PowerShell. Vous pouvez utiliser le script pour installer Zeppelin lors de la création du cluster ou lorsque le cluster est prêt à fonctionner. Les liens dans les sections ci-dessous fournissent les instructions sur la marche à suivre.

### Utilisation du portail Azure

Pour connaître les instructions liées à l’utilisation du portail Azure pour exécuter une action de script afin d’installer Zeppelin, consultez [Personnalisation des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). Vous devez apporter quelques modifications aux instructions de cet article.

* Vous devez utiliser le script pour installer Zeppelin. Le script personnalisé permettant d’installer Zeppelin sur un cluster Spark sur HDInsight est disponible aux liens suivants :
	* Pour les clusters Spark 1.6.0 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* Pour les clusters Spark 1.5.2 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* Vous devez exécuter l'action de script uniquement sur le nœud principal.

* Le script n'a pas besoin de paramètres.

### Utilisation du Kit de développement logiciel (SDK) HDInsight .NET

Pour connaître les instructions liées à l’utilisation du Kit de développement logiciel (SDK) HDInsight .NET pour exécuter une action de script afin d’installer Zeppelin, consultez [Personnalisation des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). Vous devez apporter quelques modifications aux instructions de cet article.

* Vous devez utiliser le script pour installer Zeppelin. Le script personnalisé permettant d’installer Zeppelin sur un cluster Spark sur HDInsight est disponible aux liens suivants :
	* Pour les clusters Spark 1.6.0 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* Pour les clusters Spark 1.5.2 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* Le script n'a pas besoin de paramètres.

* Définissez le type de cluster que vous créez sur Spark.

### Utilisation de Microsoft Azure PowerShell

Utilisez l'extrait de code PowerShell suivant pour créer un cluster Spark sur HDInsight Linux sur lequel Zeppelin est installé. Selon la version de cluster Spark dont vous disposez, vous devez mettre à jour l’extrait de code PowerShell ci-dessous pour inclure le lien vers le script personnalisé correspondant.

* Pour les clusters Spark 1.6.0 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Pour les clusters Spark 1.5.2 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Configurez le tunnel SSL pour accéder à un bloc-notes Zeppelin

Vous utiliserez des tunnels SSH pour accéder aux blocs-notes Zeppelin s'exécutant sur le cluster Spark sur HDInsight Linux. Les étapes ci-dessous montrent comment créer un tunnel SSH à l'aide de la ligne de commande SSH (Linux) et PuTTY (Windows).

### Création d’un tunnel à l'aide de la commande SSH (Linux)

Utilisez la commande suivante pour créer un tunnel SSH à l'aide de la commande `ssh`. Remplacez __USERNAME__ par un utilisateur SSH de votre cluster HDInsight et __CLUSTERNAME__ par le nom de votre cluster HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Cette commande va permettre de créer une connexion qui achemine le trafic vers le port local 9876 du cluster via SSH. Les options sont :

* **D 9876** : port local qui acheminera le trafic via le tunnel.

* **C** : compresse toutes les données car le trafic web est principalement du texte

* **2** : force le SSH à essayer le protocole version 2 uniquement

* **q** : mode silencieux

* **T** : désactive l’allocation pseudo-tty puisque nous transférons simplement un port

* **n** : empêche la lecture STDIN puisque nous transférons simplement un port

* **N** : n’exécute pas une commande à distance puisque nous transférons simplement un port

* **f** : s’exécute à l’arrière-plan

Si vous avez configuré le cluster avec une clé SSH, il vous faudra peut-être utiliser le paramètre `-i` et indiquer le chemin d’accès vers la clé SSH privée.

Une fois la commande terminée, le trafic envoyé au port 9876 sur l’ordinateur local sera acheminé via SSL au nœud principal du cluster et semblera provenir de celui-ci.

### Création d’un tunnel à l'aide de PuTTY (Windows)

Pour créer un tunnel SSH à l’aide de PuTTY, procédez comme suit.

1. Ouvrez PuTTY et saisissez vos informations de connexion. Si vous ne connaissez pas PuTTY, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d'informations sur son utilisation avec HDInsight.

2. Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.

3. Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH**.

	* **Port source** : le port sur le client que vous souhaitez transférer. Par exemple : **9876**.

	* **Destination** : l’adresse SSH pour le cluster HDInsight sous Linux. Par exemple : **moncluster-ssh.azurehdinsight.net**.

	* **Dynamique** : active le routage dynamique du proxy SOCKS.

	![image des options de tunneling](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.

5. Lorsque vous y êtes invité, connectez-vous au serveur. Cela permettra d'établir une session SSH et d'activer le tunnel.

### Utilisation du tunnel à partir de votre navigateur

> [AZURE.NOTE] Dans les étapes de cette section, nous utilisons le navigateur FireFox, car il est disponible gratuitement pour les systèmes d’exploitation Linux, Unix, Macintosh OS X et Windows. Les autres navigateurs modernes comme Google Chrome, Microsoft Edge ou Apple Safari devraient fonctionner aussi bien ; cependant, le plug-in FoxyProxy utilisé dans certaines étapes peut ne pas être disponible pour tous les navigateurs.

1. Configurez le navigateur pour utiliser **localhost:9876** comme un proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox : si vous avez utilisé un port autre que 9876, modifiez le port par celui que vous avez utilisé :

	![image des paramètres de Firefox](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] La sélection de **DNS Distant** résout les requêtes DNS à l’aide du cluster HDInsight. Si cette option n’est pas sélectionnée, le DNS est résolu localement.

2. Vérifiez que le trafic est routé via le tunnel en accédant à un site, comme [http://www.whatismyip.com/](http://www.whatismyip.com/), avec les paramètres de proxy activés et désactivés dans Firefox. Quand ils sont activés, l’adresse IP est celle d’une machine du centre de données Microsoft Azure.

### Extensions du navigateur

Si la configuration du navigateur pour utiliser le tunnel fonctionne, en principe vous n'avez pas besoin d'acheminer tout le trafic via le tunnel. Les extensions de navigateur comme [FoxyProxy](http://getfoxyproxy.org/) prennent en charge la correspondance des requêtes URL (FoxyProxy Standard ou Plus uniquement) pour que seules les requêtes d'URL spécifiques soient envoyées via le tunnel.

Si vous avez installé FoxyProxy Standard, suivez les étapes suivantes pour configurer celui-ci uniquement pour le trafic de transfert de HDInsight via le tunnel.

1. Ouvrez l'extension FoxyProxy dans votre navigateur. Par exemple, dans Firefox, sélectionnez l'icône FireProxy à côté du champ d'adresse.

	![icône foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Sélectionnez **Ajouter un nouveau proxy**, puis l’onglet **Général** et entrez un nom de proxy pour **HDInsightProxy**.

	![onglet général foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Sélectionnez l’onglet **Informations du proxy** et remplissez les champs suivants :

	* **Hôte ou adresse IP** : il s’agit de localhost, puisque nous utilisons un tunnel SSH sur la machine locale.

	* **Port** : port que vous avez utilisé pour le tunnel SSH

	* **Proxy SOCKS** : sélectionnez celui-ci pour autoriser le navigateur à utiliser le tunnel comme proxy.

	* **SOCKS v5** : sélectionnez celui-ci pour définir la version requise pour le proxy.

	![proxy foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Sélectionnez l’onglet **Type d’URL**, puis **Ajouter un nouveau type**. Utilisez ce qui suit pour définir le type, puis cliquez sur **OK** :

	* **Nom de type** – **zeppelinnotebook** : il s’agit simplement d’un nom de type convivial.

	* **Modèle d’URL** – **\*hn0\*** : définit un modèle qui correspond au nom de domaine complet interne du système d’extrémité où sont hébergés les blocs-notes Zeppelin. Étant donné que les blocs-notes Zeppelin sont uniquement disponibles sur headnode0 du cluster et que le système d’extrémité est généralement `http://hn0-<string>.internal.cloudapp.net`, l'utilisation du modèle **hn0** garantit que la requête est redirigée vers le point de terminaison Zeppelin.

		![type foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Cliquez sur **OK** pour ajouter le proxy et fermer les **Paramètres de proxy**.

5. Dans la partie supérieure de la boîte de dialogue FoxyProxy, changez le **Mode de sélection** pour **Utiliser les proxys en fonction de leurs types et priorités prédéfinis**, puis cliquez sur **Fermer**.

	![mode de sélection foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Après avoir suivi ces étapes, seules les requêtes pour les URL qui contiennent la chaîne __hn0__ seront acheminées via le tunnel SSL.

## Accès au bloc-notes Zeppelin

Une fois les tunnels SSH configurés, vous pouvez accéder au bloc-notes Zeppelin sur le cluster Spark en suivant les étapes ci-dessous. Dans cette section, vous allez apprendre à exécuter des instructions %sql et %hive.

1. À partir du navigateur Web, ouvrez le système d’extrémité suivant :

		http://hn0-myspar:9995

	* **hn0** correspond à headnode0
	* **myspar** correspond aux six premières lettres du nom du cluster Spark.
	* **9995** est le port sur lequel le bloc-notes Zeppelin est accessible.

2. Créer un nouveau bloc-notes. Dans le volet d'en-tête, cliquez sur **Bloc-notes**, puis sur **Créer une note**.

	![Créer un bloc-notes Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Créer un nouveau bloc-notes Zeppelin")

	Dans la même page, sous le titre **Bloc-notes**, un nouveau bloc-notes dont le nom commence par **Note XXXXXXXXX** doit s’afficher. Cliquez sur le nouveau bloc-notes.

3. Sur la page web du nouveau bloc-notes, cliquez sur l'en-tête et modifiez le nom du bloc-notes si vous le souhaitez. Appuyez sur ENTRÉE pour enregistrer la modification de nom. Vérifiez également que l’en-tête du bloc-notes indique l’état **Connected** dans le coin supérieur droit.

	![État du bloc-notes Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "État du bloc-notes Zeppelin")

### Exécuter des instructions SQL

4. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\\HdiSamples\\SensorSampleData\\hvac**.

	Collez l’extrait suivant dans le paragraphe vide créé par défaut dans le nouveau bloc-notes.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Appuyez sur **MAJ + ENTRÉE** ou cliquez sur le bouton **Lire** pour que le paragraphe exécute l'extrait de code. L’état indiqué dans le coin supérieur droit du paragraphe doit progresser de READY, PENDING, RUNNING à FINISHED. Le résultat s’affiche au bas du même paragraphe. La capture d’écran ressemble à ceci :

	![Créer une table temporaire à partir de données brutes](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Créer une table temporaire à partir de données brutes")

	Vous pouvez également indiquer un titre pour chaque paragraphe. Dans le coin droit, cliquez sur l’icône **Settings**, puis sur **Show title**.

5. Vous pouvez maintenant exécuter des instructions Spark SQL dans la table **hvac**. Collez la requête suivante dans un nouveau paragraphe. La requête récupère l’ID de bâtiment et la différence entre les températures cibles et réelles pour chaque bâtiment à une date donnée. Appuyez sur **MAJ + ENTRÉE**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	L'instruction **%sql** du début indique au bloc-notes d'utiliser l'interpréteur Spark SQL. Vous pouvez consulter les interpréteurs définis dans l'onglet **Interpreter** dans l'en-tête du bloc-notes.

	La capture d’écran qui suit présente le résultat.

	![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")

	 Cliquez sur les options d’affichage (mis en exergue dans un rectangle) pour basculer entre les différentes représentations du même résultat. Cliquez sur **Paramètres** pour choisir ce qui constitue la clé et les valeurs dans le résultat. La capture d'écran ci-dessus utilise la clé **buildingID** et la moyenne **temp\_diff** comme valeur.

	
6. Vous pouvez également exécuter des instructions Spark SQL à l’aide de variables dans la requête. L’extrait suivant montre comment définir la variable **Temp** dans la requête avec les valeurs possibles d’interrogation. Lors de la première exécution de la requête, une liste déroulante est automatiquement renseignée avec les valeurs que vous avez spécifiées pour la variable.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Collez cet extrait dans un nouveau paragraphe, puis appuyez sur **MAJ + ENTRÉE**. La capture d’écran qui suit présente le résultat.

	![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")

	Pour les requêtes suivantes, vous pouvez sélectionner une nouvelle valeur dans la liste déroulante et réexécuter la requête. Cliquez sur **Settings** pour choisir ce qui constitue la clé et les valeurs dans le résultat. La capture d'écran ci-dessus utilise la clé **buildingID**, la moyenne **temp\_diff** comme valeur, et le groupe **targettemp**.

7. Redémarrez l’interpréteur Spark SQL pour quitter l’application. Cliquez sur l’onglet **Interpreter** en haut de l’écran et, pour l’interpréteur Spark, cliquez sur **Restart**.

	![Redémarrer l’interpréteur Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Redémarrez l'interpréteur Zeppelin")

### Exécutez des instructions hive

1. À partir du bloc-notes Zeppelin, cliquez sur le bouton **Interpreter**.

	![Mettre à jour l’interpréteur Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Mettre à jour l’interpréteur Hive")

2. Pour l’interpréteur **hive**, cliquez sur **edit** (modifier).

	![Mettre à jour l’interpréteur Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Mettre à jour l’interpréteur Hive")

	Mettez les propriétés suivantes à jour.

	* Définissez **default.password** sur le mot de passe que vous avez spécifié pour l’utilisateur administrateur lors de la création du cluster HDInsight Spark.
	* Définissez **default.url** sur `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Remplacez **<nom\_cluster\_spark>** par le nom de votre cluster Spark.
	* Définissez **default.user** sur le nom de l’utilisateur administrateur que vous avez spécifié lors de la création du cluster. Par exemple, *admin*.

3. Cliquez sur **Save** (Enregistrer) et lorsque vous êtes invité à redémarrer l’interpréteur hive, cliquez sur **OK**.

4. Créez un nouveau bloc-notes et exécutez l’instruction suivante pour répertorier toutes les tables hive sur le cluster.

		%hive
		SHOW TABLES

	Par défaut, un cluster HDInsight contient un exemple de table nommé **hivesampletable**, donc la sortie suivante doit s’afficher.

	![Sortie Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Sortie Hive")

5. Exécutez l’instruction suivante pour répertorier les enregistrements dans la table.

		%hive
		SELECT * FROM hivesampletable LIMIT 5

	Un résultat similaire à ce qui suit doit s’afficher.

	![Sortie Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Sortie Hive")

## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scénarios

* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Créer et exécuter des applications

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Outils et extensions

* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0727_2016-->