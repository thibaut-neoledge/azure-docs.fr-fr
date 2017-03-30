---
title: "Création d’un groupe de machines virtuelles identiques Azure | Microsoft Docs"
description: "Créer et déployer un groupe de machines virtuelles identiques Linux ou Windows Azure avec l’interface CLI Azure, PowerShell, un modèle ou Visual Studio."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 949e59b64557ac3efc07da73a205c808e25aeaab
ms.lasthandoff: 03/22/2017

---

# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>Créer et déployer un groupe de machines virtuelles identiques
Les jeux de mise à l’échelle de machines virtuelles facilitent le déploiement et la gestion de machines virtuelles identiques en tant qu’ensemble. Les groupes à échelle identique fournissent une couche de calcul hautement évolutive et personnalisable pour les applications « hyperscale », et prennent en charge les images de plateforme Windows, les images de plateforme Linux, des images personnalisées et les extensions. Pour plus d’informations sur les jeux de mise à l’échelle, consultez [Jeux de mise à l’échelle de machine virtuelle](virtual-machine-scale-sets-overview.md).

Ce didacticiel vous montre comment créer un groupe de machines virtuelles identiques **sans** utiliser le portail Azure. Pour plus d’informations sur l’utilisation du portail Azure, consultez [Création d’un groupe de machines virtuelles identiques avec le portail Azure](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Pour plus d’informations sur les ressources Azure Resource Manager, consultez [Déploiement Azure Resource Manager et déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="log-in-to-azure"></a>Connexion à Azure

Si vous utilisez Azure CLI 2.0 ou PowerShell pour créer un groupe identique, vous devez d’abord vous connecter à votre abonnement.

Pour plus d’informations sur l’installation, la configuration et la connexion à Azure avec Azure CLI 2.0 ou PowerShell, consultez [Bien démarrer avec Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) ou [Bien démarrer avec les applets de commande Azure PowerShell](/powershell/resourcemanager/).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="prep-create-a-resource-group"></a>Préparation  : Créer un groupe de ressources

Vous devez d’abord créer un groupe de ressources auquel est associé le groupe de machines virtuelles identiques.

```azurecli
az group create --location westus2 --name vmss-test-1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name vmss-test-1
```

## <a name="create-from-azure-cli"></a>Créer à partir de l’interface de ligne de commande Azure

Avec Azure CLI, vous pouvez créer un groupe de machines virtuelles identiques avec un minimum d’effort. Si vous les omettez, les valeurs par défaut vous sont fournies. Par exemple, si vous ne spécifiez pas les informations du réseau virtuel, un réseau virtuel est créé pour vous. Si vous les omettez, les éléments suivants sont créés pour vous : un équilibrage de charge, un réseau virtuel et une adresse IP publique.

Lorsque vous choisissez l’image de machine virtuelle à utiliser sur le groupe de machines virtuelles identiques, vous avez plusieurs choix :

1. URN  
L’identificateur d’une ressource :  
**Win2012R2Datacenter**.

2. Alias d’URN  
Nom convivial de l’URN :  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**.

3. ID de ressource personnalisé  
Le chemin d’accès à une ressource Azure :  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**.

4. Ressource web  
Le chemin d’accès à une URI HTTP :  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**.

>[!TIP]
>Vous pouvez obtenir la liste des images disponibles avec `az vm image list`.

Pour créer un groupe de machines virtuelles identiques, vous devez spécifier le _groupe de ressources_, le _nom_, _l’image du système d’exploitation_ et _les informations d’authentification_. L’exemple suivant crée un groupe de machines virtuelles identiques de base (cette étape peut prendre quelques minutes).

```azurecli
az vmss create --resource-group vmss-test-1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

## <a name="create-from-powershell"></a>Créer à partir de PowerShell

PowerShell est plus complexe à utiliser que l’interface CLI Azure. Alors que l’interface CLI Azure fournit les valeurs par défaut pour la mise en réseau des ressources connexes (équilibrage de charge, adresse IP, réseau virtuel), ce n’est pas le cas de PowerShell. La référence à une image est également un peu plus complexe. Vous pouvez obtenir des images avec les applets de commande suivantes :

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

Le travail des applets de commande peut être transmis dans la séquence. Voici un exemple d’obtention de toutes les images de la région **États-Unis de l’Ouest 2** dont l’éditeur a le nom **microsoft**.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

Le flux de travail pour la création d’un groupe de machines virtuelles identiques est le suivant :

1. Créez un objet de configuration qui conserve des informations sur le groupe identique.
2. Référencez l’image du système d’exploitation de base.
3. Configurez les paramètres du système d’exploitation : authentification, préfixe du nom de machine virtuelle, utilisateur/règle Pass.
4. Configurez la mise en réseau.
5. Créez le groupe identique.

Cet exemple crée un groupe identique de base à 2 instances avec Windows Server 2016 installé.

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location WestUS2 -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Setup information about how to authenticate with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName "vmss-test-1" -Location "westus2" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ip-address" -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step may take a few minutes)
New-AzureRmVmss -ResourceGroupName vmss-test-1 -Name my-scale-set -VirtualMachineScaleSet $vmssConfig
```

## <a name="create-from-a-template"></a>Créer à partir d’un modèle

Vous pouvez déployer un groupe de machines virtuelles identiques à l’aide d’un modèle Azure Resource Manager. Vous pouvez créer votre propre modèle, ou en utiliser un issu du [référentiel de modèles](https://azure.microsoft.com/resources/templates/?term=vmss). Ces modèles peuvent être déployés directement dans votre abonnement Azure.

>[!NOTE]
>Pour créer votre propre modèle, vous créez un fichier texte _.json_. Pour obtenir des informations générales sur la façon de créer et de personnaliser un modèle, consultez [Modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Un exemple de modèle est disponible [sur GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set). Pour plus d’informations sur la création et l’utilisation de cet exemple, consultez [Groupe identique viable minimal](.\virtual-machine-scale-sets-mvss-start.md).

## <a name="create-from-visual-studio"></a>Créer à partir de Visual Studio

Avec Visual Studio, vous pouvez créer un projet de groupe de ressources Azure et y ajouter un modèle de groupe de machines virtuelles identiques. Vous pouvez choisir le modèle que vous souhaitez importer, par exemple à partir de GitHub ou de la galerie Azure. Un script de déploiement PowerShell est également généré pour vous. Pour plus d’informations, consultez [Création d’un groupe de machines virtuelles identiques avec Visual Studio](virtual-machine-scale-sets-vs-create.md).

## <a name="create-from-the-azure-portal"></a>Créer à partir du portail Azure

Le portail Azure offre un moyen pratique de créer rapidement un groupe identique. Pour plus d’informations, consultez [Création d’un groupe de machines virtuelles identiques avec le portail Azure](virtual-machine-scale-sets-portal-create.md).

