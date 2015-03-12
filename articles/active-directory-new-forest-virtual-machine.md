<properties 
	pageTitle="Installation d'une forêt Active Directory sur un réseau virtuel Azure" 
	description="Didacticiel qui explique comment créer une forêt Active Directory sur une machine virtuelle dans Azure Virtual Network." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="Justinha"/>




#Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure

Cette rubrique explique comment créer un environnement Windows Server Active Directory sur un réseau virtuel Azure, sur une machine virtuelle d'un [réseau virtuel Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). Dans ce cas, le réseau virtuel Azure n'est pas connecté à un réseau local. 

Les rubriques suivantes peuvent également vous intéresser :

- Vous pouvez éventuellement [configurer un réseau privé virtuel de site à site à l'aide de l'Assistant du Portail de gestion](http://msdn.microsoft.com/library/windowsazure/dn133795.aspx), puis installer une nouvelle forêt ou étendre une forêt locale à un réseau virtuel Azure. Dans ce cas, consultez [Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure](http://azure.microsoft.com/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Pour obtenir les procédures pas à pas de création d'un environnement test dans Azure incluant AD DS, consultez la page [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure](http://www.microsoft.com/fr-fr/download/details.aspx?id=41684).



##Sommaire##

* [Quelles sont les différences par rapport à une installation locale ?](#differ)
* [Étape 1 : création d'un réseau virtuel Azure](#createvnet)
* [Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS](#createvm)
* [Étape 3 : installation de Windows Server Active Directory](#installad)
* [Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure](#dns)
* [Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine](#domainmembers)


<h2><a id="differ"></a>Quelles sont les différences par rapport à une installation locale ?</h2>
Les différences entre l'installation d'un contrôleur de domaine dans Azure ou localement sont minimes. Le tableau suivant répertorie les principales différences. 

Pour configurer...  | Local | Azure Virtual Network	
------------- | -------------  | ------------
**Adresse IP pour le contrôleur de domaine** | Attribuez une adresse IP statique aux propriétés de carte réseau | Exécutez l'applet de commande Set-AzureStaticVNetIP pour attribuer une adresse IP statique
**Programme de résolution du client DNS** | Définissez l'adresse des serveurs DNS préféré et auxiliaire dans les propriétés de carte réseau des membres du domaine | Définissez l'adresse du serveur DNS dans les propriétés du réseau virtuel
**Stockage de base de données Active Directory** | Définissez éventuellement l'emplacement de stockage par défaut avec un autre lecteur que C:\  | Vous devez définir l'emplacement de stockage par défaut avec un autre lecteur que le lecteur C:\



<h2><a id="createvnet"></a>Étape 1 : création d'un réseau virtuel Azure</h2>
1. Connectez-vous au [Portail de gestion Azure](https://manage.windowsazure.com).
2. Créez un réseau virtuel. Cliquez sur <b>Réseaux</b> > <b>Créez un réseau virtuel</b>. Utilisez les valeurs du tableau suivant pour compléter l'Assistant. 

	Dans cette page de l'Assistant...  | Spécifiez les valeurs suivantes
	------------- | -------------
	**Détails du réseau virtuel**  | <p>Nom : entrez le nom du réseau virtuel</p><p>Region: sélectionnez la région la plus proche</p>
	**DNS et VPN**  | <p>N'indiquez pas de serveur DNS</p><p>Ne sélectionnez pas d'option VPN</p>
	**Espaces d'adressage du réseau virtuel**  | <p>Subnet name: entrez le nom du sous-réseau</p><p>IP de démarrage : <b>10.0.0.0</b></p><p>CIDR : <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS</h2>
 
1. Cliquez sur <b>Nouveau</b> > <b>Compute</b> > <b>Machine virtuelle</b> > <b>dans la galerie</b>. 
2. Utilisez les valeurs du tableau suivant pour compléter l'Assistant.

	Dans cette page de l'Assistant...  | Spécifiez les valeurs suivantes
	------------- | -------------
	**Systèmes d'exploitation**  | Sélectionnez **Windows Server 2012 R2 Datacenter**
	**Configuration de la machine virtuelle**  | <p>Release date: date du jour</p><p>Machine name: spécifiez une valeur unique</p><p>Tier: Standard</p><p>Taille : sélectionnez une taille</p><p>User name: entrez un nom. Ce compte d'utilisateur sera membre du groupe Administrateurs intégré. </p><p>Password: doit comporter au moins 8 caractères, et inclure 3 types parmi les types de caractères suivants :</p><ul><li>une lettre majuscule</li><li>une lettre minuscule</li><li>un chiffre</li><li>un caractère spécial</li></ul>
	**Service cloud**  | <p>Service de cloud computing : <b>Créer un nouveau service de cloud computing</b></p><p>Nom du service de cloud computing : acceptez la valeur par défaut</p><p>Région/Groupe d'affinités/Réseau virtuel : sélectionnez le réseau virtuel que vous avez créé</p><p>Sous-réseau du réseau virtuel : sélectionnez le sous-réseau que vous avez créé. </p><p>Compte de stockage : <b>Utiliser un compte de stockage généré automatiquement</b></p><p>Groupe à haute disponibilité : <b>Aucun</b></p><p>Points de terminaison : acceptez les valeurs par défaut</p>
	**Agent de machine virtuelle**  | Sélectionnez **Installer l'agent de machine virtuelle**

1. L'adresse IP dynamique attribuée par défaut à la machine virtuelle est valide pendant la durée du service cloud. Mais elle change lorsque la machine virtuelle est arrêtée. Attribuez une adresse IP statique en [exécutant l'applet de commande Set-AzureStaticVNetIP Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/dn630228.aspx) pour que l'adresse IP soit conservée si vous devez arrêter la machine virtuelle à un moment donné. 
2. Attachez un autre disque à la machine virtuelle pour stocker la base de données Active Directory, les fichiers journaux et SYSVOL. 
  3. Cliquez sur <b>Machine virtuelle</b> > <b>Attacher</b> > <b>Attacher un disque vide</b>. 
  4. Spécifiez une taille (par exemple, 10 Go) et acceptez les autres valeurs par défaut.
3. Connectez-vous à la machine virtuelle et formatez l'autre disque. 
  4. Cliquez sur <b>Connecter</b> pour vous connecter à la machine virtuelle, cliquez sur <b>Ouvrir</b> pour créer une session RDP, puis cliquez sur <b>Connecter</b> encore une fois.
  4. Remplacez les informations d'identification par les nouveaux nom d'utilisateur et mot de passe spécifiés.
  5. Dans le Gestionnaire de serveur, cliquez sur <b>Outils</b> > <b>Gestion de l'ordinateur</b>. 
  6. Cliquez sur <b>Gestion des disques</b> , puis cliquez sur <b>OK</b> pour initialiser le nouveau disque. 
  6. Cliquez avec le bouton droit sur le nom du disque, puis cliquez sur <b>Nouveau volume simple</b>. Terminez l'Assistant pour formater le nouveau lecteur. 

<h2><a id="installad"></a>Étape 3 : installation de Windows Server Active Directory</h2>
[Installez AD DS](http://technet.microsoft.com/library/jj574166.aspx) en faisant appel à la procédure utilisée localement (interface utilisateur, fichier de réponses ou Windows PowerShell). Vous devez fournir des informations d'identification d'administrateur pour installer une nouvelle forêt. Pour spécifier l'emplacement de la base de données Active Directory, des fichiers journaux et de SYSVOL, modifiez l'emplacement de stockage par défaut afin qu'il soit défini non sur le lecteur de système d'exploitation, mais sur l'autre disque de données que vous avez attaché à la machine virtuelle. 
<p>Lorsque l'installation du contrôleur de domaine est terminée, connectez-vous à celui-ci après vous être reconnecté à la machine virtuelle. N'oubliez pas de spécifier les informations d'identification du domaine.</p>

<h2><a id="dns"></a>Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure</h2>
1. Cliquez sur <b>Réseaux virtuels</b>, double-cliquez sur le réseau virtuel que vous avez créé, puis cliquez sur <b>Configurer</b>. 
2. Sous <b>Serveurs DNS</b>, tapez le nom et le DIP du contrôleur de domaine, puis cliquez sur <b>Enregistrer</B>. 
3. Sélectionnez la machine virtuelle, puis cliquez sur <b>Redémarrer</b> pour que la machine virtuelle configure les paramètres de résolution DNS avec l'adresse IP du nouveau serveur DNS. 


<h2><a id="domainmembers"></a>Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine</h2>
<p>Créez d'autres machines virtuelles pour mettre en service les ordinateurs membres du domaine. Vous pouvez utiliser l'interface utilisateur ou Azure PowerShell. Si vous utilisez l'interface utilisateur, suivez la procédure de création de la première machine virtuelle. Joignez ensuite les machines virtuelles au domaine comme vous le feriez localement. Si vous utilisez Azure PowerShell, vous pouvez mettre en service les machines virtuelles et les ajouter au domaine lors de leur premier démarrage. </p><p>Cet exemple créera une machine virtuelle associée au domaine appelée DC2 qui exécute Windows Server 2012 R2 Datacenter. Après l'approvisionnement de DC2, connectez-vous y en tant qu'Admin du domaine et transformez-la en contrôleur de domaine de réplication. </p><p>Vous pouvez exécuter Get-AzureVMImage pour obtenir les noms des images. Par exemple, pour renvoyer une liste des images pour Windows Server 2012 R2, exécutez Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
	'

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
	$service = 'DC2'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Si vous réexécutez le script, vous devez fournir une valeur unique pour $service. Vous pouvez exécuter Test-AzureName -Service <i>nom du service</i>, qui est retourné si le nom est déjà pris. 	

## Voir aussi

-  [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configuration d'un réseau virtuel pour le cloud uniquement dans le Portail de gestion](http://msdn.microsoft.com/library/dn631643.aspx)

-  [Configuration d'un réseau VPN de site à site dans le Portail de gestion](http://msdn.microsoft.com/library/dn133795.aspx)

-  [Installation d'un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure](http://azure.microsoft.com/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Microsoft Azure IT Pro IaaS : (01) Concepts de base des machines virtuelles](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS : (05) Création d'un réseau virtuel pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Applets de commande de gestion Azure](http://msdn.microsoft.com/library/windowsazure/jj152841)

-  [Définition de l'adresse IP statique d'une machine virtuelle Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Attribution d'une adresse IP statique à une machine virtuelle Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Installation d'une nouvelle forêt Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-  [Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)](http://technet.microsoft.com/library/hh831734.aspx)

-  [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure](http://www.microsoft.com/fr-fr/download/details.aspx?id=41684)


<!--HONumber=35.2-->

<!--HONumber=46--> 
