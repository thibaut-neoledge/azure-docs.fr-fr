<properties
	pageTitle="Didacticiel HBase : prise en main de HBase dans Hadoop | Microsoft Azure"
	description="Suivez ce didacticiel HBase pour apprendre à utiliser Apache HBase avec Hadoop dans HDInsight. Créez des tables à partir de l’interpréteur de commandes HBase et interrogez-les à l’aide de Hive."
	keywords="apache hbase,hbase,hbase shell,hbase tutorial"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/18/2015"
	ms.author="jgao"/>



# Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight

Découvrez comment approvisionner un cluster HBase dans HDInsight, créer des tables HBase et les interroger à l’aide de Hive. Pour obtenir des informations générales sur HBase, consultez la page [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE]Les informations présentes sur le document sont spécifiques aux clusters HDInsight sous Linux. Pour plus d’informations sur les clusters Windows, consultez [Prise en main d’Apache HBase avec Hadoop dans HDInsight (Windows).

##Composants requis

Avant de commencer ce didacticiel sur HBase, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PuTTY et PuTTYGen pour les clients Windows. Ces utilitaires sont disponibles à l’adresse suivante : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- [curl](http://curl.haxx.se/download.html).

## Approvisionnement d’un cluster HBase

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Pour approvisionner un cluster HBase à l’aide du portail Azure en version préliminaire**


1. Connectez-vous à la [version préliminaire du portail Azure][azure-portal].
2. Cliquez sur **Nouveau** en haut à gauche, puis cliquez sur **Données + analyse**, **HDInsight**.
3. Saisissez les valeurs suivantes :

	- **Nom du cluster** : entrez un nom permettant d’identifier ce cluster.
	- **Type de cluster** : sélectionnez **HBase**.
	- **Système d’exploitation de cluster** : sélectionnez **Ubuntu**.
	- **Abonnement** : sélectionnez l’abonnement Azure utilisé pour approvisionner ce cluster.
	- **Groupe de ressources** : ajoutez ou sélectionnez un groupe de ressources Azure. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](resource-group-overview.md).
	- **Informations d’identification**. Entrez un mot de passe pour l’utilisateur du service web HTTP. Le nom d’utilisateur par défaut est **admin**. Vous devez également entrer un **Nom d’utilisateur SSH** et un **MOT DE PASSE** ou une **CLÉ PUBLIQUE**, qui seront utilisés pour authentifier l’utilisateur SSH. L'utilisation d'une clé publique est l'approche recommandée. Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

		- [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
		- [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) Cliquez sur **Sélectionner** pour enregistrer les modifications.
	- **Source de données** : sélectionnez un compte de stockage Azure existant ou créez-en un à utiliser comme système de fichiers par défaut pour le cluster. L’emplacement du compte de stockage détermine l’emplacement du cluster. Un cluster HDInsight et le compte de stockage Azure qui en dépend doivent se situer dans le même centre de données. Le nom du **Conteneur par défaut** est le nom du cluster.  
	- **Niveaux tarifaires de nœud** : sélectionnez le nombre de serveurs de région pour le cluster HBase.

		> [AZURE.WARNING]Pour la haute disponibilité des services HBase, vous devez configurer un cluster qui contient au moins **trois** nœuds. Cela garantit que si un nœud tombe en panne, les régions de données HBase sont disponibles sur d'autres nœuds.

	- **Configuration facultative** : sélectionnez la version de cluster, configurez un réseau virtuel Azure, configurez le metastore Hive/Oozie, configurez des actions de script et ajoutez des comptes de stockage supplémentaires.

4. Cliquez sur **Create**.

>[AZURE.NOTE]Après la suppression d’un cluster HBase, vous pouvez créer un autre cluster HBase à l’aide du même conteneur d’objets blob par défaut. Le nouveau cluster utilisera les tables HBase créées dans le cluster d'origine.

## Utilisation de l’interpréteur de commandes HBase

Pour la plupart des utilisateurs, les données s’affichent sous la forme tabulaire :

![données tabulaires hbase hdinsight][img-hbase-sample-data-tabular]

Dans HBase, qui est une implémentation de BigTable, certaines données ont l’aspect suivant :

![données bigtable hbase hdinsight][img-hbase-sample-data-bigtable]

Cela deviendra plus clair une fois la procédure suivante terminée.


**Pour utiliser l’interpréteur de commandes HBase**

>[AZURE.NOTE]Les étapes indiquées ici s’appliquent à un ordinateur Windows. Pour savoir comment se connecter à un cluster HDInsight Linux à partir de Linux, Unix ou OS X, consultez [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X (version préliminaire)](hdinsight-hadoop-linux-use-ssh-unix.md) 1. Ouvrez **PuTTY**. Consultez la section Composants requis au début de l’article. 2. Si vous avez fourni une clé SSH quand vous avez créé votre compte d’utilisateur pendant le processus d’approvisionnement, vous devez effectuer l’étape suivante pour sélectionner la clé privée à utiliser pour l’authentification auprès du cluster :

	In **Category**, expand **Connection**, expand **SSH**, and select **Auth**. Finally, click **Browse** and select the .ppk file that contains your private key.

3. Dans **Catégorie**, cliquez sur **Session**.
4. Dans les options de base de l’écran de votre session PuTTY, entrez les valeurs suivantes :

	- Host Name : adresse SSH de votre serveur HDInsight dans le champ Nom d’hôte (ou Adresse IP). L’adresse SSH est le nom de votre cluster, elle est ensuite suivie de **-ssh.azurehdinsight.net**. Par exemple : *moncluster-ssh.azurehdinsight.net*.
	- Port : 22. Le port SSH sur le headnode0 est 22. Consultez [Informations sur l’utilisation de HDInsight sur Linux (version préliminaire)](hdinsight-hadoop-linux-information.md#remote-access-to-services).
4. Cliquez sur **Ouvrir** pour vous connecter au cluster.
5. Lorsque vous y êtes invité, entrez le nom d'utilisateur que vous avez entré lors la création du cluster. Si vous avez donné un mot de passe pour le compte d'utilisateur, il vous sera demandé d'entrer le mot de passe.
6. Exécutez la commande suivante :

		hbase shell

4. Créez une HBase contenant deux familles de colonne :

		create 'Contacts', 'Personal', 'Office'
		list
5. Insérez des données :

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![interpréteur de commandes hbase hadoop hdinsight][img-hbase-shell]

6. Récupérez une seule ligne :

		get 'Contacts', '1000'

	Comme il n’y a qu’une seule ligne, les résultats sont les mêmes que lors de l’utilisation de la commande d’analyse.

	Pour plus d’informations sur le schéma de la table HBase, consultez [Introduction à la conception de schéma HBase][hbase-schema]. Pour plus de commandes HBase, voir [Guide de référence Apache HBase][hbase-quick-start].


6. Quittez l’interpréteur de commandes.

		exit

**Pour charger des données en bloc dans la table de contacts HBase**

HBase propose plusieurs méthodes pour charger des données dans des tables. Pour en savoir plus, consultez la rubrique [Chargement en bloc](http://hbase.apache.org/book.html#arch.bulk.load).


Un exemple de fichier de données a été téléchargé dans un conteneur d’objets blob, wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt Le contenu du fichier de données est le suivant :

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Vous pouvez créer un fichier texte et le télécharger sur votre propre compte de stockage si vous le souhaitez. Pour obtenir des instructions, consultez la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data].

> [AZURE.NOTE]Cette procédure utilise la table de contacts HBase créée dans la dernière procédure.

1. Ouvrez **PuTTY** et connectez-vous au cluster. Consultez les instructions de la procédure précédente.
3. Exécutez la commande suivante pour transformer le fichier de données en StoreFiles et le stocker sur un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput vers la table HBase :

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Vous pouvez ouvrir l’interpréteur de commandes HBase et utiliser la commande d’analyse pour répertorier le contenu de la table.



## Utilisation de Hive pour interroger des tables HBase

Vous pouvez interroger les données des tables HBase à l’aide de Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.


1. Ouvrez **PuTTY** et connectez-vous au cluster. Consultez les instructions de la procédure précédente.
2. Ouvrez l’interpréteur de commandes Hive.

	hive
3. Exécutez le script HiveQL suivant pour créer une table Hive mappant à la table HBase. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé dans ce didacticiel en utilisant l’interpréteur de commandes HBase.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Exécutez le script HiveQL suivant. La requête Hive interroge les données dans la table HBase :

     	SELECT count(*) FROM hbasecontacts;

## Utilisation des API REST HBase à l’aide de Curl

> [AZURE.NOTE]Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
>
> L’API REST est sécurisée à l’aide de l’[authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Vous devez recevoir une réponse ayant l'aspect suivant :

    {"status":"ok","version":"v1"}

  Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** - The user name and password used to authenticate the request.
    * **-G** - Indicates that this is a GET request.

2. Utilisez la commande suivante pour répertorier les tables HBase existantes :

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Utilisez la commande suivante pour créer une table HBase avec deux familles de colonnes :

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"@name":"test","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}"

	Le schéma est fourni au format JSon.

4. Utilisez la commande suivante pour insérer des données :

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"Row":{"key":"1000","Cell":{"column":"Personal:Name", "$":"John Dole"}}}"

5. Utilisez la commande suivante pour obtenir une ligne :

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## Vérification du statut du cluster

HBase dans HDInsight est livré avec une interface utilisateur web pour la surveillance des clusters. Elle vous permet de demander des statistiques ou des informations sur les régions.

Vous pouvez aussi utiliser SSH pour transférer des requêtes locales, telles que des demandes web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight. Pour plus d’informations, consultez [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Windows (version préliminaire)](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Pour établir une session de tunnel SSH**

1. Ouvrez **PuTTY**.  
2. Si vous avez fourni une clé SSH quand vous avez créé votre compte d’utilisateur pendant le processus d’approvisionnement, vous devez effectuer l’étape suivante pour sélectionner la clé privée à utiliser pour l’authentification auprès du cluster :

	Dans **Catégorie**, développez **Connexion**, puis **SSH** et sélectionnez **Auth**. Enfin, cliquez sur **Parcourir**, puis sélectionnez le fichier .ppk qui contient votre clé privée.

3. Dans **Catégorie**, cliquez sur **Session**.
4. Dans les options de base de l’écran de votre session PuTTY, entrez les valeurs suivantes :

	- **Host Name** : adresse SSH de votre serveur HDInsight dans le champ Nom d’hôte (ou Adresse IP). L’adresse SSH est le nom de votre cluster, elle est ensuite suivie de **-ssh.azurehdinsight.net**. Par exemple *mycluster-ssh.azurehdinsight.net*
	- **Port** : 22. Le port SSH sur le headnode0 est 22.  
5. Dans la section **Category** située à gauche dans la boîte de dialogue, développez **Connection** et **SSH**, puis cliquez sur **Tunnels**.
6. Indiquez les informations suivantes dans le formulaire des options de contrôle de transfert du port SSH.

	- **Source port** : le port sur le client que vous souhaitez transférer. Par exemple, 9876.
	- **Dynamic** : active le routage dynamique du proxy SOCKS.
7. Cliquez sur **Add** pour ajouter les paramètres.
8. Cliquez sur **Open** en bas de la boîte de dialogue pour ouvrir une connexion SSH.
9. Quand vous y êtes invité, connectez-vous au serveur à l’aide d’un compte SSH. Cela permettra d'établir une session SSH et d'activer le tunnel.

**Pour rechercher le nom de domaine complet des Zookeeper à l’aide d’Ambari**

1. Accédez à https://<ClusterName>.azurehdinsight.net/.
2. Entrez deux fois les informations d’identification du compte d’utilisateur de votre cluster.
3. Dans le menu de gauche, cliquez sur **Zookeeper**.
4. Cliquez sur l’un des trois liens **ZooKeeper Server** dans la liste Summary.
5. Copiez la valeur de **Hostname**. Par exemple, zookeepernode0.zookeepernode-0-xxxxxxxxxxxxxxxxxxxx.c7.internal.cloudapp.net.

**Pour configurer un programme client (Firefox) et vérifier l’état du cluster**

1. Ouvrez Firefox.
2. Cliquer sur le bouton **Ouvrir le menu**.
3. Cliquez sur **Options**.
4. Cliquez sur **Avancé**, sur **Réseau**, puis sur **Paramètres**.
5. Sélectionnez **Configuration manuelle du proxy**.
6. Saisissez les valeurs suivantes :

	- **Hôte SOCKS** : localhost
	- **Port** : utilisez le même port que vous avez configuré pour le tunnel SSH Putty. Par exemple, 9876.
	- **SOCKS v5** : (sélectionné)
	- **DNS distant** : (sélectionné)
7. Cliquez sur **OK** pour enregistrer les modifications.
8. Accédez à http://<TheFQDN of a ZooKeeper>:60010/master-status

Un lien vers le nœud principal HBase actif qui héberge l’interface utilisateur web est disponible dans un cluster haute disponibilité.



## Et ensuite ?
Dans ce didacticiel HBase pour HDInsight, vous avez appris à approvisionner un cluster HBase, à créer des tables et à afficher les données de ces tables à partir de l’interpréteur de commandes HBase. Vous avez également appris à utiliser une requête Hive pour interroger les données des tables HBase et à utiliser les API REST C# HBase pour créer une table HBase et en extraire les données.

Pour plus d'informations, consultez les rubriques suivantes :

- [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview] : HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de grandes quantités de données non structurées et semi-structurées.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=Oct15_HO3-->