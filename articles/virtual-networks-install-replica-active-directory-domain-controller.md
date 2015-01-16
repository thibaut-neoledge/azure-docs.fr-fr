<properties urlDisplayName="Replica domain controller" pageTitle="Installation d'un contrôleur de domaine de réplica dans Azure" metaKeywords="" description="Un didacticiel qui explique comment installer un contrôleur de domaine de votre forêt Corp Active Directory sur votre machine virtuelle Azure." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#Installation d'un contrôleur de domaine Active Directory de réplication dans Azure Virtual Network

Ce didacticiel vous guide lors de l'installation d'un contrôleur de domaine supplémentaire à partir de votre forêt Corp Active Directory sur une machine virtuelle sur [Azure Virtual Network](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156007.aspx). Dans ce didacticiel, le réseau virtuel de la machine virtuelle est connecté au réseau de votre société. Pour obtenir des informations conceptuelles sur l'installation des services de domaine Active Directory (également nommés " AD DS ") sur un réseau virtuel Azure, consultez la page [Instructions pour le déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156090.aspx).

##Sommaire##

* [Configuration requise](#Prerequisites)
* [Étape 1 : vérification de l'adresse IP statique pour YourPrimaryDC](#verifystaticip)
* [Étape 2 : installation de la forêt Corp](#installforest)
* [Étape 3 : création de sous-réseaux et de sites](#subnets)
* [Étape 4 : installation d'un contrôleur de domaine supplémentaire dans CloudSite](#cloudsite)
* [Étape 5 : validation de l'installation](#validate)
* [Étape 6 : configuration d'une machine virtuelle jointe au domaine au démarrage](#provisionvm)
* [Étape 7 : sauvegarde du contrôleur de domaine](#backup)
* [Étape 8 : test de l'authentification et de l'autorisation](#test)


<h2><a id="Prerequisites"></a>Configuration requise</h2>

-	[Configurez un réseau virtuel privé de site à site dans le portail de gestion](http://msdn.microsoft.com/fr-fr/library/dn133795.aspx) configuré entre le réseau virtuel Azure et le réseau Corp.
-	Créez un service cloud dans le réseau virtuel.
-	Déployez deux machines virtuelles dans le service cloud qui appartiennent au réseau virtuel (indiquez le sous-réseau où vous voulez placer la machine virtuelle). Pour plus d'informations, consultez la page [Ajout d'une machine virtuelle à un réseau virtuel](http://azure.microsoft.com/fr-fr/documentation/articles/virtual-networks-add-virtual-machine/). L'une des machines virtuelles doit avoir au moins la taille L pour que vous puissiez y attacher deux disques de données. Ces derniers sont requis pour stocker :
	- la base de données et les journaux Active Directory ;
	- les sauvegardes d'états du système.
-	Un réseau Corp avec deux machines virtuelles (YourPrimaryDC et FileServer).
-	L'infrastructure DNS déployée si les utilisateurs externes doivent résoudre des noms pour les comptes Active Directory. Dans ce cas, créez une délégation de zone DNS avant d'installer le serveur DNS sur le contrôleur de domaine ou autorisez l'Assistant Installation de services de domaine Active Directory à créer la délégation. Pour plus d'informations sur la création d'une délégation de zone DNS, consultez la page [Création d'une délégation de zone](http://technet.microsoft.com/library/cc753500.aspx).
-	Procédez comme suit pour configurer les paramètres de résolution de client DNS du contrôleur de domaine que vous installez sur une machine virtuelle Azure :
	- Serveur DNS préféré : serveur DNS local 
	- Serveur DNS auxiliaire : adresse de bouclage ou, si possible, un autre serveur DNS exécuté sur un contrôleur de domaine dans le même réseau virtuel.

<div class="dev-callout"> 
<b>Remarque</b>
<p>Vous devez fournir votre propre infrastructure DNS pour prendre en charge AD DS sur le réseau virtuel Azure. L'infrastructure DNS fournie par Azure pour cette version ne prend pas en charge certaines fonctionnalités requises par AD DS, comme une inscription d'enregistrement de ressource SRV. </p>
</div>

<div class="dev-callout"> 
<b>Remarque</b>
<p>Si vous avez déjà suivi la procédure d'<a href="/fr-fr/manage/services/networking/active-directory-forest/">installation d'une nouvelle forêt Active Directory dans Azure</a>, vous devrez peut-être supprimer AD DS du contrôleur de domaine sur le réseau virtuel Azure avant de commencer ce didacticiel. Pour plus d'informations sur la suppression d'AD DS, consultez la page <a href="http://technet.microsoft.com/fr-fr/library/cc771844(v=WS.10).aspx">Suppression d'un contrôleur de domaine d'un domaine</a>.</p>
</div>


<h2><a id="verifystaticip"></a>Étape 1 : vérification de l'adresse IP statique pour YourPrimaryDC</h2>

1. Connectez-vous à YourPrimaryDC sur le réseau Corp.

2. Dans Gestionnaire de serveur, cliquez sur Afficher les connexions réseau.

3. Cliquez avec le bouton droit sur la connexion au réseau local, puis cliquez sur Propriétés.

4. Cliquez sur Internet Protocol Version 4 (TCP/IPv4), puis sur Propriétés.

5. Vérifiez que le serveur a une adresse IP statique. 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>Étape 2 : installation de la forêt Corp</h2>

1. Dans la session RDP de la machine virtuelle, cliquez sur **Démarrer**, tapez **dcpromo** et appuyez sur ENTRÉE.

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. Sur la page d'accueil, cliquez sur **Suivant**.

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. Sur la page Compatibilité du système d'exploitation, cliquez sur **Suivant**.

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. Sur la page Choisissez une configuration de déploiement, cliquez sur **Créer un domaine dans une nouvelle forêt**, puis sur **Suivant**. 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. Sur la page Nommez le domaine racine de la forêt page, tapez **corp.contoso.com**, le nom de domaine complet du domaine racine de la forêt, puis cliquez sur **Suivant**. 

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. Sur la page Définir le niveau fonctionnel de la forêt, cliquez sur **Windows Server 2008 R2**, puis sur **Suivant**.

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. Sur la page Options supplémentaires pour le contrôleur de domaine, cliquez sur **Serveur DNS**, puis sur **Suivant**.

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. Si la délégation DNS suivante apparaît, cliquez sur **Oui**.

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. Sur la page Emplacement pour la base de données Active Directory, fichiers journaux et SYSVOL, entrez ou sélectionnez l'emplacement des fichiers, puis cliquez sur **Suivant**.

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. Sur la page Administrateur de restauration des services d'annuaire, entrez et validez le mot de passe DSRM, puis cliquez sur **Suivant**.

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. Sur la page Résumé, validez vos sélections, puis cliquez sur **Suivant**. 

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. Une fois l'Assistant Installation d'Active Directory terminé, cliquez sur **Terminer**, puis sur **Redémarrer maintenant** pour terminer l'installation. 

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>Étape 3 : création de sous-réseaux et de sites</h2>

1. Sur YourPrimaryDC, cliquez sur Démarrer, sur Outils d'administration, puis sur Sites et services Active Directory.
2. Cliquez sur **Sites**, cliquez avec le bouton droit sur **Sous-réseaux**, puis cliquez sur **Nouveau sous-réseau**.

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. Dans **Préfixe :**, tapez **10.1.0.0/24**, sélectionnez l'objet de site **Default-First-Site-Name**, puis cliquez sur **OK**.

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. Cliquez avec le bouton droit sur **Sites**, puis cliquez sur **Nouveau site**.

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. Dans Nom, tapez **CloudSite**, sélectionnez **DEFAULTIPSITELINK**, puis cliquez sur **OK**. 

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. Cliquez sur **OK** pour confirmer la création du site. 

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. Cliquez avec le bouton droit sur **Sous-réseaux**, puis cliquez sur **Nouveau sous-réseau**.

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. Dans **Préfixe :**, tapez **10.1.0.0/24**, sélectionnez l'objet de site **CloudSite**, puis cliquez sur **OK**.

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>Étape 4 : installation d'un contrôleur de domaine supplémentaire dans CloudSite</h2>

1. Connectez-vous à YourVMachine, cliquez sur **Démarrer**, tapez **dcpromo**, puis appuyez sur ENTRÉE.

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. Sur la page d'accueil, cliquez sur **Suivant**.

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. Sur la page Compatibilité du système d'exploitation, cliquez sur **Suivant**.

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. Sur la page Choisissez une configuration de déploiement, cliquez sur **Forêt existante**, sur **Ajouter un contrôleur de domaine à un domaine existant**, puis sur **Suivant**.

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. Sur la page Informations d'identification réseau, vérifiez que vous installez bien le contrôleur de domaine dans le domaine **corp.contoso.com**, puis entrez les informations d'identification d'un membre du groupe Admins du domaine (ou utilisez les informations d'identification corp\administrator). 

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. Sur la page Sélectionner un domaine, cliquez sur **Suivant**. 

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. Sur la page Sélectionner un site, vérifiez que CloudSite est sélectionné, puis cliquez sur **Suivant**.

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. Sur la page Options supplémentaires pour le contrôleur de domaine, cliquez sur **Suivant**. 

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. Sur le message d'avertissement Attribution IP statique, cliquez sur **Oui, l'ordinateur utilisera une adresse IP attribuée automatiquement par un serveur DHCP (non recommandé).**

	**Important** 

	Même si l'adresse IP du réseau virtuel Azure est dynamique, elle reste active pendant toute la durée d'utilisation de la machine virtuelle. Vous n'avez donc pas besoin de configurer une adresse IP statique sur le contrôleur de domaine que vous installez sur le réseau virtuel. La configuration d'une adresse IP pour la machine virtuelle entraîne des défaillances de communication.


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. ﻿À l'invite du message d'avertissement de délégation DNS, cliquez sur **Oui**.

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. Sur la page Location for Active Directory database, log files and SYSVOL, cliquez sur Parcourir, puis entrez ou sélectionnez un emplacement sur le disque dur pour les fichiers Active Directory et cliquez sur **Suivant**. 

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. Sur la page Administrateur de restauration des services d'annuaire, entrez et validez le mot de passe DSRM, puis cliquez sur **Suivant**.

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. Sur la page Résumé, cliquez sur **Suivant**.

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. Une fois l'Assistant Installation d'Active Directory terminé, cliquez sur **Terminer**, puis sur **Redémarrer maintenant** pour terminer l'installation. 

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>Étape 5 : validation de l'installation</h2>

1. Reconnectez-vous à la machine virtuelle.

2. Cliquez sur **Démarrer**, cliquez avec le bouton droit sur **Invite de commandes**, puis cliquez sur **Exécuter en tant qu'administrateur**. 

3. Tapez la commande suivante, puis appuyez sur ENTRÉE :  'Dcdiag /c /v'

4. Vérifiez que les tests fonctionnent correctement. 

Une fois le contrôleur de domaine configuré, exécutez la cmdlet Windows PowerShell suivante pour configurer des machines virtuelles supplémentaires et les ajouter automatiquement au domaine une fois leur configuration terminée. Vous devez configurer les paramètres de résolution de client DNS des machines virtuelles durant la configuration de ces dernières. Indiquez les noms de votre domaine, de votre machine virtuelle, etc. 

Pour plus d'informations sur l'utilisation de Windows PowerShell, consultez les pages [Mise en route d'Azure PowerShell](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156055.aspx) et [Cmdlets de gestion Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841).


<h2><a id="provisionvm"></a>Étape 6 : configuration d'une machine virtuelle jointe au domaine au démarrage</h2>

1. Pour créer une machine virtuelle supplémentaire jointe au domaine lors de son premier démarrage, ouvrez Azure PowerShell ISE, collez le script suivant, remplacez les espaces réservés par vos propres valeurs, puis exécutez-le. 

	Pour déterminer l'adresse IP interne du contrôleur de domaine, cliquez sur le nom du réseau virtuel où il est exécuté. 

	Dans l'exemple suivant, l'adresse IP interne du contrôleur de domaine est 10.4.3.1. Add-AzureProvisioningConfig possède aussi un paramètre -MachineObjectOU qui, s'il est spécifié (ce qui requiert un nom unique dans Active Directory), permet de configurer les paramètres de stratégie de groupe sur toutes les machines virtuelles dans ce conteneur.

	Une fois les machines virtuelles configurées, connectez-vous en indiquant un compte de domaine au format Nom d'utilisateur principal (UPN), tel que administrateur@corp.contoso.com. 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-fr-fr-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>Étape 7 : sauvegarde du contrôleur de domaine</h2>


1. Connectez-vous à YourVMachine.

2. Cliquez sur **Démarrer**, sur **Gestionnaire de serveur**, sur **Ajouter des fonctionnalités**, puis sélectionnez **Fonctionnalités de la Sauvegarde de Windows Server**. Suivez les instructions pour installer Sauvegarde Windows Server.

3. Cliquez sur **Démarrer**, sur **Sauvegarde de Windows Server**, puis sur **Sauvegarde unique**.
 
4. Cliquez sur **Autres options**, puis sur **Suivant**.

5. Cliquez sur **Serveur complet**, puis sur **Suivant**.

6. Cliquez sur **Lecteurs locaux**, puis sur **Suivant**.

7. Sélectionnez le disque de destination (qui ne doit pas héberger les fichiers du système d'exploitation ou la base de données Active Directory), puis cliquez sur Suivant.

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. Validez vos paramètres de sauvegarde, puis cliquez sur **Sauvegarder**.

<h2><a id="test"></a>Étape 8 : test de l'authentification et de l'autorisation</h2>

1. Pour tester l'authentification et l'autorisation, créez un compte d'utilisateur de domaine dans Active Directory. 
Connectez-vous à la machine virtuelle cliente dans chaque site, puis créez un dossier partagé pour cette machine.

2. Testez l'accès au dossier partagé en utilisant différents comptes, groupes et autorisations. 

## Voir aussi

-  [Réseau virtuel Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156007.aspx)

-  [Windows Azure IT Pro IaaS : (01) Concepts de base des machines virtuelles](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT Pro IaaS : (05) Création d'un réseau virtuel pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156055.aspx)

-  [Cmdlets de gestion Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841)
