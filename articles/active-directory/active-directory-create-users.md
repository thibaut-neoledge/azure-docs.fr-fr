<properties
	pageTitle="Création ou modification d’utilisateurs dans Azure Active Directory | Microsoft Azure"
	description="Une rubrique expliquant comment créer ou modifier des comptes d’utilisateurs dans Azure Active Directory."
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
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="curtand;viviali"/>

# Création ou modification d’utilisateurs dans Azure AD

Vous devez créer un compte pour chaque utilisateur ayant besoin d’accéder à un service cloud Microsoft. Vous pouvez également modifier des comptes d’utilisateurs ou les supprimer lorsqu’ils ne sont plus nécessaires. Par défaut, les utilisateurs ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez éventuellement leur affecter de telles autorisations.

## Créer un utilisateur

1. Cliquez sur **Active Directory**, puis sélectionnez le nom du répertoire de votre organisation.
2. Dans la page **Utilisateurs**, cliquez sur **Ajouter un utilisateur**.
3. Dans la page **Dites-nous en plus sur cet utilisateur**, pour **Type d’utilisateur**, sélectionnez l’une des options suivantes :

	- **Nouvel utilisateur dans votre organisation** : pour créer un nouveau compte d’utilisateur dans votre répertoire
	- **Utilisateur avec un compte Microsoft existant** : pour ajouter un compte consommateur Microsoft à votre répertoire (par exemple, un compte Outlook)
	- **Utilisateur dans un autre répertoire Azure AD** : pour ajouter un compte d’utilisateur à votre répertoire à partir d’un autre répertoire Azure AD (Remarque : vous devez être membre de l’autre répertoire pour y sélectionner un utilisateur)
	- **Utilisateurs d’entreprises partenaires** : pour inviter et autoriser les utilisateurs d’entreprises partenaires dans votre répertoire ([Voir Collaboration B2B Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md))


4. Selon l’option sélectionnée, entrez un nom d’utilisateur, une adresse de messagerie, ou téléchargez un fichier CSV pour les utilisateurs partenaires.
5. Dans la page **Profil** d’utilisateur, entrez les nom et prénom de l’utilisateur, un nom convivial et un rôle d’utilisateur à partir du menu déroulant Rôles. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md). Spécifiez si vous voulez **Activer l’authentification multifacteur**.
6. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.

Si votre organisation utilise plusieurs domaines, vous devez avoir connaissance des problèmes suivants lorsque vous créez un compte d’utilisateur :

- Vous pouvez créer des comptes d’utilisateurs avec le même nom d’utilisateur principal (UPN) sur plusieurs domaines si, par exemple, vous créez d’abord geoffgrisso@contoso.onmicrosoft.com, puis geoffgrisso@contoso.com.
- Vous ne pouvez pas créer en premier geoffgrisso@contoso.com@contoso.com, puis geoffgrisso@contoso.onmicrosoft.com.

## Modifier un utilisateur

Pour modifier un utilisateur dans le portail Azure Classic :

1. Cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Sur la page **Utilisateurs**, cliquez sur le nom d’affichage de l’utilisateur à modifier.
3. Apportez les modifications nécessaires, puis cliquez sur **Enregistrer**.

Si l’utilisateur que vous essayez de modifier est synchronisé avec votre service Active Directory, un message d’erreur s’affiche et vous ne pouvez pas modifier l’utilisateur à l’aide de cette procédure. Pour modifier l’utilisateur, utilisez vos outils de gestion Active Directory locaux.

## Réinitialiser le mot de passe de l’utilisateur

1. Cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Sur la page **Utilisateurs**, cliquez sur le nom d’affichage de l’utilisateur à modifier.
3. En bas de la fenêtre du portail de gestion, cliquez sur **Réinitialiser le mot de passe**.
4. Dans la boîte de dialogue de réinitialisation du mot de passe, cliquez sur **Réinitialiser**.
5. Cliquez sur la case à cocher pour confirmer que le mot de passe a été réinitialisé.

## Créer des utilisateurs externes

Dans Azure AD, vous pouvez également ajouter des utilisateurs à un répertoire Azure AD avec un compte Microsoft, à partir d’un autre répertoire Azure AD auquel vous appartenez, ou depuis des sociétés partenaires en téléchargeant un fichier CSV. Pour créer un utilisateur externe, créez un utilisateur dans le portail et pour **Type d’utilisateur**, sélectionnez **Utilisateur dans un autre répertoire Azure AD** ou **Utilisateurs d’entreprises partenaires**.

Dans les deux cas, les utilisateurs créés sont issus d’un autre répertoire et sont créés en tant qu’**utilisateurs externes**. Les utilisateurs externes peuvent collaborer avec les utilisateurs qui existent déjà dans un répertoire à l’aide de leur compte uniquement, sans avoir à créer de nouveaux comptes ni d’informations d’identification. Lorsqu’ils se connectent, les utilisateurs externes sont authentifiés par leur répertoire de base, et cette authentification fonctionne pour tous les autres répertoires dont ils font partie.

