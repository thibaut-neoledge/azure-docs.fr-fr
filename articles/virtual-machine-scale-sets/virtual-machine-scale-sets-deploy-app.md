---
title: "Déployer une application sur des groupes de machines virtuelles identiques"
description: "Utilisez des extensions pour déployer une application sur des groupes de machines virtuelles identiques Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fa7d9d3bef4cb326844ede76171e8c566e87116b
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Déployer votre application sur des groupes de machines virtuelles identiques

Cet article décrit les différentes façons d’installer un logiciel au moment où le groupe identique est approvisionné.

Vous pouvez consulter l’article [Vue d’ensemble de la conception des groupes identiques](virtual-machine-scale-sets-design-overview.md), qui décrit certaines des limites imposées par les groupes de machines virtuelles identiques.

## <a name="capture-and-reuse-an-image"></a>Capturer et réutiliser une image

Vous pouvez utiliser une machine virtuelle que vous avez dans Azure pour préparer une image de base pour votre groupe identique. Ce processus crée un disque managé dans votre compte de stockage, que vous pouvez référencer comme image de base pour votre groupe identique. 

Effectuez également les étapes suivantes :

1. Créer une machine virtuelle Azure
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. Connectez-vous à distance à la machine virtuelle et personnalisez le système à votre convenance.

   Si vous le souhaitez, vous pouvez installer votre application maintenant. Notez cependant qu’en installant votre application maintenant, vous pouvez rendre sa mise à niveau plus complexe, car vous devrez peut-être d’abord la supprimer. Au lieu de cela, vous pouvez utiliser cette étape pour installer les prérequis nécessaires à votre application, comme un runtime spécifique ou une fonctionnalité du système d’exploitation.

3. Suivez le didacticiel « Capturer une machine » pour [Linux][linux-vm-capture] ou pour [Windows][windows-vm-capture].

4. Créez un [groupe de machines virtuelles identiques][vmss-create] avec l’URI de l’image que vous avez capturée à l’étape précédente.

Pour plus d’informations sur les disques, consultez [Vue d’ensemble de Managed Disks](../virtual-machines/windows/managed-disks-overview.md) et [Utiliser des disques de données attachés](virtual-machine-scale-sets-attached-disks.md).

## <a name="install-when-the-scale-set-is-provisioned"></a>Installer quand le groupe identique est approvisionné

