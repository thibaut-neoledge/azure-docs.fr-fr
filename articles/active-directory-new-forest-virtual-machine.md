<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />

# Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure

Cette rubrique explique comment créer un nouvel environnement Windows Server Active Directory sur un réseau virtuel Azure sur une machine virtuelle sur un [réseau virtuel Azure][réseau virtuel Azure]. Dans ce cas, le réseau virtuel Azure n'est pas connecté à un réseau local.

Les rubriques suivantes peuvent également vous intéresser :

-   Vous pouvez éventuellement [configurer un réseau privé virtuel de site à site à l'aide de l'Assistant du portail de gestion][configurer un réseau privé virtuel de site à site à l'aide de l'Assistant du portail de gestion], puis installer une nouvelle forêt ou étendre une forêt locale à un réseau virtuel Azure. Dans ce cas, consultez la page [Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure][Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure].
-   Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure][Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure].
-   Pour obtenir les procédures pas à pas de création d'un environnement test dans Azure incluant AD DS, consultez la page [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure][Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure].

## Sommaire

-   [Quelles sont les différences par rapport à une installation locale ?][Quelles sont les différences par rapport à une installation locale ?]
-   [Étape 1 : création d'un réseau virtuel Azure][Étape 1 : création d'un réseau virtuel Azure]
-   [Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS][Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS]
-   [Étape 3 : installation de Windows Server Active Directory][Étape 3 : installation de Windows Server Active Directory]
-   [Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure][Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure]
-   [Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine][Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine]

## <span id="differ"></span></a>Quelles sont les différences par rapport à une installation locale ?

Les différences entre l'installation d'un contrôleur de domaine dans Azure ou localement sont minimes. Le tableau suivant répertorie les principales différences.

| Pour configurer...                               | Au niveau local                                                                                                            | Azure Virtual Network                                                                                              |
|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| **Adresse IP pour le contrôleur de domaine**     | Attribuez une adresse IP statique aux propriétés d'adaptateur réseau                                                       | Obtenez l'adresse IP via DHCP et exécutez la cmdlet Set-AzureStaticVNetIP pour la convertir en adresse IP statique |
| **Programme de résolution du client DNS**        | Définissez l'adresse des serveurs DNS préféré et auxiliaire dans les propriétés d'adaptateur réseau des membres du domaine | Définissez l'adresse du serveur DNS dans les propriétés du réseau virtuel                                          |
| **Stockage de base de données Active Directory** | (Facultatif) Définissez l'emplacement de stockage par défaut sur un autre lecteur que le lecteur C:\\                      | Vous devez définir l'emplacement de stockage par défaut sur un autre lecteur que le lecteur C:\\                   |

## <span id="createvnet"></span></a>Étape 1 : création d'un réseau virtuel Azure

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Créez un réseau virtuel. Cliquez sur **Réseaux** \> **Create a virtual network**. Utilisez les valeurs du tableau suivant pour compléter l'Assistant.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Sur cette page de l'Assistant...</th>
    <th align="left">Spécifiez les valeurs suivantes</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Détails du réseau virtuel</strong></td>
    <td align="left"><p>Name: entrez le nom du réseau virtuel</p>
    <p>Region: sélectionnez la région la plus proche</p>
    <p>Affinity Group: <strong>créez un nouveau groupe d'affinités</strong></p>
    <p>Affinity Group Name: entrez le nom du groupe d'affinités</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS et VPN</strong></td>
    <td align="left"><p>N'indiquez pas de serveur DNS</p>
    <p>Ne sélectionnez pas d'option VPN</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Espaces d'adressage du réseau virtuel</strong></td>
    <td align="left"><p>Subnet name: entrez le nom du sous-réseau</p>
    <p>IP de démarrage : <strong>10.0.0.0</strong></p>
    <p>CIDR : <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS

