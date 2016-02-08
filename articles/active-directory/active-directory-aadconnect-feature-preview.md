<properties
   pageTitle="Azure AD Connect : fonctionnalités de la version préliminaire | Microsoft Azure"
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
   ms.date="01/21/2016"
   ms.author="andkjell;billmath"/>

# Plus de détails sur les fonctionnalités de la version préliminaire
Cette rubrique décrit l’utilisation des fonctionnalités disponibles dans la version préliminaire.

## Extensions d’annuaire
Les extensions d'annuaire vous permettent d'étendre le schéma dans Azure AD avec vos propres attributs à partir d'un annuaire Active Directory local. Vous pouvez ainsi créer des applications métier avec les attributs que vous continuez à gérer en local. Ces attributs peuvent être utilisés via des [extensions d’annuaire Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Vous pouvez voir les attributs disponibles à l’aide de l’[explorateur d’Azure AD Graph](https://graphexplorer.cloudapp.net) et de [l’Explorateur Microsoft Graph](https://graphexplorer2.azurewebsites.net/) respectivement.

Actuellement, aucune charge de travail Office 365 n’utilise ces attributs.

Lors de l’installation d’Azure AD Connect, une application dans laquelle ces attributs seront disponibles est enregistrée. Vous pouvez voir cette application dans le portail Azure. ![Application d’extension de schéma](./media/active-directory-aadconnect-feature-preview/extension3.png)

Ces attributs sont désormais disponibles via Graph : ![Graph](./media/active-directory-aadconnect-feature-preview/extension4.png)

Les attributs ont pour préfixe extension\_{AppClientId} \_. L’AppClientId aura la même valeur pour tous les attributs de votre annuaire Azure AD.

Seuls les attributs à valeur unique sont pris en charge et les valeurs des attributs ne peuvent pas comporter plus de 250 caractères.

## Écriture différée de groupe
L’option pour l’écriture différée de groupe dans les fonctionnalités facultatives permet l’écriture différée de « groupes dans Office 365 » vers une forêt avec Exchange installé. Il s’agit d’un nouveau type de groupe qui est toujours contrôlé dans le cloud. Si Exchange est installé sur site, vous pouvez réécrire ces groupes en local afin que les utilisateurs disposant d’une boîte aux lettres Exchange de local puissent envoyer et recevoir des courriers électroniques de la part de ces groupes.

Vous trouverez [ici](http://aka.ms/O365g) d’autres informations sur les groupes Office 365 et la façon de les utiliser.

Ce groupe est représenté comme un groupe de distribution dans les versions locales d’AD DS. Votre serveur Exchange local doit être au niveau de la mise à jour cumulative 8 d’Exchange 2013 (publiée en mars 2015) ou d’Exchange 2016 pour pouvoir reconnaître ce nouveau type de groupe.

**Notes relatives à la version préliminaire**

- L’attribut de carnet d’adresses n’est pas rempli pour l’instant dans la version préliminaire. Sans cet attribut, le groupe ne sera pas visible dans la liste d’adresses globale. Le moyen le plus simple de renseigner cet attribut est d’utiliser l’applet de commande Exchange PowerShell `update-recipient`.
- Seules les forêts dotées du schéma Exchange constituent des cibles valides pour les groupes. Si aucune version d’Exchange n’est détectée, il est impossible d’activer l’écriture différée de groupe.
- Seuls les déploiements d’entreprise basés sur une seule forêt Exchange sont actuellement pris en charge. Si vous avez plusieurs organisations Exchange en local, vous aurez besoin d’une solution GALSync locale pour que ces groupes s’affichent dans vos autres forêts.
- La fonctionnalité d’écriture différée de groupe ne prend pas en charge les groupes de sécurité ou les groupes de distribution pour l’instant.

>[AZURE.NOTE] L’écriture différée sur un groupe nécessite un abonnement Azure AD Premium.

## Écriture différée de l’utilisateur
> [AZURE.IMPORTANT] La fonctionnalité d’écriture différée utilisateur en version préliminaire a été provisoirement supprimée lors de la mise à jour d’Azure AD Connect en août 2015. Si vous l'avez activée, vous devez désactiver cette fonctionnalité.

L’écriture différée de l’utilisateur est actuellement en version préliminaire. Elle peut être utilisée uniquement si Azure AD est la source de tous les objets utilisateur et si l’annuaire Active Directory local est vide avant d’activer la fonctionnalité (déploiement en mode champ vert).

> [AZURE.WARNING] Cette fonctionnalité doit être testée uniquement dans un environnement de test et ne doit pas être utilisée dans un annuaire Azure AD destiné à une utilisation en production.

.

>[AZURE.NOTE] L’écriture différée utilisateur sur un appareil exige un abonnement Azure AD Premium.

## Étapes suivantes
Poursuivez votre [installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->