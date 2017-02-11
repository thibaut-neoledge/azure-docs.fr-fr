---
title: "Gérer les groupes de sécurité réseau avec PowerShell | Microsoft Docs"
description: "Découvrez comment gérer des groupes de sécurité réseau existants avec PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 8ecda7d59b3631b07e807ee018017c75b963dac5
ms.openlocfilehash: 8eaca4e52363e577c015d9d183f200abedbf4d43


---
# <a name="manage-nsgs-using-powershell"></a>Gérer les groupes de sécurité réseau avec PowerShell

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Récupérer des informations
Vous pouvez afficher vos groupes de sécurité réseau existants, récupérer des règles pour un groupe de sécurité réseau existant et découvrir quelles sont les ressources associées à un groupe de sécurité réseau.

### <a name="view-existing-nsgs"></a>Afficher les groupes de sécurité réseau existants
Pour afficher tous les groupes de sécurité réseau existants d’un abonnement, exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup`.

Résultat attendu :

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Pour afficher la liste des groupes de sécurité réseau dans un groupe de ressources spécifique, exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup`.

Sortie attendue :

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>Répertorier toutes les règles pour un groupe de sécurité réseau
Pour afficher les règles d’un groupe de sécurité réseau nommé **NSG-FrontEnd**, entrez la commande suivante :

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

Sortie attendue :

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> Vous pouvez aussi utiliser `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` pour répertorier les règles par défaut du groupe de sécurité réseau **NSG-FrontEnd**.
> 

### <a name="view-nsgs-associations"></a>Afficher les associations de groupes de sécurité réseau
Pour afficher les ressources auxquelles le groupe de sécurité réseau **NSG-FrontEnd** est associé, exécutez la commande suivante :

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

Recherchez les propriétés **NetworkInterfaces** et **Subnets**, comme indiqué ci-dessous :

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

Dans l’exemple précédent, le groupe de sécurité réseau n’est associé à aucune interface réseau (NIC) ; il est associé à un sous-réseau nommé **FrontEnd**.

## <a name="manage-rules"></a>Gérer les règles
Vous pouvez ajouter des règles à un groupe de sécurité réseau existant, modifier des règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle
Pour ajouter une règle autorisant le trafic **entrant** sur le port **443** d’une machine vers le groupe de sécurité réseau **NSG-FrontEnd**, procédez comme suit :

1. Exécutez la commande suivante pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable :

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Exécutez la commande suivante pour ajouter une règle à un groupe de sécurité réseau :

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Pour enregistrer les modifications apportées au groupe de sécurité réseau, exécutez la commande suivante :

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    La sortie attendue affiche uniquement les règles de sécurité :
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Modifier une règle
Pour modifier la règle créée précédemment qui permet d’autoriser le trafic entrant d’ **Internet** uniquement, procédez comme suit.

1. Exécutez la commande suivante pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable :

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Exécutez la commande suivante avec les nouveaux paramètres de règle :

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange Internet `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Pour enregistrer les modifications apportées au groupe de sécurité réseau, exécutez la commande suivante :

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    La sortie attendue affiche uniquement les règles de sécurité :
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Supprimer une règle
1. Exécutez la commande suivante pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable :

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Pour supprimer la règle du groupe de sécurité réseau, exécutez la commande suivante :

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. Enregistrez les modifications apportées au groupe de sécurité réseau en exécutant la commande suivante :

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Sortie attendue affichant uniquement les règles de sécurité, notez que **https-rule** n’est plus répertorié :
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Gérer les associations
Vous pouvez associer un groupe de sécurité réseau à des cartes réseau et des sous-réseaux. Vous pouvez également dissocier un groupe de sécurité réseau de n’importe quelle ressource à laquelle il est associé.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un groupe de sécurité réseau à une carte réseau
Pour associer le groupe de sécurité réseau **NSG-FrontEnd** à la carte réseau **TestNICWeb1**, procédez comme suit :

1. Exécutez la commande suivante pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable :

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Exécutez la commande suivante pour récupérer la carte réseau existante et la stocker dans une variable :

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. Attribuez à la propriété **NetworkSecurityGroup** de la variable **NIC** la valeur de la variable **NSG** en entrant la commande suivante :

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez la commande suivante :

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Sortie attendue affichant uniquement la propriété **NetworkSecurityGroup** :
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier un groupe de sécurité réseau d’une carte réseau
Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** de la carte réseau **TestNICWeb1**, procédez comme suit :

1. Exécutez la commande suivante pour récupérer la carte réseau existante et la stocker dans une variable :

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. Attribuez à la propriété **NetworkSecurityGroup** de la variable **NIC** la valeur **$null** en exécutant la commande suivante :

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. Pour enregistrer les modifications apportées à la carte réseau, exécutez la commande suivante :

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Sortie attendue affichant uniquement la propriété **NetworkSecurityGroup** :
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier un groupe de sécurité réseau d’un sous-réseau
Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** du sous-réseau **FrontEnd**, procédez comme suit :

1. Exécutez la commande suivante pour récupérer le réseau virtuel et le stocker dans une variable :

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Exécutez la commande suivante pour récupérer le sous-réseau **FrontEnd** et le stocker dans une variable :

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Attribuez à la propriété **NetworkSecurityGroup** de la variable **subnet** la valeur **$null** en entrant la commande suivante :

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez la commande suivante :

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Sortie attendue affichant uniquement les propriétés du sous-réseau **FrontEnd** . Notez qu’il n’y a pas de propriété pour **NetworkSecurityGroup**:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Association d’un groupe de sécurité réseau à un sous-réseau
Pour associer à nouveau le groupe de sécurité réseau **NSG-FrontEnd** au sous-réseau **FrontEnd**, procédez comme suit :

1. Exécutez la commande suivante pour récupérer le réseau virtuel et le stocker dans une variable :

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Exécutez la commande suivante pour récupérer le sous-réseau **FrontEnd** et le stocker dans une variable :

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Exécutez la commande suivante pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable :

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. Attribuez à la propriété **NetworkSecurityGroup** de la variable **subnet** la valeur **$null** en exécutant la commande suivante :

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. Pour enregistrer les modifications apportées au sous-réseau, exécutez la commande suivante :

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Sortie attendue présentant uniquement la propriété **NetworkSecurityGroup** du sous-réseau **FrontEnd** :
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Suppression d'un groupe de sécurité réseau
Vous ne pouvez supprimer un groupe de sécurité réseau que s’il n’est associé à aucune ressource. Pour supprimer un groupe de sécurité réseau, procédez comme suit.

1. Pour consulter les ressources associées à un groupe de sécurité réseau, exécutez `azure network nsg show` comme illustré dans [Afficher les associations de groupes de sécurité réseau](#View-NSGs-associations).
2. Si le groupe de sécurité réseau est associé à des cartes réseau, exécutez `azure network nic set` comme illustré dans [Dissocier un groupe de sécurité réseau d’une carte réseau](#Dissociate-an-NSG-from-a-NIC) pour chaque carte réseau. 
3. Si le groupe de sécurité réseau est associé à un sous-réseau, exécutez `azure network vnet subnet set` comme illustré dans [Dissocier un groupe de sécurité réseau d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet) pour chaque sous-réseau.
4. Pour supprimer le groupe de sécurité réseau, exécutez la commande suivante :

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > Le paramètre `-Force` garantit que vous n’avez pas besoin de confirmer la suppression.
   > 

## <a name="next-steps"></a>Étapes suivantes
* [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.




<!--HONumber=Dec16_HO2-->


