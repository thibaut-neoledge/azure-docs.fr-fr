---
title: "Ajouter la personnalisation de votre société spécifique à une langue à votre page de connexion dans la version préliminaire d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment ajouter des images et du texte de la société dans une langue spécifique à une page de connexion Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a0310d6a-aaa7-4ea0-991d-6d3135b4382a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fece49df6a35f09ee73bb4b9ec4c33a0529a9f59
ms.lasthandoff: 12/28/2016


---
# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Ajouter la personnalisation de votre société spécifique à une langue à votre page de connexion dans la version préliminaire d’Azure Active Directory
Pour éviter toute confusion, de nombreuses entreprises veulent que tous les sites Web et services qu’elles gèrent aient un aspect similaire. La version préliminaire d’Azure Active Directory offre cette possibilité en vous permettant de personnaliser l’apparence la page de connexion pour qu’elle affiche le logo de votre société et sa palette de couleurs personnalisée. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)  La page de connexion s’affiche lorsque vous vous connectez à Office 365 ou à d’autres applications Web qui utilisent Azure AD comme fournisseur d’identité. Vous interagissez avec cette page pour saisir vos informations d’identification.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personnalisation de la page de connexion pour une autre langue
Vous pouvez ajouter des éléments de langage spécifiques à votre page de connexion personnalisée uniquement si vous avez déjà créé une page de connexion personnalisée, comme décrit dans [Ajouter votre marque à votre page de connexion](active-directory-branding-custom-signon-azure-portal.md). Vous pouvez configurer une page de connexion par répertoire avec un jeu d’éléments personnalisables par défaut. Une fois que vous avez configuré un jeu d’éléments de la page « par défaut », vous pouvez configurer des versions supplémentaires avec différents paramètres régionaux. Vous pouvez également combiner différents éléments. Par exemple, vous pouvez :

* Créez une **Image de page de connexion** par défaut adaptée à toutes les cultures, puis créez des versions spécifiques pour l’anglais et le français. Lorsque vous définissez vos navigateurs sur l’une de ces deux langues, l’image spécifique à la langue s’affiche, tandis que l’illustration par défaut apparaît pour toutes les autres langues.
* Configurez différents logos pour votre organisation (par exemple, des versions en japonais ou en hébreu).

Nous vous recommandons de garder un nombre raisonnable de variantes de langue, pour des raisons de performances et de maintenance.

**Pour ajouter votre marque à l’annuaire :**

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

   ![Ouvrir la gestion des utilisateurs](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. Sur le panneau **Utilisateurs et groupes** panneau, sélectionnez **Personnalisation de société**.
4. Sur le panneau **Utilisateurs et groupes - Personnalisation de la société**, sélectionnez la commande **Ajouter une langue**.

    ![Ajouter des éléments spécifiques à une langue](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. Modifiez les éléments que vous voulez personnaliser. Tous ces éléments sont facultatifs.
6. Cliquez sur **Save**.

Il peut s’écouler jusqu’à une heure avant que les modifications que vous avez apportées à la page de connexion soient visibles.

## <a name="next-steps"></a>Étapes suivantes
[Ajouter votre marque à votre page de connexion](active-directory-branding-custom-signon-azure-portal.md)

