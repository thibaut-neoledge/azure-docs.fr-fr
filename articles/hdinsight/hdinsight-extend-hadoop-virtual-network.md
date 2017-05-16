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
ms.date: 04/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a3052e53c642ef3e6d9bb0489476274987707f91
ms.lasthandoff: 04/27/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Extension des capacités de HDInsight à l’aide d’Azure Virtual Network

Découvrez comment utiliser les réseaux virtuels Azure avec HDInsight pour activer les scénarios suivants :

* Limiter l’accès à HDInsight. Par exemple, empêcher le trafic entrant provenant d’Internet.

* Accéder directement aux services sur HDInsight qui ne sont pas exposés sur Internet. Par exemple, travailler directement avec les répartiteurs Kafka ou utiliser l’API Java HBase.

* Connecter directement les services à HDInsight. Par exemple, utiliser Oozie pour importer ou exporter des données vers un serveur SQL Server dans votre centre de données.

* Créer des solutions qui impliquent plusieurs clusters HDInsight. Par exemple, utiliser Spark ou Storm pour analyser les données stockées dans Kafka.

## <a name="prerequisites"></a>Composants requis

* Azure CLI 2.0 : Pour plus d’informations, consultez [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell : Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Les étapes décrites dans ce document nécessitent la dernière version d'Azure PowerShell et de l’interface de ligne de commande (CLI) Azure. Si vous utilisez une ancienne version, les commandes peuvent être différentes. Pour de meilleurs résultats, utilisez les liens précédents pour installer les dernières versions.

## <a id="whatis"></a>Présentation d’Azure Virtual Network

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution.

Voici la liste des éléments à prendre en considération lors de l’utilisation de HDInsight dans un réseau virtuel :

* __Réseaux virtuels Classic et Resource Manager__ : utilisez le tableau suivant pour déterminer le type de réseau à utiliser en fonction du système d’exploitation du cluster HDInsight :

    | Système d’exploitation de HDInsight | Réseau virtuel Classic | Réseau virtuel Resource Manager |
    | ---- | ---- | ---- |
    | Linux | no | yes |
    | Windows | yes | no |

    Pour accéder aux ressources dans un réseau virtuel incompatible, joignez les deux réseaux. Pour plus d’informations sur la connexion de réseaux virtuels classiques et Resource Manager, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

* __DNS personnalisé__ : Azure fournit la résolution de noms pour les services Azure qui sont installés dans un réseau virtuel Azure. Cette résolution de noms ne s’étend pas en dehors du réseau virtuel. Pour activer la résolution de noms pour les ressources en dehors du réseau virtuel, vous devez utiliser un serveur DNS personnalisé. Pour plus d’informations sur l’utilisation d’un serveur DNS personnalisé, consultez le document [Résolution de noms pour les machines virtuelles et les instances de rôles](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

* __Tunneling forcé__ : HDInsight ne prend pas en charge la configuration de tunneling forcé du réseau virtuel Azure.

* __Limitation du trafic réseau__ : HDInsight prend en charge l’utilisation de groupes de sécurité réseau pour limiter le trafic réseau, mais nécessite un accès illimité à plusieurs adresses IP Azure. Pour plus d’informations, consultez la section [Réseaux virtuels sécurisés](#secured-virtual-networks).

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud)

![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

L’utilisation de Virtual Network pour associer les services Azure à Azure HDInsight active les scénarios suivants :

* **Appel des services ou des travaux HDInsight** depuis le service Sites Web Azure ou des services exécutés dans des machines virtuelles Azure.

* **Transfert direct des données** entre HDInsight et Base de données SQL Azure, SQL Server ou une autre solution de stockage de données exécutée sur une machine virtuelle.

* **Combinaison de plusieurs serveurs HDInsight** en une seule solution. Il existe différents types de clusters HDInsight, correspondant à la charge de travail ou à la technologie pour laquelle ils sont optimisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster. L’utilisation d’un réseau virtuel permet à plusieurs clusters de communiquer directement entre eux.

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>Connecter des ressources de cloud à un réseau de centre de données local

Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données au réseau virtuel Azure. Vous pouvez établir la connexion à l’aide d’un appareil VPN matériel ou du Service de routage et d’accès à distance.

![diagramme d’une configuration de site à site](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

Une configuration de point à site vous permet de connecter une ressource propre à Réseau virtuel Azure en utilisant un VPN logiciel.

![diagramme d’une configuration de point à site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

L’utilisation d’un réseau virtuel pour lier le cloud et votre centre de données autorise des scénarios similaires dans la configuration cloud uniquement. Mais au lieu d’être limité à l’utilisation des ressources dans le cloud, vous pouvez également utiliser des ressources de votre centre de données.

* **Transfert direct de données** entre HDInsight et votre centre de données. Par exemple, en utilisant Sqoop pour transférer des données vers ou depuis SQL Server ou en lisant des données générées par une application métier.

* **Appel des services ou des travaux HDInsight** depuis une application métier. Par exemple, l’utilisation des API Java HBase pour stocker et récupérer des données depuis un cluster HBase HDInsight.

Pour plus d'informations sur les fonctions, les avantages et les capacités du service Réseau virtuel Azure, consultez [Vue d’ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Créez le réseau virtuel Azure avant d’approvisionner un cluster HDInsight, puis spécifiez le réseau lors de la création du cluster. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="secured-virtual-networks"></a>Réseaux virtuels sécurisés

Le service HDInsight est un service géré et nécessite un accès aux services de gestion Azure lors de l’approvisionnement et quand il est en cours d’exécution. Le portail de gestion Azure propose les services suivants :

* Surveiller l’intégrité du cluster
* Lancer le basculement des ressources du cluster
* Changer le nombre de nœuds du cluster via des opérations de mise à l’échelle
* D’autres tâches de gestion

> [!NOTE]
> Ces opérations ne nécessitent pas un accès total à Internet. Si vous limitez l’accès à Internet, autorisez l’accès entrant sur le port 443 pour les adresses IP suivantes. Ceci permet à Azure de gérer HDInsight :

Les adresses IP qui doivent être autorisés sont spécifiques à la région dans laquelle se trouvent le cluster HDInsight et le réseau virtuel. Utilisez le tableau suivant pour rechercher les adresses IP de votre région.

| Pays | Région | Adresses IP autorisées | Port autorisé |
| ---- | ---- | ---- | ---- |
| Brésil | Sud du Brésil | 191.235.84.104</br>191.235.87.113 | 443 |
| Canada | Est du Canada | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | Centre du Canada | 52.228.37.66</br>52.228.45.222 | 443 |
| Allemagne | Centre de l’Allemagne | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | Nord-Est de l’Allemagne | 51.5.150.132</br>51.5.144.101 | 443 |
| Inde | Inde centrale | 52.172.153.209</br>52.172.152.49 | 443 |
| Royaume-Uni | Ouest du Royaume-Uni | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | Sud du Royaume-Uni | 51.140.47.39</br>51.140.52.16 | 443 |
| États-Unis | Centre-Ouest des États-Unis | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | Ouest des États-Unis 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__Si votre région ne figure pas dans ce tableau__, autorisez le trafic vers le port __443__ sur l’adresse IP suivante :

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight prend uniquement en charge la restriction du trafic entrant. Lorsque vous définissez les règles du groupe de sécurité réseau pour le sous-réseau contenant HDInsight, __utilisez uniquement les règles de trafic entrant__.

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>Utilisation de HDInsight dans des réseaux virtuels sécurisés

Si vous bloquez l’accès à Internet, vous ne pouvez pas utiliser les services HDInsight qui sont normalement exposés via la passerelle publique d’un cluster. Ceux-ci incluent Ambari et SSH. Au lieu de cela, vous devez accéder à des services à l’aide de l’adresse IP interne des nœuds principaux du cluster.

Pour trouver l’adresse IP interne des nœuds principaux, utilisez les scripts indiqués dans la section [IP internes et noms de domaine complets](#internal-ips-and-fqdns).

### <a name="example-secured-virtual-network"></a>Exemple : réseau virtuel sécurisé

Les exemples suivants montrent comment créer un groupe de sécurité réseau qui autorise sur le port 443 le trafic entrant qui provient des adresses IP suivantes :

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> Ces adresses sont destinées aux régions pour lesquelles aucune adresse IP spécifique n’est répertoriée. Pour trouver les adresses IP de votre région, utilisez les informations de la section [Réseaux virtuels sécurisés](#secured-virtual-networks).

Ces étapes supposent que vous avez déjà créé un réseau virtuel et le sous-réseau dans lequel vous souhaitez installer HDInsight. Consultez [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!WARNING]
> Les règles sont testées sur le trafic réseau par ordre par __priorité__. Une fois qu’une règle correspond aux critères de test, elle est appliquée et aucune autre règle n’est testée pour cette demande. Si vous disposez d’une règle qui bloque globalement le trafic entrant (comme une règle **Refuser tout**), elle __doit__ venir après les règles qui autorisent le trafic.
>
> Pour plus d’informations sur les règles de groupe de sécurité réseau, consultez le document [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

**Exemple : modèle de gestion des ressources Azure**

Utilisation du modèle de gestion des ressources suivant issu des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) pour créer un cluster HDInsight dans un réseau virtuel avec les configurations réseau sécurisées :

[Déployer un réseau virtuel Azure sécurisé et un cluster HDInsight Hadoop dans le réseau virtuel](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**Exemple : Azure PowerShell**

```powershell
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
# Create a Network Security Group.
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
```

**Exemple : Azure CLI**

1. Utilisez la commande suivante pour créer un groupe de sécurité réseau nommé `hdisecure`. Remplacez **RESOURCEGROUPNAME** par le groupe de ressources qui contient le réseau virtuel Azure. Remplacez **LOCATION** par l’emplacement (région) où le groupe a été créé.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Une fois que le groupe est créé, vous recevez des informations sur le nouveau groupe.

2. Utilisez ce qui suit pour ajouter des règles au nouveau groupe de sécurité réseau qui autorisent les communications entrantes sur le port 443 à partir du service de gestion et de contrôle d’intégrité Azure HDInsight. Remplacez la valeur **RESOURCEGROUPNAME** par le nom du groupe de ressources qui contient le réseau virtuel Azure.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. Une fois que les règles ont été créées, utilisez ce qui suit pour récupérer l’identificateur unique pour ce groupe de sécurité réseau :

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Cette commande retourne une valeur semblable au texte suivant :

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilisez des guillemets autour des ID dans la commande si vous n’obtenez pas les résultats attendus.

4. Utilisez la commande suivante pour appliquer un groupe de sécurité réseau à un sous-réseau. Remplacez les valeurs __GUID__ et __RESOURCEGROUPNAME__ par celles renvoyées à l’étape précédente. Remplacez __VNETNAME__ et __SUBNETNAME__ par le nom de réseau virtuel et le nom de sous-réseau que vous souhaitez utiliser lors de la création d’un cluster HDInsight.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Une fois cette commande terminée, vous pouvez installer HDInsight dans le réseau virtuel sécurisé sur le sous-réseau utilisé dans ces étapes.

> [!IMPORTANT]
> Les étapes précédentes donnent uniquement accès au service de gestion et de contrôle d’intégrité de HDInsight sur le cloud Azure. Tout autre accès au cluster HDInsight à partir de l’extérieur du réseau virtuel est bloqué. Pour activer l’accès depuis l’extérieur du réseau virtuel, vous devez ajouter des règles supplémentaires pour le groupe de sécurité réseau.
>
> L’exemple suivant montre comment activer l’accès SSH depuis Internet :
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

Pour plus d’informations sur les groupes de sécurité réseau, voir [Présentation des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Pour plus d’informations sur le contrôle du routage dans un réseau virtuel Azure, consultez [Routes définies par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="retrieve-internal-ips-and-fqdns"></a>Récupérer les adresses IP internes et noms de domaine complets

Lors d’une connexion à HDInsight via un réseau virtuel, vous pouvez vous connecter directement aux nœuds du cluster. Pour déterminer l’adresse IP interne et les noms de domaine complets (FQDN) pour les nœuds du cluster, utilisez les scripts suivants :

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

__Interface de ligne de commande Azure__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> Dans l’exemple Azure CLI 2.0, remplacez `<resourcegroupname>` par le nom du groupe de ressources qui contient le réseau virtuel.

Les scripts fonctionnent en interrogeant les cartes d’interface réseau (NIC) virtuelles pour le cluster. Les cartes d’interface réseau existent dans le groupe de ressources qui contient le réseau virtuel utilisé par HDInsight.

## <a id="nextsteps"></a>Étapes suivantes

L’exemple suivant montre comment utiliser HDInsight avec Azure Virtual Network :

* [Clusters HBase dans Azure Virtual Network](hdinsight-hbase-provision-vnet.md)

* [Analyse des données de capteur avec Storm et HBase dans HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Approvisionnement de clusters dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

* [Utiliser Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop-mac-linux.md)

Pour en savoir plus sur les réseaux virtuels Azure, consultez [Vue d’ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).


