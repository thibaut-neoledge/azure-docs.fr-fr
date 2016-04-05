<properties
	pageTitle="Ajouter des utilisateurs ou modifier les informations utilisateur dans Azure Active Directory | Microsoft Azure"
	description="Explique comment ajouter des utilisateurs ou modifier les informations utilisateur dans Azure Active Directory, y compris celles des utilisateurs externes et invités."
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
	ms.date="03/24/2016"
	ms.author="curtand;viviali"/>

# Ajout ou modification d’utilisateurs dans Azure Active Directory

Vous devez ajouter un compte au répertoire de votre client pour chaque utilisateur ayant besoin d’accéder à un service cloud Microsoft. Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur affecter des rôles à tout moment.

## Ajouter un utilisateur

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) avec un compte d’administrateur général pour le répertoire.
2. Cliquez sur **Active Directory**, puis sélectionnez le nom du répertoire de votre organisation.
3. Sélectionnez l’onglet **Utilisateurs** puis, dans la zone de commande, sélectionnez **Ajouter un utilisateur**.
4. Sur la page **Dites-nous en plus sur cet utilisateur**, sous **Type d’utilisateur**, sélectionnez l’une des options suivantes :

	- **Nouvel utilisateur dans votre organisation** : pour ajouter un nouveau compte d’utilisateur dans votre répertoire
	- **Utilisateur avec un compte Microsoft existant** : pour ajouter un compte consommateur Microsoft à votre répertoire (par exemple, un compte Outlook)
	- **Utilisateur dans un autre annuaire Microsoft Azure AD** : pour ajouter un compte d’utilisateur à votre répertoire à partir d’un autre répertoire Azure AD (remarque : vous devez être membre de l’autre répertoire pour y sélectionner un utilisateur)
	- **Utilisateurs dans les entreprises partenaires** : pour inviter et autoriser les utilisateurs des entreprises partenaires dans votre répertoire (Voir [Collaboration B2B Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md))


5. Selon le type d’utilisateur sélectionné, entrez un nom d’utilisateur, une adresse e-mail, ou chargez un fichier CSV spécifiant les adresses e-mail avec lesquelles les utilisateurs doivent se connecter.
6. Sur la page **Profil** d’utilisateur, entrez les nom et prénom de l’utilisateur, un nom convivial et un rôle d’utilisateur à partir de la liste **Rôles**. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md). Spécifiez si vous voulez **Activer l’authentification multifacteur**.
7. Sur la page **Obtenir un mot de passe temporaire**, sélectionnez **Créer**.

> [AZURE.IMPORTANT] Si votre organisation utilise plusieurs domaines, vous devez avoir connaissance des problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> - Vous pouvez ajouter des comptes d’utilisateurs avec le même nom d’utilisateur principal (UPN) sur plusieurs domaines si, par exemple, vous ajoutez d’abord geoffgrisso@contoso.onmicrosoft.com, puis geoffgrisso@contoso.com.
> - Vous ne devez pas ajouter geoffgrisso@contoso.com avant d’ajouter geoffgrisso@contoso.onmicrosoft.com. Le respect de cet ordre est extrêmement important et il peut être difficile de revenir en arrière.

## Modification des informations utilisateur

Vous pouvez modifier tous les attributs de l’utilisateur à l’exception des ID d’objet de l’utilisateur.

1. Ouvrez le répertoire de votre organisation.
2. Sélectionnez l’onglet **Utilisateurs**, puis sélectionnez le nom d’affichage de l’utilisateur que vous souhaitez modifier.
3. Apportez les modifications nécessaires, puis cliquez sur **Enregistrer**.

Si l’utilisateur que vous essayez de modifier est synchronisé avec votre service Active Directory local, un message d’erreur s’affiche et vous ne pouvez pas modifier les informations utilisateur en suivant cette procédure. Pour modifier l’utilisateur, servez-vous de vos outils de gestion Active Directory locaux.

## Réinitialiser le mot de passe d’un utilisateur

1. Ouvrez le répertoire de votre organisation.
2. Sélectionnez l’onglet **Utilisateurs**, puis sélectionnez le nom d’affichage de l’utilisateur que vous souhaitez modifier.
3. Dans la zone de commande, sélectionnez **Réinitialiser le mot de passe**.
4. Dans la boîte de dialogue de réinitialisation du mot de passe, cliquez sur **Réinitialiser**.
5. Cochez la case pour finaliser la réinitialisation du mot de passe.

## Ajouter des utilisateurs externes

Dans Azure AD, vous pouvez également ajouter des utilisateurs à un annuaire Azure AD avec un compte Microsoft, qu’ils proviennent d’un autre annuaire Azure AD auquel vous appartenez ou d’une entreprise partenaire, en chargeant un fichier CSV. Pour ajouter un utilisateur externe, ajoutez un utilisateur dans le portail. Pour **Type d’utilisateur**, sélectionnez **Utilisateur dans un autre annuaire Microsoft Azure AD** ou **Utilisateurs dans les entreprises partenaires**.

Les deux types d’utilisateurs sont issus d’un autre répertoire et sont ajoutés comme **utilisateurs externes**. Les utilisateurs externes peuvent collaborer avec les utilisateurs qui sont déjà dans un répertoire à l’aide de leur compte. Ils n’ont pas besoin d’ajouter de nouveaux comptes ni d’informations d’identification. Quand ils se connectent, les utilisateurs externes sont authentifiés par leur annuaire de base, et cette authentification fonctionne pour tout autre annuaire auquel ils ont été ajoutés.

