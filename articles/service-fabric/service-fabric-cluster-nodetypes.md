---
title: "Types de nœuds Service Fabric et groupes de machines virtuelles identiques | Microsoft Docs"
description: "Décrit la relation entre les types de nœuds Service Fabric et les groupes de machines virtuelles identiques et la connexion à distance à une instance de groupe de machines virtuelles identiques ou à un nœud de cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 8c9e91d122591a19d34d944e2d9aaeb327cdafe4
ms.contentlocale: fr-fr
ms.lasthandoff: 09/05/2017

---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Relation entre les types de nœuds Service Fabric et les groupes de machines virtuelles identiques
Les groupes de machines virtuelles identiques constituent une ressource Azure Compute. Ils peuvent être utilisés pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud qui est défini dans un cluster Service Fabric est configuré en tant que groupe de machines virtuelles identiques distinct. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité.

La capture d’écran suivante montre un cluster qui a deux types de nœuds : FrontEnd et BackEnd.  Chaque type de nœud comporte cinq nœuds.

![Capture d’écran d’un cluster qui a deux types de nœuds][NodeTypes]

## <a name="mapping-virtual-machine-scale-set-instances-to-nodes"></a>Mappage des instances du groupe de machines virtuelles identiques sur les nœuds
Comme vous pouvez le constater ci-dessus, les instances du groupe de machines virtuelles identiques démarrent à l’instance 0. Les noms reflètent la numérotation. Par exemple, BackEnd_0 représente l’instance 0 du groupe de machines virtuelles identiques BackEnd. Ce groupe comprend cinq instances, nommées BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 et BackEnd_4.

Quand vous effectuez une montée en puissance sur un groupe de machines virtuelles identiques, une instance est créée. En règle générale, le nom de la nouvelle instance du groupe de machines virtuelles identiques est constitué du nom du groupe de machines virtuelles identiques et du numéro de l’instance suivante. Dans notre exemple, il s’agit de BackEnd_5.

## <a name="mapping-virtual-machine-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mappage des équilibreurs de charge de groupe de machines virtuelles identiques sur chaque type de nœud/groupe de machines virtuelles identiques
Si vous avez déployé votre cluster à partir du portail ou si vous avez utilisé l’exemple de modèle Resource Manager, vous obtenez la liste de toutes les ressources d’un groupe de ressources. Vous voyez les équilibreurs de charge de chaque groupe de machines virtuelles identiques ou type de nœud.

Le nom ressemble à ceci : **LB-&lt;NodeType name&gt;**. Par exemple, LB-sfcluster4doc-0, comme indiqué dans cette capture d’écran :

![Ressources][Resources]

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connexion distante à une instance de groupe de machines virtuelles identiques ou à un nœud de cluster
Chaque type de nœud qui est défini dans un cluster est configuré comme un groupe de machines virtuelles identiques distinct.  Cela signifie que les types de nœuds peuvent subir une montée ou descente en puissance indépendamment les uns des autres. En outre, ils peuvent comprendre différentes références SKU de machine virtuelle. Contrairement aux machines virtuelles à instance unique, les instances de groupe de machines virtuelles identiques n’obtiennent pas une adresse IP virtuelle qui leur est propre. C’est pourquoi rechercher une adresse IP et un port pour se connecter à distance à une instance spécifique peut s’avérer un peu difficile.

Voici la procédure à suivre pour les trouver.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Étape 1 : Rechercher l’adresse IP virtuelle du type de nœud, puis les règles NAT de trafic entrant pour RDP
Pour ce faire, vous devez obtenir les valeurs des règles NAT de trafic entrant qui ont été établies dans le cadre de la définition des ressources pour **Microsoft.Network/loadBalancers**.

Dans le portail, accédez au panneau Équilibreur de charge, puis à **Paramètres**.

![Panneau Équilibreur de charge][LBBlade]

Dans **Paramètres**, cliquez sur **Règles NAT de trafic entrant**. Vous disposez à présent de l’adresse IP et du port nécessaires pour vous connecter à distance à la première instance du groupe de machines virtuelles identiques. Dans la capture d’écran ci-dessous, il s’agit de **104.42.106.156** et **3389**

