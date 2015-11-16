<properties
	pageTitle="Fonctionnalités de haute disponibilité de HDInsight sous Linux (Hadoop) | Microsoft Azure"
	description="Découvrez comment les clusters HDInsight Linux améliorent la fiabilité et la disponibilité en utilisant un nœud principal supplémentaire. Vous allez apprendre dans quelle mesure les services Hadoop tels qu’Ambari et Hive sont concernés, et comment se connecter à chaque nœud principal à l’aide de SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="larryfr"/>

#Disponibilité et fiabilité des clusters Hadoop dans HDInsight

Un deuxième nœud principal est utilisé par les clusters Hadoop Linux déployés par Azure HDInsight. Cela augmente la disponibilité et la fiabilité des services et des travaux Hadoop en cours d'exécution s'exécutant dans Azure.

> [AZURE.NOTE]Les étapes de ce document sont spécifiques aux clusters HDInsight sous Linux. Si vous utilisez un cluster Windows, consultez [Disponibilité et fiabilité des clusters Hadoop Windows dans HDInsight](hdinsight-high-availability.md) pour plus d'informations spécifiques Windows.

##Connaissance des nœuds principaux

Certaines implémentations de Hadoop sont dotées d'un seul nœud principal hébergeant les services et les composants qui gèrent la défaillance des nœuds de données (worker) sans heurts. Mais tout arrêt des services principaux en cours d'exécution sur le nœud principal peut provoquer l'arrêt du cluster.

Les clusters HDInsight fournissent un nœud principal secondaire, ce qui permet aux services et composants de continuer de s'exécuter sur le nœud secondaire en cas de panne sur le serveur principal.

> [AZURE.IMPORTANT]Les deux nœuds principaux sont actifs et s'exécutent simultanément sur le cluster. Certains services tels que HDFS ou YARN, ne sont plus « actifs » sur un nœud principal à toute donnée temps (et « En attente » sur le nœud de tête). D'autres services tels que HiveServer2 ou Hive MetaStore sont actifs sur les deux nœuds principaux simultanément.

