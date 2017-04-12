---
title: "Réinitialisation du mot de passe ou de la configuration Bureau à distance sur une machine virtuelle Windows dans Azure | Microsoft Docs"
description: "Découvrez comment réinitialiser un mot de passe de compte ou des services Bureau à distance sur une machine virtuelle Windows créée avec le modèle de déploiement Classic à l’aide du portail Azure ou d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 43e5cf1ab3bc3121d7e3915ea0785998e0ee2fc6
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows créée avec le modèle de déploiement Classic
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Vous pouvez également [effectuer ces étapes pour les machines virtuelles créées à l’aide du modèle de déploiement Resource Manager](../reset-rdp.md).

Si vous ne pouvez pas vous connecter à une machine virtuelle Windows, vous pouvez réinitialiser le mot de passe d’administrateur local ou la configuration du service Bureau à distance. Vous pouvez utiliser le portail Azure ou l’extension d’accès aux machines virtuelles dans Azure PowerShell pour réinitialiser le mot de passe.

## <a name="ways-to-reset-configuration-or-credentials"></a>Comment réinitialiser la configuration ou les informations d’identification
Vous pouvez réinitialiser les services Bureau à distance et les informations d’identification de différentes manières, selon vos besoins :

- [Réinitialisation à l’aide du portail Azure](#azure-portal)
- [Réinitialisation à l’aide d’Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portail Azure
Vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le service Bureau à distance. Pour développer le menu du portail, cliquez sur les trois barres dans l’angle supérieur gauche, puis sur **Machines virtuelles (classiques)** :

![Accédez à votre machine virtuelle Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Sélectionnez votre machine virtuelle Windows, puis cliquez sur **Réinitialiser... à distance**. La boîte de dialogue suivante apparaît pour réinitialiser la configuration du Bureau à distance :

![Page Réinitialiser la configuration RDP](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Vous pouvez également réinitialiser le nom et le mot de passe du compte d’administrateur local. À partir de votre machine virtuelle, cliquez sur **Support + dépannage** > **Réinitialiser le mot de passe**. Le panneau de réinitialisation du mot de passe s’affiche :

![Page Réinitialiser le mot de passe](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Après avoir entré le nom d’utilisateur et le mot de passe, cliquez sur **Enregistrer**.

## <a name="vmaccess-extension-and-powershell"></a>Extension VMAccess et PowerShell
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
Si l’extension d’accès aux machines virtuelles Azure ne répond pas et que vous ne pouvez pas réinitialiser le mot de passe, vous pouvez [réinitialiser le mot de passe Windows local en mode hors connexion](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette méthode est plus avancée et nécessite que vous connectiez le disque dur virtuel de la machine virtuelle problématique à une autre machine virtuelle. Suivez d’abord la procédure décrite dans cet article et n’utilisez la méthode de réinitialisation du mot de passe en mode hors connexion qu’en dernier recours.

[Extensions et fonctionnalités des machines virtuelles Azure](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


