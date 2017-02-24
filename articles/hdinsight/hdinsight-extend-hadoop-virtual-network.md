---
title: Extension de HDInsight avec Virtual Network | Microsoft Docs
description: "Apprenez à utiliser Azure Virtual Network pour connecter HDInsight à d&quot;autres ressources de cloud ou à des ressources de votre centre de données"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 45de2422e79215ecfbacf5bd15712eb780c49016
ms.openlocfilehash: c0a99dadc1d588942ade14267bd45eff09080315


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Extension des capacités de HDInsight à l’aide d’Azure Virtual Network
Azure Virtual Network vous permet d’étendre vos solutions Hadoop pour intégrer des ressources locales telles que SQL Server, combiner plusieurs types de clusters HDInsight ou créer des réseaux privés sécurisés entre des ressources dans le cloud.

## <a name="prerequisites"></a>Composants requis

* Interface de ligne de commande Azure 2.0 (version préliminaire) : pour plus d’informations, consultez la rubrique [Installer et configurer l’interface de ligne de commande Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell : consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) pour plus d’informations.

> [!NOTE]
> Les étapes décrites dans ce document nécessitent la dernière version d'Azure PowerShell et de l’interface de ligne de commande (CLI) Azure. Si vous utilisez une ancienne version, les commandes peuvent être différentes. Pour de meilleurs résultats, utilisez les liens précédents pour installer les dernières versions.

