---
title: "Conseils sur l’utilisation de Hadoop sur un cluster HDInsight basé sur Linux - Azure | Microsoft Docs"
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
ms.date: 02/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 1d2d3d9d6c8dee02f2eb96ba20894e1d52541102
ms.openlocfilehash: 584af73f3f2d428f7551de0b12b498b1a118e5dc


---
# <a name="information-about-using-hdinsight-on-linux"></a>Informations sur l’utilisation de HDInsight sous Linux

Les clusters Azure HDInsight fournissent Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure. En principe, il fonctionne comme tout autre Hadoop sur une installation Linux. Ce document présente des différences spécifiques que vous devriez connaître.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis

La plupart des étapes décrites dans ce document utilisent les utilitaires ci-après, que vous pouvez avoir besoin d’installer sur votre système.

* [cURL](https://curl.haxx.se/) : permet de communiquer avec les services Web.
* [jq](https://stedolan.github.io/jq/) : permet d’analyser des documents JSON.
* [Interface de ligne de commande Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (version préliminaire) - permet de gérer à distance les services Azure

## <a name="users"></a>Users

Sauf s’il est [joint à un domaine](hdinsight-domain-joined-introduction.md), HDInsight doit être considéré comme un système **mono-utilisateur**. Un compte d’utilisateur SSH unique est créé avec le cluster, avec les autorisations de niveau administrateur. Des comptes SSH supplémentaires peuvent être créés, mais ils auront également l’accès administrateur au cluster.

HDInsight joint à un domaine prend en charge la présence de plusieurs utilisateurs et des paramètres d’autorisation et de rôles plus précis. Pour plus d’informations, consultez la section [Gestion des clusters HDInsight joints à un domaine](hdinsight-domain-joined-manage.md).

## <a name="domain-names"></a>Noms de domaine

Le nom de domaine complet (FQDN) à utiliser pour se connecter au cluster depuis Internet est **&lt;clustername>.azurehdinsight.net** ou (pour SSH exclusivement) **&lt;clustername-ssh>.azurehdinsight.net**.

En interne, chaque nœud du cluster porte un nom qui est attribué pendant la configuration du cluster. Pour rechercher les noms de cluster, consultez la page **Hôtes** sur l’interface Web Ambari. Vous pouvez également utiliser les éléments suivants pour renvoyer la liste des hôtes à partir de l’API REST Ambari :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Remplacez **PASSWORD** par le mot de passe du compte d’administrateur et **CLUSTERNAME** par le nom de votre cluster. Ainsi sera retourné un document JSON qui contient la liste des hôtes du cluster. Ensuite, jq extrait la valeur de l’élément `host_name` pour chaque hôte du cluster.

Si vous avez besoin de trouver le nom du nœud d’un service spécifique, vous pouvez interroger Ambari pour obtenir ce composant. Par exemple, pour trouver les hôtes du nœud de nom HDFS, utilisez ce qui suit.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Cette demande renvoie un document JSON qui décrit le service. Ensuite, jq extrait uniquement la valeur `host_name` des hôtes.

## <a name="remote-access-to-services"></a>Accès à distance aux services

* **Ambari (Web)** : https://&lt;clustername>.azurehdinsight.net

    Authentifiez-vous à l’aide du nom d’utilisateur et du mot de passe de l’administrateur du cluster, puis connectez-vous à Ambari. Vous devez vous authentifier avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.

    L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

    > [!IMPORTANT]
    > Bien qu'Ambari pour votre cluster soit accessible directement sur Internet, certaines fonctionnalités dépendent de l'accès aux nœuds via le nom de domaine interne utilisé par le cluster. Puisqu’il s’agit d’un nom de domaine interne, et non public, des erreurs vous indiquant que le serveur est introuvable sont susceptibles d’apparaître lorsque vous essayerez d’accéder à certaines fonctionnalités sur Internet.
    >
    > Pour bénéficier de toutes les fonctionnalités de l’interface utilisateur Web Ambari, vous devez utiliser un tunnel SSH pour assurer l’acheminement proxy vers le nœud principal cluster. Consultez [Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)** : https://&lt;clustername>.azurehdinsight.net/ambari

    > [!NOTE]
    > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
    >
    > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

    > [!NOTE]
    > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
    >
    > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net sur le port 22 ou 23. Le port 22 est utilisé pour se connecter au nœud principal primaire tandis que le port 23 est utilisé pour se connecter au nœud principal secondaire. Pour plus d’informations sur les nœuds principaux, consultez [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > Vous pouvez accéder aux nœuds principaux du cluster uniquement via SSH depuis une machine cliente. Une fois connecté, vous pouvez ensuite accéder aux nœuds Worker à l’aide de SSH depuis un nœud principal.

## <a name="file-locations"></a>Emplacements des fichiers

Les fichiers relatifs à Hadoop se trouvent sur les nœuds du cluster dans `/usr/hdp`. Le répertoire contient les sous-répertoires suivants :

* **2.2.4.9-1** : ce répertoire est nommé en fonction de la version de la plateforme de données Hortonworks utilisée par HDInsight. Le numéro de votre cluster peut être différent de celui qui figure ici.
* **En cours** : ce répertoire contient des liens vers des répertoires contenus dans le dossier **2.2.4.9-1** et existe afin que vous n’ayez pas à saisir de numéro de version (qui peut changer) à chaque fois que vous souhaitez accéder à un fichier.

Vous trouverez des exemples de données et de fichiers JAR sur le système HDSF (Hadoop HDFS Distributed File System) ou le stockage Blob Azure dans `/example` et `/HdiSamples`.

## <a name="hdfs-blob-storage-and-data-lake-store"></a>HDFS, stockage Blob et Data Lake Store

Dans la plupart des distributions Hadoop, le système HDFS est sauvegardé par un stockage local sur les machines dans le cluster. Bien que cela soit efficace, cette méthode peut être coûteuse pour une solution basée sur le cloud où vous êtes facturé à l’heure ou à la minute pour les ressources de calcul.

HDInsight utilise le stockage Blob Azure ou Azure Data Lake Store comme magasin par défaut. Cela permet de bénéficier des avantages suivants :

* Un stockage à long terme peu coûteux
* Un accès à partir de services externes tels que les sites Web, les utilitaires de téléchargement de fichier, les kits de développement logiciel (SDK) en différentes langues et les navigateurs Web

> [!IMPORTANT]
> Le stockage Blob peut contenir jusqu’à 4,75 To, même si les objets blob individuels (ou les fichiers du point de vue de HDInsight) ne peuvent pas dépasser 195 Go. Azure Data Lake Store peut évoluer de manière dynamique pour contenir des milliers de milliards de fichiers, avec des fichiers individuels d’une taille supérieure à un pétaoctet.
>
> Pour plus d’informations, voir [Understanding blobs](https://docs.microsoft.com/rest/api/storageservices/fileservices/understanding-block-blobs--append-blobs--and-page-blobs) (Présentation des objets blob) et [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Lorsque vous utilisez le stockage Azure ou Data Lake Store, vous n’avez normalement aucune opération particulière à effectuer à partir de HDInsight pour accéder aux données. Par exemple, la commande suivante listera les fichiers dans le dossier `/example/data`, qu’il soit stocké sur le stockage Blob Azure ou sur Data Lake Store :

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI et schéma

Certaines commandes peuvent vous imposer de spécifier le schéma dans l’URI lorsque vous accédez à un fichier. Par exemple, le composant Storm-HDFS requiert la spécification du schéma. Lorsque vous n’utilisez pas le stockage par défaut (stockage ajouté en tant que stockage « supplémentaire » au cluster), vous devez toujours indiquer le schéma dans l’URI.

Lorsque vous utilisez le __stockage Blob__, le schéma peut être l’un des suivants :

* `wasb:///` : accès au stockage par défaut via une communication non chiffrée.

* `wasbs:///` : accès au stockage par défaut via une communication chiffrée.

* `wasbs://<container-name>@<account-name>.blob.core.windows.net/` : utilisé pour communiquer avec un compte de stockage autre que celui par défaut. Par exemple, si vous avez un compte de stockage supplémentaire ou si vous accédez à des données stockées dans un compte de stockage accessible au public.

Lorsque vous utilisez __Data Lake Store__, le schéma peut être l’un des suivants :

* `adl:///` : accès au Data Lake Store par défaut pour le cluster.

* `adl://<storage-name>.azuredatalakestore.net/` : utilisé pour communiquer avec un Data Lake Store autre que celui par défaut ou pour accéder à des données en dehors du répertoire racine de votre cluster HDInsight.

> [!IMPORTANT]
> Lorsque vous utilisez Data Lake Store en tant que magasin par défaut pour HDInsight, vous devez spécifier un chemin d’accès dans le magasin à utiliser comme racine de stockage HDInsight. Le chemin d’accès par défaut est : `/clusters/<cluster-name>/`.
>
> Lorsque vous utilisez `/` ou `adl:///` pour accéder aux données, vous pouvez uniquement accéder à des données stockées à la racine (par exemple, `/clusters/<cluster-name>/`) du cluster. Pour accéder à des données n’importe où dans le magasin, utilisez le format `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Quel stockage le cluster utilise-t-il ?

Vous pouvez utiliser Ambari pour récupérer la configuration de stockage par défaut du cluster. Utilisez la commande suivante pour récupérer les informations de configuration HDFS à l’aide de curl, puis filtrez ces informations avec [jq](https://stedolan.github.io/jq/):

```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Cette commande renvoie la première configuration appliquée au serveur (`service_config_version=1`), qui contient ces informations. Si vous récupérez une valeur qui a été modifiée après la création du cluster, vous devrez peut-être lister les versions de configuration et récupérer la plus récente.

Cette commande renvoie une valeur semblable à ce qui suit :

* `wasbs://<container-name>@<account-name>.blob.core.windows.net` si vous utilisez un compte de stockage Azure.

    Le nom du compte est le nom du compte de stockage Azure, tandis que le nom du conteneur est le conteneur d’objets blob correspondant à la racine de stockage du cluster.

* `adl://home` si vous utilisez Azure Data Lake Store. Pour obtenir le nom du magasin Data Lake Store, utilisez l’appel REST suivant :

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Cette commande renvoie le nom d’hôte suivant : `<data-lake-store-account-name>.azuredatalakestore.net`.

    Pour obtenir le répertoire du magasin qui correspond à la racine de HDInsight, utilisez l’appel REST suivant :

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Cette commande renvoie un chemin d’accès semblable au suivant : `/clusters/<hdinsight-cluster-name>/`.

Vous pouvez également rechercher les informations de stockage à l’aide du portail Azure en suivant les étapes ci-dessous :

1. Ouvrez le [portail Azure](https://portal.azure.com/)et sélectionnez votre cluster HDInsight.

2. Dans la section **Propriétés**, sélectionnez **Comptes de stockage**. Les informations de stockage du cluster s’affichent.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Comment accéder à des fichiers situés en dehors de HDInsight ?

Il existe plusieurs façons d’accéder à des données à l’extérieur du cluster HDInsight. Voici quelques liens vers des utilitaires et Kits de développement logiciel (SDK) qui peuvent être utilisés pour exploiter vos données :

Si vous utilisez le __stockage Azure__, consultez les liens suivants pour découvrir les méthodes permettant d’accéder à vos données :

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): commandes de l’interface de ligne de commande fonctionnant avec Azure. Après l’installation, utilisez la commande `az storage` pour obtenir de l’aide sur l’utilisation du stockage ou la commande `az storage blob` pour obtenir les commandes spécifiques aux objets blob.
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): script python pour travailler avec des objets blob dans Azure Storage.
* Divers Kits de développement logiciel (SDK) :

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST d’Azure Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Si vous utilisez __Azure Data Lake Store__, consultez les liens suivants pour découvrir les méthodes permettant d’accéder à vos données :

* [Navigateur Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interface de ligne de commande Azure](../data-lake-store/data-lake-store-get-started-cli.md)
* [API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="a-namescalingascaling-your-cluster"></a><a name="scaling"></a>Mise à l’échelle de votre cluster

La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de données utilisés par un cluster sans avoir à supprimer puis recréer ce cluster. Vous pouvez effectuer des opérations de mise à l’échelle pendant que d’autres travaux ou processus s’exécutent sur un cluster.

Les différents types de cluster sont affectés par la mise à l’échelle comme suit :

* **Hadoop** : quand vous réduisez le nombre de nœuds dans un cluster, les services du cluster sont redémarrés. Cette opération peut provoquer l’exécution de tâches ou en attente ou en échec à la fin de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l’opération terminée.
* **HBase** : les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

    1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez l’un des articles suivants :

        * [Utiliser le protocole SSH de HDInsight à partir de Linux, Unix et Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Utiliser le protocole SSH (PuTTY) avec HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    2. Pour démarrer le shell HBAse, utilisez la commande suivante :

            hbase shell
    
    3. Une fois le shell HBase chargé, utilisez ce qui suit pour équilibrer manuellement les serveurs régionaux :

            balancer

* **Storm** : vous devez rééquilibrer les topologies Storm en cours d’exécution après l’exécution d’une opération de mise à l’échelle. Ainsi, la topologie peut réajuster les paramètres de parallélisme basés sur le nouveau nombre de nœuds du cluster. Pour rééquilibrer les topologies en cours d’exécution, utilisez l’une des options suivantes :

    * **SSH** : connectez-vous au serveur et utilisez la commande suivante pour rééquilibrer une topologie :

            storm rebalance TOPOLOGYNAME

        Vous pouvez également spécifier des paramètres pour remplacer les indicateurs de parallélisme initialement fournis par la topologie. Par exemple, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` permet de reconfigurer la topologie sur 5 processus Worker, 3 exécuteurs pour le composant blue-spout et 10 exécuteurs pour le composant yellow-bolt.

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

Certaines technologies Hadoop sont fournies dans des fichiers jar autonomes, qui contiennent des fonctions utilisées dans le cadre d’un travail MapReduce ou à partir de Pig ou Hive. Bien qu’elles puissent être installées à l’aide d’actions de script, elles ne nécessitent généralement aucune configuration et peuvent être chargées vers le cluster après l’approvisionnement, puis utilisées directement. Si vous souhaitez que le composant survive à la réinitialisation du cluster, vous pouvez stocker le fichier jar dans le stockage par défaut de votre cluster (WASB ou ADL).

Par exemple, si vous souhaitez utiliser la dernière version de [DataFu](http://datafu.incubator.apache.org/), vous pouvez télécharger un fichier jar contenant le projet, puis le télécharger vers le cluster HDInsight. Suivez ensuite la documentation DataFu pour savoir comment l’utiliser à partir de Pig ou Hive.

> [!IMPORTANT]
> Certains composants qui sont des fichiers jar autonomes sont fournis avec HDInsight, mais ne se trouvent pas dans le chemin d’accès. Pour rechercher un composant spécifique sur votre cluster, vous pouvez utiliser la commande suivante :
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Cette commande renvoie le chemin d’accès de tous les fichiers jar correspondants.

Si le cluster fournit déjà une version d’un composant sous la forme d’un fichier jar autonome, mais que vous souhaitez utiliser une autre version, vous pouvez charger une nouvelle version du composant vers le cluster et essayer de l’utiliser dans vos travaux.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aide à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer la migration de HDInsight Windows vers HDInsight Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)



<!--HONumber=Feb17_HO1-->


