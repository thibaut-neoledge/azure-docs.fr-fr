---
title: "Connecter des réseaux virtuels classiques à des réseaux virtuels Resource Manager : Portail | Microsoft Docs"
description: "Découvrez comment créer une connexion VPN entre des réseaux virtuels classiques et des réseaux virtuels Resource Manager à l’aide d’une passerelle VPN et du portail"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: 3d12aaf19326c45cba1a214f1d68f41f0db8ccf6
ms.lasthandoff: 04/05/2017


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Connecter des réseaux virtuels utilisant des modèles de déploiement différents dans le portail
> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure propose actuellement deux modèles de gestion : le modèle classique et le modèle Resource Manager. Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles et des rôles d’instance Azure exécutés dans un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s’exécutent dans un réseau virtuel créé dans Resource Manager. Cet article vous aide à créer une connexion de passerelle VPN via un tunnel VPN IPsec/IKE entre les réseaux virtuels.

Pour plus d’informations sur les connexions entre deux réseaux virtuels, consultez la partie [Interconnexion de réseaux virtuels](#faq) à la fin de cet article. Vous pouvez également consulter l’article [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). L’homologation de réseaux virtuels est une autre façon de créer une connexion entre vos réseaux virtuels et présente des avantages de connexion pour certaines configurations.

### <a name="prerequisites"></a>Composants requis

* Ces étapes supposent que les deux réseaux virtuels ont déjà été créés. Si vous utilisez cet article en guise d’exercice et que vous ne disposez pas de réseaux virtuels, vous trouverez des liens dans les étapes pour vous aider à les créer.
* Vérifiez que les plages d’adresses des réseaux virtuels ne se chevauchent pas ou ne chevauchent aucune des plages des autres connexions susceptibles d’être utilisées par les passerelles.
* Installez les dernières applets de commande PowerShell pour le Gestionnaire de ressources et pour la Gestion des services (classique). Dans cet article, nous utilisons le portail Azure et PowerShell. PowerShell est requis pour créer la connexion à partir du réseau virtuel classique vers le réseau virtuel Resource Manager. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). 

### <a name="values"></a>Exemples de paramètres
Vous pouvez utiliser les exemples de paramètres comme référence, ou vous pouvez les utiliser pour créer une configuration de test.

**Réseau virtuel classique**

Nom du réseau virtuel = ClassicVNet <br>
Espace d’adressage = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
Groupe de ressources = ClassicRG <br>
Emplacement = Ouest des États-Unis <br>
Sous-réseau de passerelle = 10.0.0.32/28 <br>
Site local = RMVNetLocal <br>

**Réseau virtuel Resource Manager**

Nom du réseau virtuel = RMVNet <br>
Espace d’adressage = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
Sous-réseau de passerelle = 192.168.0.0/26 <br>
Groupe de ressources = RG1 <br>
Emplacement = Est des États-Unis <br>
Nom de passerelle de réseau virtuel = RMGateway <br>
Type de passerelle = VPN <br>
Type de VPN = Route-based <br>
Nom de l’adresse IP publique de la passerelle = rmgwpip <br>
Passerelle de réseau local = ClassicVNetLocal <br>
Nom de connexion = RMtoClassic

### <a name="connection-overview"></a>Vue d’ensemble de la connexion

Pour cette configuration, vous créez une connexion de passerelle VPN via un tunnel VPN IPsec/IKE entre les réseaux virtuels. Assurez-vous que vos plages de réseau virtuel ne se chevauchent pas entre elles ou avec un réseau local auquel elles se connectent.

Le tableau suivant montre comment les réseaux virtuels et les sites locaux sont définis :

