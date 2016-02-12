<properties
   pageTitle="Azure Privileged Identity Management : comment effectuer une révision de la sécurité"
   description="Découvrez comment ajouter des rôles aux identités privilégiées avec l’extension Privileged Identity Management."
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
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management : Comment effectuer une révision de sécurité


Il est très facile d’examiner un accès privilégié une fois qu’une [révision de la sécurité a été démarrée](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Pour les réviseurs : approbation ou refus de l’accès

### Révision manuelle
1. Dans le menu principal de PIM, cliquez sur **Réviser l’accès administratif**. Une liste de révisions de sécurité s’affiche.
2. Sélectionnez les **utilisateurs** dans la liste pour lesquels vous souhaitez modifier l’accès. REMARQUE : l’accès sera réellement modifié. Ce processus crée simplement une liste de vérification pour ceux qui pourraient être amenés à modifier l’accès pour le rôle. Une fois qu’au moins un utilisateur a été sélectionné, les boutons **Approuver l’accès** et **Refuser l’accès** sont activés.
3. Cliquez sur **Approuver l’accès** ou **Refuser l’accès** pour les utilisateurs que vous avez sélectionnés. Une notification s’affiche dans le menu principal du portail Azure, et la liste de révisions disparaît. Fermez le panneau **Réviser les rôles Azure AD**.

### Révision automatique
1. L’utilisateur reçoit un message électronique indiquant qu’il doit réviser son accès. Le message électronique contient un lien de connexion au portail Azure.
2. Une fois connecté, l’utilisateur peut approuver ou refuser son propre accès en cliquant sur les boutons **Approuver l’accès** ou **Refuser l’accès**. Son nom disparaît de la liste.

## Pour les responsables de la révision : gestion des révisions de sécurité

## Achèvement ou arrêt d’une révision
1. Accédez au tableau de bord PIM.
2. Dans la liste **Révisions de sécurité**, cliquez sur la révision de sécurité que vous souhaitez achever. Le panneau des détails de la révision de sécurité s’affiche.
3. Cliquez sur **Arrêter la révision** pour achever ou arrêter la révision. Cette opération archive la révision, et le panneau disparaît.

## Exportation d’une révision
Vous pouvez exporter une révision pour l’utiliser dans Excel ou dans tout autre programme pouvant utiliser des fichiers CSV.

1. Accédez au tableau de bord PIM.
2. Cliquez sur la section **Révisions de sécurité** du tableau de bord. Le panneau **Révisions de sécurité** s’affiche.
3. Cliquez sur la révision de sécurité que vous souhaitez exporter. Le panneau des détails de la révision de sécurité s’affiche.
4. Cliquez sur le bouton **Exporter**. Un fichier CSV démarre le téléchargement.

## Suppression d’une révision

> [AZURE.WARNING] La suppression ayant lieu sans affichage préalable d’un avertissement, assurez-vous que vous *voulez* vraiment supprimer cette révision.

1. Revenez au tableau de bord de PIM.
2. Cliquez sur la section **Révisions de sécurité** du tableau de bord. Le panneau **Révisions de sécurité** s’affiche.
3. Cliquez sur la révision de sécurité que vous souhaitez supprimer. Le panneau des détails de la révision de sécurité s’affiche.
4. Cliquez sur le bouton **Supprimer**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->