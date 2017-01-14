---
title: "Connexion de réseaux virtuels classiques aux réseaux virtuels Resource Manager dans le portail | Microsoft Docs"
description: "Découvrez comment créer une connexion VPN entre des réseaux virtuels classiques et des réseaux virtuels Resource Manager à l’aide d’une passerelle VPN et du portail"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 2c6ffb6c175612c57ddff3a71b2c7a1855c52348


---
# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Connecter des réseaux virtuels utilisant des modèles de déploiement différents dans le portail
> [!div class="op_single_selector"]
> * [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure propose actuellement deux modèles de gestion : le modèle classique et le modèle Resource Manager (RM). Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles et des rôles d’instance Azure exécutés dans un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s’exécutent dans un réseau virtuel créé dans Resource Manager. Cet article vous guide dans le processus de connexion de réseaux virtuels classiques à des réseaux virtuels Resource Manager afin de permettre aux ressources situées dans les modèles de déploiement distincts de communiquer entre elles via une connexion de passerelle.

Vous pouvez créer une connexion entre des réseaux virtuels situés dans des abonnements différents et des régions différentes. Vous pouvez également connecter des réseaux virtuels qui disposent déjà de connexions à des réseaux locaux, à condition que la passerelle avec laquelle ils ont été configurés soit dynamique ou basée sur un itinéraire. Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez le [Forum Aux Questions sur l’interconnexion de réseaux virtuels](#faq) à la fin de cet article.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles et méthodes de déploiement pour les connexions de réseau virtuel à réseau virtuel
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Nous mettons à jour le tableau suivant à mesure que de nouveaux articles et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.<br><br>

**Connexion entre deux réseaux virtuels**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet Peering**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Avant tout chose
Les étapes suivantes vous guident à travers les paramétrages nécessaires pour configurer une passerelle dynamique ou basée sur un itinéraire pour chaque réseau virtuel et créer une connexion VPN entre les passerelles. Cette configuration ne prend pas en charge les passerelles statiques ou basées sur des stratégies. 

Dans cet article, nous utilisons le portail Classic, le portail Azure et PowerShell. À l’heure actuelle, il n’est pas possible de créer cette configuration uniquement à l’aide du portail Azure.

### <a name="prerequisites"></a>Composants requis
* Les deux réseaux virtuels ont déjà été créés.
* Les plages d’adresses des réseaux virtuels ne se chevauchent pas ou ne chevauchent aucune des plages des autres connexions susceptibles d’être utilisées par les passerelles.
* Vous avez installé les dernières applets de commande PowerShell (1.0.2 ou version ultérieure). Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) . Veillez à installer à la fois les applets de commande de gestion des services et Resource Manager (RM). 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Exemples de paramètres
Vous pouvez utiliser les exemples de paramètres en tant que référence.

**Paramètres de réseau virtuel classique**

Nom du réseau virtuel = ClassicVNet  <br>
Emplacement = Ouest des États-Unis <br>
Espaces d’adressage du réseau virtuel = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
Sous-réseau de passerelle : 10.0.0.32/29 <br>
Nom du réseau local = RMVNetLocal <br>

**Paramètres de réseau virtuel Resource Manager**

Nom du réseau virtuel = RMVNet  <br>
Groupe de ressources = RG1 <br>
Espaces d’adressage IP du réseau virtuel = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
Sous-réseau de passerelle = 192.168.0.0/26 <br>
Emplacement = Est des États-Unis <br>
Nom de passerelle de réseau virtuel = RMGateway <br>
Nom d’adresse IP publique de passerelle = gwpip <br>
Type de passerelle = VPN <br>
Type de VPN = Route-based <br>
Passerelle de réseau local = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>Section 1 : Configurer les paramètres du réseau virtuel classique
Dans cette section, nous créons le réseau local et la passerelle pour votre réseau virtuel classique. Pour ce faire, vous devez utiliser le portail Classic. À l’heure actuelle, le portail Azure n’offre pas tous les paramètres associés à un réseau virtuel classique.

### <a name="part-1---create-a-new-local-network"></a>Partie 1 : Créer un réseau local
Ouvrez le [portail Classic](https://manage.windowsazure.com) et connectez-vous avec votre compte Azure.

1. En bas à gauche de l’écran, cliquez sur **NOUVEAU** > **Network Services** > **Réseau virtuel** > **Ajouter un réseau local**.
2. Dans la fenêtre **Spécifier les détails de votre réseau local** , tapez un nom pour le réseau virtuel RM auquel vous souhaitez vous connecter. Dans la zone de texte **Adresse IP de l’appareil VPN (facultatif)** , tapez une adresse IP publique valide. Il s’agit simplement d’une valeur temporaire. Vous modifierez cette adresse IP ultérieurement. En bas à gauche de la fenêtre, cliquez sur le bouton fléché.
3. Dans la page **Spécifier l’espace d’adressage**, dans la zone de texte **Adresse IP de départ**, tapez le préfixe réseau et le bloc CIDR du réseau virtuel Resource Manager auquel vous souhaitez vous connecter. Ce paramètre sert à spécifier l’espace d’adressage à router vers le réseau virtuel RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Partie 2 : Associer le réseau local à votre réseau virtuel
1. En haut de la page, cliquez sur **Réseaux virtuels** pour passer à l’écran Réseaux virtuels, puis cliquez sur votre réseau virtuel classique pour le sélectionner. Dans la page de votre réseau virtuel, cliquez sur **Configurer** pour accéder à la page de configuration.
2. Sous la section **Connectivité de site à site**, sélectionnez la case à cocher **Se connecter au réseau local**. Sélectionnez ensuite le réseau local que vous avez créé. Si vous avez créé plusieurs réseaux locaux, veillez à sélectionner celui que vous avez créé pour représenter votre réseau virtuel Resource Manager dans la liste déroulante.
3. Cliquez sur **Enregistrer** au bas de la page.

### <a name="part-3---create-the-gateway"></a>Partie 3 : Créer la passerelle
1. Après avoir enregistré les paramètres, cliquez sur **Tableau de bord** en haut de la page pour passer à la page Tableau de bord. En bas de la page Tableau de bord, cliquez sur **Créer la passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** pour lancer la création de votre passerelle. Une passerelle de routage dynamique est requise pour cette configuration.
2. Attendez que la passerelle soit créée. Cette opération peut parfois prendre 45 minutes ou plus.

### <a name="a-nameipapart-4---view-the-gateway-public-ip-address"></a><a name="ip"></a>Partie 4 : Afficher l’adresse IP publique de la passerelle
Une fois la passerelle créée, vous pouvez afficher l’adresse IP de la passerelle dans la page **Tableau de bord** . Il s’agit de l’adresse IP publique de votre passerelle. Notez ou copiez l’adresse IP publique. Elle vous servira ultérieurement lors de la création du réseau local pour la configuration de votre réseau virtuel Resource Manager.

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>Section 2 : Configurer les paramètres du réseau virtuel Resource Manager
Dans cette section, nous créons la passerelle de réseau virtuel et le réseau local pour votre réseau virtuel Resource Manager. Ne commencez pas la procédure suivante avant d’avoir récupéré l’adresse IP publique de la passerelle du réseau virtuel classique.

Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Partie 1 : Créer un sous-réseau de passerelle
Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Créez un sous-réseau de passerelle avec un nombre CIDR de /28 ou plus (/27, /26, etc.).

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Partie 2 : Créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="part-3---create-a-local-network-gateway"></a>Partie 3 : Créer une passerelle de réseau local
La « passerelle de réseau local » fait généralement référence à votre emplacement local. Elle indique à Azure la plage d’adresses IP à router vers l’emplacement et l’adresse IP publique de l’appareil pour cet emplacement. Cependant, dans ce cas, elle fait référence à la plage d’adresses et à l’adresse IP publique associées à votre réseau virtuel classique et à la passerelle de réseau virtuel.

Donnez à la passerelle de réseau local un nom qui sera utilisé par Azure pour la référencer. Vous pouvez créer votre passerelle de réseau local pendant la création de votre passerelle de réseau virtuel. Pour cette configuration, vous utilisez l’adresse IP publique affectée à votre passerelle de réseau virtuel classique dans la [section précédente](#ip).

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

### <a name="part-4---copy-the-public-ip-address"></a>Partie 4 : Copier l’adresse IP publique
Une fois la création de la passerelle de réseau virtuel terminée, copiez l’adresse IP publique associée à la passerelle. Elle vous servira lors de la configuration des paramètres de réseau local pour votre réseau virtuel classique. 

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Section 3 : Modifier le réseau local pour le réseau virtuel classique
Ouvrez le [portail Classic](https://manage.windowsazure.com).

1. Dans le portail Classic, faites défiler la page vers le bas sur le côté gauche et cliquez sur **Réseaux**. En haut de la page **Réseaux**, cliquez sur **Réseaux locaux**. 
2. Cliquez sur le réseau local que vous avez configuré dans la partie 1 pour le sélectionner. En bas de la page, cliquez sur **Modifier**.
3. Dans la page **Spécifier les détails de votre réseau local** , remplacez l’adresse IP temporaire par l’adresse IP publique de la passerelle Resource Manager que vous avez créée dans la section précédente. Cliquez sur la flèche pour passer à la section suivante. Vérifiez que **l’espace d’adressage** est correct, puis cliquez sur la coche pour accepter les modifications.

## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>Section 4 : Créer la connexion
Dans cette section, nous créons la connexion entre les réseaux virtuels. Pour ce faire, vous devez utiliser PowerShell. Vous ne pouvez pas créer cette connexion dans l’un ou l’autre des portails. Assurez-vous que vous avez téléchargé et installé les applets de commande PowerShell classiques et Resource Manager (RM).

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Les applets de commande suivantes vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés pour être reconnus par Azure PowerShell.
   
        Login-AzureRmAccount 
   
     Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.
   
        Get-AzureRmSubscription
   
    Spécifiez l’abonnement que vous souhaitez utiliser. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. Ajoutez votre compte Azure pour utiliser les applets de commande PowerShell classiques. Pour ce faire, vous pouvez utiliser la commande suivante :
   
        Add-AzureAccount
3. Définissez votre clé partagée en exécutant l’exemple suivant. Dans cet exemple, `-VNetName` est le nom du réseau virtuel classique et `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le réseau local lors de sa configuration dans le portail Classic. `-SharedKey` est une valeur que vous pouvez générer et spécifier. La valeur que vous spécifiez ici doit être la même que celle spécifiée à l’étape suivante lors de la création de votre connexion.
   
        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
4. Créez la connexion VPN en exécutant les commandes suivantes :
   
    **Définir les variables**
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
    **Créer la connexion**<br> Notez que `-ConnectionType` est défini sur « IPsec » et non pas sur « Vnet2Vnet ». Dans cet exemple, `-Name` est le nom que vous souhaitez donner à votre connexion. L’exemple suivant crée une connexion nommée*rm-to-classic-connection*.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Vérifier votre connexion
Vous pouvez vérifier votre connexion à l’aide du portail Classic, du portail Azure ou de PowerShell. Vous pouvez utiliser les étapes suivantes pour vérifier votre connexion. Remplacez les valeurs par les vôtres.

[!INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-faq"></a><a name="faq"></a>Forum Aux Questions sur l’interconnexion de réseaux virtuels
Consultez les détails du Forum Aux Questions pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Dec16_HO1-->


