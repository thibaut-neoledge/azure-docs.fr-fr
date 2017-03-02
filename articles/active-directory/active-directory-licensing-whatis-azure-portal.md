---

title: "À quoi correspondent les licences basées sur les groupes dans Azure Active Directory ? | Microsoft Docs"
description: "Description des licences basées sur les groupes Azure Active Directory, de leur fonctionnement, de leur prise en main et des meilleures pratiques"
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 1f4e0904c9025ce0b21d904acc7b959e823039ef
ms.lasthandoff: 02/22/2017


---

# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>À quoi correspondent les licences basées sur les groupes dans Azure Active Directory ?

Les services de cloud computing Microsoft, comme Office 365, Enterprise Mobility + Security, Dynamics CRM et d’autres produits similaires, exigent d’affecter des licences à chaque utilisateur qui a besoin d’y accéder. La gestion des licences est exposée aux administrateurs par le biais d’un des portails de gestion (Office, Azure) et des applets de commande PowerShell. L’état d’affectation des licences est stocké dans Azure Active Directory, l’infrastructure à la base de la gestion des identités de tous les services de cloud computing Microsoft.

Jusqu’à présent, les licences ne pouvaient être affectées qu’au niveau de chaque utilisateur, ce qui pouvait rendre difficile la gestion à grande échelle pour nos clients. Par exemple, pour ajouter ou supprimer des licences utilisateur en raison d’évolutions de l’organisation, par exemple l’arrivée ou le départ d’utilisateurs dans l’organisation ou dans un service, l’administrateur devait souvent écrire un script PowerShell complexe afin d’effectuer des appels individuels au service cloud.

Pour résoudre ces difficultés, nous avons introduit une nouvelle fonctionnalité dans le système de gestion de licences Azure AD : les licences basées sur les groupes. Il est à présent possible d’affecter une ou plusieurs licences de produits à un groupe. Azure AD permet de garantir que les licences sont affectées à tous les membres du groupe. Tous les nouveaux membres qui rejoignent le groupe se voient affecter les licences appropriées et, lorsqu’ils quittent le groupe, ces licences sont supprimées. Ceci élimine toute nécessité d’automatiser la gestion des licences avec PowerShell pour refléter les évolutions de la structure de l’organisation et des services utilisateur par utilisateur.

## <a name="features"></a>Caractéristiques

Voici les principales fonctionnalités de la fonction de licences basées sur les groupes :

- Les licences peuvent être affectées à n’importe quel groupe de sécurité dans Azure AD. Les groupes de sécurité peuvent être synchronisés en local à l’aide d’Azure AD Connect, créés directement dans Azure AD (ils sont également appelé groupes cloud purs) ou créés automatiquement avec la fonctionnalité de groupe dynamique d’Azure AD.

- Lorsqu’une licence de produit est affectée à un groupe, l’administrateur peut désactiver un ou plusieurs plans de services dans le produit. En règle générale, cette opération est effectuée lorsque l’organisation n’est pas encore prête à commencer à utiliser un service inclus dans un produit, par exemple si l’administrateur souhaite affecter un produit Office 365 E3 à un service mais désactiver temporairement le service Yammer Entreprise.

- Tous les services de cloud computing Microsoft nécessitant des licences au niveau des utilisateurs sont pris en charge. Cela comprend tous les produits Office 365, Enterprise Mobility + Security, Dynamics CRM, etc.

- Les licences basées sur les groupes ne sont pour le moment disponibles que sur le [Portail Azure](https://portal.azure.com). Les clients qui utilisent principalement d’autres portails de gestion des utilisateurs et des groupes, comme le portail Office 365, peuvent continuer à les utiliser, mais devront utiliser le Portail Azure pour gérer les licences au niveau des groupes.

- Azure AD gère automatiquement les modifications de licences résultant de modifications de l’appartenance aux groupes. En règle générale, les licences d’un utilisateur qui rejoint ou quitte un groupe sont modifiées dans les quelques minutes qui suivent la modification de l’appartenance au groupe.

- Un utilisateur peut être membre de plusieurs groupes pour lesquels des stratégies de licence sont spécifiées ; il peut aussi disposer de licences qui lui ont été directement affectées en dehors des groupes. L’état utilisateur qui en résulte est une combinaison de toutes les licences de produits et services affectées.

- Dans certains cas, il n’est pas possible d’affecter les licences à un utilisateur, par exemple s’il n’y a pas suffisamment de licences disponibles dans le client ou si des services en conflit les uns avec les autres ont été affectés en même temps. Les administrateurs ont accès aux informations sur les utilisateurs pour lesquels Azure AD n’a pas pu traiter entièrement les licences de groupes ; ils peuvent prendre des mesures correctives en fonction de ces informations.

- Dans la préversion publique, un abonnement d’évaluation ou payant à Azure AD De base ou une version supérieure est requis dans le client pour pouvoir utiliser la gestion des licences basées sur les groupes. En outre, tous les utilisateurs qui héritent de licences de groupes doivent avoir l’édition payante de la licence Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des licences basées sur les groupes, consultez :

* [Affectation de licences à un groupe dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers les licences basées sur les groupes dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Autres scénarios de licences basées sur les groupes Azure Active Directory](active-directory-licensing-group-advanced.md)

