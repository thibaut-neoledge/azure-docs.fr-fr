<properties
	pageTitle="Version préliminaire des services Azure Active Directory Domain : guide d’administration | Microsoft Azure"
	description="Créer une UO sur des domaines gérés par les services de domaine Azure Active Directory"
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
	ms.date="04/08/2016"
	ms.author="maheshu"/>

# Créer une UO sur un domaine géré par les services de domaine Azure Active Directory
Les domaines gérés par les services de domaine Azure Active Directory incluent deux conteneurs intégrés, appelés « Ordinateurs AADDC » et « Utilisateurs AADDC », respectivement. Le conteneur « Ordinateurs AADDC » inclut des objets ordinateur associés à tous les ordinateurs qui sont joints au domaine géré. Le conteneur « Utilisateurs AADDC » comprend les utilisateurs et les groupes du client Azure AD. Parfois, il peut être nécessaire de créer des comptes de service sur le domaine géré, afin de déployer des charges de travail. Pour cela, vous pouvez créer une unité d’organisation (UO) personnalisée dans le domaine géré, puis générer des comptes de service dans cette UO. Cet article vous explique comment créer une UO dans votre domaine géré.


## Installer les outils d’administration Active Directory sur une machine virtuelle jointe à un domaine à des fins de gestion à distance
Vous pouvez administrer à distance les domaines gérés par les services de domaine Azure Active Directory (AD) par l’intermédiaire des outils d’administration familiers d’Active Directory, par exemple le Centre d’administration Active Directory (ADAC, Active Directory Administrative Center) ou AD PowerShell. Les administrateurs clients ne disposent pas des privilèges permettant la connexion aux contrôleurs de domaine sur le domaine géré, via le Bureau à distance. Pour administrer le domaine géré, installez les outils d’administration Active Directory sur une machine virtuelle jointe au domaine géré. Pour savoir comment faire, voir [Administer an Azure AD Domain Services managed domain (Administrer un domaine géré par les services de domaine Azure AD)](active-directory-ds-admin-guide-administer-domain.md).

## Créer une UO sur le domaine géré
Maintenant que les outils d’administration AD sont installés sur la machine virtuelle jointe au domaine, nous pouvons les utiliser pour créer une unité d’organisation sur le domaine géré. Procédez comme suit.

> [AZURE.NOTE] Seuls les membres du groupe « AAD DC Administrators » disposent des privilèges requis pour créer une nouvelle UO. Veillez à effectuer les étapes suivantes en tant qu’utilisateur appartenant à ce groupe.

1. Dans l’écran d’accueil, cliquez sur **Outils d’administration**. Les outils d’administration AD doivent apparaître comme installés sur la machine virtuelle.

	![Outils d’administration installés sur le serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Cliquez sur **Centre d’administration Active Directory**.

	![Centre d'administration Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Cliquez sur le nom de domaine figurant dans le volet de gauche (par exemple : contoso100.com) pour afficher le domaine.

    ![ADAC - Affichage du domaine](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. Sur le volet **Tâches** affiché à droite, cliquez sur l’option **Nouveau** figurant sous le nœud du nom de domaine. Dans cet exemple, nous allons cliquer sur l’option **Nouveau** figurant sous le nœud « contoso100(local) » dans le volet **Tâches** affiché à droite, comme indiqué ci-dessous.

    ![ADAC - Nouvelle unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Vous devez voir apparaître l’option permettant de créer une UO. Cliquez sur **Unité d’organisation** pour afficher la boîte de dialogue **Créer une unité d’organisation**.

6. Dans la boîte de dialogue **Créer une unité d’organisation**, indiquez une valeur dans le champ **Nom** de l’UO. Décrivez brièvement l’UO. Vous pouvez également renseigner le champ **Gérée par** de l’UO. Cliquez sur **OK** pour créer l’UO.

    ![ADAC - Boîte de dialogue Créer une unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. L’unité d’organisation créée doit désormais s’afficher dans le centre ADAC.

    ![ADAC - Unité d’organisation créée](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## Sécurité/autorisations associées aux nouvelles unités d’organisation
Par défaut, l’utilisateur (membre du groupe « AAD DC Administrators ») ayant créé l’UO bénéficie de privilèges d’administration (contrôle total) concernant l’unité d’organisation. L’utilisateur peut ensuite accorder des privilèges à d’autres utilisateurs ou au groupe « AAD DC Administrators », au besoin. Comme indiqué dans la capture d’écran ci-dessous, l’utilisateur bob@domainservicespreview.onmicrosoft.com, qui a créé la nouvelle UI « MyCustomOU », bénéficie d’un contrôle absolu sur cette dernière.

 ![ADAC - Sécurité de la nouvelle UO](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## Remarques sur l’administration des unités d’organisation (UO) personnalisées
Maintenant que vous avez créé une UO personnalisée, vous pouvez créer des utilisateurs, des groupes, des ordinateurs et des comptes de service dans cette dernière. Vous ne pourrez pas déplacer des utilisateurs ou des groupes de l’UO Utilisateurs AADDC vers des UO personnalisées.

> [AZURE.WARNING] Les comptes de service, groupes, objets ordinateur et comptes d’utilisateur que vous créez dans un UO personnalisée ne seront pas disponible sur votre client Azure AD. En d’autres termes, ces objets ne s’afficheront pas via l’API Azure AD Graph ou dans l’interface utilisateur Azure AD. Ces objets seront uniquement disponibles dans votre domaine géré par les services de domaine Azure AD.


## Contenu connexe

- [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)

- [Centre d’administration Active Directory : Prise en main](https://technet.microsoft.com/library/dd560651.aspx)

- [Guide pas à pas des comptes de service (éventuellement en anglais)](https://technet.microsoft.com/library/dd548356.aspx)

<!---HONumber=AcomDC_0420_2016-->