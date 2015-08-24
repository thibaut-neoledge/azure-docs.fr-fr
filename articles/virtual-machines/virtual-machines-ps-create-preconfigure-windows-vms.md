<properties
	pageTitle="Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows"
	description="Découvrez comment utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="kathydav"/>

# Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

Ces étapes vous montrent comment personnaliser un jeu de commandes Azure PowerShell en vue de créer et de préconfigurer une machine virtuelle Azure basée sur Windows à l'aide d'une approche modulaire. À l'aide de cette procédure, vous pouvez créer rapidement un jeu de commandes pour une nouvelle machine virtuelle basée sur Windows et étendre un déploiement existant, ou créer plusieurs jeux de commandes qui génèrent rapidement un environnement personnalisé de développement/test ou destiné aux professionnels de l'informatique.

Ces étapes utilisent une méthode de cases à remplir pour créer des jeux de commandes Azure PowerShell. Cette méthode peut être utile si vous découvrez PowerShell ou simplement si vous souhaitez connaître les valeurs à indiquer pour une configuration réussie. Les utilisateurs avancés de PowerShell peuvent prendre les commandes et indiquer leurs propres valeurs pour les variables (lignes commençant par « $ »).

Pour plus d’informations sur la configuration des machines virtuelles basées sur Linux, voir l’article [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Linux](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md).

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Création et préconfiguration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)

## Étape 1 : installer Azure PowerShell