## Gestion des utilisateurs externes et limitations

Lorsque vous ajoutez à votre annuaire un utilisateur provenant d’un autre annuaire, cet utilisateur est ajouté à votre annuaire en tant qu’utilisateur externe. Initialement, le nom d’affichage et le nom d’utilisateur sont copiés à partir de l’« annuaire de base » de l’utilisateur et appliqués à l’utilisateur externe dans votre annuaire. Ceux-ci, ainsi que les autres propriétés du compte d’utilisateur externe, sont dès lors totalement indépendants : si des modifications sont apportées à l’utilisateur dans son annuaire de base (modification du nom de l’utilisateur, ajout d’une fonction, etc.), celles-ci ne sont pas propagées au compte d’utilisateur externe dans votre annuaire.

Le seul lien entre les deux comptes est que l’utilisateur s’authentifie toujours auprès de l’annuaire de base ou à l’aide de son compte Microsoft. Ainsi, vous ne verrez jamais d’option pour réinitialiser le mot de passe ou activer l’authentification multifacteur pour un utilisateur externe : pour le moment, la stratégie d’authentification de l’annuaire de base ou du compte Microsoft est la seule évaluée quand l’utilisateur se connecte.

> [AZURE.NOTE]
Vous pouvez quand même désactiver l’utilisateur externe dans le répertoire afin de l’empêcher d’accéder à votre répertoire.

Si un utilisateur est supprimé de son annuaire de base ou s’il annule son compte Microsoft, l’utilisateur externe existe toujours dans votre annuaire. Cependant, l’utilisateur ne peut pas accéder aux ressources de votre répertoire étant donné qu’il ne peut plus s’authentifier avec son répertoire de base ni à l’aide de son compte Microsoft.

Voici les services auxquels peuvent avoir accès les utilisateurs externes Azure AD :

- **Portail Azure Classic** : permet aux utilisateurs externes administrant plusieurs répertoires de gérer chacun d’entre eux
- **SharePoint Online** : permet aux utilisateurs externes d’accéder aux ressources autorisées SharePoint Online si le partage externe est activé
- **Dynamics CRM** : permet aux utilisateurs externes d’accéder aux ressources autorisées Dynamics CRM s’ils sont sous licence via PowerShell

Voici les limitations connues des utilisateurs externes d’Azure AD :

- Les utilisateurs externes qui sont administrateurs ne peuvent pas ajouter des utilisateurs d’entreprises partenaires à des annuaires (collaboration B2B) en dehors de leur annuaire de base
- Les utilisateurs externes ne peuvent pas donner leur consentement aux applications mutualisées dans des annuaires en dehors de leur annuaire de base
- Pour l’instant, les utilisateurs externes ne peuvent pas accéder à Visual Studio Online*
- Pour l’instant, les utilisateurs externes ne peuvent pas accéder à PowerBI
- Les utilisateurs externes ne peuvent pas avoir de licences pour le portail Office

* Visual Studio Online autorise l’accès aux utilisateurs externes qui s’authentifient à l’aide de comptes Microsoft, mais pas aux utilisateurs externes qui s’authentifient à l’aide de comptes professionnels ou scolaires.

## Gestion des utilisateurs invités et limitations

Les comptes invités représentent les utilisateurs d’autres répertoires qui ont été invités dans votre répertoire pour accéder à une ressource spécifique comme une application, un document SharePoint Online ou une ressource Azure. L’attribut UserType (type d’utilisateur) d’un compte invité sur votre répertoire est défini sur « Invité ». L’attribut UserType des utilisateurs habituels (c’est-à-dire ceux qui sont membres de votre répertoire) est « Membre ».

Au sein de l’annuaire, les invités disposent de droits limités. Ces droits ont pour but de limiter leur capacité à découvrir des informations sur les autres utilisateurs de l’annuaire tout en leur permettant d’interagir avec les utilisateurs et les groupes associés aux ressources sur lesquelles ils travaillent. Les utilisateurs invités peuvent :

- Voir les autres utilisateurs et groupes associés à l’abonnement Azure auquel ils sont affectés
- Voir les membres des groupes auxquels ils appartiennent
- Rechercher d’autres utilisateurs dans le répertoire, à condition de connaître leur adresse e-mail complète
- Voir un ensemble d’attributs limité au nom d’affichage, à l’adresse e-mail, au nom d’utilisateur principal (UPN) et à la photo miniature des utilisateurs qu’ils recherchent
- Obtenir la liste des domaines vérifiés du répertoire client
- Donner leur consentement aux applications, en leur accordant le même accès qu’aux membres dans votre annuaire

## Définir les stratégies d’accès utilisateur

L'onglet **Configurer** d'un annuaire inclut des options de contrôle d'accès des utilisateurs externes. Ces options ne peuvent être modifiées que dans le portail Azure Classic par un administrateur général du répertoire (il n’existe aucune méthode Windows PowerShell ou API).

Pour ouvrir l’onglet **Configurer** dans le portail Azure Classic, sélectionnez **Active Directory**, puis le nom du répertoire.

![Onglet Configurer d’Azure Active Directory][1]

Vous pouvez ensuite modifier les options de contrôle d'accès des utilisateurs externes.

![][2]


## Étapes suivantes

- [Administration d’Azure AD](active-directory-administer.md)
- [Gestion des mots de passe dans Azure AD](active-directory-manage-passwords.md)
- [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0330_2016-->