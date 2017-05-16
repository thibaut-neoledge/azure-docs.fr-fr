---
title: "Homologation de réseaux virtuels Azure - Modèle | Microsoft Docs"
description: "Découvrez comment créer une homologation de réseaux virtuels à l’aide d’un modèle Azure Resource Manager."
services: virtual-network
documentationcenter: 
author: narayanannamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 75f8d10e-23e8-44bd-9972-aab74048cf38
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 86eec91025ae8ae553fa1f76a687e440784e7855
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-peering-using-an-azure-resource-manager-template"></a>Créer une homologation de réseaux virtuels à l’aide d’un modèle Azure Resource Manager
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer une homologation de réseaux virtuels à l’aide de modèles Resource Manager, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.

   > [!NOTE]
   > L’applet de commande PowerShell pour la gestion de l’homologation de réseaux virtuels est fournie avec [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. Le texte ci-dessous illustre la définition d’un lien d’homologation de réseaux virtuels de VNet1 à VNet2, d’après le scénario ci-dessus. Copiez le contenu ci-dessous et enregistrez-le dans un fichier nommé VNetPeeringVNet1.json.

    ```json
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
    ```
3. La section ci-dessous illustre la définition d’un lien d’homologation de réseaux virtuels de VNet2 à VNet1, d’après le scénario ci-dessus.  Copiez le contenu ci-dessous et enregistrez-le dans un fichier nommé VNetPeeringVNet2.json.

    ```json
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
    ```
    Comme l’illustre le modèle ci-dessus, il existe plusieurs propriétés configurables pour l’homologation de réseaux virtuels :
   
   | Option | Description | Default |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Indique si l’espace d’adressage d’un réseau virtuel homologue est inclus ou non dans le cadre de la balise Virtual_network. |Oui |
   | AllowForwardedTraffic |Spécifie si le trafic ne provenant pas d’un réseau virtuel homologué est accepté ou rejeté. |Non |
   | AllowGatewayTransit |Permet au réseau virtuel homologue d’utiliser votre passerelle de réseau virtuel. |Non |
   | UseRemoteGateways |Permet d’utiliser la passerelle de votre réseau virtuel homologue. Le réseau virtuel homologue doit avoir une passerelle configurée et la propriété AllowGatewayTransit sélectionnée. Vous ne pouvez pas utiliser cette option si vous avez une passerelle configurée. |Non |
   
    Chaque lien de l’homologation de réseaux virtuels présente l’ensemble des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess sur True pour l’homologation de réseaux virtuels de VNet1 à VNet2 et lui attribuer la valeur False pour le lien d’homologation de réseaux virtuels dans l’autre direction.
4. Pour déployer le fichier de modèle, exécutez `New-AzureRmResourceGroupDeployment` afin de créer ou de mettre à jour le déploiement. Pour plus d’informations sur l’utilisation des modèles Resource Manager, reportez-vous à [cet article](../azure-resource-manager/resource-group-template-deploy.md).

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
    ```

   > [!NOTE]
   > Remplacez le nom du groupe de ressources et le fichier de modèle comme il convient.
   > 
   > 
   
    Voici un exemple basé sur le scénario précédent :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
    ```

    Sortie retournée :
   
        DeploymentName        : VNetPeeringVNet1
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
    ```

    Sortie retournée :
   
        DeploymentName        : VNetPeeringVNet2
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. Une fois le déploiement terminé, exécutez l’applet de commande suivante pour afficher l’état de l’homologation :

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
    ```

    Sortie retournée :
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : VNet101
        VirtualNetworkName    : VNet1
        ProvisioningState        : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Une fois l’homologation créée dans ce scénario, vous devez être en mesure d’établir des connexions de n’importe quelle machine virtuelle à n’importe quelle machine virtuelle des deux réseaux virtuels. Par défaut, la propriété `AllowVirtualNetworkAccess` est définie sur *True* et l’homologation de réseaux virtuels configure les listes de contrôle d’accès appropriées pour permettre la communication entre les réseaux virtuels. Vous pouvez toujours appliquer les règles de groupe de sécurité réseau pour bloquer la connectivité entre des sous-réseaux ou des machines virtuelles spécifiques afin d’avoir un contrôle précis de l’accès entre deux réseaux virtuels. Lisez l’article [Groupes de sécurité réseau](virtual-networks-create-nsg-arm-ps.md) pour en savoir plus sur les groupes de sécurité réseau.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer une homologation de réseaux virtuels entre des abonnements, suivez les étapes ci-dessous :

1. Connectez-vous à Azure avec le compte de l’utilisateur privilégié UserA de l’abonnement Subscription-A et exécutez l’applet de commande suivante :

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
    ```

    Cela n’est pas obligatoire. L’homologation peut être établie si des utilisateurs ont effectué des demandes d’homologation individuelles pour leurs réseaux virtuels respectifs, à condition que les demandes correspondent. L’ajout d’un utilisateur privilégié de l’autre réseau virtuel en tant qu’utilisateur du réseau virtuel local facilite la configuration.
2. Connectez-vous à Azure avec le compte de l’utilisateur privilégié UserB de l’abonnement Subscription-B et exécutez l’applet de commande suivante :

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
    ```

    > [!IMPORTANT]
    > Si l’homologation que vous créez est entre deux réseaux virtuels créés à l’aide du modèle de déploiement Azure Resource Manager, exécutez les étapes restantes de cette section. Si les deux réseaux virtuels ont été créés via des modèles de déploiement différents, ignorez les étapes restantes de cette section et suivez les étapes de la section [Homologation de réseaux virtuels créés via des modèles de déploiement différents](#x-model) de cet article.

3. Dans la session de connexion de l’utilisateur UserA, exécutez cette applet de commande :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all
    ```
   
    Le JSON est le suivant :

    ```json
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
    ```
4. Dans la session de connexion de l’utilisateur UserB, exécutez l’applet de commande suivante :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
    ```

    Le JSON est le suivant :

    ```json
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
    ```

    Une fois l’homologation créée dans ce scénario, vous devez être en mesure d’établir des connexions de n’importe quelle machine virtuelle à n’importe quelle machine virtuelle des deux réseaux virtuels entre différents abonnements.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez déployer l’exemple de modèle suivant pour établir l’homologation de réseaux virtuels. Vous devez définir la propriété `AllowForwardedTraffic` sur *True* pour permettre à l’appliance virtuelle du réseau virtuel homologué d’envoyer et de recevoir du trafic.

    Le JSON suivant correspond au contenu d’un modèle permettant de créer une homologation de réseaux virtuels de HubVNet à VNet1. Notez que la propriété AllowForwardedTraffic est définie sur False.

    ```json
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
        ]
        }
    ```
2. Le JSON suivant correspond au contenu d’un modèle permettant de créer une homologation de réseaux virtuels de VNet1 à HubVnet. Notez que la propriété AllowForwardedTraffic est définie sur True.

    ```json
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
    ```
3. Une fois l’homologation établie, vous pouvez vous reporter à [cet article](virtual-network-create-udr-arm-ps.md) pour créer des itinéraires définis par l’utilisateur (UDR) afin de rediriger le trafic du réseau virtuel VNET1 via une appliance virtuelle et d’utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez la définir sur l’adresse IP de l’appliance virtuelle du réseau virtuel homologue HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Pour créer une homologation entre des réseaux virtuels utilisant des modèles de déploiement différents, procédez comme suit :

1. Si vous créez une homologation entre des réseaux virtuels déployés via des modèles de déploiement différents dans le *même* abonnement, passez directement à l’étape 2. La possibilité de créer une homologation de réseaux virtuels créés via des modèles de déploiement différents qui se trouvent dans des abonnements *différents* est disponible dans la **version préliminaire**. Les fonctionnalités de la version préliminaire n’ont pas le même niveau de fiabilité et de contrat de niveau de service que les fonctionnalités disponibles en version générale. Si vous créez une homologation entre des réseaux virtuels déployés via des modèles de déploiement différents dans des abonnements différents, vous devez tout d’abord effectuer les tâches suivantes :
    - Enregistrez la fonction de version préliminaire dans votre abonnement Azure en entrant la commande suivante à partir de PowerShell : `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` et `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`
    - Exécutez les étapes 1 à 2 de la section [Homologation entre des abonnements](#x-sub) de cet article.
2. Le texte ci-dessous illustre la définition d’un lien d’homologation de réseaux virtuels de VNET1 à VNET2 dans ce scénario. Un seul lien est requis pour homologuer un réseau virtuel Classic à un réseau virtuel Azure Resource Manager.

    Veillez à placer dans votre ID d’abonnement dans l’emplacement du réseau virtuel Classic, ou VNET2, et à remplacer MyResouceGroup par le nom du groupe de ressources approprié.

    ```json
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
            "name": "VNET1/LinkToVNET2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"
        }

            }
            }
        ]
        }
    ```
3. Pour déployer le fichier de modèle, exécutez l’applet de commande suivante afin de créer ou de mettre à jour le déploiement :

    ```powershell
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
    ```

    Sortie retournée :
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
4. Une fois le déploiement réussi, vous pouvez exécuter l’applet de commande suivante pour afficher l’état de l’homologation :

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
    ```

    Sortie retournée :
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Lorsque l’homologation est établie entre un réseau virtuel Classic et un réseau virtuel Resource Manager, vous devez être en mesure d’établir des connexions de n’importe quelle machine virtuelle de VNET1 à n’importe quelle machine virtuelle de VNET2, et inversement.


