<properties
	pageTitle="Version préliminaire des services Azure Active Directory Domain : guide d’administration | Microsoft Azure"
	description="Administrer les domaines gérés par les services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="maheshu"/>

# Administrer un domaine géré par les services de domaine Azure Active Directory
Cet article indique comment administrer un domaine géré par les services de domaine Azure Active Directory.

## Tâches d’administration pouvant être effectuées sur un domaine géré
Pour commencer, étudions les tâches d’administration que vous pouvez effectuer sur un domaine géré. Les membres du groupe « AAD DC Administrators » bénéficient de privilèges leur permettant d’effectuer les tâches suivantes sur le domaine géré :

- joindre des ordinateurs au domaine ;

- configurer le GPO intégré pour les conteneurs Ordinateurs et Utilisateurs au sein du domaine ;

- administrer le serveur DNS sur le domaine ;

- créer des UO personnalisées sur le domaine ;

- obtenir un accès d’administrateur aux ordinateurs joints au domaine géré ;


## Privilèges d’administrateur dont vous ne disposez pas concernant un domaine géré
Le domaine est géré par Microsoft, y compris les activités telles que les mises à jour correctives, la surveillance, les sauvegardes, etc. Par conséquent, le domaine est verrouillé ; vous ne disposez pas de privilèges permettant d’effectuer certaines tâches d’administration sur le domaine. Voici quelques exemples de tâches que vous ne pouvez pas exécuter.

- Vous ne pouvez pas manipuler des privilèges d’administrateur de domaine ou d’entreprise pour le domaine géré.

- Vous ne pouvez pas étendre le schéma du domaine géré.

- Vous ne pouvez pas vous connecter à des contrôleurs de domaine via le Bureau à distance.

- Vous ne pouvez pas ajouter des contrôleurs de domaine au domaine.


## Configurer une machine virtuelle jointe au domaine afin d’administrer le domaine géré à distance
Vous pouvez administrer les domaines gérés par les services de domaine Azure Active Directory (AD) par l’intermédiaire des outils d’administration familiers d’Active Directory, par exemple le Centre d’administration Active Directory (ADAC, Active Directory Administrative Center) ou AD PowerShell. Les administrateurs clients ne disposent pas des privilèges permettant la connexion aux contrôleurs de domaine sur le domaine géré, via le Bureau à distance. Par conséquent, les membres du groupe « AAD DC Administrators » peuvent administrer les domaines gérés à distance, à l’aide des outils d’administration AD d’un ordinateur client/Windows Server joint au domaine géré. Les outils d’administration AD peuvent être installés en tant que composants des Outils d’administration de serveur distant (fonction en option) sur les machines clientes et Windows Server jointes au domaine géré.

La première étape consiste à configurer une machine virtuelle Windows Server qui est jointe au domaine géré. Pour savoir comment procéder, vous pouvez consulter l’article [Join a Window Server virtual machine to an Azure AD Domain Services managed domain (Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD)](active-directory-ds-admin-guide-join-windows-vm.md).

### Administrer à distance le domaine géré à partir d’un ordinateur client (par exemple : Windows 10)
Remarque : ces instructions tirent parti d’une machine virtuelle Windows Server afin d’administrer le domaine géré par les services de domaine Azure AD (AAD-DS). Toutefois, vous pouvez également choisir d’utiliser une machine virtuelle cliente Windows (par exemple, Windows 10).

Vous pouvez [installer les Outils d’administration de serveur distant](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) sur un ordinateur virtuel client Windows en suivant les instructions de TechNet.


## Installer les Outils d’administration Azure Directory sur la machine virtuelle
Procédez comme suit pour installer les outils d’administration Azure Directory sur la machine virtuelle jointe au domaine. Pour en savoir plus sur [l’installation et l’utilisation des Outils d’administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx), consultez TechNet.

