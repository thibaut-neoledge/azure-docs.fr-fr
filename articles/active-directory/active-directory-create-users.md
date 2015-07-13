<properties 
	pageTitle="Création ou modification d’utilisateurs dans Azure AD" 
	description="Rubrique qui explique comment créer ou modifier des comptes d’utilisateurs dans Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Création ou modification d’utilisateurs dans Azure AD

Vous devez créer un compte pour chaque utilisateur ayant besoin d’accéder à un service cloud Microsoft. Vous pouvez également modifier des comptes d’utilisateurs ou les supprimer lorsqu’ils ne sont plus nécessaires. Par défaut, les utilisateurs ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez éventuellement leur affecter de telles autorisations.

## Créer un utilisateur

1. Cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Dans la page **Utilisateurs**, cliquez sur **Ajouter un utilisateur**.
3. Dans la page **Dites-nous en plus sur cet utilisateur**, pour **Type d’utilisateur**, sélectionnez l’une des options suivantes : 
	1. **Nouvel utilisateur dans votre organisation** : indique que vous souhaitez créer et gérer un compte d’utilisateur dans votre annuaire.
	2. **Utilisateur avec un compte Microsoft existant** : indique que vous souhaitez ajouter un compte Microsoft existant à votre client d’annuaire afin de collaborer sur des ressources Microsoft Azure avec un co-administrateur qui accède à Microsoft Azure avec un compte Microsoft.	
	3. **Utilisateur dans un autre annuaire Azure AD** : indique que vous souhaitez ajouter un compte d’utilisateur à votre annuaire à partir d’un autre annuaire Azure AD. Pour sélectionner un utilisateur appartenant à un autre annuaire, vous devez être membre de celui-ci. 
4. Selon l’option sélectionnée, tapez un nom d’utilisateur ou un nom de compte Microsoft à l’aide duquel cet utilisateur se connectera.
5. Dans la page **Profil** d’utilisateur, entrez les nom et prénom de l’utilisateur, un nom convivial et un rôle d’utilisateur à partir du menu déroulant Rôles. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md). Spécifiez si vous voulez **Activer l’authentification multifacteur**.
6. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.

Si votre organisation utilise plusieurs domaines, vous devez avoir connaissance des problèmes suivants lorsque vous créez un compte d’utilisateur :

- Vous pouvez créer des comptes d’utilisateurs avec le même nom d’utilisateur principal (UPN) sur plusieurs domaines si, par exemple, vous créez d’abord geoffgrisso@contoso.onmicrosoft.com, puis geoffgrisso@contoso.com.
- Vous ne pouvez pas créer en premier geoffgrisso@contoso.com@contoso.com, puis geoffgrisso@contoso.onmicrosoft.com.

## Modifier un utilisateur

Si l’utilisateur que vous essayez de modifier est synchronisé avec votre service Active Directory, un message d’erreur s’affiche et vous ne pouvez pas modifier l’utilisateur à l’aide de cette procédure. Pour modifier l’utilisateur, utilisez vos outils de gestion Active Directory locaux.
 
Pour modifier un utilisateur dans le portail de gestion Azure :

1. Cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Dans la page **Utilisateurs**, cliquez sur le nom complet de l’utilisateur à modifier.
3. Apportez les modifications nécessaires, puis cliquez sur **Enregistrer**.

## Réinitialiser le mot de passe de l’utilisateur

1. Cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Dans la page **Utilisateurs**, cliquez sur le nom complet de l’utilisateur à modifier.
3. En bas de la fenêtre du portail de gestion, cliquez sur **Réinitialiser le mot de passe**.
4. Dans la boîte de dialogue de réinitialisation du mot de passe, cliquez sur **Réinitialiser**.
5. Cliquez sur la case à cocher pour confirmer que le mot de passe a été réinitialisé.

## Créer un utilisateur externe

Dans Azure AD, vous pouvez également ajouter des utilisateurs à un annuaire Azure AD à partir d’un autre annuaire Azure AD ou un utilisateur avec un compte Microsoft. Un utilisateur peut être membre de 20 annuaires différents.

Les utilisateurs qui sont ajoutés à partir d’un autre annuaire sont des utilisateurs externes. Les utilisateurs externes peuvent collaborer avec des utilisateurs qui existent déjà dans un annuaire, comme dans un environnement de test, sans avoir à se connecter avec de nouveaux comptes et informations d’identification. Les utilisateurs externes sont authentifiés par leur annuaire principal lorsqu’ils se connectent et cette authentification fonctionne pour tous les autres annuaires dont ils sont membres.

Pour créer un utilisateur externe, créez un utilisateur dans le portail et pour **Type d’utilisateur**, sélectionnez **Utilisateur dans un autre annuaire Azure AD**.

## Gestion des utilisateurs externes et limitations

Lorsque vous ajoutez l’utilisateur d’un annuaire dans un nouvel annuaire, cet utilisateur est un utilisateur externe dans le nouvel annuaire. Initialement, le nom complet et le nom d’utilisateur sont copiés à partir de l’« annuaire principal » de l’utilisateur et appliqués à l’utilisateur externe dans l’autre annuaire. Ceux-ci, ainsi que les autres propriétés de l’objet utilisateur externe, sont dès lors totalement indépendants : si vous apportez des modifications à l’utilisateur dans l’annuaire principal (modification du nom de l’utilisateur, ajout d’une fonction, etc.), celles-ci ne sont pas propagées au compte de l’utilisateur externe dans l’autre annuaire.

