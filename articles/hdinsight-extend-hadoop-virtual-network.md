<properties
   pageTitle="Extension de HDInsight avec Azure Virtual Network | VNet" metaKeywords="virtual network, vnet, azure, hdinsight" description="Apprenez à utiliser Azure Virtual Network pour connecter HDInsight à d'autres ressources de cloud ou à des ressources de votre centre de données"
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


#Extension des capacités de HDInsight à l'aide de Azure Virtual Network

Azure Virtual Network vous permet d'étendre vos solutions Hadoop à intégrer aux ressources locales telles que SQL Server ou à créer des réseaux privés sécurisés entre des ressources dans le cloud.

> [AZURE.NOTE] Actuellement, HDInsight ne prend pas en charge les réseaux virtuels Azure basés sur les affinités. Lorsque vous utilisez HDInsight, vous devez utiliser les réseaux virtuels basés sur l'emplacement.

##<a id="whatis"></a>Qu'est-ce que Azure Virtual Network ?

[Azure Virtual Network](/fr-fr/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	L'utilisation de Virtual Network pour associer les services Azure à HDInsight active les scénarios suivants :

	* **Appel des services ou des tâches HDInsight** depuis des sites Web Azure ou des services exécutés dans des machines virtuelles Azure.

	* **Transfert direct des données** entre HDInsight et la base de données SQL ou SQL Server ou une autre solution de stockage de données exécutée sur une machine virtuelle.

	* **Combinaison de multiples serveurs HDInsight** en une seule solution. Par exemple, il est possible d'utiliser un serveur Storm HDInsight pour consommer les données entrantes puis stocker les données traitées sur un serveur HBase HDInsight. Les données brutes peuvent également être stockées sur un serveur Hadoop HDInsight pour une analyse future avec MapReduce.

* Connectez vos ressources de cloud à votre réseau de centre de données local (de site à site ou d'un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	L'utilisation de Virtual Network pour lier le cloud et votre centre de données active des scénarios similaires à celui de la configuration pour le cloud uniquement. Mais au lieu de vous limiter à travailler avec des ressources dans le cloud, vous pouvez également travailler avec des ressources dans votre centre de données.

	* **Transfert direct de données** entre HDInsight et votre centre de données. Par exemple, en utilisant Sqoop pour transférer des données vers ou depuis SQL Server ou en lisant des données générées par une application cœur de métier.

	* **Appel des services ou des tâches HDInsight** depuis une application cœur de métier. Par exemple, l'utilisation des API Java HBase pour stocker et récupérer des données depuis un cluster HBase HDInsight.

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Vous devez créer l'Azure Virtual Network avant d'approvisionner un cluster HDInsight. Pour plus d'informations, consultez [Tâches de configuration du réseau virtuel](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight prend uniquement en charge les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec les réseaux virtuels basés sur des groupes d'affinités.
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

Pour plus d'informations sur l'approvisionnement d'un cluster HDInsight sur un réseau virtuel, consultez [Approvisionnement des clusters Hadoop dans HDInsight](/fr-fr/documentation/articles/hdinsight-provision-clusters/).

##<a id="tasks"></a>Tâches et informations

Cette section contient des informations relatives aux tâches courantes ainsi que des informations dont vous pouvez avoir besoin lorsque vous utilisez HDInsight avec un réseau virtuel.

###Déterminer le nom de domaine complet

Un nom de domaine complet spécifique sera attribué au cluster HDInsight pour l'interface du réseau virtuel. Il s'agit d'une adresse que vous devez utiliser lorsque vous vous connectez au cluster à partir d'autres ressources sur le réseau virtuel. Pour déterminer le nom de domaine complet, utilisez l'URL suivante pour consulter le service de gestion Ambari.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Pour plus d'informations sur l'utilisation de Ambari avec HDInsight, consultez [Surveillance des clusters Hadoop dans HDInsight à l'aide de l'API Ambari](/fr-fr/documentation/articles/hdinsight-monitor-use-ambari-api/).

Vous devez indiquer le nom de cluster ainsi qu'un service et un composant exécuté sur le cluster, tel que le gestionnaire de ressources YARN.

> [AZURE.NOTE] Les données sont renvoyées sous forme d'un document JSON qui contient un grand nombre d'informations relatives au composant. Afin d'extraire le nom de domaine complet uniquement, il vous faut utiliser un analyseur JSON pour récupérer la valeur  `host_components[0].HostRoles.host_name`.

Par exemple, pour renvoyer le nom de domaine complet à partir d'un cluster HDInsight Hadoop, vous pouvez utiliser une des méthodes suivantes pour récupérer les données pour le gestionnaire de ressources YARN.

* [Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/)

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

Pour vous connecter à HBase à distance à l'aide de l'API Java, vous devez définir les adresses quorum Zookeeper pour le cluster HBase et les indiquer dans votre application.

Pour obtenir l'adresse quorum Zookeeper, suivez l'une des méthodes suivantes pour interroger le service de gestion Ambari.

* [Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/)

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

> [AZURE.NOTE] Pour plus d'informations sur l'utilisation de Ambari avec HDInsight, consultez [Surveillance des clusters Hadoop dans HDInsight à l'aide de l'API Ambari](/fr-fr/documentation/articles/hdinsight-monitor-use-ambari-api/).

Une fois que vous avez l'information quorum, utilisez-la dans votre application cliente.

Par exemple, pour une application Java qui utilise l'API HBase, vous ajouterez un fichier **hbase-site.xml** au projet et indiquerez l'information quorum dans le fichier comme suit.

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

Certains services tel que SQL Server peuvent limiter les connexions réseau entrantes. Cela empêchera HDInsight de fonctionner correctement avec ces services.

Si vous rencontrez des problèmes pour accéder à un service à partir de HDInsight, consultez la documentation pour ce service pour vous assurer que vous avez activé l'accès au réseau. Vous pouvez également vérifier l'accès au réseau en créant une machine virtuelle Azure sur le même réseau virtuel et utiliser les utilitaires pour vérifier que la machine virtuelle peut se connecter au service via le réseau virtuel.

##<a id="nextsteps"></a>Étapes suivantes

L'exemple suivant montre comment utiliser HDInsight avec Azure Virtual Network.

* [Analyse des données de capteur avec Storm et HBase dans HDInsight](/fr-fr/documentation/articles/hdinsight-storm-sensor-data-analysis/) : explique comment configurer un cluster Storm et HBase dans un réseau virtuel et comment écrire des données à distance depuis Storm dans HBase.

* [Approvisionnement des clusters HBase dans Azure Virtual Network](/fr-fr/documentation/articles/hdinsight-hbase-provision-vnet/) : donne des informations sur l'approvisionnement d'un cluster HBase dans un réseau virtuel Azure

* [Approvisionnement des clusters Hadoop dans HDInsight](/fr-fr/documentation/articles/hdinsight-provision-clusters/) : donne des informations sur l'approvisionnement des clusters Hadoop, y compris les informations relatives à l'utilisation de Azure Virtual Network

* [Utilisation de Sqoop avec Hadoop dans HDInsight](/fr-fr/documentation/articles/hdinsight-use-sqoop/) : donne des informations sur l'utilisation de Sqoop pour transférer des données avec SQL Server via un réseau virtuel.

Pour en savoir plus sur les réseaux virtuels Azure, consultez [Vue d'ensemble de Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=45--> 
