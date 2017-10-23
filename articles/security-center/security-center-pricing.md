---
title: "Passer au niveau tarifaire Standard de Security Center pour une sécurité renforcée | Microsoft Docs"
description: "Cet article fournit des informations sur les tarifs d’Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 14513c37964246397d9f748322975ef40ef3e8ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Passer au niveau tarifaire Standard de Security Center pour une sécurité renforcée
Azure Security Center fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces pour les charges de travail s’exécutant dans Azure, en local et dans d’autres clouds. Il fournit une visibilité et un contrôle sur les charges de travail cloud hybrides, des défenses actives qui réduisent votre exposition aux menaces et une détection intelligente pour vous aider à suivre le rythme des cyberattaques en constante évolution.

## <a name="pricing-tiers"></a>Niveaux tarifaires
Security Center est proposé en deux niveaux :

- Le niveau **Gratuit** est automatiquement activé dans tous les abonnements Azure. Il vous donne accès à des stratégies de sécurité, à la fonctionnalité d’évaluation de la sécurité en continu et à des recommandations de sécurité actionnables pour vous aider à protéger vos ressources Azure.
- Le niveau **Standard** étend les fonctionnalités du niveau Gratuit aux charges de travail s’exécutant en privé et sur d’autres clouds publics, fournissant une gestion unifiée de la sécurité et une protection contre les menaces dans l’ensemble de vos charges de travail cloud hybrides. Le niveau Standard ajoute également des fonctionnalités de détection avancée des menaces, qui utilisent des analytiques comportementales intégrées et l’apprentissage machine pour identifier les attaques et les vulnérabilités zero-day, des contrôles d’accès et d’application pour réduire l’exposition aux attaques réseau et aux programmes malveillants, et bien plus encore. Le niveau Standard est gratuit les 60 premiers jours.

Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.

## <a name="try-standard-free-for-60-days"></a>Essayer gratuitement le niveau Standard pendant 60 jours
Le niveau Standard est gratuit les 60 premiers jours. Une fois ces 60 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

Vous pouvez mettre à niveau un abonnement Azure vers le niveau Standard, qui est hérité par toutes les ressources dans l’abonnement, ou vous pouvez définir une stratégie unique pour mettre à niveau un groupe de ressources spécifique uniquement.

Pour obtenir le niveau Standard :

1. Sélectionnez **Stratégie de sécurité** dans le menu principal de **Security Center**.
2. Sélectionnez l’abonnement que vous souhaitez mettre à niveau vers la version Standard.
3. Dans le panneau **Stratégie de sécurité**, sélectionnez **Niveau tarifaire**.
4. Sélectionnez **Standard** pour effectuer la mise à niveau.
5. Cliquez sur **Enregistrer**.

![Incident de sécurité][1]

> [!NOTE]
> Pour activer toutes les fonctionnalités de Security Center, vous devez appliquer le niveau tarifaire Standard à l’abonnement ou au groupe de ressources contenant les machines virtuelles concernées. La configuration de la tarification d’un espace de travail n’active pas l’accès juste à temps à la machine virtuelle, les contrôles d’application adaptatifs, ni les détections réseau pour les ressources Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Pourquoi passer au niveau Standard ?
Security Center offre une sécurité renforcée et une meilleure protection contre les menaces à vos charges de travail cloud hybrides, y compris :

- **Sécurité hybride** : obtenez une vue unifiée de la sécurité sur l’ensemble de vos charges de travail cloud et locales. Appliquez des stratégies de sécurité et évaluez en continu la sécurité de vos charges de travail cloud hybrides pour garantir la conformité aux normes de sécurité. Collectez, recherchez et analysez les données de sécurité à partir d’un large éventail de sources, dont les pare-feu et d’autres solutions partenaires.
- **Détection avancée des menaces** : utilisez l’analytique avancée et Microsoft Intelligent Security Graph pour avoir un avantage sur les cyberattaques en constante évolution.  Tirez parti des analytiques comportementales intégrées et de l’apprentissage machine pour identifier les attaques et les vulnérabilités zero-day. Surveillez les réseaux, machines et services cloud pour prévenir les attaques entrantes et les activités consécutives à une violation. Simplifiez l’investigation avec des outils interactifs et des informations sur les menaces contextuelles.
- **Contrôles d’accès et d’application** : bloquez les programmes malveillants et les autres applications indésirables en appliquant des recommandations de mise en liste verte adaptées à vos charges de travail spécifiques et alimentées par l’apprentissage machine. Réduisez la surface exposée aux attaques du réseau avec l’accès contrôlé juste à temps aux ports de gestion sur les machines virtuelles Azure, ce qui diminue de façon significative l’exposition aux attaques par force brute et autres attaques réseau.


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté la tarification de Security Center. Pour en savoir plus sur la sécurité renforcée et la protection avancée contre les menaces du niveau Standard, consultez :

- [Détection avancée des menaces](security-center-threat-report.md)
- [Gérer l’accès Juste à temps à la machine virtuelle](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