Les extensions de machine virtuelle peuvent être appliquées à un groupe de machines virtuelles identiques. Avec une extension de machine virtuelle, vous pouvez personnaliser les machines virtuelles d’un groupe identique comme un tout. Pour plus d’informations sur les extensions, consultez [Extensions de machine virtuelle](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Il existe trois extensions principales que vous pouvez utiliser, selon que votre système d’exploitation est Linux ou Windows.

### <a name="windows"></a>Windows

Pour un système d’exploitation Windows, utilisez l’extension **Script personnalisé 1.8** ou l’extension **DSC PowerShell**.

#### <a name="custom-script"></a>Script personnalisé

L’extension Script personnalisé exécute un script sur chaque instance de machine virtuelle dans le groupe identique. Un fichier de configuration ou une variable indique quels fichiers sont téléchargés sur la machine virtuelle, puis quelle commande exécuter. Vous pouvez l’utiliser par exemple pour exécuter un programme d’installation, un script, un fichier de commandes ou un fichier exécutable.

PowerShell utilise une table de hachage pour les paramètres. Cet exemple configure l’extension Script personnalisé pour exécuter un script PowerShell qui installe IIS.

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Utilisez le commutateur `-ProtectedSetting` pour les paramètres qui peuvent contenir des informations sensibles.

---------


Azure CLI utilise un fichier JSON pour les paramètres. Cet exemple configure l’extension Script personnalisé pour exécuter un script PowerShell qui installe IIS. Enregistrez le fichier JSON suivant sous _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

Ensuite, exécutez cette commande Azure CLI.

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Utilisez le commutateur `--protected-settings` pour les paramètres qui peuvent contenir des informations sensibles.

### <a name="powershell-dsc"></a>DSC PowerShell

Vous pouvez utiliser DSC PowerShell pour personnaliser les instances de machine virtuelle du groupe identique. L’extension **DSC** publiée par **Microsoft.Powershell** déploie et exécute la configuration DSC fournie sur chaque instance de machine virtuelle. Une variable ou un fichier de configuration indique l’extension où se trouve le package *.zip* et quelle combinaison _script-fonction_ exécuter.

PowerShell utilise une table de hachage pour les paramètres. Cet exemple déploie un package DSC qui installe IIS.

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Utilisez le commutateur `-ProtectedSetting` pour les paramètres qui peuvent contenir des informations sensibles.

-----------

Azure CLI utilise un fichier JSON pour les paramètres. Cet exemple déploie un package DSC qui installe IIS. Enregistrez le fichier JSON suivant sous _settings.json_.

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

Ensuite, exécutez cette commande Azure CLI.

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Utilisez le commutateur `--protected-settings` pour les paramètres qui peuvent contenir des informations sensibles.

### <a name="linux"></a>Linux

Linux peut utiliser l’extension **Script personnalisé v2.0** ou utiliser **cloud-init** lors de la création.

Un script personnalisé est une extension simple qui télécharge des fichiers sur les instances de machine virtuelle et exécute une commande.

#### <a name="custom-script"></a>Script personnalisé

Enregistrez le fichier JSON suivant sous _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Utilisez Azure CLI pour ajouter cette extension à un groupe de machines virtuelles identiques. Chaque machine virtuelle du groupe identique exécute automatiquement l’extension.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Utilisez le commutateur `--protected-settings` pour les paramètres qui peuvent contenir des informations sensibles.

#### <a name="cloud-init"></a>Cloud-Init

Cloud-Init est utilisé lors de la création du groupe identique. Créez d’abord un fichier local nommé _cloud-init.txt_ et ajoutez-y votre configuration. Par exemple, consultez [ce Gist](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt)

Utilisez Azure CLI pour créer un groupe identique. Le champ `--custom-data` accepte le nom de fichier d’un script cloud-init.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>Comment gérer les mises à jour de l’application ?

Si vous avez déployé votre application via une extension, modifiez quelque chose dans la définition de l’extension. Cette modification entraîne le redéploiement de l’extension sur toutes les instances de machine virtuelle. Vous **devez** modifier quelque chose qui concerne l’extension, par exemple renommer un fichier référencé. Sinon, Azure ne détecte pas que l’extension a été modifiée.

Si vous avez intégré l’application de votre propre image du système d’exploitation, utilisez un pipeline de déploiement automatisé pour les mises à jour de l’application. Concevez votre architecture de façon à faciliter le passage rapide en production d’un groupe identique intermédiaire. Un bon exemple de cette approche est le [fonctionnement du pilote Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

[Packer](https://www.packer.io/) et [Terraform](https://www.terraform.io/) prennent en charge Azure Resource Manager. Vous pouvez donc également définir vos images « en tant que code » et les générer dans Azure, puis utiliser le disque dur virtuel dans votre groupe identique. Cependant, cette opération devient problématique pour les images de place de marché, pour lesquelles les extensions / scripts personnalisés deviennent plus importants, car vous ne manipulez pas directement les éléments exécutables de la Place de marché.

## <a name="what-happens-when-a-scale-set-scales-out"></a>Que se passe-t-il quand le nombre de machines virtuelles d’un groupe de machines virtuelles identiques augmente ?
Quand vous ajoutez une ou plusieurs machines virtuelles à un groupe identique, l’application est automatiquement installée. Par exemple, si des extensions sont définies pour le groupe identique, elles s’exécutent sur une nouvelle machine virtuelle chaque fois qu’elle est créée. Si le groupe identique est basé sur une image personnalisée, toute nouvelle machine virtuelle est une copie de l’image personnalisée source. Si les machines virtuelles du groupe identique sont des hôtes de conteneurs, vous pouvez avoir du code exécuté au démarrage pour charger les conteneurs dans une extension de script personnalisé. Une extension peut aussi installer un agent qui s’inscrit auprès d’un orchestrateur de cluster, comme Azure Container Service.


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Comment déployer une mise à jour du système d’exploitation sur plusieurs domaines de mise à jour ?
Supposons que vous voulez mettre à jour votre image de système d’exploitation tout en conservant le groupe de machines virtuelles identiques en cours d’exécution. PowerShell et Azure CLI peuvent mettre à jour les images de machine virtuelle, une seule machine virtuelle à la fois. L’article [Mettre à niveau un groupe de machines virtuelles identiques](./virtual-machine-scale-sets-upgrade-scale-set.md) fournit également plus d’informations sur les options disponibles pour effectuer une mise à niveau du système d’exploitation sur un groupe de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisez PowerShell pour gérer votre groupe identique](virtual-machine-scale-sets-windows-manage.md).
* [Créez un modèle de groupe identique](virtual-machine-scale-sets-mvss-start.md).


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md


