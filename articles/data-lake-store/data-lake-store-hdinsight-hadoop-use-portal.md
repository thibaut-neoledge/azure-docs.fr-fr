<properties 
   pageTitle="Créer des clusters HDInsight Hadoop avec Azure Data Lake Store à l'aide du portail | Azure" 
   description="Utiliser le portail Azure pour créer et utiliser les clusters HDInsight Hadoop avec Azure Data Lake Store" 
   services="data-lake-store" 
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
   ms.date="02/03/2016"
   ms.author="nitinme"/>

# Créer un cluster HDInsight avec Data Lake Store à l'aide du portail Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Apprenez à utiliser le portail Azure pour créer un cluster HDInsight (Hadoop, HBase ou Storm) avec accès à Azure Data Lake Store. Points importants à prendre en compte pour cette version :

* **Pour les clusters Hadoop (Windows et Linux)**, le Data Lake Store n’est utilisable que comme compte de stockage supplémentaire. Le compte de stockage par défaut pour de tels clusters sera toujours Objets BLOB de stockage Azure (WASB).

* **Pour les clusters Storm (Windows et Linux)**, le Data Lake Store peut être utilisé pour écrire des données à partir d’une topologie Storm. Le Data Lake Store peut également servir à stocker des données de référence qui peuvent ensuite être lues par une topologie Storm.

* **Pour les clusters HBase (Windows et Linux)**, vous pouvez utiliser Data Lake Store comme stockage par défaut ou comme stockage supplémentaire. L’option permettant de créer des clusters HBase avec accès au Data Lake Store est disponible uniquement si vous utilisez HDI version 3.1 ou 3.2 (pour Windows) ou 3.2 (pour Windows et Linux).


## Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md). Une fois que vous avez créé le compte, effectuez les tâches suivantes pour télécharger des exemples de données. Vous aurez besoin de ces données plus tard dans ce didacticiel, pour exécuter des tâches à partir d’un cluster HDInsight ayant accès au Data Lake Store. 

	* [Créer un dossier dans votre Data Lake Store](data-lake-store-get-started-portal.md#createfolder).
	* [Charger un fichier dans votre Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).


## Créer un cluster HDInsight ayant accès au Data Lake Store.

Dans cette section, vous créez un cluster HDInsight Hadoop qui utilise le Data Lake Store comme stockage supplémentaire. Dans cette version, pour un cluster Hadoop, Data Lake Store ne peut être utilisé que comme stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les objets blob Azure Storage (WASB). Par conséquent, nous allons tout d'abord créer le compte de stockage et les conteneurs de stockage requis par le cluster.

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Suivez les étapes de la [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) pour lancer l'approvisionnement d'un cluster HDInsight.
 
3. Dans le panneau **Configuration facultative**, cliquez sur **Source de données**. Dans le panneau **Source de données**, spécifiez les détails du compte de stockage et du conteneur de stockage, choisissez **Est des États-Unis 2** comme **Emplacement**, puis cliquez sur **Identité AAD de cluster**.

	![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Ajouter un principal du service à un cluster HDInsight")

4. Dans le panneau **Identité AAD de cluster**, vous pouvez sélectionner un principal du service existant ou en créer un.
	
	* **Créer un principal du service**.
	
		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Nouveau**. Cliquez sur **Principal du service** puis, dans le panneau **Créer un principal du service**, fournissez les valeurs pour créer un nouveau principal du service. Dans ce cadre, un certificat et une application Azure Active Directory sont également créés. Cliquez sur **Create**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Ajouter un principal du service à un cluster HDInsight")

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Gérer l’accès ADLS**. Le panneau affiche les comptes Data Lake Store associés à l’abonnement. Toutefois, vous pouvez uniquement affecter des autorisations au compte que vous avez créé. Sélectionnez les autorisations de lecture/écriture/exécution (READ/WRITE/EXECUTE) pour le compte auquel vous souhaitez associer le cluster HDInsight, puis cliquez sur **Enregistrer les autorisations**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Ajouter un principal du service à un cluster HDInsight")

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Télécharger le certificat** pour télécharger le certificat associé au principal du service que vous avez créé. Cela est utile si vous souhaitez utiliser le même principal du service à l’avenir, tout en créant des clusters HDInsight supplémentaires. Cliquez sur **Sélectionner**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Ajouter un principal du service à un cluster HDInsight")


	* **Choisir un principal du service existant**.

		* Dans le panneau **Identité AAD de cluster**, cliquez sur **Existant**, puis sur **Principal du service**. Dans le panneau **Sélectionner un principal du service**, recherchez un principal du service existant. Cliquez sur un nom de principal du service, puis sur **Sélectionner**.

			![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Ajouter un principal du service à un cluster HDInsight")

		* Dans le panneau **Identité AAD de cluster**, chargez le certificat (.pfx) associé au principal du service que vous avez sélectionné, puis saisissez le mot de passe du certificat.
		
		* Cliquez sur **Gérer l’accès ADLS**. Le panneau affiche les comptes Data Lake Store associés à l’abonnement. Toutefois, vous pouvez uniquement affecter des autorisations au compte que vous avez créé. Sélectionnez les autorisations de lecture/écriture/exécution (READ/WRITE/EXECUTE) pour le compte auquel vous souhaitez associer le cluster HDInsight, puis cliquez sur **Enregistrer les autorisations**.

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

	Vous pouvez également ouvrir directement Ambari en accédant à https://CLUSTERNAME.azurehdinsight.net dans un navigateur web (où **CLUSTERNAME** est le nom de votre cluster HDInsight).

2. Ouvrez la vue Hive. Sélectionnez l’ensemble de carrés dans le menu de la page (en regard du lien **Admin** et du bouton à droite de la page) pour répertorier les vues disponibles. Sélectionnez la vue **Hive**.

	![Sélection des vues ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Une page similaire à celle ci-dessous doit s'afficher :

	![Image de la page de vue hive, contenant une section de l’éditeur de requête](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. Dans la section **Éditeur de requête** de la page, collez l’instruction HiveQL suivante dans la feuille de calcul :

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Cliquez sur le bouton **Exécuter** au bas de l’**Éditeur de requête** pour démarrer la requête. Une section **Résultats du processus de requête** doit apparaître en dessous de l’**Éditeur de requête** et afficher des informations sur la tâche.

6. Une fois la requête terminée, la section **Résultats du processus de requête** affiche les résultats de l’opération. Les informations suivantes devraient s’afficher dans l’onglet **Résultats** :

7. Lancez la requête suivante pour vérifier que la table a été créée.

		SHOW TABLES;

	L’onglet **Résultats** doit afficher les éléments suivants :

		hivesampletable
		vehicles

	**vehicles** est la table que vous avez créée précédemment. **hivesampletable** est un exemple de table disponible dans tous les clusters HDInsight par défaut.

8. Vous pouvez également exécuter une requête pour récupérer des données de la table **vehicles**.

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

	Cliquez sur **Afficher les détails** correspondant à cette requête. Le résultat doit afficher les éléments suivants :

		hivesampletable
		vehicles

	**vehicles** est la table que vous avez créée précédemment. **hivesampletable** est un exemple de table disponible dans tous les clusters HDInsight par défaut.

5. Vous pouvez également exécuter une requête pour récupérer des données de la table **vehicles**.

		SELECT * FROM vehicles LIMIT 5;


## Accéder à Data Lake Store avec les commandes HDFS

Une fois que vous avez configuré le cluster HDInsight pour qu'il utilise Data Lake Store, vous pouvez utiliser les commandes de l'interpréteur de commandes HDFS pour accéder au magasin.

### Pour un cluster Linux

Dans cette section, vous allez utiliser SSH dans le cluster et exécuter les commandes HDFS. Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour en savoir plus sur l’utilisation de PuTTY, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

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


## Utiliser Data Lake Store dans une topologie Storm

Vous pouvez utiliser le Data Lake Store pour écrire des données à partir d’une topologie Storm. Pour obtenir des instructions sur la réalisation de ce scénario, consultez la page [Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Voir aussi

* [PowerShell : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0204_2016-->