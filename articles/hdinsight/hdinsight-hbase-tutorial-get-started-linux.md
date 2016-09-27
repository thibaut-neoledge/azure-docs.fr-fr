<properties
	pageTitle="Didacticiel HBase : prise en main de HBase dans Hadoop | Microsoft Azure"
	description="Suivez ce didacticiel HBase pour apprendre à utiliser Apache HBase avec Hadoop dans HDInsight. Créez des tables à partir de l’interpréteur de commandes HBase et interrogez-les à l’aide de Hive."
	keywords="apache hbase,hbase,shell hbase,didacticiel hbase"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/25/2016"
	ms.author="jgao"/>



# Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight Linux 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Découvrez comment créer un cluster HBase dans HDInsight, créer des tables HBase et les interroger à l’aide de Hive. Pour obtenir des informations générales sur HBase, consultez la page [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

Les informations présentes sur le document sont spécifiques aux clusters HDInsight sous Linux. Pour plus d’informations sur les clusters Windows, utilisez le sélecteur d’onglets en haut de la page pour basculer.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###Composants requis

Avant de commencer ce didacticiel sur HBase, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell(SSU)](hdinsight-hadoop-linux-use-ssh-unix.md).
- [curl](http://curl.haxx.se/download.html).

## Créer un cluster HBase

La procédure suivante utilise un modèle Azure Resource Manager pour créer un cluster HBase. Pour comprendre les paramètres utilisés dans la procédure et d’autres méthodes de création de cluster, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle est situé dans un conteneur d’objets blob public.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2FHbase.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir du panneau **Paramètres**, saisissez les informations suivantes :

    - **ClusterName** : entrez un nom pour le cluster HBase que vous allez créer.
    - **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
    - **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**. Vous pouvez le renommer.
     
    Tous les autres paramètres sont facultatifs.
    
    Chaque cluster possède une dépendance de compte Azure Blob Storage. Après avoir supprimé un cluster, les données sont conservées dans le compte de stockage. Le nom du compte de stockage par défaut du cluster est le nom du cluster suivi du suffixe « store ». Il est codé en dur dans la section des variables du modèle.
        
3. Cliquez sur **OK** pour enregistrer les paramètres.
4. Dans le panneau **Déploiement personnalisé**, cliquez sur la zone de liste déroulante **Groupe de ressources**, puis cliquez sur **Nouveau** pour créer un nouveau groupe de ressources. Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et une autre ressource liée.
5. Cliquez sur **Conditions juridiques**, puis cliquez sur **Créer**.
6. Cliquez sur **Create**. La création d’un cluster prend environ 20 minutes.


>[AZURE.NOTE] Après la suppression d’un cluster HBase, vous pouvez créer un autre cluster HBase à l’aide du même conteneur d’objets blob par défaut. Le nouveau cluster utilisera les tables HBase créées dans le cluster d'origine. Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

## Créer des tables et insérer des données

Vous pouvez utiliser SSH pour vous connecter à des clusters HBase et utiliser le Shell HBase pour créer des tables HBase et insérer et interroger des données. Pour plus d’informations sur l’utilisation de SSH depuis Linux, Unix, OS X et Windows, consultez [Utilisation de SSH Hadoop Linux sur HDInsight à partir de Linux, Unix et OS X](hdinsight-hadoop-linux-use-ssh-unix.md) et [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Pour la plupart des utilisateurs, les données s’affichent sous la forme tabulaire :

![données tabulaires hbase hdinsight][img-hbase-sample-data-tabular]

Dans HBase, qui est une implémentation de BigTable, certaines données ont l’aspect suivant :

![données bigtable hbase hdinsight][img-hbase-sample-data-bigtable]

Cela deviendra plus clair une fois la procédure suivante terminée.


**Pour utiliser l’interpréteur de commandes HBase**

1. Depuis SSH, exécutez la commande suivante :

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

	Pour plus d'informations sur le schéma de la table Hbase, consultez [Introduction à la conception de schéma HBase][hbase-schema]. Pour plus de commandes HBase, voir [Guide de référence Apache HBase][hbase-quick-start].

6. Quittez l’interpréteur de commandes.

		exit



**Pour charger des données en bloc dans la table de contacts HBase**

HBase propose plusieurs méthodes pour charger des données dans des tables. Pour en savoir plus, consultez la rubrique [Chargement en bloc](http://hbase.apache.org/book.html#arch.bulk.load).


Un exemple de fichier de données a été chargé dans un conteneur d’objets blob public, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*. Le contenu du fichier de données est le suivant :

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

> [AZURE.NOTE] Cette procédure utilise la table de contacts HBase créée dans la dernière procédure.

1. Depuis SSH, exécutez la commande suivante pour transformer le fichier de données en StoreFiles et le stocker sur un chemin d’accès relatif spécifié par Dimporttsv.bulk.output : Si vous êtes dans le Shell HBase, utilisez la commande Quitter pour sortir.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput vers la table HBase :

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Vous pouvez ouvrir l’interpréteur de commandes HBase et utiliser la commande d’analyse pour répertorier le contenu de la table.



## Utiliser Hive pour interroger HBase

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

> [AZURE.NOTE] Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
>
> L’API REST est sécurisée à l’aide de l’[authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

		curl -u <UserName>:<Password> \
		-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Vous devez recevoir une réponse ayant l'aspect suivant :

		{"status":"ok","version":"v1"}

	Les paramètres utilisés dans cette commande sont les suivants :

	* **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
	* **-G** : indique qu’il s’agit d’une demande GET.

2. Utilisez la commande suivante pour répertorier les tables HBase existantes :

		curl -u <UserName>:<Password> \
		-G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Utilisez la commande suivante pour créer une table HBase avec deux familles de colonnes :

		curl -u <UserName>:<Password> \
		-X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
		-H "Accept: application/json" \
		-H "Content-Type: application/json" \
		-d "{"@name":"Contact1","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}" \
		-v

	Le schéma est fourni au format JSon.

4. Utilisez la commande suivante pour insérer des données :

		curl -u <UserName>:<Password> \
		-X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
		-H "Accept: application/json" \
		-H "Content-Type: application/json" \
		-d "{"Row":{"key":"MTAwMA==","Cell":{"column":"UGVyc29uYWw6TmFtZQ==", "$":"Sm9obiBEb2xl"}}}" \
		-v

	Vous devez coder en base64 les valeurs spécifiées dans le commutateur -d. Dans l’exemple :

	- MTAwMA==: 1000
	- UGVyc29uYWw6TmFtZQ==: Personal:Name
	- Sm9obiBEb2xl: John Dole

	[false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) vous permet d’insérer plusieurs valeurs (par lot).

5. Utilisez la commande suivante pour obtenir une ligne :

		curl -u <UserName>:<Password> \
		-X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
		-H "Accept: application/json" \
		-v

Pour plus d’informations sur HBase Rest, voir le [Guide de référence Apache HBase](https://hbase.apache.org/book.html#_rest).

## Vérification du statut du cluster

HBase dans HDInsight est livré avec une interface utilisateur web pour la surveillance des clusters. Elle vous permet de demander des statistiques ou des informations sur les régions.

Vous pouvez aussi utiliser SSH pour transférer des requêtes locales, telles que des demandes web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight. Pour plus d’informations, consultez [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Pour établir une session de tunnel SSH**

1. Ouvrez **PuTTY**.
2. Si vous avez fourni une clé SSH quand vous avez créé votre compte d’utilisateur pendant le processus de création, vous devez effectuer l’étape suivante pour sélectionner la clé privée à utiliser pour l’authentification auprès du cluster :

	Dans **Catégorie**, développez **Connexion**, puis **SSH** et sélectionnez **Auth**. Enfin, cliquez sur **Parcourir**, puis sélectionnez le fichier .ppk qui contient votre clé privée.

3. Dans **Catégorie**, cliquez sur **Session**.
4. Dans les options de base de l’écran de votre session PuTTY, entrez les valeurs suivantes :

	- **Nom d’hôte** : adresse SSH de votre serveur HDInsight dans le champ Nom d’hôte (ou Adresse IP). L’adresse SSH est le nom de votre cluster, elle est ensuite suivie de **-ssh.azurehdinsight.net**. Par exemple *mycluster-ssh.azurehdinsight.net*
	- **Port** : 22. Le port ssh sur le nœud principal est le port 22.
5. Dans la section **Catégorie** située à gauche de la boîte de dialogue, développez **Connexion** et **SSH**, puis cliquez sur **Tunnels**.
6. Indiquez les informations suivantes dans le formulaire des options de contrôle de transfert du port SSH.

	- **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, 9876.
	- **Dynamique** : active le routage dynamique du proxy SOCKS.
7. Cliquez sur **Ajouter** pour ajouter les paramètres.
8. Cliquez sur **Ouvrir** en bas de la boîte de dialogue pour ouvrir une connexion SSH.
9. Quand vous y êtes invité, connectez-vous au serveur à l’aide d’un compte SSH. Cela permettra d'établir une session SSH et d'activer le tunnel.

**Pour rechercher le nom de domaine complet des Zookeeper à l’aide d’Ambari**

1. Accédez à https://<ClusterName>.azurehdinsight.net/.
2. Entrez deux fois les informations d’identification du compte d’utilisateur de votre cluster.
3. Dans le menu de gauche, cliquez sur **zookeeper**.
4. Cliquez sur l’un des trois liens **ZooKeeper Server** dans la liste Summary.
5. Copiez la valeur de **Hostname**. Par exemple, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Pour configurer un programme client (Firefox) et vérifier l’état du cluster**

1. Ouvrez Firefox.
2. Cliquez sur le bouton **Ouvrir le menu**.
3. Cliquez sur **Options**.
4. Cliquez sur **Avancé**, sur **Réseau**, puis sur **Paramètres**.
5. Sélectionnez **Configuration manuelle du proxy**.
6. Saisissez les valeurs suivantes :

	- **Hôte Socks** : localhost
	- **Port** : utilisez le même port que celui que vous avez configuré pour le tunnel SSH Putty. Par exemple, 9876.
	- **SOCKS v5** : (sélectionné)
	- **DNS distant** : (sélectionné)
7. Cliquez sur **OK** pour enregistrer les modifications.
8. Accédez au http://&lt;The nom de domaine complet public d’un ZooKeeper>:60010/master-status.

Un lien vers le nœud principal HBase actif qui héberge l’interface utilisateur web est disponible dans un cluster haute disponibilité.

##Suppression du cluster

Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Étapes suivantes

Dans ce didacticiel HBase pour HDInsight, vous avez appris à créer un cluster HBase, à créer des tables et à afficher les données de ces tables à partir de l’interpréteur de commandes HBase. Vous avez également appris à utiliser une requête Hive pour interroger les données des tables HBase et à utiliser les API REST C# HBase pour créer une table HBase et en extraire les données.

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

<!---HONumber=AcomDC_0921_2016-->