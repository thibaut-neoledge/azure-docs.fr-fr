---
title: "Comment créer des images de machines virtuelles Microsoft Azure avec Packer | Microsoft Docs"
description: "Découvrez comment utiliser Packer pour créer des images de machines virtuelles Windows dans Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/13/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 035286e74e66cf1eb12a51551fa55a2a0ea5517c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Comment utiliser Packer pour créer des images de machines virtuelles Windows dans Azure
Chaque machine virtuelle dans Azure est créée à partir d’une image qui définit la distribution Windows et la version du système d’exploitation. Les images peuvent inclure des configurations et des applications pré-installées. La Place de marché Microsoft Azure fournit de nombreuses images internes et de tiers pour les systèmes d’exploitation et environnements d’application les plus courants. Vous pouvez également créer vos propres images personnalisées selon vos besoins. Cet article explique comment utiliser l’outil open source [Packer](https://www.packer.io/) pour définir et générer des images personnalisées dans Azure.


## <a name="create-supporting-azure-resources"></a>Création de ressources de prise en charge Azure
Pendant le processus de génération, Packer crée des ressources Azure temporaires lorsqu’il génère la machine virtuelle source. Pour capturer cette machine virtuelle source afin de l’utiliser en tant qu’image, vous devez définir un groupe de ressources et un compte de stockage. La sortie du processus de génération de Packer est stockée dans ce groupe de ressources et ce compte de stockage.

Commencez par créer un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

Créez ensuite un compte de stockage avec la commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Les noms des comptes de stockage doivent être uniques et comporter entre 3 et 24 caractères (uniquement des lettres minuscules et des chiffres). L’exemple qui suit permet de créer un compte de stockage nommé *mystorageaccount* :

```powershell
$storageAccountName = "mystorageaccount"
New-AzureRmStorageAccount -ResourceGroupName $rgName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"
```


## <a name="create-azure-credentials"></a>Création des informations d’identification Azure
Packer s’authentifie auprès d’Azure à l’aide d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme Packer. Vous contrôlez et définissez les opérations que le principal de service est autorisé à effectuer dans Azure.

Créez un principal de service avec la commande [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) et assignez au principal de service les autorisations requises pour créer et gérer des ressources avec la commande [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) :

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "Azure Packer" -Password "P@ssw0rd!"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Pour vous authentifier auprès d’Azure, vous devez également obtenir vos ID client et d’abonnement Azure à l’aide de la commande [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) :

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId
$sub.SubscriptionId
```

Vous utiliserez ces deux ID à l’étape suivante.


## <a name="define-packer-template"></a>Définition du modèle Packer
Pour générer les images, vous devez créer un modèle en tant que fichier JSON. Dans le modèle, vous définissez des générateurs et des fournisseurs pour mener à bien le processus de génération réel. Packer possède un [fournisseur pour Azure](https://www.packer.io/docs/builders/azure.html) qui vous permet de définir des ressources Azure, telles que les informations d’identification du principal de service créées à l’étape précédente.

Créez un fichier nommé *windows.json* et collez le contenu suivant : Saisissez vos propres valeurs comme suit :

| Paramètre       | Emplacement |
|-----------------|----------------------------------------------------|
| *client_id*      | Affichage de l’ID du principal de service avec `$sp.applicationId` |
| *client_secret*  | Mot de passe que vous avez spécifié dans `$securePassword` |
| *tenant_id*      | Sortie de la commande `$sub.TenantId` |
| *subscription_id* | Sortie de la commande `$sub.SubscriptionId` |
| *object_id*       | Affichage de l’ID d’objet du principal de service avec `$sp.Id` |
| *storage_account* | Nom que vous avez spécifié dans `$storageAccountName` |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": "true",
    "winrm_insecure": "true",
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force}",
      "& $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /shutdown /quiet"
    ]
  }]
}
```

Ce modèle génère une machine virtuelle Windows Server 2016, installe IIS, puis généralise la machine virtuelle avec Sysprep.


## <a name="build-packer-image"></a>Génération de l’image Packer
Si Packer n’est pas encore installé sur votre ordinateur local, [suivez les instructions d’installation de Packer](https://www.packer.io/docs/install/index.html).

Générez l’image en spécifiant votre fichier de modèle Packer comme suit :

```bash
./packer build windows.json
```

Voici un exemple de sortie issue des commandes précédentes :

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the certificate's URL ...
==> azure-arm:  -> Key Vault Name        : 'pkrkvbxpchwtl43'
==> azure-arm:  -> Key Vault Secret Name : 'packerKeyVaultSecret'
==> azure-arm:  -> Certificate URL       : 'https://pkrkvbxpchwtl43.vault.azure.net/secrets/packerKeyVaultSecret/6c12261b552c48ebadb7d4a88d99d011'
==> azure-arm: Setting the certificate's URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '40.121.160.214'
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /tmp/packer-powershell-provisioner759984171
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04"><Obj S="progress" RefId="0"><TN RefId="0"><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N="SourceId">1</
I64><PR N="Record"><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd?se=2017-07-13T22%3A25%3A02Z&sig=BYAbOXakavYV%2FbdWYfGqUIdsz2GXivbk0hxG0
5Mc09k%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json?se=2017-07-13T22%3A25%3A02Z&sig=wDMO3aSifbWLSISwoUOfkDMc5z7iKbGV
3us64gGvvlw%3D&sp=r&sr=b&sv=2015-02-21
```

La génération de la machine virtuelle, l’exécution des fournisseurs et le nettoyage du déploiement par Packer ne prennent que quelques minutes.


## <a name="create-azure-image"></a>Création de l’image Azure
La sortie du processus de génération de Packer est un disque dur virtuel (VHD, virtual hard disk) dans le compte de stockage spécifié. Créez une image Azure à partir de ce VHD avec la commande [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) et spécifiez le chemin d’accès `OSDiskUri` indiqué à la fin de la sortie de la génération de Packer. L’exemple suivant permet de créer une image nommée `myImage` :

```powershell
$osVhdUri = "https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd"
$imageName = "myImage"

$imageConfig = New-AzureRmImageConfig -Location $location
$imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig `
    -OsType "Windows" `
    -OsState "Generalized" `
    -BlobUri $osVhdUri
$image = New-AzureRmImage -ImageName $imageName `
    -ResourceGroupName $rgName `
    -Image $imageConfig
```

Cette image peut être utilisée pour créer des machines virtuelles dans votre abonnement Azure. Vous n’êtes pas obligé de créer une machine virtuelle dans le même groupe de ressources que votre image source.


## <a name="create-vm-from-azure-image"></a>Création d’une machine virtuelle à partir d’une image Azure
Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential).

```powershell
$cred = Get-Credential
```

Vous pouvez à présent créer une machine virtuelle à partir de votre image à l’aide de la commande [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant permet de créer une machine virtuelle nommée *myVM* à partir de *myImage*.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod "Static" `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```

La création de la machine virtuelle ne nécessite que quelques minutes.


## <a name="test-vm-and-iis"></a>Test de la machine virtuelle et d’IIS
Obtenez l’adresse IP publique de votre machine virtuelle avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* créée précédemment :

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIP" | select "IpAddress"
```

Vous pouvez alors entrer l’adresse IP publique dans un navigateur web.

![Site IIS par défaut](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, IIS était déjà installé et vous avez utilisé Packer pour créer une image de machine virtuelle. Vous pouvez utiliser cette image de machine virtuelle avec les flux de travail de déploiement existants, par exemple pour déployer votre application sur les machines virtuelles créées à partir de l’image avec Team Services, Ansible, Chef ou Puppet.

Pour obtenir d’autres exemples de modèles Packer pour d’autres distributions Windows, consultez [ce référentiel GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
