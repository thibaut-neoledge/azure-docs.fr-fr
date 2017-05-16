---
title: "Installation d’un contrôleur de domaine Active Directory de réplication dans Azure | Microsoft Docs"
description: A tutorial that explains how to install a domain controller from an on-premises Active Directory forest on an Azure virtual machine.
services: virtual-network
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: virtual-network
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7b267f11a2989b1e621906a46ea4e3bf7f58ca2b
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installation d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure
Cette rubrique montre comment installer des contrôleurs de domaine supplémentaires (également connus sous le nom de contrôleurs de domaine de réplication) pour un domaine Active Directory local sur des machines virtuelles Azure dans un réseau virtuel Azure.

Les rubriques suivantes peuvent également vous intéresser :

* Vous pouvez, si vous le souhaitez, installer une nouvelle forêt Active Directory sur un réseau virtuel Azure. Dans ce cas, consultez la page [Installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md).
* Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Schéma du scénario
Dans ce scénario, des utilisateurs externes doivent accéder à des applications qui s'exécutent sur des serveurs appartenant à un domaine. Les machines virtuelles qui exécutent les serveurs d'applications et les contrôleurs de domaine de réplication sont installées sur un réseau virtuel Azure. Le réseau virtuel peut être connecté au réseau local par une connexion [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md), comme illustré dans le schéma suivant, ou vous pouvez utiliser [ExpressRoute](../expressroute/expressroute-locations-providers.md) pour obtenir une connexion plus rapide.