1. Accédez au nœud **Virtual Machines** du portail Azure Classic. Sélectionnez la machine virtuelle que vous venez de créer et cliquez sur l’option **Connecter** de la barre de commandes, au bas de la fenêtre.

    ![Se connecter à une machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail Classic vous invite à ouvrir ou enregistrer un fichier .rdp, qui est utilisé pour la connexion à la machine virtuelle. Lorsqu’il est téléchargé, cliquez sur le fichier .rdp.

3. À l’invite de connexion, utilisez les informations d’identification d’un utilisateur appartenant au groupe « AAD DC Administrators ». Dans notre cas : bob@domainservicespreview.onmicrosoft.com, par exemple.

4. Dans l’écran d’accueil, ouvrez **Gestionnaire de serveur**. Dans le volet central de la fenêtre Gestionnaire de serveur, cliquez sur l’option **Ajout de rôles et de fonctionnalités**.

    ![Lancer le gestionnaire de serveur sur la machine virtuelle](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Sur la page **Avant de commencer** de l’**Assistant Ajout de rôles et de fonctionnalités**, cliquez sur **Suivant**.

    ![Page Avant de commencer](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Sur la page **Type d’installation**, laissez l’option **Installation basée sur un rôle ou une fonctionnalité** sélectionnée et cliquez sur **Suivant**.

	![Page Type d’installation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Dans la page **Sélection du serveur**, choisissez la machine virtuelle actuelle dans le pool de serveurs, puis cliquez sur **Suivant**.

	![Page Sélection du serveur](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Sur la page **Rôles des serveurs**, cliquez sur **Suivant**. Nous allons ignorer cette page, car nous ne sommes pas en train d’installer des rôles sur le serveur.

9. Sur la page **Fonctionnalités**, cliquez sur le nœud **Outils d’administration de serveur distant** pour le développer, puis sur le nœud **Outils d’administration de rôles**, pour le développer. Sélectionnez la fonctionnalité **Outils AD DS et AD LDS** dans la liste Outils d’administration de rôles, comme indiqué ci-dessous.

	![Page Fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Sur la page **Confirmation**, cliquez sur **Installer** pour installer la fonctionnalité Outils AD DS et AD LDS sur la machine virtuelle. Une fois l’installation de la fonctionnalité terminée, cliquez sur **Fermer** afin de fermer l’Assistant **Ajout de rôles et de fonctionnalités**.

	![Page Confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## Explorer le domaine géré
Maintenant que les outils d’administration AD sont installés sur la machine virtuelle jointe au domaine, nous pouvons les utiliser pour explorer et administrer le domaine géré.

1. Dans l’écran d’accueil, cliquez sur **Outils d’administration**. Les outils d’administration AD doivent apparaître comme installés sur la machine virtuelle.

	![Outils d’administration installés sur le serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Cliquez sur **Centre d’administration Active Directory**.

	![Centre d'administration Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Cliquez sur le nom de domaine figurant dans le volet de gauche (par exemple : contoso100.com) pour explorer le domaine. Vous pouvez remarquer deux conteneurs appelés « Ordinateurs AADDC » et « Utilisateurs AADDC », respectivement.

    ![ADAC - Affichage du domaine](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Cliquez sur le conteneur appelé **Utilisateurs AADDC** pour faire apparaître tous les utilisateurs et groupes qui appartiennent au domaine géré. Les comptes d’utilisateur et groupes de votre client Azure AD doivent apparaître dans ce conteneur. Remarque : dans cet exemple, un compte d’utilisateur associé à l’utilisateur « bob » et un groupe appelé « AAD DC Administrators » sont disponibles dans ce conteneur.

    ![ADAC - Utilisateurs du domaine](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Cliquez sur le conteneur **Ordinateurs AADDC** pour afficher les ordinateurs joints à ce domaine géré. Vous devez voir apparaître une entrée pour la machine virtuelle en cours, qui est jointe au domaine. Les comptes d’ordinateur de l’ensemble des ordinateurs joints au domaine géré par les services de domaine Azure AD apparaissent dans le conteneur « Ordinateurs AADDC ».

    ![ADAC - Ordinateurs joints au domaine](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<!---HONumber=AcomDC_0420_2016-->