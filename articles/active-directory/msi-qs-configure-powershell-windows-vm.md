---
title: "Comment configurer la MSI sur une machine virtuelle Azure à l’aide de PowerShell"
description: "Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide de PowerShell."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurer une identité du service administré (MSI) de machine virtuelle à l’aide de PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité automatiquement gérée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de tout service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprenez à activer et à supprimer la MSI pour une machine virtuelle Windows Azure, à l’aide de PowerShell.

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Installez également [Azure PowerShell version 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) si vous ne l’avez pas déjà fait.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure

Une nouvelle ressource de l’ordinateur virtuel Windows compatible avec l’identité du service administré est créée dans un nouveau groupe de ressources, à l’aide des paramètres de configuration spécifiés. Notez également que nombre de ces cmdlets peuvent s’exécuter au minimum pendant 30 secondes avant de renvoyer des résultats et que la création de machine virtuelle finale prend plusieurs minutes.

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure dans lequel vous souhaitez déployer la machine virtuelle.

   ```powershell
   Login-AzureRmAccount
   ```

2. Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#terminology) destiné à l’imbrication et au déploiement de votre machine virtuelle et de ses ressources connexes à l’aide de la cmdlet `New-AzureRmResourceGroup`. Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de ressources que vous souhaitez utiliser à la place :

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. Créez des ressources réseau pour la machine virtuelle.

   a. Créez un réseau virtuel, un sous-réseau et une adresse IP publique. Ces ressources sont utilisées pour fournir une connectivité réseau à la machine virtuelle et la connecter à Internet :

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. Créez un groupe de sécurité réseau et une règle de groupe de sécurité réseau. Le groupe de sécurité réseau permet sécurise la machine virtuelle avec des règles entrantes et sortantes. Dans ce cas, une règle entrante est créée pour le port 3389, qui autorise les connexions Bureau à distance entrantes. Nous souhaitons également créer une règle entrante pour le port 80, qui autorise le trafic web entrant :

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. Créez une carte réseau virtuelle pour la machine virtuelle. La carte réseau connecte la machine virtuelle à un sous-réseau, un groupe de sécurité réseau et une adresse IP publique :

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. Créez la machine virtuelle.

   a. Créez un objet de machine virtuelle pouvant être configuré. Ces paramètres sont utilisés lors du déploiement de la machine virtuelle, comme une image de machine virtuelle, la taille et la configuration de l’authentification. Le paramètre `-IdentityType "SystemAssigned"` utilisé dans la cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) entraîne l’approvisionnement de la machine virtuelle avec une MSI. La cmdlet `Get-Credential` vous invite à saisir des informations d’identification, qui sont configurées en tant que nom d’utilisateur et mot de passe pour la machine virtuelle :

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. Approvisionnez la nouvelle machine virtuelle :

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. Ajoutez l’extension de machine virtuelle de la MSI à l’aide du paramètre `-Type "ManagedIdentityExtensionForWindows"` sur la cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Activer l’identité du service administré sur une machine virtuelle Azure existante

Si vous devez activer l’identité du service administré sur une machine virtuelle existante :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure dans lequel vous souhaitez déployer la machine virtuelle :

   ```powershell
   Login-AzureRmAccount
   ```

2. Commencez par récupérer les propriétés de la machine virtuelle à l’aide de la cmdlet `Get-AzureRmVM`. Ensuite, pour activer la MSI, utilisez l’option `-IdentityType` de la cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) :

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Ajoutez l’extension de machine virtuelle de la MSI à l’aide du paramètre `-Type "ManagedIdentityExtensionForWindows"` sur la cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton. Veillez à indiquer le paramètre `-Location` approprié, correspondant à l’emplacement de la machine virtuelle existante :

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle pour laquelle la MSI n’est plus nécessaire, vous pouvez utiliser la cmdlet `RemoveAzureRmVMExtension` pour supprimer la MSI de la machine virtuelle :

1. Utilisez l’option `-Name "ManagedIdentityExtensionForWindows"` avec la cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) :

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Contenu connexe

- [Vue d’ensemble de l’identité du service administré](msi-overview.md)
- Cet article est une adaptation du démarrage rapide [Créer une machine virtuelle Windows avec PowerShell](../virtual-machines/windows/quick-create-powershell.md), modifié pour inclure des instructions spécifiques à la MSI. 

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

















