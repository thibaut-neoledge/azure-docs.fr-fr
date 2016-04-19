<properties
	pageTitle="Installation de Symantec Endpoint Protection sur une machine virtuelle | Microsoft Azure"
	description="Découvrez comment installer et configurer l'extension de sécurité Symantec Endpoint Protection sur une machine virtuelle Azure nouvelle ou existante, créée avec le modèle de déploiement classique."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="iainfou"/>

# Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Cet article décrit comment installer et configurer le client Symantec Endpoint Protection sur une machine virtuelle nouvelle ou existante exécutant Windows Server. Il s'agit du client complet, qui inclut des services tels qu'une protection antivirus et anti-programmes espions, un pare-feu et une prévention contre les intrusions.

Le client est installé sous forme d'extension de sécurité en utilisant l'agent de machine virtuelle. Sur une nouvelle machine virtuelle, vous devez installer l'agent en même temps que le client du point de terminaison. Sur une machine virtuelle existante sans l'agent, vous devez d'abord télécharger et installer l'agent. Cet article aborde ces deux situations.

Si vous disposez d'un abonnement Symantec pour une solution locale, vous pouvez l'utiliser pour protéger vos machines virtuelles Azure. Si vous n'êtes pas encore client, vous pouvez vous inscrire pour une version d'évaluation. Pour plus d'informations sur cette solution, consultez la rubrique [Symantec Endpoint Protection sur la plateforme Azure de Microsoft][Symantec]. Cette page fournit également des liens vers des informations de licence, ainsi que d’autres instructions d’installation du client si vous êtes déjà un client de Symantec.

## Installation de Symantec Endpoint Protection sur une nouvelle machine virtuelle

Le [portail Azure Classic][Portal] vous permet d’installer l’agent de machine virtuelle et l’extension de sécurité Symantec lors de l’utilisation de l’option **À partir de la bibliothèque** pour créer la machine virtuelle. Il s'agit d'une méthode simple pour ajouter une protection Symantec en cas de création d'une seule machine virtuelle.

L'option **À partir de la galerie** ouvre un Assistant qui vous aide à configurer la machine virtuelle. Utilisez la dernière page de l'Assistant pour installer l'agent de machine virtuelle et l'extension de sécurité Symantec.

Pour des instructions générales, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server][Create]. Lorsque vous atteignez la dernière page de l’Assistant :

1.	Dans l’Agent de machine virtuelle, **Installer un agent de machine virtuelle** doit déjà être coché.

2.	Sous Extensions de sécurité, cochez **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-windows-classic-install-symantec/InstallVMAgentandSymantec.png)

3.	Cliquez sur la coche en bas de la page pour créer la machine virtuelle.

## Installation de Symantec Endpoint Protection sur une machine virtuelle existante

Avant de débuter, vous avez besoin des éléments suivants :

- Le module Azure PowerShell, version 0.8.2 ou ultérieure, installé sur votre poste de travail. Vous pouvez vérifier la version de Microsoft Azure PowerShell installée à l’aide de la commande **Get-Module azure | format-table version**. Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d'Azure PowerShell][PS]. Veillez à vous connecter à votre abonnement Azure.

- L’agent de machine virtuelle exécuté sur la machine virtuelle Azure.

Pour commencer, vérifiez que l’agent de machine virtuelle est déjà installé sur la machine virtuelle. Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < and >.

> [AZURE.TIP] Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez **Get-AzureVM** pour répertorier les noms de toutes les machines virtuelles de l’abonnement actuel.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Si la commande **write-host** affiche la valeur **True**, cela signifie que l’agent de machine virtuelle est installé. Si elle affiche **False**, consultez les instructions et un lien vers le téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie][Agent].

Si l’Agent de machine virtuelle est installé, exécutez ces commandes pour installer l’agent Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Pour vérifier que l’extension de sécurité Symantec a été installée et est à jour :

1.	Connectez-vous à votre machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Windows Server][Logon].
2.	Pour Windows Server 2008 R2, cliquez sur **Démarrer > Symantec Endpoint Protection**. Pour Windows Server 2012 ou Windows Server 2012 R2, à partir de l’écran d’accueil, tapez **Symantec**, puis cliquez sur **Symantec Endpoint Protection**.
3.	À partir de l’onglet **État** de la fenêtre **Symantec Endpoint Protection**, appliquez des mises à jour ou redémarrez, le cas échéant.

## Ressources supplémentaires

[Connexion à une machine virtuelle exécutant Windows Server][Logon]

[Fonctionnalités et extensions de machine virtuelle Azure][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_0406_2016-->