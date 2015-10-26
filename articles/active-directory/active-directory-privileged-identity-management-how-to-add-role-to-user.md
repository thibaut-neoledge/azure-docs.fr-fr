<properties
   pageTitle="Azure Privileged Identity Management : comment commencer à ajouter un rôle à un utilisateur"
   description="Découvrez comment ajouter des rôles aux identités privilégiées avec l’extension Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management : comment ajouter ou supprimer un rôle d’utilisateur

## Ajout ou suppression d’un rôle d’utilisateur
Vous pouvez accéder au panneau **Ajouter des utilisateurs gérés** de l’interface de PIM de plusieurs façons. La séquence de clics pour chacune d’elles est indiquée ci-dessous :

## Chemins pour ajouter un rôle à un utilisateur ou pour supprimer un rôle d’un utilisateur
- Tableau de bord > Utilisateurs dans les rôles d’administrateur > Ajouter ou Supprimer
- Tableau de bord > Résumé des rôles > Liste de tous les utilisateurs > Ajouter ou Supprimer
- Tableau de bord > cliquez sur le rôle d’utilisateur dans la table des rôles (par exemple Administrateur général) > Ajouter ou Supprimer

## Ajouter un rôle à un utilisateur à partir de la section Utilisateurs dans les rôles d’administrateur ou en accédant à Résumé des rôles > Liste de tous les utilisateurs
Une fois que vous avez accédé au panneau **Ajouter des utilisateurs gérés**...

1. Cliquez sur **Sélectionner un rôle**. Si vous avez accédé à l’écran actuel en cliquant sur un rôle d’utilisateur dans la table des rôles, le rôle est déjà sélectionné.
2. Sélectionnez un rôle dans la liste des rôles. Par exemple, si vous sélectionnez **Administrateur de mots de passe**, le panneau **Sélectionner des utilisateurs** s’ouvre.
3. Entrez le nom de l’utilisateur auquel vous souhaitez ajouter le rôle dans le champ de recherche. Si l’utilisateur est répertorié, son compte s’affiche quand vous tapez, ainsi que les utilisateurs ayant un nom similaire.
4. Sélectionnez l’utilisateur dans la liste, puis cliquez sur **Terminé**.
5. Cliquez sur **OK** pour enregistrer votre sélection. Si vous ne le faites pas, la sélection ne sera pas enregistrée. L’utilisateur que vous avez sélectionné s’affiche dans la liste et le rôle sera temporaire.
6. Si vous souhaitez conserver le rôle, cliquez sur l’utilisateur dans la liste. Les informations de l’utilisateur s’affichent dans un nouveau panneau. Sélectionnez **Rendre permanent** dans le menu des informations utilisateur.
7. Cliquez sur **Activer** pour démarrer une demande d’activation de ce rôle pour l’utilisateur. Entrez le motif de l’activation dans le champ de texte **Raison de la demande**. À ce stade, le rôle est automatiquement activé pour cet utilisateur, et une notification est envoyée aux administrateurs généraux.

## Supprimer un utilisateur d’un rôle
1. Accédez à l’utilisateur dans la liste des rôles d’utilisateur en suivant un des chemins décrits plus haut.
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**. Un message de confirmation s’affiche.
4. Cliquez sur Oui pour supprimer le rôle de l’utilisateur.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->