Le seul lien entre les deux objets est que l’utilisateur s’authentifie toujours auprès de l’annuaire principal ou à l’aide de son compte Microsoft. Par conséquent, vous ne verrez jamais d’option pour réinitialiser le mot de passe ou activer l’authentification multifacteur pour un compte d’utilisateur externe : pour le moment, la stratégie d’authentification de l’annuaire principal ou du compte Microsoft est la seule évaluée quand l’utilisateur se connecte.

> [AZURE.NOTE]Vous pouvez quand même désactiver l’utilisateur externe dans l’annuaire afin de l’empêcher d’accéder à votre annuaire.

Si un utilisateur est supprimé de son annuaire principal ou s’il annule son compte Microsoft, l’utilisateur externe existe toujours dans l’annuaire. Cependant, l’utilisateur ne peut pas accéder aux ressources de l’annuaire étant donné qu’il ne peut plus s’authentifier auprès de son annuaire principal ni à l’aide de son compte Microsoft.

Un utilisateur administrateur de plusieurs annuaires peut gérer ceux-ci individuellement dans le portail de gestion Azure. Cependant, d’autres applications telles qu’Office 365 ne permettent pas pour le moment d’affecter des services ou d’accéder à des services en tant qu’utilisateur externe dans un autre annuaire. À partir de maintenant, nous fournirons aux développeurs des recommandations sur le fonctionnement de leurs applications avec les utilisateurs membres de plusieurs annuaires.

Certaines restrictions existent actuellement dans le sens où un administrateur peut seulement donner son consentement à une application mutualisée dans son annuaire principal et être configuré pour les applications SaaS et l’authentification unique (SSO) via le panneau d’accès de son annuaire principal. Les utilisateurs de compte Microsoft sont soumis aux mêmes restrictions étant donné qu’ils ne peuvent pas donner leur consentement à une application mutualisée ou utiliser le panneau d’accès pour le moment.

## Invités

Un **invité** est un utilisateur de votre annuaire dont le type d’utilisateur est défini sur « Invité ». Le type d’utilisateur des utilisateurs habituels est « Membre », ce qui permet d’indiquer qu’ils sont membres de votre annuaire. Vous créez des invités lorsque vous partagez une ressource avec une personne externe par rapport à votre annuaire, par exemple quand vous ajoutez un compte Microsoft à votre abonnement Azure ou quand vous partagez un document dans SharePoint avec un utilisateur externe.

Au sein de l’annuaire, les invités disposent de droits limités. Ces droits ont pour but de limiter leur capacité à découvrir des informations sur les autres utilisateurs de l’annuaire tout en leur permettant d’interagir avec les utilisateurs et les groupes associés aux ressources sur lesquelles ils travaillent. Par exemple, un invité affecté à un abonnement Azure est en mesure de voir les autres utilisateurs et groupes associés à cet abonnement. Il peut également trouver les autres utilisateurs de l’annuaire qui doivent être autorisés à accéder à l’abonnement, à condition de connaître leur adresse de messagerie. Un invité n’est en mesure de voir qu’un nombre limité de propriétés des autres utilisateurs, à savoir le nom d’affichage, l’adresse de messagerie, le nom d’utilisateur principal (UPN) et la photo miniature.

## Configurer les stratégies d’accès utilisateur

L'onglet **Configurer** d'un annuaire inclut des options de contrôle d'accès des utilisateurs externes. Ces options peuvent être modifiées uniquement dans l'interface utilisateur (il n'existe aucune méthode Windows PowerShell ou API) dans le portail Azure complet par un administrateur global de l'annuaire. Pour ouvrir l’onglet **Configurer** dans le portail Azure, cliquez sur **Active Directory**, puis sur le nom de l’annuaire.

![][1]

Vous pouvez ensuite modifier les options de contrôle d'accès des utilisateurs externes.

![][2]

Par défaut, les invités ne peuvent pas énumérer les éléments de contenu de l'annuaire ; ils ne peuvent donc pas afficher les utilisateurs ou groupes dans la **Liste des membres**. Ils peuvent rechercher un utilisateur en entrant son adresse e-mail complète, puis accorder l'accès. Voici l'ensemble des restrictions par défaut pour les invités :

- Ils ne peuvent pas énumérer les utilisateurs ou groupes dans l'annuaire.
- Ils ne peuvent accéder qu'à certaines informations limitées d'un utilisateur s'ils ne connaissent pas son adresse e-mail.
- Ils peuvent accéder à certaines informations limitées d'un groupe s'ils connaissent son nom.

La capacité des invités à n'accéder qu'à certaines informations limitées d'un utilisateur ou d'un groupe les autorise à inviter d'autres personnes et afficher certains détails des personnes avec qui ils collaborent.

## Étapes suivantes

- [Administration d’Azure AD](active-directory-administer.md)
- [Gestion des mots de passe dans Azure AD](active-directory-manage-passwords.md)
- [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
 

<!---HONumber=62-->