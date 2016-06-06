<properties
   pageTitle="Comment accéder à PIM | Microsoft Azure"
   description="Découvrez comment ajouter des rôles à des utilisateurs avec l’extension Azure Active Directory Privileged Identity Management pour qu’ils puissent gérer PIM."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Comment donner accès pour gérer Azure AD Privileged Identity Management

Le premier utilisateur à activer Azure AD Privileged Identity Management (PIM) pour une organisation doit être un administrateur général. D’autres administrateurs généraux n’ont cependant pas accès à PIM par défaut et ne peuvent donc pas gérer les attributions temporaires. Pour donner accès à PIM, le premier utilisateur peut affecter les autres au rôle d’administrateur de rôle privilégié. Cette affectation doit être effectuée depuis PIM proprement dit et ne peut pas être modifiée via PowerShell ou d’autres portails.

> [AZURE.NOTE] La gestion d’Azure AD PIM requiert l’authentification Azure MFA. Les comptes Microsoft ne pouvant pas s’inscrire pour l’authentification Azure MFA, un utilisateur qui se connecte avec un compte Microsoft ne peut pas accéder à Azure AD PIM.

Vérifiez qu’il y a toujours au moins deux utilisateurs dans un rôle d’administrateur de rôle privilégié, au cas où un utilisateur serait verrouillé ou son compte supprimé.

## Donner accès à un autre utilisateur pour gérer PIM

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez l’application **Azure AD Privileged Identity Management** sur le tableau de bord.
2. Sélectionnez le rôle **Administrateur de rôle privilégié**. La liste des utilisateurs actuels de ce rôle s'affiche.
3. Cliquez sur **Ajouter** et un volet de l'Assistant s'affiche. Le rôle est déjà sélectionné.
4. Cliquez sur **Sélectionnez utilisateurs** et le panneau de la liste des utilisateurs s'ouvre.
5. Entrez le nom de l’utilisateur dans le champ de recherche. Si l’utilisateur est répertorié, son compte s’affiche quand vous tapez.
6. Sélectionnez l’utilisateur dans les résultats de la recherche, puis cliquez sur **OK**.
7. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné s’affiche dans la liste et le rôle sera temporaire par défaut.

  - Si vous souhaitez conserver le rôle, cliquez sur l’utilisateur dans la liste. Les informations de l’utilisateur s’affichent dans un nouveau panneau. Sélectionnez **Rendre permanent** dans le menu des informations utilisateur.

8. Envoyez à l'utilisateur un lien vers la documentation Azure [Prise en main d'Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).


## Supprimez les droits d'accès d'un autre utilisateur pour la gestion de PIM

Avant de supprimer le rôle d’administrateur de rôle privilégié d’un utilisateur, vérifiez toujours que deux utilisateurs y sont toujours affectés.

1. Dans le tableau de bord PIM, cliquez sur le rôle **Administrateur de rôle privilégié**. La liste des utilisateurs actuels de ce rôle s'affiche.
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**. Un message de confirmation s’affiche.
4. Cliquez sur **Oui** pour supprimer le rôle de l’utilisateur.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->