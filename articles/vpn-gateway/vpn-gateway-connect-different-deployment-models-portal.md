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
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Connecter des réseaux virtuels utilisant des modèles de déploiement différents dans le portail
> [!div class="op_single_selector"]
> * [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure propose actuellement deux modèles de gestion : le modèle classique et le modèle Resource Manager (RM). Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles et des rôles d’instance Azure exécutés dans un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s’exécutent dans un réseau virtuel créé dans Resource Manager. Cet article vous guide dans le processus de connexion de réseaux virtuels classiques à des réseaux virtuels Resource Manager afin de permettre aux ressources situées dans les modèles de déploiement distincts de communiquer entre elles via une connexion de passerelle. 

Vous pouvez créer une connexion entre des réseaux virtuels situés dans des abonnements différents et des régions différentes. Vous pouvez également connecter des réseaux virtuels qui disposent déjà de connexions à des réseaux locaux, à condition que la passerelle avec laquelle ils ont été configurés soit dynamique ou basée sur un itinéraire. Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez la section [Interconnexion de réseaux virtuels](#faq) à la fin de cet article.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles et méthodes de déploiement pour les connexions de réseau virtuel à réseau virtuel
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Nous mettons à jour le tableau suivant à mesure que de nouveaux articles et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.<br><br>

**Connexion entre deux réseaux virtuels**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet Peering**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>Avant tout chose
Les étapes suivantes vous guident à travers les paramétrages nécessaires pour configurer une passerelle dynamique ou basée sur un itinéraire pour chaque réseau virtuel et créer une connexion VPN entre les passerelles. Cette configuration ne prend pas en charge les passerelles statiques ou basées sur des stratégies. 

Dans cet article, nous utilisons le portail Azure et PowerShell. PowerShell est requis pour créer les connexions entre les réseaux virtuels. Impossible de créer les connexions pour cette configuration à l’aide du portail.

### <a name="prerequisites"></a>Composants requis
* Les deux réseaux virtuels ont déjà été créés.
* Les plages d’adresses des réseaux virtuels ne se chevauchent pas ou ne chevauchent aucune des plages des autres connexions susceptibles d’être utilisées par les passerelles.
* Vous avez installé les dernières applets de commande PowerShell. Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) . Veillez à installer à la fois les applets de commande de gestion des services et Resource Manager (RM). 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Exemples de paramètres
Vous pouvez utiliser les exemples de paramètres en tant que référence.

**Paramètres de réseau virtuel classique**

Nom du réseau virtuel = ClassicVNet  <br>
Espace d’adressage = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
Groupe de ressources = ClassicRG <br>
Emplacement = Ouest des États-Unis <br>
Sous-réseau de passerelle = 10.0.0.32/28 <br>
Site local = RMVNetLocal <br>

**Paramètres de réseau virtuel Resource Manager**

Nom du réseau virtuel = RMVNet  <br>
Espace d’adressage = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
Sous-réseau de passerelle = 192.168.0.0/26 <br>
Groupe de ressources = RG1 <br>
Emplacement = Est des États-Unis <br>
Nom de passerelle de réseau virtuel = RMGateway <br>
Type de passerelle = VPN <br>
Type de VPN = Route-based <br>
Nom d’adresse IP publique de passerelle = gwpip <br>
Passerelle de réseau local = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>Section 1 : Configurer les paramètres du réseau virtuel classique
Dans cette section, nous créons le réseau local (site local) et la passerelle de réseau virtuel pour votre réseau virtuel classique. Pour ce faire, vous devez utiliser le portail Azure. Les étapes supposent qu’aucune passerelle VPN n’a été créée pour le réseau virtuel classique. Si vous disposez déjà d’une passerelle, vérifiez qu’il s’agit d’une passerelle dynamique. Si elle est statique, vous devez tout d’abord supprimer la passerelle VPN, puis effectuer les étapes suivantes.

### <a name="part-1---configure-the-local-site"></a>Partie 1 : Configurer le site local

Ouvrez le [portail Azure](https://ms.portal.azure.com) et connectez-vous avec votre compte Azure.

1. Accédez à **Toutes les ressources** et recherchez le réseau virtuel classique.
2. Dans le panneau **Vue d’ensemble**, dans la section **Connexions VPN**, cliquez sur le graphique **Passerelle** pour créer une passerelle.

    ![Configurer une passerelle VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurer une passerelle VPN")
3. Dans le panneau **Nouvelle connexion VPN**, pour **Type de connexion**, sélectionnez **De site à site**.
4. Sous **Site local**, cliquez sur **Configurer les paramètres requis**. Cette action ouvre le panneau **Site local**.
5. Dans le panneau **Site Local**, créez un nom que vous utiliserez pour faire référence au réseau virtuel Resource Manager. Par exemple, RMVNetLocal.
6. Si la passerelle VPN pour le réseau virtuel Resource Manager a déjà une adresse IP publique, utilisez cette valeur pour le champ **Adresse IP de passerelle VPN**. Si vous n’avez pas encore créé de passerelle de réseau virtuel pour votre réseau virtuel Resource Manager, vous pouvez utiliser une adresse IP avec espace réservé. Assurez-vous que l’adresse IP avec espace réservé utilise un format valide. Remplacez ensuite l’adresse IP avec espace réservé par l’adresse IP publique de la passerelle de réseau virtuel Resource Manager.
7. Pour **Espace d’adressage du client**, utilisez les valeurs des espaces d’adressage IP de réseau virtuel pour le réseau virtuel Resource Manager. Ce paramètre sert à spécifier les espaces d’adressage à router vers le réseau virtuel RM.
8. Cliquez sur **OK** pour enregistrer les valeurs et revenez au panneau **Nouvelle connexion VPN**.

### <a name="part-2---create-the-virtual-network-gateway"></a>Partie 2 : Créer la passerelle de réseau virtuel
1. Dans le panneau **Nouvelle connexion VPN**, sélectionnez la case à cocher **Créer une passerelle immédiatement** et cliquez sur **Configuration de passerelle facultative** pour ouvrir le panneau **Configuration de la passerelle**. 

    ![Ouvrir le panneau Configuration de la passerelle](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Ouvrir le panneau Configuration de la passerelle")
2. Cliquez sur **Subnet Configure required settings** (Configurer les paramètres requis pour le sous-réseau) pour ouvrir le panneau **Add subnet** (Ajouter un sous-réseau). Dans ce panneau, vous verrez que le nom est déjà configuré avec la valeur requise **GatewaySubnet**.
3. Le **Plage d’adresses** fait référence à la plage pour le sous-réseau de passerelle. Bien que vous puissiez créer un sous-réseau de passerelle avec une plage d’adresses /29 (3 adresses), nous vous recommandons de créer un sous-réseau de passerelle contenant davantage d’adresses IP disponibles afin de prendre en charge les futures configurations nécessitant ces adresse supplémentaires. Si possible, utilisez /27 ou /28. Cliquez sur **OK** pour créer le sous réseau de passerelle.
4. Dans le panneau **Configuration de la passerelle**, le paramètre **Taille** désigne la référence SKU de passerelle. Sélectionnez la référence SKU de passerelle pour votre passerelle VPN.
5. Vérifiez que le **Type de routage** est **Dynamique**, puis cliquez sur **OK** pour revenir au panneau **Nouvelle connexion VPN**.
6. Dans le panneau **Nouvelle connexion VPN**, cliquez sur **OK** pour commencer à créer votre passerelle VPN. Cette opération peut prendre jusqu’à 45 minutes.


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>Partie 3 : Copier l’adresse IP publique de passerelle de réseau virtuel
Une fois la passerelle de réseau virtuel créée, vous pouvez afficher l’adresse IP de la passerelle. 

1. Accédez à votre réseau virtuel classique, puis cliquez sur **Vue d’ensemble**.
2. Cliquez sur **Connexions VPN** afin d’ouvrir le panneau Connexions VPN. Dans le panneau Connexions VPN, vous pouvez afficher l’adresse IP publique. Il s’agit de l’adresse IP publique affectée à votre passerelle de réseau virtuel. Notez ou copiez l’adresse IP. Vous en aurez besoin aux étapes suivantes pour vos paramètres de configuration de passerelle de réseau local Resource Manager. Vous pouvez également afficher l’état de vos connexions à la passerelle. Le site de réseau local que vous avez créé est répertorié comme « Connexion ». Le statut sera modifié une fois les connexions créées. Fermez le panneau après avoir copié l’adresse IP de passerelle.

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>Section 2 : Configurer les paramètres du réseau virtuel Resource Manager
Dans cette section, nous créons la passerelle de réseau virtuel et le réseau local pour votre réseau virtuel Resource Manager. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Partie 1 : Créer un sous-réseau de passerelle
Vous devez d’abord créer un sous-réseau de passerelle pour pouvoir configurer une passerelle de réseau virtuel. Créez un sous-réseau de passerelle avec un nombre CIDR de /28 ou plus. (/27, /26, etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Partie 2 : Créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

La création d’une passerelle de réseau virtuel peut prendre jusqu’à 45 minutes. Vous pouvez attendre que la création de la passerelle de réseau virtuel Resource Manager se termine, ou vous pouvez passer à la [Partie 3 : Créer une passerelle de réseau local](#createlng). Une fois la passerelle créée, une adresse IP publique lui est assignée. Dans les étapes suivantes, cette adresse IP est utilisée pour remplacer les informations sur l’adresse IP avec espace réservé pour les paramètres de site local de réseau virtuel classiques que vous avez créés à la Section 1. 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>Partie 3 : Créer une passerelle de réseau local

La passerelle de réseau local spécifie la plage d’adresses et l’adresse IP publique associées à votre réseau virtuel classique et à la passerelle de réseau virtuel.

- Utilisez l’adresse IP publique affectée à votre passerelle de réseau virtuel classique dans la [section précédente](#ip). 
- Donnez à la passerelle de réseau local un nom qui sera utilisé par Azure pour la référencer. Par exemple, « ClassicVNetLocal ».
- Utilisez l’espace d’adressage que vous avez affecté à votre réseau virtuel classique (et pas seulement le sous-réseau).

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>Section 3 : Modifier les paramètres de site local du réseau virtuel classique

Dans cette section, vous allez travailler avec le réseau virtuel classique. Vous allez remplacer l’adresse IP avec espace réservé que vous avez utilisée lorsque vous avez spécifié les paramètres du site local. Cette section utilise les applets de commande PowerShell (SM) classiques. Si vous ne l’avez pas encore fait, affichez la passerelle de réseau virtuel pour le réseau virtuel Resource Manager et copiez l’adresse IP publique. Il s’agit de l’adresse IP que vous utiliserez pour remplacer l’adresse IP avec espace réservé.

Vérifiez que vous avez téléchargé la dernière version de ces applets de commande, comme indiqué à la section [Avant toute chose](#before).

1. Dans le portail Azure, accédez au réseau virtuel classique.
2. Dans le panneau de votre réseau virtuel, cliquez sur **Vue d’ensemble**.
3. Dans la section **Connexions VPN**, cliquez sur le nom de votre site local dans le graphique.

    ![Connexions VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Connexions VPN")
4. Dans le panneau **Connexions VPN de site à site**, cliquez sur le nom du site.

    ![Nom du site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nom du site local")
5. Dans le volet de connexion pour votre site local, cliquez sur le nom du site local pour ouvrir le panneau **Site local**.

    ![Ouvrir le site local](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Ouvrir le site local")
6. Dans le panneau **Site local**, remplacez l’adresse IP de passerelle VPN avec l’adresse IP de la passerelle Resource Manager.

    ![Adresse IP de la passerelle](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Adresse IP de la passerelle")
7. Cliquez sur **OK** pour mettre à jour l’adresse IP.


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>Section 4 : Créer la connexion
Dans cette section, vous créez la connexion entre les réseaux virtuels. Pour ce faire, vous devez utiliser PowerShell. Vous ne pouvez pas créer cette connexion dans l’un ou l’autre des portails. Assurez-vous que vous avez téléchargé et installé les applets de commande PowerShell classiques et Resource Manager (RM).

### <a name="part-1---log-in-to-your-azure-account"></a>Partie 1 : Se connecter au compte Azure

1. Ouvrez la console PowerShell avec des droits élevés et connectez-vous à votre compte Azure. Les applets de commande suivantes vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés pour être reconnus par Azure PowerShell.
   
        Login-AzureRmAccount 
   
2. Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.
   
        Get-AzureRmSubscription
   
3. Spécifiez l’abonnement que vous souhaitez utiliser. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Ajoutez votre compte Azure pour utiliser les applets de commande PowerShell classiques. Pour ce faire, vous pouvez utiliser la commande suivante :
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>Partie 2 : Télécharger votre fichier de configuration réseau

Parfois, les noms de réseau virtuel classique et de sites de réseau local sont modifiés dans le fichier de configuration réseau lors de la création des paramètres du réseau virtuel classique dans le portail Azure en raison de différences dans les modèles de déploiement. Par exemple, à l’aide du portail Azure, nous avons nommé le réseau virtuel classique « Classic VNet » et nous l’avons créé dans un groupe de ressources nommé « ClassicRG ». Le nom qui est contenu dans le fichier de configuration de réseau est converti en « Group ClassicRG Classic VNet ». Lorsque vous spécifiez le nom d’un réseau virtuel qui contient des espaces, utilisez des guillemets autour de la valeur.

1. Exportez votre fichier de configuration réseau Azure en exécutant la commande suivante. Vous pouvez modifier l’emplacement d’exportation du fichier si nécessaire.
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. Utilisez un éditeur de texte, tel que le Bloc-notes, pour ouvrir le fichier .xml et afficher son contenu. Vérifiez les valeurs pour les noms de votre réseau virtuel classique et du site de réseau local.

### <a name="part-3---set-the-shared-key"></a>Partie 3 : Configurer la clé partagée

Configurez la clé partagée pour la connexion à partir du réseau virtuel classique vers le réseau virtuel Resource Manager. Lorsque vous utilisez ces applets de commande, vérifiez les valeurs de `-VNetName` et `-LocalNetworkSiteName` à l’aide du fichier de configuration de réseau. Lorsque vous spécifiez des noms qui contiennent des espaces, utilisez des guillemets autour de la valeur. 

- Dans cet exemple, `-VNetName` est le nom du réseau virtuel classique. 
- `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le site local.
- La `-SharedKey` est une valeur que vous générez et spécifiez. Dans l’exemple, nous avons utilisé *abc123*, mais vous pouvez générer quelque chose de plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée à l’étape suivante lors de la création de votre connexion.

Dans la console PowerShell, configurez votre clé partagée en exécutant l’exemple suivant, en veillant à remplacer les valeurs par les vôtres. La réponse de cet exemple doit afficher l’ **état Successful**(Opération réussie).
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>Partie 4 : Créez la connexion VPN en exécutant les commandes suivantes :
   
1. Définissez les variables.
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. Créez la connexion. Dans cet exemple, `-Name` est le nom que vous souhaitez donner à votre connexion, et non quelque chose que vous avez déjà créé. L’exemple suivant crée une connexion nommée « RM-Classic ». Notez que `-ConnectionType` est « IPsec », et non « Vnet2Vnet », et que la `-SharedKey` correspond à la clé configurée précédemment.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>Section 5 : Vérifier vos connexions
Vous pouvez vérifier vos connexions à l’aide du portail Azure ou de PowerShell. Lors de la vérification, vous devrez peut-être patienter quelques minutes, le temps que la connexion soit créée. Lorsqu’une connexion est réussie, l’état de connectivité passe de « Connexion » à « Connecté ».

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Pour vérifier la connexion de votre réseau virtuel classique à votre réseau virtuel Resource Manager

####<a name="verify-your-connection-using-powershell"></a>Vérifier votre connexion à l’aide de PowerShell


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Vérifier votre connexion à l’aide du portail Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Pour vérifier la connexion de votre réseau virtuel Resource Manager à votre réseau virtuel classique

####<a name="verify-your-connection-using-powershell"></a>Vérifier votre connexion à l’aide de PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Vérifier votre connexion à l’aide du portail Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>Interconnexion de réseaux virtuels

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


