<properties
   pageTitle="Fonctionnalités Azure AD dans la version préliminaire | Microsoft Azure"
   description="Cette rubrique décrit en détail les fonctionnalités disponibles en version préliminaire dans Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Plus de détails sur les fonctionnalités de la version préliminaire
Cette rubrique décrit l’utilisation des fonctionnalités disponibles dans la version préliminaire.

## Écriture différée de l’utilisateur
> [AZURE.IMPORTANT]La fonctionnalité d'écriture différée utilisateur en version préliminaire a été temporairement supprimée de la mise à jour d'août d’AAD Connect. Si vous l'avez activée, vous devez désactiver cette fonctionnalité.

L’écriture différée de l’utilisateur est actuellement en version préliminaire. Elle peut être utilisée uniquement si Azure AD est la source de tous les objets utilisateur et si l’annuaire Active Directory local est vide avant d'activer la fonctionnalité.

> [AZURE.IMPORTANT]Cette fonctionnalité doit uniquement être testée dans un environnement de test et ne doit pas être utilisée dans un annuaire Azure AD destiné à une utilisation en production.

## Écriture différée de groupe
L’option pour l’écriture différée de groupe dans les fonctionnalités facultatives permet l’écriture différée de « groupes dans Office 365 » vers une forêt avec Exchange installé. Il s’agit d’un nouveau type de groupe qui est toujours contrôlé dans le cloud. Cette fonctionnalité est disponible dans outlook.office365.com ou myapps.microsoft.com, comme indiqué ici :


![Filtrage de la synchronisation](./media/active-directory-aadconnect-feature-preview/office365.png)

![Filtrage de la synchronisation](./media/active-directory-aadconnect-feature-preview/myapps.png)

Ce groupe est représenté comme un groupe de distribution dans les versions locales d’AD DS. Votre serveur Exchange local doit être au niveau de la mise à jour cumulative 8 d’Exchange 2013 (publiée en mars 2015) pour reconnaître ce nouveau type de groupe.

**Remarque :**

- L’attribut de carnet d’adresses n’est pas rempli pour l’instant dans la version préliminaire. Pour ce faire, nous vous recommandons d’utiliser l’applet de commande Exchange PowerShell update-recipient.
- Seules les forêts dotées du schéma Exchange constituent des cibles valides pour les groupes. Si aucune version d’Exchange n’est détectée, il est impossible d’activer l’écriture différée de groupe.
- La fonctionnalité d’écriture différée de groupe ne prend pas en charge les groupes de sécurité ou les groupes de distribution pour l’instant.

Vous trouverez [ici](http://aka.ms/O365g) plus d’informations sur les groupes Office 365.

## Extensions d’annuaire
Les extensions d'annuaire vous permettent d'étendre le schéma dans Azure AD avec vos propres attributs à partir d'un annuaire Active Directory local.

Seuls les attributs à valeur unique sont pris en charge et les valeurs des attributs ne peuvent pas comporter plus de 250 caractères. Le schéma Azure AD et le métaverse sont étendus avec les attributs sélectionnés. Une nouvelle application est ajoutée dans Azure AD avec les attributs.

![Filtrage de la synchronisation](./media/active-directory-aadconnect-feature-preview/extension3.png)

Ces attributs sont désormais disponibles via l’API Graph :

![Filtrage de la synchronisation](./media/active-directory-aadconnect-feature-preview/extension4.png)

## Étapes suivantes
Poursuivez votre [installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO3-->