1.  Cliquez sur **Nouveau** \> **Compute** \> **Machine virtuelle** \> **From Gallery**.
2.  Utilisez les valeurs du tableau suivant pour compléter l'Assistant.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Sur cette page de l'Assistant...</th>
    <th align="left">Spécifiez les valeurs suivantes</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Système d'exploitation</strong></td>
    <td align="left">Sélectionnez <strong>Windows Server 2012 R2 Datacenter</strong></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Configuration de machine virtuelle</strong></td>
    <td align="left"><p>Release date: date du jour</p>
    <p>Machine name: spécifiez une valeur unique</p>
    <p>Tier: Standard</p>
    <p>Size: sélectionnez une taille</p>
    <p>User name: entrez un nom. Ce compte d'utilisateur sera membre du groupe Administrateurs intégré.</p>
    <p>Password: doit comporter au moins 8 caractères, et inclure 3 types parmi les types de caractères suivants :</p>
    <ul>
    <li>une lettre majuscule</li>
    <li>une lettre minuscule</li>
    <li>un chiffre</li>
    <li>un caractère spécial</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Service cloud</strong></td>
    <td align="left"><p>Cloud service: <strong>Create a new cloud service</strong></p>
    <p>Cloud service name: acceptez la valeur par défaut</p>
    <p>Region/AffinityGroup/VirtualNetwork: sélectionnez le réseau virtuel que vous avez créé</p>
    <p>Virtual network subnet: sélectionnez le sous-réseau que vous avez créé.</p>
    <p>Storage account: <strong>Use an automatically generated storage account</strong></p>
    <p>Availability set: <strong>None</strong></p>
    <p>Endpoints: acceptez les valeurs par défaut</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Agent VM</strong></td>
    <td align="left">Sélectionnez <strong>Install the VM Agent</strong></td>
    </tr>
    </tbody>
    </table>

3.  L'adresse IP dynamique attribuée par défaut à la machine virtuelle est valide pendant la durée du service cloud. Mais elle change lorsque la machine virtuelle est arrêtée. Vous pouvez attribuer une adresse IP statique en [exécutant la cmdlet Set-AzureStaticVNetIP Azure PowerShell][exécutant la cmdlet Set-AzureStaticVNetIP Azure PowerShell] afin que l'adresse IP soit conservée si vous devez arrêter la machine virtuelle à un moment donné.
4.  Attachez un autre disque à la machine virtuelle pour stocker la base de données Active Directory, les fichiers journaux et SYSVOL.
	5.  Cliquez sur **VM** \> **Attach** \> **Attach empty disk**.
	6.  Spécifiez une taille (par exemple, 10 Go) et acceptez les autres valeurs par défaut.
7.  Connectez-vous à la machine virtuelle et formatez l'autre disque.
	8.  Cliquez sur **Connect** pour vous connecter à la machine virtuelle, sur **Ouvrir** pour créer une session RDP, puis à nouveau sur **Connect**.
	9.  Remplacez les informations d'identification par les nouveaux nom d'utilisateur et mot de passe spécifiés.
	10. Dans Gestionnaire de serveur, cliquez sur **Outils** \> **Gestion de l'ordinateur**.
	11. Cliquez sur **Disk Management** et sur **Ok** pour initialiser le nouveau disque.
	12. Cliquez avec le bouton droit sur le nom du disque, puis cliquez sur **Nouveau volume simple**. Terminez l'Assistant pour formater le nouveau lecteur.

## <span id="installad"></span></a>Étape 3 : installation de Windows Server Active Directory

[Installez AD DS][Installez AD DS] en faisant appel à la procédure utilisée localement (interface utilisateur, fichier de réponse ou Windows PowerShell). Vous devez fournir des informations d'identification d'administrateur pour installer une nouvelle forêt. Pour spécifier l'emplacement de la base de données Active Directory, des fichiers journaux et de SYSVOL, modifiez l'emplacement de stockage par défaut afin qu'il soit défini non sur le lecteur de système d'exploitation, mais sur l'autre disque de données que vous avez attaché à la machine virtuelle.

Lorsque l'installation du contrôleur de domaine est terminée, connectez-vous à celui-ci après vous être reconnecté à la machine virtuelle. N'oubliez pas de spécifier les informations d'identification du domaine.

</p>
## <span id="dns"></span></a>Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure

1.  Cliquez sur **Réseaux virtuels**, double-cliquez sur le réseau virtuel que vous avez créé, puis cliquez sur **Configurer**.
2.  Sous **Serveurs DNS**, tapez le nom et le DIP du contrôleur de domaine, puis cliquez sur **Enregistrer**.
3.  Sélectionnez la machine virtuelle et cliquez sur **Redémarrer** pour que la machine virtuelle configure les paramètres de résolution DNS avec l'adresse IP du nouveau serveur DNS.

## <span id="domainmembers"></span></a>Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine

Créez d'autres machines virtuelles pour mettre en service les ordinateurs membres du domaine. Vous pouvez utiliser l'interface utilisateur ou Azure PowerShell. Si vous utilisez l'interface utilisateur, suivez la procédure de création de la première machine virtuelle. Joignez ensuite les machines virtuelles au domaine comme vous le feriez localement. Si vous utilisez Azure PowerShell, vous pouvez mettre en service les machines virtuelles et les ajouter au domaine lors de leur premier démarrage.

Cet exemple créera une machine virtuelle associée au domaine appelée DC2 qui exécute Windows Server 2012 R2 Datacenter. Après l'approvisionnement de DC2, connectez-vous y en tant qu'Admin du domaine et transformez-la en contrôleur de domaine de réplication.

Vous pouvez exécuter Get-AzureVMImage pour obtenir les noms des images. Par exemple, pour renvoyer une liste des images pour Windows Server 2012 R2, exécutez Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.

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
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Si vous réexécutez le script, vous devez fournir une valeur unique pour $service. Vous pouvez exécuter Test-AzureName -Service *nom du service*, qui est retourné si le nom est déjà pris.

## Voir aussi

-   [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure][Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure]

-   [Configuration d'un réseau virtuel sur le cloud uniquement dans le portail de gestion][Configuration d'un réseau virtuel sur le cloud uniquement dans le portail de gestion]

-   [Configuration d'un VPN de site à site dans le portail de gestion][Configuration d'un VPN de site à site dans le portail de gestion]

-   [Installation d'un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure][Installation d'un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure]

-   [Windows Azure IT Pro IaaS : (01) Principes fondamentaux d'une machine virtuelle][Windows Azure IT Pro IaaS : (01) Principes fondamentaux d'une machine virtuelle]

-   [Windows Azure IT Pro IaaS : (05) Création de réseaux virtuels et connectivité entre différents locaux][Windows Azure IT Pro IaaS : (05) Création de réseaux virtuels et connectivité entre différents locaux]

-   [Réseau virtuel Azure][réseau virtuel Azure]

-   [Installation et configuration d’Azure PowerShell][Installation et configuration d’Azure PowerShell]

-   [Azure PowerShell][Azure PowerShell]

-   [Cmdlets de gestion Azure][Cmdlets de gestion Azure]

-   [Définition de l'adresse IP statique d'une machine virtuelle Azure][Définition de l'adresse IP statique d'une machine virtuelle Azure]

-   [Attribution d'une IP statique à une machine virtuelle Azure][Attribution d'une IP statique à une machine virtuelle Azure]

-   [Installation d'une nouvelle forêt Active Directory][Installez AD DS]

-   [Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)][Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)]

-   [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure][Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure].

  [réseau virtuel Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156007.aspx
  [configurer un réseau privé virtuel de site à site à l'aide de l'Assistant du portail de gestion]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn133795.aspx
  [Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156090.aspx
  [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure]: http://www.microsoft.com/fr-fr/download/details.aspx?id=41684
  [Quelles sont les différences par rapport à une installation locale ?]: #differ
  [Étape 1 : création d'un réseau virtuel Azure]: #createvnet
  [Étape 2 : création d'une machine virtuelle exécutant le contrôleur de domaine et les rôles serveur DNS]: #createvm
  [Étape 3 : installation de Windows Server Active Directory]: #installad
  [Étape 4 : configuration du serveur DNS pour le réseau virtuel Azure]: #dns
  [Étape 5 : création de machines virtuelles pour les membres du domaine et participation au domaine]: #domainmembers
  [portail de gestion Azure]: https://manage.windowsazure.com
  [exécutant la cmdlet Set-AzureStaticVNetIP Azure PowerShell]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn630228.aspx
  [Installez AD DS]: http://technet.microsoft.com/library/jj574166.aspx
  [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [Configuration d'un réseau virtuel sur le cloud uniquement dans le portail de gestion]: http://msdn.microsoft.com/fr-fr/library/dn631643.aspx
  [Configuration d'un VPN de site à site dans le portail de gestion]: http://msdn.microsoft.com/fr-fr/library/dn133795.aspx
  [Installation d'un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IT Pro IaaS : (01) Principes fondamentaux d'une machine virtuelle]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT Pro IaaS : (05) Création de réseaux virtuels et connectivité entre différents locaux]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Installation et configuration d’Azure PowerShell]: http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156055.aspx
  [Cmdlets de gestion Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841
  [Définition de l'adresse IP statique d'une machine virtuelle Azure]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [Attribution d'une IP statique à une machine virtuelle Azure]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)]: http://technet.microsoft.com/fr-fr/library/hh831734.aspx