Les nœuds [ZooKeeper](http://zookeeper.apache.org/) (ZKs) sont utilisés pour la sélection primaire de services principaux sur les nœuds principaux et garantissent que les services, les nœuds (worker) et les passerelles savent sur quel nœud principal, un service principal est actif.

## Accès aux nœuds principaux

En général, tous les accès au cluster via les passerelles publiques (Ambari web et API REST) ne sont pas affectées par les nœuds principaux. La demande est acheminée vers le nœud principal actif et traitée comme il convient.

Lors de l’accès au cluster à l’aide de SSH, la connexion via le port 22 (valeur par défaut pour SSH) entraîne la connexion au nœud principal 0. Une connexion via le port 23 entraîne une connexion au nœud principal 1.

### Noms de domaine pleinement qualifiés internes (FQDN)

Les nœuds présents dans un cluster HDInsight sont dotés d'une adresse IP interne et d'un nom de domaine complet uniquement accessibles depuis le cluster (par exemple, une session SSH sur le nœud principal ou un travail en cours d'exécution sur le cluster.) Lorsque vous accédez à des services sur le cluster à l'aide de l'adresse IP ou du nom de domaine complet interne, vous devez utiliser Ambari pour vérifier l'adresse IP ou le nom de domaine complet à utiliser.

Par exemple, le service Oozie peut s'exécuter uniquement sur un nœud principal et l'utilisation de la commande `oozie` à partir d'une session SSH requiert l'URL du service. Elle peut être extraite à partir d'Ambari à l'aide de la commande suivante :

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

elle renverra une valeur semblable à celle qui suit, qui contient l'URL interne à utiliser avec la commande `oozie` :

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

## Comment contrôler l'état d'un service

L'interface utilisateur web de Ambari ou l'API REST Ambari peuvent être utilisées pour vérifier l'état des services qui s'exécutent sur le nœud principal.

###API Ambari REST

Vous pouvez utiliser la commande suivante pour vérifier l'état d'un service via l'API REST Ambari :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Remplacez **Mot de passe** par l'utilisateur HTTP (admin), le mot de passe de compte

* Remplacez **CLUSTERNAME** par le nom de votre cluster.

* Remplacez **SERVICENAME** par le nom du service dont l'état doit être contrôlé

Par exemple, pour vérifier l'état du service **HDFS** dans un cluster nommé **moncluster**, avec un mot de passe **mot de passe**, vous devez utiliser la commande suivante :

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La réponse ressemblera à ce qui suit :

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

L’URL indique que le service est en cours d’exécution sur **nœud principal 0**.

L'URL indique que le service est en cours d'exécution ou **démarré**.

Si vous ne connaissez pas les services installés sur le cluster, vous pouvez utiliser les éléments suivants pour récupérer une liste :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Composants du service

Les services peuvent contenir des composants dont vous souhaitez vérifier l'état individuellement. Par exemple, HDFS contient le composant NameNode. Pour afficher des informations relatives à un composant, la commande serait :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Si vous ne connaissez pas les services fournis pour n service, vous pouvez utiliser les éléments suivants pour récupérer une liste :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Interface utilisateur web d'Ambari

L'interface utilisateur web d'Ambari est visible à https://CLUSTERNAME.azurehdinsight.net. Remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, saisissez les informations d'identification utilisateur de votre cluster. Le nom d'utilisateur HTTP par défaut est **admin** et le mot de passe est le mot de passe que vous avez saisi lors de la création du cluster.

Lorsque vous arrivez sur la page Ambari, les services installés apparaissent à gauche de la page.

![Services installés](./media/hdinsight-high-availability-linux/services.png)

Une série d'icônes s'affichent en regard d'un service pour indiquer son état. Des alertes liées à un service peuvent être affichées à l'aide du lien **Alertes** situé en haut de la page. Vous pouvez sélectionner chaque service pour afficher plus d'informations sur ce dernier.

La page de service fournit des informations sur l'état et la configuration de chaque service. Il ne fournit pas d'informations sur le nœud principal sur lequel le service s'exécute. Pour afficher ces informations, utilisez le lien **Hôtes** en haut de la page. Cela permettra d'afficher les hôtes au sein du cluster, notamment les nœuds principaux.

![liste des hôtes](./media/hdinsight-high-availability-linux/hosts.png)

La sélection du lien de l'un des nœuds principaux affichera les services et les composants qui s'exécutent sur ce nœud.

![État du composant](./media/hdinsight-high-availability-linux/nodeservices.png)

## Accès aux fichiers journaux sur le nœud principal secondaire

###SSH

Lorsque vous êtes connecté à un nœud principal via SSH, les fichiers journaux se trouvent sous **/var/log**. Par exemple, **/var/log/hadoop-yarn/yarn** contiennent les journaux correspondant à YARN.

Chaque nœud principal peut contenir des entrées de journal uniques. Vous devez donc vérifier les journaux correspondant aux deux.

###Ambari

> [AZURE.NOTE]L'accès aux fichiers journaux via Ambari nécessite un tunnel SSH, les sites web pour les services individuels ne sont pas exposés publiquement sur Internet. Pour des informations sur l'utilisation d'un tunnel SSH, consultez [Utilisation de SSH Tunneling pour accéder à l'interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d'autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md).

À partir de l'interface utilisateur Web d'Ambari, sélectionnez le service dont vous souhaitez afficher les journaux (par exemple, YARN) et utilisez **Liens rapides** pour sélectionner le nœud principal dont vous voulez afficher les journaux.

![Utilisation des liens rapides pour afficher les journaux](./media/hdinsight-high-availability-linux/viewlogs.png)

## Configuration de la taille du nœud principal ##

La taille du nœud principal ne peut être sélectionnée que lors de la création du cluster. La taille par défaut des nœuds principaux est **A3**, ce qui fournit 4 cœurs, 7 Go de mémoire et 285 Go de stockage local. Vous trouverez une liste des différentes tailles de machines virtuelles disponibles pour HDInsight, y compris le noyau, la mémoire et le stockage local pour chacune, sur la [page Tarification HDInsight](http://azure.microsoft.com/pricing/details/hdinsight/).

Lorsque vous créez un nouveau cluster, vous pouvez spécifier la taille des nœuds. Les éléments suivants fournissent des informations sur la façon de spécifier la taille à l'aide de la [version préliminaire d'Azure][preview-portal], [Azure PowerShell][azure-powershell] et l'[interface de ligne de commande Azure][azure-cli] :

* **Portail Azure en version préliminaire** : lorsque vous créez un cluster, vous obtenez la possibilité de définir la taille (niveau de prix) des nœuds principal et de données (worker) du cluster :

	![Image de l'Assistant de création de cluster avec sélection de taille de nœud](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Interface de ligne de commande Azure** : lorsque vous utilisez la commande `azure hdinsight cluster create`, vous pouvez définir la taille du nœud principal à l'aide du paramètre `--headNodeSize`.

* **Azure PowerShell** : lorsque vous utilisez l'`New-AzureRmHDInsightCluster`applet de commande, vous pouvez définir la taille du nœud principal en utilisant à l'aide du paramètre `-HeadNodeVMSize`.

##Étapes suivantes

Dans ce document, vous avez appris comment HDInsight Azure offre une haute disponibilité pour Hadoop. Utilisez les éléments qui suivent pour en savoir plus sur les éléments mentionnés dans ce document.

- [Référence REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Installation et configuration Azure CLI](../xplat-cli-install.md)

- [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md)

- [Gestion de HDInsight à l'aide d'Ambari](hdinsight-hadoop-manage-ambari.md)

- [Approvisionnement de clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=Nov15_HO2-->