---
title: "Créer et gérer dans Azure des machines virtuelles Windows utilisant plusieurs cartes d’interface réseau | Microsoft Docs"
description: "Découvrez comment créer et gérer une machine virtuelle Windows équipée de plusieurs cartes d’interface réseau à l’aide d’Azure PowerShell ou de modèles Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/05/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 92f5181dbf36ef0f7e2568d557faa7c5f2144ad9
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Créer et gérer une machine virtuelle Windows équipée de plusieurs cartes d’interface réseau
Les machines virtuelles (VM) dans Azure peuvent être équipées de plusieurs cartes d’interface réseau (NIC) virtuelles. Un scénario courant consiste à avoir des sous-réseaux différents pour les connectivités frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article explique comment créer une machine virtuelle équipée de plusieurs cartes d’interface réseau. Il explique également comment ajouter ou supprimer des cartes d’interface réseau d’une machine virtuelle existante. Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](sizes.md) , pensez à dimensionner la vôtre en conséquence.

Pour plus d’informations, notamment sur la création de plusieurs cartes d’interface réseau dans vos propres scripts PowerShell, voir [Déploiement de machines virtuelles avec plusieurs cartes d’interface réseau](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="prerequisites"></a>Composants requis
Vérifiez que vous disposez de la [dernière version d’Azure PowerShell installée et configurée](/powershell/azure/overview).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myVnet* et *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Créer une machine virtuelle avec plusieurs cartes d’interface réseau
Créez d’abord un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *EastUs*:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Créer un réseau virtuel et des sous-réseaux
Un scénario courant pour un réseau virtuel consiste à avoir deux sous-réseaux ou plus. Un sous-réseau peut être dédié au trafic frontal, et l’autre au trafic principal. Pour connecter les deux sous-réseaux, vous utilisez ensuite plusieurs cartes d’interface réseau sur votre machine virtuelle.

1. Définissez deux sous-réseaux de réseau virtuel avec la commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L’exemple suivant définit les sous-réseaux pour *mySubnetFrontEnd* et *mySubnetBackEnd* :

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Créez votre réseau virtuel et vos sous-réseaux avec la commande [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *myVnet* :

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Créer plusieurs cartes réseau
Créez deux cartes d’interface réseau avec la commande [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Associez une carte d’interface réseau au sous-réseau frontal et l’autre au sous-réseau principal. L’exemple suivant crée des cartes d’interface réseau nommées *myNic1* et *myNic2* :

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

En général, vous créez également un [groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../../load-balancer/load-balancer-overview.md) pour faciliter la gestion et la répartition du trafic entre vos machines virtuelles. L’article [Machines virtuelles équipées de plusieurs cartes d’interface réseau](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) explique comment créer un groupe de sécurité réseau et affecter des cartes d’interface réseau.

### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle
Maintenant, commencez à élaborer la configuration de votre machine virtuelle. La taille d’une machine virtuelle détermine le nombre maximal de cartes réseau qu’elle peut accueillir. Pour plus d’informations, voir [Tailles des machines virtuelles Windows](sizes.md).

1. Tout d’abord, définissez les informations d’identification de votre machine virtuelle sur la variable `$cred` comme suit :

    ```powershell
    $cred = Get-Credential
    ```

2. Définissez votre machine virtuelle avec la commande [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). L’exemple suivant définit une machine virtuelle nommée *myVM* et utilise une taille de machine virtuelle qui prend en charge plus de deux cartes d’interface réseau (*Standard_DS3_v2*) :

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Créez le reste de la configuration de votre machine virtuelle avec les commandes [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) et [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). L’exemple suivant crée une machine virtuelle Windows Server 2016 R2 :

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Associez les deux cartes d’interface réseau que vous avez créées précédemment à l’aide de la commande [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) :

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Enfin, créez votre machine virtuelle avec la commande [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) :

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>Ajouter une carte réseau à une machine virtuelle existante
Pour ajouter une carte d’interface réseau virtuelle à une machine virtuelle existante, désallouez la machine virtuelle, ajoutez la carte d’interface réseau virtuelle, puis démarrez la machine virtuelle.

1. Désallouez la machine virtuelle avec la commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L’exemple suivant désalloue la machine virtuelle nommée *myVM* dans *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenez la configuration existante de la machine virtuelle avec commande [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). L’exemple suivant récupère des informations sur la machine virtuelle nommée *myVM* dans *myResourceGroup* :

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. L’exemple suivant crée une carte d’interface réseau virtuelle avec la commande [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface), nommée *myNic3*, et associée à *mySubnetBackEnd*. La carte d’interface réseau virtuelle est ensuite associée à la machine virtuelle nommée *myVM* dans *myResourceGroup* avec la commande [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) :

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Cartes d’interface réseau virtuelles principales
    L’une des cartes d’interface réseau sur une machine virtuelle dotées de plusieurs cartes doit être la carte principale. Si l’une des cartes d’interface réseau virtuelles existantes sur la machine virtuelle est déjà définie comme principale, vous pouvez ignorer cette étape. L’exemple suivant suppose que deux cartes d’interface réseau virtuelles sont désormais présentes sur une machine virtuelle, et que vous souhaitez ajouter la première carte d’interface réseau (`[0]`) en tant que carte principale :
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Démarrez la machine virtuelle avec la commande [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm) :

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>Supprimer une carte réseau d’une machine virtuelle existante
Pour supprimer une carte d’interface réseau virtuelle à partir d’une machine virtuelle existante, désallouez la machine virtuelle, supprimez la carte d’interface réseau virtuelle, puis démarrez la machine virtuelle.

1. Désallouez la machine virtuelle avec la commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L’exemple suivant désalloue la machine virtuelle nommée *myVM* dans *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenez la configuration existante de la machine virtuelle avec commande [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). L’exemple suivant récupère des informations sur la machine virtuelle nommée *myVM* dans *myResourceGroup* :

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obtenez des informations sur la suppression de la carte d’interface réseau avec la commande [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). L’exemple suivant obtient des informations sur la carte *myNic3* :

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Supprimez la carte d’interface réseau avec la commande [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface), puis mettez à jour la machine virtuelle avec la commande [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). L’exemple suivant supprime la carte *myNic3* obtenue par `$nicId` à l’étape précédente :

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Démarrez la machine virtuelle avec la commande [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm) :

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Créer plusieurs cartes d’interface réseau avec des modèles
Grâce aux modèles Azure Resource Manager, vous pouvez créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes d’interface réseau. Les modèles Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Pour plus d’informations, voir [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Vous pouvez utiliser la commande *copy* pour spécifier le nombre d’instances à  :

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Pour plus d’informations, voir [Création de plusieurs instances à l’aide de la commande *copie*](../../resource-group-create-multiple.md). 

Vous pouvez également utiliser la commande `copyIndex()` pour ajouter un numéro à un nom de ressource. Vous pouvez ensuite créer les cartes *myNic1*, *MyNic2* et ainsi de suite. Voici un exemple de code pour l’ajout de la valeur d’index :

```json
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de la [création de plusieurs cartes d’interface réseau à l’aide de modèles Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Réviser les [Tailles des machines virtuelles Windows](sizes.md) lorsque vous tentez de créer une machine virtuelle équipée de plusieurs cartes d’interface réseau. Faites attention au nombre maximal de cartes d’interface réseau pris en charge par chaque taille de machine virtuelle. 