| Réseau virtuel | Espace d'adressage | Région | Se connecte au site de réseau local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Ouest des États-Unis | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Est des États-Unis |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>1. Configurer les paramètres de réseau virtuel classique
Dans cette section, vous créez le réseau local (site local) et la passerelle de réseau virtuel pour votre réseau virtuel classique. Si vous n’avez pas de réseau virtuel classique et que vous exécutez ces étapes en guise d’exercice, vous pouvez créer un réseau virtuel à l’aide de [cet article](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) et les valeurs de paramètres [d’exemple](#values) ci-dessus. Si vous disposez déjà d’un réseau virtuel avec une passerelle VPN, vérifiez que la passerelle est dynamique. Si elle est statique, vous devez tout d’abord supprimer la passerelle VPN avant de poursuivre.

Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres,ou utilisez les valeurs [d’exemple](#values).

### <a name="part-1---configure-the-local-site"></a>Partie 1 : Configurer le site local

Ouvrez le [portail Azure](https://ms.portal.azure.com) et connectez-vous avec votre compte Azure.

1. Accédez à **Toutes les ressources** et recherchez le **réseau virtuel classique** dans la liste.
2. Dans le panneau **Vue d’ensemble**, dans la section **Connexions VPN**, cliquez sur le graphique **Passerelle** pour créer une passerelle.

    ![Configurer une passerelle VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurer une passerelle VPN")
3. Dans le panneau **Nouvelle connexion VPN**, pour **Type de connexion**, sélectionnez **De site à site**.
4. Sous **Site local**, cliquez sur **Configurer les paramètres requis**. Cette opération ouvre le panneau **Site local**.
5. Dans le panneau **Site local**, créez un nom pour faire référence au réseau virtuel Resource Manager. Par exemple, « RMVNetLocal ».
6. Si la passerelle VPN pour le réseau virtuel Resource Manager a déjà une adresse IP publique, utilisez cette valeur pour le champ **Adresse IP de passerelle VPN**. Si vous effectuez ces étapes en guise d’exercice, ou si ne disposez pas encore dune passerelle de réseau virtuel pour votre réseau virtuel Resource Manager, vous pouvez créer une adresse IP d’espace réservé. Assurez-vous que l’adresse IP avec espace réservé utilise un format valide. Remplacez ensuite l’adresse IP avec espace réservé par l’adresse IP publique de la passerelle de réseau virtuel Resource Manager.
7. Pour **Espace d’adressage du client**, utilisez les valeurs des espaces d’adressage IP de réseau virtuel pour le réseau virtuel Resource Manager. Ce paramètre sert à spécifier les espaces d’adressage à router vers le réseau virtuel Resource Manager.
8. Cliquez sur **OK** pour enregistrer les valeurs et revenez au panneau **Nouvelle connexion VPN**.

### <a name="part-2---create-the-virtual-network-gateway"></a>Partie 2 : Créer la passerelle de réseau virtuel
1. Dans le panneau **Nouvelle connexion VPN**, sélectionnez la case à cocher **Créer une passerelle immédiatement** et cliquez sur **Configuration de passerelle facultative** pour ouvrir le panneau **Configuration de la passerelle**. 

    ![Ouvrir le panneau Configuration de la passerelle](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Ouvrir le panneau Configuration de la passerelle")
2. Cliquez sur **Subnet Configure required settings** (Configurer les paramètres requis pour le sous-réseau) pour ouvrir le panneau **Add subnet** (Ajouter un sous-réseau). Le **Nom** est déjà configuré avec la valeur requise **GatewaySubnet**.
3. La **Plage d’adresses** fait référence à la plage pour le sous-réseau de passerelle. Bien que vous puissiez créer un sous-réseau de passerelle avec une plage d’adresses /29 (3 adresses), nous vous recommandons de créer un sous-réseau de passerelle qui contient plus d’adresses IP. Cela afin d’accueillir des configurations futures nécessitant la disponibilité d’un plus grand nombre d’adresses IP. Si possible, utilisez /27 ou /28. Si vous suivez ces étapes dans le cadre d’un exercice, vous pouvez vous référer aux valeurs [d’exemple](#values). Cliquez sur **OK** pour créer le sous réseau de passerelle.
4. Dans le panneau **Configuration de la passerelle**, le paramètre **Taille** désigne la référence SKU de passerelle. Sélectionnez la référence SKU de passerelle pour votre passerelle VPN.
5. Vérifiez que le **Type de routage** est **Dynamique**, puis cliquez sur **OK** pour revenir au panneau **Nouvelle connexion VPN**.
6. Dans le panneau **Nouvelle connexion VPN**, cliquez sur **OK** pour commencer à créer votre passerelle VPN. La création d’une passerelle VPN peut prendre jusqu’à 45 minutes.

### <a name="ip"></a>Partie 3 : Copier l’adresse IP publique de passerelle de réseau virtuel
Une fois la passerelle de réseau virtuel créée, vous pouvez afficher l’adresse IP de la passerelle. 

1. Accédez à votre réseau virtuel classique, puis cliquez sur **Vue d’ensemble**.
2. Cliquez sur **Connexions VPN** afin d’ouvrir le panneau Connexions VPN. Dans le panneau Connexions VPN, vous pouvez afficher l’adresse IP publique. Il s’agit de l’adresse IP publique affectée à votre passerelle de réseau virtuel. 
3. Notez ou copiez l’adresse IP. Vous en aurez besoin aux étapes suivantes pour vos paramètres de configuration de passerelle de réseau local Resource Manager. Vous pouvez également afficher l’état de vos connexions à la passerelle. Le site de réseau local que vous avez créé est répertorié comme « Connexion ». Le statut sera modifié une fois les connexions créées.
4. Fermez le panneau après avoir copié l’adresse IP de passerelle.

## <a name="rmvnet"></a>2. Configurer les paramètres du réseau virtuel Resource Manager

Dans cette section, vous créez la passerelle de réseau virtuel et la passerelle de réseau local pour votre réseau virtuel Resource Manager. Si vous n’avez pas de réseau virtuel Resource Manager et que vous exécutez ces étapes en guise d’exercice, vous pouvez créer un réseau virtuel à l’aide de [cet article](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) et les valeurs de paramètres [d’exemple](#values) ci-dessus.

Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres,ou utilisez les valeurs [d’exemple](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Partie 1 : Créer un sous-réseau de passerelle
Vous devez d’abord créer un sous-réseau de passerelle pour pouvoir configurer une passerelle de réseau virtuel. Créez un sous-réseau de passerelle avec un nombre CIDR de /28 ou plus. (/27, /26, etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]


[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Partie 2 : Créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>Partie 3 : Créer une passerelle de réseau local

La passerelle de réseau local spécifie la plage d’adresses et l’adresse IP publique associées à votre réseau virtuel classique et à la passerelle de réseau virtuel.

Si vous effectuez ces étapes en guise d’exercice, consultez ces paramètres :

| Réseau virtuel | Espace d'adressage | Région | Se connecte au site de réseau local |Adresse IP publique de la passerelle|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Ouest des États-Unis | RMVNetLocal (192.168.0.0/16) |L’adresse IP publique qui est affectée à la passerelle ClassicVNet|
| RMVNet | (192.168.0.0/16) |Est des États-Unis |ClassicVNetLocal (10.0.0.0/24) |L’adresse IP publique qui est affectée à la passerelle RMVNet.|

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>3. Modifier les paramètres de site local du réseau virtuel classique

Dans cette section, vous remplacez l’adresse IP avec espace réservé que vous avez utilisée lorsque vous avez spécifié les paramètres du site local avec l’adresse IP de passerelle de réseau virtuel Resource Manager. Cette section utilise les applets de commande PowerShell (SM) classiques.

1. Dans le portail Azure, accédez au réseau virtuel classique.
2. Dans le panneau de votre réseau virtuel, cliquez sur **Vue d’ensemble**.
3. Dans la section **Connexions VPN**, cliquez sur le nom de votre site local dans le graphique.

    ![Connexions VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Connexions VPN")
4. Dans le panneau **Connexions VPN de site à site**, cliquez sur le nom du site.

    ![Nom du site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nom du site local")
5. Dans le volet de connexion pour votre site local, cliquez sur le nom du site local pour ouvrir le panneau **Site local**.

    ![Ouvrir le site local](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Ouvrir le site local")
6. Dans le panneau **Site local**, remplacez **l’adresse IP de passerelle VPN** avec l’adresse IP de la passerelle Resource Manager.

    ![Adresse IP de la passerelle](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Adresse IP de la passerelle")
7. Cliquez sur **OK** pour mettre à jour l’adresse IP.

## <a name="RMtoclassic"></a>4. Créer une connexion entre Resource Manager et classique

Dans cette procédure, vous configurez la connexion entre le réseau virtuel Resource Manager et le réseau virtuel classique à l’aide du portail Azure.

1. Dans **Toutes les ressources**, recherchez la passerelle de réseau local. Dans notre exemple, la passerelle de réseau local est **ClassicVNetLocal**.
2. Cliquez sur **Configuration** et vérifiez que la valeur d’adresse IP est la passerelle VPN pour le réseau virtuel classique. Mettez à jour si nécessaire, puis cliquez sur **Enregistrer**. Fermez le panneau.
3. Dans **Toutes les ressources**, cliquez sur la passerelle de réseau local.
4. Cliquez sur **Connexions** afin d’ouvrir le panneau Connexions.
5. Dans le panneau **Connexions**, cliquez sur **+** pour ajouter une connexion.
6. Dans le panneau **Ajouter une connexion**, donnez un nom à la connexion. Par exemple, « RMtoClassic ».
7. **Site à site** est déjà sélectionnée sur ce panneau.
8. Sélectionnez la passerelle de réseau virtuel que vous souhaitez associer à ce site.
9. Créer une **clé partagée**. Cette clé est également utilisée dans la connexion que vous créez entre le réseau virtuel classique et le réseau virtuel Resource Manager. Vous pouvez générer la clé ou en créer une. Dans l’exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe.
10. Cliquez sur **OK** pour créer la connexion.

##<a name="classictoRM"></a>5. Créer une connexion entre classique et Resource Manager

Dans cette procédure, vous configurez la connexion entre le réseau virtuel Resource Manager et le réseau virtuel classique à l’aide du portail Azure. Pour ce faire, vous devez utiliser PowerShell. Vous ne pouvez pas créer cette connexion dans le portail. Assurez-vous que vous avez téléchargé et installé les applets de commande PowerShell classiques et Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Se connecter au compte Azure

Ouvrez la console PowerShell avec des droits élevés et connectez-vous à votre compte Azure. Les applets de commande suivantes vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés pour être reconnus par Azure PowerShell.
   
    Login-AzureRmAccount 
   
Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.
   
    Get-AzureRmSubscription
   
Spécifiez l’abonnement que vous souhaitez utiliser. 
   
    Select-AzureRmSubscription -SubscriptionName "Name of subscription"

Ajoutez votre compte Azure pour utiliser les applets de commande PowerShell classiques. Pour ce faire, vous pouvez utiliser la commande suivante :
   
    Add-AzureAccount

### <a name="2-view-the-network-configuration-file-values"></a>2. Afficher le fichier de configuration réseau

Lorsque vous créez un réseau virtuel dans le portail Azure, le nom complet utilisé par Azure n’est pas visible dans le portail Azure. Par exemple, un réseau virtuel qui semble être nommé « ClassicVNet1 » dans le portail Azure peut avoir un nom beaucoup plus long dans le fichier de configuration réseau. Le nom peut ressembler à ceci : « Group ClassicRG1 ClassicVNet1 ». Dans ces étapes, vous téléchargez le fichier de configuration réseau et affichez les valeurs.

Créez un répertoire sur votre ordinateur, puis exportez le fichier de configuration réseau dans ce répertoire. Dans cet exemple, le fichier de configuration réseau est exporté vers C:\AzureNet.

    Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

Dans un éditeur de texte, ouvrez le fichier, puis affichez le nom de votre réseau virtuel classique. Utilisez les noms indiqués dans le fichier de configuration réseau lors de l’exécution de vos applets de commande PowerShell.

- Les noms des réseaux virtuels sont répertoriés comme suit : **VirtualNetworkSite name =**
- Les noms de site sont répertoriés comme suit : **LocalNetworkSite name=**

### <a name="3-create-the-connection"></a>3. Créer la connexion

Configurez la clé partagée et créez la connexion à partir du réseau virtuel classique vers le réseau virtuel Resource Manager.

- Dans cet exemple, **-VNetName** est le nom du réseau virtuel classique, comme indiqué dans votre fichier de configuration réseau. 
- **-LocalNetworkSiteName** est le nom que vous avez spécifié pour le site local, comme indiqué dans votre fichier de configuration réseau.
- **-SharedKey** est une valeur que vous pouvez générer et spécifier. Dans l’exemple, nous avons utilisé *abc123*, mais vous pouvez générer quelque chose de plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la création de la connexion entre Resource Manager et classique.

        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123

##<a name="verify"></a>6. Vérifiez vos connexions
Vous pouvez vérifier vos connexions à l’aide du portail Azure ou de PowerShell. Lors de la vérification, vous devrez peut-être patienter quelques minutes, le temps que la connexion soit créée. Lorsqu’une connexion est réussie, l’état de connectivité passe de « Connexion » à « Connecté ».

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Pour vérifier la connexion de votre réseau virtuel classique à votre réseau virtuel Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Pour vérifier la connexion de votre réseau virtuel Resource Manager à votre réseau virtuel classique

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Interconnexion de réseaux virtuels

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]
