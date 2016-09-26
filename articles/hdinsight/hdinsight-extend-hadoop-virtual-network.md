<properties
	pageTitle="Extension de HDInsight avec Virtual Network | Microsoft Azure"  
	description="Apprenez à utiliser Azure Virtual Network pour connecter HDInsight à d'autres ressources de cloud ou à des ressources de votre centre de données"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>


#Extension des capacités de HDInsight à l’aide d’Azure Virtual Network

Azure Virtual Network vous permet d’étendre vos solutions Hadoop à intégrer aux ressources locales telles que SQL Server ou à créer des réseaux privés sécurisés entre des ressources dans le cloud.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>Présentation d’Azure Virtual Network

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	L’utilisation de Virtual Network pour associer les services Azure à Azure HDInsight active les scénarios suivants :

	* **Appel des services ou des travaux HDInsight** depuis le service Sites Web Azure ou des services exécutés dans des machines virtuelles Azure.

	* **Transfert direct des données** entre HDInsight et Base de données SQL Azure, SQL Server ou une autre solution de stockage de données exécutée sur une machine virtuelle.

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

> [AZURE.NOTE] Vous devez créer le réseau Azure Virtual Network avant d'approvisionner un cluster HDInsight. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).

## Conditions requises pour le réseau virtuel

> [AZURE.IMPORTANT] La création d’un cluster HDInsight sur un réseau virtuel nécessite des configurations spécifiques du réseau virtuel, qui sont décrites dans cette section.

###Réseaux virtuels basés sur l’emplacement.

Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.

###Réseau virtuel classique ou v2

Les clusters Windows nécessitent un réseau virtuel classique, tandis que les clusters Linux nécessitent un réseau virtuel Azure Resource Manager. Si le type de réseau est incorrect, il ne sera pas utilisable lorsque vous créerez le cluster.

Si vous disposez de ressources sur un réseau virtuel qui n’est pas utilisable par le cluster que vous envisagez de créer, vous pouvez créer un nouveau réseau virtuel utilisable par le cluster et le connecter au réseau virtuel incompatible. Vous pouvez ensuite créer le cluster dans la version réseau dont il a besoin, et il sera en mesure d’accéder aux ressources de l’autre réseau puisque les deux seront reliés. Pour plus d’informations sur la connexion de réseaux virtuels classiques et nouveaux, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

###DNS personnalisé

Lorsque vous créez un réseau virtuel, Azure fournit la résolution de noms par défaut pour les services Azure, tels que HDInsight, installés sur le réseau. Toutefois, vous devrez peut-être utiliser votre propre système DNS (Domain Name) dans des cas de résolution de noms de domaine inter-réseaux. Par exemple, lors de la communication entre des services situés dans deux réseaux virtuels joints. HDInsight prend en charge la résolution de noms Azure par défaut, ainsi que le système DNS personnalisé lorsqu’il est utilisé avec un réseau virtuel Azure.

