<properties
   pageTitle="Conseils sur l’utilisation de Hadoop sur un cluster HDInsight basé sur Linux | Microsoft Azure"
   description="Obtenez des conseils d’implémentation concernant l’utilisation de clusters HDInsight (Hadoop) basés sur Linux dans un environnement Linux familier, exécuté dans le cloud Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>


<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/24/2015"
   ms.author="larryfr"/>


# Informations sur l’utilisation de HDInsight sur Linux (version préliminaire)

Les clusters Azure HDInsight sous Linux fournissent Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure. En principe, il fonctionne comme tout autre Hadoop sur une installation Linux. Ce document présente des différences spécifiques que vous devriez connaître.

## Noms de domaine

Le nom de domaine complet (FQDN) à utiliser pour se connecter au cluster est **&lt;clustername>.azurehdinsight.net** ou (pour SSH exclusivement) **&lt;clustername-ssh>.azurehdinsight.net**.

## Accès à distance aux services

* **Ambari (web)** - https://&lt;clustername>. azurehdinsight.net

	> [AZURE.NOTE]Authentifiez-vous à l’aide du nom d’utilisateur et du mot de passe de l’administrateur du cluster, puis connectez-vous à Ambari. Vous devrez utiliser là aussi le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
	>
	> L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

	Bien qu'Ambari pour votre cluster soit accessible directement sur Internet, certaines fonctionnalités dépendent de l'accès aux nœuds via le nom de domaine interne utilisé par le cluster. Puisqu’il s’agit d’un nom de domaine interne, et non public, des erreurs vous indiquant que le serveur est introuvable apparaîtront lorsque vous essayerez d’accéder à certaines fonctionnalités sur Internet.

	Pour contourner ce problème, utilisez un tunnel SSH pour rediriger le trafic Web via proxy vers le nœud principal du cluster. Utilisez la section **Tunneling SSH** des articles suivants pour créer un tunnel SSH d’un port de votre ordinateur local vers le cluster.

	* [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) : procédure pour créer un tunnel SSH à l’aide de la commande `ssh`.

	* [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows) : procédure pour utiliser Putty pour créer un tunnel SSH.

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
	>
	> L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
	>
	> L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net sur le port 22

	> [AZURE.NOTE]Vous pouvez accéder au nœud principal du cluster uniquement via SSH depuis une machine cliente. Une fois connecté, vous pouvez ensuite accéder aux nœuds de travail à l’aide de SSH depuis le nœud principal.

## Emplacements des fichiers

Les fichiers relatifs à Hadoop se trouvent sur les nœuds du cluster dans `/usr/hdp/current`.

Vous trouverez des exemples de données et de fichiers JAR sur le système HDSF (Hadoop HDFS Distributed File System) ou le stockage d’objets blob Azure dans ’/example’ ou ’wasb:///example’.

## HDFS, stockage d’objets blob Azure et meilleures pratiques de stockage

Dans la plupart des distributions Hadoop, le système HDFS est sauvegardé par un stockage local sur les machines dans le cluster. Bien que cela soit efficace, cette méthode peut être coûteuse pour une solution basée sur le cloud où vous êtes facturé à l'heure pour les ressources de calcul.

HDInsight utilise le stockage d’objets blob Azure comme stockage par défaut, ce qui offre les avantages suivants :

* Un stockage à long terme peu coûteux

* Un accès à partir de services externes tels que les sites Web, les utilitaires de téléchargement de fichier, les kits de développement logiciel (SDK) en différentes langues et les navigateurs Web

Puisqu'il s'agit d'un stockage par défaut pour HDInsight, vous n'avez normalement pas besoin de faire quoi que ce soit pour l'utiliser. Par exemple, la commande suivante listera les fichiers dans le dossier **/example/data**, qui est stocké sur le stockage d’objets blob Azure :

	hadoop fs -ls /example/data

Pour certaines commandes, vous pouvez être obligé de préciser que vous utilisez le stockage d’objets blob. Pour cela, vous pouvez ajouter à la commande le préfixe suivant : ****WASB://**

HDInsight vous permet également d’associer de multiples comptes de stockage d’objets blob à un cluster. Pour accéder à des données sur un compte de stockage d’objets blob qui n’est pas celui par défaut, vous pouvez utiliser le format **WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**. Par exemple, celui-ci listera le contenu du répertoire **/example/data** pour le conteneur et le compte de stockage indiqués :

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Quel stockage d’objets blob le cluster utilise-t-il ?

À la création du cluster, vous pouvez choisir d’utiliser un compte Azure Storage et un conteneur existants ou d’en créer de nouveaux. Vous avez peut-être oublié cela par la suite. Vous pouvez trouver le conteneur et le compte de stockage par défaut à l’aide de l’API REST Ambari.

1. Utilisez la commande suivante pour récupérer les informations de configuration HDFS :

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. Dans les données JSON renvoyées, recherchez l’entrée `fs.defaultFS`. Celle-ci contient les noms par défaut du conteneur et du compte de stockage dans un format similaire à ce qui suit :

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]Si vous avez installé [jq](http://stedolan.github.io/jq/), vous pouvez utiliser les éléments suivants pour retourner uniquement l’entrée `fs.defaultFS` :
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. Pour trouver la clé utilisée pour s’authentifier auprès du compte de stockage, ou pour rechercher les comptes de stockage secondaire associés au cluster, procédez comme suit :

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. Dans les données JSON renvoyées, recherchez les entrées commençant par `fs.azure.account.key`. Le reste du nom de l’entrée correspond au nom du compte de stockage. Par exemple : `fs.azure.account.key.mystorage.blob.core.windows.net`. La valeur stockée dans cette entrée est la clé utilisée pour s’authentifier auprès du compte de stockage.

	> [AZURE.TIP]Si vous avez installé [jq](http://stedolan.github.io/jq/), vous pouvez utiliser les éléments suivants pour renvoyer uniquement la liste des clés et valeurs :
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


### Comment accéder au stockage d’objets blob ?

Outre la commande Hadoop depuis le cluster, vous pouvez accéder aux objets blob de plusieurs façons :

* [Interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md) : commandes de l’interface de ligne de commande fonctionnant avec Azure. Après l’installation, utilisez la commande `azure storage` pour obtenir de l’aide sur l’utilisation du stockage ou la commande `azure blob` pour obtenir les commandes spécifiques aux objets blob.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage) : script python pour travailler avec des objets blob dans Azure Storage.

* Un ensemble de kits de développement logiciel (SDK) :

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.JS](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST d’Azure Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## Étapes suivantes

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->