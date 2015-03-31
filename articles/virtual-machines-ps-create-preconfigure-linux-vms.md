<properties 
	pageTitle="Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Linux" 
	description="Découvrez comment utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Linux dans Azure." 
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
	ms.date="03/05/2015" 
	ms.author="josephd"/>

# Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Linux

Ces étapes vous montrent comment personnaliser un jeu de commandes Azure PowerShell en vue de créer et de préconfigurer une machine virtuelle Azure basée sur Linux à l'aide d'une approche modulaire. À l'aide de cette procédure, vous pouvez créer rapidement un jeu de commandes pour une nouvelle machine virtuelle basée sur Linux et étendre un déploiement existant, ou créer plusieurs jeux de commandes qui génèrent rapidement un environnement personnalisé de développement/test ou destiné aux professionnels de l'informatique.

Ces étapes utilisent une méthode de cases à remplir pour créer des jeux de commandes Azure PowerShell. Cette méthode peut être utile si vous découvrez PowerShell ou simplement si vous souhaitez connaître les valeurs à indiquer pour une configuration réussie. Les utilisateurs avancés de PowerShell peuvent prendre les commandes et indiquer leurs propres valeurs pour les variables (lignes commençant par " $ ").

Si vous souhaitez connaître la rubrique associée pour configurer des machines virtuelles basées sur Windows, consultez [Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines-ps-create-preconfigure-windows-vms/).

## Étape 1 : Installation d'Azure PowerShell

