<properties
   pageTitle="Azure AD Connect : fonctionnalités de la version préliminaire | Microsoft Azure"
   description="Cette rubrique décrit en détail les fonctionnalités disponibles en version préliminaire dans Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/27/2016"
   ms.author="andkjell;billmath"/>

# Plus de détails sur les fonctionnalités de la version préliminaire
Cette rubrique décrit l’utilisation des fonctionnalités disponibles dans la version préliminaire.

## Écriture différée de groupe
L’option pour l’écriture différée de groupe dans les fonctionnalités facultatives permet l’écriture différée de **groupes dans Office 365** vers une forêt avec Exchange installé. Il s’agit d’un nouveau type de groupe qui est toujours contrôlé dans le cloud. Si Exchange est installé sur site, vous pouvez réécrire ces groupes en local afin que les utilisateurs disposant d’une boîte aux lettres Exchange de local puissent envoyer et recevoir des courriers électroniques de la part de ces groupes.

Vous trouverez [ici](http://aka.ms/O365g) d’autres informations sur les groupes Office 365 et la façon de les utiliser.

Ce groupe est représenté comme un groupe de distribution dans les versions locales d’AD DS. Votre serveur Exchange local doit être au niveau de la mise à jour cumulative 8 d’Exchange 2013 (publiée en mars 2015) ou d’Exchange 2016 pour pouvoir reconnaître ce nouveau type de groupe.

**Notes relatives à la version préliminaire**

- L’attribut de carnet d’adresses n’est pas rempli pour l’instant dans la version préliminaire. Sans cet attribut, le groupe ne sera pas visible dans la liste d’adresses globale. Le moyen le plus simple de renseigner cet attribut est d’utiliser l’applet de commande Exchange PowerShell `update-recipient`.
- Seules les forêts dotées du schéma Exchange constituent des cibles valides pour les groupes. Si aucune version d’Exchange n’est détectée, il est impossible d’activer l’écriture différée de groupe.
- Seuls les déploiements d’entreprise basés sur une seule forêt Exchange sont actuellement pris en charge. Si vous avez plusieurs organisations Exchange en local, vous aurez besoin d’une solution GALSync locale pour que ces groupes s’affichent dans vos autres forêts.
- La fonctionnalité d’écriture différée de groupe ne prend pas en charge les groupes de sécurité ou les groupes de distribution pour l’instant.

>[AZURE.NOTE] L’écriture différée des groupes nécessite un abonnement Azure AD Premium.

## Écriture différée de l’utilisateur
> [AZURE.IMPORTANT] La fonctionnalité d’écriture différée utilisateur en version préliminaire a été supprimée lors de la mise à jour d’Azure AD Connect en août 2015. Si vous l'avez activée, vous devez désactiver cette fonctionnalité.

## Étapes suivantes
Poursuivez votre [installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0928_2016-->