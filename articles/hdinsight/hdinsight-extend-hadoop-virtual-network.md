<properties
	pageTitle="Extension de HDInsight avec Virtual Network | Microsoft Azure"  
	description="Apprenez à utiliser Azure Virtual Network pour connecter HDInsight à d'autres ressources de cloud ou à des ressources de votre centre de données"
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
   ms.date="01/13/2015"
   ms.author="larryfr"/>


#Extension des capacités de HDInsight à l’aide d’Azure Virtual Network

Azure Virtual Network vous permet d’étendre vos solutions Hadoop à intégrer aux ressources locales telles que SQL Server ou à créer des réseaux privés sécurisés entre des ressources dans le cloud.

> [AZURE.NOTE]HDInsight ne prend pas en charge les réseaux virtuels Azure basés sur les affinités. Lorsque vous utilisez HDInsight, vous devez utiliser les réseaux virtuels basés sur l’emplacement.


##<a id="whatis"></a>Présentation d’Azure Virtual Network

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	L’utilisation de Virtual Network pour associer les services Azure à Azure HDInsight active les scénarios suivants :

	* **Appel des services ou des travaux HDInsight** depuis le service Sites Web Azure ou des services exécutés dans des machines virtuelles Azure.

	* **Transfert direct des données** entre HDInsight et Azure SQL Database, SQL Server ou une autre solution de stockage de données exécutée sur une machine virtuelle.

	* **Combinaison de plusieurs serveurs HDInsight** en une seule solution. Par exemple, il est possible d’utiliser un serveur Storm HDInsight pour consommer les données entrantes puis stocker les données traitées sur un serveur HBase HDInsight. Les données brutes peuvent également être stockées sur un serveur Hadoop HDInsight pour une analyse future avec MapReduce.

* Connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou de point à site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource propre à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

	L’utilisation d’un réseau virtuel pour lier le cloud et votre centre de données autorise des scénarios similaires dans la configuration cloud uniquement. Mais au lieu d’être limité à l’utilisation des ressources dans le cloud, vous pouvez également utiliser des ressources de votre centre de données.

	* **Transfert direct de données** entre HDInsight et votre centre de données. Par exemple, en utilisant Sqoop pour transférer des données vers ou depuis SQL Server ou en lisant des données générées par une application métier.

	* **Appel des services ou des travaux HDInsight** depuis une application métier. Par exemple, l’utilisation des API Java HBase pour stocker et récupérer des données depuis un cluster HBase HDInsight.

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant d'approvisionner un cluster HDInsight. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](http://azure.microsoft.com/documentation/services/virtual-network/).

## Conditions requises pour le réseau virtuel

> [AZURE.IMPORTANT]La création d’un cluster HDInsight sur un réseau virtuel nécessite des configurations spécifiques du réseau virtuel, qui sont décrites dans cette section.

* Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités. 

* Il est vivement recommandé de créer un seul sous-réseau pour chaque cluster HDInsight.

* Les clusters Windows nécessitent un réseau virtuel v1 (classique), tandis que les clusters basés sur Linux nécessitent un réseau virtuel v2 (Azure Resource Manager). Si le type de réseau est incorrect, il ne sera pas utilisable lorsque vous créerez le cluster.

    Si vous disposez de ressources sur un réseau virtuel qui n’est pas utilisable par le cluster que vous envisagez de créer, vous pouvez créer un nouveau réseau virtuel utilisable par le cluster et le connecter au réseau virtuel incompatible. Vous pouvez ensuite créer le cluster dans la version réseau dont il a besoin, et il sera en mesure d’accéder aux ressources de l’autre réseau puisque les deux seront reliés. Pour plus d’informations sur la connexion de réseaux virtuels classiques et nouveaux, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../virtual-network/virtual-networks-arm-asm-s2s.md).

