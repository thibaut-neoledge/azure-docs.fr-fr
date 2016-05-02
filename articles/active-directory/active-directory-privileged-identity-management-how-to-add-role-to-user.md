<properties
   pageTitle="Ajouter ou supprimer un rôle d’utilisateur | Microsoft Azure"
   description="Découvrez comment ajouter des rôles privilégiés avec l’application Azure Active Directory Privileged Identity Management."
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
   ms.date="04/18/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management : comment ajouter ou supprimer un rôle d’utilisateur

Avec Azure Active Directory (AD), un administrateur général (ou un administrateur d’entreprise) peut mettre à jour les utilisateurs auxquels des rôles sont **définitivement** affectés dans Azure AD. Cette opération s’effectue avec les applets de commande PowerShell, telles que `Add-MsolRoleMember` et `Remove-MsolRoleMember`. Il peut également utiliser le portail Azure Classic comme décrit dans [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

L’application Azure AD Privileged Identity Management permet aux administrateurs de sécurité de rendre les affectations de rôle permanentes. Cependant, il permet également aux administrateurs d’ajouter ou de supprimer des candidats pour les affectations **temporaires** à ces rôles. Un candidat peut activer le rôle lorsqu’il en a besoin, puis l’autorisation expirera lorsqu’il aura terminé.

## Gérer des rôles avec PIM dans le portail Azure

Vous pouvez affecter aux utilisateurs de votre organisation différents rôles administratifs dans Azure AD. Ces affectations de rôles contrôlent quelles tâches, telles que l’ajout/la suppression d’utilisateurs ou la modification des paramètres de service, les utilisateurs sont autorisés à effectuer sur Azure AD, Office 365 et autres services et applications Microsoft. Vous trouverez plus d’informations sur les rôles disponibles dans [Rôles dans Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Pour ajouter un utilisateur à un rôle ou supprimer un utilisateur d’un rôle à l’aide de PIM, afficher le tableau de bord PIM, puis cliquez sur le bouton **Utilisateurs dans les rôles d’administrateur** ou sélectionnez un rôle spécifique (par exemple, administrateur général) dans le tableau des rôles.

> [AZURE.NOTE] Si vous n’avez pas encore activé PIM dans le portail Azure, accédez à [Prise en main d’Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) pour plus d’informations.

Si vous souhaitez donner l’accès à PIM à un autre utilisateur, consultez [Comment accorder l’accès à PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md) pour plus d’informations sur les rôles qu’exige PIM.

## Ajouter un utilisateur à un rôle
Une fois que vous avez accédé au panneau du rôle, en sélectionnant un rôle dans le tableau de bord Azure AD PIM ou en cliquant sur le bouton **Utilisateurs dans les rôles d’administrateur**,

1. cliquez sur **Add**.
  - Si vous avez accédé à l’écran actuel en cliquant sur un rôle d’utilisateur dans la table des rôles, le rôle est déjà sélectionné ou  
  - cliquez sur **Sélectionner un rôle** et choisissez un rôle dans la liste des rôles. Par exemple, **Administrateur de mots de passe**.
2. Recherchez l’utilisateur dans le panneau **Sélectionner les utilisateurs**. Si l’utilisateur est répertorié, son compte s’affiche quand vous tapez.
3. Sélectionnez l’utilisateur dans la liste des résultats, puis cliquez sur **OK**.
4. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné s’affiche dans la liste et le rôle sera temporaire par défaut.

  >[AZURE.NOTE] Si vous souhaitez conserver le rôle, cliquez sur l’utilisateur dans la liste. Les informations de l’utilisateur s’affichent dans un nouveau panneau. Sélectionnez **Rendre permanent** dans le menu des informations utilisateur. Vous devez effectuer cette opération si l’utilisateur ne peut pas s’inscrire à Azure Muti-Fator Authentication (MFA) ou s’il utilise un compte Microsoft. Les administrateurs temporaires sont invités à s’inscrire à MFA lors de l’activation.

Maintenant qu’un rôle temporaire a été affecté à l’utilisateur, indiquez-lui qu’il peut l’activer en suivant les instructions dans [Comment activer ou désactiver un rôle](active-directory-privileged-identity-management-how-to-activate-role.md).

## Supprimer un utilisateur d’un rôle

Vous pouvez supprimer des utilisateurs des attributions de rôle temporaire, mais assurez-vous qu’il reste toujours au moins un administrateur général permanent.

Suivez ces étapes pour supprimer un utilisateur spécifique d’un rôle :

1. Accédez au rôle dans la liste des rôles, en sélectionnant un rôle dans le tableau de bord Azure AD PIM ou en cliquant sur le bouton **Utilisateurs dans les rôles d’administrateur**,
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**. Un message vous demande de confirmer.
4. Cliquez sur **Oui** pour supprimer le rôle de l’utilisateur.

Si vous ne savez pas quels utilisateurs ont toujours besoin de leurs attributions de rôles, vous pouvez [démarrer une révision de sécurité pour le rôle](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->