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
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Créer un cluster HDInsight avec Data Lake Store à l'aide du portail Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Apprenez à utiliser le portail Azure pour créer un cluster HDInsight (Hadoop, HBase ou Storm) avec accès à Azure Data Lake Store. Points importants à prendre en compte pour cette version :

* **Pour les clusters Hadoop et Storm (Windows et Linux)**, le Data Lake Store n'est utilisable que comme compte de stockage supplémentaire. Le compte de stockage par défaut pour de tels clusters sera toujours Objets BLOB de stockage Azure (WASB).

* **Pour les clusters HBase (Windows et Linux)**, vous pouvez utiliser Data Lake Store comme stockage par défaut ou comme stockage supplémentaire.


Dans cet article, nous approvisionnons un cluster Hadoop avec Data Lake Store comme stockage supplémentaire. Pour configurer HDInsight afin qu'il fonctionne avec Data Lake Store à l'aide du portail Azure, la procédure est la suivante :

* Créer un cluster HDInsight avec authentification à un principal du service Azure Active Directory
* Configurer l'accès Data Lake Store à l'aide du même principal du service
* Lancer une tâche de test sur le cluster

## Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).


## Créer un cluster HDInsight avec authentification à un principal du service Azure Active Directory

Dans cette section, vous créez un cluster HDInsight Hadoop qui utilise le Data Lake Store comme stockage supplémentaire. Dans cette version, pour un cluster Hadoop, Data Lake Store ne peut être utilisé que comme stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les objets blob Azure Storage (WASB). Par conséquent, nous allons tout d'abord créer le compte de stockage et les conteneurs de stockage requis par le cluster.

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Suivez les étapes de la [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) pour lancer l'approvisionnement d'un cluster HDInsight.
 
