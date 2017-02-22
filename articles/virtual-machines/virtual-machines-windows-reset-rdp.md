---
title: "Réinitialisation du mot de passe ou de la configuration Bureau à distance sur une machine virtuelle Windows | Microsoft Docs"
description: "Découvrez comment réinitialiser un mot de passe de compte ou des services Bureau à distance sur une machine virtuelle Windows à l’aide du Portail Azure ou d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 8062106872bd50cd265fc4e64bc6f4c0fb2dbb1d
ms.openlocfilehash: b7458e13174dc03f2c4cab707c033d96a80628e6


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous ne pouvez pas vous connecter à une machine virtuelle Windows, vous pouvez réinitialiser le mot de passe d’administrateur local ou la configuration du service Bureau à distance. Vous pouvez utiliser le portail Azure ou l’extension d’accès aux machines virtuelles dans Azure PowerShell pour réinitialiser le mot de passe. Si vous utilisez PowerShell, assurez-vous d’installer le dernier module PowerShell sur votre ordinateur de travail et d’être connecté à votre abonnement Azure. Pour plus de détails sur la procédure, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!TIP]
> Vous pouvez vérifier la version de PowerShell que vous avez installée à l’aide de la commande :
>
> `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`


## <a name="ways-to-reset-configuration-or-credentials"></a>Comment réinitialiser la configuration ou les informations d’identification
Vous pouvez réinitialiser les services Bureau à distance et les informations d’identification de différentes manières, selon vos besoins. Pour les machines virtuelles créées à l’aide du modèle de déploiement Resource Manager :