## Gestion des utilisateurs externes et limitations

Lorsque vous ajoutez à votre répertoire un utilisateur provenant d’un autre répertoire, cet utilisateur est ajouté à votre répertoire en tant qu’utilisateur externe. Initialement, le nom d’affichage et le nom d’utilisateur sont copiés à partir du « répertoire de base » de l’utilisateur et appliqués à l’utilisateur externe dans votre répertoire. Ceux-ci, ainsi que les autres propriétés de l’objet utilisateur externe, sont dès lors totalement indépendants : si des modifications sont apportées à l’utilisateur dans son répertoire de base (modification du nom de l’utilisateur, ajout d’une fonction, etc.), celles-ci ne sont pas propagées à l’objet utilisateur externe dans votre répertoire.

Le seul lien entre les deux objets est que l’utilisateur s’authentifie toujours auprès du répertoire de base ou à l’aide de son compte Microsoft. Par conséquent, vous ne verrez jamais d’option pour réinitialiser le mot de passe ou activer l’authentification multifacteur pour un utilisateur externe : pour le moment, la stratégie d’authentification du répertoire de base ou du compte Microsoft est la seule évaluée quand l’utilisateur se connecte.

> [AZURE.NOTE]
Vous pouvez quand même désactiver l’utilisateur externe dans l’annuaire afin de l’empêcher d’accéder à votre annuaire.

Si un utilisateur est supprimé de son répertoire de base ou s’il annule son compte Microsoft, l’utilisateur externe existe toujours dans votre répertoire. Cependant, l’utilisateur ne peut pas accéder aux ressources de votre répertoire étant donné qu’il ne peut plus s’authentifier auprès de son répertoire de base ni à l’aide de son compte Microsoft.

Voici les services auxquels peuvent avoir accès les utilisateurs externes Azure AD :

- Portail Azure Classic : permet aux utilisateurs externes administrant plusieurs répertoires de gérer chacun d’entre eux
- SharePoint Online : permet aux utilisateurs externes d’accéder aux ressources autorisées SharePoint Online si le partage externe est activé
- Dynamics CRM : permet aux utilisateurs externes d’accéder aux ressources autorisées Dynamics CRM s’ils sont sous licence via PowerShell

Voici les limitations connues des utilisateurs externes d’Azure AD :

- les utilisateurs externes qui sont administrateurs ne peuvent pas ajouter des utilisateurs d’entreprises partenaires à des répertoires (B2B) en dehors de leur répertoire de base
- les utilisateurs externes ne peuvent pas donner leur consentement aux applications mutualisées dans des répertoires en dehors de leur répertoire de base
- pour l’instant, les utilisateurs externes ne peuvent pas accéder à Visual Studio Online
- pour l’instant, les utilisateurs externes ne peuvent pas accéder à PowerBI
- les utilisateurs externes ne peuvent pas avoir de licences pour le portail Office

## Invités

Un **invité** est un utilisateur de votre répertoire dont l’attribut UserType (type d’utilisateur) est défini sur « Invité ». L’attribut UserType des utilisateurs habituels est « Membre », ce qui indique qu’ils sont membres de votre répertoire. Les invités représentent les utilisateurs d’autres répertoires qui ont été invités dans votre répertoire pour accéder à une ressource spécifique comme une application, un document SharePoint ou une ressource Azure.

Au sein de l’annuaire, les invités disposent de droits limités. Ces droits ont pour but de limiter leur capacité à découvrir des informations sur les autres utilisateurs de l’annuaire tout en leur permettant d’interagir avec les utilisateurs et les groupes associés aux ressources sur lesquelles ils travaillent. Les invités ont accès aux fonctionnalités suivantes :

- voir les autres utilisateurs et groupes associés à l’abonnement Azure auquel ils sont affectés
- voir les membres des groupes auxquels ils appartiennent
- rechercher d’autres utilisateurs dans le répertoire à condition de connaître leur adresse électronique complète
- voir un ensemble d’attributs limité au nom complet, à l’adresse de messagerie, au nom d’utilisateur principal (UPN) et à la photo miniature des utilisateurs qu’ils recherchent
- obtenir la liste des domaines vérifiés du client
- donner leur consentement aux applications, en leur accordant le même accès que dans votre répertoire

## Configurer les stratégies d’accès utilisateur

L'onglet **Configurer** d'un annuaire inclut des options de contrôle d'accès des utilisateurs externes. Ces options ne peuvent être modifiées que dans l’interface utilisateur (il n’existe aucune méthode Windows PowerShell ou API) dans le portail Azure Classic et par un administrateur général du répertoire. Pour ouvrir l’onglet **Configurer** dans le portail Azure Classic, cliquez sur **Active Directory**, puis sur le nom de l’annuaire.

![][1]

Vous pouvez ensuite modifier les options de contrôle d'accès des utilisateurs externes.

![][2]


## Étapes suivantes

- [Administration d’Azure AD](active-directory-administer.md)
- [Gestion des mots de passe dans Azure AD](active-directory-manage-passwords.md)
- [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0218_2016-->