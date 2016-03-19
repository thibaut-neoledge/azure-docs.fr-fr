<properties
	pageTitle="Réinitialisation du mot de passe ou du Bureau à distance sur une machine virtuelle Windows | Microsoft Azure"
	description="Réinitialisez le mot de passe administrateur ou les services Bureau à distance sur une machine virtuelle Windows créée avec le modèle de déploiement du Gestionnaire des ressources."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>

# Réinitialisation d’un mot de passe ou du service Bureau à distance pour une machine virtuelle Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Si vous ne pouvez pas vous connecter à une machine virtuelle Windows en raison de l’oubli d’un mot de passe ou d’un problème avec la configuration du service Bureau à distance, utilisez le portail Azure ou l’extension VMAccess pour réinitialiser le mot de passe de l’administrateur local ou la configuration du service Bureau à distance.

## Portail Azure

Pour réinitialiser le service Bureau à distance dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Windows* > **Réinitialiser l'accès à distance**. La page suivante apparaît.


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

Pour réinitialiser le nom et le mot de passe du compte d'administrateur local dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Windows* > **Tous les paramètres** > **Réinitialisation du mot de passe**. La page suivante apparaît.

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)


## Extension VMAccess et PowerShell

Avant de débuter, vous avez besoin des éléments suivants :

- Le module Azure PowerShell, version 0.8.5 ou ultérieure. Vous pouvez vérifier la version de Microsoft Azure PowerShell installée à l’aide de la commande **Get-Module azure | format-table version**. Pour des instructions et un lien vers la dernière version, voir [Installation et configuration d’Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409).
- Le nouveau mot de passe du compte de l’administrateur local. Vous n’avez pas besoin de cela si vous souhaitez réinitialiser la configuration du service Bureau virtuel.
- L’agent de machine virtuelle.

VMAccess ne doit être installé avant que vous puissiez l’utiliser. Tant que l’agent de machine virtuelle est installé sur la machine virtuelle, l’extension est automatiquement chargée quand vous exécutez une commande Azure PowerShell qui utilise l’applet de commande **Set-AzureVMExtension**.

Tout d'abord, vérifiez que l’agent de machine virtuelle est déjà installé. Ajoutez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes Azure PowerShell de niveau administrateur. Remplacez tous les éléments entre guillemets, y compris les caractères < and >.

	$csName = "<cloud service name>"
	$vmName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $csName -Name $vmName
	write-host $vm.VM.ProvisionGuestAgent

Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez **Get-AzureVM** afin d’afficher ces informations pour toutes les machines virtuelles de l’abonnement actuel.

Si la commande **write-host** affiche la valeur **True**, cela signifie que l’agent de machine virtuelle est installé. Si la commande présente la valeur **False**, accédez aux instructions et au lien de téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) (en anglais).

Si vous créez la machine virtuelle avec le portail, exécutez la commande supplémentaire suivante.

	$vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l’erreur « L’agent invité d’approvisionnement doit être activé sur l’objet VM avant de définir l’extension d’accès à la machine virtuelle IaaS » de se produire lors de l’exécution de la commande **Set-AzureVMExtension** dans les sections suivantes.

À présent, vous pouvez effectuer ces tâches :

- Réinitialiser un mot de passe de compte d’administrateur local
- Réinitialiser la configuration du service Bureau à distance

## Réinitialiser le mot de passe de compte d’administrateur local

Ajoutez le nom du compte d’administrateur local actuel et le nouveau mot de passe, puis exécutez les commandes suivantes.

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Si vous tapez un nom différent de celui du compte actuel, l’extension VMAccess renomme le compte d’administrateur local, affecte le mot de passe à ce compte et ferme la session Bureau à distance.
- Si le compte d’administrateur local est désactivé, l’extension VMAccess l’active.

Ces commandes réinitialisent également la configuration du service Bureau à distance.

## Réinitialiser la configuration du service Bureau à distance

Pour réinitialiser la configuration du service Bureau à distance, exécutez la commande suivante.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Les extensions VMAccess exécutent ces deux commandes sur la machine virtuelle :

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Cette commande active le groupe de pare-feu Windows intégré qui autorise le trafic entrant du Bureau à distance, qui utilise le port TCP 3389.

- **Set-ItemProperty -Path ’HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server’ -name "fDenyTSConnections" -Value 0**

	Cette commande définit la valeur de registre fDenyTSConnections sur 0, pour activer les connexions Bureau à distance.

Si cette opération ne résout pas le problème d’accès au Bureau à distance, exécutez le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Dans le package de diagnostic, cliquez sur le **package de diagnostic Microsoft Azure IaaS (Windows)** pour créer une session de diagnostic.
2.	Sur la page **Lequel des problèmes suivants se produit sur votre machine virtuelle Azure ?**, sélectionnez le problème **Connectivité RDP à une machine virtuelle Azure (redémarrage requis)**.

Pour plus d’informations, voir l’article de la Base de connaissances [Package de diagnostic Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864).

Si vous ne parvenez pas à exécuter le package de diagnostic Azure IaaS (Windows) ou que son exécution n’a pas résolu votre problème, voir [Résoudre les problèmes des connexions du Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Ressources supplémentaires

[Extensions et fonctionnalités des machines virtuelles Azure](virtual-machines-extensions-features.md)

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

<!---HONumber=AcomDC_1217_2015-->