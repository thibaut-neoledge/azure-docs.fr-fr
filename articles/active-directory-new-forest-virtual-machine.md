<properties 
	pageTitle="Installation d'une forêt Active Directory sur un réseau virtuel Azure" 
	description="Didacticiel qui explique comment créer une forêt Active Directory sur une machine virtuelle dans Azure Virtual Network." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="03/04/2015" 
	ms.author="Justinha"/>


# Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure

Cette rubrique explique comment créer un environnement Windows Server Active Directory sur un réseau virtuel Azure, sur une machine virtuelle d'un [réseau virtuel Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). Dans ce cas, le réseau virtuel Azure n'est pas connecté à un réseau local. 

Les rubriques suivantes peuvent également vous intéresser :

- Vous pouvez éventuellement [configurer un réseau privé virtuel de site à site à l'aide de l'Assistant du Portail de gestion](http://msdn.microsoft.com/library/windowsazure/dn133795.aspx), puis installer une nouvelle forêt ou étendre une forêt locale à un réseau virtuel Azure. Dans ce cas, consultez [Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure](virtual-networks-install-replica-active-directory-domain-controller.md).
-  Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez la rubrique [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).


## Quelles sont les différences par rapport à une installation locale ?

Les différences entre l'installation d'un contrôleur de domaine dans Azure ou localement sont minimes. Le tableau suivant répertorie les principales différences. 

Pour configurer...  | Local | Réseau virtuel Azure	
------------- | -------------  | ------------
**Adresse IP pour le contrôleur de domaine**  | Attribuez une adresse IP statique aux propriétés de carte réseau   | Exécutez l'applet de commande Set-AzureStaticVNetIP pour attribuer une adresse IP statique
**Programme de résolution du client DNS**  | Définissez l'adresse des serveurs DNS préféré et auxiliaire dans les propriétés de carte réseau des membres du domaine   | Définissez l'adresse du serveur DNS dans les propriétés du réseau virtuel
**Stockage de base de données Active Directory**  | Définissez éventuellement l'emplacement de stockage par défaut avec un autre lecteur que C:\  | Vous devez définir l'emplacement de stockage par défaut avec un autre lecteur que le lecteur C:\



## création d'un réseau virtuel Azure

1. Connectez-vous au portail de gestion Azure.
2. Créez un réseau virtuel. Cliquez sur **Réseaux** > **Créer un réseau virtuel**. Utilisez les valeurs du tableau suivant pour compléter l'Assistant. 

	Dans cette page de l'Assistant...  | Spécifiez les valeurs suivantes
	------------- | -------------
	**Détails du réseau virtuel**  | <p>Nom : entrez le nom du réseau virtuel</p><p>Region: sélectionnez la région la plus proche</p>
	**DNS et VPN**  | <p>N'indiquez pas de serveur DNS</p><p>Ne sélectionnez pas d'option VPN</p>
	**Espaces d'adressage du réseau virtuel**  | <p>Subnet name: entrez le nom du sous-réseau</p><p>IP de démarrage : <b>10.0.0.0</b></p><p>CIDR : <b>/24 (256)</b></p>



## Création de machines virtuelles exécutant le contrôleur de domaine et les rôles serveur DNS
 
Répétez les étapes suivantes pour créer des machines virtuelles pour héberger le rôle de contrôleur de domaine en fonction des besoins. Vous devez déployer au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de panne. Si le réseau virtuel Azure inclut au moins deux contrôleurs de domaine configurés de manière similaire (ce sont tous deux des catalogues globaux, ils exécutent un serveur DNS et aucun d'eux ne contient de rôle FSMO, etc.), placez alors les machines virtuelles qui exécutent ces catalogues globaux dans un groupe à haute disponibilité afin d'améliorer la tolérance aux pannes.

1. Dans le portail de gestion Azure, cliquez sur **Nouveau** > **Calcul** > **Machine virtuelle** > **À partir de la galerie**. Utilisez les valeurs suivantes pour terminer l'Assistant. Acceptez la valeur par défaut d'un paramètre, sauf si une autre valeur est suggérée ou requise.

    Dans cette page de l'Assistant...  |  Spécifiez les valeurs suivantes
	------------- | -------------
	**Choisir une image**  | Windows Server 2012 R2 Datacenter
	**Configuration de machine virtuelle**  | <p>Nom de la machine virtuelle : Tapez un nom en une seule partie (par exemple, AzureDC1).</p><p>Nouveau nom d'utilisateur : Tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte d'administrateur intégré nommé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : Tapez un mot de passe</p>
	**Configuration de la machine virtuelle**  | <p>Service de cloud computing : Sélectionnez <b>Créer un nouveau service de cloud computing</b> pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront le rôle de contrôleur de domaine.</p><p>Nom DNS du service de cloud computing : Spécifiez un nom global unique</p><p>Région/Groupe d'affinités/Réseau virtuel : Spécifiez le nom du réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez <b>Utiliser un compte de stockage généré automatiquement</b> pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage lors de la création de machines virtuelles supplémentaires qui hébergeront le rôle de contrôleur de domaine.</p><p>Groupe à haute disponibilité : Choisissez <b>Créer un groupe à haute disponibilité</b>.</p><p>Nom du groupe à haute disponibilité : Tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p>
	**Configuration de machine virtuelle**  | <p>Sélectionnez <b>Installer l'Agent de machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p>
2. Attachez un disque à chaque machine virtuelle qui exécutera le rôle de serveur de contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple, 10 Go) et laissez l'option **Préférences de cache d'hôte** définie sur **Aucun**. Après la première connexion à la machine virtuelle, ouvrez **Gestionnaire de serveur** > **Service de fichiers et de stockage** pour créer un volume sur ce disque à l'aide de NTFS.
3. Réservez une adresse IP statique pour les machines virtuelles qui exécuteront le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez Microsoft Web Platform Installer et [installez Azure PowerShell](powershell-install-configure.md) , puis exécutez l'applet de commande Set-AzureStaticVNetIP. Par exemple :

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Pour plus d'informations sur la définition d'une adresse IP statique, consultez la rubrique [Configuration d'une adresse IP interne statique pour une machine virtuelle](https://msdn.microsoft.com/library/azure/dn630228.aspx).

## installation de Windows Server Active Directory

Faites appel à la même procédure utilisée localement pour [installer AD DS](https://technet.microsoft.com/library/jj574166.aspx) (pour cela, vous pouvez utiliser l'interface utilisateur, un fichier de réponses ou Windows PowerShell). Vous devez fournir des informations d'identification d'administrateur pour installer une nouvelle forêt. Pour spécifier l'emplacement de la base de données Active Directory, des fichiers journaux et de SYSVOL, modifiez l'emplacement de stockage par défaut afin qu'il soit défini non sur le lecteur de système d'exploitation, mais sur l'autre disque de données que vous avez attaché à la machine virtuelle. 

Lorsque l'installation du contrôleur de domaine est terminée, connectez-vous à celui-ci après vous être reconnecté à la machine virtuelle. N'oubliez pas de spécifier les informations d'identification du domaine.

## Réinitialisation du serveur DNS pour le réseau virtuel Azure

1. Réinitialisez la configuration du redirecteur DNS sur le nouveau serveur contrôleur de domaine/DNS. 
  1. Dans Gestionnaire de serveur, cliquez sur **Outils** > **DNS**. 
  2. Dans **Gestionnaire DNS**, cliquez avec le bouton droit sur le nom du serveur DNS puis cliquez sur **Propriétés**. 
  3. Sous l'onglet **Redirecteurs**, cliquez sur l'adresse IP du redirecteur puis cliquez sur **Modifier**.  Sélectionnez l'adresse IP, puis cliquez sur **Supprimer**. 
  4. Cliquez sur **OK** pour fermer l'éditeur, puis à nouveau sur **Ok** pour fermer les propriétés du serveur DNS. 
2. Mettez à jour les paramètres du serveur DNS pour le réseau virtuel. 
  1. Cliquez sur **Réseaux virtuels** > double-cliquez sur le réseau virtuel que vous avez créé > **Configurer** > **Serveurs DNS**, entrez le nom et l'adresse DIP de l'une des machines virtuelles qui exécutent le rôle de serveur contrôleur de domaine/DNS, puis cliquez sur **Enregistrer**. 
  2. Sélectionnez la machine virtuelle et cliquez sur **Redémarrer** pour que la machine virtuelle configure les paramètres de résolution DNS avec l'adresse IP du nouveau serveur DNS. 


## Création de machines virtuelles pour les membres du domaine

1. Répétez les étapes suivantes pour créer des machines virtuelles qui exécuteront le rôle de serveur d'applications. Acceptez la valeur par défaut d'un paramètre, sauf si une autre valeur est suggérée ou requise.

	Dans cette page de l'Assistant...  | Spécifiez les valeurs suivantes
	------------- | -------------
	**Choisir une image**  | Windows Server 2012 R2 Datacenter
	**Configuration de machine virtuelle**  | <p>Nom de la machine virtuelle : Tapez un nom en une seule partie (par  exemple, AppServer1).</p><p>Nouveau nom d'utilisateur : Tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte d'administrateur intégré nommé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : Tapez un mot de passe</p>
	**Configuration de machine virtuelle**  | <p>Service de cloud computing : Sélectionnez **Créer un nouveau service de cloud computing** pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Nom DNS du service de cloud computing : Spécifiez un nom global unique</p><p>Région/Groupe d'affinités/Réseau virtuel : Spécifiez le nom du réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez **Utiliser un compte de stockage généré automatiquement** pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage lorsque de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Groupe à haute disponibilité : Choisissez **Créer un groupe à haute disponibilité**.</p><p>Nom du groupe à haute disponibilité : Tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p>
	**Configuration de machine virtuelle**  | <p>Sélectionnez <b>Installer l'Agent de machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p>
2. Une fois que chaque machine virtuelle supplémentaire est approvisionnée, connectez-vous et joignez-la au domaine. Dans **Gestionnaire de serveur**, cliquez sur **Serveur Local** > **GROUPE DE TRAVAIL** > **Modifier...** , puis sélectionnez **Domaine** et tapez le nom de votre domaine local. Spécifiez les informations d'identification d'un utilisateur de domaine, puis redémarrez la machine virtuelle pour terminer la jonction au domaine.

Comme alternative à l'utilisation du portail de gestion pour approvisionner des machines virtuelles, vous pouvez utiliser Windows PowerShell pour Microsoft Azure. Utilisez [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx) et [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) pour approvisionner une machine virtuelle comme machine appartenant à un domaine lors de son premier démarrage et utilisez [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) pour créer la machine virtuelle proprement dite. 


Pour plus d'informations sur l'utilisation de Windows PowerShell, consultez [Prise en main des applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) et le [Guide de référence des applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## Voir aussi

-  [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configuration d'un réseau virtuel pour le cloud uniquement dans le Portail de gestion](https://msdn.microsoft.com/library/dn631643.aspx)

-  [Configuration d'un réseau VPN de site à site dans le Portail de gestion](https://msdn.microsoft.com/library/dn133795.aspx)

-  [Installation d'un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure](virtual-networks-install-replica-active-directory-domain-controller.md)

-  [Microsoft Azure IT Pro IaaS : (01) Concepts de base des machines virtuelles](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS : (05) Création d'un réseau virtuel pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Présentation du réseau virtuel](https://msdn.microsoft.com/library/azure/jj156007.aspx)

-  [Installation et configuration d'Azure PowerShell](powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Guide de référence des applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Définition de l'adresse IP statique d'une machine virtuelle Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Attribution d'une adresse IP statique à une machine virtuelle Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Installation d'une nouvelle forêt Active Directory](https://technet.microsoft.com/library/jj574166.aspx)

-  [Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)](https://technet.microsoft.com/library/hh831734.aspx)

-  [Guide des laboratoires de test : configuration de base Windows Server 2012 R2 dans Azure](http://www.microsoft.com/download/details.aspx?id=41684).


<!--HONumber=47-->
