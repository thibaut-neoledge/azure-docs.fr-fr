<properties 
	pageTitle="Réinitialisation d'un mot de passe ou du service Bureau à distance pour les machines virtuelles Windows" 
	description="Réinitialisez rapidement un mot de passe d'administrateur local ou le service Bureau à distance pour les machines virtuelles Windows à l'aide des commandes PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="josephd"/>

# Réinitialisation d'un mot de passe ou du service Bureau à distance pour les machines virtuelles Windows

Si vous ne pouvez pas vous connecter à une machine virtuelle Windows en raison d'un oubli de mot de passe ou d'un problème avec la configuration du service Bureau à distance, utilisez l'extension VMAccess pour réinitialiser le mot de passe de l'administrateur local ou la configuration du service Bureau à distance.
 
## Configuration requise

Avant de débuter, vous avez besoin des éléments suivants :

- Le module Azure PowerShell, version 0.8.5 ou ultérieure. Vous pouvez vérifier la version d'Azure PowerShell installée à l'aide de la commande **Get-Module azure | format-table version**. Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d'Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409). 
- Le nouveau mot de passe du compte de l'administrateur local. Vous n'avez pas besoin de cela si vous souhaitez réinitialiser la configuration du service Bureau virtuel. 
- L'agent de machine virtuelle. 

VMAccess ne doit être installé avant que vous puissiez l'utiliser. Tant que l'agent de machine virtuelle est installé sur la machine virtuelle, l'extension est chargée automatiquement lorsque vous exécutez une commande Azure PowerShell qui utilise la cmdlet **Set-AzureVMExtension**.
 
Tout d'abord, vérifiez que l'agent de machine virtuelle est déjà installé. Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez **Get-AzureVM** afin d'afficher ces informations pour toutes les machines virtuelles de l'abonnement en cours.

Si la commande **write-host** affiche **True**, cela signifie que l'agent de machine virtuelle est installé. Si elle affiche **False**, consultez les instructions et un lien vers le téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Si vous créez la machine virtuelle à l'aide du portail de gestion Azure, exécutez la commande supplémentaire suivante :

	$vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l'erreur " L'agent invité d'approvisionnement doit être activé sur l'objet VM avant de définir l'extension d'accès à la machine virtuelle IaaS " de se produire lors de l'exécution de la commande Set-AzureVMExtension dans les sections suivantes. 

À présent, vous pouvez effectuer ces tâches :

- Réinitialiser un mot de passe de compte d'administrateur local
- Réinitialiser la configuration du service Bureau à distance

## Réinitialiser le mot de passe de compte d'administrateur local

Renseignez le nom du compte d'administrateur local actuel et le nouveau mot de passe, puis exécutez ces commandes.

	$cred=Get-Credential -Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension -vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Si vous tapez un nom différent de celui du compte actuel, l'extension VMAccess renomme le compte d'administrateur local, affecte le mot de passe à ce compte et ferme la session Bureau à distance.
- Si le compte d'administrateur local est désactivé, l'extension VMAccess l'active.
 
Ces commandes réinitialisent également la configuration du service Bureau à distance.

## Réinitialiser la configuration du service Bureau à distance

Pour réinitialiser la configuration du service Bureau à distance, exécutez cette commande.

	Set-AzureVMAccessExtension -vm $vm | Update-AzureVM

Les extensions VMAccess exécutent ces deux commandes sur la machine virtuelle :

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Cette commande active le groupe de pare-feu Windows intégré qui autorise le trafic entrant du Bureau à distance, qui utilise le port TCP 3389.

- **Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Cette commande définit la valeur de registre fDenyTSConnections sur 0, pour activer les connexions Bureau à distance.

Si cela ne résout pas le problème d'accès au Bureau à distance, exécutez le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). 

1.	Cliquez sur le **package de diagnostic Microsoft Azure IaaS (Windows)** sur cette page pour créer une nouvelle session de diagnostic.
2.	Sur la page **Lequel des problèmes suivants se produit sur votre machine virtuelle Azure ?**, sélectionnez le problème **Connectivité RDP à une machine virtuelle Azure (redémarrage requis)** 

Pour plus d'informations, consultez [l'article de la base de connaissances relatif au package de diagnostic Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864). 

Si vous ne parvenez pas à exécuter le package de diagnostic Azure IaaS (Windows) ou que son exécution n'a pas résolu votre problème, consultez [Dépannage des connexions de Bureau à distance vers une machine virtuelle Azure exécutant Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Ressources supplémentaires

[Fonctionnalités et extensions de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Connexion à une machine virtuelle Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)


<!--HONumber=52-->