Pour plus d’informations sur l’utilisation de votre propre serveur DNS avec un réseau virtuel Azure, consultez la section __Résolution de noms à l’aide de votre propre serveur DNS__ du document [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

###Réseaux virtuels sécurisés

Le service HDInsight est un service géré, et il nécessite un accès à Internet lors de l’approvisionnement et quand il est en cours d’exécution. Ceci permet à Azure de surveiller l’intégrité du cluster, de lancer le basculement des ressources du cluster, de modifier le nombre de nœuds dans le cluster à l’aide d’opérations de mise à l’échelle et d’effectuer d’autres tâches de gestion.

Si vous devez installer HDInsight dans un réseau virtuel sécurisé, vous devez autoriser l’accès entrant sur le port 443 pour les adresses IP suivantes, qui permettent à Azure de gérer le cluster HDInsight.

* 168\.61.49.99
* 23\.99.5.239
* 168\.61.48.131
* 138\.91.141.162

Autoriser l’accès entrant à partir du port 443 pour ces adresses vous permettra d’installer correctement HDInsight sur un réseau virtuel sécurisé.

> [AZURE.IMPORTANT] HDInsight prend uniquement en charge la restriction du trafic entrant. Lorsque vous définissez les règles du groupe de sécurité réseau pour le sous-réseau contenant HDInsight, utilisez uniquement les règles de trafic entrant.

L’exemple suivant illustre comment créer un nouveau groupe de sécurité réseau qui autorise les adresses requises et qui applique le groupe de sécurité à un sous-réseau au sein de votre réseau virtuel. Ces étapes supposent que vous avez déjà créé un réseau virtuel et le sous-réseau dans lequel vous souhaitez installer HDInsight.

__Utilisation de Microsoft Azure PowerShell__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__Utilisation de l’interface de ligne de commande Azure (CLI)__

1. Utilisez la commande suivante pour créer un groupe de sécurité réseau nommé `hdisecure`. Remplacez __RESOURCEGROUPNAME__ et __LOCATION__ par le groupe de ressources qui contient le réseau virtuel Azure et l’emplacement (région) où le groupe a été créé.

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Une fois que le groupe a été créé, vous recevrez des informations sur le nouveau groupe. Recherchez une ligne semblable à la suivante et enregistrez les informations `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure`. Elles seront utilisées dans une étape ultérieure.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Utilisez ce qui suit pour ajouter des règles au nouveau groupe de sécurité réseau qui autorisent les communications entrantes sur le port 443 à partir du service de gestion et de contrôle d’intégrité Azure HDInsight. Remplacez la valeur __RESOURCEGROUPNAME__ par le nom du groupe de ressources qui contient le réseau virtuel Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Une fois que les règles ont été créées, utilisez les éléments suivants pour appliquer le nouveau groupe de sécurité réseau à un sous-réseau. Remplacez la valeur __RESOURCEGROUPNAME__ par le nom du groupe de ressources qui contient le réseau virtuel Azure. Remplacez __VNETNAME__ et __SUBNETNAME__ par le nom du réseau virtuel Azure et le sous-réseau que vous utiliserez lors de l’installation de HDInsight.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Une fois cette commande terminée, vous pouvez installer HDInsight dans le réseau virtuel sécurisé sur le sous-réseau utilisé dans ces étapes.

> [AZURE.IMPORTANT] Les étapes ci-dessus donnent uniquement accès au service de gestion et de contrôle d’intégrité de HDInsight sur le cloud Azure. Cela vous permet d’installer un cluster HDInsight dans le sous-réseau. Cependant, l'accès au cluster HDInsight de l'extérieur du réseau virtuel est bloqué par défaut. Vous devez ajouter des règles supplémentaires pour le groupe de sécurité réseau si vous souhaitez activer l'accès de l'extérieur du réseau virtuel.
>
> Par exemple, pour autoriser l’accès SSH à partir d’Internet, vous devrez ajouter une règle similaire à ce qui suit :
>
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Interface de ligne de commande Azure - ```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Pour plus d’informations sur les groupes de sécurité réseau, voir [Présentation des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Pour plus d’informations sur le contrôle du routage dans un réseau virtuel Azure, voir [Itinéraires définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

##<a id="tasks"></a>Tâches et informations

Cette section contient des informations relatives aux tâches courantes ainsi que des informations dont vous pouvez avoir besoin lorsque vous utilisez HDInsight avec un réseau virtuel.

###Détermination du nom de domaine complet (FQDN)

Un nom de domaine complet spécifique est attribué au cluster HDInsight pour l’interface du réseau virtuel. Il s’agit d’une adresse que vous devez utiliser lors d’une connexion au cluster à partir d’autres ressources sur le réseau virtuel. Pour déterminer le nom de domaine complet, utilisez l’URL suivante pour consulter le service de gestion Ambari :

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Surveillance des clusters Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Vous devez indiquer le nom de cluster ainsi qu'un service et un composant exécuté sur le cluster, tel que le gestionnaire de ressources YARN.

> [AZURE.NOTE] Les données sont renvoyées sous la forme d’un document JSON (JavaScript Object Notation) qui contient un grand nombre d’informations relatives au composant. Pour extraire le nom de domaine complet uniquement, vous devez utiliser un analyseur JSON pour récupérer la valeur `host_components[0].HostRoles.host_name`.

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

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Surveillance des clusters Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

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

<!---HONumber=AcomDC_0914_2016-->