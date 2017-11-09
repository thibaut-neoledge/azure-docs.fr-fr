---
title: "Migrer les stratégies classiques dans le portail Azure | Microsoft Docs"
description: "Migrez les stratégies classiques dans le portail Azure."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrer les stratégies classiques dans le portail Azure 


[L’accès conditionnel](active-directory-conditional-access-azure-portal.md) est une fonctionnalité d’Azure Active Directory (Azure AD) qui vous permet de contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Si l’objectif est toujours le même, la publication du nouveau portail Azure a également introduit des améliorations significatives pour le fonctionnement de l’accès conditionnel. Les stratégies d’accès conditionnel que vous avez configurées à l’extérieur du portail Azure peuvent coexister avec les nouvelles stratégies que vous créez dans le portail Azure. Tant que vous ne les désactivez ou supprimez pas, elles restent appliquées dans votre environnement. Toutefois, nous recommandons que vous migriez vos stratégies classiques vers des stratégies d’accès conditionnel Azure Active Directory, car :

- Les nouvelles stratégies vous permettent de répondre à des scénarios que vous ne pouvez pas gérer avec des stratégies classiques.

- Vous pouvez réduire le nombre de stratégies que vous devez gérer en les consolidant.   

Cette rubrique vous aide pour la migration de vos stratégies classiques existantes vers les nouvelles stratégies d’accès conditionnel Azure AD.


## <a name="classic-policies"></a>Stratégies classiques

Les stratégies d’accès conditionnel pour Azure AD et Intune que vous n’avez pas créées dans le portail Azure sont également appelées **stratégies classiques**. Pour migrer vos stratégies classiques, vous n’avez pas besoin d’accéder à votre portail Azure classique. Le portail Azure vous offre une vue [**Stratégies classiques (préversion)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) qui vous permet de passer en revue vos stratégies classiques.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Ouvrir une stratégie classique

**Pour ouvrir une stratégie classique :**

1. Dans la barre de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/02.png)
 
2. Dans la page **Accès conditionnel - Stratégies**, dans la section **Gérer**, cliquez sur **Stratégies classiques (préversion)**.

3. Dans la liste des stratégies classiques, sélectionnez la stratégie qui vous intéresse.   

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Stratégies d’accès conditionnel Azure AD

Cette rubrique fournit des instructions détaillées qui vous permettent de migrer vos stratégies classiques sans être familiarisé avec les stratégies d’accès conditionnel Azure AD. Toutefois, être familiarisé avec la terminologie et les concepts de base de l’accès conditionnel d’Azure AD vous aidera à améliorer votre expérience de la migration.

Consultez l'article :

- [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md) pour en savoir plus sur les concepts de base et la terminologie

- [Bien démarrer avec l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) pour vous familiariser avec l’interface utilisateur dans le portail Azure


 





## <a name="multi-factor-authentication-policy"></a>Stratégie d’authentification multifacteur 

Cet exemple montre comment migrer une stratégie classique qui nécessite l’authentification de multifacteur** pour une application cloud. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**Pour migrer une stratégie classique :**

1. [Ouvrez la stratégie classique](#open-a-classic-policy) pour obtenir les paramètres de configuration.
2. Créez une nouvelle stratégie d’accès conditionnel Azure AD afin de remplacer votre stratégie classique. 


### <a name="create-a-new-conditional-access-policy"></a>Créer une nouvelle stratégie d’accès conditionnel


1. Dans la barre de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/02.png)



3. Dans la page **Accès conditionnel**, pour ouvrir la page **Nouveau**, cliquez sur **Ajouter** dans la barre d’outils située en haut.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/03.png)

4. Dans la page **Nom** du panneau **Nouveau**, indiquez le nom de votre stratégie.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/29.png)

5. Dans la section **Affectations**, cliquez sur **Utilisateurs et groupes**.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/05.png)

    a. Si vous avez tous les utilisateurs sélectionnés dans votre stratégie classique, cliquez sur **Tous les utilisateurs**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/35.png)

    b. Si vous avez des groupes sélectionnés dans votre stratégie classique, cliquez sur **Sélectionner des utilisateurs et des groupes**, puis sélectionnez les groupes et utilisateurs requis.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/36.png)

    c. Si vous avez des groupes exclus, cliquez sur l’onglet **Exclure** et sélectionnez les groupes et utilisateurs requis. 

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/37.png)

6. Dans la page **Nouveau**, pour ouvrir la page **Applications cloud**, cliquez sur **Applications cloud** dans la section **Affectation**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Dans la page **Applications cloud**, procédez comme suit :

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/08.png)

    a. Cliquez sur **Sélectionner les applications**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, sélectionnez votre application cloud, puis cliquez sur **Sélectionner**.

    d. Dans la page **Applications cloud**, cliquez sur **Terminé**.



9. Si vous avez sélectionné **Imposer l’authentification multifacteur** :

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/26.png)

    a. Dans la section **Contrôles d’accès**, cliquez sur **Accorder**.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/27.png)

    b. Sur la page **Accorder**, cliquez sur **Accorder l’accès**, puis cliquez sur **Imposer l’authentification multifacteur**.

    c. Cliquez sur **Sélectionner**.


10. Cliquez sur **Activé** pour activer votre stratégie.

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/30.png)

11. Désactivez la stratégie classique. 

    ![Accès conditionnel](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
