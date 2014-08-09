<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure
==============================================================================

Cette rubrique explique comment créer un nouvel environnement Windows Server Active Directory sur un réseau virtuel Azure sur une machine virtuelle sur un [réseau virtuel Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156007.aspx). Dans ce cas, le réseau virtuel Azure n'est pas connecté à un réseau local.

Les rubriques suivantes peuvent également vous intéresser :

-   Vous pouvez éventuellement [configurer un réseau privé virtuel de site à site à l'aide de l'Assistant du portail de gestion](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn133795.aspx), puis installer une nouvelle forêt ou étendre une forêt locale à un réseau virtuel Azure. Dans ce cas, consultez la page [Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-   Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156090.aspx).
-   Pour obtenir les procédures pas à pas de création d'un environnement test dans Azure incluant AD DS, consultez la page [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41684).

Sommaire
--------

-   [Quelles sont les différences par rapport à une installation locale ?](#differ)
-   [Étape 1 : création d'un réseau virtuel Azure](#createvnet)
-   [Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS](#createvm)
-   [Étape 3 : installation de Windows Server Active Directory](#installad)
-   [Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure](#dns)
-   [Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine](#domainmembers)

Quelles sont les différences par rapport à une installation locale ?
--------------------------------------------------------------------

Les différences entre l'installation d'un contrôleur de domaine dans Azure ou localement sont minimes. Le tableau suivant répertorie les principales différences.

Pour configurer...  | Local  | Réseau virtuel Azure 
-------------  | -------------  | ------------ 
**Adresse IP du contrôleur de domaine**  | Attribuez une adresse IP statique aux propriétés d'adaptateur réseau  | Obtenez l'adresse IP via DHCP et exécutez la cmdlet Set-AzureStaticVNetIP pour la convertir en adresse IP statique 
**Programme de résolution du client DNS**  | Définissez l'adresse des serveurs DNS préféré et auxiliaire dans les propriétés d'adaptateur réseau des membres du domaine  | Définissez l'adresse du serveur DNS dans les propriétés du réseau virtuel 
**Stockage de base de données Active Directory**  | (Facultatif) Définissez l'emplacement de stockage par défaut sur un autre lecteur que le lecteur C:\\  | Vous devez définir l'emplacement de stockage par défaut sur un autre lecteur que le lecteur C:\\

Étape 1 : création d'un réseau virtuel Azure
--------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2.  Créez un réseau virtuel. Cliquez sur **Réseaux** \> **Create a virtual network**. Utilisez les valeurs du tableau suivant pour compléter l'Assistant.

    Sur cette page de l'Assistant  | Spécifiez les valeurs suivantes 
	-----------------------  | ------------------------ 
	**Détails du réseau virtuel**  | <p>Nom : entrez le nom du réseau virtuel</p><p>Région : sélectionnez la région la plus proche</p><p>Groupe d'affinités : **Créer un nouveau groupe d'affinités**</p><p>Nom du groupe d'affinités : entrez le nom du groupe d'affinités</p> 
	**DNS and VPN** | <p>N'indiquez pas de serveur DNS</p><p>Ne sélectionnez pas d'option VPN</p> 
	**Virtual network address spaces** | <p>Nom du sous-réseau : entrez le nom du sous-réseau</p>Adresse IP de début : **10.0.0.0**</p><p>CIDR : **/24 (256)**</p>

Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS
------------------------------------------------------------------------------------------------------

1.  Cliquez sur **Nouveau** \> **Compute** \> **Machine virtuelle** \> **From Gallery**.
2.  Utilisez les valeurs du tableau suivant pour compléter l'Assistant.

    Sur les pages de cet Assistant | Spécifiez les valeurs suivantes 
	------------- | ------------- 
	**Système d'exploitation** | Sélectionnez **Windows Server 2012 R2 Datacenter** 
	**Configuration de la machine virtuelle** | <p>Date de version : date du jour<p>Nom de la machine : spécifiez une valeur unique<p>Niveau : standard<p>Taille : sélectionnez une taille<p>Nom d'utilisateur : entrez un nom. Ce compte d'utilisateur sera membre du groupe Administrateurs intégré. <p>Mot de passe : il doit inclure au moins 8 caractères, dont 3 des types de caractères suivants :<p><ul><li>une majuscule</li><li>une minuscule</li><li>un chiffre</li><li>un caractère spécial</li>
	**Service cloud** | <p>Service cloud : **Create a new cloud service**<p>Nom du service cloud : acceptez la valeur par défaut<p>Region/AffinityGroup/VirtualNetwork : sélectionnez le réseau virtuel que vous avez créé<p>Virtual network subnet : sélectionnez le sous-réseau que vous avez créé. <p>Compte de stockage : **Use an automatically generated storage account**<p>Groupe à haute disponibilité : **Aucun**<p>Points de terminaison : acceptez les valeurs par défaut<p>
	**VM Agent** | Sélectionnez **Install the VM Agent**

3.  L'adresse IP dynamique attribuée par défaut à la machine virtuelle est valide pendant la durée du service cloud. Mais elle change lorsque la machine virtuelle est arrêtée. Vous pouvez attribuer une adresse IP statique en [exécutant la cmdlet Set-AzureStaticVNetIP Azure PowerShell](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn630228.aspx) afin que l'adresse IP soit conservée si vous devez arrêter la machine virtuelle à un moment donné.
4.  Attachez un autre disque à la machine virtuelle pour stocker la base de données Active Directory, les fichiers journaux et SYSVOL.
	1.  Cliquez sur **VM** \> **Attach** \> **Attach empty disk**.
	2.  Spécifiez une taille (par exemple, 10 Go) et acceptez les autres valeurs par défaut.
5.  Connectez-vous à la machine virtuelle et formatez l'autre disque.
	1.  Cliquez sur **Connect** pour vous connecter à la machine virtuelle, sur **Ouvrir** pour créer une session RDP, puis à nouveau sur **Connect**.
	2.  Remplacez les informations d'identification par les nouveaux nom d'utilisateur et mot de passe spécifiés.
	3. Dans Gestionnaire de serveur, cliquez sur **Outils** \> **Gestion de l'ordinateur**.
	4. Cliquez sur **Disk Management** et sur **Ok** pour initialiser le nouveau disque.
	5. Cliquez avec le bouton droit sur le nom du disque, puis cliquez sur **Nouveau volume simple**. Terminez l'Assistant pour formater le nouveau lecteur.

Étape 3 : installation de Windows Server Active Directory
---------------------------------------------------------

[Installez AD DS](http://technet.microsoft.com/library/jj574166.aspx) en faisant appel à la procédure utilisée localement (interface utilisateur, fichier de réponse ou Windows PowerShell). Vous devez fournir des informations d'identification d'administrateur pour installer une nouvelle forêt. Pour spécifier l'emplacement de la base de données Active Directory, des fichiers journaux et de SYSVOL, modifiez l'emplacement de stockage par défaut afin qu'il soit défini non sur le lecteur de système d'exploitation, mais sur l'autre disque de données que vous avez attaché à la machine virtuelle.

Lorsque l'installation du contrôleur de domaine est terminée, connectez-vous à celui-ci après vous être reconnecté à la machine virtuelle. N'oubliez pas de spécifier les informations d'identification du domaine.

Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure
-------------------------------------------------------------------

1.  Cliquez sur **Réseaux virtuels**, double-cliquez sur le réseau virtuel que vous avez créé, puis cliquez sur **Configurer**.
2.  Sous **Serveurs DNS**, tapez le nom et le DIP du contrôleur de domaine, puis cliquez sur **Enregistrer**.
3.  Sélectionnez la machine virtuelle et cliquez sur **Redémarrer** pour que la machine virtuelle configure les paramètres de résolution DNS avec l'adresse IP du nouveau serveur DNS.

Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine
-------------------------------------------------------------------------------------------------

Créez d'autres machines virtuelles pour mettre en service les ordinateurs membres du domaine. Vous pouvez utiliser l'interface utilisateur ou Azure PowerShell. Si vous utilisez l'interface utilisateur, suivez la procédure de création de la première machine virtuelle. Joignez ensuite les machines virtuelles au domaine comme vous le feriez localement. Si vous utilisez Azure PowerShell, vous pouvez mettre en service les machines virtuelles et les ajouter au domaine lors de leur premier démarrage, comme dans l'exemple suivant. '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Version d'évaluation gratuite"

    #Déployez une machine virtuelle et joignez-la au domaine
    #-------------------------------------------
    #Spécifiez l'adresse IP DNS du contrôleur de domaine (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC-1' -IPAddress '10.0.0.4'

    # Image de système d'exploitation à utiliser
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201310.01-en.us-127GB.vhd'
    $service = 'ConApp1'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #Configuration de la machine virtuelle
    $vmname = 'ConApp1'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Si vous réexécutez le script, vous devez fournir une valeur unique pour \$service. Vous pouvez exécuter Test-AzureName -Service *nom du service*, qui est retourné si le nom est déjà pris.

Voir aussi
----------

-   [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156090.aspx)

-   [Création d'un réseau virtuel dans Azure](http://www.windowsazure.com/fr-fr/manage/services/networking/create-a-virtual-network/)

-   [Création d'un réseau virtuel pour une connectivité entre différents locaux de site à site](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-networks-create-site-to-site-cross-premises-connectivity/)

-   [Installation d'un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-   [Réseau virtuel Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156007.aspx)

-   [Installation et configuration d’Azure PowerShell](http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/)

-   [Azure PowerShell](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156055.aspx)

-   [Cmdlets de gestion Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841)

-   [Définition de l'adresse IP statique d'une machine virtuelle Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-   [Installation d'une nouvelle forêt Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-   [Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)](http://technet.microsoft.com/en-us/library/hh831734.aspx)

-   [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41684).


