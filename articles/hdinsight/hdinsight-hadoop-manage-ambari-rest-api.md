<properties
   pageTitle="Surveiller et gérer des clusters HDInsight à l'aide de l'API REST d'Apache Ambari | Microsoft Azure"
   description="Découvrez comment utiliser Ambari pour gérer et surveiller des clusters HDInsight Linux. Dans ce document, vous allez apprendre à utiliser l'API REST d'Ambari incluse avec les clusters HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2015"
   ms.author="larryfr"/>

#Gérer des clusters HDInsight à l'aide de l'API REST d'Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifie la gestion et la surveillance d'un cluster Hadoop en fournissant une interface utilisateur web et une API REST faciles à utiliser. Ambari est inclus dans les clusters HDInsight sous Linux et sert à surveiller le cluster et à apporter des modifications de configuration. Dans ce document, vous découvrirez les informations de base sur l'utilisation de l'API REST d'Ambari en effectuant les tâches courantes, par exemple trouver le nom de domaine complet des nœuds du cluster ou trouver le compte de stockage par défaut utilisé par le cluster.

> [AZURE.NOTE]Les informations mentionnées dans cet article s'appliquent uniquement aux clusters HDInsight sous Linux. Pour les clusters HDInsight sous Windows, seul un sous-ensemble de la fonctionnalité de surveillance est disponible via l'API REST d'Ambari. Consultez la page [Surveillance d'Hadoop Windows sur HDInsight à l'aide de l'API Ambari](hdinsight-monitor-use-ambari-api.md).

##Configuration requise

