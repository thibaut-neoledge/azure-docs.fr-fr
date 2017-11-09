---
title: "Gérer l’accès aux ressources Azure avec Azure Active Directory"
description: "Découvrez toutes les façons de gérer l’accès aux ressources Azure en utilisant les différentes fonctionnalités d’Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: e42bbed0a9e37a6632b2a71d2b3546fcbc38cdc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Gérer l’accès aux ressources Azure avec Azure Active Directory

Il est vital pour toute organisation qui utilise le cloud de pouvoir gérer les identités et les accès aux ressources situées dans cloud. Azure Active Directory (Azure AD) est le système de gestion des identités et des accès pour Microsoft Azure.  

Avant d’explorer la prise en charge des fonctionnalités d’Azure AD, regardez la vidéo suivante, qui concerne le verrouillage des accès sur le Cloud Azure à l’aide de l’authentification unique, du contrôle d’accès en fonction du rôle et de l’accès conditionnel. Vous en apprendrez davantage sur les points suivants :

- les meilleures pratiques de configuration de la connexion unique sur le portail Azure, grâce à une instance Active Directory locale ;
- l’utilisation du contrôle d’accès en fonction du rôle (RBAC) pour affiner le contrôle d’accès aux ressources dans les abonnements ;
- l’application de règles d’authentification robustes à l’aide de l’accès conditionnel Azure AD.
- Le concept Managed Service Identity, selon lequel les ressources Azure peuvent être authentifiées automatiquement auprès des services Azure, et les développeurs n’ont pas besoin de gérer des clés API ou des clés secrètes.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Zones de fonctionnalité
Azure AD fournit les fonctionnalités suivantes pour gérer l’accès aux ressources Azure :

|||
|---|---|
| [Relation entre les abonnements et les locataires Azure AD](active-directory-understanding-resource-access.md) | Apprenez-en davantage sur Azure AD, la source de confiance des utilisateurs et des groupes qui utilisent un abonnement Azure. |
| [Role-Based Access Control (RBAC)](role-based-access-control-what-is.md) | Proposez une gestion fine des accès via les rôles attribués aux utilisateurs, aux groupes ou aux principaux de service. |
| [Privileged Identity Management (PIM) avec RBAC](pim-azure-resource.md) | Contrôlez les accès hautement privilégiés en affectant des rôles privilégiés juste à temps. |
| [Accès conditionnel pour la gestion Azure](conditional-access-azure-management.md) | Définissez des stratégies d’accès conditionnel pour autoriser ou bloquer l’accès aux points de terminaison de gestion Azure. |
| [Identité de service managée (MSI)](msi-overview.md) | Donnez à des ressources Azure comme les machines virtuelles leur propre identité afin de ne pas être obligé d’insérer des informations d’identification dans votre code. |

 