* HDInsight n’est pas pris en charge sur les réseaux virtuels Azure qui limitent explicitement l’accès vers/à partir d’Internet. Un exemple est l’utilisation de groupes de sécurité réseau ou d’ExpressRoute pour bloquer le trafic Internet vers des ressources du réseau virtuel. Le service HDInsight est un service géré, et il nécessite un accès à Internet lors de l’approvisionnement et quand il est en cours d’exécution, pour qu’Azure puisse surveiller l’intégrité du cluster, lancer le basculement des ressources du cluster et effectuer d’autres tâches de gestion automatisées.

    Si vous voulez utiliser HDInsight sur un réseau virtuel qui bloque le trafic Internet, procédez comme suit :

    1. Créez un sous-réseau dans le réseau virtuel. Par défaut, le nouveau sous-réseau sera en mesure de communiquer avec Internet. Cela permet à HDInsight d’être installé sur ce sous-réseau. Étant donné que le nouveau sous-réseau se trouve dans le même réseau virtuel que les sous-réseaux sécurisés, il peut également communiquer avec les ressources installées.
    
    2. Vous pouvez vérifier qu’il n’y a aucune table d’itinéraires ni aucun groupe de sécurité réseau spécifiques associés au sous-réseau en utilisant les instructions PowerShell suivantes. Remplacez __VIRTUALNETWORKNAME__ avec votre nom de réseau virtuel, remplacez __GROUPNAME__ avec le nom du groupe de ressources qui contient le réseau virtuel, puis remplacez __SUBNET__ avec le nom du sous-réseau.
        
            $vnet = Get-AzureRmVirtualNetwork -Name VIRTUALNETWORKNAME -ResourceGroupName GROUPNAME
            $vnet.Subnets | Where-Object Name -eq "SUBNET"
            
        Dans les résultats, notez que __NetworkSecurityGroup__ et __RouteTable__ sont tous deux `null`.
    
    2. Créez le cluster HDInsight. Lorsque vous configurez les paramètres de réseau virtuel pour le cluster, sélectionnez le sous-réseau créé à l’étape 1.

    > [AZURE.NOTE]Les étapes ci-dessus supposent que vous n’avez pas restreint les communications avec les adresses IP _au sein de la plage d’adresses IP de réseau virtuel_. Dans le cas contraire, vous devrez peut-être modifier ces restrictions pour permettre la communication avec le nouveau sous-réseau.

    Pour plus d’informations sur les groupes de sécurité réseau, voir [Présentation des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Pour plus d’informations sur le contrôle du routage dans un réseau virtuel Azure, voir [Itinéraires définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

Pour plus d’informations sur l’approvisionnement d’un cluster HDInsight sur un réseau virtuel, consultez la page [Approvisionnement des clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md).

##<a id="tasks"></a>Tâches et informations

Cette section contient des informations relatives aux tâches courantes ainsi que des informations dont vous pouvez avoir besoin lorsque vous utilisez HDInsight avec un réseau virtuel.

###Détermination du nom de domaine complet (FQDN)

Un nom de domaine complet spécifique est attribué au cluster HDInsight pour l’interface du réseau virtuel. Il s’agit d’une adresse que vous devez utiliser lors d’une connexion au cluster à partir d’autres ressources sur le réseau virtuel. Pour déterminer le nom de domaine complet, utilisez l’URL suivante pour consulter le service de gestion Ambari :

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE]Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Surveillance des clusters Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Vous devez indiquer le nom de cluster ainsi qu'un service et un composant exécuté sur le cluster, tel que le gestionnaire de ressources YARN.

> [AZURE.NOTE]Les données sont renvoyées sous la forme d’un document JSON (JavaScript Object Notation) qui contient un grand nombre d’informations relatives au composant. Pour extraire le nom de domaine complet uniquement, vous devez utiliser un analyseur JSON pour récupérer la valeur `host_components[0].HostRoles.host_name`.

Par exemple, pour renvoyer le nom de domaine complet à partir d’un cluster HDInsight Hadoop, vous pouvez utiliser une des méthodes suivantes pour récupérer les données pour le gestionnaire de ressources YARN :

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [Curl](http://curl.haxx.se/) et [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Connexion à HBase

Pour vous connecter à HBase à distance à l’aide de l’API Java, vous devez définir les adresses quorum Zookeeper pour le cluster HBase et les indiquer dans votre application.

Pour obtenir l’adresse quorum Zookeeper, suivez l’une des méthodes suivantes pour interroger le service de gestion Ambari :

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [Curl](http://curl.haxx.se/) et [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE]Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Surveillance des clusters Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Une fois que vous avez l'information quorum, utilisez-la dans votre application cliente.

Par exemple, pour une application Java qui utilise l’API HBase, vous ajoutez un fichier **hbase-site.xml** au projet et indiquez l’information quorum dans le fichier comme suit :

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###Vérification de la connectivité du réseau

Certains services tel que SQL Server peuvent limiter les connexions réseau entrantes. Cela empêchera HDInsight de fonctionner correctement avec ces services.

Si vous rencontrez des problèmes pour accéder à un service à partir de HDInsight, consultez la documentation pour ce service pour vous assurer que vous avez activé l'accès au réseau. Vous pouvez également vérifier l’accès au réseau en créant une machine virtuelle Azure sur le même réseau virtuel et utiliser les utilitaires pour vérifier que la machine virtuelle peut se connecter au service via le réseau virtuel.

##<a id="nextsteps"></a>Étapes suivantes

L’exemple suivant montre comment utiliser HDInsight avec Azure Virtual Network :

* [Analyse des données de capteur avec Storm et HBase dans HDInsight](hdinsight-storm-sensor-data-analysis.md) : explique comment configurer un cluster Storm et HBase dans un réseau virtuel et comment écrire des données à distance depuis Storm dans HBase.

* [Approvisionnement des clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : donne des informations sur l’approvisionnement des clusters Hadoop, y compris les informations relatives à l’utilisation d’Azure Virtual Network.

* [Utilisation de Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop-mac-linux.md) : donne des informations sur l’utilisation de Sqoop pour transférer des données avec SQL Server via un réseau virtuel.

Pour en savoir plus sur les réseaux virtuels Azure, consultez la page [Vue d’ensemble d’Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

<!---HONumber=AcomDC_0121_2016-->