<properties
   pageTitle="Créer une homologation de réseaux virtuels à l’aide de modèles Resource Manager | Microsoft Azure"
   description="Découvrez comment créer une homologation de réseaux virtuels à l’aide des modèles disponibles dans Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Créer une homologation de réseaux virtuels à l’aide de modèles Resource Manager

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer une homologation de réseaux virtuels à l’aide de modèles Resource Manager, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivre les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.

Remarque : L’applet de commande PowerShell pour la gestion de l’homologation de réseaux virtuels est fournie avec [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0).

2. La section ci-dessous illustre la définition du lien d’homologation de réseaux virtuels de VNet1 à VNet2, d’après le scénario ci-dessus. Copiez le contenu dans un fichier et enregistrez ce dernier sous le nom VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
    

3. La section ci-dessous illustre la définition du lien d’homologation de réseaux virtuels de VNet2 à VNet1, d’après le scénario ci-dessus. Copiez le contenu dans un fichier et enregistrez ce dernier sous le nom VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

Comme le modèle ci-dessus l’illustre, il existe plusieurs propriétés configurables pour l’homologation de réseaux virtuels :

|Option|Description|Default|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Indique si l’espace d’adressage du réseau virtuel homologue doit être inclus dans le cadre de la balise Virtual\_network.|Oui|
|AllowForwardedTraffic|Spécifie si le trafic ne provenant pas du réseau virtuel homologué doit être accepté ou rejeté.|Non|
|AllowGatewayTransit|Permet au réseau virtuel homologue d’utiliser votre passerelle de réseau virtuel.|Non|
|UseRemoteGateways|Permet d’utiliser la passerelle de votre réseau virtuel homologue. Une passerelle doit être configurée pour le réseau virtuel homologue et la propriété AllowGatewayTransit doit être sélectionnée. Vous ne pouvez pas utiliser cette option si vous avez une passerelle configurée.|Non|

Chaque lien de l’homologation de réseaux virtuels présente plusieurs des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess sur True pour l’homologation de réseaux virtuels de VNet1 à VNet2 et lui attribuer la valeur False pour le lien d’homologation de réseaux virtuels dans l’autre sens.


4. Pour déployer le fichier de modèle, vous pouvez exécuter l’applet de commande New-AzureRmResourceGroupDeployment afin de créer ou de mettre à jour le déploiement. Pour plus d’informations sur l’utilisation du modèle Resource Manager, reportez-vous à [cet article](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

> [AZURE.NOTE] Remplacez le nom du groupe de ressources et le chemin d’accès du fichier de modèle comme il convient.

Voici un exemple basé sur le scénario ci-dessus :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

La sortie est la suivante :

        DeploymentName		: VNetPeeringVNet1
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:05:03 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

La sortie est la suivante :

        DeploymentName		: VNetPeeringVNet2
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:07:22 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Une fois le déploiement terminé, vous pouvez exécuter l’applet de commande ci-dessous pour afficher l’état de l’homologation :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    La sortie est la suivante :

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: VNet101
        VirtualNetworkName	: VNet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Une fois l’homologation créée dans ce scénario, vous devez être en mesure d’établir des connexions de n’importe quelle machine virtuelle à n’importe quelle machine virtuelle des deux réseaux virtuels. Par défaut, la propriété AllowVirtualNetworkAccess est définie sur True et l’homologation de réseaux virtuels configure les listes de contrôle d’accès appropriées pour permettre la communication entre les réseaux virtuels. Mais vous pouvez toujours appliquer les règles de groupe de sécurité réseau pour bloquer la connectivité entre par exemple des sous-réseaux ou des machines virtuelles spécifiques afin d’avoir un contrôle précis de l’accès entre deux réseaux virtuels. Pour plus d’informations sur la création de règles de groupe de sécurité réseau, reportez-vous à [cet article](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer une homologation de réseaux virtuels entre différents abonnements, suivez les étapes ci-dessous :

1. Connectez-vous à Azure en tant qu’utilisateur privilégié UserA de l’abonnement Subscription-A et exécutez l’applet de commande :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

Il ne s’agit pas d’une obligation : l’homologation peut être établie si des utilisateurs ont effectué des demandes d’homologation individuelles pour leurs réseaux respectifs, à condition que les demandes correspondent. L’ajout de l’utilisateur privilégié de l’autre réseau virtuel en tant qu’utilisateur du réseau virtuel local facilite la configuration.

2. Connectez-vous à Azure en tant qu’utilisateur privilégié UserB de l’abonnement Subscription-B et exécutez l’applet de commande :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Ensuite, dans la session de connexion de l’utilisateur UserA, exécutez l’applet de commande :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Voici comment le fichier JSON est défini :
    
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
   
4. Ensuite, dans la session de connexion de l’utilisateur UserB, exécutez l’applet de commande :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
   
   Voici comment le fichier JSON est défini :

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
 
 Une fois l’homologation créée dans ce scénario, vous devez être en mesure d’établir des connexions de n’importe quelle machine virtuelle à n’importe quelle machine virtuelle des deux réseaux virtuels entre différents abonnements.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez déployer l’exemple de modèle ci-dessous pour établir l’homologation de réseaux virtuels, en veillant bien à définir la propriété AllowForwardedTraffic sur True pour permettre à l’appliance virtuelle réseau du réseau virtuel homologué d’envoyer et de recevoir du trafic. Voici le modèle permettant de créer l’homologation de réseaux virtuels de HubVNet à VNet1. Notez que la propriété AllowForwardedTraffic est définie sur False.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Voici le modèle permettant de créer l’homologation de réseaux virtuels de VNet1 à HubVNet. Notez que la propriété AllowForwardedTraffic est définie sur True.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Une fois l’homologation établie, vous pouvez vous reporter à [cet article](virtual-network-create-udr-arm-ps.md) et créer un itinéraire défini par l’utilisateur (UDR) pour rediriger le trafic du réseau virtuel VNet1 via une appliance virtuelle afin d’utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez la définir sur l’adresse IP de l’appliance virtuelle du réseau virtuel homologue HubVNet.

<!---HONumber=AcomDC_0803_2016-->