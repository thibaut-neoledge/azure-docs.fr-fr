---
title: "Conseils sur l’utilisation de Hadoop sur un cluster HDInsight basé sur Linux | Microsoft Docs"
description: "Obtenez des conseils d’implémentation concernant l’utilisation de clusters HDInsight (Hadoop) basés sur Linux dans un environnement Linux familier, exécuté dans le cloud Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: f0b6630bc75c010a102d7202c86e89b640e2330c


---
# <a name="information-about-using-hdinsight-on-linux"></a>Informations sur l’utilisation de HDInsight sous Linux
Les clusters Azure HDInsight sous Linux fournissent Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure. En principe, il fonctionne comme tout autre Hadoop sur une installation Linux. Ce document présente des différences spécifiques que vous devriez connaître.

## <a name="prerequisites"></a>Conditions préalables
La plupart des étapes décrites dans ce document utilisent les utilitaires ci-après, que vous pouvez avoir besoin d’installer sur votre système.

* [cURL](https://curl.haxx.se/) : permet de communiquer avec les services Web.
* [jq](https://stedolan.github.io/jq/) : permet d’analyser des documents JSON.
* [Interface de ligne de commande Azure](../xplat-cli-install.md) - permet de gérer à distance les services Azure

    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## <a name="users"></a>Utilisateurs
Sauf s’il est [joint à un domaine](hdinsight-domain-joined-introduction.md), HDInsight doit être considéré comme un système **mono-utilisateur**. Un compte d’utilisateur SSH unique est créé avec le cluster, avec les autorisations de niveau administrateur. Des comptes SSH supplémentaires peuvent être créés, mais ils auront également l’accès administrateur au cluster.

HDInsight joint à un domaine prend en charge la présence de plusieurs utilisateurs et des paramètres d’autorisation et de rôles plus précis. Pour plus d’informations, consultez la section [Gestion des clusters HDInsight joints à un domaine](hdinsight-domain-joined-manage.md).

## <a name="domain-names"></a>Noms de domaine
Le nom de domaine complet (FQDN) à utiliser pour se connecter au cluster depuis Internet est **&lt;clustername>.azurehdinsight.net** ou (pour SSH exclusivement) **&lt;clustername-ssh>.azurehdinsight.net**.

En interne, chaque nœud du cluster porte un nom qui est attribué pendant la configuration du cluster. Pour trouver les noms de cluster, vous pouvez consulter la page **Hôtes** de l’interface utilisateur Web d’Ambari, ou utiliser ce qui suit pour obtenir la liste des hôtes à partir de l’API REST Ambari :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Remplacez **PASSWORD** par le mot de passe du compte d’administrateur et **CLUSTERNAME** par le nom de votre cluster. Ainsi sera retourné un document JSON qui contient la liste des hôtes du cluster. Ensuite, jq extrait la valeur de l’élément `host_name` pour chaque hôte du cluster.

Si vous avez besoin de trouver le nom du nœud d’un service spécifique, vous pouvez interroger Ambari pour obtenir ce composant. Par exemple, pour trouver les hôtes du nœud de nom HDFS, utilisez ce qui suit.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Un document JSON est alors retourné. Il décrit le service. Ensuite, jq extrait uniquement la valeur `host_name` des hôtes.

## <a name="remote-access-to-services"></a>Accès à distance aux services
* **Ambari (Web)** : https://&lt;clustername>.azurehdinsight.net

    Authentifiez-vous à l’aide du nom d’utilisateur et du mot de passe de l’administrateur du cluster, puis connectez-vous à Ambari. Vous devrez utiliser là aussi le nom d’utilisateur et le mot de passe de l’administrateur du cluster.

    L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

  > [!IMPORTANT]
  > Bien qu'Ambari pour votre cluster soit accessible directement sur Internet, certaines fonctionnalités dépendent de l'accès aux nœuds via le nom de domaine interne utilisé par le cluster. Puisqu’il s’agit d’un nom de domaine interne, et non public, des erreurs vous indiquant que le serveur est introuvable apparaîtront lorsque vous essayerez d’accéder à certaines fonctionnalités sur Internet.
  >
  > Pour bénéficier de toutes les fonctionnalités de l’interface utilisateur Web Ambari, vous devez utiliser un tunnel SSH pour assurer l’acheminement proxy vers le nœud principal cluster. Consultez [Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md)
  >
  >
* **Ambari (REST)** : https://&lt;clustername>.azurehdinsight.net/ambari

  > [!NOTE]
  > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
  >
  > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.
  >
  >
* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

  > [!NOTE]
  > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
  >
  > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.
  >
  >
* **SSH** - &lt;clustername>-ssh.azurehdinsight.net sur le port 22 ou 23. Le port 22 est utilisé pour se connecter au nœud principal primaire tandis que le port 23 est utilisé pour se connecter au nœud principal secondaire. Pour plus d’informations sur les nœuds principaux, consultez [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

  > [!NOTE]
  > Vous pouvez accéder aux nœuds principaux du cluster uniquement via SSH depuis une machine cliente. Une fois connecté, vous pouvez ensuite accéder aux nœuds Worker à l’aide de SSH depuis un nœud principal.
  >
  >

## <a name="file-locations"></a>Emplacements des fichiers
Les fichiers relatifs à Hadoop se trouvent sur les nœuds du cluster dans `/usr/hdp`. Le répertoire contient les sous-répertoires suivants :

* **2.2.4.9-1** : ce répertoire est nommé en fonction de la version de la plateforme de données Hortonworks utilisée par HDInsight. Le numéro de votre cluster peut être différent de celui qui figure ici.
* **En cours** : ce répertoire contient des liens vers des répertoires contenus dans le dossier **2.2.4.9-1** et existe afin que vous n’ayez pas à saisir de numéro de version (qui peut changer) à chaque fois que vous souhaitez accéder à un fichier.

Vous trouverez des exemples de données et de fichiers JAR sur le système HDSF (Hadoop HDFS Distributed File System) ou le stockage d’objets blob Azure dans '/example' ou 'wasbs:///example'.

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS, stockage d’objets blob Azure et meilleures pratiques de stockage
Dans la plupart des distributions Hadoop, le système HDFS est sauvegardé par un stockage local sur les machines dans le cluster. Bien que cela soit efficace, cette méthode peut être coûteuse pour une solution basée sur le cloud où vous êtes facturé à l’heure ou à la minute pour les ressources de calcul.

HDInsight utilise le stockage d’objets blob Azure comme stockage par défaut, ce qui offre les avantages suivants :

* Un stockage à long terme peu coûteux
* Un accès à partir de services externes tels que les sites Web, les utilitaires de téléchargement de fichier, les kits de développement logiciel (SDK) en différentes langues et les navigateurs Web

Puisqu'il s'agit d'un stockage par défaut pour HDInsight, vous n'avez normalement pas besoin de faire quoi que ce soit pour l'utiliser. Par exemple, la commande suivante listera les fichiers dans le dossier **/example/data** , qui est stocké sur le stockage d’objets blob Azure :

    hdfs dfs -ls /example/data

Pour certaines commandes, vous pouvez être obligé de préciser que vous utilisez le stockage d’objets blob. Pour cela, vous pouvez ajouter à la commande le préfixe **wasb://**, ou **wasbs://**.

HDInsight vous permet également d’associer de multiples comptes de stockage d’objets blob à un cluster. Pour accéder à des données sur un compte de stockage d’objets blob qui n’est pas celui par défaut, vous pouvez utiliser le format **wasbs://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**. Par exemple, celui-ci listera le contenu du répertoire **/example/data** pour le conteneur et le compte de stockage indiqués :

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Quel stockage d’objets blob le cluster utilise-t-il ?
À la création du cluster, vous pouvez choisir d’utiliser un compte Azure Storage et un conteneur existants ou d’en créer de nouveaux. Vous avez peut-être oublié cela par la suite. Vous pouvez trouver le conteneur et le compte de stockage par défaut à l’aide de l’API REST Ambari.

1. Utilisez la commande suivante pour récupérer les informations de configuration HDFS à l’aide de curl, puis filtrez ces informations avec [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'

   > [!NOTE]
   > Renvoie la première configuration appliquée au serveur (`service_config_version=1`), qui contiendra ces informations. Si vous récupérez une valeur qui a été modifiée après la création du cluster, vous devrez peut-être lister les versions de configuration et récupérer la plus récente.
   >
   >

    Renvoie une valeur similaire à la suivante, où **CONTAINER** est le conteneur par défaut et **ACCOUNTNAME** est le nom de compte Azure Storage :

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net
2. Récupérez le groupe de ressources du compte de stockage en utilisant [l’interface de ligne de commande Azure](../xplat-cli-install.md). Dans la commande suivante, remplacez **ACCOUNTNAME** par le nom du compte de stockage récupéré à partir d’Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'

    Renvoie le nom du groupe de ressources du compte.

   > [!NOTE]
   > Si cette commande ne renvoie aucun résultat, vous devrez peut-être modifier l'interface de ligne de commande Azure pour la faire passer en mode Azure Resource Manager et réexécuter la commande. Pour passer en mode Azure Resource Manager, utilisez la commande suivante :
   >
   > `azure config mode arm`
   >
   >
3. Récupérez la clé du compte de stockage. Remplacez **GROUPNAME** par le groupe de ressources de l'étape précédente. Remplacez **ACCOUNTNAME** par le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Renvoie la clé primaire du compte.

Vous pouvez également rechercher les informations de stockage à l’aide du portail Azure :

1. Ouvrez le [portail Azure](https://portal.azure.com/)et sélectionnez votre cluster HDInsight.
2. Dans la section **Base**, sélectionnez **Tous les paramètres**.
3. Dans **Paramètres**, sélectionnez **Clés de stockage Azure**.
4. Dans **Clés de stockage Azure**, sélectionnez un des comptes de stockage répertoriés. Vous obtiendrez des informations relatives au compte de stockage.
5. Sélectionnez l’icône de clé. Cette opération affichera les clés correspondant à ce compte de stockage.

### <a name="how-do-i-access-blob-storage"></a>Comment accéder au stockage d’objets blob ?
Outre la commande Hadoop depuis le cluster, vous pouvez accéder aux objets blob de plusieurs façons :

* [Interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli-install.md): commandes de l’interface de ligne de commande fonctionnant avec Azure. Après l’installation, utilisez la commande `azure storage` pour obtenir de l’aide sur l’utilisation du stockage ou la commande `azure blob` pour obtenir les commandes spécifiques aux objets blob.
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): script python pour travailler avec des objets blob dans Azure Storage.
* Un ensemble de kits de développement logiciel (SDK) :

  * [Java](https://github.com/Azure/azure-sdk-for-java)
  * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
  * [PHP](https://github.com/Azure/azure-sdk-for-php)
  * [Python](https://github.com/Azure/azure-sdk-for-python)
  * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
  * [.NET](https://github.com/Azure/azure-sdk-for-net)
* [API REST d’Azure Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## <a name="a-namescalingascaling-your-cluster"></a><a name="scaling"></a>Mise à l’échelle de votre cluster
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de données utilisés par un cluster exécuté dans Azure HDInsight sans avoir à supprimer puis recréer ce cluster.

Vous pouvez effectuer des opérations de mise à l’échelle pendant que d’autres travaux ou processus s’exécutent sur un cluster.

Les différents types de cluster sont affectés par la mise à l’échelle comme suit :

* **Hadoop** : quand vous réduisez le nombre de nœuds dans un cluster, les services du cluster sont redémarrés. Cette opération peut provoquer l’exécution de tâches ou en attente ou en échec à la fin de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l’opération terminée.
* **HBase** : les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

  1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez l’un des articles suivants :

     * [Utiliser le protocole SSH de HDInsight à partir de Linux, Unix et Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
     * [Utiliser le protocole SSH de HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
  2. Pour démarrer le shell HBAse, utilisez la commande suivante :

          hbase shell
  3. Une fois le shell HBase chargé, utilisez ce qui suit pour équilibrer manuellement les serveurs régionaux :

          balancer
* **Storm** : vous devez rééquilibrer les topologies Storm en cours d’exécution après l’exécution d’une opération de mise à l’échelle. Ainsi, la topologie peut réajuster les paramètres de parallélisme basés sur le nouveau nombre de nœuds du cluster. Pour rééquilibrer les topologies en cours d’exécution, utilisez l’une des options suivantes :

  * **SSH** : connectez-vous au serveur et utilisez la commande suivante pour rééquilibrer une topologie :

          storm rebalance TOPOLOGYNAME

      Vous pouvez également spécifier des paramètres pour remplacer les indicateurs de parallélisme initialement fournis par la topologie. Par exemple, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` permet de reconfigurer la topologie sur 5 processus de travail, 3 exécuteurs pour le composant blue-spout et 10 exécuteurs pour le composant yellow-bolt.
  * **Interface storm** : utilisez les étapes suivantes pour rééquilibrer une topologie avec l’interface utilisateur Storm.

    1. Ouvrez **https://CLUSTERNAME.azurehdinsight.net/stormui** dans un navigateur web, CLUSTERNAME désignant le nom de votre cluster Storm. Si vous y êtes invité, entrez le nom et le mot de passe de l’administrateur (admin) du cluster HDInsight spécifiés lors de la création du cluster.
    2. Sélectionnez la topologie que vous souhaitez rééquilibrer, puis le bouton **Rééquilibrer** . Saisissez le délai avant l’opération de rééquilibrage.

Pour obtenir des informations spécifiques sur la mise à l’échelle de votre cluster HDInsight, consultez :

* [Gérer les clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestion des clusters Hadoop dans HDInsight au moyen d’Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Comment installer Hue (ou un autre composant Hadoop) ?
HDInsight est un service géré, ce qui signifie que les nœuds dans un cluster peuvent être détruits et automatiquement reconfigurés par Azure en cas de problème. Pour cette raison, il est déconseillé d’installer manuellement des éléments directement sur les nœuds de cluster. Utilisez plutôt des [actions de script HDInsight](hdinsight-hadoop-customize-cluster.md) si vous devez installer les éléments suivants :

* Un service ou un site web comme Spark ou Hue.
* Un composant qui nécessite des modifications de configuration sur plusieurs nœuds du cluster. Par exemple, une variable d’environnement nécessaire, la création d’un répertoire de journalisation ou la création d’un fichier de configuration.

Les actions de script sont des scripts Bash exécutés pendant l’approvisionnement de cluster et peuvent être utilisées pour installer et configurer des composants supplémentaires sur le cluster. Des exemples de scripts sont fournis pour l’installation des composants suivants :

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Pour plus d’informations sur le développement de vos propres actions de script, consultez [Développement d’actions de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Fichiers Jar
Certaines technologies Hadoop sont fournies dans des fichiers jar autonomes, qui contiennent des fonctions utilisées dans le cadre d’une tâche MapReduce ou à partir de Pig ou Hive. Bien qu’elles puissent être installées à l’aide d’actions de script, elles ne nécessitent généralement aucune configuration et peuvent être chargées simplement vers le cluster après l’approvisionnement, puis utilisées directement. Si vous souhaitez que le composant survive à la réinitialisation du cluster, vous pouvez stocker le fichier jar dans WASB.

Par exemple, si vous souhaitez utiliser la dernière version de [DataFu](http://datafu.incubator.apache.org/), vous pouvez télécharger un fichier jar contenant le projet, puis le télécharger vers le cluster HDInsight. Suivez ensuite la documentation DataFu pour savoir comment l’utiliser à partir de Pig ou Hive.

> [!IMPORTANT]
> Certains composants qui sont des fichiers jar autonomes sont fournis avec HDInsight, mais ne se trouvent pas dans le chemin d’accès. Pour rechercher un composant spécifique sur votre cluster, vous pouvez utiliser la commande suivante :
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Cette commande retourne le chemin d’accès de tous les fichiers jar correspondants.
>
>

Si le cluster fournit déjà une version d’un composant sous la forme d’un fichier jar autonome, mais que vous souhaitez utiliser une autre version, vous pouvez charger une nouvelle version du composant vers le cluster et essayer de l’utiliser dans vos travaux.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).
>
>

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer la migration de HDInsight Windows vers HDInsight Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)



<!--HONumber=Nov16_HO3-->


