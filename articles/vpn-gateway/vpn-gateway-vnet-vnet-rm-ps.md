---
title: "Connecter un réseau virtuel Azure à un autre réseau virtuel : PowerShell | Microsoft Docs"
description: "Cet article vous guide dans l’interconnexion de réseaux virtuels avec Azure Resource Manager et PowerShell"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 900e104574c4250adc2a0d3f5abf3749da4a578b
ms.lasthandoff: 04/12/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-powershell"></a>Configurer une connexion de réseau virtuel à réseau virtuel à l’aide de PowerShell

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations de connexion multi-sites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels.


![Diagramme v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

Cet article vous guidera au long des étapes de création d’une connexion entre des réseaux virtuels dans le modèle de déploiement Resource Manager à l’aide d’une passerelle VPN. Les réseaux virtuels peuvent être situés dans des régions identiques ou différentes et appartenir à des abonnements identiques ou différents. 

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] Si vous souhaitez créer une interconnexion de réseaux virtuels à l’aide d’un modèle de déploiement différent, entre différents modèles de déploiement ou au moyen d’un autre outil de déploiement, vous pouvez sélectionner une option dans la liste déroulante d’articles suivante :

> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Classic - Portail Azure](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connexions entre différents modèles de déploiement - Portail Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connexions entre différents modèles de déploiement - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions de réseau virtuel à réseau virtuel
La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN Azure pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Les réseaux virtuels que vous connectez peuvent être situés dans différentes régions. Ou dans différents abonnements. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels, comme indiqué dans le schéma suivant :

![À propos des connexions](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>Pourquoi connecter des réseaux virtuels ?
Vous pouvez décider de connecter des réseaux virtuels pour les raisons suivantes :

* **Géo-redondance et présence géographique dans plusieurs régions**
  
  * Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
  * Avec Traffic Manager et l’équilibreur de charge Azure, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.
* **Applications multiniveaux régionales avec une limite d’isolement ou administrative**
  
  * Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés pour des besoins d’isolement ou d’administration.

Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez la partie [Interconnexion de réseaux virtuels](#faq) à la fin de cet article.

## <a name="which-set-of-steps-should-i-use"></a>Quelle procédure dois-je utiliser ?
Cet article inclut deux ensembles d’étapes distincts. L’un des ensembles est destiné aux [réseaux virtuels qui se trouvent dans le même abonnement](#samesub), et l’autre aux [réseaux virtuels qui se trouvent dans des abonnements différents](#difsub). La principale différence réside dans le fait que vous puissiez ou non créer et configurer toutes les ressources de passerelle et de réseau virtuel au sein de la même session PowerShell.

Les étapes de cet article utilisent les variables déclarées au début de chaque section. Si vous travaillez déjà avec des réseaux virtuels existants, modifiez les variables pour les adapter aux paramètres de votre propre environnement. 

## <a name="samesub"></a>Connexion de réseaux virtuels situés dans le même abonnement
![Diagramme v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Avant de commencer
Avant de commencer, vous devez installer les applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .

### <a name="Step1"></a>Étape 1 : planifier vos plages d’adresses IP
Dans les étapes suivantes, nous allons créer deux réseaux virtuels avec leurs sous-réseaux de passerelle respectifs et leur configuration. Nous allons ensuite créer une connexion VPN entre les deux réseaux virtuels. Il est important de planifier les plages d’adresses IP pour votre configuration réseau. N’oubliez pas que vous devez vous assurer qu’aucune plage de réseaux virtuels ou de réseaux locaux ne se chevauche.

Nous utilisons les valeurs suivantes dans les exemples :

**Valeurs pour TestVNet1 :**

* Nom du réseau virtuel : TestVNet1
* Groupe de ressources : TestRG1
* Emplacement : Est des États-Unis
* TestVNet1 : 10.11.0.0/16 et 10.12.0.0/16
* FrontEnd : 10.11.0.0/24
* BackEnd : 10.12.0.0/24
* GatewaySubnet : 10.12.255.0/27
* Serveur DNS : 8.8.8.8
* GatewayName : VNet1GW
* Adresse IP publique : VNet1GWIP
* Type de VPN : RouteBased
* Connection(1to4) : VNet1toVNet4
* Connection(1to5) : VNet1toVNet5
* Type de connexion : VNet2VNet

**Valeurs pour TestVNet4 :**

* Nom du réseau virtuel : TestVNet4
* TestVNet2 : 10.41.0.0/16 et 10.42.0.0/16
* Serveur frontal : 10.41.0.0/24
* Serveur principal : 10.42.0.0/24
* Sous-réseau de passerelle : 10.42.255.0/27
* Groupe de ressources : TestRG4
* Emplacement : États-Unis de l’Ouest
* Serveur DNS : 8.8.8.8
* Nom de la passerelle : VNet4GW
* Adresse IP publique : VNet4GWIP
* Type de VPN : RouteBased
* Connexion : VNet4toVNet1
* Type de connexion : VNet2VNet

### <a name="Step2"></a>Étape 2 : créez et configurez TestVNet1
1. Déclarer vos variables
   
    Commencez par déclarer les variables. Cet exemple déclare les variables avec les valeurs de cet exercice. Dans la plupart des cas, vous devez remplacer les valeurs par les vôtres. Cependant, vous pouvez utiliser ces variables si vous exécutez la procédure pour vous familiariser avec ce type de configuration. Si besoin, modifiez les variables, puis copiez et collez-les dans la console PowerShell.

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $DNS1 = "8.8.8.8"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```
2. Connexion à votre abonnement
   
    Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

  ```powershell
  Login-AzureRmAccount
  ```
   
    Vérifiez les abonnements associés au compte.
 
  ```powershell
  Get-AzureRmSubscription
  ``` 
   
    Spécifiez l’abonnement que vous souhaitez utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```

3. Création d’un groupe de ressources

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. Créer les configurations de sous-réseau pour TestVNet1
   
    Cet exemple crée un réseau virtuel nommé TestVNet1 et trois sous-réseaux nommés GatewaySubnet, FrontEnd et Backend. Lorsque vous remplacez les valeurs, pensez à toujours nommer votre sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la création de votre passerelle échoue. 
   
    L’exemple suivant utilise les variables définies précédemment. Dans cet exemple, le sous-réseau de passerelle utilise /27. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau plus vaste qui inclut un plus grand nombre d’adresses en sélectionnant au moins /28 ou /27. Cela permettra à un nombre suffisant d’adresses de s’adapter à de possibles configurations supplémentaires possibles que vous êtes susceptible de souhaiter par la suite.

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. Créer TestVNet1

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. Demander une adresse IP publique
   
  Demandez l’allocation d’une adresse IP publique à la passerelle que vous allez créer pour votre réseau virtuel. Notez que la valeur AllocationMethod est dynamique. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est allouée à votre passerelle de façon dynamique. 
   
  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. Créer la configuration de la passerelle
   
    La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple pour créer la configuration de votre passerelle.

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. Créer la passerelle pour TestVNet1
   
    Dans cette étape, vous créez la passerelle de réseau virtuel de votre TestVNet1. Les configurations de réseau virtuel à réseau virtuel requièrent un VPN de type RouteBased. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku Standard
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>Étape 3 : créez et configurez TestVNet4
Une fois que vous avez configuré TestVNet1, créez TestVNet4. Suivez les étapes ci-dessous, en remplaçant les valeurs par les vôtres si nécessaire. Cette étape peut être réalisée dans la même session PowerShell, car elle se trouve dans le même abonnement.

1. Déclarer vos variables
   
    Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $DNS4 = "8.8.8.8"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
   
    Avant de continuer, assurez-vous que vous êtes toujours connecté à l’abonnement 1.
2. Création d’un groupe de ressources

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. Créer les configurations de sous-réseau pour TestVNet4

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. Créer TestVNet4

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. Demander une adresse IP publique

  ```powershell  
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. Créer la configuration de la passerelle

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. Créer la passerelle TestVNet4. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku Standard
  ```

### <a name="step-4---connect-the-gateways"></a>Étape 4 : connectez les passerelles
1. Obtenir les passerelles de réseau virtuel
   
    Dans cet exemple, étant donné que les deux passerelles se trouvent dans le même abonnement, cette étape peut être effectuée dans la même session PowerShell.
   
  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. Créer la connexion TestVNet1 à TestVNet4
   
    Lors de cette étape, vous créez la connexion de TestVNet1 à TestVNet4. Une clé partagée est référencée dans les exemples. Vous pouvez utiliser vos propres valeurs pour cette clé partagée. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.
   
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. Créer la connexion TestVNet4 à TestVNet1
   
    Cette étape est similaire à celle présentée ci-dessus, sauf que vous créez la connexion de TestVNet4 à TestVNet1. Vérifiez que les clés partagées correspondent.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
   
    Après quelques minutes, la connexion doit être établie.
4. Vérifiez votre connexion. Consultez la section [Vérification de votre connexion](#verify).

## <a name="difsub"></a>Connexion de réseaux virtuels situés dans différents abonnements
![Diagramme v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

Dans ce scénario, nous connectons TestVNet1 et TestVNet5. TestVNet1 et TestVNet5 se trouvent dans des abonnements différents. Cette configuration nécessite l’ajout d’une connexion supplémentaire entre réseaux virtuels pour connecter TestVNet1 à TestVNet5. 

La différence réside dans le fait qu’une partie des étapes de configuration doit être exécutée dans une session PowerShell distincte dans le contexte d’un deuxième abonnement, notamment lorsque les deux abonnements appartiennent à différentes organisations. 

Ces instructions sont la suite des étapes précédentes répertoriées plus haut. Vous devez terminer les étapes [1](#Step1) et [2](#Step2) pour créer et configurer TestVNet1 et la passerelle VPN pour TestVNet1. Une fois les étapes 1 et 2 effectuées, passez à l’étape 5 pour créer TestVNet5.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>Étape 5 : vérifiez les plages d’adresses IP supplémentaires
Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel TestVNet5 ne chevauche aucune de vos plages de réseaux virtuels ou de passerelles de réseau locales. 

Dans cet exemple, les réseaux virtuels peuvent appartenir à différentes organisations. Pour cet exercice, vous pouvez utiliser les valeurs suivantes pour TestVNet5 :

**Valeurs pour TestVNet5 :**

* Nom du réseau virtuel : TestVNet5
* Groupe de ressources : TestRG5
* Emplacement : Japon de l’Est
* TestVNet5 : 10.51.0.0/16 et 10.52.0.0/16
* Serveur frontal : 10.51.0.0/24
* Serveur principal : 10.52.0.0/24
* Sous-réseau de passerelle : 10.52.255.0.0/27
* Serveur DNS : 8.8.8.8
* Nom de la passerelle : VNet5GW
* Adresse IP publique : VNet5GWIP
* Type de VPN : RouteBased
* Connexion : VNet5toVNet1
* Type de connexion : VNet2VNet

**Valeurs supplémentaires pour TestVNet1 :**

* Connexion : VNet1toVNet5

### <a name="step-6---create-and-configure-testvnet5"></a>Étape 6 : créez et configurez TestVNet5
Cette étape doit être effectuée dans le cadre du nouvel abonnement. Cette partie peut être effectuée par l’administrateur dans une organisation différente qui possède l’abonnement.

1. Déclarer vos variables
   
    Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $DNS5 = "8.8.8.8"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. Se connecter à l’abonnement 5
   
    Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

  ```powershell
  Login-AzureRmAccount
  ```
   
    Vérifiez les abonnements associés au compte.
   
  ```powershell
  Get-AzureRmSubscription
  ```
   
    Spécifiez l’abonnement que vous souhaitez utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. Création d’un groupe de ressources

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. Créer les configurations de sous-réseau pour TestVNet4

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. Créer TestVNet5

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. Demander une adresse IP publique

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. Créer la configuration de la passerelle

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. Créer la passerelle TestVNet5

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard
  ```

### <a name="step-7---connecting-the-gateways"></a>Étape 7 : connectez les passerelles
Dans cet exemple, étant donné que les passerelles se trouvent dans différents abonnements, nous avons divisé cette étape en deux sessions PowerShell notées [Abonnement 1] et [Abonnement 5].

1. **[Abonnement 1]** Obtenir la passerelle de réseau virtuel pour Abonnement 1
   
    Veillez à ouvrir une session et à vous connecter à Abonnement 1.

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```
   
    Copiez la sortie des éléments suivants et envoyez-la à l’administrateur d’Abonnement 5 par message électronique ou une autre méthode.

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```
   
    Ces deux éléments ont des valeurs similaires à l’exemple de sortie suivant :

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[Abonnement 5]** Obtenir la passerelle de réseau virtuel pour Abonnement 5
   
    Veillez à ouvrir une session et à vous connecter à Abonnement 5.

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```
   
    Copiez la sortie des éléments suivants et envoyez-la à l’administrateur d’Abonnement 1 par message électronique ou une autre méthode.

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```
   
    Ces deux éléments ont des valeurs similaires à l’exemple de sortie suivant :

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[Abonnement 1]** Créer la connexion TestVNet1 à TestVNet5
   
    Dans cette étape, vous créez la connexion de TestVNet1 à TestVNet5. La différence réside dans le fait que $vnet5gw ne peut pas être obtenu directement, car il se trouve dans un abonnement différent. Vous devez créer un objet PowerShell avec les valeurs communiquées par Abonnement 1 dans les étapes précédentes. Utilisez l’exemple ci-dessous. Remplacez le nom, l’ID et la clé partagée par vos propres valeurs. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.
   
    Veillez à vous connecter à Abonnement 1.

  ```powershell
  $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[Abonnement 5]** Créer la connexion TestVNet5 à TestVNet1
   
    Cette étape est similaire à celle présentée ci-dessus, sauf que vous créez la connexion de TestVNet5 à TestVNet1. La procédure de création d’un objet PowerShell basé sur les valeurs obtenues à partir d’Abonnement 1 s’applique également ici. Dans cette étape, vérifiez que les clés partagées correspondent.
   
    Veillez à vous connecter à Abonnement 5.

  ```powershell
  $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>Vérification d’une connexion
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

### <a name="faq"></a>Interconnexion de réseaux virtuels
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .
* Pour plus d’informations sur le protocole BGP, consultez les articles [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [Comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md). 


