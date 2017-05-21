---
title: "Se connecter à Kafka sur HDInsight à l’aide de réseaux virtuels - Azure | Microsoft Docs"
description: "Découvrez comment vous connecter à distance à Kafka sur HDInsight en utilisant le client kafka-python. La configuration dans ce document utilise HDInsight à l’intérieur d’un réseau virtuel Azure. Le client distant se connecte au réseau virtuel via une passerelle VPN de point à site."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9489fdc3c5388a7510bc4411b4abb05fa72fbf4f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Se connecter à Kafka sur HDInsight (version préliminaire) via un réseau virtuel Azure

Découvrez comment vous connecter à Kafka sur HDInsight à l’aide de réseaux virtuels Azure. Les clients Kafka (producteurs et consommateurs) peuvent s’exécuter directement sur HDInsight ou sur des systèmes distants. Les clients distants doivent se connecter à Kafka sur HDInsight via un réseau virtuel Azure. Les informations fournies dans ce document vous permettront de comprendre comment les clients distants peuvent se connecter à HDInsight à l’aide de réseaux virtuels Azure.

> [!IMPORTANT]
> Les différentes configurations abordées dans ce document peuvent être utilisées avec des clients Linux, macOS ou Windows. Toutefois, l’exemple faisant appel à une connexion de point à site fournit uniquement un client VPN pour Windows.
>
> L’exemple utilise également un client Python ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) pour vérifier la communication avec Kafka sur HDInsight.

## <a name="architecture-and-planning"></a>Architecture et planification

Les clusters HDInsight sont sécurisés à l’intérieur d’un réseau virtuel Azure et autorisent uniquement le trafic SSH et HTTPS entrant. Le trafic arrive via une passerelle publique, qui n’achemine pas le trafic des clients Kafka. Pour accéder à Kafka à partir d’un client distant, vous devez créer un réseau virtuel Azure qui fournit une passerelle de réseau privé virtuel (VPN). Après avoir configuré le réseau virtuel et la passerelle, installez HDInsight dans le réseau virtuel et connectez-vous à celui-ci à l’aide de la passerelle VPN.

