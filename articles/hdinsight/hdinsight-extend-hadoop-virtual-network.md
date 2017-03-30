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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: afd43fb536278d6aa76acaf7c9d18714bc5acd2b
ms.lasthandoff: 03/15/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Extension des capacités de HDInsight à l’aide d’Azure Virtual Network
Réseau virtuel Azure vous permet d’étendre vos solutions Hadoop pour incorporer des ressources locales comme SQL Server. Il vous permet également de combiner plusieurs types de clusters HDInsight, ou de créer des réseaux privés sécurisés entre des ressources dans le cloud.

## <a name="prerequisites"></a>Composants requis

* Azure CLI 2.0 : Pour plus d’informations, consultez [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell : Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Les étapes décrites dans ce document nécessitent la dernière version d'Azure PowerShell et de l’interface de ligne de commande (CLI) Azure. Si vous utilisez une ancienne version, les commandes peuvent être différentes. Pour de meilleurs résultats, utilisez les liens précédents pour installer les dernières versions.

## <a id="whatis"></a>Présentation d’Azure Virtual Network

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).
  
    ![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    L’utilisation de Virtual Network pour associer les services Azure à Azure HDInsight active les scénarios suivants :
  
    * **Appel des services ou des travaux HDInsight** depuis le service Sites Web Azure ou des services exécutés dans des machines virtuelles Azure.
    * **Transfert direct des données** entre HDInsight et Base de données SQL Azure, SQL Server ou une autre solution de stockage de données exécutée sur une machine virtuelle.
    * **Combinaison de plusieurs serveurs HDInsight** en une seule solution. Il existe différents types de clusters HDInsight, correspondant à la charge de travail ou à la technologie pour laquelle ils sont optimisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster. L’utilisation d’un réseau virtuel permet à plusieurs clusters de communiquer directement entre eux.

* Connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou de point à site) en utilisant un réseau privé virtuel (ou VPN).
  
    Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données au réseau virtuel Azure. Vous pouvez établir la connexion à l’aide d’un appareil VPN matériel ou du service Routage et accès distant.
  
    ![diagramme d’une configuration de site à site](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    Une configuration de point à site vous permet de connecter une ressource propre à Réseau virtuel Azure en utilisant un VPN logiciel.
  
    ![diagramme d’une configuration de point à site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    L’utilisation d’un réseau virtuel pour lier le cloud et votre centre de données autorise des scénarios similaires dans la configuration cloud uniquement. Mais au lieu d’être limité à l’utilisation des ressources dans le cloud, vous pouvez également utiliser des ressources de votre centre de données.
  
    * **Transfert direct de données** entre HDInsight et votre centre de données. Par exemple, en utilisant Sqoop pour transférer des données vers ou depuis SQL Server ou en lisant des données générées par une application métier.
    * **Appel des services ou des travaux HDInsight** depuis une application métier. Par exemple, l’utilisation des API Java HBase pour stocker et récupérer des données depuis un cluster HBase HDInsight.

Pour plus d'informations sur les fonctions, les avantages et les capacités du service Réseau virtuel Azure, consultez [Vue d’ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Créez le réseau virtuel Azure avant d’approvisionner un cluster HDInsight, puis spécifiez le réseau lors de la création du cluster. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel

> [!IMPORTANT]
> La création d’un cluster HDInsight sur un réseau virtuel nécessite des configurations spécifiques du réseau virtuel, qui sont décrites dans cette section.

### <a name="location-based-virtual-networks"></a>Réseaux virtuels basés sur l’emplacement.

Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.

### <a name="classic-or-v2-virtual-network"></a>Réseau virtuel classique ou v2

Les clusters Linux nécessitent un réseau virtuel Azure Resource Manager (les clusters Windows nécessitent un réseau virtuel classique). Si le type de réseau est incorrect, il ne peut pas être sélectionné quand vous créez le cluster.

Vous pouvez joindre les différents types de réseau, ce qui vous permet d’accéder aux ressources sur un réseau virtuel incompatible. Créez le cluster dans la version du réseau qui lui est nécessaire : il peut alors accéder aux ressources de l’autre réseau, car les deux réseaux sont joints. Pour plus d’informations sur la connexion de réseaux virtuels classiques et Resource Manager, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

### <a name="custom-dns"></a>DNS personnalisé

Lorsque vous créez un réseau virtuel, Azure fournit la résolution de noms par défaut pour les services Azure, tels que HDInsight, installés sur le réseau. Toutefois, vous devrez peut-être utiliser votre propre système DNS (Domain Name) dans des cas de résolution de noms de domaine inter-réseaux. Par exemple, lors de la communication entre des services situés dans deux réseaux virtuels joints. HDInsight prend en charge la résolution de noms Azure par défaut, ainsi qu’un serveur DNS personnalisé quand il est utilisé avec Réseau virtuel Azure.

Pour plus d’informations sur l’utilisation d’un serveur DNS personnalisé, consultez le document [Résolution de noms pour les machines virtuelles et les instances de rôles](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

### <a name="secured-virtual-networks"></a>Réseaux virtuels sécurisés

Le service HDInsight est un service géré, et il nécessite un accès à Internet lors de l’approvisionnement et quand il est en cours d’exécution. La connectivité Internet est utilisée par Azure pour :

* Surveiller l’intégrité du cluster
* Lancer le basculement des ressources du cluster
* Changer le nombre de nœuds du cluster via des opérations de mise à l’échelle
* D’autres tâches de gestion

Ces opérations ne nécessitent pas un accès total à Internet. Si vous limitez l’accès à Internet, autorisez l’accès entrant sur le port 443 pour les adresses IP suivantes. Ceci permet à Azure de gérer HDInsight :

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

Région __Centre de l’Inde__ :

* 52.172.153.209
* 52.172.152.49

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

L’exemple suivant montre comment créer un groupe de sécurité réseau qui autorise les adresses nécessaires et qui applique le groupe de sécurité à un sous-réseau au sein de votre réseau virtuel. Changez les adresses IP utilisées dans cet exemple pour les faire correspondre à la région Azure que vous utilisez.

Ces étapes supposent que vous avez déjà créé un réseau virtuel et le sous-réseau dans lequel vous souhaitez installer HDInsight. Consultez [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!IMPORTANT]
> Notez la valeur `priority` utilisée dans ces exemples. Les règles sont testées sur le trafic réseau par ordre par priorité. Une fois qu’une règle correspond aux critères de test et est appliquée, aucune autre règle n’est testée.
> 
> Si vous avez des règles personnalisées qui bloquent une grande partie du trafic entrant (comme une règle **Refuser tout**), vous devrez peut-être ajuster les valeurs de priorité dans ces exemples. Les règles des exemples doivent être appliquées avant les règles qui bloquent l’accès. Sinon, la règle **Refuser tout** est testée en premier et les règles de cet exemple ne sont jamais appliquées. Vous ne devez pas bloquer les règles par défaut pour un réseau virtuel Azure. Par exemple, vous ne devez pas créer de règle **Refuser tout** appliquée avant la règle **AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL** par défaut (qui a une priorité de 65000.)
> 
> Pour plus d’informations sur les règles de groupe de sécurité réseau, consultez [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

**Utilisation d’un modèle de gestion des ressources Azure**

Utilisation du modèle de gestion des ressources suivant issu des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) pour créer un cluster HDInsight dans un réseau virtuel avec les configurations réseau sécurisées :

[Déployer un réseau virtuel Azure sécurisé et un cluster HDInsight Hadoop dans le réseau virtuel](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

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

1. Utilisez la commande suivante pour créer un groupe de sécurité réseau nommé `hdisecure`. Remplacez **RESOURCEGROUPNAME** par le groupe de ressources qui contient le réseau virtuel Azure. Remplacez **LOCATION** par l’emplacement (région) où le groupe a été créé.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    Une fois que le groupe est créé, vous recevez des informations sur le nouveau groupe.

2. Utilisez ce qui suit pour ajouter des règles au nouveau groupe de sécurité réseau qui autorisent les communications entrantes sur le port 443 à partir du service de gestion et de contrôle d’intégrité Azure HDInsight. Remplacez la valeur **RESOURCEGROUPNAME** par le nom du groupe de ressources qui contient le réseau virtuel Azure.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. Une fois que les règles ont été créées, utilisez ce qui suit pour récupérer l’identificateur unique pour ce groupe de sécurité réseau :

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    Cette commande retourne une valeur semblable au texte suivant :

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilisez des guillemets autour des ID dans la commande si vous n’obtenez pas les résultats attendus.

4. Utilisez la commande suivante pour appliquer un groupe de sécurité réseau à un sous-réseau. Remplacez les valeurs __GUID__ et __RESOURCEGROUPNAME__ par celles renvoyées à l’étape précédente. Remplacez __VNETNAME__ et __SUBNETNAME__ par le nom de réseau virtuel et le nom de sous-réseau que vous souhaitez utiliser lors de la création d’un cluster HDInsight.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    Une fois cette commande terminée, vous pouvez installer HDInsight dans le réseau virtuel sécurisé sur le sous-réseau utilisé dans ces étapes.

> [!IMPORTANT]
> Les étapes précédentes donnent uniquement accès au service de gestion et de contrôle d’intégrité de HDInsight sur le cloud Azure. Tout autre accès au cluster HDInsight à partir de l’extérieur du réseau virtuel est bloqué. Vous devez ajouter des règles supplémentaires pour le groupe de sécurité réseau si vous voulez activer l’accès depuis l’extérieur du réseau virtuel.
> 
> L’exemple suivant montre comment activer l’accès SSH depuis Internet : 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Interface de ligne de commande Azure - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

Pour plus d’informations sur les groupes de sécurité réseau, voir [Présentation des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Pour plus d’informations sur le contrôle du routage dans un réseau virtuel Azure, consultez [Routes définies par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

## <a id="tasks"></a>Tâches et informations

Cette section contient des informations relatives aux tâches courantes ainsi que des informations dont vous pouvez avoir besoin lorsque vous utilisez HDInsight avec un réseau virtuel.

### <a name="determine-the-fqdn"></a>Détermination du nom de domaine complet (FQDN)

Un nom de domaine complet spécifique est affecté au cluster HDInsight pour l’interface du réseau virtuel. Le nom de domaine complet est l’adresse que vous devez utiliser lors de la connexion au cluster depuis d’autres ressources du réseau virtuel. Pour déterminer le nom de domaine complet, utilisez l’URL suivante pour consulter le service de gestion Ambari :

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

Vous utilisez le nom du cluster ainsi qu’un service et un composant exécutés sur le cluster, comme le gestionnaire de ressources YARN.

> [!NOTE]
> Les données retournées sont constituées d’un document JSON (JavaScript Objet Notation). Pour extraire le nom de domaine complet uniquement, vous devez utiliser un analyseur JSON pour récupérer la valeur `host_components[0].HostRoles.host_name` .

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

> [!IMPORTANT]
> Si vous avez limité l’accès Internet au cluster, vous ne pouvez pas utiliser Ambari sur Internet. Au lieu de cela, vous devez utiliser une des méthodes suivantes pour récupérer le nom de domaine complet :
>
> * Azure PowerShell : `Get-AzureRmNetworkInterface -ResourceGroupName GROUPNAME | Foreach-object { Write-Output $_.DnsSettings.InternalFqdn }`
> * Azure CLI 2.0 : ` az network nic list --resource-group GROUPNAME --query '[].dnsSettings.internalFqdn'`
>
> Dans les deux exemples, remplacez __GROUPNAME__ par le nom du groupe de ressources qui contient le réseau virtuel.

### <a name="connecting-to-hbase"></a>Connexion à HBase

Pour vous connecter à HBase à distance en utilisant l’API Java, vous devez déterminer les adresses du quorum Zookeeper pour le cluster HBase et spécifier les informations de quorum dans votre application.

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

Certains services tel que SQL Server peuvent limiter les connexions réseau entrantes. Si vous rencontrez des problèmes pour accéder à un service à partir de HDInsight, consultez la documentation pour ce service pour vous assurer que vous avez activé l'accès au réseau. Vous pouvez également vérifier l’accès au réseau en créant une machine virtuelle Azure sur le même réseau virtuel. Utilisez ensuite les utilitaires clients sur la machine virtuelle pour vérifier que celle-ci peut se connecter au service via le réseau virtuel.

## <a id="nextsteps"></a>Étapes suivantes

L’exemple suivant montre comment utiliser HDInsight avec Azure Virtual Network :

* [Analyse des données de capteur avec Storm et HBase dans HDInsight](hdinsight-storm-sensor-data-analysis.md) : montre comment configurer un cluster Storm et HBase dans un réseau virtuel.
* [Approvisionnement des clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : donne des informations sur l’approvisionnement des clusters Hadoop, y compris les informations relatives à l’utilisation de Réseau virtuel Azure.
* [Utilisation de Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop-mac-linux.md) : donne des informations sur l’utilisation de Sqoop pour transférer des données avec SQL Server via un réseau virtuel.

Pour en savoir plus sur les réseaux virtuels Azure, consultez [Vue d’ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).