- [Réinitialisation à l’aide du portail Azure](#azure-portal---resource-manager)
- [Réinitialisation à l’aide d’Azure PowerShell](#vmaccess-extension-and-powershell---resource-manager)

Pour les machines virtuelles créées à l’aide du modèle de déploiement Classic :

- [Réinitialisation à l’aide du portail Azure](#azure-portal---classic)
- [Réinitialisation à l’aide d’Azure PowerShell](#vmaccess-extension-and-powershell---classic)

## <a name="azure-portal---resource-manager"></a>Portail Azure - Resource Manager
Pour développer le menu du portail, cliquez sur les trois barres dans l’angle supérieur gauche, puis sur **Machines virtuelles** :

![Accédez à votre machine virtuelle Azure](./media/virtual-machines-windows-reset-rdp/Portal-Select-VM.png)

Sélectionnez votre machine virtuelle Windows, puis cliquez sur **Support + dépannage** > **Réinitialiser le mot de passe**. Le panneau de réinitialisation du mot de passe s’affiche :

![Page Réinitialiser le mot de passe](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Entrez le nom d’utilisateur et un nouveau mot de passe, puis cliquez sur **Enregistrer**. Essayez à nouveau de vous connecter à votre machine virtuelle.


## <a name="vmaccess-extension-and-powershell---resource-manager"></a>Extension VMAccess et PowerShell - Resource Manager
Assurez-vous qu’Azure PowerShell 1.0 ou une version ultérieure est installé et que vous vous êtes connecté à votre compte à l’aide de l’applet de commande `Login-AzureRmAccount` .

### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe de compte d’administrateur local**
Vous pouvez réinitialiser le mot de passe ou le nom d’utilisateur de l’administrateur à l’aide de la commande PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Créez les informations d’identification de votre compte d’administrateur local à l’aide de la commande suivante :

```powershell
$cred=Get-Credential
```

Si vous tapez un nom différent de celui du compte actuel, l’extension VMAccess suivante renomme le compte d’administrateur local, affecte le mot de passe à ce compte et envoie une commande de fermeture de la session Bureau à distance. Si le compte d’administrateur local est désactivé, l’extension VMAccess l’active.

Utilisez l’extension VMAccess pour définir les nouvelles informations d’identification comme suit :

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Remplacez `myResourceGroup`, `myVM`, `myVMAccess` et l’emplacement par des valeurs adaptées à votre installation.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**
Vous pouvez réinitialiser l’accès à distance à votre machine virtuelle en utilisant [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) comme suit. (Remplacez `myResourceGroup`, `myVM`, `myVMAccess` et l’emplacement par vos propres valeurs.)

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
    -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"
```

Ou : 

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0
```

> [!TIP]
> Les deux commandes ajoutent un nouvel agent d’accès de machine virtuelle à la machine virtuelle. En toutes circonstances, une machine virtuelle ne peut avoir qu’un seul agent d’accès de machine virtuelle. Pour pouvoir définir les propriétés de l’agent d’accès à la machine virtuelle, supprimez l’agent d’accès défini précédemment à l’aide de `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. 
>
> À partir de la version 1.2.2 d’Azure PowerShell, vous pouvez ignorer cette étape si vous utilisez `Set-AzureRmVMExtension` avec une option `-ForceRerun`. Si vous utilisez `-ForceRerun`, assurez-vous d’utiliser le même nom pour l’agent d’accès à la machine virtuelle que celui défini par la commande précédente.

Si vous ne pouvez toujours pas vous connecter à distance à votre machine virtuelle, consultez les étapes supplémentaires dans la rubrique [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="azure-portal---classic"></a>Portail Azure - Classic
Pour des machines virtuelles créées à l’aide du modèle de déploiement Classic, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le service Bureau à distance. Pour développer le menu du portail, cliquez sur les trois barres dans l’angle supérieur gauche, puis sur **Machines virtuelles (classiques)** :

![Accédez à votre machine virtuelle Azure](./media/virtual-machines-windows-reset-rdp/Portal-Select-Classic-VM.png)

Sélectionnez votre machine virtuelle Windows, puis cliquez sur **Réinitialiser... à distance**. La boîte de dialogue suivante apparaît pour réinitialiser la configuration du Bureau à distance :

![Page Réinitialiser la configuration RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Vous pouvez également réinitialiser le nom et le mot de passe du compte d’administrateur local. À partir de votre machine virtuelle, cliquez sur **Support + dépannage** > **Réinitialiser le mot de passe**. Le panneau de réinitialisation du mot de passe s’affiche :

![Page Réinitialiser le mot de passe](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Après avoir entré le nom d’utilisateur et le mot de passe, cliquez sur **Enregistrer**.

## <a name="vmaccess-extension-and-powershell---classic"></a>Extension VMAccess et PowerShell - Classic
Assurez-vous que l’agent de machine virtuelle est installé sur la machine virtuelle. L’extension VMAccess n’a pas besoin d’être installée avant d’être utilisée, du moment que l’agent de machine virtuelle est disponible. Vérifiez que l’agent de machine virtuelle est déjà installé à l’aide de la commande suivante. (Remplacez « myCloudService » et « myVM » par les noms de votre service cloud et de votre machine virtuelle, respectivement. Pour obtenir ces noms, exécutez `Get-AzureVM` sans paramètre.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Si la commande **write-host** affiche la valeur **True**, cela signifie que l’agent de machine virtuelle est installé. Si la commande présente la valeur **False**, accédez aux instructions et au lien de téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) (en anglais).

Si vous avez créé la machine virtuelle à l’aide du portail, vérifiez que `$vm.GetInstance().ProvisionGuestAgent` retourne **True**. Si ce n’est pas le cas, définissez cette valeur à l’aide de cette commande :

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Cette commande empêche l’erreur suivante de se produire quand vous exécutez la commande **Set-AzureVMExtension** au cours des étapes suivantes : « L’agent invité d’approvisionnement doit être activé sur l’objet Machine virtuelle avant de définir l’extension VMAccess IaaS ».

### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe de compte d’administrateur local**
Créez des informations d’identification de connexion avec le nom du compte d’administrateur local actuel et un nouveau mot de passe, puis exécutez la commande `Set-AzureVMAccessExtension` comme suit.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Si vous tapez un nom différent de celui du compte actuel, l’extension VMAccess renomme le compte d’administrateur local, affecte le mot de passe à ce compte et envoie une commande de déconnexion du Bureau à distance. Si le compte d’administrateur local est désactivé, l’extension VMAccess l’active.

Ces commandes réinitialisent également la configuration du service Bureau à distance.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**
Pour réinitialiser la configuration du service Bureau à distance, exécutez la commande suivante :

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

L’extension VMAccess exécute deux commandes sur la machine virtuelle :

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Cette commande active le groupe de pare-feu Windows intégré qui autorise le trafic entrant du Bureau à distance, qui utilise le port TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Cette commande définit la valeur de registre fDenyTSConnections sur 0, pour activer les connexions Bureau à distance.

## <a name="next-steps"></a>Étapes suivantes
Si l’extension d’accès aux machines virtuelles Azure ne répond pas et que vous ne pouvez pas réinitialiser le mot de passe, vous pouvez [réinitialiser le mot de passe Windows local en mode hors connexion](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette méthode est plus avancée et nécessite que vous connectiez le disque dur virtuel de la machine virtuelle problématique à une autre machine virtuelle. Suivez d’abord la procédure décrite dans cet article et n’utilisez la méthode de réinitialisation du mot de passe en mode hors connexion qu’en dernier recours.

[Extensions et fonctionnalités des machines virtuelles Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO3-->


