---
title: "Définir des stratégies de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Définir des stratégies de sécurité dans Azure Security Center
Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité en vous guidant tout au long des étapes nécessaires à l’exécution de cette tâche.


## <a name="how-security-policies-work"></a>Comment fonctionnent les stratégies de sécurité ?
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier la stratégie dans Security Center ou utiliser la [stratégie Azure](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) pour créer des définitions, définir des stratégies supplémentaires et affecter des stratégies à des groupes d’administrations (qui peuvent représenter toute l’organisation, une branche commerciale de cette dernière, etc), et surveiller la conformité des stratégies à ces étendues.

> [!NOTE]
> La stratégie Azure est disponible en préversion limitée. Cliquez [ici](https://aka.ms/getpolicy) pour la rejoindre. Pour plus d’informations sur les stratégies Azure, consultez [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (Créer et gérer des stratégies pour mettre en vigueur la conformité).

## <a name="how-to-change-security-policies-in-security-center"></a>Comment modifier des stratégies de sécurité dans Security Center ?
Vous pouvez modifier la stratégie de sécurité par défaut pour chacun de vos abonnements Azure dans Security Center. Pour modifier une stratégie de sécurité, vous devez avoir le rôle d’administrateur de la sécurité, de propriétaire ou de collaborateur pour l’abonnement concerné ou le groupe de gestion dans laquelle elle se trouve. Connectez-vous au portail Azure et suivez les étapes ci-dessous pour afficher les stratégies de sécurité dans Security Center :

1. Dans le tableau de bord **Security Center**,sous **Général**, cliquez sur **Stratégie de sécurité**.
2. Sélectionnez l’abonnement sur lequel activer la stratégie de sécurité.

    ![Gestion des stratégies](./media/security-center-policies/security-center-policies-fig10.png)

3. Dans la section **COMPOSANTS DE LA STRATÉGIE** , cliquez sur **stratégie de sécurité**.

    ![Composants de la stratégie](./media/security-center-policies/security-center-policies-fig12.png)

4. Il s’agit de la stratégie par défaut affectée à Security Center via la stratégie Azure. Vous pouvez supprimer les éléments qui se trouvent sous **PARAMÈTRES ET STRATÉGIES**, ou ajouter d’autres définitions de stratégies se trouvant sous **OPTIONS DISPONIBLES**. Pour ce faire, il suffit de cliquer sur le signe plus à côté du nom de la définition.

    ![Définitions de stratégies](./media/security-center-policies/security-center-policies-fig11.png)

5. Si vous souhaitez plus d’explications sur la stratégie, cliquez dessus pour afficher autre page comportant les détails et le code JSON ayant la structure [définition de stratégie(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure) :

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. Lorsque vous avez terminé, cliquez sur **Enregistrer**.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md). découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md). découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md). découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