Si ce n’est pas encore fait, installez Azure PowerShell sur votre ordinateur local à l’aide des instructions décrites dans [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Ouvrez ensuite une invite de commande Azure PowerShell.

## Étape 2 : configurer votre abonnement et votre compte de stockage

Pour configurer votre abonnement et votre compte de stockage Azure, exécutez ces commandes à l’invite de commandes Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Le nom de l’abonnement apparaît dans la propriété SubscriptionName du résultat de la commande **Get-AzureSubscription**. Le nom du compte de stockage correct apparaît dans la propriété Label de la sortie de la commande **Get-AzureStorageAccount** une fois que vous avez exécuté la commande **Select-AzureSubscription**.

## Étape 3 : déterminer la valeur ImageFamily

Vous devez ensuite déterminer la valeur ImageFamily ou Étiquette pour l'image spécifique correspondant à la machine virtuelle Azure que vous voulez créer. Voici quelques exemples tirés de la galerie du portail de gestion Azure.

![](./media/virtual-machines-ps-create-preconfigure-windows-vms/PSPreconfigWindowsVMs_1.png)

Vous pouvez obtenir la liste des valeurs ImageFamily disponibles à l'aide de cette commande.

	Get-AzureVMImage | select ImageFamily -Unique

Voici des exemples de valeurs ImageFamily pour les ordinateurs basés sur Windows :

- Windows Server 2012 R2 Datacenter
- Windows Server 2008 R2 SP1
- Windows Server Technical Preview
- SQL Server 2012 SP1 Enterprise sur Windows Server 2012

Si vous trouvez l’image que vous recherchez, ouvrez une nouvelle instance de l’éditeur de texte de votre choix ou de l’environnement d’écriture de scripts intégré de PowerShell (ISE). Copiez le texte suivant dans le nouveau fichier texte ou PowerShell ISE, en remplaçant la valeur ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Dans certains cas, le nom de l'image se trouve dans la propriété Étiquette au lieu de la valeur ImageFamily. Si vous ne trouvez pas l'image souhaitée à l'aide de la propriété ImageFamily, triez les images par propriété Étiquette à l'aide de cette commande.

	Get-AzureVMImage | select Label -Unique

Si vous trouvez l'image appropriée avec cette commande, ouvrez une nouvelle instance de l'éditeur de texte de votre choix ou PowerShell ISE. Copiez le texte suivant dans le nouveau fichier texte ou PowerShell ISE, en remplaçant la valeur Label.

	$label="<Label value>"
	$image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Étape 4 : générer votre jeu de commandes

Créez le reste de votre jeu de commandes en copiant le jeu de blocs approprié ci-dessous dans votre nouveau fichier texte ou ISE, puis en renseignant les valeurs des variables et en supprimant les caractères < and >. Pour avoir une idée du résultat final, consultez les deux [exemples](#examples) figurant à la fin de cet article.

Pour commencer, choisissez l'un des deux blocs de commandes suivants (obligatoire).

Option 1 : spécifiez un nom et une taille de machine virtuelle.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2 : spécifiez un nom, une taille et un nom de groupe à haute disponibilité.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Pour plus d’informations sur les valeurs InstanceSize des machines virtuelles des séries D, DS et G, voir l’article [Tailles de machines virtuelles et de services cloud pour Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Pour un ordinateur Windows autonome, vous pouvez éventuellement spécifier le compte et le mot de passe de l'administrateur local.

	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

 Choisissez un mot de passe fort. Pour en vérifier la force, consultez la page [Password Checker : Utilisation de mots de passe forts](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Pour ajouter l’ordinateur Windows à un domaine Active Directory existant, vous pouvez éventuellement spécifier le compte et le mot de passe de l’administrateur local, le domaine, ainsi que le nom et le mot de passe d’un compte de domaine.

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account."
	$cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Pour connaître les autres options de préconfiguration disponibles pour les machines virtuelles Windows, consultez la syntaxe des jeux de paramètres **Windows** et **WindowsDomain** dans [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Vous pouvez éventuellement attribuer à la machine virtuelle une adresse IP spécifique, appelée DIP statique.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Vous pouvez vérifier la disponibilité d'une adresse IP particulière à l'aide de la commande suivante :

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Vous pouvez éventuellement affecter la machine virtuelle à un sous-réseau spécifique dans un réseau virtuel Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Vous pouvez éventuellement ajouter un disque de données unique à la machine virtuelle.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Pour un contrôleur de domaine Active Directory, définissez $hcaching sur « Aucun ».

Vous pouvez éventuellement ajouter la machine virtuelle à un jeu à charge équilibrée existant pour le trafic externe.

	$port="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Enfin, choisissez l'un de ces blocs de commande requis pour la création de la machine virtuelle.

Option 1 : créez la machine virtuelle dans un service cloud existant.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Le nom court du service cloud est celui qui apparaît dans la liste des services cloud ou la liste des groupes de ressources dans le portail Azure en version préliminaire.

Option 2 : créez la machine virtuelle dans un service cloud et un réseau virtuel existants.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Étape 5 : exécuter votre jeu de commandes

Passez en revue le jeu de commandes Azure PowerShell que vous avez créé dans votre éditeur de texte ou dans l’environnement d’écriture de scripts intégré de PowerShell (ISE), constitué de plusieurs blocs de commandes de l’étape 4. Vérifiez que vous avez spécifié toutes les variables nécessaires et qu'elles ont les valeurs correctes. Vérifiez également que vous avez supprimé tous les caractères < and >.

Si vous utilisez un éditeur de texte, copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Azure PowerShell ouverte. Vous émettez ainsi le jeu de commandes en tant que série de commandes PowerShell et créez votre machine virtuelle Azure. Vous pouvez également exécuter le jeu de commandes dans PowerShell ISE.

Si vous comptez créer cette machine virtuelle de nouveau ou une autre similaire, vous pouvez :

- Enregistrez ce jeu de commandes en tant que fichier de script PowerShell (\*.ps1).
- Enregistrez ce jeu de commandes en tant que Runbook Azure Automation dans la section **Automatisation** du Portail de gestion Azure.

## <a id="examples"></a>Exemples

Voici deux exemples d'utilisation des étapes ci-dessus pour créer des jeux de commandes Azure PowerShell qui créent des machines virtuelles Azure basées sur Windows.

### Exemple 1

J'ai besoin d'un jeu de commandes PowerShell permettant de créer la machine virtuelle initiale pour un contrôleur de domaine Active Directory qui :

- utilise l'image Windows Server 2012 R2 Datacenter
- se nomme AZDC1
- est un ordinateur autonome
- comporte un disque de données supplémentaire de 20 Go
- possède l'adresse IP statique 192.168.244.4
- se trouve dans le sous-réseau BackEnd du réseau virtuel AZDatacenter
- se trouve dans le service cloud Azure-TailspinToys.

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle. Les lignes vides entre chaque bloc offrent une meilleure lisibilité.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Exemple 2

J'ai besoin d'un jeu de commandes PowerShell permettant de créer une machine virtuelle pour un serveur métier qui :

- utilise l'image Windows Server 2012 R2 Datacenter
- se nomme LOB1
- appartient au domaine corp.contoso.com
- comporte un disque de données supplémentaire de 200 Go
- se trouve dans le sous-réseau FrontEnd du réseau virtuel AZDatacenter
- se trouve dans le service cloud Azure-TailspinToys.

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account."
	$cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
	$domaindns="corp.contoso.com"
	$domacctdomain="CORP"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## Ressources supplémentaires

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[FAQ sur les machines virtuelles Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Vue d’ensemble des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

[Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

[Création et préconfiguration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)

<!---HONumber=August15_HO7-->