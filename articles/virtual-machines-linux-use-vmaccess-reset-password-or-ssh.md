<properties pageTitle="Utilisation de VMAccess pour les ordinateurs virtuels Linux" description="Utilisation de l'extension VMAccess pour Linux afin de réinitialiser les mots de passe, les clés SSH et les configurations SSH" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="kathydav"/>

#Réinitialisation d'un mot de passe ou de SSH pour les machines virtuelles Linux#

Si vous ne pouvez pas vous connecter à une machine virtuelle Linux en raison d'un mot de passe ou d'une clé SSH oubliée ou d'un problème avec la configuration SSH, utilisez l'extension VMAccessforLinux pour réinitialiser le mot de passe, la clé SSH ou la configuration SSH. 


##Configuration requise

- Agent Microsoft Azure Linux version 2.0.5 ou ultérieure. La plupart des images Linux dans la galerie de machines virtuelles incluent la version 2.0.5. Pour connaître la version installée, exécutez `waagent -version`. Pour mettre à jour l'agent, suivez les instructions du [Guide de l'utilisateur l'Agent Linux Azure].

- Module Azure PowerShell. Le module inclut la cmdlet **Set-AzureVMExtension**, avec laquelle vous exécuterez les commandes pour utiliser l'extension **VMAccessForLinux**. Pour plus d'informations sur la configuration du module, consultez [Installation et configuration d'Azure PowerShell].

- Un nouveau mot de passe ou des clés SSH, si vous souhaitez réinitialiser l'un des deux. Vous n'avez pas besoin de ces derniers si vous souhaitez corriger la configuration SSH. 

##Aucune installation nécessaire

VMAccess ne doit être installé avant que vous puissiez l'utiliser. Tant que le Agent Linux et le module Azure sont installés, l'extension est chargée automatiquement lorsque vous exécutez une commande qui appelle la cmdlet **Set-AzureVMExtension**. 

##Utilisation de l'extension pour réinitialiser un mot de passe, la clé ou la configuration SSH, ou ajouter un utilisateur

Vous utiliserez la cmdlet **Set-AzureVMExtension** pour effectuer les modifications que VMAccess vous permet d'effectuer. Dans tous les cas, démarrez en utilisant le nom du service cloud et le nom de la machine virtuelle pour obtenir l'objet de machine de virtuelle et le stocker dans une variable.   

Ouvrez Azure PowerShell et tapez la commande suivante dans l'invite de commandes. Veillez à remplacer les espaces réservés MyServiceName et MyVMName par les vrais noms :

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

Exécutez ensuite les tâches suivantes :

+ [Réinitialisation du mot de passe](#password)
+ [Réinitialisation d'une clé SSH](#SSHkey)
+ [Réinitialisation du mot de passe et de la clé SSH](#both)
+ [Réinitialisation de la configuration SSH](#config)

### <a name="password"></a>Réinitialisation du mot de passe
Tapez le nom d'utilisateur et le mot de passe et stockez-les dans des variables, puis créez une variable pour stocker les valeurs afin que les commandes suivantes puissent les utiliser.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

Stockez le nom, l'éditeur et le numéro de version dans des variables : 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

Avec toutes les valeurs requises stockées dans des variables, exécutez la commande suivante :

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Si vous souhaitez réinitialiser le mot de passe ou la clé SSH pour un compte d'utilisateur, veillez à taper le nom d'utilisateur exact. Si vous tapez un nom différent, l'extension VMAccess crée un nouveau compte d'utilisateur et affecte le mot de passe à ce compte.

### <a name="SSHkey"></a>Réinitialisation d'une clé SSH

Tapez le nom d'utilisateur et le chemin d'accès de votre clé publique SSH et stockez-les dans des variables :

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

Exécutez les commandes suivantes :

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>Réinitialisation du mot de passe et de la clé SSH

Pour l'utilisateur actuel, tapez un nouveau mot de passe et le chemin d'accès du nouveau certificat avec la clé publique SSH et stockez-les dans des variables : 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

Exécutez les commandes suivantes :

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>Réinitialisation de la configuration SSH

Les erreurs de configuration SSH peuvent vous empêcher d'accéder à la machine virtuelle. Vous pouvez résoudre ce problème en rétablissant la configuration par défaut. Cela supprime tous les nouveaux paramètres d'accès dans la configuration (nom d'utilisateur, mot de passe ou clé SSH). Cela ne change pas le mot de passe ou les clés SSH du compte d'utilisateur. L'extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut.  

Définissez l'indicateur montrant que vous souhaitez réinitialiser la configuration et stockez-le dans une variable : 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

Exécutez les commandes suivantes :

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Le fichier de configuration SSH se trouve dans /etc/ssh/sshd_config.

## Résolution des problèmes

Lorsque vous utilisez la cmdlet **Set-AzureVMExtension**, vous pouvez obtenir cette erreur : " L'approvisionnement de l'agent invité doit être activée sur l'objet de machine virtuelle avant de configurer l'extension IaaS VM Access ". 

Cela peut se produire si vous avez utilisé le portail de gestion pour créer la machine virtuelle Linux, car la valeur de la propriété de l'agent invité peut ne pas être définie sur " True ". Pour résoudre ce problème, exécutez les commandes suivantes :

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#Ressources supplémentaires
[Fonctionnalités et extensions de machine virtuelle Azure] []

[Connexion à une machine virtuelle Azure avec RDP ou SSH] []


<!--Link references-->
[Guide de l'utilisateur l'Agent Linux Azure]: ../virtual-machines-linux-agent-user-guide
[Installation et configuration d'Azure PowerShell]: ../install-configure-powershell
[Fonctionnalités et extensions de machine virtuelle Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn606311.aspx
[Connexion à une machine virtuelle Azure avec RDP ou SSH]: http://msdn.microsoft.com/fr-fr/library/azure/dn535788.aspx



<!--HONumber=42-->
