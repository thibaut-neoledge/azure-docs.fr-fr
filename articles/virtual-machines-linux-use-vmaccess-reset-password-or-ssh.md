<properties 
	pageTitle="Utilisation de VMAccess pour les machines virtuelles Linux" 
	description="Utilisation de l'extension VMAccess pour Linux pour réinitialiser les mots de passe et les clés SSH, réinitialiser les configurations SSH, et supprimer des utilisateurs Linux" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Réinitialisation d'un mot de passe ou de SSH pour les machines virtuelles Linux #

Si vous ne pouvez pas vous connecter à une machine virtuelle Linux en raison d'un mot de passe oublié, d'une clé SSH (Secure Shell) incorrecte ou d'un problème lié à la configuration SSH, utilisez l'extension VMAccessforLinux pour réinitialiser le mot de passe ou la clé SSH, ou corriger la configuration SSH. 

## Configuration requise

- Agent Microsoft Azure Linux version 2.0.5 ou ultérieure. La plupart des images Linux dans la galerie de machines virtuelles incluent la version 2.0.5. Pour connaître la version installée, exécutez  `waagent -version`. Pour mettre à jour l'agent, suivez les instructions du [Guide de l'utilisateur de l'agent Linux Azure].
- Azure PowerShell. Vous allez utiliser les commandes de la cmdlet **Set-AzureVMExtension** pour charger et configurer automatiquement l'extension **VMAccessForLinux**. Pour plus d'informations sur la configuration d'Azure PowerShell, consultez la page [Installation et configuration d'Azure PowerShell].
- Un nouveau mot de passe ou des clés SSH, si vous souhaitez réinitialiser l'un des deux. Vous n'avez pas besoin de ces derniers si vous souhaitez corriger la configuration SSH. 

## Aucune installation nécessaire

VMAccess ne doit être installé avant que vous puissiez l'utiliser. Tant que l'agent Linux est installé sur la machine virtuelle, l'extension est chargée automatiquement lorsque vous exécutez une commande Azure PowerShell qui utilise la cmdlet **Set-AzureVMExtension**. 

## Utilisation de l'extension pour réinitialiser un mot de passe, la clé ou la configuration SSH, ou supprimer un utilisateur

Vous utiliserez la cmdlet **Set-AzureVMExtension** pour effectuer les modifications que VMAccess vous permet d'effectuer. Dans tous les cas, démarrez en utilisant le nom du service cloud et le nom de la machine virtuelle pour obtenir l'objet de machine de virtuelle et le stocker dans une variable. 

Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Si vous ne connaissez pas le service cloud et le nom de la machine virtuelle, exécutez **Get-AzureVM** pour afficher ces informations pour toutes les machines virtuelles dans votre abonnement actuel.


> [AZURE.NOTE] Les lignes de commande qui commencent par $ définissent des variables PowerShell qui sont ensuite utilisées dans les commandes PowerShell.

Si vous créez la machine virtuelle à l'aide du portail de gestion Azure, exécutez la commande supplémentaire suivante :

	$vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l'erreur " L'agent invité d'approvisionnement doit être activé sur l'objet VM avant de définir l'extension d'accès à la machine virtuelle IaaS " de se produire lors de l'exécution de la commande Set-AzureVMExtension dans les sections suivantes. 

Exécutez ensuite les tâches suivantes :

+ [Réinitialisation du mot de passe](#password)
+ [Réinitialisation d'une clé SSH](#SSHkey)
+ [Réinitialisation du mot de passe et de la clé SSH](#both)
+ [Réinitialisation de la configuration SSH](#config)
+ [Supprimer un utilisateur](#delete)

### <a name="password"></a>Réinitialisation du mot de passe

Renseignez le nom d'utilisateur Linux actuel et le nouveau mot de passe, puis exécutez ces commandes.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Si vous souhaitez réinitialiser le mot de passe ou la clé SSH pour un compte d'utilisateur, veillez à taper le nom d'utilisateur exact. Si vous tapez un nom différent, l'extension VMAccess crée un nouveau compte d'utilisateur et affecte le mot de passe à ce compte.


### <a name="SSHKey"></a>Réinitialisation d'une clé SSH

Renseignez le nom d'utilisateur Linux actuel et le chemin d'accès au certificat contenant les clés SSH, puis exécutez ces commandes.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Réinitialisation du mot de passe et de la clé SSH

Renseignez le nom d'utilisateur Linux actuel, le nouveau mot de passe ainsi que le chemin d'accès au certificat contenant les clés SSH, puis exécutez ces commandes.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Réinitialisation de la configuration SSH

Les erreurs de configuration SSH peuvent vous empêcher d'accéder à la machine virtuelle. Vous pouvez résoudre ce problème en rétablissant la configuration SSH par défaut. Cette action supprime tous les nouveaux paramètres d'accès dans la configuration, tels que le nom d'utilisateur, le mot de passe et la clé SSH, mais ne modifie pas le mot de passe ou les clés SSH du compte d'utilisateur. L'extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut. 

Exécutez ces commandes.

	$PrivateConfig = '{"reset_ssh": "True"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Le fichier de configuration SSH se trouve dans /etc/ssh/sshd_config.

### <a name="delete"></a> Supprimer un utilisateur

Renseignez le nom d'utilisateur Linux à supprimer, puis exécutez ces commandes.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## Ressources supplémentaires

[Extensions et fonctionnalités des machines virtuelles Azure] []

[Connexion à une machine virtuelle Azure avec RDP ou SSH] []


<!--Link references-->
[Guide de l'utilisateur de l'agent Linux Azure]: ../virtual-machines-linux-agent-user-guide
[Installation et configuration d'Azure PowerShell]: ../install-configure-powershell
[Extensions et fonctionnalités des machines virtuelles Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Connexion à une machine virtuelle Azure avec RDP ou SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
