---
title: "Configuration de groupes de disponibilité Always On dans Azure VM - Classic"
description: "Créez un groupe de disponibilité Always On avec Azure Virtual Machines. Ce didacticiel utilise l&quot;interface utilisateur et des outils, plutôt que des scripts."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: a48bc18cb27e163f00cc94be8c6d6c44d57472e6


---
# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configuration de groupes de disponibilité Always On dans Azure VM - Classic
> [!div class="op_single_selector"]
> * [Resource Manager : modèle](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Resource Manager : mode manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Classic : interface utilisateur](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Classic : PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br/>

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Ce didacticiel de bout en bout vous indique comment implémenter des groupes de disponibilité en utilisant SQL Server Always On sur des machines virtuelles Azure.

À la fin du didacticiel, votre solution SQL Server Always On dans Azure comprendra les éléments suivants :

* un réseau virtuel contenant plusieurs sous-réseaux, notamment un sous-réseau frontal et un sous-réseau principal ;
* un contrôleur de domaine avec un domaine Active Directory (AD) ;
* deux machines virtuelles SQL Server déployées dans le sous-réseau principal et jointes au domaine AD ;
* un cluster WSFC à 3 nœuds avec le modèle de quorum Nœud majoritaire ;
* un groupe de disponibilité avec deux réplicas avec validation synchrone d'une base de données de disponibilité.

La figure suivante est une représentation graphique de la solution.

![Architecture du laboratoire de test pour les groupes de disponibilité dans Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Notez qu'il s'agit d'une configuration possible. Par exemple, vous pouvez réduire le nombre de machines virtuelles pour un groupe de disponibilité de deux réplicas, afin de faire des économies sur les heures de calcul dans Azure, en utilisant le contrôleur de domaine comme témoin de partage de fichiers du quorum dans un cluster WSFC à 2 nœuds. Cette méthode permet de se passer d'une machine virtuelle par rapport à la configuration ci-dessus.

Ce didacticiel part des principes suivants :

* Vous disposez déjà d’un compte Azure.
* Vous savez déjà comment approvisionner une machine virtuelle SQL Server classique de la galerie de machines virtuelles avec l’interface graphique utilisateur.
* Vous disposez déjà d’une connaissance approfondie des groupes de disponibilité Always On. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Si l’utilisation des groupes de disponibilité Always On avec SharePoint vous intéresse, consultez [Configurer des groupes de disponibilité AlwaysOn SQL Server 2012 pour SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Création du réseau virtuel et du serveur de contrôleur de domaine
Vous commencez avec un nouveau compte d'essai Azure. Une fois que vous avez terminé la configuration de votre compte, vous devez vous trouver dans l’écran d’accueil du portail Azure Classic.

1. Cliquez sur le bouton **Nouveau** dans l'angle inférieur gauche de la page, comme illustré ci-dessous.
   
    ![Cliquez sur Nouveau dans le portail](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Cliquez sur **Services réseau**, puis cliquez sur **Réseau virtuel**, puis sur **Création personnalisée**, comme illustré ci-dessous.
   
    ![Créer un réseau virtuel](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. Dans la boîte de dialogue **CRÉER UN RÉSEAU VIRTUEL** , créez un réseau virtuel en parcourant les pages avec les paramètres ci-dessous. 
   
   | Page | Paramètres |
   | --- | --- |
   | Détails du réseau virtuel |**NOM = ContosoNET**<br/>**REGION = West US** |
   | Serveurs DNS et connectivité VPN |Aucun |
   | Espaces d’adressage du réseau virtuel |Ces paramètres sont illustrés dans la capture d'écran ci-dessous :  ![Création d’un réseau virtuel](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Créez ensuite la machine virtuelle que vous allez utiliser comme contrôleur de domaine (DC). Cliquez sur **Nouveau**, puis sur **Compute**, puis sur **Machine virtuelle**, puis sur **De la galerie**, comme illustré ci-dessous.
   
    ![Créer une machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. Dans la boîte de dialogue **CRÉER UNE MACHINE VIRTUELLE** , configurez une nouvelle machine virtuelle en parcourant les pages avec les paramètres ci-dessous. 
   
   | Page | Paramètres |
   | --- | --- |
   | Sélection du système d’exploitation de la machine virtuelle |Windows Server 2012 R2 Datacenter |
   | Configuration de la machine virtuelle |**DATE DE SORTIE DE LA VERSION** = (dernière version)<br/>**NOM DE LA MACHINE VIRTUELLE** = ContosoDC<br/>**NIVEAU** = STANDARD<br/>**TAILLE** = A2 (2 cœurs)<br/>**NOUVEAU NOM D'UTILISATEUR** = AzureAdmin<br/>**NOUVEAU MOT DE PASSE** = Contoso!000<br/>**CONFIRM** = Contoso!000 |
   | Configuration de la machine virtuelle |**SERVICE CLOUD** = créez un service cloud<br/>**NOM DNS DU SERVICE CLOUD** = un nom de service cloud unique<br/>**NOM DNS** = un nom unique (ex : ContosoDC123)<br/>**RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL** = ContosoNET<br/>**SOUS-RÉSEAUX VIRTUELS** = Back(10.10.2.0/24)<br/>**COMPTE DE STOCKAGE** : utilisez un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = (Aucun) |
   | Options de la machine virtuelle |Utilisation des valeurs par défaut |

Une fois que vous avez configuré la nouvelle machine virtuelle, attendez que l'ordinateur virtuel soit provisionné. Ce processus prend du temps, et si vous cliquez sur l’onglet **Virtual Machine** dans le portail Azure Classic, vous pouvez voir le cycle des états ContosoDC allant de **Départ (approvisionnement)** à **Arrêté**, **Départ**, **En cours d’exécution (approvisionnement)** et enfin **En cours d’exécution**.

Le serveur du contrôleur de domaine est maintenant correctement configuré. Ensuite, vous allez configurer le domaine Active Directory sur ce serveur de contrôleur de domaine.

## <a name="configure-the-domain-controller"></a>Configuration du contrôleur de domaine
Dans les étapes suivantes, vous allez configurer la machine ContosoDC comme contrôleur de domaine pour corp.contoso.com.

1. Dans le portail, sélectionnez la machine **ContosoDC** . Sur l'onglet **Tableau de bord**, cliquez sur **Connexion** pour ouvrir un fichier RDP pour l'accès Bureau à distance.
   
    ![Se connecter à la machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Connectez-vous avec votre compte administrateur configuré (**\AzureAdmin**) et votre mot de passe (**Contoso!000**).
3. Par défaut, le tableau de bord **Gestionnaire de serveur** doit être affiché.
4. Cliquez sur le lien du tableau de bord **Ajouter des rôles et fonctionnalités** .
   
    ![Ajout de rôles Explorateur de serveurs](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Sélectionnez **Suivant** jusqu’à ce que vous atteigniez la section **Rôles de serveur**.
6. Sélectionnez les rôles **Services de domaine Active Directory** et **Serveur DNS**. Lorsque vous y êtes invité, ajoutez les fonctionnalités supplémentaires requises par ces rôles.
   
   > [!NOTE]
   > Vous obtiendrez un avertissement de validation vous informant qu'il n’y a aucune adresse IP statique. Si vous testez la configuration, cliquez sur Continuer. Pour les scénarios de production, [utilisez PowerShell pour définir l'adresse IP de l'ordinateur de contrôleur de domaine](../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Boîte de dialogue Ajouter des rôles](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Cliquez sur **Suivant** jusqu’à ce que vous atteigniez la section **Confirmation**. Cochez la case **Redémarrer automatiquement le serveur de destination, si nécessaire** .
8. Cliquez sur **Installer**.
9. Une fois les fonctionnalités installées, retournez dans le tableau de bord **Gestionnaire de serveur** .
10. Sélectionnez la nouvelle option **AD DS** dans le volet gauche.
11. Cliquez sur le lien **Plus** dans la barre d'avertissement jaune.
    
     ![Boîte de dialogue AD DS sur une machine virtuelle du serveur DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. Dans la colonne **Action** de la boîte de dialogue **Tous les détails de la tâche serveur**, cliquez sur **Promouvoir ce serveur en contrôleur de domaine**.
13. Dans l' **Assistant de configuration des services de domaine Active Directory**, utilisez les valeurs suivantes :
    
    | Page | Paramètre |
    | --- | --- |
    | Configuration du déploiement |**Ajouter une nouvelle forêt** = sélectionné<br/>**Nom de domaine racine** = corp.contoso.com |
    | Options de contrôleur de domaine : |**Mot de passe** = Contoso!000<br/>**Confirmer le mot de passe** = Contoso!000 |
14. Cliquez sur **Suivant** pour parcourir les autres pages de l'Assistant. Sur la page **Vérification de la configuration requise**, vérifiez que vous voyez le message suivant : **Toutes les vérifications de la configuration requise ont donné satisfaction**. Notez que vous devez examiner les messages d'avertissement applicables, mais il est possible de poursuivre l'installation.
15. Cliquez sur **Installer**. La machine virtuelle **ContosoDC** redémarre automatiquement.

## <a name="configure-domain-accounts"></a>Configuration des comptes de domaine
Les étapes suivantes configurent les comptes Active Directory (AD) pour une utilisation ultérieure.

1. Reconnectez-vous à la machine **ContosoDC** .
2. Dans **Gestionnaire de serveur**, sélectionnez **Outils**, puis cliquez sur **Centre d’administration Active Directory**.
   
    ![Centre d'administration Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. Dans le panneau **Centre d'administration Active Directory** select **corp (local)** dans le volet gauche.
4. Dans le volet droit **Tâches**, sélectionnez **Nouveau**, puis cliquez sur **Utilisateur**. Utilisez les paramètres suivants :
   
   | Paramètre | Valeur |
   | --- | --- |
   | **Prénom** |Installer |
   | **SamAccountName de l'utilisateur** |Installer |
   | **Mot de passe** |Contoso!000 |
   | **Confirmer le mot de passe** |Contoso!000 |
   | **Autres options de mot de passe** |Sélectionné |
   | **Le mot de passe n'expire jamais** |Activé |
5. Cliquez sur **OK** pour créer l’utilisateur **d’Installation**. Ce compte sera utilisé pour configurer le cluster de basculement et le groupe de disponibilité.
6. Créez deux utilisateurs supplémentaires en suivant les mêmes étapes : **CORP\SQLSvc1** et **CORP\SQLSvc2**. Ces comptes seront utilisés pour les instances de SQL Server. Ensuite, vous devez donner à **CORP\Install**les autorisations nécessaires pour la configuration des clusters de basculement Windows Service (WSFC).
7. Dans le **Centre d’administration Active Directory**, sélectionnez **corp (local)** dans le volet gauche. Ensuite, dans le volet droit **Tâches**, cliquez sur **Propriétés**.
   
    ![Propriétés de l'utilisateur CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Sélectionnez **Extensions**, puis cliquez sur le bouton **Avancé** de l’onglet **Sécurité**.
9. Dans la fenêtre **Paramètres de sécurité avancés pour corp** . Cliquez sur **Ajouter**.
10. Cliquez sur **Sélectionner un principal**. Recherchez **CORP\Install**. Cliquez sur **OK**.
11. Sélectionnez les autorisations **Lire toutes les propriétés** et **Créer des objets ordinateur**.
    
     ![Autorisations de l’utilisateur Corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Cliquez sur **OK**, puis sur **OK** à nouveau. Fermez la fenêtre des propriétés corp.

Maintenant que vous avez fini de configurer Active Directory et les objets utilisateur, vous allez créer trois machines virtuelles SQL Server et les joindre à ce domaine.

## <a name="create-the-sql-server-vms"></a>Création des machines virtuelles SQL Server
Créez ensuite trois machines virtuelles, dont un nœud de cluster WSFC et deux machines virtuelles SQL Server. Pour créer les machines virtuelles, revenez au portail Azure Classic, cliquez sur **Nouveau**, **Compute**, **Machine virtuelle**, puis **De la galerie**. Utilisez ensuite les modèles dans le tableau suivant pour vous aider à créer les machines virtuelles.

| Page | MV1 | MV2 | MV3 |
| --- | --- | --- | --- |
| Sélection du système d’exploitation de la machine virtuelle |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Configuration de la machine virtuelle |**DATE DE SORTIE DE LA VERSION** = (dernière version)<br/>**NOM DE LA MACHINE VIRTUELLE** = ContosoWSFCNode<br/>**NIVEAU** = STANDARD<br/>**TAILLE** = A2 (2 cœurs)<br/>**NOUVEAU NOM D'UTILISATEUR** = AzureAdmin<br/>**NOUVEAU MOT DE PASSE** = Contoso!000<br/>**CONFIRM** = Contoso!000 |**DATE DE SORTIE DE LA VERSION** = (dernière version)<br/>**NOM DE LA MACHINE VIRTUELLE** = ContosoSQL1<br/>**NIVEAU** = STANDARD<br/>**TAILLE** = A3 (4 cœurs)<br/>**NOUVEAU NOM D'UTILISATEUR** = AzureAdmin<br/>**NOUVEAU MOT DE PASSE** = Contoso!000<br/>**CONFIRM** = Contoso!000 |**DATE DE SORTIE DE LA VERSION** = (dernière version)<br/>**NOM DE LA MACHINE VIRTUELLE** = ContosoSQL2<br/>**NIVEAU** = STANDARD<br/>**TAILLE** = A3 (4 cœurs)<br/>**NOUVEAU NOM D'UTILISATEUR** = AzureAdmin<br/>**NOUVEAU MOT DE PASSE** = Contoso!000<br/>**CONFIRM** = Contoso!000 |
| Configuration de la machine virtuelle |**SERVICE CLOUD** = nom DNS du service cloud unique créé précédemment (ex : ContosoDC123)<br/>**RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL** = ContosoNET<br/>**SOUS-RÉSEAUX VIRTUELS** = Back(10.10.2.0/24)<br/>**COMPTE DE STOCKAGE** : utilisez un compte de stockage généré automatiquement<br/>**GROUPE À HAUTE DISPONIBILITÉ** = Créez un groupe à haute disponibilité<br/>**AVAILABILITY SET NAME** = SQLHADR |**SERVICE CLOUD** = nom DNS du service cloud unique créé précédemment (ex : ContosoDC123)<br/>**RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL** = ContosoNET<br/>**SOUS-RÉSEAUX VIRTUELS** = Back(10.10.2.0/24)<br/>**COMPTE DE STOCKAGE** : utilisez un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = SQLHADR (Vous pouvez également configurer le groupe à haute disponibilité après la création de la machine. Les trois machines doivent être assignées au groupe à haute disponibilité SQLHADR.) |**SERVICE CLOUD** = nom DNS du service cloud unique créé précédemment (ex : ContosoDC123)<br/>**RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL** = ContosoNET<br/>**SOUS-RÉSEAUX VIRTUELS** = Back(10.10.2.0/24)<br/>**COMPTE DE STOCKAGE** : utilisez un compte de stockage généré automatiquement<br/>**AVAILABILITY SET** = SQLHADR (Vous pouvez également configurer le groupe à haute disponibilité après la création de la machine. Les trois machines doivent être assignées au groupe à haute disponibilité SQLHADR.) |
| Options de la machine virtuelle |Utilisation des valeurs par défaut |Utilisation des valeurs par défaut |Utilisation des valeurs par défaut |

<br/>

> [!NOTE]
> La configuration précédente suggère des machines virtuelles de niveau STANDARD, car les machines de niveau DE BASE ne prennent pas en charge les points de terminaison à équilibrage de charge nécessaires pour créer ultérieurement un écouteur de groupe de disponibilité. De plus, les tailles de machines suggérées ici sont destinées au test des groupes de disponibilité dans les machines virtuelles Azure. Pour optimiser les performances des charges de travail de production, consultez les recommandations relatives à la taille des ordinateurs SQL Server et à la configuration dans [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

Une fois les trois machines virtuelles entièrement configurées, vous devez les attacher au domaine **corp.contoso.com** et accorder à CORP\Install des droits d’administration sur les machines. Pour ce faire, procédez comme suit pour chacune des trois machines virtuelles.

1. Tout d'abord, modifiez l'adresse de serveur DNS par défaut. Commencez par télécharger le fichier Bureau à distance (RDP) de chaque machine virtuelle dans votre répertoire local en sélectionnant la machine virtuelle dans la liste en cliquant sur le bouton **Connexion** . Pour sélectionner une machine virtuelle, cliquez partout sauf sur la première cellule de la ligne, comme illustré ci-dessous.
   
    ![Téléchargement du fichier RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Lancez le fichier RDP téléchargé et connectez-vous à la machine virtuelle à l’aide de votre compte d’administrateur configuré (**BUILTIN\AzureAdmin**) et de votre mot de passe (**Contoso!000**).
3. Une fois connecté, vous devez voir le tableau de bord **Gestionnaire de serveur** . Dans le volet gauche, cliquez sur **Serveur local** .
4. Sélectionnez le lien **Adresse IPv4 attribuée par DHCP, Compatible IPv6** .
5. Dans la fenêtre **Connexions réseau** , sélectionnez l’icône du réseau.
   
    ![Modification du serveur DNS préféré de la machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Dans la barre de commandes, cliquez sur **Modifier les paramètres de cette connexion** (selon la taille de la fenêtre, vous devrez peut-être cliquer sur la double flèche droite pour afficher cette commande).
7. Cliquez sur **Internet Protocol Version 4 (TCP/IPv4)** , puis sur Propriétés.
8. Sélectionnez Utiliser l’adresse de serveur DNS suivante et spécifiez **10.10.2.4** dans **Serveur DNS préféré**.
9. L’adresse **10.10.2.4** est celle attribuée à une machine virtuelle dans le sous-réseau 10.10.2.0/24 dans un réseau virtuel Azure, et cette machine virtuelle est **ContosoDC**. Pour vérifier l’adresse IP de **ContosoDC**, exécutez **nslookup contosodc** à l’invite de commandes, comme indiqué ci-dessous.
   
    ![Utilisation de NSLOOKUP pour trouver l'adresse IP du contrôleur de domaine](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Cliquez sur **OK**, puis sur **Fermer** pour valider les modifications. Vous pouvez maintenant joindre la machine virtuelle à **corp.contoso.com**.
11. De retour dans la fenêtre **Serveur local**, cliquez sur le lien **WORKGROUP**.
12. Dans la section **Nom de l’ordinateur**, cliquez sur **Modifier**.
13. Cochez la case **Domaine** et saisissez **corp.contoso.com** dans la zone de texte. Cliquez sur **OK**.
14. Dans la boîte de dialogue contextuelle **Sécurité Windows**, spécifiez les informations d’identification du compte d’administrateur de domaine par défaut (**CORP\AzureAdmin**) et le mot de passe (**Contoso!000**).
15. Lorsque le message « Bienvenue dans le domaine corp.contoso.com » s’affiche, cliquez sur **OK**.
16. Cliquez sur **Fermer**, puis sur **Redémarrer maintenant** dans la boîte de dialogue contextuelle.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Ajoutez l'utilisateur Corp\Install en tant qu'administrateur sur chaque machine virtuelle :
1. Attendez le redémarrage de la machine virtuelle, puis relancez le fichier RDP pour vous connecter à la machine virtuelle à l’aide du compte **BUILTIN\AzureAdmin**.
2. Dans **Gestionnaire de serveur**, sélectionnez **Outils**, puis cliquez sur **Gestion de l’ordinateur**.
   
    ![Gestion de l'ordinateur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. Dans la fenêtre **Gestion de l’ordinateur**, développez **Utilisateurs et groupes locaux**, puis sélectionnez **Groupes**.
4. Double-cliquez sur le groupe **Administrateurs** .
5. Dans la boîte de dialogue **Propriétés de Administrateurs**, cliquez sur le bouton **Ajouter**.
6. Entrez l’utilisateur **CORP\Install**, puis cliquez sur **OK**. À l’invite d’informations d’identification, utilisez le compte **AzureAdmin** avec le mot de passe **Contoso!000**.
7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de Administrateurs**.

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Ajoutez la fonctionnalité de **Clustering de basculement** à chaque machine virtuelle.
1. Dans le tableau de bord **Gestionnaire de serveur**, cliquez sur **Ajouter des rôles et fonctionnalités**.
2. Dans **l’Assistant d’ajout de rôles et fonctionnalités**, cliquez sur **Suivant** jusqu’à ce que vous atteigniez la page **Fonctionnalités**.
3. Sélectionnez **Clustering de basculement**. Lorsque vous y êtes invité, ajoutez toutes les autres fonctionnalités.
   
    ![Ajout de la fonctionnalité de Clustering de basculement à la machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Cliquez sur **Suivant**, puis sur **Installer** dans la page **Confirmation**.
5. Lorsque l’installation de la fonctionnalité **Clustering de basculement** est terminée, cliquez sur **Fermer**.
6. Déconnectez-vous de la machine virtuelle.
7. Répétez les étapes de cette section pour les trois serveurs : **ContosoWSFCNode**, **ContosoSQL1** et **ContosoSQL2**.

Les machines virtuelles SQL Server sont maintenant configurés et exécutées, mais elles sont installées avec les options par défaut de SQL Server.

## <a name="create-the-wsfc-cluster"></a>Création du cluster WSFC
Dans cette section, vous allez créer le cluster WSFC qui héberge le groupe de disponibilité que vous créerez ultérieurement. À ce stade, vous devriez avoir effectué les opérations suivantes pour chacune des trois machines virtuelles que vous allez utiliser dans le cluster WSFC :

* configuration complète dans Azure
* machine virtuelle jointe au domaine
* Ajout de **CORP\Install** au groupe Administrateurs local
* Ajout de la fonctionnalité de Clustering de basculement à chaque machine virtuelle

Il s'agit des composants requis sur chaque machine virtuelle avant sa liaison au cluster WSFC.

Notez également que le réseau virtuel Azure ne se comporte pas de la même manière qu'un réseau local. Vous devez créer le cluster dans l'ordre suivant :

1. Création d’un cluster à nœud unique sur l’un des nœuds (**ContosoSQL1**).
2. Modification de l’adresse IP du cluster en une adresse IP inutilisée (**10.10.2.101**).
3. Mise en ligne du nom de cluster.
4. Ajout des autres nœuds (**ContosoSQL2** et **ContosoWSFCNode**).

Suivez les étapes ci-dessous pour accomplir les tâches de configuration complète du cluster.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en utilisant le compte de domaine **CORP\Install**.
2. Dans le tableau de bord **Gestionnaire de serveur**, sélectionnez **Outils**, puis cliquez sur **Gestionnaire du cluster de basculement**.
3. Dans le volet gauche, cliquez sur **Gestionnaire du cluster de basculement**, puis sur **Créer un cluster**, comme illustré ci-dessous.
   
    ![Créer un cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. Dans l'Assistant de création de cluster, créez un cluster à un nœud en complétant les pages avec les paramètres ci-dessous :
   
   | Page | Paramètres |
   | --- | --- |
   | Avant de commencer |Utilisation des valeurs par défaut |
   | Sélection des serveurs |Tapez **ContosoSQL1** dans **Entrez le nom du serveur**, puis cliquez sur **Ajouter** |
   | Avertissement de validation |Cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster**. |
   | Point d'accès pour l'administration du cluster |Saisissez **Cluster1** dans **Nom du cluster**. |
   | Confirmation |Utilisez les valeurs par défaut, sauf si vous utilisez des espaces de stockage. Consultez la remarque qui suit ce tableau. |
   
   > [!WARNING]
   > Si vous utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739), qui regroupent plusieurs disques dans des pools de stockage, vous devez décocher la case **Ajouter la totalité du stockage au cluster** sur la page **Confirmation**. Si vous ne décochez pas cette option, les disques virtuels sont détachés pendant le processus de clustering. Par conséquent, ils n’apparaissent pas non plus dans le Gestionnaire de disque ou dans l'explorateur, jusqu'à ce que les espaces de stockage soient supprimés du cluster et rattachés à l'aide de PowerShell.
   > 
   > 
5. Dans le volet gauche, développez **Gestionnaire du cluster de basculement**, puis cliquez sur **Cluster1.corp.contoso.com**.
6. Dans le volet central, faites défiler jusqu’à la section **Ressources principales du cluster** et développez les détails de **Nom : Cluster1**. Les ressources **Nom** et **Adresse IP** doivent toutes deux être à l’état **Échec**. La ressource d’adresse IP ne peut pas être mise en ligne, car le cluster a la même adresse IP que la machine elle-même, qui est une adresse dupliquée.
7. Cliquez avec le bouton droit sur la ressource **Adresse IP** en échec, puis cliquez sur **Propriétés**.
   
    ![Propriétés du cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Sélectionnez **Adresse IP statique** et spécifiez **10.10.2.101** dans la zone de texte Adresse. Cliquez ensuite sur **OK**.
9. Dans la section **Principales ressources de cluster**, cliquez avec le bouton droit sur **Nom : Cluster1**, puis cliquez sur **Mettre en ligne**. Attendez que les deux ressources soient en ligne. Lorsque la ressource du cluster apparaît en ligne, elle met à jour le serveur de contrôleur de domaine avec un nouveau compte d’ordinateur Active Directory (AD). Ce compte AD permet ultérieurement d’exécuter le service en cluster du groupe de disponibilité.
10. Enfin, ajoutez les nœuds restants au cluster. Dans l’arborescence du navigateur, cliquez avec le bouton droit sur **Cluster1.corp.contoso.com**, puis cliquez sur **Ajouter un nœud**, comme illustré ci-dessous.
    
     ![Ajout d’un nœud au cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. Dans **l’Assistant d’ajout de nœud**, cliquez sur **Suivant**. Dans la page **Sélectionner des serveurs**, ajoutez **ContosoSQL2** et **ContosoWSFCNode** à la liste en tapant le nom du serveur dans **Entrez le nom du serveur**, puis en cliquant sur **Ajouter**. Une fois ces opérations effectuées, cliquez sur **Suivant**.
12. Sur la page **Avertissement de validation**, cliquez sur **Non** (dans un scénario de production, vous devez effectuer les essais de validation). Cliquez ensuite sur **Suivant**.
13. Dans la page **Confirmation**, cliquez sur **Suivant** pour ajouter les nœuds.
    
    > [!WARNING]
    > Si vous utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739), qui regroupent plusieurs disques dans des pools de stockage, vous devez décocher la case **Ajouter la totalité du stockage disponible au cluster** . Si vous ne décochez pas cette option, les disques virtuels sont détachés pendant le processus de clustering. Par conséquent, ils n’apparaissent pas non plus dans le Gestionnaire de disque ou dans l'explorateur, jusqu'à ce que les espaces de stockage soient supprimés du cluster et rattachés à l'aide de PowerShell.
    > 
    > 
14. Une fois les nœuds ajoutés au cluster, cliquez sur **Terminer**. Le Gestionnaire du cluster de basculement doit maintenant indiquer que votre cluster a trois nœuds, répertoriés dans le conteneur **Nœuds** .
15. Déconnectez-vous de la session Bureau à distance.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Préparer les instances de SQL Server pour le groupe de disponibilité
Dans cette section, vous effectuerez les opérations suivantes sur **ContosoSQL1** et **contosoSQL2** :

* Ajouter une connexion pour **NT AUTHORITY\System** avec les autorisations nécessaires pour l’instance SQL Server par défaut
* Ajouter **CORP\Install** en tant que rôle sysadmin à l’instance SQL Server par défaut
* Ouvrir le pare-feu pour l'accès à distance de SQL Server
* Activer la fonctionnalité de groupes de disponibilité Always On
* Modifier le compte de service SQL Server pour **CORP\SQLSvc1** et **CORP\SQLSvc2**, respectivement

Ces actions peuvent être effectuées dans n'importe quel ordre. Néanmoins, les étapes ci-dessous les traitent dans l'ordre. Suivez les étapes pour **ContosoSQL1** et **ContosoSQL2** :

1. Si vous ne vous êtes pas déconnecté de la session Bureau à distance pour la machine virtuelle, faites-le maintenant.
2. Lancez les fichiers RDP pour **ContosoSQL1** et **ContosoSQL2** et connectez-vous en tant que **BUILTIN\AzureAdmin**.
3. Tout d’abord, ajoutez **NT AUTHORITY\System** aux connexions SQL Server, avec les autorisations nécessaires. Lancez **SQL Server Management Studio**.
4. Cliquez sur **Connexion** pour vous connecter à l’instance de SQL Server par défaut.
5. Dans l’**Explorateur d’objets**, développez **Sécurité**, puis **Connexions**.
6. Cliquez avec le bouton droit sur la connexion **NT AUTHORITY\System**, puis cliquez sur **Propriétés**.
7. Dans la page **Éléments sécurisables**, pour le serveur local, sélectionnez **Autoriser** pour les autorisations suivantes et cliquez sur **OK**.
   
   * Modifier un groupe de disponibilité
   * Connecter SQL
   * Afficher l’état du serveur
8. Ensuite, ajoutez **CORP\Install** comme rôle **sysadmin** à l’instance SQL Server par défaut. Dans **l’Explorateur d’objets**, cliquez avec le bouton droit sur **Connexions**, puis cliquez sur **Nouvelle connexion**.
9. Saisissez **CORP\Install** dans **Nom de connexion**.
10. Dans la page **Rôles de serveur**, cliquez sur **sysadmin**. Cliquez ensuite sur **OK**. Une fois la connexion créée, vous pouvez l'afficher en développant **Connexions** in **Explorateur d'objets**.
11. Puis, créez une règle de pare-feu pour un serveur SQL. Lancez le **Pare-feu Windows avec fonctions de sécurité avancées** à partir de l’écran **d’accueil**.
12. Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet droit, cliquez sur **Nouvelle règle**.
13. Dans la page **Type de règle**, sélectionnez **Programme**, puis cliquez sur **Suivant**.
14. Dans la page **Programme**, sélectionnez **Ce chemin d’accès au programme** et saisissez **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** dans la zone de texte (si vous suivez ces instructions avec SQL Server 2012, le répertoire de SQL Server est **MSSQL11.MSSQLSERVER**). Cliquez ensuite sur **Suivant**.
15. Dans la page **Action**, sélectionnez **Autoriser la connexion** et cliquez sur **Suivant**.
16. Dans la page **Profil**, acceptez les paramètres par défaut et cliquez sur **Suivant**.
17. Dans la page **Nom**, spécifiez un nom pour la règle, par exemple **SQL Server (règle de programme)** dans la zone de texte **Nom**, puis cliquez sur **Terminer**.
18. Ensuite, activez la fonctionnalité **Groupes de disponibilité Always On** . À partir de l’écran **d’accueil**, lancez le **Gestionnaire de configuration SQL Server**.
19. Dans l’arborescence du navigateur, cliquez sur **Services SQL Server**, cliquez avec le bouton droit sur **SQL Server (MSSQLSERVER)**, puis cliquez sur **Propriétés**.
20. Cliquez sur l’onglet **Haute disponibilité Always On**, sélectionnez **Activer les groupes de disponibilité Always On** comme illustré ci-dessous, puis cliquez sur **Appliquer**. Cliquez sur **OK** dans la boîte de dialogue contextuelle mais ne fermez pas encore la fenêtre Propriétés. Redémarrez le service SQL Server après avoir modifié le compte de service.
    
     ![Activer des groupes de disponibilité Always On](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Ensuite, modifiez le compte de service SQL Server. Cliquez sur l’onglet **Ouverture de session**, puis tapez **CORP\SQLSvc1** (pour **ContosoSQL1**) ou **CORP\SQLSvc2** (pour **ContosoSQL2**) dans **Nom de compte**, tapez et confirmez le mot de passe, puis cliquez sur **OK**.
22. Dans la fenêtre contextuelle, cliquez sur **Oui** pour redémarrer le service SQL Server. Une fois que le service SQL Server est redémarré, les modifications apportées dans la fenêtre Propriétés sont actives.
23. Déconnectez-vous des machines virtuelles.

## <a name="create-the-availability-group"></a>Création du groupe de disponibilité
Vous pouvez maintenant configurer le groupe de disponibilité. Voici une présentation des opérations à effectuer :

* Création d’une base de données (**MyDB1**) sur **ContosoSQL1**
* Sauvegarde complète et sauvegarde du journal des transactions de la base de données
* Restauration de la version complète et de sauvegardes des journaux vers **ContosoSQL2** avec l'option **NORECOVERY** 
* Création du groupe de disponibilité (**AG1**) avec validation synchrone, basculement automatique et réplicas secondaires lisibles

### <a name="create-the-mydb1-database-on-contososql1"></a>Création de la base de données MyDB1 sur ContosoSQL1 :
1. Si vous ne vous êtes pas encore déconnecté des sessions Bureau à distance pour **ContosoSQL1** et **ContosoSQL2**, faites-le maintenant.
2. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en tant que **CORP\Install**.
3. Dans **l’Explorateur de fichiers**, sous **C:\**, créez un répertoire appelé **sauvegarde**. Vous utiliserez ce répertoire pour sauvegarder et restaurer votre base de données.
4. Cliquez avec le bouton droit sur le nouveau répertoire, pointez sur **Partager avec**, puis cliquez sur **Des personnes spécifiques**, comme illustré ci-dessous.
   
    ![Créer un dossier de sauvegarde](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Ajoutez **CORP\SQLSvc1** et donnez-lui l’autorisation **Lecture/écriture**, puis ajoutez **CORP\SQLSvc2** et donnez-lui l’autorisation **Lecture**, comme illustré ci-dessous, puis cliquez sur **Partager**. Une fois le processus de partage de fichiers terminé, cliquez sur **Terminer**.
   
    ![Accorder des autorisations pour le dossier de sauvegarde](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Créez ensuite la base de données. À partir du menu **d’accueil**, lancez **SQL Server Management Studio**, puis cliquez sur **Connexion** pour vous connecter à l’instance SQL Server par défaut.
7. Dans **l’Explorateur d’objets**, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Nouvelle base de données**.
8. Dans **Nom de base de données**, saisissez **MyDB1**, puis cliquez sur **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Effectuez une sauvegarde complète de MyDB1 et restaurez-la sur ContosoSQL2 :
1. Puis, effectuez une sauvegarde complète de la base de données. Dans **l’Explorateur d’objets**, développez **Bases de données**, cliquez avec le bouton droit sur **MyDB1**, pointez sur **Tâches**, puis cliquez sur **Sauvegarder**.
2. Dans la section **Source**, laissez la valeur de **Type de sauvegarde** sur **Complet**. Dans la section **Destination**, cliquez sur **Supprimer** afin de supprimer le chemin d’accès de fichier par défaut correspondant au fichier de sauvegarde.
3. Dans la section **Destination**, cliquez sur **Ajouter**.
4. Dans la zone de texte **Nom de fichier**, tapez **\\ContosoSQL1\backup\MyDB1.bak**. Ensuite, cliquez sur **OK**, puis à nouveau sur **OK** pour sauvegarder la base de données. Une fois l’opération de sauvegarde terminée, cliquez à nouveau sur **OK** pour fermer la boîte de dialogue.
5. Puis, effectuez une sauvegarde du journal des transactions de la base de données. Dans **l’Explorateur d’objets**, développez **Bases de données**, cliquez avec le bouton droit sur **MyDB1**, pointez sur **Tâches**, puis cliquez sur **Sauvegarder**.
6. Dans **Type de sauvegarde**, sélectionnez **Journal des transactions**. Conservez le chemin d’accès des fichiers **Destination** que vous avez spécifié précédemment et cliquez sur **OK**. Une fois l’opération de sauvegarde terminée, cliquez à nouveau sur **OK** .
7. Ensuite, restaurez la sauvegarde du journal des transactions et la sauvegarde complète sur **ContosoSQL2**. Lancez le fichier RDP pour **ContosoSQL2** et connectez-vous en tant que **CORP\Install**. Laissez la session Bureau à distance de **ContosoSQL1** ouverte.
8. À partir du menu **d’accueil**, lancez **SQL Server Management Studio**, puis cliquez sur **Connexion** pour vous connecter à l’instance SQL Server par défaut.
9. Dans **l’Explorateur d’objets**, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Restaurer la base de données**.
10. Dans la section **Source**, sélectionnez **Unité**, puis cliquez sur le bouton **…** .
11. Dans **Sélectionner les unités de sauvegarde**, cliquez sur **Ajouter**.
12. Dans l'emplacement du fichier de sauvegarde, saisissez \\ContosoSQL1\backup, cliquez sur Actualiser, sélectionnez MyDB1.bak, puis cliquez sur OK et de nouveau sur OK. La sauvegarde complète et la sauvegarde des journaux doivent désormais apparaître dans le volet Jeux de sauvegarde à restaurer.
13. Accédez à la page Options, puis sélectionnez RESTORE WITH NORECOVERY dans l'état de récupération, puis cliquez sur OK pour restaurer la base de données. Lorsque l'opération de restauration est terminée, cliquez à nouveau sur OK.

### <a name="create-the-availability-group"></a>Création du groupe de disponibilité :
1. Retournez à la session Bureau à distance de **ContosoSQL1**. Dans **l’Explorateur d’objets** dans SSMS, cliquez avec le bouton droit sur **Haute disponibilité Always On**, puis sur **Assistant Nouveau groupe de disponibilité**, comme illustré ci-dessous.
   
    ![Lancer l'Assistant Nouveau groupe de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. Sur la page **Introduction**, cliquez sur **Suivant**. Dans la page **Spécifier le nom du groupe de disponibilité**, saisissez **AG1** dans **Nom du groupe de disponibilité**, puis cliquez à nouveau sur **Suivant**.
   
    ![Assistant Nouveau groupe de disponibilité, spécifier le nom du groupe de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. Dans la page **Sélectionner des bases de données**, sélectionnez **MyDB1**, puis cliquez sur **Suivant**. Cette base de données répond à la configuration requise pour un groupe de disponibilité, car vous avez effectué au moins une sauvegarde complète sur le réplica principal prévu.
   
    ![Assistant Nouveau groupe de disponibilité, sélectionner les bases de données](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. Sur la page **Spécifier les réplicas**, cliquez sur **Ajouter un réplica**.
   
    ![Assistant Nouveau groupe de disponibilité, spécifier les réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. La boîte de dialogue **Se connecter au serveur** apparaît. Tapez **ContosoSQL2** dans **Nom du serveur**, puis cliquez sur **Connexion**.
   
    ![Assistant Nouveau groupe de disponibilité, se connecter au serveur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. De retour sur la page **Spécifier les réplicas**, vous devez maintenant voir **ContosoSQL2** répertorié dans les **Réplicas disponibles**. Configurez les réplicas comme indiqué ci-dessous. Quand vous avez terminé, cliquez sur **Suivant**.
   
    ![Assistant Nouveau groupe de disponibilité, spécifier les réplicas (Terminé)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. Sur la page **Sélectionner la synchronisation de données initiale**, cliquez sur **Joindre uniquement**, puis sur **Suivant**. Vous avez déjà effectué la synchronisation des données manuellement lorsque vous avez effectué les sauvegardes complète et de transaction sur **ContosoSQL1** avant de les restaurer sur **ContosoSQL2**. Vous pouvez donc choisir de ne pas effectuer les opérations de sauvegarde et de restauration sur votre base de données et sélectionner **Complète** pour laisser l’Assistant Nouveau groupe de disponibilité effectuer la synchronisation des données pour vous. Toutefois, cela n’est pas recommandé pour les grandes bases de données de certaines entreprises.
   
    ![Assistant Nouveau groupe de disponibilité, sélectionner la synchronisation initiale des données](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. Dans la page **Validation**, cliquez sur **Suivant**. Cette page doit ressembler à l’illustration ci-dessous. Un avertissement concernant la configuration de l’écouteur s’affiche, car aucun écouteur du groupe de disponibilité n’est configuré. Vous pouvez ignorer cet avertissement, étant donné que ce didacticiel ne configure pas d’écouteur. Pour configurer l’écouteur après avoir terminé ce didacticiel, voir [Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
   
    ![Assistant Nouveau groupe de disponibilité, validation](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. Dans la page **Résumé**, cliquez sur **Terminer**, puis patientez pendant que l’assistant configure le nouveau groupe de disponibilité. Dans la page **Progression**, vous pouvez cliquer sur **Plus de détails** pour afficher la progression détaillée. Une fois l’assistant terminé, consultez la page **Résultats** pour vérifier que le groupe de disponibilité a bien été créé, comme illustré ci-dessous, puis cliquez sur **Fermer** pour quitter l’assistant.
   
    ![Assistant Nouveau groupe de disponibilité, résultats](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. Dans **l’Explorateur d’objets**, développez **Haute disponibilité Always On**, puis **Groupes de disponibilité**. Vous devez maintenant voir le nouveau groupe de disponibilité dans ce conteneur. Cliquez avec le bouton droit sur **AG1 (principal)**, puis cliquez sur **Afficher le tableau de bord**.
    
     ![Afficher le tableau de bord de groupe de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. Votre **tableau de bord Always On** doit ressembler à celui illustré ci-dessous. Vous pouvez voir les réplicas, le mode de basculement de chaque réplica et l'état de synchronisation.
    
     ![Tableau de bord de groupe de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Revenez au **Gestionnaire de serveur**, sélectionnez **Outils**, puis lancez le **Gestionnaire du cluster de basculement**.
13. Développez **Cluster1.corp.contoso.com**, puis **Services et applications**. Sélectionnez **Rôles** et notez que le rôle du groupe de disponibilité **AG1** a été créé. Notez que AG1 n'a pas d’adresse IP permettant aux clients de base de données de se connecter au groupe de disponibilité, car vous n'avez pas configuré d’écouteur. Vous pouvez vous connecter directement au nœud principal pour les opérations de lecture-écriture et au nœud secondaire pour les requêtes en lecture seule.
    
     ![Groupe de disponibilité dans le Gestionnaire du cluster de basculement](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> N'essayez pas de basculer le groupe de disponibilité à partir du Gestionnaire du Cluster de basculement. Vous devez effectuer toutes les opérations de basculement à partir du **tableau de bord Always On** dans SSMS. Pour plus d’informations, consultez [Restrictions d’utilisation du Gestionnaire du cluster de basculement WSFC avec des groupes de disponibilité](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Vous avez correctement implémenté SQL Server Always On en créant un groupe de disponibilité dans Azure. Pour configurer un écouteur pour ce groupe de disponibilité, voir [Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Pour en savoir plus sur l’utilisation de SQL Server dans Azure, consultez [SQL Server sur Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Dec16_HO1-->


