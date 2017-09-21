---
title: "Présentation de l’accès aux ressources dans Azure | Microsoft Docs"
description: "Cette rubrique explique les concepts relatifs à l’utilisation des administrateurs d’abonnements pour contrôler l’accès aux ressources dans l’ensemble du portail Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: e5ac068e37d921530272e5eb3dc76d976a86a742
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="understanding-resource-access-in-azure"></a>Présentation de l'accès aux ressources dans Azure

Le contrôle des accès dans Azure s’envisage d’abord dans une perspective de facturation. Le propriétaire d'un compte Azure accessible par le biais du [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) est l'administrateur de compte. Les abonnements sont un conteneur de facturation, mais ils constituent également une limite de sécurité : chaque abonnement a un administrateur de service qui peut ajouter, supprimer et modifier des ressources Azure dans cet abonnement à l’aide du [portail Azure](https://portal.azure.com/). L'administrateur de sécurité par défaut d'un nouvel abonnement est l'administrateur de compte, mais ce dernier peut modifier l'administrateur de sécurité dans le centre des comptes Azure.

<br><br>![Comptes Azure][1]

Les abonnements sont également associés à un répertoire. Le répertoire définit un ensemble d'utilisateurs. Ceux-ci peuvent être des utilisateurs professionnels ou scolaires qui ont créé le répertoire, ou bien des utilisateurs externes (autrement dit, des comptes Microsoft). Les abonnements sont accessibles par un sous-ensemble des utilisateurs du répertoire qui ont été affectés comme administrateur de service ou comme coadministrateur ; la seule exception est que, pour des raisons d'héritage, les comptes Microsoft (anciennement Windows Live ID) peuvent être affectés comme administrateur de service ou comme co-admistrateur sans être présents dans le répertoire.

<br><br>![Contrôle des accès par rôle dans Azure][2]

Fonctionnalité dans le portail Azure Classic permettant aux administrateurs de service connectés à l’aide d’un Account Microsoft de modifier l’annuaire associé à un abonnement à l’aide de la commande **Modifier l’annuaire** à la page **Abonnements** sous **Paramètres**. Notez que cette opération a des incidences sur le contrôle des accès de cet abonnement.

> [!NOTE]
> La commande **Modifier le répertoire** du portail de gestion Azure n’est pas disponible pour les utilisateurs connectés avec un compte professionnel ou scolaire parce que ces comptes peuvent se connecter uniquement au répertoire auquel ils appartiennent.
> 
> 

<br><br>![Flux de connexion utilisateur simple][3]

Dans le cas le plus simple, une organisation (par exemple Contoso) applique la facturation et le contrôle des accès au même ensemble d'abonnements. Autrement dit, le répertoire est associé à des abonnements appartenant à un seul compte Azure. Une fois correctement connectés au portail Azure Classic, les utilisateurs voient deux collections de ressources (représentées en orange dans l’illustration précédente) :

* Répertoires où leur compte d'utilisateur existe (source ou ajouté comme principal étranger). Notez que le répertoire utilisé pour la connexion n'est pas approprié pour ce calcul. Ainsi, vos répertoires seront toujours affichés, où que vous soyez connecté.
* Ressources qui font partie des abonnements associés au répertoire utilisé pour la connexion ET auxquelles l'utilisateur peut accéder (qu’ils soient administrateurs de service ou co-administrateurs).

<br><br>![Utilisateur avec plusieurs abonnements et répertoires][4]

Les utilisateurs disposant d’abonnements sur plusieurs répertoires ont la possibilité de basculer le contexte actuel du portail Azure Classic à l’aide du filtre d’abonnement. En arrière-plan, il en résulte une connexion distincte à un répertoire différent, mais cela est effectué en toute transparence à l'aide de l’authentification unique.

Les opérations telles que le déplacement de ressources entre des abonnements peuvent être plus difficiles en raison de cet affichage unique des répertoires des abonnements. Pour effectuer le transfert de ressources, il peut être nécessaire de d’abord utiliser la commande **Modifier l’annuaire** commande de la page Abonnements sous **Paramètres** pour associer les abonnements au même annuaire.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la façon de modifier les administrateurs pour un abonnement Azure, voir [Comment ajouter ou modifier les rôles d’administrateur Azure](../billing/billing-add-change-azure-subscription-administrator.md)
* Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

