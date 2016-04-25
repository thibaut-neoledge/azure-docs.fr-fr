<properties 
	pageTitle="Simulation d'environnement de test de cloud hybride | Microsoft Azure" 
	description="Créez une simulation d'environnement de cloud hybride pour exécuter des tests informatiques ou de développement, à l'aide de deux réseaux virtuels Azure et d'une connexion de réseau virtuel à réseau virtuel." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/28/2016" 
	ms.author="josephd"/>

# Configuration d'une simulation d'environnement de cloud hybride à des fins de test (mode de déploiement classique)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](../virtual-machines/virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).

Cet article vous présente la création d'un environnement de cloud hybride simulé avec Microsoft Azure pour le test, à l'aide de deux réseaux virtuels Azure distincts. Utilisez cette configuration à la place de la [Configuration d’un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md) lorsque vous ne disposez pas d’une connexion Internet directe et d’une adresse IP publique. Voici la configuration obtenue.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Elle simule un environnement de production de cloud hybride. Elle comprend :

- Un réseau local simulé et simplifié hébergé dans un réseau virtuel Azure (le réseau virtuel TestLab).
- Un réseau virtuel entre sites simulé hébergé dans Azure (TestVNET).
- Une connexion de réseau virtuel à réseau virtuel entre les deux réseaux virtuels.
- Un contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Elle fournit une base et un point de départ commun pour :

- Développer et tester des applications dans une simulation d’environnement de cloud hybride.
- Créer des configurations de test des ordinateurs, certains dans le réseau virtuel TestLab et d’autres dans le réseau virtuel TestVNET, afin de simuler des charges de travail dans un cloud hybride.

La configuration de l’environnement de test de cloud hybride comprend quatre grandes étapes :