3. Dans le panneau **Configuration facultative**, cliquez sur **Source de données**. Dans le panneau **Source de données**, spécifiez les détails du compte de stockage et du conteneur de stockage, choisissez **Est des États-Unis 2** comme **Emplacement**, puis cliquez sur **Identité AAD de cluster**.

	![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Ajouter un principal du service à un cluster HDInsight")

4. Dans le panneau **Identité AAD de cluster**, vous pouvez sélectionner un principal du service existant ou en créer un.
	
	* **Créer un principal du service**. Dans le panneau **Identité AAD de cluster**, cliquez sur **Nouveau**. Cliquez sur **Principal du service** puis, dans le panneau **Créer un principal du service**, fournissez les valeurs pour créer un nouveau principal du service. Dans ce cadre, un certificat et une application Azure Active Directory sont également créés. Cliquez sur **Create**.

		![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Ajouter un principal du service à un cluster HDInsight")

		Dans le panneau **Identité AAD de cluster**, cliquez sur **Sélectionner** pour poursuivre la création du principal du service.

		![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Ajouter un principal du service à un cluster HDInsight")


	* **Choisir un principal du service existant**. Dans le panneau **Identité AAD de cluster**, cliquez sur **Existant**. Cliquez sur **Principal du service** puis, dans le panneau **Sélectionner un principal du service**, recherchez un principal du service existant. Cliquez sur un nom de principal du service, puis sur **Sélectionner**.

		![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Ajouter un principal du service à un cluster HDInsight")

		Dans le panneau **Identité AAD de cluster**, téléchargez le certificat (.pfx) que vous avez créé précédemment et fournissez le mot de passe que vous avez utilisé pour créer le certificat. Cliquez sur **Sélectionner**. La configuration d'Azure Active Directory pour le cluster HDInsight est terminée.

		![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Ajouter un principal du service à un cluster HDInsight")

6. Cliquez sur **Sélectionner** dans le panneau **Source des données** et poursuivez l'approvisionnement du cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Une fois le cluster approvisionné, vous pouvez vérifier que le principal du service est associé au cluster HDInsight. Pour ce faire, cliquez sur **Paramètres** dans le panneau du cluster, puis sur **Identité AAD de cluster**. Vous devez alors voir le principal du service associé.

	![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Ajouter un principal du service à un cluster HDInsight")

## <a name="acl"></a>Configurer le principal du service pour qu'il accède au système de fichiers Data Lake Store

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Si vous ne disposez pas d'un compte Data Lake Store, créez-en un. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).

	Si vous disposez déjà d'un compte Data Lake Store, cliquez sur **Parcourir** dans le volet gauche, puis sur **Data Lake Store**, et enfin sur le nom du compte auquel vous souhaitez accorder l'accès.

	Effectuez les tâches suivantes sous votre compte Data Lake Store.

	* [Créer un dossier dans votre Data Lake Store](data-lake-store-get-started-portal.md#createfolder).
	* [Charger un fichier dans votre Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

	Vous utiliserez ultérieurement les fichiers téléchargés, lorsque vous testerez le compte Data Lake Store avec le cluster HDInsight.

3. Dans le panneau Data Lake Store, cliquez sur **Explorateur de données**.

	![Explorateur de données](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "Explorateur de données")

4. Dans le panneau **Explorateur de données**, cliquez sur la racine de votre compte puis, dans le panneau de votre compte, cliquez sur l'icône **Accès**.

	![Définir des ACL sur le système de fichiers Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "Définir des ACL sur le système de fichiers Data Lake")

5. Le panneau **Accès** répertorie les accès standard et personnalisés déjà affectés à la racine. Cliquez sur l'icône **Ajouter** pour ajouter des ACL de niveau personnalisé et inclure le principal du service que vous avez créé précédemment.

	![Lister les accès standard et personnalisés](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "Lister les accès standard et personnalisés")

6. Cliquez sur l'icône **Ajouter** pour ouvrir le panneau **Ajouter un accès personnalisé**. Dans ce panneau, cliquez sur **Sélectionner un utilisateur ou un groupe** puis, dans le panneau **Sélectionner un utilisateur ou un groupe**, recherchez le principal du service créé précédemment dans Azure Active Directory. Le nom du principal du service créé précédemment est **HDIADL**. Cliquez sur le nom du principal du service, puis sur **Sélectionner**.

	![Ajouter un groupe](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "Ajouter un groupe")

7. Cliquez sur **Sélectionner des autorisations**, sélectionnez les autorisations que vous souhaitez attribuer au principal du service, puis cliquez sur **OK**.

	![Affecter des autorisations à un groupe](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "Affecter des autorisations à un groupe")

8. Dans le panneau **Ajouter un accès personnalisé**, cliquez sur **OK**. Le groupe récemment créé, avec les autorisations associées, sera désormais répertorié dans le panneau **Accès**.

	![Affecter des autorisations à un groupe](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "Affecter des autorisations à un groupe")

7. Si nécessaire, vous pouvez également modifier les autorisations d'accès après avoir ajouté le principal du service. Cochez ou décochez la case de chaque type d'autorisation (lecture, écriture, exécution) selon que vous souhaitez retirer ou affecter cette autorisation. Cliquez sur **Enregistrer** pour enregistrer les modifications, ou sur **Ignorer** pour annuler les modifications.



## Exécuter des tâches de test sur le cluster HDInsight pour utiliser Azure Data Lake Store

Après avoir configuré un cluster HDInsight, vous pouvez exécuter des tâches de test sur le cluster pour vérifier que le cluster HDInsight peut accéder aux données dans Azure Data Lake Store. Pour ce faire, nous allons exécuter quelques requêtes hive ayant pour cible Data Lake Store.

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

	Cliquez sur Afficher les détails correspondant à cette requête. Le résultat doit afficher les éléments suivants :

		hivesampletable
		vehicles

	**vehicles** est la table que vous avez créée précédemment. **hivesampletable** est un exemple de table disponible dans tous les clusters HDInsight par défaut.

5. Vous pouvez également exécuter une requête pour récupérer des données de **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

## Accéder à Data Lake Store avec les commandes HDFS

Une fois que vous avez configuré le cluster HDInsight pour qu'il utilise Data Lake Store, vous pouvez utiliser les commandes de l'interpréteur de commandes HDFS pour accéder au magasin.

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

## Considérations sur l'approvisionnement d'un cluster HBase qui utilise Data Lake Store comme stockage par défaut

Pour les clusters HBase, vous pouvez utiliser des comptes Data Lake Store comme stockage par défaut. Dans ce cas, le principal du service associé au cluster **doit** avoir accès au compte Data Lake Store pour que le cluster soit approvisionné. Vous pouvez vous en assurer de deux manières :

* **Si vous utilisez un principal du service existant**, vous devez vous assurer que le principal du service est ajouté à la liste de contrôle d'accès à la racine du système de fichiers Data Lake Store avant de lancer l'approvisionnement du cluster.
* **Si vous choisissez de créer un nouveau principal du service** dans le cadre de l'approvisionnement du cluster, vous devez ajouter le principal du service nouvellement créé à la racine du système de fichiers Data Lake Store dès le début de l'approvisionnement du cluster. Dans le cas contraire, le cluster sera approvisionné mais les services HBase ne pourront pas démarrer. Pour contourner ce problème, vous devez ajouter le principal du service à l'ACL du compte Data Lake Store, puis redémarrer les services HBase avec l'interface utilisateur web Ambari.

Pour obtenir des instructions sur l'ajout d'un principal du service à un système de fichiers Data Lake Store, consultez [Configurer le principal du service pour qu'il accède au système de fichiers Data Lake Store](#acl).



## Voir aussi

* [PowerShell : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0107_2016-->