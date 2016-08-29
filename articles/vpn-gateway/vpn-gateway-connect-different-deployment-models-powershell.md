<properties 
   pageTitle="Connexion de réseaux virtuels classiques à des réseaux virtuels Resource Manager à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment créer une connexion VPN entre des réseaux virtuels classiques et des réseaux virtuels Resource Manager à l’aide d’une passerelle VPN et de PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="cherylmc" />

# Connecter des réseaux virtuels utilisant des modèles de déploiement différents à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure propose actuellement deux modèles de gestion : le modèle classique et le modèle Resource Manager (RM). Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles et des rôles d’instance Azure exécutés dans un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s’exécutent dans un réseau virtuel créé dans Resource Manager. Cet article vous guide dans le processus de connexion de réseaux virtuels classiques à des réseaux virtuels Resource Manager afin de permettre aux ressources situées dans les modèles de déploiement distincts de communiquer entre elles via une connexion de passerelle.

Vous pouvez créer une connexion entre des réseaux virtuels situés dans des abonnements différents, des régions différentes et des modèles de déploiement différents. Vous pouvez également connecter des réseaux virtuels qui disposent déjà de connexions à des réseaux locaux, à condition que la passerelle avec laquelle ils ont été configurés soit dynamique ou basée sur un itinéraire. Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez le [Forum Aux Questions sur l’interconnexion de réseaux virtuels](#faq) à la fin de cet article. 
[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## Avant tout chose

Les étapes suivantes vous guident à travers les paramétrages nécessaires pour configurer une passerelle dynamique ou basée sur un itinéraire pour chaque réseau virtuel et créer une connexion VPN entre les passerelles. Cette configuration ne prend pas en charge les passerelles statiques ou basées sur des stratégies.

Avant de commencer, vérifiez les points suivants :

 - Les deux réseaux virtuels ont déjà été créés.
 - Les plages d’adresses des réseaux virtuels ne se chevauchent pas ou ne chevauchent aucune des plages des autres connexions susceptibles d’être utilisées par les passerelles.
 - Vous avez installé les dernières applets de commande PowerShell (1.0.2 ou version ultérieure). Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Veillez à installer à la fois les applets de commande de gestion des services et Resource Manager (RM).

### <a name="exampleref"></a>Exemples de paramètres

Vous pouvez utiliser les exemples de paramètres comme référence lors de l’utilisation des applets de commande PowerShell dans les étapes suivantes.

**Paramètres de réseau virtuel classique**

Nom du réseau virtuel = ClassicVNet <br> Emplacement = États-Unis de l’Ouest <br> Espaces d’adresses du réseau virtuel = 10.0.0.0/24 <br> Sous-réseau 1 = 10.0.0.0/27 <br> Sous-réseau de passerelle = 10.0.0.32/29 <br> Nom du réseau local = RMVNetLocal <br> Type de passerelle = DynamicRouting

**Paramètres de réseau virtuel Resource Manager**

Nom du réseau virtuel = RMVNet <br> Groupe de ressources = RG1 <br> Espaces d’adresses du réseau virtuel = 192.168.1.0/16 <br> Sous-réseau 1 = 192.168.1.0/24 <br> Sous-réseau de passerelle = 192.168.0.0/26 <br> Emplacement = États-Unis de l’Est<br> Nom d’adresse IP publique de la passerelle = gwpip <br> Passerelle de réseau local = ClassicVNetLocal <br> Nom de la passerelle de réseau virtuel = RMGateway <br> Configuration d’adressage IP de la passerelle = gwipconfig


## <a name="createsmgw"></a>Section 1 : Configurer le réseau virtuel classique

### Partie 1 : Télécharger votre fichier de configuration réseau

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous invite à saisir vos informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l'applet de commande télécharge vos paramètres de compte pour qu'ils soient reconnus par Azure PowerShell. Vous allez utiliser les applets de commande PowerShell de gestion des services pour mener à bien cette partie de la configuration.

		Add-AzureAccount

2. Exportez votre fichier de configuration réseau Azure en exécutant la commande suivante. Vous pouvez modifier l’emplacement d’exportation du fichier si nécessaire. Vous allez modifier le fichier, puis l’importer vers Azure.

		Get-AzureVNetConfig -ExportToFile c:\AzureNet\NetworkConfig.xml

3. Ouvrez le fichier .xml que vous avez téléchargé pour le modifier. Pour obtenir un exemple de fichier de configuration réseau, consultez le [schéma de configuration réseau](https://msdn.microsoft.com/library/jj157100.aspx).
 

### Partie 2 : Vérifier le sous-réseau de passerelle

Dans l’élément **VirtualNetworkSites**, ajoutez un sous-réseau de passerelle à votre réseau virtuel si vous n’en avez pas déjà créé un. Lorsque vous travaillez avec le fichier de configuration réseau, le sous-réseau de passerelle DOIT être nommé « GatewaySubnet ». Sinon, Azure ne peut pas le reconnaître et l’utiliser comme sous-réseau de passerelle.
	
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### Partie 3 : Ajouter le site de réseau local

Le site de réseau local que vous ajoutez représente le réseau virtuel RM auquel vous souhaitez vous connecter. Vous devrez peut-être ajouter un élément **LocalNetworkSites** au fichier s’il n’en existe pas déjà un. À ce stade de la configuration, n’importe quelle adresse IP publique valide peut être utilisée pour l’élément VPNGatewayAddress, car nous n’avons pas encore créé la passerelle pour le réseau virtuel Resource Manager. Une fois la passerelle créée, nous remplacerons cette adresse IP temporaire par l’adresse IP publique qui a été affectée à la passerelle RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.1.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### Partie 4 : Associer le réseau virtuel au site de réseau local

Dans cette section, nous spécifions le site de réseau local auquel vous souhaitez connecter le réseau virtuel. Dans ce cas, il s’agit du réseau virtuel Resource Manager que vous avez référencé plus tôt. Assurez-vous que les noms correspondent. Cette étape ne vise pas à créer une passerelle. Elle spécifie le réseau local auquel la passerelle se connectera.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### Partie 5 : Enregistrer et charger le fichier

Enregistrez le fichier, puis importez-le dans Azure en exécutant la commande ci-dessous. Assurez-vous que vous modifiez le chemin d'accès selon les besoins de votre environnement.

		Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\ClassicVNet.xml

Un résultat similaire au résultat suivant, indiquant que l’importation a réussi, doit s’afficher.

		OperationDescription        OperationId                      OperationStatus                                                
		--------------------        -----------                      ---------------                                                
		Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### Partie 6 : Créer la passerelle de réseau virtuel 

Vous pouvez créer votre passerelle de réseau virtuel soit à l’aide du portail Classic, soit à l’aide de PowerShell. À l’heure actuelle, il n’est pas possible de créer une passerelle classique dans le portail Azure.

#### Pour créer votre passerelle dans le portail Classic

1. Dans le [portail Classic](https://manage.windowsazure.com), accédez à **Réseaux** et cliquez sur le réseau virtuel classique pour lequel vous souhaitez créer une passerelle de réseau virtuel. La page principale de votre réseau virtuel s’ouvre.
2. Cliquez sur **Tableau de bord** en haut de la page pour passer à la page Tableau de bord.
3. En bas de la page Tableau de bord, cliquez sur **Créer la passerelle**, puis sur **Routage dynamique**.
4. Cliquez sur **Oui** pour lancer la création de votre passerelle.
5. Attendez que la passerelle soit créée. Cette opération peut parfois prendre 45 minutes ou plus.
6. Une fois la passerelle créée, vous pouvez afficher l’adresse IP de la passerelle dans la page **Tableau de bord**. Il s’agit de l’adresse IP publique de votre passerelle. Notez ou copiez l’adresse IP publique. Elle vous servira ultérieurement lors de la création du réseau local pour la configuration de votre réseau virtuel Resource Manager.
7. Vous pouvez éventuellement utiliser cette applet de commande pour récupérer les paramètres de votre passerelle et vérifier l’état de la passerelle : `Get-AzureVirtualNetworkGateway`

#### Pour créer votre passerelle à l’aide de PowerShell

Pour créer votre passerelle à l’aide de PowerShell, utilisez l’exemple suivant.

	New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting


## <a name="creatermgw"></a>Section 2 : Configurer la passerelle du réseau virtuel Resource Manager

Pour créer une passerelle VPN pour le réseau virtuel RM, suivez les instructions suivantes. Ne commencez pas cette procédure avant d’avoir récupéré l’adresse IP publique de la passerelle du réseau virtuel classique.

1. Dans la console PowerShell, **connectez-vous à votre compte Azure**. Les applets de commande suivantes vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés pour être reconnus par Azure PowerShell.

		Login-AzureRmAccount 

 	Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.

		Get-AzureRmSubscription

	Spécifiez l’abonnement que vous souhaitez utiliser.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Créez une passerelle de réseau local**. Dans un réseau virtuel, la passerelle de réseau local fait généralement référence à votre emplacement local. Dans ce cas, la passerelle de réseau local fait référence à votre réseau virtuel classique. Donnez à cet emplacement un nom auquel Azure pourra se référer, puis spécifiez le préfixe de l’espace d’adressage. Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic à envoyer vers votre emplacement local. Si vous avez besoin d’ajuster ces informations ultérieurement, avant de créer votre passerelle, vous pouvez modifier les valeurs et exécuter à nouveau l’exemple.<br><br>
	- `-Name` est le nom à affecter pour faire référence à la passerelle de réseau local.<br>
	- `-AddressPrefix` est l’espace d’adressage de votre réseau virtuel classique.<br>
	- `-GatewayIpAddress` est l’adresse IP publique de la passerelle du réseau virtuel classique. Veillez à modifier l’exemple suivant pour refléter l’adresse IP correcte.
	
			New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
			-Location "West US" -AddressPrefix "10.0.0.0/24" `
			-GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Demandez qu’une adresse IP publique** soit allouée à la passerelle VPN de votre réseau virtuel Azure Resource Manager. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est allouée à votre passerelle de façon dynamique. Néanmoins, cela ne signifie pas que l’adresse IP est modifiée. L’adresse IP de la passerelle VPN Azure change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN Azure.<br> cette étape, nous définissons également une variable qui sera utilisée à une étape ultérieure.


		$ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
		-ResourceGroupName RG1 -Location 'EastUS' `
		-AllocationMethod Dynamic

4. **Vérifiez que votre réseau virtuel possède un sous-réseau de passerelle**. S’il n’existe aucun sous-réseau de passerelle, ajoutez-en un. Veillez à nommer le sous-réseau de passerelle *GatewaySubnet*.

5. **Récupérez le sous-réseau** utilisé pour la passerelle en exécutant la commande suivante. À cette étape, nous définissons également une variable qui sera utilisée à l’étape suivante.
	- `-Name` est le nom de votre réseau virtuel Resource Manager.
	- `-ResourceGroupName` est le groupe de ressources auquel le réseau virtuel est associé. Pour fonctionner correctement, le sous-réseau de passerelle doit être nommé *GatewaySubnet*.


			$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Créez la configuration d’adressage IP de la passerelle**. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple suivant pour créer la configuration de votre passerelle.<br>À cette étape, la propriété ID et les objets d’adresse IP doivent être transmis aux paramètres `-SubnetId` et `-PublicIpAddressId` respectivement. Vous ne pouvez pas utiliser une chaîne simple. Ces variables sont définies à l’étape de demande d’une adresse IP publique et à l’étape de récupération du sous-réseau.

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
		-Name gwipconfig -SubnetId $subnet.id `
		-PublicIpAddressId $ipaddress.id
	
7. **Créez la passerelle du réseau virtuel Resource Manager** en exécutant la commande suivante. `-VpnType` doit être défini sur *RouteBased*.

		New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
		-Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
		-IpConfigurations $gwipconfig `
		-EnableBgp $false -VpnType RouteBased

8. Une fois la passerelle VPN créée, **copiez l’adresse IP publique**. Elle vous servira lors de la configuration des paramètres de réseau local pour votre réseau virtuel classique. Vous pouvez utiliser l’applet de commande suivante pour récupérer l’adresse IP publique. L’adresse IP publique est répertoriée dans la réponse en tant que *IpAddress*.

		Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## Section 3 : Modifier le réseau local pour le réseau virtuel classique

Vous pouvez effectuer cette étape en exportant le fichier de configuration réseau, en le modifiant, puis en l’enregistrant et en le réimportant dans Azure. Vous avez également la possibilité de modifier ce paramètre dans le portail Classic.

### Pour effectuer la modification dans le portail

1. Ouvrez le [portail Classic](https://manage.windowsazure.com).

2. Dans le portail Classic, faites défiler la page vers le bas sur le côté gauche et cliquez sur **Réseaux**. En haut de la page **Réseaux**, cliquez sur **Réseaux locaux**.

3. Dans la page **Réseaux locaux**, cliquez sur le réseau local que vous avez configuré dans la partie 1 pour le sélectionner, puis accédez au bas de la page et cliquez sur **Modifier**.

4. Dans la page **Spécifier les détails de votre réseau local**, remplacez l’adresse IP temporaire par l’adresse IP publique de la passerelle Resource Manager que vous avez créée dans la section précédente. Cliquez sur la flèche pour passer à la section suivante. Vérifiez que **l’espace d’adressage** est correct, puis cliquez sur la coche pour accepter les modifications.

### Pour effectuer la modification à l’aide du fichier de configuration réseau

1. Exportez le fichier de configuration réseau.
2. Dans un éditeur de texte, modifiez l’adresse IP de la passerelle VPN.

	    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Enregistrez vos modifications et réimportez le fichier modifié dans Azure.


## <a name="connect"></a>Section 4 : Créer une connexion entre les passerelles

La création d’une connexion entre les passerelles nécessite PowerShell. Vous devrez peut-être ajouter votre compte Azure pour utiliser les applets de commande PowerShell classiques. Pour ce faire, vous pouvez utiliser l’applet de commande suivante :
		
	Add-AzureAccount

1. **Définissez votre clé partagée** en exécutant l’exemple suivant. Dans cet exemple, `-VNetName` est le nom du réseau virtuel classique et `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le réseau local lors de sa configuration dans le portail Classic. `-SharedKey` est une valeur que vous pouvez générer et spécifier. La valeur que vous spécifiez ici doit être la même que celle spécifiée à l’étape suivante lors de la création de votre connexion. La réponse de cet exemple doit afficher l’état Successful (Opération réussie).

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Créez la connexion VPN en exécutant les commandes suivantes.

	**Définir les variables**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**Créer la connexion**<br> Notez que `-ConnectionType` est défini sur « IPsec » et non pas sur « Vnet2Vnet ».
		
		New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Vous pouvez afficher votre connexion dans l’un ou l’autre des portails, ou à l’aide de l’applet de commande `Get-AzureRmVirtualNetworkGatewayConnection`.

		Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Forum Aux Questions sur l’interconnexion de réseaux virtuels

Consultez les détails du Forum Aux Questions pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0810_2016-->