![Règles NAT][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-virtual-machine-scale-set-instancenode"></a>Étape 2 : Déterminer le port à utiliser pour se connecter à distance à un nœud ou à une instance du groupe de machines virtuelles identiques
Plus haut dans ce document, j’ai abordé le mappage des instances du groupe de machines virtuelles identiques sur les nœuds. Cela permet de déterminer le port exact.

Les ports sont alloués dans l’ordre croissant des instances du groupe de machines virtuelles identiques. Ainsi, dans mon exemple, pour le type de nœud frontal, les ports pour chacune des cinq instances sont les suivants. Vous devez maintenant effectuer le même mappage pour votre instance de groupe de machines virtuelles identiques.

| **Instance de groupe de machines virtuelles identiques** | **Port** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-virtual-machine-scale-set-instance"></a>Étape 3 : Se connecter à distance à une instance du groupe de machines virtuelles identiques
Dans la capture d’écran ci-dessous, j’utilise Connexion Bureau à distance pour me connecter à l’instance FrontEnd_1 :

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Comment modifier les valeurs des plages de ports RDP
### <a name="before-cluster-deployment"></a>Avant le déploiement du cluster
Quand vous configurez le cluster à l’aide d’un modèle Resource Manager, vous pouvez spécifier une plage dans **inboundNatPools**.

Accédez à la définition de ressource pour **Microsoft.Network/loadBalancers**. Sous celle-ci se trouve la description de **inboundNatPools**.  Remplacez les valeurs *frontendPortRangeStart* et *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Après le déploiement du cluster
Après le déploiement du cluster, cette procédure est un peu plus complexe et peut aboutir au recyclage des machines virtuelles. Définissez de nouvelles valeurs à l’aide d’Azure PowerShell. Vérifiez qu’Azure PowerShell 1.0 ou version ultérieure est installé sur votre ordinateur. Si vous ne disposez pas d’Azure Powershell 1.0 ou ultérieur, je vous recommande vivement de suivre les étapes décrites dans [Installer et configurer Azure PowerShell](/powershell/azure/overview).

Connectez-vous à votre compte Azure. Si cette commande PowerShell échoue pour une raison quelconque, vous devez vérifier si Azure PowerShell est correctement installé.

```
Login-AzureRmAccount
```

Exécutez la commande suivante pour obtenir les détails de votre équilibrage de charge et découvrir les valeurs qui décrivent **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

À présent, définissez *frontendPortRangeEnd* et *frontendPortRangeStart* sur les valeurs souhaitées.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```

## <a name="how-to-change-the-rdp-username--password-for-nodes"></a>Comment modifier le nom d’utilisateur et le mot de passe RDP des nœuds

Les étapes suivantes expliquent comment modifier le mot de passe de tous les nœuds d’un type donné. Ces modifications seront appliquées à tous les nœuds actuels et futurs du groupe de machines virtuelles identiques.

### <a name="step-1-open-powershell-with-elevated-privileges-administrator-mode"></a>Étape 1 : Ouvrez PowerShell avec des privilèges élevés (mode Administrateur). 
### <a name="step-2-run-the-following-commands-to-log-in-and-select-your-subscription-for-the-session-change-the-subscriptionid-parameter-to-your-subscription-id"></a>Étape 2 : Exécutez les commandes suivantes pour vous connecter et sélectionner votre abonnement pour la session. Remplacez le paramètre `SUBSCRIPTIONID` par votre ID d’abonnement. 

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
```

### <a name="step-3-run-the-following-script-with-the-appropriate-nodetypename-resourcegroup-username-and-password-values-the-username-and-password-values-will-be-the-new-credentials-that-should-be-used-in-future-rdp-sessions"></a>Étape 3 : Exécutez le script suivant, avec les valeurs `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` et `PASSWORD` appropriées. Les valeurs `USERNAME` et `PASSWORD` sont les nouvelles informations d’identification qui devront être utilisées pour les futures sessions RDP. 

```powershell
$nodeTypeName = 'NODETYPENAME'
$resourceGroup = 'RESOURCEGROUP'
$publicConfig = @{'UserName' = 'USERNAME'}
$privateConfig = @{'Password' = 'PASSWORD'}
$extName = 'VMAccessAgent'
$publisher = 'Microsoft.Compute'
$node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
$node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
```

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de la fonction « Déployer n’importe où » et comparaison avec les clusters gérés par Azure](service-fabric-deploy-anywhere.md)
* [Sécurité des clusters](service-fabric-cluster-security.md)
* [ Kit de développement logiciel (SDK) de Service Fabric et prise en main](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