Si ce n'est pas encore fait, installez Azure PowerShell sur votre ordinateur local à l'aide des instructions décrites dans [Installation et configuration d'Azure PowerShell](../install-configure-powershell/). Ouvrez ensuite une invite de commande Azure PowerShell.

## Étape 2 : Configuration de votre compte d'abonnement et de stockage

Pour configurer votre compte d'abonnement et de stockage Azure, exécutez ces commandes dans l'invite de commandes Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >, par les noms appropriés.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Le nom de l'abonnement apparaît dans la propriété SubscriptionName du résultat de la commande **Get-AzureSubscription**. Le nom du compte de stockage apparaît dans la propriété Étiquette du résultat de la commande **Get-AzureStorageAccount** une fois que vous avez émis la commande **Select-AzureSubscription**. Vous pouvez également stocker ces commandes dans un fichier texte pour une utilisation ultérieure.

## Étape 3 : Définition de la valeur ImageFamily

Vous devez ensuite déterminer la valeur ImageFamily pour l'image spécifique correspondant à la machine virtuelle Azure que vous voulez créer. Vous pouvez obtenir la liste des valeurs ImageFamily disponibles à l'aide de cette commande.

	Get-AzureVMImage | select ImageFamily -Unique

Voici des exemples de valeurs ImageFamily pour les ordinateurs basés sur Linux :

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Ouvrez une nouvelle instance de l'éditeur de texte de votre choix et copiez les informations suivantes, qui remplacent la valeur ImageFamily, dans le nouveau fichier texte.
 
	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Étape 4 : Création de votre jeu de commandes

Créez le reste de votre jeu de commandes en copiant le jeu de blocs approprié ci-dessous dans votre nouveau fichier texte, puis en renseignant les valeurs des variables et en supprimant les caractères < et >. Consultez les deux [exemples](#examples) cités à la fin de cet article pour avoir une idée du résultat final.

Pour commencer, choisissez l'un des deux blocs de commandes suivants (obligatoire).

Option 1 : spécifiez un nom de machine virtuelle et une taille.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2 : spécifiez un nom, une taille et un nom de groupe à haute disponibilité.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Pour les valeurs InstanceSize des machines virtuelles des séries D, DS et G, consultez la page [Tailles de machines virtuelles et de services cloud pour Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Spécifiez le nom d'utilisateur Linux initial et le mot de passe (obligatoire). Choisissez un mot de passe fort. Pour en vérifier la force, consultez la page de [vérification de mot de passe à l'aide de Password Checker](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$username="<user account name>"
	$pass="<user account password>"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

Si vous enregistrez le jeu de commandes obtenu dans un fichier, stockez-le dans un emplacement sécurisé afin de protéger le nom de compte et le mot de passe.

Vous pouvez éventuellement spécifier un jeu de paires de clés SSH déjà déployées dans l'abonnement.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Pour plus d'informations, consultez la rubrique [Utilisation de SSH avec Linux dans Azure](../virtual-machines-linux-use-ssh-key/).

Vous pouvez éventuellement spécifier une liste de clés publiques SSH déjà déployées dans l'abonnement.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Pour connaître les autres options de préconfiguration disponibles pour les machines virtuelles basées sur Linux, consultez la syntaxe du jeu de paramètres **Linux** dans [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Vous pouvez éventuellement attribuer à la machine virtuelle une adresse IP spécifique, appelée DIP statique.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Vous pouvez vérifier la disponibilité d'une adresse IP particulière à l'aide de la commande suivante :

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

Vous pouvez éventuellement affecter la machine virtuelle à un sous-réseau spécifique dans un réseau virtuel Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Vous pouvez éventuellement ajouter un disque de données unique à la machine virtuelle. 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Vous pouvez éventuellement ajouter la machine virtuelle à un jeu à charge équilibrée existant pour le trafic externe.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Enfin, démarrez le processus de création de machine virtuelle en choisissant l'un des blocs de commandes suivants (obligatoire).

Option 1 : créez la machine virtuelle dans un nouveau service cloud. 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

Vous pouvez obtenir la liste des emplacements Azure à l'aide de la commande suivante :

	Get-AzureLocation | Select DisplayName

Option 2 : créez la machine virtuelle dans un service cloud existant. 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

Le nom court du service cloud est celui qui apparaît dans la liste des services cloud ou la liste des groupes de ressources dans le portail Azure en version préliminaire. 

Option 3 : créez la machine virtuelle dans un service cloud existant et un réseau virtuel.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Étape 5 : Exécution de votre jeu de commandes

Passez en revue le jeu de commandes Azure PowerShell constitué de plusieurs blocs de commandes que vous avez créé dans votre éditeur de texte à l'étape 4. Vérifiez que vous avez spécifié toutes les variables nécessaires et qu'elles ont les valeurs correctes. Vérifiez également que vous avez supprimé tous les caractères < et >.

Copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Azure PowerShell ouverte. Vous émettez ainsi le jeu de commandes en tant que série de commandes PowerShell et créez votre machine virtuelle Azure. Si vous le faites dans un abonnement, un compte de stockage, un service cloud, un groupe de haute disponibilité, un réseau virtuel ou un sous-réseau inapproprié, supprimez la machine virtuelle, corrigez la syntaxe du bloc de commande, puis exécutez le jeu de commandes modifié. 

Une fois la machine virtuelle créée, consultez [Connexion à une machine virtuelle exécutant Linux](../virtual-machines-linux-how-to-log-on/). 

Si vous comptez créer cette machine virtuelle de nouveau ou une autre similaire, vous pouvez : 

- Enregistrer ce jeu de commandes en tant que fichier texte ou fichier de script PowerShell (*.ps1)
- Enregistrer ce jeu de commandes en tant que runbook Azure dans la section **Automation** du portail de gestion Azure 

## <a id="examples"></a>Exemples

Voici deux exemples d'utilisation des étapes ci-dessus pour créer des jeux de commandes Azure PowerShell qui créent des machines virtuelles basées sur Linux dans Azure.

### Exemple 1

J'ai besoin d'un jeu de commandes PowerShell permettant de créer la machine virtuelle Linux initiale pour un serveur MySQL qui :

- utilise l'image Ubuntu Server 12.10
- se nomme AZMYSQL1 
- comporte un disque de données supplémentaire de 500 Go
- possède l'adresse IP statique 192.168.244.4
- se trouve dans le sous-réseau BackEnd du réseau virtuel AZDatacenter
- se trouve dans le service cloud Azure-TailspinToys

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle. Les lignes vides entre chaque bloc offrent une meilleure lisibilité.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin397A"
	$pass="3A#q291{Y"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Exemple 2

J'ai besoin d'un jeu de commandes PowerShell permettant de créer une machine virtuelle Linux pour un serveur Apache qui :

- utilise l'image SUSE Linux Enterprise Server 12
- se nomme LOB1
- comporte un disque de données supplémentaire de 50 Go 
- fait partie du jeu d'équilibrage de charge LOBServers pour un trafic web standard
- se trouve dans le sous-réseau FrontEnd du réseau virtuel AZDatacenter
- se trouve dans le service cloud Azure-TailspinToys

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin261Z"
	$pass="9Z2:3Wqp1~"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

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
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Ressources supplémentaires

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[FAQ sur les machines virtuelles Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Vue d'ensemble des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Installation et configuration d'Azure PowerShell](../install-configure-powershell/)

[Connexion à une machine virtuelle exécutant Linux](../virtual-machines-linux-how-to-log-on/)

[Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines-ps-create-preconfigure-windows-vms/)


<!--HONumber=47-->