* [cURL](http://curl.haxx.se/) : cURL est un utilitaire multiplateforme utilisable pour travailler en ligne de commande avec les API REST. Dans ce document, il est utilisé pour communiquer avec l'API REST d'Ambari.
* [jq](https://stedolan.github.io/jq/) : jq est un utilitaire de ligne de commande multiplateforme conçu pour travailler avec des documents JSON. Dans ce document, il est utilisé pour analyser les documents JSON renvoyés par l'API REST d'Ambari.

##<a id="whatis"></a>Présentation d'Ambari

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifie la gestion de Hadoop en fournissant une interface utilisateur web conviviale qui peut être utilisée pour approvisionner, gérer et surveiller les clusters Hadoop. Les développeurs peuvent intégrer ces fonctionnalités dans leurs applications à l'aide des <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API REST Ambari</a>.

Ambari est fourni par défaut avec les clusters HDInsight Linux.

##API REST

L'URI de base pour l'API REST d'Ambari sur HDInsight est https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.IMPORTANT]La connexion à Ambari sur HDInsight requiert HTTPS. Vous devez également vous authentifier auprès d'Ambari en utilisant le nom du compte d'administrateur (la valeur par défaut est __admin__) et le mot de passe que vous avez fournis à la création du cluster.

Voici un exemple d'utilisation de cURL pour effectuer une requête GET sur l'API REST :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Si vous l'exécutez, en remplaçant __PASSWORD__ par le mot de passe de votre cluster et __CLUSTERNAME__ par le nom du cluster, vous recevez un document JSON qui commence par des informations de ce type :

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Dans la mesure où il s'agit de JSON, il est généralement plus facile d'utiliser un analyseur JSON pour récupérer les données. Par exemple, si vous souhaitez récupérer le nombre total d'alertes (contenues dans l'élément __« Host/host\_status/ALERT »__) vous pouvez utiliser les éléments suivants pour accéder directement à la valeur :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Ceci récupère le document JSON, puis redirige la sortie vers jq. `'.Clusters.health_report."Host/host_status/ALERT"'` indique l'élément du document JSON que vous souhaitez récupérer.

> [AZURE.NOTE]L'élément __Host/host\_status/ALERT__ est placé entre guillemets pour indiquer que « / » fait partie du nom de l'élément. Pour plus d'informations sur l'utilisation de jq, consultez le [Site web jq](https://stedolan.github.io/jq/).

##Exemple : Obtenir le nom de domaine complet de nœuds de cluster

Lorsque vous travaillez avec HDInsight, vous pouvez avoir besoin de connaître le nom de domaine complet (FQDN) d'un nœud de cluster. Vous pouvez facilement récupérer le nom de domaine complet des différents nœuds du cluster avec les commandes suivantes :

* __Nœuds principaux__ : `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds de travail__ : `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds Zookeeper__ : `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Notez que toutes suivent le même modèle : requête d'un composant dont nous savons qu'il s'exécute sur ces nœuds, puis extraction des éléments `host_name` contenant le nom de domaine complet de ces nœuds.

L'élément `host_components` du document renvoyé contient plusieurs éléments. `.host_components[]`, puis la spécification d'un chemin d'accès dans l'élément, s'exécutera en boucle sur chaque élément et extraira la valeur du chemin d'accès spécifique. Si vous ne voulez qu'une seule valeur, par exemple la première entrée de nom de domaine complet, vous pouvez renvoyer les éléments sous forme de collection, puis sélectionner une entrée spécifique :

    jq '[.host_components[].HostRoles.host_name][0]'

Renvoie le premier nom de domaine complet de la collection.

##Exemple : Récupérer le compte de stockage et le conteneur par défaut

Lorsque vous créez un cluster HDInsight, vous devez utiliser un compte Azure Storage et un conteneur d'objets blob comme stockage par défaut du cluster. Vous pouvez utiliser Ambari pour récupérer ces informations après avoir créé le cluster. Par exemple, si vous souhaitez écrire des données par programme, directement dans le conteneur.

L'instruction suivante récupérera l'URI WASB du stockage par défaut du cluster :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]Renvoie la première configuration appliquée au serveur (`service_config_version=1`), qui contiendra ces informations. Si vous récupérez une valeur qui a été modifiée après la création du cluster, vous devrez peut-être lister les versions de configuration et récupérer la plus récente.

Renvoie une valeur similaire à la suivante, où __CONTAINER__ est le conteneur par défaut et __ACCOUNTNAME__ est le nom de compte Azure Storage :

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Vous pouvez ensuite utiliser ces informations avec [Azure CLI](../xplat-cli-install.md) pour télécharger des données à partir du conteneur. Par exemple :

1. Récupérez le groupe de ressources du compte de stockage. Remplacez __ACCOUNTNAME__ par le nom du compte de stockage récupéré à partir d'Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Renvoie le nom du groupe de ressources du compte.
    
    > [AZURE.NOTE]Si cette commande ne renvoie aucun résultat, vous devrez peut-être modifier l'interface de ligne de commande Azure pour la faire passer en mode Azure Resource Manager et réexécuter la commande. Pour passer en mode Azure Resource Manager, utilisez la commande suivante :
    >
    > `azure config mode arm`
    
2. Récupérez la clé du compte de stockage. Remplacez __GROUPNAME__ par le groupe de ressources de l'étape précédente. Remplacez __ACCOUNTNAME__ par le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Renvoie la clé primaire du compte.
    
3. Utilisez la commande télécharger pour stocker un fichier dans le conteneur :

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Remplacez __ACCOUNTNAME__ par le nom du compte de stockage. Remplacez __ACCOUNTKEY__ par la clé récupérée précédemment. __FILEPATH__ est le chemin d'accès du fichier à télécharger, tandis que __BLOBPATH__ est le chemin d'accès dans le conteneur.

    Par exemple, si vous souhaitez que le fichier s'affiche dans HDInsight à l'adresse wasb://example/data/filename.txt, alors __BLOBPATH__ serait `example/data/filename.txt`.

##Étapes suivantes

Pour obtenir une référence complète de l'API REST, consultez la page [Référence V1 de l'API d'Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE]Certaines fonctionnalités d'Ambari, telles que l'ajout ou la suppression d'hôtes du cluster, ou l'ajout de nouveaux services, sont désactivées, puisqu'il est géré par le service cloud HDInsight.

<!---HONumber=AcomDC_0107_2016-->