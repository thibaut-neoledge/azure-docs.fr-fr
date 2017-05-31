---
title: "Présentation du Centre de sécurité Azure | Microsoft Docs"
description: "Découvrez le Centre de sécurité Azure, ses fonctionnalités principales et son fonctionnement."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: fd52bd8e14ca9bdcd06fc820e5e03fb5feccc72f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017


---
# <a name="introduction-to-azure-security-center"></a>Présentation du Centre de sécurité Azure
Découvrez le Centre de sécurité Azure, ses fonctionnalités principales et son fonctionnement.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.
>
>

## <a name="what-is-azure-security-center"></a>Qu’est-ce que le Centre de sécurité Azure ?
 Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## <a name="key-capabilities"></a>Fonctionnalités clés
 Le Centre de sécurité propose des fonctions de prévention, de détection et de réponse aux menaces conviviales et efficaces, intégrées dans Azure. Ses fonctionnalités principales sont les suivantes :

| Étape | Fonctionnalité |
| --- | --- |
| Prévention |Surveille l’état de sécurité de vos ressources Azure |
| Prévention | Définit des stratégies pour vos groupes de ressources et abonnements Azure en fonction des exigences en matière de sécurité de votre société, du type d’application que vous utilisez et de la confidentialité de vos données |
| Prévention | Utilise des recommandations de sécurité basées sur des stratégies pour guider les propriétaires de services dans le processus d’implémentation de contrôles nécessaires. |
| Prévention | Déploie rapidement des services de sécurité et les appliances de Microsoft et de partenaires |
| Détection |Collecte et analyse automatiquement des données de sécurité à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. |
| Détection | Exploite des informations globales concernant des menaces provenant de produits et services Microsoft, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) et de sources externes. |
| Détection | Applique des analyses avancées, notamment l’apprentissage automatique (Machine Learning) et des études du comportement |
| Réponse |Fournit des notifications d’incidents/alertes de sécurité hiérarchisées |
| Réponse | Donne des informations sur la source de l’attaque et les ressources affectés |
| Réponse | Suggère des manières d’empêcher l’attaque en cours et d’empêcher des attaques futures |

## <a name="introductory-walkthrough"></a>Introduction pas à pas
 Le Centre de sécurité est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). [Connectez-vous au portail](https://portal.azure.com). Sous le menu principal du portail, faites défiler la page jusqu’à l’option **Centre de sécurité** ou sélectionnez la vignette **Centre de sécurité** que vous avez préalablement épinglée au tableau de bord du portail.

![Mosaïque de sécurité dans le portail Azure][1]

Dans le Centre de sécurité, vous pouvez définir des stratégies de sécurité, surveiller des configurations de sécurité et afficher des alertes de sécurité.

### <a name="security-policies"></a>Stratégies de sécurité
Vous pouvez définir des stratégies pour vos groupes de ressources et abonnements Azure en fonction des besoins de votre entreprise en termes de sécurité. Vous pouvez également les personnaliser selon les types d’applications que vous utilisez ou de la sensibilité des données dans chaque abonnement. Par exemple, les ressources utilisées pour le développement ou le test peuvent présenter des exigences de sécurité différentes de celles qui sont utilisées pour les applications de production. De même, les applications dont les données sont réglementées telles que les informations d’identification personnelle (PII)) peuvent nécessiter un niveau de sécurité plus élevé.

> [!NOTE]
> Pour modifier une stratégie de sécurité au niveau de l’abonnement ou au niveau du groupe de ressources, vous devez être propriétaire de l’abonnement ou collaborer à celui-ci.
>
>

Dans le panneau **Centre de sécurité**, sélectionnez la vignette **Stratégie** pour obtenir la liste de vos abonnements et groupes de ressources.   

![Panneau Centre de sécurité][2]

Dans le panneau **Stratégie de sécurité**, sélectionnez un abonnement pour afficher les détails de la stratégie.

![Panneau Stratégie de sécurité, abonnement][3]

**Collecte des données** (voir ci-dessus) active la collecte de données pour une stratégie de sécurité. L’activation autorise les opérations suivantes :

* Analyse quotidienne de toutes les machines virtuelles prises en charge pour la surveillance de la sécurité et le suivi des recommandations.
* Collecte d’événements de sécurité à des fins d’analyse et de détection de menaces.

**Choisir un compte de stockage par région** (voir ci-dessus) vous permet de choisir, pour chaque région où s’exécutent des machines virtuelles, le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Si vous ne choisissez pas un compte de stockage pour chaque région, il est créé pour vous. Pour des raisons de sécurité, les données collectées sont isolées logiquement des autres données clients.

