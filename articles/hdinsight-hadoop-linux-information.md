<properties
   pageTitle="Ce qu'il faut savoir sur Hadoop dans HDInsight sous Linux | Azure"
   description="Les clusters HDInsight sous Linux fournissent Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Utilisation de HDInsight sous Linux (Aperçu)

Les clusters HDInsight sous Linux fournissent Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure. En principe, il fonctionne comme tout autre Hadoop sur une installation Linux. Ce document présente des différences spécifiques que vous devriez connaître.

##Noms de domaine

Le nom de domaine complet (FQDN) à utiliser pour se connecter au cluster est **&lt;clustername>.azurehdinsight.net** ou (pour SSH uniquement) **&lt;clustername>.aurehdinsight.net**.

##Services accessibles à distance

* **Ambari (Web)** : https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE] Authentifiez-vous à l'aide du nom d'utilisateur et du mot de passe de l'administrateur du cluster, puis connectez-vous à Ambari. Vous devrez utiliser là aussi le nom d'utilisateur et le mot de passe de l'administrateur du cluster.
	> 
	> L'authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

	Même si, pour votre cluster, Ambari est accessible directement via Internet, certaines fonctionnalités doivent accéder à des nœuds via le nom de domaine interne utilisé par le cluster. Puisqu'il s'agit d'un nom de domaine interne, et non public, vous allez recevoir des erreurs indiquant que le serveur est introuvable lorsque vous essaierez d'accéder à certaines fonctionnalités via Internet.

	Pour contourner ce problème, utilisez un tunnel SSH pour rediriger le trafic Web via proxy vers le nœud principal du cluster. Utilisez les articles suivants pour créer un tunnel SSH depuis un port sur votre machine locale vers le cluster.

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Utilisez SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix, ou OS X</a> - étapes de création d'un tunnel SSH à l'aide de la commande  `ssh`

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Utilisez SSH avec Hadoop dans HDInsight sous Linux à partir de Windows</a> - étapes d'utilisation de Putty pour la création d'un tunnel SSH

* **Ambari (REST)** : https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Authentifiez-vous avec le nom d'utilisateur et le mot de passe de l'administrateur du cluster.
	> 
	> L'authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** : https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Authentifiez-vous avec le nom d'utilisateur et le mot de passe de l'administrateur du cluster.
	> 
	> L'authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** : &lt;clustername>-ssh.azurehdinsight.net sur le port 22

	> [AZURE.NOTE] Vous pouvez accéder au nœud principal du cluster uniquement via SSH depuis une machine cliente. Une fois connecté, vous pouvez ensuite accéder aux nœuds de travail à l'aide de SSH depuis le nœud principal.

##Emplacements des fichiers

Les fichiers relatifs à Hadoop peuvent être trouvés sur les nœuds du cluster à  `/usr/hdp/current`.

Des exemples de données et de fichiers jar peuvent être trouvés sur le système de fichiers DFS Hadoop (HDFS) (WASB, Stockage d'objets blob Microsoft Azure) sous '/example' ou 'wasb:///example'.

##Meilleures pratiques relatives à HDFS, WASB et au stockage

Dans la plupart des distributions Hadoop, le système de fichiers DFS d'Hadoop est sauvegardé par un stockage local sur les machines dans le cluster. Bien que cela soit efficace, cette méthode peut être coûteuse pour une solution basée sur le cloud où vous êtes facturé à l'heure pour les ressources de calcul.

HDInsight utilise le stockage d'objets blob Azure comme stockage par défaut, ce qui offre les avantages suivants :

* Un stockage à long terme peu coûteux

* Un accès à partir de services externes tels que les sites Web, les utilitaires de téléchargement de fichier, les kits de développement logiciel (SDK) en différentes langues et les navigateurs Web

Puisqu'il s'agit d'un stockage par défaut pour HDInsight, vous n'avez normalement pas besoin de faire quoi que ce soit pour l'utiliser. Par exemple, la commande suivante listera les fichiers dans le dossier **/example/data**, qui est stocké sur le stockage d'objets blob Azure.

	hadoop fs -ls /example/data

Il se peut que vous deviez préciser que vous utilisez le stockage d'objets blob pour certaines commandes. Pour cela, vous pouvez ajouter à la commande le préfixe suivant : **WASB://**.

HDInsight vous permet également d'associer de multiples comptes de stockage d'objets blob à un cluster. Pour accéder à des données sur un compte de stockage d'objets blob qui n'est pas celui par défaut, vous pouvez utiliser le format **WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**. Par exemple, celui-ci listera les contenus du répertoire **/example/data** pour le conteneur et le compte de stockage indiqués.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

###Quel stockage d'objets blob le cluster utilise-t-il ?

À la création du cluster, vous pouvez choisir d'utiliser un compte de stockage et un conteneur existants ou d'en créer un nouveau. Vous avez peut-être oublié cela par la suite. Vous pouvez trouver le compte de stockage et le conteneur à l'aide des méthodes suivantes.

**Portail Azure**

1. Dans le <a href="https://manage.windowsazure.com/" target="_blank">Portail de gestion Azure</a>, sélectionnez votre cluster HDInsight.

2. Sélectionnez **Tableau de bord** en haut de la page.

3. Les comptes de stockage et les conteneurs sont listés dans la section **Ressources liées** de la page.

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Interface de ligne de commande multiplateforme Azure**

*À venir !*

###Comment accéder au stockage d'objets blob ?

Outre la commande Hadoop depuis le cluster, vous pouvez accéder aux objets blob de plusieurs façons :

* <a href="http://azure.microsoft.com/ documentation/articles/xplat-cli/" target="_blank">Interface de ligne de commande multiplateforme Azure</a> - après l'installation, consultez `azure storage` pour une aide sur l'utilisation du stockage, ou `azure blob` pour les commandes spécifiques aux objets blob.

* Un ensemble de kits de développement logiciel (SDK) :

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/fr-fr/library/azure/dd135733.aspx" target="_blank">API REST de stockage</a>


##Étapes suivantes

* [Utilisation de Hive avec HDInsight](../hdinsight-use-hive/)
* [Utilisation de Pig avec HDInsight](../hdinsight-use-pig/)
* [Utilisation des tâches MapReduce avec HDInsight](../hdinsight-use-mapreduce)


<!--HONumber=45--> 
