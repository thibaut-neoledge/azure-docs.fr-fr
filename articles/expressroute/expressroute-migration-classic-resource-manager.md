---
title: "Migrer des circuits ExpressRoute et les réseaux virtuels associés d’un déploiement classique vers Resource Manager - Azure | Microsoft Docs"
description: "Cette page explique comment migrer un circuit classique et les réseaux virtuels associés vers Resource Manager."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>Migrer des circuits ExpressRoute et les réseaux virtuels associés du modèle de déploiement classique au modèle de déploiement Resource Manager

Cet article explique comment migrer des circuits ExpressRoute et les réseaux virtuels associés du modèle de déploiement classique au modèle de déploiement Azure Resource Manager. 


## <a name="before-you-begin"></a>Avant de commencer
* Vérifiez que vous disposez de la dernière version des modules Azure PowerShell. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md). Vous devez avoir bien compris les limites et les limitations.
* Vérifiez que le circuit est totalement opérationnel dans le modèle de déploiement classique.
* Assurez-vous que vous disposez d’un groupe de ressources créé dans le modèle de déploiement Resource Manager.
* Passez en revue la documentation de migration de ressources suivante :

    * [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQ : Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Erreurs courantes lors de la migration de Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-migration-errors.md)

## <a name="supported-and-unsupported-scenarios"></a>Scénarios pris en charge et non pris en charge

* Il est possible de migrer un circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager sans le moindre temps d’arrêt. Vous pouvez déplacer n’importe quel circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager sans temps d’arrêt. Suivez les instructions relatives au [déplacement des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell](expressroute-howto-move-arm.md). C’est une condition requise pour déplacer des ressources connectées au réseau virtuel.
* Il est possible de migrer les réseaux virtuels, les passerelles et les déploiements associés au sein du réseau virtuel qui sont attachés à un circuit ExpressRoute se trouvant dans le même abonnement vers l’environnement Resource Manager sans le moindre temps d’arrêt. Vous pouvez suivre les étapes décrites plus loin pour migrer des ressources telles que des réseaux virtuels, des passerelles et des machines virtuelles déployées au sein du réseau virtuel. Vous devez vous assurer que les réseaux virtuels sont configurés correctement avant de les migrer. 
* La migration des réseaux virtuels, des passerelles et des déploiements associés au sein du réseau virtuel qui ne se trouvent pas dans le même abonnement que le circuit ExpressRoute nécessite un temps d’arrêt. La dernière section du document décrit les étapes à suivre pour migrer des ressources.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Déplacer un circuit ExpressRoute d’un déploiement classique vers Resource Manager
Vous devez déplacer un circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager avant d’essayer de migrer des ressources attachées au circuit ExpressRoute. Pour accomplir cette tâche, consultez les articles suivants :

* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md).
* [Migrer un circuit d’un déploiement classique vers Resource Manager à l’aide d’Azure PowerShell](expressroute-howto-move-arm.md).
* Utilisez le portail de gestion des services Azure. Vous pouvez suivre le flux de travail pour [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et sélectionner l’option d’importation. 

Cette opération n’implique pas de temps d’arrêt. Vous pouvez continuer à transférer des données entre votre site et Microsoft pendant la migration.

## <a name="prepare-your-virtual-network-for-migration"></a>Préparer votre réseau virtuel pour la migration
Vous devez vous assurer que le réseau de votre réseau virtuel à migrer ne présente pas d’artefacts inutiles. Pour télécharger la configuration de votre réseau virtuel afin d’y apporter les modifications nécessaires, exécutez l’applet de commande PowerShell suivante :

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
Vous devez vous assurer que toutes les références à <ConnectionsToLocalNetwork> sont supprimées des réseaux virtuels à migrer. Un exemple de configuration réseau est illustré dans l’extrait de code suivant :

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
Si <ConnectionsToLocalNetwork> n’est pas vide, supprimez les références dans cette section et soumettez à nouveau la configuration de votre réseau. Vous pouvez le faire en exécutant l’applet de commande PowerShell suivante :

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrer les réseaux virtuels, les passerelles et les déploiements associés se trouvant dans le même abonnement que le circuit ExpressRoute
Cette section décrit les étapes à suivre pour migrer un réseau virtuel, une passerelle et les déploiements associés se trouvant dans le même abonnement que le circuit ExpressRoute. Cette migration n’implique aucun temps d’arrêt. Vous pouvez continuer à utiliser toutes les ressources pendant le processus de migration. Le plan de gestion est verrouillé pendant la migration. 

1. Assurez-vous que le circuit ExpressRoute a été déplacé de l’environnement classique à l’environnement Resource Manager.
2. Assurez-vous que le réseau virtuel a été préparé correctement pour la migration.
3. Inscrivez votre abonnement pour la migration de ressources. Pour inscrire votre abonnement pour la migration de ressources, utilisez l’extrait de code PowerShell suivant : 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. Effectuez la validation, la préparation et la migration. Pour déplacer le réseau virtuel, utilisez l’extrait de code PowerShell suivant :
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    Vous pouvez également abandonner la migration en exécutant l’applet de commande PowerShell suivante :
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>Migrer les réseaux virtuels, les passerelles et les déploiements associés se trouvant dans un autre abonnement que le circuit ExpressRoute

1. Assurez-vous que le circuit ExpressRoute a été déplacé de l’environnement classique à l’environnement Resource Manager.
2. Assurez-vous que le réseau virtuel a été préparé correctement pour la migration.
3. Assurez-vous que le circuit ExpressRoute peut fonctionner dans l’environnement classique et l’environnement Resource Manager. Pour permettre l’utilisation du circuit dans l’environnement classique et l’environnement Resource Manager, utilisez le script PowerShell suivant : 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. Créez des autorisations dans l’environnement Resource Manager. Pour savoir comment créer des autorisations, consultez [Lier un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md). Pour créer une autorisation, utilisez l’extrait de code PowerShell suivant :
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    Notez l’ID du circuit et la clé d’autorisation. Ces éléments sont utilisés pour connecter le circuit au réseau virtuel une fois la migration terminée.
  
5. Supprimez le lien de circuit dédié associé au réseau virtuel. Pour supprimer le lien de circuit dans l’environnement classique, utilisez l’applet de commande suivante : 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. Inscrivez votre abonnement pour la migration de ressources. Pour inscrire votre abonnement pour la migration de ressources, utilisez l’extrait de code PowerShell suivant : 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. Effectuez la validation, la préparation et la migration. Pour déplacer le réseau virtuel, utilisez l’extrait de code PowerShell suivant :
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    Vous pouvez également abandonner la migration en exécutant l’applet de commande PowerShell suivante :
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. Connectez à nouveau le réseau virtuel au circuit ExpressRoute. L’extrait de code PowerShell suivant est exécuté dans le contexte de l’abonnement dans lequel le réseau virtuel est créé. Vous ne devez pas exécuter cet extrait de code dans l’abonnement dans lequel le circuit est créé. Utilisez l’ID du circuit en tant que PeerID et la clé d’autorisation notée à l’étape 4.
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>Étapes suivantes
* [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQ : Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Erreurs courantes lors de la migration de Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-migration-errors.md)

