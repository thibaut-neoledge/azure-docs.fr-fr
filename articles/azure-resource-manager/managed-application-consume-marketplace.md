---
title: "Utilisation des applications gérées de la Place de marché Azure | Microsoft Docs"
description: "Explique comment créer une application gérée Azure disponible via la Place de marché."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Utilisation des applications gérées de la Place de marché Azure

Comme décrit dans l’article [Vue d’ensemble des applications gérées Azure](managed-application-overview.md), il existe deux scénarios dans l’expérience de bout en bout. D’un côté, il y a l’éditeur ou le fournisseur désireux de créer une application gérée destinée à être utilisée par les clients. De l’autre, le client final ou l’utilisateur de l’application gérée. Cet article concerne le second scénario. Il décrit comment vous pouvez déployer une application gérée à partir de la Place de marché Microsoft Azure.

## <a name="create-from-the-marketplace"></a>Créer une application à partir de la Place de marché

Le déploiement d’une application gérée à partir de la Place de marché est similaire au déploiement de n’importe quel type de ressources de ce Marketplace. 

Dans le portail, sélectionnez **+ Nouveau** et recherchez le type de solution à déployer. Parmi les options disponibles, sélectionnez celle dont vous avez besoin.

![rechercher des solutions](./media/managed-application-consume-marketplace/search-apps.png)

Passez en revue le résumé de l’application, puis sélectionnez **Créer**.

![créer une application gérée](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Comme avec toute autre solution, vous devez définir les valeurs de certains champs. Ces champs varient selon le type d’application gérée que vous créez. 

## <a name="view-support-information"></a>Afficher les informations de support

Après le déploiement de votre application gérée, affichez les informations de support associées. Ces informations s’affichent dans le panneau de l’application gérée.

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>Afficher les autorisations d’éditeur

Le fournisseur qui gère votre application a accès à vos ressources. Pour afficher ces autorisations, sélectionnez **Autorisations**.

![autorisations](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la publication d’une application gérée sur la Place de marché, consultez l’article [Applications gérées sur la Place de marché Azure](managed-application-author-marketplace.md).
* Pour publier des applications gérées uniquement disponibles pour les utilisateurs de votre organisation, consultez la page [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
* Pour utiliser des applications gérées uniquement disponibles pour les utilisateurs de votre organisation, consultez la page [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).