![Un schéma illustrant HDInsight à l’intérieur d’un réseau virtuel Azure avec un client connecté via un VPN](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

La liste suivante contient des informations sur l’utilisation de Kafka sur HDInsight avec un réseau virtuel :

1. Créez un réseau virtuel. Pour obtenir des informations spécifiques sur l’utilisation de HDInsight avec des réseaux virtuels Azure, consultez le document [Étendre HDInsight à l’aide de réseaux virtuels Azure](hdinsight-extend-hadoop-virtual-network.md).

2. (Facultatif) Créez une machine virtuelle Azure à l’intérieur du réseau virtuel et installez un serveur DNS personnalisé dessus. Ce serveur DNS est utilisé pour activer la résolution de noms pour les clients distants dans une configuration de site à site ou entre réseaux virtuels. Pour plus d’informations, consultez le document [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

3. Créez une passerelle VPN pour le réseau virtuel. Pour plus d’informations sur les configurations de passerelle VPN, consultez le document [À propos de la passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

4. Créez une instance HDInsight à l’intérieur du réseau virtuel. Si vous avez configuré un serveur DNS personnalisé pour le réseau, HDInsight est automatiquement configuré pour l’utiliser.

5. (Facultatif) Si vous n’avez pas utilisé de serveur DNS personnalisé et si vous n’avez pas activé la résolution de noms entre les clients et le réseau virtuel, vous devez configurer Kafka pour la publication d’adresses IP. Pour plus d’informations, consultez la section [Configuration de Kafka pour la publication d’adresses IP](#configure-kafka-for-ip-advertising) du présent document.

## <a name="create-using-powershell"></a>Création à l’aide de PowerShell

En suivant les étapes de cette section, vous pourrez créer la configuration ci-dessous à l’aide [d’Azure PowerShell](/powershell/azure/overview) :

* Réseau virtuel Azure
* Passerelle VPN de point à site
* Compte Stockage Azure (utilisé par HDInsight)
* Kafka sur HDInsight

1. Suivez les étapes décrites dans le document [Créer un certificat auto-signé pour les connexions de point à site avec PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md) afin de créer les certificats requis pour la passerelle.

2. Ouvrez une invite de commande PowerShell et utilisez le code suivant pour vous connecter à votre abonnement Azure :

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Utilisez le code suivant pour créer des variables qui contiennent des informations de configuration :

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. Utilisez le code suivant pour créer le réseau virtuel et le groupe de ressources Azure :

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > Ce processus peut prendre plusieurs minutes.

5. Utilisez le code suivant pour créer le compte Stockage Azure et le conteneur de blobs :

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Utilisez le code suivant pour créer le cluster HDInsight :

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Ce processus prend environ 20 minutes.

8. Utilisez la cmdlet suivante pour récupérer l’URL du client VPN Windows pour le réseau virtuel :

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Pour télécharger le client VPN Windows, utilisez l’URI renvoyé dans votre navigateur web.

## <a name="configure-kafka-for-ip-advertising"></a>Configuration de Kafka pour la publication d’adresses IP

Par défaut, Zookeeper renvoie le nom de domaine des répartiteurs Kafka aux clients. Cette configuration ne fonctionne pas pour le client VPN, car il ne peut pas utiliser la résolution de noms pour les entités dans le réseau virtuel. Suivez les étapes ci-dessous pour configurer Kafka sur HDInsight, afin qu’il publie des adresses IP plutôt que des noms de domaine :

1. Accédez à https://CLUSTERNAME.azurehdinsight.net via votre navigateur web. Remplacez __CLUSTERNAME__ par le nom du cluster Kafka sur HDInsight.

    Lorsque vous y êtes invité, utilisez le nom et le mot de passe utilisateur HTTPS correspondant au cluster. L’interface utilisateur web d’Ambari pour le cluster s’affiche.

2. Pour afficher plus d’informations sur Kafka, sélectionnez __Kafka__ dans la liste sur la gauche.

    ![Liste des services avec l’option Kafka en surbrillance](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Pour afficher la configuration Kafka, sélectionnez __Configurations__ en haut et au centre de la page.

    ![Liens vers les configurations Kafka](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Pour trouver la configuration __kafka-env__, entrez `kafka-env` dans le champ __Filtre__ situé en haut à droite.

    ![Configuration Kafka, pour kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Pour configurer Kafka afin qu’il publie des adresses IP, ajoutez le texte suivant au bas du champ __kafka-env-template__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Pour configurer l’interface écoutée par Kafka, entrez `listeners` dans le champ __Filtre__ situé en haut à droite.

7. Pour configurer Kafka afin qu’il écoute toutes les interfaces réseau, remplacez la valeur du champ __écouteurs__ par `PLAINTEXT://0.0.0.0:92092`.

8. Utilisez le bouton __Enregistrer__ pour enregistrer les modifications apportées à la configuration. Entrez un message texte décrivant les modifications. Sélectionnez __OK__ une fois les modifications apportées.

    ![Bouton pour enregistrer la configuration](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Pour éviter des erreurs lors du redémarrage de Kafka, utilisez le bouton __Service Actions__ (Actions du service) et sélectionnez __Activer le mode de maintenance__. Sélectionnez OK pour terminer cette opération.

    ![Actions de service, avec l’option Activer le mode de maintenance en surbrillance](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Pour redémarrer Kafka, utilisez le bouton __Redémarrer__ et sélectionnez __Restart All Affected__ (Redémarrer tous les éléments affectés). Confirmez le redémarrage, puis utilisez le bouton __OK__ une fois l’opération terminée.

    ![Bouton Redémarrer avec l’option Restart All Affected (Redémarrer tous les éléments affectés) en surbrillance](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. Pour désactiver le mode de maintenance, utilisez le bouton __Service Actions__ (Actions du service) et sélectionnez __Désactiver le mode de maintenance__. Sélectionnez **OK** pour terminer cette opération.

## <a name="connect-to-the-vpn-gateway"></a>Connexion à la passerelle VPN

Pour vous connecter à la passerelle VPN à partir d’un __client Windows__, suivez la section __Connexion à Azure__ du document [Configuration d’une connexion de point à site à un réseau virtuel à l’aide de PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameclientcertificatea7---install-an-exported-client-certificate).

## <a name="remote-kafka-client"></a>Client Kafka distant

Pour vous connecter à Kafka à partir de l’ordinateur client, vous devez utiliser l’adresse IP des répartiteurs Kafka ou des nœuds Zookeeper (selon ce dont votre client a besoin). Suivez les étapes ci-dessous pour récupérer l’adresse IP des répartiteurs Kafka, puis utilisez-les à partir d’une application Python.

1. Utilisez le script suivant pour récupérer les adresses IP des nœuds du cluster :

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    Ce script suppose que `$resourceGroupName` est le nom du groupe de ressources Azure qui contient le réseau virtuel Azure. Le résultat du script ressemble au texte suivant :

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Si votre client Kafka utilise les nœuds Zookeeper au lieu des répartiteurs Kafka, remplacez `*workernode*` par `*zookeepernode*` dans le script PowerShell.

    > [!WARNING]
    > Si vous mettez le cluster à l’échelle, ou si des nœuds échouent et sont remplacés, les adresses IP peuvent changer. Il n’existe actuellement aucun moyen pour pré-affecter des adresses IP spécifiques aux nœuds d’un cluster HDInsight.

2. Utilisez ce qui suit pour installer le client [kafka-python](http://kafka-python.readthedocs.io/) :

        pip install kafka-python

3. Pour envoyer des données à Kafka, utilisez le code Python suivant :

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Remplacez les entrées `'ip_address'` par les adresses renvoyées à partir de l’étape 1 de cette section.

    > [!NOTE]
    > Ce code envoie la chaîne `test message` à la rubrique `testtopic`. La configuration par défaut de Kafka sur HDInsight consiste à créer la rubrique si elle n’existe pas.

4. Pour récupérer les messages à partir de Kafka, utilisez le code Python suivant :

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Remplacez les entrées `'ip_address'` par les adresses renvoyées à partir de l’étape 1 de cette section. La sortie contient le message de test envoyé au producteur au cours de l’étape précédente.

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes pour vous connecter au réseau virtuel, ou pour vous connecter à HDInsight via le réseau, consultez le document [Résoudre les problèmes liés à la passerelle de réseau virtuel et aux connexions par le biais de PowerShell d’Azure Network Watcher](../network-watcher/network-watcher-troubleshoot-manage-powershell.md) pour obtenir des conseils.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’un réseau virtuel Azure avec une passerelle VPN de point à site, consultez les documents suivants :

* [Configuration d’une connexion point à site à un réseau virtuel à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configuration d’une connexion de point à site à un réseau virtuel à l’aide de PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Pour plus d’informations sur l’utilisation de Kafka sur HDInsight, consultez les documents suivants :

* [Prise en main de Kafka sur HDInsight](hdinsight-apache-kafka-get-started.md)
* [MirrorMaker permet de créer un réplica Kafka sur un cluster HDInsight (version préliminaire)](hdinsight-apache-kafka-mirroring.md)

