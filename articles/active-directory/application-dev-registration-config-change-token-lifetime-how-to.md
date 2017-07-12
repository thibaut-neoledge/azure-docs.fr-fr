---
title: "Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée | Microsoft Docs"
description: "Comment mettre à jour les stratégies de durée de vie des jetons pour l’application que vous développez sur Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 54c30123bb582f515dfb0324cdfd897a6c4af5c0
ms.contentlocale: fr-fr
ms.lasthandoff: 04/14/2017

---


<a id="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application" class="xliff"></a>

# Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée

Azure AD Premium permet aux développeurs d’applications et aux administrateurs de locataires de configurer la durée de vie des jetons émis pour les clients non confidentiels. Les stratégies de durée de vie des jetons sont définies à l’échelle d’un locataire ou en fonction des ressources accessibles.

 * Pour définir une stratégie de durée de vie de jeton, vous devez télécharger le [module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Exécutez la commande **Connect-AzureAD -Confirm**.

 * Voici un exemple de stratégie qui définit l’âge maximal d’un jeton d’actualisation à facteur unique. Créez la stratégie : ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Consultez l’article [Durées de vie des jetons configurables dans Azure Active Directory (version préliminaire publique)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) pour apprendre à créer d’autres stratégies personnalisées.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Durées de vie des jetons configurables dans Azure Active Directory (version préliminaire publique)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Référence de jeton Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)


