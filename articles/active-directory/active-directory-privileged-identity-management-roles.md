<properties
   pageTitle="Rôles dans PIM | Microsoft Azure"
   description="Découvrez les rôles utilisés pour les identités dotées de privilèges avec l’extension Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Rôles dans Azure AD Privileged Identity Management

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Vous pouvez affecter aux utilisateurs de votre organisation différents rôles administratifs dans Azure AD. Ces affectations de rôles contrôlent quelles tâches, telles que l’ajout/la suppression d’utilisateurs ou la modification des paramètres de service, les utilisateurs sont autorisés à effectuer sur Azure AD, Office 365 et autres services Microsoft Online Services et applications connectées.

Un administrateur général peut mettre à jour les utilisateurs **définitivement** affectés aux rôles dans Azure AD à l’aide des applets de commande PowerShell, telles que `Add-MsolRoleMember` et `Remove-MsolRoleMember`, ou via le portail classique, comme décrit à la rubrique [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

Azure AD Privileged Identity Management (PIM) gère les stratégies pour l’accès privilégié des utilisateurs dans Azure AD. PIM affecte les utilisateurs à un ou plusieurs rôles dans Azure AD, et ces affectations peuvent être permanentes ou temporaires. Lorsqu’un utilisateur est définitivement affecté à un rôle ou qu’il active une affectation de rôle temporaire, il peut alors gérer Azure Active Directory, Office 365 et d’autres applications avec les autorisations attribuées aux rôles.


## Rôles gérés dans PIM

Privileged Identity Management vous permet d’affecter des utilisateurs à des rôles d’administrateur courants, notamment :


- L’**administrateur général** (également connu en tant qu’administrateur d’entreprise) a accès à toutes les fonctionnalités d’administration. Vous pouvez avoir plus d’un administrateur général dans votre organisation. La personne qui s’inscrit pour acheter Office 365 devient automatiquement un administrateur général.
- L’**administrateur de facturation** effectue des achats, gère les abonnements, gère les tickets de support et surveille l’état des services.
- L’**administrateur de mots de passe** réinitialise les mots de passe, gère les demandes de service et surveille l’état des services. Les administrateurs de mot de passe sont limités à la réinitialisation des mots de passe des utilisateurs.
- L’**administrateur de services fédérés** gère les demandes de service et surveille l’état des services.

  > [AZURE.NOTE] Si vous utilisez Office 365, avant d’affecter le rôle d’administrateur de service à un utilisateur, commencez par affecter les autorisations d’administrateur à un service, comme Exchange Online.

- L’**administrateur de gestion des utilisateurs** réinitialise les mots de passe, surveille l’état des services et gère les comptes d’utilisateur, les groupes d’utilisateurs et les demandes de service. L’administrateur de gestion des utilisateurs ne peut pas supprimer un administrateur général, créer d’autres rôles d’administrateur ou réinitialiser des mots de passe pour les administrateurs de facturation, généraux et du service.
- L’**administrateur Exchange** dispose d’un accès administratif à Exchange Online via le Centre d’administration Exchange (EAC) et peut effectuer presque n’importe quelle tâche dans Exchange Online.
- L’**administrateur SharePoint** dispose d’un accès administratif à SharePoint Online via le Centre d’administration SharePoint Online et peut effectuer presque n’importe quelle tâche dans SharePoint Online.
- L’**administrateur Skype Entreprise** dispose d’un accès administratif à Skype Entreprise via le Centre d’administration Skype Entreprise et peut effectuer presque n’importe quelle tâche dans Skype Entreprise Online.

Consultez les articles suivants pour plus d’informations sur l’[attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md) et sur l’[attribution de rôles d’administrateur dans Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


À partir de PIM, vous pouvez [affecter temporairement ces rôles à un utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md) afin qu’il puisse [activer le rôle si nécessaire](active-directory-privileged-identity-management-how-to-activate-role.md).

Si vous souhaitez autoriser un autre utilisateur à effectuer la gestion dans PIM, les rôles que requiert PIM pour cet utilisateur sont décrits dans [Comment accorder l’accès à PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Rôles non gérés dans PIM

Les rôles dans Exchange Online ou SharePoint Online, à l’exception de ceux mentionnés ci-dessus, ne sont pas représentés dans Azure AD et par conséquent, ne sont pas visibles dans PIM. Pour plus d’informations sur la modification des affectations de rôles affinées dans ces services Office 365, voir [Autorisations dans Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Les groupes de ressources et les abonnements Azure ne sont pas représentés non plus dans Azure AD. Pour gérer les abonnements Azure, voir [Comment ajouter ou modifier des rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md) et, pour plus d’informations sur Azure RBAC, voir [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## Rôles d’utilisateur et connexion
Pour certains services et applications Microsoft, l’affectation d’un utilisateur à un rôle peut ne pas suffire pour permettre à cet utilisateur d’être un administrateur.

L’accès au portail Azure Classic nécessite que l’utilisateur soit un administrateur de service ou un coadministrateur sur un abonnement Azure, même si l’utilisateur n’a pas besoin de gérer les abonnements Azure. Par exemple, pour gérer les paramètres de configuration pour Azure AD dans le portail classique, un utilisateur doit être administrateur général dans Azure AD et coadministrateur des abonnements sur un abonnement Azure. Pour savoir comment ajouter des utilisateurs aux abonnements Azure, voir [Ajout ou modification de rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md).

L’accès à Microsoft Online Services peut nécessiter l’affectation d’une licence à l’utilisateur pour qu’il puisse ouvrir le portail du service ou effectuer des tâches administratives.

## Attribution d’une licence à un utilisateur dans Azure AD

1. Avec un compte d’administrateur général ou un compte de coadministrateur, connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).
2. Sélectionnez **Tous les éléments** dans le menu principal.
3. Sélectionnez l’annuaire que vous souhaitez utiliser et auquel des licences sont associées.
4. Sélectionnez **Licences**. La liste des licences disponibles s’affiche.
5. Sélectionnez le plan de licence contenant les licences que vous souhaitez distribuer.
6. Sélectionnez **Affecter des utilisateurs**.
7. Sélectionnez l’utilisateur auquel vous souhaitez affecter une licence.
8. Cliquez sur le bouton **Affecter**. L’utilisateur peut à présent ouvrir une session Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0518_2016-->