## <a name="a-idwhatisawhat-is-azure-virtual-network"></a><a id="whatis"></a>Présentation d’Azure Virtual Network

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).
  
    ![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    L’utilisation de Virtual Network pour associer les services Azure à Azure HDInsight active les scénarios suivants :
  
    * **Appel des services ou des travaux HDInsight** depuis le service Sites Web Azure ou des services exécutés dans des machines virtuelles Azure.
    * **Transfert direct des données** entre HDInsight et Base de données SQL Azure, SQL Server ou une autre solution de stockage de données exécutée sur une machine virtuelle.
    * **Combinaison de plusieurs serveurs HDInsight** en une seule solution. Il existe différents types de clusters HDInsight, correspondant aux différentes charges de travail ou technologies pour lesquelles ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster. L’utilisation d’un réseau virtuel permet à plusieurs clusters de communiquer directement entre eux.

* Connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou de point à site) en utilisant un réseau privé virtuel (ou VPN).
  
    Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).
  
    ![diagram of site-to-site configuration](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    Une configuration de point à site vous permet de connecter une ressource propre à Azure Virtual Network en utilisant un VPN logiciel.
  
    ![diagram of point-to-site configuration](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    L’utilisation d’un réseau virtuel pour lier le cloud et votre centre de données autorise des scénarios similaires dans la configuration cloud uniquement. Mais au lieu d’être limité à l’utilisation des ressources dans le cloud, vous pouvez également utiliser des ressources de votre centre de données.
  
    * **Transfert direct de données** entre HDInsight et votre centre de données. Par exemple, en utilisant Sqoop pour transférer des données vers ou depuis SQL Server ou en lisant des données générées par une application métier.
    * **Appel des services ou des travaux HDInsight** depuis une application métier. Par exemple, l’utilisation des API Java HBase pour stocker et récupérer des données depuis un cluster HBase HDInsight.

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Vous devez créer le réseau Azure Virtual Network avant d'approvisionner un cluster HDInsight. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel

> [!IMPORTANT]
> La création d’un cluster HDInsight sur un réseau virtuel nécessite des configurations spécifiques du réseau virtuel, qui sont décrites dans cette section.

### <a name="location-based-virtual-networks"></a>Réseaux virtuels basés sur l’emplacement.

Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.

### <a name="classic-or-v2-virtual-network"></a>Réseau virtuel classique ou v2

Les clusters Linux nécessitent un réseau virtuel Azure Resource Manager (les clusters Windows nécessitent un réseau virtuel classique). Si le type de réseau est incorrect, il ne sera pas utilisable lorsque vous créerez le cluster.

Si vous disposez de ressources sur un réseau virtuel qui n’est pas utilisable par le cluster que vous envisagez de créer, vous pouvez créer un nouveau réseau virtuel utilisable par le cluster et le connecter au réseau virtuel incompatible. Vous pouvez ensuite créer le cluster dans la version réseau dont il a besoin, et il sera en mesure d’accéder aux ressources de l’autre réseau puisque les deux seront reliés. Pour plus d’informations sur la connexion de réseaux virtuels classiques et nouveaux, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

### <a name="custom-dns"></a>DNS personnalisé

Lorsque vous créez un réseau virtuel, Azure fournit la résolution de noms par défaut pour les services Azure, tels que HDInsight, installés sur le réseau. Toutefois, vous devrez peut-être utiliser votre propre système DNS (Domain Name) dans des cas de résolution de noms de domaine inter-réseaux. Par exemple, lors de la communication entre des services situés dans deux réseaux virtuels joints. HDInsight prend en charge la résolution de noms Azure par défaut, ainsi que le système DNS personnalisé lorsqu’il est utilisé avec un réseau virtuel Azure.

Pour plus d’informations sur l’utilisation de votre propre serveur DNS avec un réseau virtuel Azure, consultez la section **Résolution de noms à l’aide de votre propre serveur DNS** du document [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

### <a name="secured-virtual-networks"></a>Réseaux virtuels sécurisés

Le service HDInsight est un service géré, et il nécessite un accès à Internet lors de l’approvisionnement et quand il est en cours d’exécution. Ceci permet à Azure de surveiller l’intégrité du cluster, de lancer le basculement des ressources du cluster, de modifier le nombre de nœuds dans le cluster à l’aide d’opérations de mise à l’échelle et d’effectuer d’autres tâches de gestion.

Si vous devez installer HDInsight dans un réseau virtuel sécurisé, vous devez autoriser l’accès entrant sur le port 443 pour les adresses IP suivantes, qui permettent à Azure de gérer le cluster HDInsight.

> [!IMPORTANT]
> Les adresses IP qui doivent être autorisés sont spécifiques à la région dans laquelle se trouvent le cluster HDInsight et le réseau virtuel. Utilisez les éléments suivants pour rechercher les adresses IP de votre région.

Région __Sud du Brésil__ :

* 191.235.84.104
* 191.235.87.113

Région __Est du Canada__ :

* 52.229.127.96
* 52.229.123.172

Région __Centre du Canada__ :

* 52.228.37.66
* 52.228.45.222

Région __États-Unis du Centre-Ouest__ :

* 52.161.23.15
* 52.161.10.167

Région __États-Unis de l’Ouest 2__ :

* 52.175.211.210
* 52.175.222.222

__Toutes les autres régions__ :

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Autoriser l’accès entrant à partir du port 443 pour ces adresses vous permettra d’installer correctement HDInsight sur un réseau virtuel sécurisé.

> [!IMPORTANT]
> HDInsight prend uniquement en charge la restriction du trafic entrant. Lorsque vous définissez les règles du groupe de sécurité réseau pour le sous-réseau contenant HDInsight, utilisez uniquement les règles de trafic entrant.

L’exemple suivant illustre comment créer un nouveau groupe de sécurité réseau qui autorise les adresses requises et qui applique le groupe de sécurité à un sous-réseau au sein de votre réseau virtuel. Les adresses utilisées dans cet exemple appartiennent à la liste __Toutes les autres régions__ ci-dessus. Si vous êtes dans l’une des régions répertoriées plus haut, comme les __États-Unis du Centre-Ouest__, modifiez le script pour utiliser les adresses IP pour votre région.

Ces étapes supposent que vous avez déjà créé un réseau virtuel et le sous-réseau dans lequel vous souhaitez installer HDInsight. Consultez [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!IMPORTANT]
> Notez la valeur `priority` utilisée dans ces exemples, car les règles sont testées sur le trafic réseau par ordre par priorité. Une fois qu’une règle correspond aux critères de test et est appliquée, aucune autre règle n’est testée.
> 
> Si vous avez des règles personnalisées qui bloquent largement le trafic entrant (comme une règle **Refuser tout**), vous devrez peut-être ajuster les valeurs de priorité dans ces exemples ou dans vos règles afin que les règles des exemples soient testées avant les règles qui bloquent l’accès. Dans le cas contraire, la règle **Refuser tout** sera testée d’abord et les règles de cet exemple ne seront jamais appliquées. Vous devez également faire attention à ne pas bloquer les règles par défaut pour un réseau virtuel Azure. Par exemple, vous ne devez pas créer de règle **Refuser tout** appliquée avant la règle **AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL** par défaut (qui a une priorité de 65000.)
> 
> Pour plus d’informations sur la façon dont les règles sont appliquées et les règles entrantes et sortantes par défaut, consultez [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

**Utilisation de Microsoft Azure PowerShell**

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
        -NetworkSecurityGroup $nsg

**Utilisation de l’interface de ligne de commande Azure (CLI)**

1. Utilisez la commande suivante pour créer un groupe de sécurité réseau nommé `hdisecure`. Remplacez **RESOURCEGROUPNAME** et **LOCATION** par le groupe de ressources qui contient le réseau virtuel Azure et l’emplacement (région) où le groupe a été créé.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    Une fois que le groupe a été créé, vous recevrez des informations sur le nouveau groupe.

2. Utilisez ce qui suit pour ajouter des règles au nouveau groupe de sécurité réseau qui autorisent les communications entrantes sur le port 443 à partir du service de gestion et de contrôle d’intégrité Azure HDInsight. Remplacez la valeur **RESOURCEGROUPNAME** par le nom du groupe de ressources qui contient le réseau virtuel Azure.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. Une fois que les règles ont été créées, utilisez ce qui suit pour récupérer l’identificateur unique pour ce groupe de sécurité réseau :

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    Cette commande renvoie une valeur semblable au texte suivant :

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilisez des guillemets autour des ID dans la commande si vous n’obtenez pas les résultats attendus.

4. Utilisez la commande suivante pour appliquer un groupe de sécurité réseau à un sous-réseau. Remplacez les valeurs __GUID__ et __RESOURCEGROUPNAME__ par celles renvoyées à l’étape précédente. Remplacez __VNETNAME__ et __SUBNETNAME__ par le nom de réseau virtuel et le nom de sous-réseau que vous souhaitez utiliser lors de la création d’un cluster HDInsight.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    Une fois cette commande terminée, vous pouvez installer HDInsight dans le réseau virtuel sécurisé sur le sous-réseau utilisé dans ces étapes.

> [!IMPORTANT]
> Les étapes ci-dessus donnent uniquement accès au service de gestion et de contrôle d’intégrité de HDInsight sur le cloud Azure. Cela vous permet d’installer un cluster HDInsight dans le sous-réseau. Cependant, l'accès au cluster HDInsight de l'extérieur du réseau virtuel est bloqué par défaut. Vous devez ajouter des règles supplémentaires pour le groupe de sécurité réseau si vous souhaitez activer l'accès de l'extérieur du réseau virtuel.
> 
> Par exemple, pour autoriser l’accès SSH à partir d’Internet, vous devrez ajouter une règle similaire à ce qui suit : 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Interface de ligne de commande Azure - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

Pour plus d’informations sur les groupes de sécurité réseau, voir [Présentation des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Pour plus d’informations sur le contrôle du routage dans un réseau virtuel Azure, voir [Itinéraires définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="a-idtasksatasks-and-information"></a><a id="tasks"></a>Tâches et informations

Cette section contient des informations relatives aux tâches courantes ainsi que des informations dont vous pouvez avoir besoin lorsque vous utilisez HDInsight avec un réseau virtuel.

### <a name="determine-the-fqdn"></a>Détermination du nom de domaine complet (FQDN)

Un nom de domaine complet spécifique est attribué au cluster HDInsight pour l’interface du réseau virtuel. Il s’agit d’une adresse que vous devez utiliser lors d’une connexion au cluster à partir d’autres ressources sur le réseau virtuel. Pour déterminer le nom de domaine complet, utilisez l’URL suivante pour consulter le service de gestion Ambari :

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [!NOTE]
> Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Surveillance des clusters Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Vous devez indiquer le nom de cluster ainsi qu'un service et un composant exécuté sur le cluster, tel que le gestionnaire de ressources YARN.

> [!NOTE]
> Les données sont renvoyées sous la forme d’un document JSON (JavaScript Object Notation) qui contient un grand nombre d’informations relatives au composant. Pour extraire le nom de domaine complet uniquement, vous devez utiliser un analyseur JSON pour récupérer la valeur `host_components[0].HostRoles.host_name` .

Par exemple, pour renvoyer le nom de domaine complet à partir d’un cluster HDInsight Hadoop, vous pouvez utiliser une des méthodes suivantes pour récupérer les données pour le gestionnaire de ressources YARN :

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) et [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### <a name="connecting-to-hbase"></a>Connexion à HBase

Pour vous connecter à HBase à distance à l’aide de l’API Java, vous devez définir les adresses quorum Zookeeper pour le cluster HBase et les indiquer dans votre application.

Pour obtenir l’adresse quorum Zookeeper, suivez l’une des méthodes suivantes pour interroger le service de gestion Ambari :

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
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

* [cURL](http://curl.haxx.se/) et [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Surveillance des clusters Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Une fois que vous avez l'information quorum, utilisez-la dans votre application cliente.

Par exemple, pour une application Java qui utilise l’API HBase, vous ajoutez un fichier **hbase-site.xml** au projet et indiquez l’information quorum dans le fichier comme suit :

```xml
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

### <a name="verify-network-connectivity"></a>Vérification de la connectivité du réseau

Certains services tel que SQL Server peuvent limiter les connexions réseau entrantes. Cela empêchera HDInsight de fonctionner correctement avec ces services.

Si vous rencontrez des problèmes pour accéder à un service à partir de HDInsight, consultez la documentation pour ce service pour vous assurer que vous avez activé l'accès au réseau. Vous pouvez également vérifier l’accès au réseau en créant une machine virtuelle Azure sur le même réseau virtuel et utiliser les utilitaires pour vérifier que la machine virtuelle peut se connecter au service via le réseau virtuel.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Étapes suivantes

L’exemple suivant montre comment utiliser HDInsight avec Azure Virtual Network :

* [Analyse des données de capteur avec Storm et HBase dans HDInsight](hdinsight-storm-sensor-data-analysis.md) : explique comment configurer un cluster Storm et HBase dans un réseau virtuel et comment écrire des données à distance depuis Storm dans HBase.
* [Approvisionnement des clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : donne des informations sur l’approvisionnement des clusters Hadoop, y compris les informations relatives à l’utilisation d’Azure Virtual Network.
* [Utilisation de Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop-mac-linux.md) : donne des informations sur l’utilisation de Sqoop pour transférer des données avec SQL Server via un réseau virtuel.

Pour en savoir plus sur les réseaux virtuels Azure, consultez la page [Vue d’ensemble d’Azure Virtual Network](../virtual-network/virtual-networks-overview.md).




<!--HONumber=Feb17_HO2-->


