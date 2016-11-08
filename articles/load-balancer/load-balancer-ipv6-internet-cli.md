---
title: Créer un équilibrage de charge accessible sur Internet avec IPv6 dans Azure Resource Manager, à l’aide de l’interface de ligne de commande Azure | Microsoft Docs
description: Découvrir comment créer un équilibrage de charge accessible sur Internet avec IPv6 dans Azure Resource Manager, à l’aide de l’interface de ligne de commande Azure
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: IPv6, équilibreur de charge azure, double pile, adresse ip publique, ipv6 natif, mobile, iot

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: sewhee

---
# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Créer un équilibrage de charge accessible sur Internet avec IPv6 dans Azure Resource Manager, à l’aide de l’interface de ligne de commande Azure
> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interface de ligne de commande Azure](load-balancer-ipv6-internet-cli.md)
> * [Modèle](load-balancer-ipv6-internet-template.md)
> 
> 

Un équilibrage de charge Azure est de type Couche 4 (TCP, UDP). L’équilibrage de charge offre une disponibilité élevée en distribuant le trafic entrant parmi les instances de service saines dans les services cloud ou les machines virtuelles dans un jeu d’équilibrage de la charge. Azure Load Balancer peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

## <a name="example-deployment-scenario"></a>Exemple de scénario de déploiement
Le diagramme suivant illustre la solution d’équilibrage de charge déployée à l’aide de l’exemple de modèle décrit dans cet article.

