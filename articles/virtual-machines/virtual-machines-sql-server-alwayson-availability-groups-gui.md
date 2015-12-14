<properties
	pageTitle="Configuration de groupes de disponibilité AlwaysOn (GUI) | Microsoft Azure"
	description="Création d'un groupe de disponibilité AlwaysOn avec les machines virtuelles Azure. Ce didacticiel utilise l'interface utilisateur et des outils, plutôt que des scripts."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/30/2015"
	ms.author="jroth" />

# Configuration de groupes de disponibilité AlwaysOn dans Azure VM (GUI)

> [AZURE.SELECTOR]
- [Azure classic portal](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Ce didacticiel de bout en bout vous montre comment implémenter un groupe de disponibilité en utilisant SQL Server AlwaysOn sur des machines virtuelles Azure.

>[AZURE.NOTE]Dans le portail de gestion Azure, il existe une nouvelle installation de la galerie pour les groupes de disponibilité AlwaysOn avec un écouteur. Cela configure automatiquement tous les éléments nécessaires pour les groupes de disponibilité AlwaysOn. Pour plus d’informations, consultez [Offre AlwaysOn SQL Server dans la galerie du portail Microsoft Azure Classic](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Pour utiliser PowerShell, consultez le didacticiel du même scénario : [Configurer les groupes de disponibilité AlwaysOn dans Azure avec PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md).

À la fin du didacticiel, votre solution SQL Server AlwaysOn dans Azure comprendra les éléments suivants :

- un réseau virtuel contenant plusieurs sous-réseaux, notamment un sous-réseau frontal et un sous-réseau principal ;

- un contrôleur de domaine avec un domaine Active Directory (AD) ;

- deux machines virtuelles SQL Server déployées dans le sous-réseau principal et jointes au domaine AD ;

- un cluster WSFC à 3 nœuds avec le modèle de quorum Nœud majoritaire ;

- un groupe de disponibilité avec deux réplicas avec validation synchrone d'une base de données de disponibilité.

La figure suivante est une représentation graphique de la solution.

![Architecture du laboratoire de test pour les groupes de disponibilité dans Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

Notez qu'il s'agit d'une configuration possible. Par exemple, vous pouvez réduire le nombre de machines virtuelles pour un groupe de disponibilité de deux réplicas, afin de faire des économies sur les heures de calcul dans Azure, en utilisant le contrôleur de domaine comme témoin de partage de fichiers du quorum dans un cluster WSFC à 2 nœuds. Cette méthode permet de se passer d'une machine virtuelle par rapport à la configuration ci-dessus.

Ce didacticiel part des principes suivants :

- vous disposez déjà d’un compte Azure ;

- vous savez déjà comment configurer une machine virtuelle SQL Server dans la galerie de machines virtuelles avec l'interface graphique utilisateur. Pour plus d'informations, consultez [Configuration d'une machine virtuelle SQL Server dans Azure](virtual-machines-provision-sql-server.md)

- Vous avez déjà une connaissance approfondie des groupes de disponibilité AlwaysOn. Pour plus d'informations, consultez [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE]Si l'utilisation des groupes de disponibilité AlwaysOn avec SharePoint vous intéresse, consultez [Configurer des groupes de disponibilité AlwaysOn SQL Server 2012 pour SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Création du réseau virtuel et du serveur de contrôleur de domaine

Vous commencez avec un nouveau compte d'essai Azure. Une fois que vous avez terminé la configuration de votre compte, vous devez vous trouver dans l’écran d’accueil du portail Azure Classic.

1. Cliquez sur le bouton **Nouveau** dans l'angle inférieur gauche de la page, comme illustré ci-dessous.

	![Cliquez sur Nouveau dans le portail](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. Cliquez sur **Services réseau**, puis cliquez sur **Réseau virtuel,** puis sur **Création personnalisée**, comme illustré ci-dessous.

	![Création d’un réseau virtuel](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. Dans la boîte de dialogue **CRÉER UN RÉSEAU VIRTUEL**, créez un réseau virtuel en parcourant les pages avec les paramètres ci-dessous.

	|Page|Paramètres|
|---|---|
|Détails du réseau virtuel|**NAME = ContosoNET**<br/>**REGION = West US**|
|Serveurs DNS et connectivité VPN|Aucun|
|Espaces d’adressage du réseau virtuel|Ces paramètres sont illustrés dans la capture d'écran ci-dessous : ![Création d’un réseau virtuel](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. Créez ensuite la machine virtuelle que vous allez utiliser comme contrôleur de domaine (DC). Cliquez sur **Nouveau**, puis sur **Compute**, puis sur **Machine virtuelle**, puis sur **De la galerie**, comme illustré ci-dessous.

	![Créer une machine virtuelle](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. Dans la boîte de dialogue **CRÉER UNE MACHINE VIRTUELLE**, configurez une nouvelle machine virtuelle en parcourant les pages avec les paramètres ci-dessous.

	|Page|Paramètres|
|---|---|
|Sélection du système d’exploitation de la machine virtuelle|Windows Server 2012 R2 Datacenter|
|Configuration de la machine virtuelle|**VERSION RELEASE DATE** = (dernière version)<br/>**VIRTUAL MACHINE NAME** = ContosoDC<br/>**TIER** = STANDARD<br/>**SIZE** = A2 (2 cœurs)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|
|Configuration de la machine virtuelle|**CLOUD SERVICE** = Créer un service cloud<br/>**CLOUD SERVICE DNS NAME** = Un nom de service cloud unique<br/>**DNS NAME** = Un nom unique (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Utiliser un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = (None)|
|Options de la machine virtuelle|Utilisation des valeurs par défaut|

Une fois que vous avez configuré la nouvelle machine virtuelle, attendez que l'ordinateur virtuel soit provisionné. Ce processus prend du temps, et si vous cliquez sur l’onglet **Virtual Machine** dans le portail Azure Classic, vous pouvez voir le cycle des états ContosoDC allant de **Départ (approvisionnement)** à **Arrêté**, **Départ**, **En cours d’exécution (approvisionnement)** et enfin **En cours d’exécution**.

Le serveur du contrôleur de domaine est maintenant correctement configuré. Ensuite, vous allez configurer le domaine Active Directory sur ce serveur de contrôleur de domaine.

## Configuration du contrôleur de domaine

Dans les étapes suivantes, vous allez configurer la machine ContosoDC comme contrôleur de domaine pour corp.contoso.com.

1. Dans le portail, sélectionnez la machine **ContosoDC**. Sur l'onglet **Tableau de bord** cliquez sur **Connexion** pour ouvrir un fichier RDP pour l'accès Bureau à distance.

	![Se connecter à la machine virtuelle](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. Connectez-vous avec votre compte administrateur configuré (**\\AzureAdmin**) et votre mot de passe (**Contoso!000**).

1. Par défaut, le tableau de bord **Gestionnaire de serveur** doit être affiché.

1. Cliquez sur le lien du tableau de bord **Ajouter des rôles et fonctionnalités**.

	![Ajout de rôles Explorateur de serveurs](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. Sélectionnez **Suivant** jusqu'à ce que vous atteigniez la section **Rôles des serveurs**.

1. Sélectionnez les rôles **Services de domaine Active Directory** et **serveur DNS**. Lorsque vous y êtes invité, ajoutez les fonctionnalités supplémentaires requises par ces rôles.

	>[AZURE.NOTE]Vous obtiendrez un avertissement de validation vous informant qu'il n’y a aucune adresse IP statique. Si vous testez la configuration, cliquez sur Continuer. Pour les scénarios de production, [utilisez PowerShell pour définir l'adresse IP de l'ordinateur de contrôleur de domaine](./virtual-network/virtual-networks-reserved-private-ip.md).

	![Boîte de dialogue Ajouter des rôles](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. Cliquez sur **Suivant** jusqu'à ce que vous atteigniez la section **Confirmation**. Cochez la case **Redémarrer automatiquement le serveur de destination, si nécessaire**.

1. Cliquez sur **Installer**.

1. Une fois les fonctionnalités installées, retournez dans le tableau de bord **Gestionnaire de serveur**.

1. Sélectionnez la nouvelle option **AD DS** dans le volet gauche.

1. Cliquez sur le lien **Plus** dans la barre d'avertissement jaune.

	![Boîte de dialogue AD DS sur une machine virtuelle du serveur DNS](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. Dans la colonne **Action** de la boîte de dialogue **Tous les détails de la tâche serveur**, cliquez sur **Promouvoir ce serveur en contrôleur de domaine**.

1. Dans l'**Assistant de configuration des services de domaine Active Directory**, utilisez les valeurs suivantes :

	|Page|Paramètre|
|---|---|
|Configuration du déploiement|**Ajouter une nouvelle forêt** = sélectionné<br/>**Nom de domaine racine** = corp.contoso.com|
|Options de contrôleur de domaine :|**Mot de passe** = Contoso!000<br/>**Confirmer le mot de passe**= Contoso!000|

1. Cliquez sur **Suivant** pour parcourir les autres pages de l'Assistant. Sur la page **Vérification de la configuration requise**, vérifiez que vous voyez le message suivant : **Toutes les vérifications de la configuration requise ont donné satisfaction**. Notez que vous devez examiner les messages d'avertissement applicables, mais il est possible de poursuivre l'installation.

1. Cliquez sur **Installer**. La machine virtuelle **ContosoDC** redémarre automatiquement.

## Configuration des comptes de domaine

Les étapes suivantes configurent les comptes Active Directory (AD) pour une utilisation ultérieure.

1. Reconnectez-vous à la machine **ContosoDC**.

1. Dans **Gestionnaire de serveur**, sélectionnez **Outils**, puis cliquez sur **Centre d'administration Active Directory**.

	![Centre d'administration Active Directory](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. Dans le **Centre d'administration Active Directory**, sélectionnez **corp (local)** dans le volet gauche.

1. Dans le volet droit **Tâches**, sélectionnez **Nouveau** puis cliquez sur **Utilisateur**. Utilisez les paramètres suivants :

	|Paramètre|Valeur|
|---|---|
|**Prénom**|Installer|
|**SamAccountName de l'utilisateur**|Installer|
|**Mot de passe**|Contoso!000|
|**Confirmer le mot de passe**|Contoso!000|
|**Autres options de mot de passe**|Sélectionné|
|**Le mot de passe n'expire jamais**|Activé|

1. Cliquez sur **OK** pour créer l'utilisateur d'**Installation**. Ce compte sera utilisé pour configurer le cluster de basculement et le groupe de disponibilité.

1. Créez deux utilisateurs supplémentaires avec les mêmes étapes : **CORP\\SQLSvc1** et **CORP\\SQLSvc2**. Ces comptes seront utilisés pour les instances de SQL Server. Ensuite, vous devez donner à **CORP\\Install** les autorisations nécessaires pour la configuration des clusters de basculement Windows Service (WSFC).

1. Dans le **Centre d'administration Active Directory**, sélectionnez **corp (local)** dans le volet gauche. Ensuite, dans le volet droit **Tâches**, cliquez sur **Propriétés**.

	![Propriétés de l'utilisateur CORP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. Sélectionnez **Extensions**, puis cliquez sur le bouton **Avancé** de l'onglet **Sécurité**.

1. Dans la fenêtre **Paramètres de sécurité avancés pour corp**. Cliquez sur **Ajouter**.

1. Cliquez sur **Sélectionner un principal**. Recherchez **CORP\\Install**. Cliquez sur **OK**.

1. Sélectionnez les autorisations **Lire toutes les propriétés** et **Créer des objets ordinateur**.

	![Autorisations de l’utilisateur Corp](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. Cliquez sur **OK**, puis sur **OK** à nouveau. Fermez la fenêtre des propriétés corp.

Maintenant que vous avez fini de configurer Active Directory et les objets utilisateur, vous allez créer trois machines virtuelles SQL Server et les joindre à ce domaine.

## Création des machines virtuelles SQL Server

Créez ensuite trois machines virtuelles, dont un nœud de cluster WSFC et deux machines virtuelles SQL Server. Pour créer les machines virtuelles, revenez au portail Azure Classic, cliquez sur **Nouveau**, **Compute**, **Machine virtuelle**, puis **De la galerie**. Utilisez ensuite les modèles dans le tableau suivant pour vous aider à créer les machines virtuelles.

|Page|MV1|MV2|MV3|
|---|---|---|---|
|Sélection du système d’exploitation de la machine virtuelle|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuration de la machine virtuelle|**VERSION RELEASE DATE** = (dernière version)<br/>**VIRTUAL MACHINE NAME** = ContosoWSFCNode<br/>**TIER** = STANDARD<br/>**SIZE** = A2 (2 cœurs)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|**VERSION RELEASE DATE** = (dernière version)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL1<br/>**TIER** = STANDARD<br/>**SIZE** = A3 (4 cœurs)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|**VERSION RELEASE DATE** = (dernière version)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL2<br/>**TIER** = BASIC<br/>**SIZE** = A3 (4 cœurs)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|
|Configuration de la machine virtuelle|**CLOUD SERVICE** = Nom DNS de service cloud unique créé auparavant (ex : ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Utiliser un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = Créer un groupe à haute disponibilité<br/>**AVAILABILITY SET NAME** = SQLHADR|**CLOUD SERVICE** = Nom DNS de service cloud unique créé auparavant (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Utiliser un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = SQLHADR (Vous pouvez également configurer le groupe à haute disponibilité après la création de la machine. Les trois machines doivent être assignées au groupe à haute disponibilité SQLHADR.)|**CLOUD SERVICE** = Nom DNS de service cloud unique créé auparavant (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Utiliser un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = SQLHADR (Vous pouvez également configurer le groupe à haute disponibilité après la création de la machine. Les trois machines doivent être assignées au groupe à haute disponibilité SQLHADR.)|
|Options de la machine virtuelle|Utilisation des valeurs par défaut|Utilisation des valeurs par défaut|Utilisation des valeurs par défaut|

<br/>

>[AZURE.NOTE]La configuration précédente suggère des machines virtuelles de niveau STANDARD, car les machines de niveau DE BASE ne prennent pas en charge les points de terminaison à équilibrage de charge nécessaires pour créer ultérieurement un écouteur de groupe de disponibilité. De plus, les tailles de machines suggérées ici sont destinées au test des groupes de disponibilité dans les machines virtuelles Azure. Pour optimiser les performances des charges de travail de production, consultez les recommandations relatives à la taille des ordinateurs SQL Server et à la configuration dans [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-performance-best-practices.md).

Une fois les trois machines virtuelles entièrement configurées, vous devez les attacher au domaine **corp.contoso.com** et accorder à CORP\\Install des droits d’administration sur les machines. Pour ce faire, procédez comme suit pour chacune des trois machines virtuelles.

1. Tout d'abord, modifiez l'adresse de serveur DNS par défaut. Commencez par télécharger le fichier Bureau à distance (RDP) de chaque machine virtuelle dans votre répertoire local en sélectionnant la machine virtuelle dans la liste en cliquant sur le bouton **Connexion**. Pour sélectionner une machine virtuelle, cliquez partout sauf sur la première cellule de la ligne, comme illustré ci-dessous.

	![Téléchargement du fichier RDP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. Lancez le fichier RDP téléchargé et connectez-vous à la machine virtuelle à l’aide de votre compte d’administrateur configuré (**BUILTIN\\AzureAdmin**) et de votre mot de passe (**Contoso!000**).

1. Une fois connecté, vous devez voir le tableau de bord **Gestionnaire de serveur**. Dans le volet gauche, cliquez sur **Serveur local**.

1. Sélectionnez le lien **Adresse IPv4 attribuée par DHCP, Compatible IPv6**.

1. Dans la fenêtre **Connexions réseau**, sélectionnez l’icône du réseau.

	![Modification du serveur DNS préféré de la machine virtuelle](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. Dans la barre de commandes, cliquez sur **Modifier les paramètres de cette connexion** (en fonction de la taille de la fenêtre, vous devrez peut-être cliquer sur la double flèche droite pour afficher cette commande).

1. Cliquez sur **Internet Protocol Version 4 (TCP/IPv4)**, puis sur Propriétés.

1. Sélectionnez Utiliser l’adresse de serveur DNS suivante et spécifiez **10.10.2.4** dans **Serveur DNS préféré**.

1. L’adresse **10.10.2.4** est celle attribuée à une machine virtuelle dans le sous-réseau 10.10.2.0/24 dans un réseau virtuel Azure, et cette machine virtuelle est **ContosoDC**. Pour vérifier l’adresse IP de **ContosoDC**, exécutez **nslookup contosodc** à l’invite de commandes, comme indiqué ci-dessous.

	![Utilisation de NSLOOKUP pour trouver l'adresse IP du contrôleur de domaine](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. Cliquez sur **OK**, puis sur **Fermer** pour valider les modifications. Vous pouvez maintenant joindre la machine virtuelle à **corp.contoso.com**.

1. De retour dans la fenêtre **Serveur local**, cliquez sur le lien **WORKGROUP**.

1. Dans la section **Nom de l’ordinateur**, cliquez sur **Modifier**.

1. Cochez la case **Domaine** et tapez **corp.contoso.com** dans la zone de texte. Cliquez sur **OK**.

1. Dans la boîte de dialogue contextuelle **Sécurité Windows**, spécifiez les informations d’identification du compte d’administrateur de domaine par défaut (**CORP\\AzureAdmin**) et le mot de passe (**Contoso!000**).

1. Lorsque le message « Bienvenue dans le domaine corp.contoso.com » s’affiche, cliquez sur **OK**.

1. Cliquez sur **Fermer**, puis sur **Redémarrer maintenant** dans la boîte de dialogue contextuelle.

### Ajoutez l'utilisateur Corp\\Install en tant qu'administrateur sur chaque machine virtuelle :

1. Attendez le redémarrage de la machine virtuelle, puis relancez le fichier RDP pour vous connecter à la machine virtuelle à l’aide du compte **BUILTIN\\AzureAdmin**.

1. Dans **Gestionnaire de serveur** sélectionnez **Outils**, puis cliquez sur **Gestion de l’ordinateur**.

	![Gestion de l'ordinateur](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. Dans la fenêtre **Gestion de l’ordinateur**, développez **Utilisateurs et groupes locaux**, puis sélectionnez **Groupes**.

1. Double-cliquez sur le groupe **Administrateurs**.

1. Dans la boîte de dialogue **Propriétés de Administrateurs**, cliquez sur le bouton **Ajouter**.

1. Entrez l’utilisateur **CORP\\Install**, puis cliquez sur **OK**. À l’invite d’informations d’identification, utilisez le compte **AzureAdmin** avec le mot de passe **Contoso!000**.

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de Administrateurs**.

### Ajoutez la fonctionnalité de **Clustering de basculement** à chaque machine virtuelle.

1. Dans le tableau de bord **Gestionnaire de serveur**, cliquez sur **Ajouter des rôles et des fonctionnalités**.

1. Dans l’**Assistant Ajout de rôles et de fonctionnalités**, cliquez sur **Suivant** jusqu’à atteindre la page **Fonctionnalités**.

1. Sélectionnez **Clustering de basculement**. Lorsque vous y êtes invité, ajoutez toutes les autres fonctionnalités.

	![Ajout de la fonctionnalité de Clustering de basculement à la machine virtuelle](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. Cliquez sur **Suivant**, puis sur **Installer** dans la page **Confirmation**.

1. Lorsque l’installation de la fonctionnalité de **Clustering de basculement** est terminée, cliquez sur **Fermer**.

1. Déconnectez-vous de la machine virtuelle.

1. Répétez les étapes de cette section pour les trois serveurs : **ContosoWSFCNode**, **ContosoSQL1** et **ContosoSQL2**.

Les machines virtuelles SQL Server sont maintenant configurés et exécutées, mais elles sont installées avec les options par défaut de SQL Server.

## Création du cluster WSFC

Dans cette section, vous allez créer le cluster WSFC qui héberge le groupe de disponibilité que vous créerez ultérieurement. À ce stade, vous devriez avoir effectué les opérations suivantes pour chacune des trois machines virtuelles que vous allez utiliser dans le cluster WSFC :

- configuration complète dans Azure

- machine virtuelle jointe au domaine

- Ajout de **CORP\\Install** au groupe Administrateurs local

- Ajout de la fonctionnalité de Clustering de basculement à chaque machine virtuelle

Il s'agit des composants requis sur chaque machine virtuelle avant sa liaison au cluster WSFC.

Notez également que le réseau virtuel Azure ne se comporte pas de la même manière qu'un réseau local. Vous devez créer le cluster dans l'ordre suivant :

1. Création d’un cluster à nœud unique sur l’un des nœuds (**ContosoSQL1**).

1. Modification de l’adresse IP du cluster en une adresse IP inutilisée (**10.10.2.101**).

1. Mise en ligne du nom de cluster.

1. Ajout des autres nœuds (**ContosoSQL2** et **ContosoWSFCNode**).

Suivez les étapes ci-dessous pour accomplir les tâches de configuration complète du cluster.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en utilisant le compte de domaine **CORP\\Install**.

1. Dans le tableau de bord **Gestionnaire de serveur**, sélectionnez **Outils**, puis cliquez sur **Gestionnaire du cluster de basculement**.

1. Dans le volet gauche, cliquez sur **Gestionnaire du cluster de basculement**, puis sur **Créer un cluster**, comme illustré ci-dessous.

	![Créer un cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. Dans l'Assistant de création de cluster, créez un cluster à un nœud en complétant les pages avec les paramètres ci-dessous :

	|Page|Paramètres|
|---|---|
|Avant de commencer|Utilisation des valeurs par défaut|
|Sélection des serveurs|Tapez **ContosoSQL1** dans **Entrez le nom du serveur**, puis cliquez sur **Ajouter**.|
|Avertissement de validation|Cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster**.|
|Point d'accès pour l'administration du cluster|Tapez **Cluster1** dans **Nom de cluster**.|
|Confirmation|Utilisez les valeurs par défaut, sauf si vous utilisez des espaces de stockage. Consultez la remarque qui suit ce tableau.|

	>[AZURE.WARNING]Si vous utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739), qui regroupent plusieurs disques dans des pools de stockage, vous devez décocher la case **Ajouter la totalité du stockage disponible au cluster** dans la page **Confirmation**. Si vous ne décochez pas cette option, les disques virtuels sont détachés pendant le processus de clustering. Par conséquent, ils n’apparaissent pas non plus dans le Gestionnaire de disque ou dans l'explorateur, jusqu'à ce que les espaces de stockage soient supprimés du cluster et rattachés à l'aide de PowerShell.

1. Dans le volet gauche, développez **Gestionnaire du cluster de basculement**, puis cliquez sur **Cluster1.corp.contoso.com**.

1. Dans le volet central, faites défiler jusqu’à la section **Principales ressources de cluster** et développez les détails de **Nom : Cluster1**. Les ressources **Nom** et **Adresse IP** doivent toutes deux être à l’état **Échec**. La ressource d’adresse IP ne peut pas être mise en ligne, car le cluster a la même adresse IP que la machine elle-même, qui est une adresse dupliquée.

1. Cliquez avec le bouton droit sur la ressource **Adresse IP** en échec, puis cliquez sur **Propriétés**.

	![Propriétés du cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. Sélectionnez **Adresse IP statique** et spécifiez **10.10.2.101** dans la zone de texte Adresse. Cliquez ensuite sur **OK**.

1. Dans la section **Principales ressources de cluster**, cliquez avec le bouton droit sur **Nom : Cluster1**, puis cliquez sur **Mettre en ligne**. Attendez que les deux ressources soient en ligne. Lorsque la ressource du cluster apparaît en ligne, elle met à jour le serveur de contrôleur de domaine avec un nouveau compte d’ordinateur Active Directory (AD). Ce compte AD permet ultérieurement d’exécuter le service en cluster du groupe de disponibilité.

1. Enfin, ajoutez les nœuds restants au cluster. Dans l’arborescence du navigateur, cliquez avec le bouton droit sur **Cluster1.corp.contoso.com**, puis cliquez sur **Ajouter un nœud**, comme illustré ci-dessous.

	![Ajout d’un nœud au cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. Dans l’**Assistant Ajout de nœud**, cliquez sur **Suivant**. Dans la page **Sélectionner des serveurs**, ajoutez **ContosoSQL2** et **ContosoWSFCNode** à la liste en tapant le nom du serveur dans **Entrez le nom du serveur**, puis en cliquant sur **Ajouter**. Une fois ces opérations effectuées, cliquez sur **Suivant**.

1. Dans la page **Avertissement de validation**, cliquez sur **Non** (dans un scénario de production, vous devez effectuer les tests de validation). Cliquez ensuite sur **Suivant**.

1. Dans la page **Confirmation**, cliquez sur **Suivant** pour ajouter les nœuds.

	>[AZURE.WARNING]Si vous utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739), qui regroupent plusieurs disques dans des pools de stockage, vous devez décocher la case **Ajouter la totalité du stockage disponible au cluster**. Si vous ne décochez pas cette option, les disques virtuels sont détachés pendant le processus de clustering. Par conséquent, ils n’apparaissent pas non plus dans le Gestionnaire de disque ou dans l'explorateur, jusqu'à ce que les espaces de stockage soient supprimés du cluster et rattachés à l'aide de PowerShell.

1. Une fois les nœuds ajoutés au cluster, cliquez sur **Terminer**. Le Gestionnaire du cluster de basculement doit maintenant indiquer que votre cluster a trois nœuds, répertoriés dans le conteneur **Nœuds**.

1. Déconnectez-vous de la session Bureau à distance.

## Préparer les instances de SQL Server pour le groupe de disponibilité

Dans cette section, vous allez effectuer les opérations suivantes sur **ContosoSQL1** et **contosoSQL2** :

- Ajouter une connexion pour **NT AUTHORITY\\System** avec les autorisations nécessaires pour l’instance SQL Server par défaut

- Ajouer **CORP\\Install** en tant que rôle sysadmin à l’instance SQL Server par défaut

- Ouvrir le pare-feu pour l'accès à distance de SQL Server

- Activer la fonctionnalité de groupes de disponibilité AlwaysOn

- Modifier le compte de service SQL Server pour **CORP\\SQLSvc1** et **CORP\\SQLSvc2**, respectivement

Ces actions peuvent être effectuées dans n'importe quel ordre. Néanmoins, les étapes ci-dessous les traitent dans l'ordre. Suivez les étapes pour **ContosoSQL1** et **ContosoSQL2** :

1. Si vous ne vous êtes pas déconnecté de la session Bureau à distance pour la machine virtuelle, faites-le maintenant.

1. Lancez les fichiers RDP pour **ContosoSQL1** et **ContosoSQL2** et connectez-vous en tant que **BUILTIN\\AzureAdmin**.

1. Tout d’abord, ajoutez **NT AUTHORITY\\System** aux connexions SQL Server, avec les autorisations nécessaires. Lancez **SQL Server Management Studio**.

1. Cliquez sur **Connexion** pour vous connecter à l’instance de SQL Server par défaut.

1. Dans l’**Explorateur d’objets**, développez **Sécurité**, puis **Connexions**.

1. Cliquez avec le bouton droit sur la connexion **NT AUTHORITY\\System**, puis cliquez sur **Propriétés**.

1. Dans la page **Éléments sécurisables**, pour le serveur local, sélectionnez **Autoriser** pour les autorisations suivantes et cliquez sur **OK**.

	- Modifier un groupe de disponibilité

	- Connecter SQL

	- Afficher l'état du serveur

1. Ensuite, ajoutez **CORP\\Install** comme rôle **sysadmin** à l’instance SQL Server par défaut. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur **Connexions**, puis cliquez sur **Nouvelle connexion**.

1. Tapez **CORP\\Install** dans **Nom de connexion**.

1. Dans la page **Rôles de serveur**, cliquez sur **sysadmin**. Cliquez ensuite sur **OK**. Une fois la connexion créée, vous pouvez l’afficher en développant **Connexions** dans l’**Explorateur d’objets**.

1. Puis, créez une règle de pare-feu pour un serveur SQL. Lancez le **Pare-feu Windows avec fonctions avancées de sécurité** à partir de l’écran d’**accueil**.

1. Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet droit, cliquez sur **Nouvelle règle**.

1. Dans la page **Type de règle**, sélectionnez **Programme**, puis cliquez sur **Suivant**.

1. Dans la page **Programme**, sélectionnez **Ce chemin d’accès au programme** et tapez **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** dans la zone de texte (si vous suivez ces instructions avec SQL Server 2012, le répertoire de SQL Server est **MSSQL11.MSSQLSERVER**). Cliquez ensuite sur **Suivant**.

1. Dans la page **Action**, sélectionnez **Autoriser la connexion** et cliquez sur **Suivant**.

1. Dans la page **Profil**, acceptez les paramètres par défaut et cliquez sur **Suivant**.

1. Dans la page **Nom**, spécifiez un nom pour la règle, par exemple **SQL Server (règle de programme)** dans la zone de texte **Nom**, puis cliquez sur **Terminer**.

1. Ensuite, activez la fonctionnalité **Groupes de disponibilité AlwaysOn**. À partir de l’écran d’**accueil**, lancez le **Gestionnaire de configuration SQL Server**.

1. Dans l’arborescence du navigateur, cliquez sur **Services SQL Server**, cliquez avec le bouton droit sur **SQL Server (MSSQLSERVER)**, puis cliquez sur **Propriétés**.

1. Cliquez sur l’onglet **Haute disponibilité AlwaysOn**, sélectionnez **Activer les groupes de disponibilité AlwaysOn**, comme illustré ci-dessous, puis cliquez sur **Appliquer**. Cliquez sur **OK** dans la boîte de dialogue contextuelle mais ne fermez pas encore la fenêtre Propriétés. Redémarrez le service SQL Server après avoir modifié le compte de service.

	![Activation des groupes à haute disponibilité AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. Ensuite, modifiez le compte de service SQL Server. Cliquez sur l’onglet **Ouverture de session**, puis tapez **CORP\\SQLSvc1** (pour **ContosoSQL1**) ou **CORP\\SQLSvc2** (pour **ContosoSQL2**) dans **Nom de compte**, tapez et confirmez le mot de passe, puis cliquez sur **OK**.

1. Dans la fenêtre contextuelle, cliquez sur **Oui** pour redémarrer le service SQL Server. Une fois que le service SQL Server est redémarré, les modifications apportées dans la fenêtre Propriétés sont actives.

1. Déconnectez-vous des machines virtuelles.

## Création du groupe de disponibilité

Vous pouvez maintenant configurer le groupe de disponibilité. Voici une présentation des opérations à effectuer :

- Création d’une base de données (**MyDB1**) sur **ContosoSQL1**

- Sauvegarde complète et sauvegarde du journal des transactions de la base de données

- Restauration des sauvegardes complètes et des sauvegardes des journaux vers **ContosoSQL2** avec l’option **NORECOVERY**

- Création du groupe de disponibilité (**AG1**) avec validation synchrone, basculement automatique et réplicas secondaires lisibles

### Création de la base de données MyDB1 sur ContosoSQL1 :

1. Si vous ne vous êtes pas encore déconnecté des sessions Bureau à distance pour **ContosoSQL1** et **ContosoSQL2**, faites-le maintenant.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en tant que **CORP\\Install**.

1. Dans l’**Explorateur de fichiers**, sous **C:**, créez un répertoire nommé **sauvegarde**. Vous utiliserez ce répertoire pour sauvegarder et restaurer votre base de données.

1. Cliquez avec le bouton droit sur le nouveau répertoire, pointez sur **Partager avec**, puis cliquez sur **Des personnes spécifiques**, comme illustré ci-dessous.

	![Créer un dossier de sauvegarde](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. Ajoutez **CORP\\SQLSvc1** et donnez-lui l’autorisation **Lecture/écriture**, puis ajoutez **CORP\\SQLSvc2** et donnez-lui l’autorisation **Lecture**, comme illustré ci-dessous, puis cliquez sur **Partager**. Une fois le processus de partage de fichiers terminé, cliquez sur **Terminer**.

	![Accorder des autorisations pour le dossier de sauvegarde](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. Créez ensuite la base de données. À partir du menu d’**accueil**, lancez **SQL Server Management Studio**, puis cliquez sur **Connexion** pour vous connecter à l’instance de SQL Server par défaut.

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Nouvelle base de données**.

1. Dans **Nom de base de données**, tapez **MyDB1**, puis cliquez sur **OK**.

### Effectuez une sauvegarde complète de MyDB1 et restaurez-la sur ContosoSQL2 :

1. Puis, effectuez une sauvegarde complète de la base de données. Dans l’**Explorateur d’objets**, développez **Bases de données**, cliquez sur **MyDB1**, pointez sur **Tâches**, puis cliquez sur **Sauvegarder**.

1. Dans la section **Source**, laissez la valeur de **Type de sauvegarde** sur **Complet**. Dans la section **Destination**, cliquez sur **Supprimer** afin de supprimer le chemin d’accès de fichier par défaut correspondant au fichier de sauvegarde.

1. Dans la section **Destination**, cliquez sur **Ajouter**.

1. Dans la zone de texte **Nom de fichier**, tapez **\\ContosoSQL1\\backup\\MyDB1.bak**. Ensuite, cliquez sur **OK**, puis à nouveau sur **OK** pour sauvegarder la base de données. Une fois l’opération de sauvegarde terminée, cliquez à nouveau sur **OK** pour fermer la boîte de dialogue.

1. Puis, effectuez une sauvegarde du journal des transactions de la base de données. Dans l’**Explorateur d’objets**, développez **Bases de données**, cliquez sur **MyDB1**, pointez sur **Tâches**, puis cliquez sur **Sauvegarder**.

1. Dans **Type de sauvegarde**, sélectionnez **Journal des transactions**. Conservez le chemin d’accès des fichiers **Destination** que vous avez spécifié précédemment et cliquez sur **OK**. Une fois l’opération de sauvegarde terminée, cliquez à nouveau sur **OK**.

1. Ensuite, restaurez la sauvegarde du journal des transactions et la sauvegarde complète sur **ContosoSQL2**. Lancez le fichier RDP pour **ContosoSQL2** et connectez-vous en tant que **CORP\\Install**. Laissez la session Bureau à distance de **ContosoSQL1** ouverte.

1. À partir du menu d’**accueil**, lancez **SQL Server Management Studio**, puis cliquez sur **Connexion** pour vous connecter à l’instance de SQL Server par défaut.

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Restaurer une base de données**.

1. Dans la section **Source**, sélectionnez **Unité**, puis cliquez sur le bouton **…**.

1. Dans **Sélectionner les unités de sauvegarde**, cliquez sur **Ajouter**.

1. Dans l'emplacement du fichier de sauvegarde, saisissez \\ContosoSQL1\\backup, cliquez sur Actualiser, sélectionnez MyDB1.bak, puis cliquez sur OK et de nouveau sur OK. La sauvegarde complète et la sauvegarde des journaux doivent désormais apparaître dans le volet Jeux de sauvegarde à restaurer.

1. Accédez à la page Options, puis sélectionnez RESTORE WITH NORECOVERY dans l'état de récupération, puis cliquez sur OK pour restaurer la base de données. Lorsque l'opération de restauration est terminée, cliquez à nouveau sur OK.

### Création du groupe de disponibilité :

1. Retournez à la session Bureau à distance de **ContosoSQL1**. Dans l’**Explorateur d’objets** dans SSMS, cliquez sur **Haute disponibilité AlwaysOn**, puis sur **Assistant Nouveau groupe de disponibilité**, comme illustré ci-dessous.

	![Lancer l'Assistant Nouveau groupe de disponibilité](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. Dans la page **Introduction**, cliquez sur **Suivant**. Dans la page **Spécifier le nom du groupe de disponibilité**, tapez **AG1** dans **Nom du groupe de disponibilité**, puis cliquez à nouveau sur **Suivant**.

	![Assistant Nouveau groupe de disponibilité, spécifier le nom du groupe de disponibilité](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. Dans la page **Sélectionner des bases de données**, sélectionnez **MyDB1**, puis cliquez sur **Suivant**. Cette base de données répond à la configuration requise pour un groupe de disponibilité, car vous avez effectué au moins une sauvegarde complète sur le réplica principal prévu.

	![Assistant Nouveau groupe de disponibilité, sélectionner les bases de données](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. Dans la page **Spécifier les réplicas**, cliquez sur **Ajouter un réplica**.

	![Assistant Nouveau groupe de disponibilité, spécifier les réplicas](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. La boîte de dialogue **Se connecter au serveur** apparaît. Tapez **ContosoSQL2** dans **Nom du serveur**, puis cliquez sur **Connexion**.

	![Assistant Nouveau groupe de disponibilité, se connecter au serveur](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. De retour à la page **Spécifier les réplicas**, **ContosoSQL2** doit maintenant figurer dans les **Réplicas disponibles**. Configurez les réplicas comme indiqué ci-dessous. Une fois terminé, cliquez sur **Suivant**.

	![Assistant Nouveau groupe de disponibilité, spécifier les réplicas (Terminé)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. Dans la page **Sélectionner la synchronisation de données initiale**, cliquez sur **Joindre uniquement**, puis sur **Suivant**. Vous avez déjà effectué la synchronisation des données manuellement quand vous avez pris la sauvegarde complète et la sauvegarde des transactions sur **ContosoSQL1** et que vous les avez restaurées sur **ContosoSQL2**. Vous pouvez donc choisir de ne pas effectuer les opérations de sauvegarde et de restauration sur votre base de données et sélectionner **Complète** pour laisser l’Assistant Nouveau groupe de disponibilité effectuer la synchronisation des données pour vous. Toutefois, cela n’est pas recommandé pour les grandes bases de données de certaines entreprises.

	![Assistant Nouveau groupe de disponibilité, sélectionner la synchronisation initiale des données](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. Dans la page **Validation**, cliquez sur **Suivant**. Cette page doit ressembler à l’illustration ci-dessous. Un avertissement concernant la configuration de l’écouteur s’affiche, car aucun écouteur du groupe de disponibilité n’est configuré. Vous pouvez ignorer cet avertissement, étant donné que ce didacticiel ne configure pas d’écouteur. Pour configurer l’écouteur après avoir terminé ce didacticiel, consultez [Configuration d’un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

	![Assistant Nouveau groupe de disponibilité, validation](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. Dans la page **Résumé**, cliquez sur **Terminer**, puis patientez pendant que l’Assistant configure le nouveau groupe de disponibilité. Dans la page **Progression**, vous pouvez cliquer sur **Plus de détails** pour afficher la progression détaillée. Une fois l’Assistant terminé, examinez la page **Résultats** pour vérifier que le groupe de disponibilité a bien été créé, comme illustré ci-dessous, puis cliquez sur **Fermer** pour quitter l’assistant.

	![Assistant Nouveau groupe de disponibilité, résultats](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. Dans l’**Explorateur d’objets**, développez **Haute disponibilité AlwaysOn**, puis **Groupes de disponibilité**. Vous devez maintenant voir le nouveau groupe de disponibilité dans ce conteneur. Cliquez avec le bouton droit sur **AG1 (principal)**, puis cliquez sur **Afficher le tableau de bord**.

	![Afficher le tableau de bord de groupe de disponibilité](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. Votre **tableau de bord AlwaysOn** doit ressembler à celui illustré ci-dessous. Vous pouvez voir les réplicas, le mode de basculement de chaque réplica et l'état de synchronisation.

	![Tableau de bord de groupe de disponibilité](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. Revenez au **Gestionnaire de serveur**, sélectionnez **Outils**, puis lancez le **Gestionnaire du cluster de basculement**.

1. Développez **Cluster1.corp.contoso.com**, puis **Services et applications**. Sélectionnez **Rôles** et notez que le rôle du groupe de disponibilité **AG1** a été créé. Notez que AG1 n'a pas d’adresse IP permettant aux clients de base de données de se connecter au groupe de disponibilité, car vous n'avez pas configuré d’écouteur. Vous pouvez vous connecter directement au nœud principal pour les opérations de lecture-écriture et au nœud secondaire pour les requêtes en lecture seule.

	![Groupe de disponibilité dans le Gestionnaire du cluster de basculement](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING]N'essayez pas de basculer le groupe de disponibilité à partir du Gestionnaire du Cluster de basculement. Vous devez effectuer toutes les opérations de basculement à partir du **tableau de bord AlwaysOn** dans SSMS. Pour plus d’informations, consultez [Restrictions d’utilisation du Gestionnaire du cluster de basculement WSFC avec des groupes de disponibilité](https://msdn.microsoft.com/library/ff929171.aspx).

## Étapes suivantes
Vous avez correctement implémenté SQL Server AlwaysOn en créant un groupe de disponibilité dans Azure. Pour configurer un écouteur pour ce groupe de disponibilité, consultez [Configuration d’un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Pour en savoir plus sur l’utilisation de SQL Server dans Azure, consultez [SQL Server sur Azure Virtual Machines](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1203_2015-->