Les serveurs d’applications et les contrôleurs de domaine sont déployés dans des services cloud distincts afin de distribuer la charge de traitement et dans des [groupes à haute disponibilité](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour améliorer la tolérance aux pannes.
Les contrôleurs de domaine répliquent entre eux et avec les contrôleurs de domaine locaux à l'aide de la réplication Active Directory. Aucun outil de synchronisation n'est nécessaire.

![Schéma d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>création d'un site Active Directory pour le réseau virtuel Azure
Il est judicieux de créer un site dans Active Directory qui représente la région du réseau correspondant au réseau virtuel. Cela aide à optimiser l'authentification, la réplication et d'autres opérations de localisation du contrôleur de domaine. Les étapes suivantes expliquent comment créer un site. Pour plus d’informations, consultez [Ajout d’un nouveau site](https://technet.microsoft.com/library/cc781496.aspx).

1. Ouvrez Sites et services Active Directory : **Gestionnaire de serveur** > **Outils** > **Sites et services Active Directory**.
2. Créez un site représentant la région où vous avez créé un réseau virtuel Azure : cliquez sur **Sites** > **Action** > **Nouveau site** > tapez le nom du nouveau site, par exemple Azure US West > sélectionnez un lien de site > **OK**.
3. Créez un sous-réseau et associez-le au nouveau site : double-cliquez sur **Sites** > cliquez avec le bouton droit sur **Sous-réseaux** > **Nouveau sous-réseau** > tapez la plage d’adresses IP du réseau virtuel (par exemple, 10.1.0.0/16 dans le schéma du scénario) > sélectionnez le nouveau site Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Création d'un réseau virtuel Azure
1. Dans le [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > **Création personnalisée** et utilisez les valeurs suivantes pour finaliser l’Assistant.

   | Sur cette page de l'Assistant... | Spécifiez les valeurs suivantes |
   | --- | --- |
   |  **Détails du réseau virtuel** |<p>Nom : Tapez un nom pour le réseau virtuel, par exemple WestUSVNet.</p><p>Région : sélectionnez la région la plus proche.</p> |
   |  **Connectivité DNS et VPN** |<p>Serveurs DNS : Spécifiez le nom et l'adresse IP d'un ou plusieurs serveurs DNS locaux.</p><p>Connectivité : sélectionnez **Configuration d'un VPN de site à site**.</p><p>Réseau Local : Spécifier un nouveau réseau local.</p><p>Si vous utilisez ExpressRoute au lieu d’un VPN, consultez [Configuration d’une connexion ExpressRoute via un fournisseur Exchange](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Connectivité de site à site** |<p>Nom : Tapez un nom pour le réseau local.</p><p>Adresse IP de l’appareil VPN : définissez l’adresse IP publique de l’appareil à connecter au réseau virtuel. Le périphérique VPN ne peut pas se trouver derrière un traducteur d'adresses réseau.</p><p>Adresse : Spécifiez les plages d'adresses pour votre réseau local (par exemple 192.168.0.0/16 dans le schéma du scénario).</p> |
   |  **Espaces d’adressage du réseau virtuel** |<p>Espace d’adressage : spécifiez la plage d’adresses IP pour les machines virtuelles que vous souhaitez exécuter sur le réseau virtuel Azure (par exemple, 10.1.0.0/16 dans le schéma du scénario). Cette plage d’adresses et les plages d’adresses du réseau local ne peuvent pas se chevaucher.</p><p>Sous-réseaux : Spécifiez le nom et l'adresse d'un sous-réseau pour les serveurs d'applications (par exemple, sous-réseau frontal 10.1.1.0/24) et pour les contrôleurs de domaine (par exemple, sous-réseau principal 10.1.2.0/24).</p><p>Cliquez sur **Ajouter un sous-réseau de passerelle**.</p> |
2. Ensuite, vous devez configurer la passerelle du réseau virtuel pour créer une connexion VPN de site à site sécurisée. Consultez les instructions décrites dans la rubrique [Configurer une passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) .
3. Créez la connexion VPN de site à site entre le nouveau réseau virtuel et un périphérique VPN local. Consultez les instructions décrites dans la rubrique [Configurer une passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) .

## <a name="create-azure-vms-for-the-dc-roles"></a>création des machines virtuelles Azure pour les rôles de contrôleur de domaine
Répétez les étapes suivantes pour créer des machines virtuelles pour héberger le rôle de contrôleur de domaine en fonction des besoins. Vous devez déployer au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de panne. Si le réseau virtuel Azure inclut au moins deux contrôleurs de domaine configurés de manière similaire (ce sont tous deux des catalogues globaux, ils exécutent un serveur DNS et aucun d'eux ne contient de rôle FSMO, etc.), placez alors les machines virtuelles qui exécutent ces catalogues globaux dans un groupe à haute disponibilité afin d'améliorer la tolérance aux pannes.
Pour créer les machines virtuelles à l’aide de Windows PowerShell au lieu de l'interface utilisateur, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Compute** > **Machine virtuelle** > **Depuis la galerie**. Utilisez les valeurs suivantes pour terminer l'Assistant. Acceptez la valeur par défaut d'un paramètre, sauf si une autre valeur est suggérée ou requise.

   | Sur cette page de l'Assistant... | Spécifiez les valeurs suivantes |
   | --- | --- |
   |  **Choix d’une image** |Windows Server 2012 R2 Datacenter |
   |  **Configuration de la machine virtuelle** |<p>Nom de machine virtuelle : Tapez un nom en une seule partie (par exemple, AzureDC1).</p><p>Nouveau nom d'utilisateur : tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte intégré appelé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : saisissez un mot de passe</p> |
   |  **Configuration de la machine virtuelle** |<p>Service cloud : sélectionnez <b>Créer un nouveau service de cloud computing</b> pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront le rôle de contrôleur de domaine.</p><p>Nom DNS du service cloud : indiquez un nom global unique</p><p>Région/Groupe d’affinités/Réseau virtuel : indiquez le nom du réseau virtuel (par exemple WestUSVNet).</p><p>Choisissez <b>Utiliser un compte de stockage généré automatiquement pour la première machine virtuelle</b>, puis sélectionnez ce même nom de compte de stockage lors de la création de machines virtuelles supplémentaires qui hébergeront le rôle de contrôleur de domaine.</p><p>Groupe à haute disponibilité : Choisissez <b>Créer un groupe à haute disponibilité</b>.</p><p>Nom du groupe à haute disponibilité : tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p> |
   |  **Configuration de la machine virtuelle** |<p>Sélectionnez <b>Installer l'agent de la machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p> |
2. Attachez un disque à chaque machine virtuelle qui exécutera le rôle de serveur de contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple, 10 Go) et laissez l'option **Préférences de cache d'hôte** définie sur **Aucun**. Pour ces étapes, consultez [Association d'un disque de données à une machine virtuelle Windows](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
3. Après votre première connexion à la machine virtuelle, ouvrez **Gestionnaire de serveur** > **Services de fichiers et de stockage** pour créer un volume sur ce disque à l'aide de NTFS.
4. Réservez une adresse IP statique pour les machines virtuelles qui exécuteront le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez Microsoft Web Platform Installer, [installez Azure PowerShell](/powershell/azure/overview) , puis exécutez la cmdlet Set-AzureStaticVNetIP. Par exemple :

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 Set-AzureStaticVNetIP -IPAddress 10.0.0.4 Update-AzureVM

Pour plus d’informations sur la configuration d’une adresse IP, consultez [Configuration d’une adresse IP interne statique pour une machine virtuelle](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>installation des services AD DS sur des machines virtuelles Azure
Connectez-vous à une machine virtuelle et vérifiez que vous disposez d'une connectivité sur la connexion VPN de site à site ou la connexion ExpressRoute aux ressources sur votre réseau local. Ensuite, installez les services AD DS sur les machines virtuelles Azure. Vous pouvez appliquer la même procédure que celle utilisée pour installer un contrôleur de domaine supplémentaire sur votre réseau local (interface utilisateur, Windows PowerShell ou un fichier de réponses). Lors de l'installation des services AD DS, veillez à spécifier le nouveau volume comme emplacement de la base de données Active Directory, des journaux et de SYSVOL. Si vous avez besoin d’un rappel concernant l’installation des services AD DS, consultez [Installer les services de domaine Active Directory (Niveau 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [Installer un contrôleur de domaine de réplication Windows Server 2012 dans un domaine existant (Niveau 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>reconfiguration du serveur DNS pour le réseau virtuel
1. Dans le [portail Azure Classic](https://manage.windowsazure.com), cliquez sur le nom du réseau virtuel, puis cliquez sur l'onglet **Configurer** pour [reconfigurer les adresses IP du serveur DNS pour votre réseau virtuel](../virtual-network/virtual-networks-manage-dns-in-vnet.md) afin d'utiliser les adresses IP statiques attribuées aux contrôleurs de domaines de réplica au lieu des adresses IP des serveurs DNS locaux.
2. Pour vous assurer que toutes les réplicas de machines virtuelles de contrôleur de domaine sur le réseau virtuel sont configurées pour utiliser des serveurs DNS sur le réseau virtuel, cliquez sur **Machines virtuelles** et cliquez sur la colonne d’état pour chaque machine virtuelle, puis cliquez sur **Redémarrer**. Attendez que la machine virtuelle affiche l’état **En cours d’exécution** avant d’essayer de vous y connecter.

## <a name="create-vms-for-application-servers"></a>création de machines virtuelles pour les serveurs d'applications
1. Répétez les étapes suivantes pour créer des machines virtuelles exécutables en tant que serveurs d’applications. Acceptez la valeur par défaut d'un paramètre, sauf si une autre valeur est suggérée ou requise.

   | Sur cette page de l'Assistant... | Spécifiez les valeurs suivantes |
   | --- | --- |
   |  **Choix d’une image** |Windows Server 2012 R2 Datacenter |
   |  **Configuration de la machine virtuelle** |<p>Nom de machine virtuelle : Tapez un nom en une seule partie (par exemple, AppServer1).</p><p>Nouveau nom d'utilisateur : tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte intégré appelé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : saisissez un mot de passe</p> |
   |  **Configuration de la machine virtuelle** |<p>Service cloud : sélectionnez **Créer un nouveau service de cloud computing** pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Nom DNS du service cloud : indiquez un nom global unique</p><p>Région/Groupe d’affinités/Réseau virtuel : indiquez le nom du réseau virtuel (par exemple WestUSVNet).</p><p>Compte de stockage : Choisissez **Utiliser un compte de stockage généré automatiquement** pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage lorsque de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Groupe à haute disponibilité : Choisissez **Créer un groupe à haute disponibilité**.</p><p>Nom du groupe à haute disponibilité : tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p> |
   |  **Configuration de la machine virtuelle** |<p>Sélectionnez <b>Installer l'agent de la machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p> |
2. Une fois que chaque machine virtuelle supplémentaire est approvisionnée, connectez-vous et joignez-la au domaine. Dans le **Gestionnaire de serveur**, cliquez sur **Serveur Local** > **GROUPE DE TRAVAIL** > **MODIFIER...** puis sélectionnez **Domaine** et tapez le nom de votre domaine local. Spécifiez les informations d'identification d'un utilisateur de domaine, puis redémarrez la machine virtuelle pour terminer la jonction au domaine.

Pour créer les machines virtuelles à l’aide de Windows PowerShell au lieu de l'interface utilisateur, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Pour plus d'informations sur l'utilisation de Windows PowerShell, consultez [Prise en main des cmdlets Azure](/powershell/azure/overview) et le [Guide de référence des cmdlets Azure](/powershell/azure/get-started-azureps).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Instructions pour le déploiement de Windows Server Active Directory sur Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Comment télécharger des contrôleurs de domaine Hyper-V locaux existants vers Azure à l’aide de PowerShell Azure](http://support.microsoft.com/kb/2904015)
* [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Iaas des professionnels de l’informatique Microsoft Azure : principes de base des machines virtuelles (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Iaas des professionnels de l’informatique Microsoft Azure :(05) Création de réseaux virtuels pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlets de gestion Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