> [!NOTE]
> La collecte des données et le choix d’un compte de stockage par région sont configurés au niveau de l’abonnement.
>
>

Sélectionnez **Stratégie de prévention** (voir ci-dessus) pour ouvrir le panneau **Stratégie de prévention**. **Afficher les recommandations pour** : vous permet de choisir les contrôles de sécurité que vous voulez surveiller et les recommandations que vous voulez voir en fonction des besoins en matière de sécurité des ressources au sein de l’abonnement.

Ensuite, sélectionnez un groupe de ressources pour afficher les détails de la stratégie.

![Panneau Stratégie de sécurité, groupe de ressources][4]

**Héritage** (voir ci-dessus) vous permet de définir le groupe de ressources en tant que :

* Hérité (option par défaut), ce qui signifie que toutes les stratégies de sécurité associées à ce groupe de ressources sont héritées à partir du niveau d’abonnement.
* Unique, ce qui signifie que le groupe de ressources a une stratégie de sécurité personnalisée. Dans ce cas, vous devez apporter des modifications sous **Afficher les recommandations pour**.

> [!NOTE]
> Si un conflit se produit entre la stratégie de niveau abonnement et la stratégie de niveau groupe de ressources, la stratégie de niveau groupe de ressources est prioritaire.
>
>

### <a name="security-recommendations"></a>Recommandations de sécurité
 Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires. Voici quelques exemples :

* Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
* Configuration de groupes de sécurité réseau et de règles pour contrôler le trafic vers les machines virtuelles
* Approvisionnement de pare-feu d’applications web pour protéger vos applications web contre les attaques ciblées
* Déploiement de mises à jour système manquantes
* Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

Cliquez sur la mosaïque **Recommandations** pour obtenir une liste de recommandations. Cliquez sur chaque recommandation pour afficher plus d’informations ou prendre des mesures pour résoudre le problème.

![Recommandations de sécurité dans le Centre de sécurité Azure][5]

### <a name="security-state-of-azure-resources"></a>État de sécurité des ressources Azure
La section **Prévention** du tableau de bord affiche la posture de sécurité globale de votre environnement par type de ressource (machines virtuelles, applications web et autres ressources).   

Sélectionnez un type de ressource dans la section **Prévention** pour afficher plus d’informations, notamment une liste des vulnérabilités de sécurité potentielles qui ont été identifiées. (Dans l’exemple ci-dessous, **Calcul** est sélectionné.)

![Mosaïque d’intégrité des ressources][6]

### <a name="security-alerts"></a>Alertes de sécurité
 Le Centre de sécurité collecte, analyse et intègre automatiquement les données du journal à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. Quand des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détections :

* Des machines virtuelles compromises qui communiquent avec des adresses IP connues comme étant malveillantes
* Des programmes malveillants avancés qui sont détectés à l’aide du rapport d’erreurs Windows
* Des attaques par force brute contre des machines virtuelles
* Des alertes de sécurité de logiciels anti-programme malveillant et de pare-feu intégrés

Un clic sur la mosaïque **Alertes de sécurité** affiche une liste d’alertes prioritaires.

![Alertes de sécurité][7]

Si vous sélectionnez une alerte, des informations supplémentaires sur l’attaque et des suggestions sur la manière d’y remédier s’affichent.

![Détails des alertes de sécurité][8]

### <a name="partner-solutions"></a>Solutions de partenaires
La vignette **Solutions de partenaires** permet de surveiller en un coup d’œil l’état d’intégrité de vos solutions de partenaires intégrées à votre abonnement Azure. Le Centre de sécurité affiche les alertes provenant des solutions.

Sélectionnez la vignette **Solutions de partenaires** . Un panneau s'ouvre et affiche la liste de toutes les solutions de partenaires connectées.

![Solutions de partenaires][9]

## <a name="get-started"></a>Prise en main
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Le Centre de sécurité est activé avec votre abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

 Le Centre de sécurité est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour plus d’informations, voir la [documentation sur le portail](https://azure.microsoft.com/documentation/services/azure-portal/) .

[Prise en main du Centre de sécurité Azure](security-center-get-started.md) vous guide à travers les composants de surveillance de sécurité et de gestion des stratégies de sécurité du Centre de sécurité.

## <a name="see-also"></a>Voir aussi
Ce document vous a donné un aperçu du Centre de sécurité, de ses fonctions clés et de sa mise en route. Pour en savoir plus, consultez les articles suivants :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

