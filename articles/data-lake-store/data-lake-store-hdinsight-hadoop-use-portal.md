<properties
   pageTitle="Créer des clusters HDInsight avec Azure Data Lake Store à l'aide du portail | Azure"
   description="Utiliser le portail Azure pour créer et utiliser les clusters HDInsight avec Azure Data Lake Store"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/10/2016"
   ms.author="nitinme"/>

# Créer un cluster HDInsight avec Data Lake Store à l'aide du portail Azure

> [AZURE.SELECTOR]
- [Utilisation du portail](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utiliser PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Apprenez à utiliser le portail Azure pour créer un cluster HDInsight (Hadoop, HBase, Spark ou Storm) avec accès à Azure Data Lake Store. Points importants à prendre en compte pour cette version :

* **Pour les clusters Spark (Linux) et Hadoop (Windows et Linux)**, le Data Lake Store n’est utilisable que comme compte de stockage supplémentaire. Le compte de stockage par défaut pour de tels clusters sera toujours Objets BLOB de stockage Azure (WASB).

* **Pour les clusters Storm (Windows et Linux)**, le Data Lake Store peut être utilisé pour écrire des données à partir d’une topologie Storm. Le Data Lake Store peut également servir à stocker des données de référence qui peuvent ensuite être lues par une topologie Storm. Pour obtenir plus d’informations, consultez [Utiliser Data Lake Store dans une topologie Storm](#use-data-lake-store-in-a-storm-topology).

* **Pour les clusters HBase (Windows et Linux)**, vous pouvez utiliser Data Lake Store comme stockage par défaut ou comme stockage supplémentaire. Pour obtenir plus d’informations, consultez [Utiliser Data Lake Store avec les clusters HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] L’option permettant de créer des clusters HDInsight avec accès au Data Lake Store est disponible uniquement si vous utilisez HDInsight version 3.2 et 3.4 (pour des clusters Hadoop, HBase et Storm sur Windows et Linux). Pour les clusters Spark sur Linux, cette option est uniquement disponible sur des clusters HDInsight 3.4.


## Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md). Une fois que vous avez créé le compte, effectuez les tâches suivantes pour télécharger des exemples de données. Vous aurez besoin de ces données plus tard dans ce didacticiel, pour exécuter des tâches à partir d’un cluster HDInsight ayant accès au Data Lake Store.

	* [Créer un dossier dans votre Data Lake Store](data-lake-store-get-started-portal.md#createfolder).
	* [Charger un fichier dans votre Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Dépot Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## Les vidéos vous permettent-elles d’apprendre plus rapidement ?

Regardez les vidéos suivantes pour savoir comment approvisionner des clusters HDInsight ayant accès à Data Lake Store.

* [Créer un cluster HDInsight ayant accès à Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* Une fois le cluster configuré, [accédez aux données dans Data Lake Store à l’aide de scripts Hive et Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Créer un cluster HDInsight ayant accès au Data Lake Store.

Dans cette section, vous créez un cluster HDInsight Hadoop qui utilise le Data Lake Store comme stockage supplémentaire. Dans cette version, pour un cluster Hadoop, Data Lake Store ne peut être utilisé que comme stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les objets blob Azure Storage (WASB). Par conséquent, nous allons tout d'abord créer le compte de stockage et les conteneurs de stockage requis par le cluster.

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Suivez les étapes de la [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) pour lancer l'approvisionnement d'un cluster HDInsight.

3. Dans le panneau **Configuration facultative**, cliquez sur **Source de données**. Dans le panneau **Source de données**, spécifiez les détails du compte de stockage et du conteneur de stockage, choisissez **Est des États-Unis 2** comme **Emplacement**, puis cliquez sur **Identité AAD de cluster**.

	![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Ajouter un principal du service à un cluster HDInsight")

4. Dans le panneau **Identité AAD de cluster**, vous pouvez sélectionner un principal du service existant ou en créer un.

	* **Créer un principal du service.**

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Nouveau**. Cliquez sur **Principal du service** puis, dans le panneau **Créer un principal du service**, fournissez les valeurs pour créer un nouveau principal du service. Dans ce cadre, un certificat et une application Azure Active Directory sont également créés. Cliquez sur **Create**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Ajouter un principal du service à un cluster HDInsight")

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Gérer l’accès ADLS**. Le panneau affiche les comptes Data Lake Store associés à l’abonnement. Toutefois, vous pouvez uniquement affecter des autorisations au compte que vous avez créé. Sélectionnez les autorisations de lecture/écriture/exécution (READ/WRITE/EXECUTE) pour le compte que vous voulez associer au cluster HDInsight, puis cliquez sur **Enregistrer les autorisations**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Ajouter un principal du service à un cluster HDInsight")

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Télécharger le certificat** pour télécharger le certificat associé au principal du service que vous avez créé. Cela est utile si vous souhaitez utiliser le même principal du service à l’avenir, tout en créant des clusters HDInsight supplémentaires. Cliquez sur **Sélectionner**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Ajouter un principal du service à un cluster HDInsight")


	* **Choisir un principal du service existant**.

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Existant**, puis sur **Principal du service**. Dans le panneau **Sélectionner un principal du service**, recherchez un principal du service existant. Cliquez sur un nom de principal du service, puis sur **Sélectionner**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Ajouter un principal du service à un cluster HDInsight")

		* Dans le panneau **Identité AAD de cluster**, chargez le certificat (.pfx) associé au principal du service que vous avez sélectionné, puis indiquez le mot de passe du certificat.

		* Cliquez sur **Gérer l’accès ADLS**. Le panneau affiche les comptes Data Lake Store associés à l’abonnement. Toutefois, vous pouvez uniquement affecter des autorisations au compte que vous avez créé. Sélectionnez les autorisations de lecture/écriture/exécution (READ/WRITE/EXECUTE) pour le compte que vous souhaitez associer au cluster HDInsight, puis cliquez sur **Enregistrer les autorisations**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Ajouter un principal du service à un cluster HDInsight")

		* Cliquez sur **Enregistrer les autorisations**, puis sur **Sélectionner**.

6. Cliquez sur **Sélectionner** dans le panneau **Source des données** et poursuivez l'approvisionnement du cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Une fois le cluster approvisionné, vous pouvez vérifier que le principal du service est associé au cluster HDInsight. Pour ce faire, cliquez sur **Paramètres** dans le panneau du cluster, puis sur **Identité AAD de cluster**. Vous devez alors voir le principal du service associé.

	![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Ajouter un principal du service à un cluster HDInsight")

## Exécuter des tâches de test sur le cluster HDInsight pour utiliser Azure Data Lake Store

Après avoir configuré un cluster HDInsight, vous pouvez exécuter des tâches de test sur le cluster pour vérifier que le cluster HDInsight peut accéder aux données dans Azure Data Lake Store. Pour ce faire, nous allons exécuter quelques requêtes hive ayant pour cible Data Lake Store.

### Pour un cluster Linux

1. Ouvrez le panneau du cluster que vous venez d'approvisionner, puis cliquez sur **Tableau de bord**. Vous accédez alors à Ambari pour le cluster Linux. En accédant à Ambari, vous êtes invité à vous authentifier sur le site : Saisissez l’administrateur (administrateur par défaut), le nom du compte et le mot de passe que vous avez utilisés lors de la création du cluster.

	![Tableau de bord du cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Tableau de bord du cluster")

	Vous pouvez aussi ouvrir directement Ambari en accédant à https://CLUSTERNAME.azurehdinsight.net dans un navigateur web (**CLUSTERNAME** représentant le nom de votre cluster HDInsight).

2. Ouvrez la vue Hive. Sélectionnez l’ensemble de carrés dans le menu de la page (en regard du lien **Admin** et du bouton à droite de la page) pour répertorier les vues disponibles. Sélectionnez la vue **Hive**.

	![Sélection des vues ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Une page similaire à celle ci-dessous doit s'afficher :

	![Image de la page de vue hive, contenant une section de l’éditeur de requête](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. Dans la section **Éditeur de requête** de la page, collez l’instruction HiveQL suivante dans la feuille de calcul :

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Cliquez sur le bouton **Exécuter** au bas de l’**Éditeur de requête** pour démarrer la requête. Une section **Résultats du processus de requête** doit apparaître en dessous de l’**Éditeur de requête** et afficher des informations sur le travail.

6. Une fois la requête terminée, la section **Résultats du processus de requête** affiche les résultats de l’opération. Les informations suivantes devraient s’afficher dans l’onglet **Résultats** :

7. Lancez la requête suivante pour vérifier que la table a été créée.

		SHOW TABLES;

	L’onglet **Résultats** doit afficher les éléments suivants :

		hivesampletable
		vehicles

	**vehicles** est la table que vous avez créée précédemment. **hivesampletable** est un exemple de table disponible dans tous les clusters HDInsight par défaut.

8. Vous pouvez aussi exécuter une requête pour récupérer des données de la table **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

### Pour un cluster Windows

1. Ouvrez le panneau du cluster que vous venez d'approvisionner, puis cliquez sur **Tableau de bord**.

	![Tableau de bord du cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Tableau de bord du cluster")

	Quand vous y êtes invité, entrez les informations d'identification administrateur du cluster.

2. La console de requête Microsoft Azure HDInsight s'ouvre. Cliquez sur **Éditeur Hive**.

	![Ouvrir l'éditeur Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Ouvrir l'éditeur Hive")

3. Dans l'éditeur Hive, entrez la requête suivante, puis cliquez sur **Soumettre**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	Dans cette requête Hive, nous créons une table à partir des données stockées dans Data Lake Store à l'adresse `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Cet emplacement contient un fichier d'exemples de données que vous devez avoir téléchargé au préalable.

	La table **Session de la tâche** en bas indique l'état de la tâche, qui passe d'**Initialisation** à **En cours d'exécution**, puis à **Terminée**. Vous pouvez également cliquer sur **Afficher les détails** pour plus d'informations sur la tâche terminée.

	![Créer une table](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Créer une table")

4. Lancez la requête suivante pour vérifier que la table a été créée.

		SHOW TABLES;

	Cliquez sur **Afficher les détails** correspondant à cette requête. Le résultat doit afficher les éléments suivants :

		hivesampletable
		vehicles

	**vehicles** est la table que vous avez créée précédemment. **hivesampletable** est un exemple de table disponible dans tous les clusters HDInsight par défaut.

5. Vous pouvez aussi exécuter une requête pour récupérer des données de la table **vehicles**.

		SELECT * FROM vehicles LIMIT 5;


## Accéder à Data Lake Store avec les commandes HDFS

Une fois que vous avez configuré le cluster HDInsight pour qu'il utilise Data Lake Store, vous pouvez utiliser les commandes de l'interpréteur de commandes HDFS pour accéder au magasin.

### Pour un cluster Linux

Dans cette section, vous allez utiliser SSH dans le cluster et exécuter les commandes HDFS. Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Une fois connecté, utilisez la commande du système de fichiers HDFS suivante pour répertorier les fichiers dans le Data Lake Store.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Le fichier que vous avez téléchargé dans Data Lake Store doit y figurer.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Vous pouvez également utiliser la commande `hdfs dfs -put` pour charger des fichiers dans Data Lake Store, puis utiliser `hdfs dfs -ls` pour vérifier si les fichiers ont été chargés avec succès.


### Pour un cluster Windows

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Cliquez sur **Parcourir**, puis sur **Clusters HDInsight** et enfin sur le cluster HDInsight que vous avez créé.

3. Dans le panneau du cluster, cliquez sur **Bureau à distance** puis, dans le panneau **Bureau à distance**, cliquez sur **Connexion**.

	![Accès à distance à un cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Créer un groupe de ressources Azure")

	Lorsque vous y êtes invité, entrez les informations d'identification que vous avez fournies à l'utilisateur du bureau à distance.

4. Dans la session à distance, lancez Windows PowerShell et utilisez les commandes du système de fichiers HDFS pour lister les fichiers dans Azure Data Lake Store.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Le fichier que vous avez téléchargé dans Data Lake Store doit y figurer.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	Vous pouvez également utiliser la commande `hdfs dfs -put` pour charger des fichiers dans Data Lake Store, puis utiliser `hdfs dfs -ls` pour vérifier si les fichiers ont été chargés avec succès.

## Utilisation de Data Lake Store avec Spark cluster

Dans cette section, vous utilisez le bloc-notes Jupyter disponible avec les clusters HDInsight Spark pour exécuter une tâche qui lit les données d’un compte Data Lake Store que vous avez associé un cluster HDInsight Spark, plutôt que le compte d’objets blob Azure Storage par défaut.

1. Copiez quelques données d’exemple du compte de stockage par défaut (WASB) associé au cluster Spark dans le compte Azure Data Lake associé au cluster. Vous pouvez utiliser l[’outil ADLCopy](http://aka.ms/downloadadlcopy) pour cette opération. Téléchargez et installez l’outil à l’aide du lien.

2. Ouvrez une invite de commandes et accédez au répertoire où vous avez installé AdlCopy, généralement `%HOMEPATH%\Documents\adlcopy`.

3. Exécutez la commande suivante pour copier un objet blob spécifique du conteneur source vers Data Lake Store :

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Pour ce didacticiel, copiez le fichier de données d’exemple **HVAC.csv** situé dans **/HdiSamples/HdiSamples/SensorSampleData/hvac/** dans le compte Azure Data Lake. L’extrait de code doit ressembler à ceci :

		AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	>[AZURE.WARNING] Assurez-vous que la casse des noms de fichiers et du chemin est correcte.

4. Vous êtes invité à entrer les informations d’identification de l’abonnement Azure sous lequel vous disposez d’un compte Data Lake Store. Vous devez voir une sortie similaire à ce qui suit :

		Initializing Copy.
		Copy Started.
		100% data copied.
		Copy Completed. 1 file copied.

	Le fichier de données (**HVAC.csv**) sera copié dans un dossier **/hvac** du compte Data Lake Store.

4. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

	![Créer un bloc-notes Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes est créé et ouvert sous le nom **Untitled.pynb**.

4. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Pour cela, collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

		from pyspark.sql.types import *
		
	À chaque exécution d’une tâche dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

	 ![État d’un travail de bloc-notes Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "État d’un travail de bloc-notes Jupyter")

4. Chargez des exemples de données dans une table temporaire à l’aide du fichier **HVAC.csv** que vous avez copié dans le compte Data Lake Store. Vous pouvez accéder aux données du compte Data Lake Store à l’aide du modèle d’URL suivant.

		adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

	Dans une cellule vide, collez l’exemple de code suivant, remplacez **MYDATALAKESTORE** par votre nom de compte Data Lake Store, puis appuyez sur **MAJ + ENTRÉE**. Cet exemple de code enregistre les données dans une table temporaire appelée **hvac**.

		# Load the data
		hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. Étant donné que vous utilisez un noyau PySpark, vous pouvez maintenant exécuter directement une requête SQL sur la table temporaire **hvac** que vous venez de créer à l’aide de la méthode magique `%%sql`. Pour plus d’informations sur la méthode magique `%%sql`, ainsi que les autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. Une fois le travail terminé, le résultat tabulaire suivant s’affiche par défaut.

 	![Sortie de table du résultat de la requête](./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "Sortie de table du résultat de la requête")

	Vous pouvez également voir les résultats dans d’autres visualisations. Par exemple, un graphique en aires pour le même résultat se présenterait comme suit.

	![Graphique en aires du résultat de la requête](./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "Graphique en aires du résultat de la requête")


6. Une fois que vous avez terminé l’exécution de l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.

## Utiliser Data Lake Store dans une topologie Storm

Vous pouvez utiliser le Data Lake Store pour écrire des données à partir d’une topologie Storm. Pour obtenir des instructions sur la réalisation de ce scénario, consultez [Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Utiliser Data Lake Store avec les clusters HBase

Avec les clusters HBase, vous pouvez utiliser Data Lake Store comme stockage par défaut, ainsi que comme stockage supplémentaire. Pour ce faire :

1.  Dans le panneau **Source des données**, pour **Emplacement des données HBase**, sélectionnez **Data Lake Store**.
2.  Sélectionnez le nom du Data Lake Store que vous souhaitez utiliser ou créez-en un.
3.  Enfin, spécifiez le **dossier racine HBase** au sein de Data Lake Store. Si le compte Data Lake Store ne dispose pas d’un dossier racine, créez-en un.

	![HBase avec Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Créer un groupe de ressources Azure")

### Considérations sur l’utilisation de Data Lake Store en tant que stockage par défaut pour les clusters HBase

* Vous pouvez utiliser le même compte Data Lake Store pour plusieurs clusters HBase. Cependant, le **dossier racine HBase** que vous fournissez pour le cluster (étape 4 dans la capture d’écran ci-dessus) doit être unique. Vous **ne devez pas** utiliser le même dossier racine pour deux clusters HBase différents.
* Même si vous utilisez un compte Data Lake Store en tant que stockage par défaut, les fichiers journaux du cluster HBase sont toujours stockés dans les objets blob d’Azure Storage (WASB) associés au cluster. Cela est mis en évidence dans la zone bleue de la capture d’écran ci-dessus.



## Voir aussi

* [PowerShell : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0615_2016-->