<properties
	pageTitle="Création d’une machine virtuelle Linux à l’aide d’Azure PowerShell | Microsoft Azure"
	description="Apprenez à créer et à préconfigurer une machine virtuelle Linux à l’aide d’Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Création et préconfiguration d’une machine virtuelle Linux à l’aide d’Azure Powershell

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager
 
Ces étapes vous montrent comment créer une machine virtuelle Linux à l'aide d'une approche de cases à remplir pour créer des jeux de commandes Azure PowerShell. Cette méthode peut être utile si vous découvrez Azure PowerShell ou simplement si vous souhaitez connaître les valeurs à indiquer pour une configuration réussie.

Vous créez votre jeu de commandes en copiant les blocs de jeux de commandes dans un fichier texte ou PowerShell ISE, puis en renseignant les valeurs des variables et en supprimant les caractères < and >. Pour avoir une idée du résultat final, consultez les deux [exemples](#examples) figurant à la fin de cet article.

Pour plus d’informations sur les machines virtuelles Windows, voir l’article [Utilisation d’Azure PowerShell pour créer des machines virtuelles basées sur Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Installation d'Azure PowerShell

Si vous ne l'avez pas déjà fait, [installez et configurez Azure PowerShell](../install-configure-powershell.md). Ouvrez ensuite une invite de commande Azure PowerShell.

## Configuration de votre compte d'abonnement et de stockage

Pour configurer votre abonnement et votre compte de stockage Azure, exécutez les commandes suivantes à l’invite de commandes Azure PowerShell.

Le nom de l’abonnement apparaît dans la propriété **SubscriptionName** du résultat de la commande **Get-AzureSubscription**.

Le nom du compte de stockage apparaît dans la propriété **Label** de la sortie de la commande **Get-AzureStorageAccount** une fois que vous avez émis la commande Select-AzureSubscription.

Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## Rechercher l'image que vous souhaitez utiliser

Ensuite, vous devez déterminer la valeur ImageFamily pour l'image que vous souhaitez utiliser. Vous pouvez obtenir la liste des valeurs ImageFamily disponibles à l’aide de la commande suivante.

	Get-AzureVMImage | select ImageFamily -Unique

Voici des exemples de valeurs ImageFamily pour les ordinateurs basés sur Linux :

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Ouvrez une nouvelle instance de l’éditeur de texte de votre choix ou une instance de l’environnement d’écriture de scripts intégré de PowerShell (ISE). Copiez le texte suivant dans le nouveau fichier texte ou PowerShell ISE, en remplaçant la valeur ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Spécifier le nom, la taille et, éventuellement, le groupe à haute disponibilité

Pour commencer, choisissez l'un des deux blocs de commandes suivants (obligatoire).

**Option 1** : spécifiez un nom et une taille de machine virtuelle.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**Option 2** : spécifiez un nom, une taille et un nom de groupe à haute disponibilité.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Pour plus d’informations sur les valeurs InstanceSize des machines virtuelles des séries D, DS et G, voir l’article [Tailles de machines virtuelles et de services cloud pour Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).


## Configurer les options de sécurité d'accès utilisateur

**Option 1** : spécifiez le nom d'utilisateur Linux initial et le mot de passe (obligatoire). Choisissez un mot de passe fort. Pour en vérifier la force, consultez la page [Password Checker : Utilisation de mots de passe forts](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**Option 2** : spécifiez un jeu de paires de clés SSH déjà déployées dans l'abonnement.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Pour plus d'informations, consultez [Utilisation de SSH avec Linux dans Azure](virtual-machines-linux-use-ssh-key.md).

**Option 3** : spécifiez une liste de clés publiques SSH déjà déployées dans l'abonnement.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Pour connaître les autres options de préconfiguration disponibles pour les machines virtuelles basées sur Linux, voir la syntaxe du jeu de paramètres **Linux** dans [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).


## Facultatif : assigner une DIP statique

Vous pouvez éventuellement attribuer à la machine virtuelle une adresse IP spécifique, appelée DIP statique.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Vous pouvez vérifier la disponibilité d’une adresse IP particulière à l’aide de la commande suivante :

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## Facultatif : affecter la machine virtuelle à un sous-réseau spécifique 

Affectez la machine virtuelle à un sous-réseau spécifique dans un réseau virtuel Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## Facultatif : ajouter un disque de données
	
Ajoutez le code suivant à votre jeu de commandes pour ajouter un disque de données à la machine virtuelle.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## Facultatif : ajouter la machine virtuelle à une charge équilibrée existante 

Ajoutez le code suivant à votre jeu de commandes pour ajouter la machine virtuelle à un jeu à charge équilibrée existant pour le trafic externe.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## Décider comment démarrer le processus de création de machine virtuelle 

Ajoutez un bloc à votre jeu de commandes pour démarrer le processus de création de machine virtuelle en choisissant l’un des blocs de commandes suivants.

**Option 1** : créez la machine virtuelle dans un service cloud existant.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Le nom abrégé du service cloud est celui qui apparaît dans la liste Azure Cloud Services dans le portail Azure Classic ou dans la liste des groupes de ressources dans le portail Azure.

**Option 2** : créez la machine virtuelle dans un service cloud et un réseau virtuel existants.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Exécution de votre jeu de commandes

Passez en revue le jeu de commandes Azure PowerShell que vous avez créé dans votre éditeur de texte ou dans PowerShell ISE et assurez-vous que vous avez spécifié toutes les variables et qu’elles présentent les valeurs correctes. Vérifiez également que vous avez supprimé tous les caractères < and >.

Copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Azure PowerShell ouverte. Vous émettez ainsi le jeu de commandes en tant que série de commandes PowerShell et créez votre machine virtuelle Azure.

Une fois la machine virtuelle créée, voir [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md).

Si vous souhaitez réutiliser le jeu de commandes, vous pouvez :

- Enregistrer ce jeu de commandes en tant que fichier de script PowerShell (*.ps1)
- Enregistrez ce jeu de commandes en tant que runbook Azure Automation dans la section **Automation** du portail Azure Classic.

## <a id="examples"></a>Exemples

Voici deux exemples d’utilisation des étapes ci-dessus pour créer des jeux de commandes Azure PowerShell qui créent des machines virtuelles Linux dans Azure.

### Exemple 1

J'ai besoin d'un jeu de commandes PowerShell permettant de créer la machine virtuelle Linux initiale pour un serveur MySQL qui :

- utilise l’image Ubuntu Server 12.10
- se nomme AZMYSQL1
- comporte un disque de données supplémentaire de 500 Go
- possède l’adresse IP statique 192.168.244.4
- se trouve dans le sous-réseau BackEnd du réseau virtuel AZDatacenter
- se trouve dans le service cloud Azure-TailspinToys.

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle. Les lignes vides entre chaque bloc offrent une meilleure lisibilité.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Exemple 2

J'ai besoin d'un jeu de commandes PowerShell permettant de créer une machine virtuelle Linux pour un serveur Apache qui :

- utilise l’image SUSE Linux Enterprise Server 12
- se nomme LOB1
- comporte un disque de données supplémentaire de 50 Go
- fait partie du jeu d’équilibreurs de charge LOBServers pour un trafic web standard
- se trouve dans le sous-réseau FrontEnd du réseau virtuel AZDatacenter
- se trouve dans le service cloud Azure-TailspinToys.

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Ressources supplémentaires

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[FAQ sur les machines virtuelles Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Vue d’ensemble des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

[Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md)

[Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=AcomDC_1203_2015-->