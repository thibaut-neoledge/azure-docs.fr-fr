<properties
	pageTitle="Réinitialisation du mot de passe ou du Bureau à distance sur une machine virtuelle Windows | Microsoft Azure"
	description="Réinitialisez le mot de passe administrateur ou les services Bureau à distance sur une machine virtuelle Windows créée avec le modèle de déploiement du Gestionnaire des ressources."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="dkshir"/>

# Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Azure basée sur Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


Si vous ne pouvez pas vous connecter à une machine virtuelle Windows en raison d’un oubli de mot de passe ou d’un problème avec la configuration du service Bureau à distance, cet article vous explique comment réinitialiser le mot de passe de l’administrateur local ou la configuration du service Bureau à distance.

Selon le modèle de déploiement de votre machine virtuelle, vous pouvez utiliser le portail ou l’extension VMAccess dans Azure PowerShell. Si vous utilisez Azure PowerShell, assurez-vous d’installer le dernier module Azure PowerShell sur votre ordinateur de travail et d’être connecté à votre abonnement Azure. Pour plus de détails sur la procédure, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


> [AZURE.TIP] Vous pouvez vérifier la version d’Azure PowerShell que vous avez installée avec la commande `Get-Module azure | format-table version`.


## Machines virtuelles Windows dans le modèle de déploiement classique

### Portail Azure

Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le service Bureau à distance. Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Windows* > **Réinitialiser l’accès à distance**. La page suivante apparaît.


![](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Vous pouvez également essayer de réinitialiser le nom et le mot de passe du compte d’administrateur local. Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Windows* > **Tous les paramètres** > **Réinitialiser le mot de passe**. La page suivante apparaît.

![](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Cliquez sur **Enregistrer** après avoir entré le nouveau nom d’utilisateur et le mot de passe.

### Extension VMAccess et PowerShell

Assurez-vous que l’agent de machine virtuelle est installé sur la machine virtuelle. L’extension VMAccess n’a pas besoin d’être installée avant d’être utilisée, du moment que l’agent de machine virtuelle est disponible. Vérifiez que l’agent de machine virtuelle est déjà installé à l’aide de la commande suivante. Remplacez « myCloudService » et « myVM » par les noms de votre service cloud et de votre machine virtuelle, respectivement. Pour rechercher ces noms, exécutez `Get-AzureVM` sans paramètre.

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Si la commande **write-host** affiche la valeur **True**, cela signifie que l’agent de machine virtuelle est installé. Si la commande présente la valeur **False**, accédez aux instructions et au lien de téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) (en anglais).

Si vous avez créé la machine virtuelle avec le portail, vérifiez que `$vm.GetInstance().ProvisionGuestAgent` retourne **True**. Sinon, définissez cette valeur à l’aide de cette commande :

	$vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l’erreur « L’agent invité d’approvisionnement doit être activé sur l’objet VM avant de définir l’extension d’accès à la machine virtuelle IaaS » de se produire lors de l’exécution de la commande **Set-AzureVMExtension** dans les sections suivantes.

#### **Réinitialiser le mot de passe de compte d’administrateur local**

Créez des informations d’identification de connexion avec le nom du compte d’administrateur local actuel et un nouveau mot de passe, puis exécutez la commande `Set-AzureVMAccessExtension` comme suit.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Si vous tapez un nom différent de celui du compte actuel, l’extension VMAccess renomme le compte d’administrateur local, affecte le mot de passe à ce compte et ferme la session Bureau à distance. Si le compte d’administrateur local est désactivé, l’extension VMAccess l’active.

Ces commandes réinitialisent également la configuration du service Bureau à distance.

#### **Réinitialiser la configuration du service Bureau à distance**

Pour réinitialiser la configuration du service Bureau à distance, exécutez la commande suivante.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Les extensions VMAccess exécutent ces deux commandes sur la machine virtuelle :

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Cette commande active le groupe de pare-feu Windows intégré qui autorise le trafic entrant du Bureau à distance, qui utilise le port TCP 3389.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Cette commande définit la valeur de registre fDenyTSConnections sur 0, pour activer les connexions Bureau à distance.


## Machines virtuelles Windows dans le modèle de déploiement Resource Manager

Le portail Azure ne prend pas en charge actuellement la réinitialisation des informations d’identification d’accès à distance ou de connexion pour les machines virtuelles créées à l’aide de Resource Manager.


### Extension VMAccess et PowerShell

Assurez-vous qu’Azure PowerShell 1.0 ou version supérieure est installé et que vous êtes connecté à votre compte à l’aide de l’applet de commande `Login-AzureRmAccount`.

#### **Réinitialiser le mot de passe de compte d’administrateur local**

Vous pouvez réinitialiser le mot de passe et/ou le nom d’utilisateur de l’administrateur à l’aide de la commande PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx).

Créez les informations d’identification de votre compte d’administrateur local à l’aide de la commande suivante :

	$cred=Get-Credential

Si vous tapez un nom différent de celui du compte actuel, la commande ci-dessous de l’extension VMAccess renomme le compte d’administrateur local, attribue le mot de passe à ce compte et ferme la session Bureau à distance. Si le compte d’administrateur local est désactivé, l’extension VMAccess l’active.
	
Utilisez l’extension VMAccess pour définir les nouvelles informations d’identification comme suit :

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password


Remplacez `myRG`, `myVM`, `myVMAccess` et l’emplacement par des valeurs adaptées à votre installation.


#### **Réinitialiser la configuration du service Bureau à distance**

Vous pouvez réinitialiser l’accès à distance à votre machine virtuelle en utilisant [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou Set-AzureRmVMAccessExtension comme suit. Remplacez `myRG`, `myVM`, `myVMAccess` et l’emplacement par vos propres valeurs.

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus

OU<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus

	
> [AZURE.TIP] `Set-AzureRmVMAccessExtension` et `Set-AzureRmVMExtension` ajoutent toutes les deux un nouvel agent d’accès de machine virtuelle à la machine virtuelle. En toutes circonstances, une machine virtuelle ne peut avoir qu’un seul agent d’accès de machine virtuelle. Pour définir successivement les propriétés de l’agent d’accès de machine virtuelle, supprimez l’agent d’accès défini précédemment à l’aide de `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. À partir de la version 1.2.2 d’Azure PowerShell, vous pouvez éviter cette étape quand vous utilisez `Set-AzureRmVMExtension` avec une option `-ForceRerun`. Assurez-vous d’utiliser le même nom pour l’agent d’accès de machine virtuelle que celui défini par la commande précédente à l’aide de l’option `-ForceRerun`.


Si vous ne pouvez toujours pas vous connecter à distance à votre machine virtuelle, consultez les étapes supplémentaires dans la rubrique [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure basée sur Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).


## Ressources supplémentaires

[Extensions et fonctionnalités des machines virtuelles Azure](virtual-machines-windows-extensions-features.md)

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0323_2016-->