![Scénario d’équilibreur de charge](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Dans ce scénario, vous allez créer les ressources Azure suivantes :

* deux machines virtuelles ;
* une interface de réseau virtuel pour chaque machine virtuelle avec des adresses IPv4 et IPv6 affectées ;
* un équilibrage de charge accessible sur Internet avec une adresse IP publique IPv4 et IPv6 ;
* un groupe à haute disponibilité contenant les deux machines virtuelles ;
* deux règles d’équilibrage de charge pour mapper les adresses IP virtuelles publiques sur les points de terminaison privés.

## <a name="deploying-the-solution-using-the-azure-cli"></a>Déploiement de la solution à l’aide de l’interface de ligne de commande (CLI) Azure
Les étapes suivantes expliquent comment créer un équilibrage de charge accessible sur Internet à l'aide d'Azure Resource Manager avec CLI. Avec Azure Resource Manager, toutes les ressources sont créées et configurées individuellement, puis rassemblées pour en créer une unique.

Pour déployer un équilibrage de charge, vous devez créer et configurer les objets suivants :

* Configuration d’adresses IP frontales : contient les adresses IP publiques pour le trafic réseau entrant.
* Pool d’adresses principales : contient des interfaces réseau (NIC) pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibrage de charge.
* Règles d’équilibrage de charge : contient des règles de mappage d’un port public situé sur l’équilibrage de charge pour le pool d’adresses principales.
* Règles NAT entrantes : contient des règles de mappage d’un port public situé sur l’équilibrage de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.
* Sondes : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Configurer votre environnement d’interface de ligne de commande pour utiliser Azure Resource Manager
Pour cet exemple, nous utilisons les outils d’interface de ligne de commande dans une fenêtre de commande PowerShell. Nous n’utilisons pas les applets de commande Microsoft Azure PowerShell mais utilisons les fonctionnalités de script PowerShell afin d’améliorer la lisibilité et la réutilisation.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape vous invitant à sélectionner votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** afin d’activer le mode Resource Manager.
   
        azure config mode arm
   
    Sortie attendue :
   
        info:    New mode is arm
3. Connectez-vous à Azure afin de consulter une liste de vos abonnements.
   
        azure login
   
    À l’invite, entrez vos informations d’identification Azure.
   
        azure account list
   
    Sélectionnez l’abonnement que vous souhaitez utiliser. Prenez note de l’ID d’abonnement pour la prochaine étape.
4. Configurez des variables PowerShell à utiliser avec les commandes d’interface de ligne de commande.
   
        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group,-a-load-balancer,-a-virtual-network,-and-subnets"></a>Créer un groupe de ressources, un équilibrage de charge, un réseau virtuel et des sous-réseaux
1. Créer un groupe de ressources
   
        azure group create $rgName $location
2. Créer un équilibrage de charge
   
        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
3. Créez un réseau virtuel (VNet).
   
        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
   
    Créez deux sous-réseaux dans ce réseau virtuel.
   
        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Créer des adresses IP publiques pour le pool frontal
1. Configurer les variables PowerShell
   
        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"
2. Créez une adresse IP publique pour le pool frontal.
   
        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
   
   > [!IMPORTANT]
   > L’équilibreur de charge utilise l’étiquette du domaine de l’adresse IP publique en tant que nom de domaine complet (FQDN). Cet usage diffère d’un déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibrage de charge.
   > Dans cet exemple, le nom de domaine complet est *contoso09152016.southcentralus.cloudapp.azure.com*.
   > 
   > 

## <a name="create-front-end-and-back-end-pools"></a>Créer ds pools frontaux et principaux
Cet exemple crée le pool d’IP frontal qui reçoit le trafic réseau entrant pour l’équilibrage de charge et le pool d’IP principal où le pool frontal envoie le trafic de réseau équilibré.

1. Configurer les variables PowerShell
   
        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"
2. Créez un pool d’IP frontal associant l’IP publique créée lors de l’étape précédente et l’équilibrage de charge.
   
        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe,-nat-rules,-and-lb-rules"></a>Créer la sonde, les règles NAT et les règles LB
Cet exemple crée les éléments suivants :

* une règle de sonde dédiée à la détection de connectivité sur le port TCP 80 ;
* une règle NAT pour transférer l’ensemble du trafic entrant sur le port 3389 vers le port 3389 pour RDP<sup>1</sup> ;
* une règle NAT pour transférer l’ensemble du trafic entrant sur le port 3391 vers le port 3389 pour RDP<sup>1</sup> ;
* une règle d’équilibrage de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal ;

<sup>1</sup> Les règles NAT sont associées à une instance de machine virtuelle spécifique derrière l’équilibreur de charge. Le trafic réseau arrivant sur le port 3389 est envoyé à la machine virtuelle spécifique sur le port 22 associé à la règle NAT. Vous devez spécifier un protocole (TCP ou UDP) pour une règle NAT. Il est impossible d'attribuer les deux protocoles au même port.

1. Configurer les variables PowerShell
   
        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"
2. Créer la sonde
   
    L’exemple suivant illustre la création d’une sonde TCP qui détecte la connectivité au port TCP principal 80 toutes les 15 secondes. Elle marque la ressource principale comme indisponible après deux échecs consécutifs.
   
        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
3. Créer des règles NAT entrantes qui prennent en charge les connexions RDP aux ressources principales
   
        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
4. Créer des règles d’équilibrage de charge qui transmettent le trafic sur différents ports principaux en fonction du port frontal de réception de la requête
   
        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
5. Vérifier vos paramètres
   
        azure network lb show --resource-group $rgName --name $lbName
   
    Sortie attendue :
   
        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Créer des cartes réseau
Créer des cartes réseau et associez-les à des règles NAT, des règles d’équilibrage de charge et des sondes.

1. Configurer les variables PowerShell
   
        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
2. Créez une carte réseau pour chaque instance principale et ajoutez une configuration IPv6.
   
        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name
   
        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Créer les ressources de machines virtuelles principales et associer chaque carte réseau
Pour créer des machines virtuelles, vous devez disposer d’un compte de stockage. Pour l’équilibrage de charge, les machines virtuelles doivent être membres d’un groupe à haute disponibilité. Pour plus d’informations sur la création des machines virtuelles, consultez la section [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. Configurer les variables PowerShell
   
        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"
   
   > [!WARNING]
   > Cet exemple utilise le nom d’utilisateur et le mot de passe pour les machines virtuelles, en texte clair. Prenez des précautions particulières lors de l’utilisation des informations d’identification en texte clair. Pour découvrir une méthode sécurisée de traitement des informations d’identification dans PowerShell, consultez l’applet de commande [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .
   > 
   > 
2. Créer le compte de stockage et le groupe à haute disponibilité
   
    Vous pouvez utilise un compte de stockage existant lors de la création des machines virtuelles. La commande suivante génère un nouveau compte de stockage.
   
        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
   
    Ensuite, créez le groupe à haute disponibilité.
   
        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
3. Créer les machines virtuelles avec les cartes réseau associées
   
        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
   
        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>Étapes suivantes
[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!--HONumber=Oct16_HO2-->


