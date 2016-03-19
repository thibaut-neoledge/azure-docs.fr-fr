<properties
	pageTitle="Attribution de rôles d’administrateur dans Azure Active Directory | Microsoft Azure"
	description="Décrit les rôles d’administrateur disponibles avec Azure Active Directory et leur utilisation."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="curtand"/>

# Attribution de rôles d’administrateur dans Azure Active Directory (Azure AD)

Selon la taille de votre entreprise, vous pouvez désigner plusieurs administrateurs, qui remplissent différentes fonctions. Ces administrateurs ont accès à plusieurs fonctionnalités du portail Azure ou du portail Azure Classic et, selon leur rôle, peuvent créer ou modifier des utilisateurs, attribuer des rôles d’administrateur à d’autres personnes, réinitialiser les mots de passe d’utilisateur, gérer les licences utilisateur et les domaines, etc.

Il est important de comprendre qu’un utilisateur à qui un rôle d’administrateur est affecté a les mêmes autorisations sur tous les services cloud auxquels votre organisation est abonnée, peu importe que vous attribuiez le rôle dans le portail Office 365 ou dans le portail Azure Classic ou encore à l’aide du module Azure AD pour Windows PowerShell.

Les rôles d’administrateur disponibles sont les suivants :

- **Administrateur de facturation**: effectue des achats, gère les abonnements, gère les tickets de support et surveille l’état des services.

- **Administrateur général**: peut accéder à toutes les fonctionnalités d’administration. La personne qui s’inscrit pour le compte Azure devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux.

- **Administrateur de mots de passe**: réinitialise les mots de passe, gère les demandes de service et surveille l’état des services. Les administrateurs de mots de passe peuvent réinitialiser uniquement les mots de passe des utilisateurs et des autres administrateurs de mots de passe.

- **Administrateur de services fédérés**: gère les demandes de service et surveille l’état des services.
> [AZURE.NOTE]
> Pour assigner le rôle d’administrateur du service à un utilisateur, l’administrateur général doit d’abord assigner des autorisations administratives à l’utilisateur du service, comme Exchange Online, puis lui assigner le rôle d’administrateur du service dans le portail Azure Classic.

- **Administrateur d’utilisateurs**: réinitialise les mots de passe, surveille l’état des services et gère les comptes d’utilisateur, les groupes d’utilisateurs et les demandes de service. Certaines limitations s’appliquent aux autorisations des administrateurs d’utilisateurs. Par exemple, ils ne peuvent pas supprimer un administrateur général, ni créer d’autres administrateurs. Ils ne peuvent pas non plus réinitialiser les mots de passe des administrateurs de facturation, généraux et de services fédérés.

## Autorisations des administrateurs

### Administrateur de facturation

Peut | Ne peut pas
------------- | -------------
<p>Afficher les informations de la société et des utilisateurs</p><p>Gérer les tickets de support Office</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p> | <p>Réinitialiser les mots de passe d’utilisateur</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et des groupes et gérer les licences utilisateur</p><p>Gérer les domaines</p><p>Gérer les informations de la société</p><p>Déléguer des rôles d’administrateur à d’autres personnes</p><p>Utiliser la synchronisation d’annuaires</p>

### Administrateur général

Peut | Ne peut pas
------------- | -------------
<p>Afficher les informations de la société et des utilisateurs</p><p>Gérer les tickets de support Office</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p> <p>Réinitialiser les mots de passe d’utilisateur</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et des groupes et gérer les licences utilisateur</p><p>Gérer les domaines</p><p>Gérer les informations de la société</p><p>Déléguer des rôles d’administrateur à d’autres personnes</p><p>Utiliser la synchronisation d’annuaires</p><p>Activer ou désactiver l’authentification multifacteur</p> | N/A

### Administrateur de mots de passe

Peut | Ne peut pas
------------- | -------------
<p>Afficher les informations de la société et des utilisateurs</p><p>Gérer les tickets de support Office</p><p>Réinitialiser les mots de passe d’utilisateur</p> | <p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et des groupes et gérer les licences utilisateur</p><p>Gérer les domaines</p><p>Gérer les informations de la société</p><p>Déléguer des rôles d’administrateur à d’autres personnes</p><p>Utiliser la synchronisation d’annuaires</p>

### Administrateur de services fédérés

Peut | Ne peut pas
------------- | -------------
<p>Afficher les informations de la société et des utilisateurs</p><p>Gérer les tickets de support Office</p> | <p>Réinitialiser les mots de passe d’utilisateur</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et des groupes et gérer les licences utilisateur</p><p>Gérer les domaines</p><p>Gérer les informations de la société</p><p>Déléguer des rôles d’administrateur à d’autres personnes</p><p>Utiliser la synchronisation d’annuaires</p>

### Administrateur d’utilisateurs

Peut | Ne peut pas
------------- | -------------
<p>Afficher les informations de la société et des utilisateurs</p><p>Gérer les tickets de support Office</p><p>Réinitialiser les mots de passe d’utilisateur, avec certaines limitations Il lui est impossible de réinitialiser les mots de passe des administrateurs de facturation, généraux et de services fédérés.</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et des groupes et gérer les licences utilisateur, avec certaines limitations. Il lui est impossible de supprimer un administrateur général ou de créer d’autres administrateurs.</p> | <p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Gérer les domaines</p><p>Gérer les informations de la société</p><p>Déléguer des rôles d’administrateur à d’autres personnes</p><p>Utiliser la synchronisation d’annuaires</p><p>Activer ou désactiver l’authentification multifacteur</p>

## Informations sur le rôle d’administrateur général

L’administrateur général a accès à toutes les fonctionnalités d’administration. Par défaut, le rôle d’administrateur général de l’annuaire est affecté à la personne qui souscrit un abonnement Azure. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur.

## Attribution ou suppression de rôles d’administrateur

1. Dans le portail Azure Classic, cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.

2. Sur la page **Utilisateurs**, cliquez sur le nom d’affichage de l’utilisateur à modifier.

3. Dans la liste **Rôle organisationnel**, sélectionnez le rôle d’administrateur que vous souhaitez attribuer à cet utilisateur, ou sélectionnez **Utilisateur** pour supprimer un rôle d’administrateur existant.

4. Dans le champ **Autre adresse de messagerie**, tapez une adresse électronique. Cette adresse électronique est utilisée pour les notifications importantes, notamment la réinitialisation automatique de mot de passe. L’utilisateur doit donc être en mesure d’accéder à ce compte, qu’il ait ou non accès à Azure.

5. Sélectionnez **Autoriser** ou **Bloquer** pour indiquer si l’utilisateur est autorisé ou non à se connecter aux services et à y accéder.

6. Spécifiez un lieu en le sélectionnant dans la liste déroulante **Lieu d’utilisation**.

7. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

## Étapes suivantes

- Pour plus d’informations sur la façon de modifier les administrateurs pour un abonnement Azure, voir [Comment ajouter ou modifier les rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md)

- Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)

- Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Gestion des utilisateurs](active-directory-create-users.md)

- [Gestion des mots de passe](active-directory-manage-passwords.md)

- [Gestion des groupes](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0224_2016-->