<properties urlDisplayName="Replica domain controller" pageTitle="Installation d'un contrôleur de domaine de réplication dans Azure" metaKeywords="" description="Didacticiel expliquant comment installer un contrôleur de domaine à partir d'une forêt Active Directory locale sur une machine virtuelle Azure." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller on an Azure Virtual Network" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="Justinha" />


#Installation d'un contrôleur de domaine Active Directory de réplication dans Azure Virtual Network

Cette rubrique montre comment installer des contrôleurs de domaine supplémentaires (également connus sous le nom de contrôleurs de domaine de réplication) pour un domaine Active Directory local sur des machines virtuelles Azure dans un réseau virtuel Azure. 

Les rubriques suivantes peuvent également vous intéresser :

- Vous pouvez, si vous le souhaitez, installer une nouvelle forêt Active Directory sur un réseau virtuel Azure. Dans ce cas, consultez la page [Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/).
-  Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Pour obtenir les procédures pas à pas de création d'un environnement test dans Azure incluant AD DS, consultez la page [Guide des laboratoires de test : Configuration de base dans Azure](http://www.microsoft.com/fr-fr/download/details.aspx?id=41684).


##Sommaire##

* [Schéma du scénario](#diagram)
* [Étape 1 : création d'un site Active Directory pour le réseau virtuel Azure](#createadsite)
* [Étape 2 : création d'un réseau virtuel Azure](#createvnet)
* [Étape 3 : création des machines virtuelles Azure pour les rôles de contrôleur de domaine](#createdcvms)
* [Étape 4 : installation des services AD DS sur des machines virtuelles Azure](#installadds)
* [Étape 5 : création de machines virtuelles pour les serveurs d'applications](#createappvms)
* [Ressources supplémentaires](#resources)

<h2><a id="diagram"></a>Schéma du scénario</h2>

Dans ce scénario, des utilisateurs externes doivent accéder à des applications qui s'exécutent sur des serveurs appartenant à un domaine. Les machines virtuelles qui exécutent les serveurs d'applications et les contrôleurs de domaine de réplication sont installées sur un réseau virtuel Azure. Le réseau virtuel peut être connecté au réseau local par une connexion [VPN de site à site](http://msdn.microsoft.com/library/azure/dn133795.aspx), comme illustré dans le schéma suivant, ou vous pouvez utiliser [ExpressRoute](http://azure.microsoft.com/services/expressroute/) pour obtenir une connexion plus rapide. 

Les serveurs d'applications et les contrôleurs de domaine sont déployés dans des [services cloud](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/) distincts afin de distribuer la charge de traitement et dans des [groupes à haute disponibilité](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/) pour améliorer la tolérance aux pannes. 
Les contrôleurs de domaine répliquent entre eux et avec les contrôleurs de domaine locaux à l'aide de la réplication Active Directory. Aucun outil de synchronisation n'est nécessaire.

![][1]

<h2><a id="createadsite"></a>Étape 1 : création d'un site Active Directory pour le réseau virtuel Azure</h2>

Il est judicieux de créer un site dans Active Directory qui représente la région du réseau correspondant au réseau virtuel. Cela aide à optimiser l'authentification, la réplication et d'autres opérations de localisation du contrôleur de domaine. Les étapes suivantes expliquent comment créer un site. Pour plus d'informations, consultez [Ajout d'un nouveau site](http://technet.microsoft.com/library/cc781496.aspx).

1. Ouvrez Sites et services Active Directory : **Gestionnaire de serveur** > **Outils** > **Sites et services Active Directory**.
2. Créez un site représentant la région où vous avez créé un réseau virtuel Azure : cliquez sur **Sites** > **Action** > **Nouveau site** > tapez le nom du nouveau site, par exemple Azure US West > sélectionnez un lien de site > **OK**.
3. Créez un sous-réseau et associez-le au nouveau site : double-cliquez sur **Sites** > cliquez avec le bouton droit sur **Sous-réseaux** > **Nouveau sous-réseau** > tapez la plage d'adresses IP du réseau virtuel (par exemple, 10.1.0.0/16 dans le schéma du scénario) > sélectionnez le nouveau site Azure > **OK**.

<h2><a id="createvnet"></a>Étape 2 : création d'un réseau virtuel Azure</h2>

<ol><li><p>In the Azure Management Portal, click <b>New</b> > <b>Network Services</b> > <b>Virtual Network</b> > <b>Custom Create</b> and use the following values to complete the wizard.</p>

<table style="width:100%">
<tr>
<td>Dans cette page de l'Assistant</td>
<td>Spécifiez les valeurs suivantes</td>
</tr>
<tr>
<td><b>Détails du réseau virtuel</b></td>
<td><ul><li>Nom : Tapez un nom pour le réseau virtuel, par exemple WestUSVNet.</li><li>Region: Il s'agit de l'emplacement Azure où se trouvera le réseau virtuel, par exemple l'Ouest des États-Unis. Cet emplacement ne peut pas être modifié une fois le réseau virtuel créé.</li></ul>
</td>
</tr>
<tr>
<td><b>Serveurs DNS et connectivité VPN</b></td>
<td><ul><li>Serveurs DNS : Spécifiez le nom et l'adresse IP d'un ou plusieurs serveurs DNS locaux.</li><li>Connectivité : Sélectionnez <b>Configurer un réseau VPN de site à site</b>.</li><li>Réseau local : Spécifiez un nouveau réseau local.</li></ul>Si vous utilisez ExpressRoute au lieu d'un VPN, consultez <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Configurer une connexion ExpressRoute via un fournisseur Exchange</a>.</td>
</tr>
<tr>
<td><b>Connectivité de site à site</b></td>
<td><ul><li>Nom : Tapez un nom pour le réseau local.</li><li>Adresse IP du périphérique VPN : Spécifiez l'adresse IP publique du périphérique qui se connectera au réseau virtuel. Le périphérique VPN ne peut pas se trouver derrière un traducteur d'adresses réseau.</li><li>Adresse : Spécifiez les plages d'adresses pour votre réseau local (par exemple 192.168.0.0/16 dans le schéma du scénario).</li></ul></td>
</tr>
<tr>
<td><b>Espaces d'adressage du réseau virtuel</b></td>
<td><ul><li>Espace d'adressage : Spécifiez la plage d'adresses IP pour les machines virtuelles que vous souhaitez exécuter sur le réseau virtuel Azure (par exemple, 10.1.0.0/16 dans le schéma du scénario). Cette plage d'adresses ne peut pas chevaucher les plages d'adresses du réseau local.</li><li>Sous-réseaux : Spécifiez le nom et l'adresse d'un sous-réseau pour les serveurs d'applications (par exemple, sous-réseau frontal 10.1.1.0/24) et pour les contrôleurs de domaine (par exemple, sous-réseau principal 10.1.2.0/24).</li><li>Cliquez sur <b>Ajouter un sous-réseau de passerelle</b>.</li></ul></td>
</tr>
</table>
</li>
<li><p>Ensuite, vous devez configurer la passerelle du réseau virtuel pour créer une connexion VPN de site à site sécurisée. Consultez <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configuration d'une passerelle de réseau virtuel dans le portail de gestion</a> pour obtenir des instructions.</p>
</li>
<li><p>Créez la connexion VPN de site à site entre le nouveau réseau virtuel et un périphérique VPN local. Consultez <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configuration d'une passerelle de réseau virtuel dans le portail de gestion</a> pour obtenir des instructions.</p>
</li>
</ol>

<h2><a id="createdcvms"></a>Étape 3 : création des machines virtuelles Azure pour les rôles de contrôleur de domaine</h2>

<p>Répétez les étapes suivantes pour créer des machines virtuelles pour héberger le rôle de contrôleur de domaine en fonction des besoins. Vous devez déployer au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de panne. </p>


<ol><li><p>In the Azure Management portal, click <b>New</b> > <b>Compute</b> > <b>Virtual Machine</b> > <b>From Gallery</b>. Use the following values to complete the wizard. Accept the default value for a setting unless another value is suggested or required.</p>
<table style="width:100%">
<tr>
<td><b>Dans cette page de l'Assistant</b></td>
<td><b>Spécifiez les valeurs suivantes</b></td>
</tr>
<tr>
<td><b>Choisir une image</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configuration de la machine virtuelle</b></td>
<td><ul><li>Nom de la machine virtuelle : Tapez un nom d'étiquette unique (par exemple, AzureDC2).</li><li>Nouveau nom d'utilisateur : Tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte d'administrateur intégré nommé Administrateur ne fonctionnera pas.</li><li>Nouveau mot de passe/confirmation : Tapez un mot de passe</li></ul></td>
</tr>
<tr>
<td><b>Configuration de la machine virtuelle</b></td>
<td><ul><li>Service de cloud computing : Choisissez <b>Créer un nouveau service de cloud computing</b> pour la première machine virtuelle et sélectionnez ce même nom de service cloud quand vous créez plusieurs machines virtuelles qui hébergeront le rôle de contrôleur de domaine.</li><li>Nom du cloud Service DNS : Spécifiez un nom global unique</li><li>Région/Groupe d'affinités/Réseau virtuel : Spécifiez le nom du réseau virtuel (par exemple, WestUSVNet).</li><li>Compte de stockage : Choisissez <b>Utiliser un compte de stockage généré automatiquement</b> pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage quand vous créez plusieurs machines virtuelles qui hébergeront le rôle de contrôleur de domaine.</li><li>Groupe à haute disponibilité : Choisissez <b>Créer un groupe à haute disponibilité</b>.</li><li>Nom du groupe à haute disponibilité : Tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</li></ul></td>
</tr>
<tr>
<td><b>Configuration de la machine virtuelle</b></td>
<td>Sélectionnez <b>Installer l'agent de machine virtuelle</b> et toutes les autres extensions dont vous avez besoin.</td>
</tr>
</table>
</li>
<li><p>Attachez un disque à chaque machine virtuelle qui exécutera le rôle de serveur de contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple 10 Go) et laissez les <b>Préférences de cache d'hôte</b> définies sur <b>Aucune</b>. Après votre première connexion à la machine virtuelle, ouvrez <b>Gestionnaire de serveur</b> > <b>Services de fichiers et de stockage</b> pour créer un volume sur ce disque à l'aide de NTFS.</p></li>
<li><p>Réservez une adresse IP statique pour les machines virtuelles qui exécuteront le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez Microsoft Web Platform Installer, <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">installez Azure PowerShell</a> et exécutez l'applet de commande <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a> .</p></li>
<li><p>Dans le portail de gestion Azure, cliquez sur le nom du réseau virtuel, puis cliquez sur l'onglet <b>Configurer</b> pour <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">reconfigurer les adresses IP de serveurs DNS pour votre réseau virtuel</a> afin d'utiliser les adresses IP statiques affectées aux contrôleurs de domaine de réplication plutôt que les adresses IP des serveurs DNS locaux. </p>
</li>
<li><p>Pour vous assurer que toutes les machines virtuelles contrôleurs de domaine de réplication sur le réseau virtuel sont configurées pour utiliser des serveurs DNS sur le réseau virtuel, cliquez sur <b>Machines virtuelles</b>, cliquez sur la colonne d'état pour chaque machine virtuelle, puis cliquez sur <b>Redémarrer</b>. Attendez que la machine virtuelle soit à l'état <b>En cours d'exécution</b> avant d'essayer de vous y connecter. 
</p>
</li>
</ol>

<h2><a id="installadds"></a>Étape 4 : installation des services AD DS sur des machines virtuelles Azure</h2>

Connectez-vous à une machine virtuelle et vérifiez que vous disposez d'une connectivité sur la connexion VPN de site à site ou la connexion ExpressRoute aux ressources sur votre réseau local. Ensuite, installez les services AD DS sur les machines virtuelles Azure. Vous pouvez appliquer la même procédure que celle utilisée pour installer un contrôleur de domaine supplémentaire sur votre réseau local (interface utilisateur, Windows PowerShell ou un fichier de réponses). Lors de l'installation des services AD DS, veillez à spécifier le nouveau volume comme emplacement de la base de données Active Directory, des journaux et de SYSVOL. Si vous avez besoin d'un rappel concernant l'installation des services AD DS, consultez [Installer les services de domaine Active Directory (Niveau 100)](http://technet.microsoft.com/library/hh472162.aspx) ou [Installer un contrôleur de domaine de réplication Windows Server 2012 dans un domaine existant (Niveau 200)](http://technet.microsoft.com/library/jj574134.aspx).

<h2><a id="x=createappvms"></a>Étape 5 : création de machines virtuelles pour les serveurs d'applications</h2>

<ol><li><p>Repeat the following steps to create VMs to run as application servers. Accept the default value for a setting unless another value is suggested or required.</p>

<table style="width:100%">
<tr>
<td><b>Dans cette page de l'Assistant</b></td>
<td><b>Spécifiez les valeurs suivantes</b></td>
</tr>
<tr>
<td><b>Choisir une image</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configuration de la machine virtuelle</b></td>
<td><ul><li>Nom de la machine virtuelle : Tapez un nom d'étiquette unique (par exemple, TreyAppServer1).</li><li>Nouveau nom d'utilisateur : Tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte d'administrateur intégré nommé Administrateur ne fonctionnera pas.</li><li>Nouveau mot de passe/confirmation :  Tapez un mot de passe</li></ul></td>
</tr>
<tr>
<td><b>Configuration de la machine virtuelle</b></td>
<td><ul><li>Service de cloud computing : Sélectionnez Créer un nouveau service de cloud computing pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront l'application.</li><li>Nom du cloud Service DNS : Spécifiez un nom global unique</li><li>Région/Groupe d'affinités/Réseau virtuel : Spécifiez le nom du réseau virtuel (par exemple, WestUSVNet).</li><li>Compte de stockage : Choisissez <b>Utiliser un compte de stockage généré automatiquement</b> pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage quand vous créez plusieurs machines virtuelles qui hébergeront le rôle de contrôleur de domaine.</li><li>Groupe à haute disponibilité : Choisissez <b>Créer un groupe à haute disponibilité</b>.</li><li>Nom du groupe à haute disponibilité : Tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</li></ul></td>
</tr>
<tr>
<td><b>Configuration de la machine virtuelle</b></td>
<td>Sélectionnez <b>Installer l'agent de machine virtuelle</b> et toutes les autres extensions dont vous avez besoin.</td>
</tr>
</table>


</li>
<li><p>Une fois que chaque machine virtuelle supplémentaire est approvisionnée, connectez-vous et joignez-la au domaine. Dans <b>Gestionnaire de serveur</b>, cliquez sur <b>Serveur local</b> > <b>Groupe de travail</b> > <b>Modifier...</b> puis sélectionnez <b>Domaine</b> et tapez le nom de votre domaine local. Spécifiez les informations d'identification d'un utilisateur de domaine, puis redémarrez la machine virtuelle pour terminer la jonction au domaine.
</p>
</li>
</ol>
<p>
Comme alternative à l'utilisation du portail de gestion pour approvisionner des machines virtuelles, vous pouvez utiliser Windows PowerShell pour Microsoft Azure. Utilisez <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> et <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig</a> pour approvisionner une machine virtuelle comme machine appartenant à un domaine lors de son premier démarrage et utilisez <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM</a> Pour créer la machine virtuelle proprement dite. 
</p>

Pour plus d'informations sur l'utilisation de Windows PowerShell, consultez [Prise en main d'Azure PowerShell](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156055.aspx) et [Applets de commande de gestion Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841).


<h2><a id="resources"></a>Ressources supplémentaires</h2>

-  [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Comment télécharger des contrôleurs de domaine Hyper-V locaux existants vers Azure à l'aide de PowerShell Azure](http://support.microsoft.com/kb/2904015)

-  [Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Windows Azure IT Pro IaaS : (01) Concepts de base des machines virtuelles](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT Pro IaaS : (05) Création d'un réseau virtuel pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Applets de commande de gestion Azure](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=35.2-->
