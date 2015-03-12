<properties 
	pageTitle="Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Azure" 
	description="Explique l'installation et la configuration de Symantec Endpoint Protection sur une machine virtuelle dans Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/26/2015" 
	ms.author="kathydav"/>

#Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Azure

Cet article décrit comment installer et configurer le client Symantec Endpoint Protection sur une machine virtuelle nouvelle ou existante exécutant Windows Server. Il s'agit du client complet, qui inclut des services tels qu'une protection antivirus et anti-programmes espions, un pare-feu et une prévention contre les intrusions. 

Le client est installé sous forme d'extension de sécurité en utilisant l'agent de machine virtuelle. Sur une nouvelle machine virtuelle, vous devez installer l'agent en même temps que le client du point de terminaison. Sur une machine virtuelle existante sans l'agent, vous devez d'abord télécharger et installer l'agent. Cet article aborde ces deux situations.

Si vous disposez d'un abonnement Symantec pour une solution locale, vous pouvez l'utiliser pour protéger vos machines virtuelles Azure. Si vous n'êtes pas encore client, vous pouvez vous inscrire pour une version d'évaluation. Pour plus d'informations sur cette solution, consultez la rubrique [Symantec Endpoint Protection sur la plateforme Azure de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=403942). Cette page fournit également des liens vers des informations de licence, ainsi que d'autres instructions d'installation du client si vous êtes déjà un client de Symantec.

##Installation de Symantec Endpoint Protection sur une nouvelle machine virtuelle

Le [portail de gestion Azure](http://manage.windowsazure.com) vous permet d'installer l'agent de machine virtuelle et l'extension de sécurité Symantec lors de l'utilisation de l'option **À partir de la galerie** pour créer la machine virtuelle. Il s'agit d'une méthode simple pour ajouter une protection Symantec en cas de création d'une seule machine virtuelle. 

L'option **À partir de la galerie** ouvre un Assistant qui vous aide à configurer la machine virtuelle. Utilisez la dernière page de l'Assistant pour installer l'agent de machine virtuelle et l'extension de sécurité Symantec. 

Pour des instructions générales, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=403943). Lorsque vous atteignez la dernière page de l'Assistant :

1.	Dans l'Agent de machine virtuelle, **Installer un agent de machine virtuelle** doit déjà être coché.

2.	Sous Extensions de sécurité, cochez **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Cliquez sur la coche en bas de la page pour créer la machine virtuelle.

## Installation de Symantec Endpoint Protection sur une machine virtuelle existante

Avant de débuter, vous avez besoin des éléments suivants :

- Le module Azure PowerShell, version 0.8.2 ou ultérieure. Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d'Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- L'agent de machine virtuelle. Pour obtenir des instructions et un lien vers le téléchargement, consultez le billet de blog [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Pour installer l'extension de sécurité Symantec sur une machine virtuelle existante :

1.	Obtenez le nom du service cloud et de la machine virtuelle. Si vous ne les connaissez pas, utilisez la commande **Get-AzureVM** pour afficher ces informations pour toutes les machines virtuelles de l'abonnement en cours. Ensuite, remplacez tout ce qui se trouve à l'intérieur des guillemets, y compris les caractères < et >, puis exécutez ces commandes :

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	Dans l'affichage de la commande Get-AzureVMAvailableExtension, notez le numéro de version de la propriété Version, puis exécutez ces commandes :

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

Pour vérifier que l'extension de sécurité Symantec a été installée et est à jour :

1.	Connectez-vous à votre machine virtuelle.
2.	Pour Windows Server 2008 R2, cliquez sur **Démarrer > Tous les programmes > Symantec Endpoint Protection**. Pour Windows Server 2012, à partir de l'écran d'accueil, tapez **Symantec**, puis cliquez sur **Symantec Endpoint Protection**.
3.	À partir de la fenêtre d'état, appliquez les mises à jour si nécessaire.

## Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Windows Server]

[Gestion des extensions]

<!--Link references-->
[Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/

[Gestion des extensions]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=42-->
