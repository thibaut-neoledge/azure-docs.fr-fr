---
title: "Définir et gérer une adresse IP privée statique à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment définir et gérer une adresse IP privée statique à l’aide de PowerShell | Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 75dbe164bf0fb4b3aff95954ce619781bbafaa5c
ms.openlocfilehash: 3b966921bccb8e2bd29412c6e4aa200c606b4bf8


---
# <a name="set-and-manage-a-static-private-ip-address-using-powershell"></a>Définir et gérer une adresse IP privée statique à l’aide de PowerShell
[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Spécifier une adresse IP privée statique lors de la création d’une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un réseau virtuel nommé *TestVNet* avec l’adresse IP privée statique *192.168.1.101*, procédez comme suit :

1. Définissez des variables pour le compte de stockage, l’emplacement, le groupe de ressources et les informations d’identification à utiliser. Vous devez entrer un nom d’utilisateur et un mot de passe pour la machine virtuelle. Le compte de stockage et le groupe de ressources doivent exister.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. Récupérez le réseau virtuel et le sous-réseau dans lesquels vous voulez créer la machine virtuelle.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. Si nécessaire, créez une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Créez une carte réseau à l’aide de l’adresse IP privée statique que vous souhaitez affecter à la machine virtuelle. Assurez-vous que l’adresse IP fait partie de la plage de sous-réseau à laquelle vous ajoutez la machine virtuelle. Il s’agit de l’étape principale de cet article, dans laquelle vous définissez l’adresse IP privée comme statique.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Créer la machine virtuelle à l’aide de la carte réseau créée ci-dessus.

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

    Sortie attendue :
    
        EndTime             : [Date and time]
        Error               : 
        Output              : 
        StartTime           : [Date and time]
        Status              : Succeeded
        TrackingOperationId : [Id]
        RequestId           : [Id]
        StatusCode          : OK 

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Récupérer des informations d’adresse IP privée statique pour une machine virtuelle
Pour visualiser les informations d’adresse IP privée statique relatives à la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs de *PrivateIpAddress* et *PrivateIpAllocationMethod* :

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Sortie attendue :

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Supprimer une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique ajoutée à la machine virtuelle dans le script ci-dessus, exécutez les commandes PowerShell suivantes :

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Sortie attendue :

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Ajouter une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [adresses IP publiques réservées](virtual-networks-reserved-public-ip.md) .
* En savoir plus sur les [adresses IP publiques de niveau d’instance](virtual-networks-instance-level-public-ip.md) .
* Consulter les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).




<!--HONumber=Nov16_HO5-->