1.	Configurer le réseau virtuel TestLab.
2.	Créer le réseau virtuel entre sites.
3.	Créer la connexion VPN de réseau virtuel à réseau virtuel.
4.	Configurer DC2. 

Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour une version d’évaluation gratuite à partir de la page permettant d’[essayer Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN, consultez la page [Avantage Azure pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Les machines virtuelles et les passerelles de réseau virtuel dans Azure entraînent des frais lors de leur utilisation. Ce coût est facturé sur votre abonnement de version d’évaluation gratuite, votre abonnement MSDN ou votre abonnement payant. Afin de réduire les coûts d'exécution de cet environnement de test quand vous ne l'utilisez pas, consultez la section [Réduction des coûts récurrents de cet environnement](#costs) plus loin dans cet article.


## Phase 1 : configuration du réseau virtuel TestLab

Suivez les instructions de la section [Environnement de test de la configuration de base](../virtual-machines/virtual-machines-windows-classic-test-config-env.md) pour configurer les ordinateurs DC1, APP1 et CLIENT1 dans un réseau virtuel Azure nommé TestLab.

Dans le portail de gestion Azure sur votre ordinateur local, connectez-vous à DC1 avec les informations d’identification CORP\\User1. Pour configurer le domaine CORP afin que les utilisateurs et les ordinateurs utilisent leur contrôleur de domaine local pour l’authentification, exécutez les commandes suivantes à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Phase 2 : création du réseau virtuel TestVNET

Tout d’abord, créez un réseau virtuel appelé TestVNET.

1.	Dans la barre des tâches du portail de gestion Azure, cliquez sur **Nouveau > Services réseau > Réseau virtuel > Création personnalisée**.
2.	Sur la page Détails du réseau virtuel, tapez **TestVNET** dans le champ **Nom**.
3.	Dans **Emplacement**, sélectionnez l’emplacement souhaité.
4.	Cliquez sur la flèche Suivant.
5.	Dans la page Serveurs DNS et connectivité VPN, dans **Serveurs DNS**, tapez **DC1** dans **Sélectionner ou entrer le nom**, puis cliquez sur la flèche Suivant.
6.	Sur la page Espace d’adresses du réseau virtuel :
	- Dans **Espace d’adressage**, dans **Adresse IP de départ**, sélectionnez ou tapez **192.168.0.0**.
	- Dans **Sous-réseaux**, cliquez sur **Subnet-1** et remplacez le nom par **TestSubnet**. 
	- Dans la colonne **CIDR (nombre d’adresses)** pour TestSubnet, cliquez sur **/24 (256)**.
7.	Cliquez sur l’icône Terminer. Attendez que le réseau virtuel soit créé avant de continuer.

Ensuite, suivez les instructions de la page [Installation et configuration d’Azure PowerShell pour installer Azure PowerShell sur votre ordinateur local](../powershell-install-configure.md).

Ensuite, créez un service cloud pour le réseau virtuel TestVNET. Vous devez choisir un nom unique. Par exemple, vous pouvez le nommer **TestVNET-***UniqueSequence*, où *UniqueSequence* est l’abréviation de votre organisation. Par exemple, si le nom de votre organisation est Tailspin Toys, vous pouvez nommer le service cloud **TestVNET-Tailspin**.

Vous pouvez vérifier si le nom est unique avec cette commande Azure PowerShell sur votre ordinateur local.

	Test-AzureName -Service <Proposed cloud service name>

Si cette commande renvoie « False », le nom proposé est unique. Créez le service cloud avec cette commande.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Phase 3 : création de la connexion de réseau virtuel à réseau virtuel

Tout d’abord, vous créez des réseaux locaux qui représentent l’espace d’adressage de chaque réseau virtuel.

1.	Dans le portail de gestion Azure sur votre ordinateur local, cliquez sur **Nouveau > Services de réseau > Réseau virtuel > Ajouter un réseau local**.
2.	Dans la page Spécifier les détails de votre réseau local, tapez **TestLabLNet** dans **Nom**, tapez **131.107.0.1** dans **Adresse IP de l'appareil VPN**, puis cliquez sur la flèche droite.
3.	Dans la page Spécifier l'espace d'adressage, dans **Adresse IP de départ**, tapez **10.0.0.0**.
4.	Dans **CIDR (Nombre d’adresses)**, sélectionnez **/24 (256)**, puis cliquez sur la coche.
5.	Cliquez sur **Nouveau > Services réseau > Réseau virtuel > Ajouter un réseau local**.
6.	Dans la page Spécifier les détails de votre réseau local, tapez **TestVNETLNet** dans **Nom**, tapez **131.107.0.2** dans **Adresse IP de l’appareil VPN**, puis cliquez sur la flèche droite.
7.	Dans la page Spécifier l’espace d’adressage, dans **Adresse IP de départ**, tapez **192.168.0.0**.
8.	Dans **CIDR (Nombre d’adresses)**, sélectionnez **/24 (256)**, puis cliquez sur la coche.

Notez que les adresses IP du périphérique VPN 131.107.0.1 et 131.107.0.2 sont des valeurs temporaires, jusqu’à ce que vous configuriez des passerelles pour les deux réseaux virtuels.

Ensuite, vous configurez chaque réseau virtuel pour qu’il utilise une connexion VPN de site à site et le réseau local correspondant à l’autre réseau virtuel.

1.	Dans le portail de gestion Azure sur votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, puis vérifiez que la colonne **Statut** de **TestLab** contient bien **Créé**.
2.	Cliquez sur **TestLab**, puis sur **Configurer**. Dans la page TestLab, dans la section **Connectivité de site à site**, cliquez sur **Se connecter au réseau local**. 
3.	Dans **Réseau local**, sélectionnez **TestVNETLNet**.
4.	Cliquez sur **Enregistrer** dans la barre des tâches. Dans certains cas, vous devrez peut-être cliquer sur **Ajouter un sous-réseau de passerelle** pour créer un sous-réseau destiné à la passerelle VPN Azure.
5.	Cliquez sur **Réseaux** dans le volet gauche, puis vérifiez que la colonne **Statut** de TestVNET contient bien **Créé**.
6.	Cliquez sur **TestVNET**, puis sur **Configurer**. Dans la page TestVNET, dans la section **Connectivité de site à site**, cliquez sur **Se connecter au réseau local**. 
7.	Dans **Réseau local**, sélectionnez **TestLabNet**.
8.	Cliquez sur **Enregistrer** dans la barre des tâches. Dans certains cas, vous devrez peut-être cliquer sur **Ajouter un sous-réseau de passerelle** pour créer un sous-réseau destiné à la passerelle VPN Azure.

Vous créez ensuite des passerelles de réseau virtuel pour les deux réseaux virtuels.

1.	Dans le portail de gestion Azure, dans la page **Réseaux**, cliquez sur **TestLab**. Sur la page Tableau de bord, vous devez voir l’état **La passerelle n’a pas été créée**.
2.	Dans la barre des tâches, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit établie et qu’elle prenne l’état **Connexion en cours**. Cette opération peut prendre quelques minutes.
3.	Sur la page Tableau de bord, notez la valeur du champ **Adresse IP de la passerelle**. Il s’agit de l’adresse IP publique de la passerelle VPN Azure pour le réseau virtuel TestLab. Enregistrez cette adresse IP, vous en aurez besoin pour configurer la connexion de réseau virtuel à réseau virtuel.
4.	Dans la barre des tâches, cliquez sur **Gérer la clé**, puis sur l’icône de copie à côté de la clé pour la copier dans le Presse-papiers. Collez cette clé dans un document que vous enregistrez. Vous avez besoin de la valeur de cette clé pour configurer la connexion de réseau virtuel à réseau virtuel.
5.	Dans la page Réseaux, cliquez sur **TestVNET**. Sur la page Tableau de bord, vous devez voir l’état **La passerelle n’a pas été créée**.
6.	Dans la barre des tâches, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit établie et qu’elle prenne l’état **Connexion en cours**. Cette opération peut prendre quelques minutes.
7.	Sur la page Tableau de bord, notez la valeur du champ **Adresse IP de la passerelle**. Il s’agit de l’adresse IP publique de la passerelle VPN Azure pour le réseau virtuel TestVNET. Enregistrez cette adresse IP, vous en aurez besoin pour configurer la connexion de réseau virtuel à réseau virtuel.

Ensuite, configurez les réseaux locaux TestLabLNet TestVNETLNet avec les adresses IP publiques obtenues lors de la création des passerelles de réseau virtuel.

1.	Dans le portail de gestion Azure, dans la page Réseaux, cliquez sur **Réseaux locaux**. 
2.	Cliquez sur **TestLabLNet**, puis sur **Modifier** dans la barre des tâches.
3.	Dans la page Spécifier les détails de votre réseau local, tapez l’adresse IP de la passerelle du réseau virtuel TestLab (étape 3 de la procédure précédente) dans **Adresse IP de l’appareil VPN (Facultatif)**, puis cliquez sur la flèche droite.
4.	Dans la page Spécifier l’espace d’adresses, cliquez sur la coche.
5.	Dans la page Réseaux locaux, cliquez sur **TestVNETLNet**, puis sur **Modifier** dans la barre des tâches.
6.	Dans la page Spécifier les détails de votre réseau local, tapez l’adresse IP de la passerelle du réseau virtuel TestVNET (étape 7 de la procédure précédente) dans **Adresse IP de l’appareil VPN (Facultatif)**, puis cliquez sur la flèche droite.
7.	Dans la page Spécifier l’espace d’adresses, cliquez sur la coche.

Ensuite, vous allez configurer la clé prépartagée pour que les deux passerelles utilisent la même valeur, qui est la valeur de clé déterminée par le portail de gestion Azure pour le réseau virtuel TestLab. Exécutez ces commandes à partir d’une invite de commandes PowerShell Azure sur votre ordinateur local, en utilisant la valeur de la clé prépartagée de TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

Ensuite, dans la page Réseau du portail de gestion Azure sur votre ordinateur local, cliquez sur le réseau virtuel **TestLab**, cliquez sur **Tableau de bord**, puis cliquez sur **Connexion** dans la barre des tâches. Attendez que le réseau virtuel TestLab soit connecté.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Phase 4 : configurer DC2

Créez d’abord une machine virtuelle Azure pour DC2. Exécutez ces commandes à l’invite de commandes Azure PowerShell sur votre ordinateur local.

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Ensuite, connectez-vous à la machine virtuelle DC2.

1.	Dans le volet gauche du Portail de gestion Azure, cliquez sur **Machines virtuelles**, puis cliquez sur **En cours d’exécution** dans la colonne **État** pour DC2.
2.	Dans la barre des tâches, cliquez sur **Se connecter**. 
3.	Lorsque vous êtes invité à ouvrir DC2.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	Si des informations d’identification vous sont demandées, utilisez celles-ci :
- Nom : **DC2\**[Nom de compte d’administrateur local]
- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d’une invite de commandes Windows PowerShell de niveau administrateur sur DC2, exécutez ces commandes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 10.0.0.4. Ceci est un test de trafic sur la connexion de réseau virtuel à réseau virtuel.

Ensuite, ajoutez le disque de données supplémentaire comme nouveau volume avec la lettre de lecteur F:.

1.	Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de fichiers et de stockage**, puis sur **Disques**.
2.	Dans le volet Contenu, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3.	Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4.	Dans la page Avant de commencer de l’Assistant Nouveau volume, cliquez sur **Suivant**.
5.	Dans la page Sélectionner le serveur et le disque, cliquez sur **Disque 2**, puis sur **Suivant**. À l’invite, cliquez sur **OK**.
6.	Dans la page Spécifier la taille du volume, cliquez sur **Suivant**.
7.	À la page Affecter à la lettre d'un lecteur ou à un dossier, cliquez sur **Suivant**.
8.	À la page Sélectionner les paramètres du système de fichiers, cliquez sur **Suivant**.
9.	À la page Confirmer les sélections, cliquez sur **Créer**.
10.	Lorsque vous avez terminé, cliquez sur **Fermer**.

Ensuite, configurez DC2 comme contrôleur de domaine réplica pour le domaine corp.contoso.com. Exécutez ces commandes dans l’invite de commandes Windows PowerShell sur DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Notez que vous êtes invité à fournir le mot de passe CORP\\User1 et un mot de passe du Mode restauration des Services annuaire (DSRM), puis à redémarrer DC2.

Maintenant que le réseau virtuel TestVNET possède son propre serveur DNS (DC2), vous devez configurer le réseau virtuel TestVNET pour utiliser ce serveur DNS.

1.	Dans le volet gauche du Portail de gestion Azure, cliquez sur **Réseaux**, puis sur **TestVNET**.
2.	Cliquez sur **Configurer**.
3.	Dans **Serveurs DNS**, supprimez l’entrée 10.0.0.4.
4.	Dans **Serveurs DNS**, ajoutez une entrée avec le nom **DC2** et l’adresse IP **192.168.0.4**. 
5.	Dans la barre de commandes en bas, cliquez sur **Enregistrer**, puis sur **Oui** quand vous y êtes invité. Attendez que la mise à jour sur le réseau TestVNet soit terminée.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
Votre environnement de cloud hybride simulé est maintenant prêt pour les tests.

## Étapes suivantes

- Configurez une [batterie de serveurs intranet SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), une [application métier web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) ou un [serveur de synchronisation de l'annuaire Office 365 (DirSync)](virtual-networks-setup-dirsync-hybrid-cloud-testing.md) sur le réseau virtuel TestVNET.


## <a id="costs"></a>Réduction des coûts récurrents de cet environnement

Afin de réduire les coûts d’exécution des machines virtuelles dans cet environnement, effectuez les tests et démonstrations nécessaires aussi rapidement que possible et supprimez ou arrêtez les machines virtuelles lorsque vous ne les utilisez pas. Par exemple, vous pouvez utiliser Azure Automation et un runbook pour arrêter automatiquement les machines virtuelles dans les réseaux virtuels TestLab et Test\_VNET à la fin de chaque journée de travail. Lorsque vous redémarrez les machines virtuelles sur le sous-réseau du réseau d’entreprise, commencez par DC1.

La passerelle VPN Azure est implémentée comme un ensemble de deux machines virtuelles qui entraînent des frais récurrents. Pour plus d’informations, voir la page [Virtual Network - Tarification](https://azure.microsoft.com/pricing/details/virtual-network/). Pour réduire les coûts des deux passerelles VPN (une pour TestLab et l’autre pour TestVNET), créez l’environnement de test et exécutez vos tests et démonstrations aussi rapidement que possible ou supprimez les passerelles en suivant ces étapes.
 
1.	Dans le portail de gestion Azure sur votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, cliquez sur **TestLab**, puis sur **Tableau de bord**.
2.	Dans la barre des tâches, cliquez sur **Supprimer une passerelle**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit supprimée et qu’elle prenne l’état **La passerelle n’a pas été créée**.
3.	Cliquez sur **Réseaux** dans le volet gauche, cliquez sur **TestVNET**, puis sur **Tableau de bord**.
4.	Dans la barre des tâches, cliquez sur **Supprimer une passerelle**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit supprimée et qu’elle prenne l’état **La passerelle n’a pas été créée**.

Si vous supprimez les passerelles et que vous souhaitez restaurer cet environnement de test, vous devez d’abord créer de nouvelles passerelles.

1.	Dans le portail de gestion Azure sur votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, puis cliquez sur **TestLab**. Sur la page Tableau de bord, vous devez voir l’état **La passerelle n’a pas été créée**.
2.	Dans la barre des tâches, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit établie et qu’elle prenne l’état **Connexion en cours**. Cette opération peut prendre quelques minutes.
3.	Sur la page Tableau de bord, notez la valeur du champ **Adresse IP de la passerelle**. Il s’agit de la nouvelle adresse IP publique de la passerelle VPN Azure pour le réseau virtuel TestLab. Vous avez besoin de cette adresse IP pour reconfigurer le réseau local TestLabLNet.
4.	Dans la barre des tâches, cliquez sur **Gérer la clé**, puis cliquez sur l’icône de copie en regard de la clé pour la copier dans le Presse-papiers. Collez cette valeur de clé dans un document et enregistrez ce dernier. Vous avez besoin de cette valeur pour reconfigurer la passerelle VPN du réseau virtuel TestVNET.
5.	À partir du Portail de gestion Azure sur votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, puis cliquez sur **TestVNET**. Sur la page Tableau de bord, vous devez voir l’état **La passerelle n’a pas été créée**.
6.	Dans la barre des tâches, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit établie et que son état passe à Connexion en cours. Cette opération peut prendre quelques minutes.
7.	Sur la page Tableau de bord, notez la valeur du champ **Adresse IP de la passerelle**. Il s’agit de la nouvelle adresse IP publique de la passerelle VPN Azure pour le réseau virtuel TestVNET. Vous avez besoin de cette adresse IP pour reconfigurer le réseau local TestVNETLNet.

Ensuite, vous configurez les réseaux locaux TestLabLNet et TestVNETLNet avec les nouvelles adresses IP publiques obtenues lors de la création des passerelles de réseau virtuel.

1.	Dans le portail de gestion Azure, dans la page Réseaux, cliquez sur **Réseaux locaux**. 
2.	Cliquez sur **TestLabLNet**, puis sur **Modifier** dans la barre des tâches.
3.	Dans la page Spécifier les détails de votre réseau local, tapez l’adresse IP de la passerelle du réseau virtuel TestLab (étape 3 de la procédure précédente) dans **Adresse IP de l’appareil VPN (Facultatif)**, puis cliquez sur la flèche droite.
4.	Dans la page Spécifier l’espace d’adresses, cliquez sur la coche.
5.	Dans la page Réseaux locaux, cliquez sur **TestVNETLNet**, puis sur **Modifier** dans la barre des tâches.
6.	Dans la page Spécifier les détails de votre réseau local, tapez l’adresse IP de la passerelle du réseau virtuel TestVNET (étape 7 de la procédure précédente) dans **Adresse IP de l’appareil VPN (Facultatif)**, puis cliquez sur la flèche droite.
7.	Dans la page Spécifier l’espace d’adresses, cliquez sur la coche.

Ensuite, vous configurez la clé prépartagée pour que les deux passerelles utilisent la même valeur, qui est la valeur de clé déterminée par le portail de gestion Azure pour le réseau virtuel TestLab. Exécutez ces commandes à partir d’une invite de commandes PowerShell Azure sur votre ordinateur local, en utilisant la valeur de la clé prépartagée de TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

Ensuite, dans la page Réseau du portail de gestion Azure, cliquez sur le réseau virtuel **TestLab**, puis sur **Connexion** dans la barre des tâches. Attendez que le réseau virtuel TestLab soit connecté au réseau local TestVNET.

<!---HONumber=AcomDC_0413_2016-->