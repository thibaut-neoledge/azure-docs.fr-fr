<properties
   pageTitle="manage-vms-azure-powershell"
   description="Gérer vos machines virtuelles à l'aide d'Azure PowerShell"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Gérer vos machines virtuelles à l'aide d'Azure PowerShell

Avant de commencer, vous devez vérifier qu'Azure PowerShell est installé. Pour ce faire, visitez la page [Installation et configuration d'Azure PowerShell](install-configure-powershell.md)

## Obtenir une image

Avant de créer une machine virtuelle, vous devez décider **quelle image utiliser**. Vous pouvez obtenir la liste des images à l'aide de l'applet de commande suivante :

      Get-AzureVMImage

Cette applet de commande retourne la liste de toutes les images disponibles dans Azure. Cette liste étant très longue, il peut être difficile de trouver l'image exacte à utiliser. Dans l'exemple ci-dessous, j'utilise d'autres applets de commande PowerShell pour limiter la liste des images retournées à celles contenant **Windows Server 2012 R2 Datacenter**. En outre, je choisis d'obtenir uniquement la dernière image en spécifiant [-1] pour le tableau d'images retourné.

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## Créer une machine virtuelle

Pour créer une machine virtuelle, vous devez d'abord utiliser l'applet de commande **New-AzureVMConfig**. Vous y spécifiez le **nom** de la machine virtuelle, sa **taille** et l'**image** à utiliser pour celle-ci. Cette applet de commande crée un objet de machine virtuelle local **$myVM**, qui sera modifié ultérieurement à l'aide d'autres applets de commande Azure PowerShell dans ce guide.

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

Ensuite, vous devrez choisir le **nom d'utilisateur** et le **mot de passe** pour votre machine virtuelle. Pour ce faire, vous pouvez utiliser l'applet de commande **Add-AzureProvisioningConfig**. C'est dans celle-ci que vous indiquez à Azure le système d'exploitation de la machine virtuelle. Notez que c'est toujours l'objet **$myVM** local auquel vous apportez des modifications.

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

Enfin, vous êtes prêt à faire tourner votre machine virtuelle sur Azure. Pour cela, vous devez utiliser l'applet de commande **New-AzureVM**.

> [AZURE.NOTE] Vous devrez configurer le service cloud avant de créer votre machine virtuelle. Il existe deux façons d'effectuer cette opération.
* Créer le service cloud à l'aide de l'applet de commande New-AzureService. Si vous choisissez cette méthode, vous devez vous assurer que l'emplacement spécifié dans l'applet de commande New-AzureVM ci-dessous correspond à l'emplacement de votre service cloud, sinon l'exécution de l'applet de commande New-AzureVM échoue.
* Laisser l'applet de commande New-AzureVM faire l'opération à votre place. Vous devez vous assurer que le nom du service est unique. Dans le cas contraire, l'exécution de l'applet de commande New-AzureVM échoue.

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**FACULTATIF**

Vous pouvez utiliser d'autres applets de commande telles qu'**Add-AzureDataDisk** ou **Add-AzureEndpoint** pour configurer des options supplémentaires pour votre machine virtuelle.

## Obtenir une machine virtuelle
La machine virtuelle étant créée sur Azure, vous voudrez certainement la voir fonctionner. Pour ce faire, vous pouvez utiliser l'applet de commande **Get-AzureVM**, comme indiqué ci-dessous.

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## Étapes suivantes
[Connexion à une machine virtuelle Azure avec RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[FAQ sur les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
