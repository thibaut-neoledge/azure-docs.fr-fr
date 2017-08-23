---
title: Tarification de Security Center | Microsoft Docs
description: "Cet article fournit des informations sur la tarification d’Azure Security Center."
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
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 367b8f38cb9fcf3dc36db83641cb1696710608ef
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-pricing"></a>Tarification d’Azure Security Center
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## <a name="pricing-tiers"></a>Niveaux de tarification
Security Center est proposé en deux niveaux :

* Le **niveau Gratuit** est automatiquement activé dans tous les abonnements Azure. Le niveau Gratuit vous permet de voir l’état de sécurité de vos ressources Azure, vos stratégies de sécurité de base, vos recommandations de sécurité, ainsi que l’intégration des produits et services de partenaires.
* Le **niveau Standard** fournit des fonctionnalités de détection avancée des menaces, notamment des informations sur les menaces, une analyse comportementale, une détection des anomalies, des informations sur les incidents de sécurité et des rapports d’évaluation des menaces. Le niveau Standard est gratuit les 60 premiers jours.

Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.

## <a name="try-standard-free-for-60-days"></a>Essayer gratuitement le niveau Standard pendant 60 jours
Le niveau Standard est gratuit les 60 premiers jours. Une fois ces 60 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

Pour obtenir le niveau Standard :

1. Dans le panneau **Centre de sécurité**, cliquez sur la vignette **Stratégie**.
2. Sélectionnez l’abonnement que vous souhaitez mettre à niveau vers la version Standard.
3. Dans le panneau **Stratégie de sécurité**, sélectionnez **Niveau tarifaire**.
4. Dans le panneau **Choisir votre niveau tarifaire**, sélectionnez **Standard**.
5. Cliquez sur **Sélectionner**.


## <a name="why-upgrade-to-standard"></a>Pourquoi passer au niveau Standard ?
Le niveau Standard de Security Center fournit toutes les fonctionnalités du niveau Gratuit, ainsi qu’une détection avancée des menaces. La détection avancée des menaces vous aide à identifier les menaces actives qui ciblent vos ressources Azure, et vous donne les informations nécessaires pour corriger rapidement le problème.

Azure Security Center emploie des analyses de sécurité avancées allant bien au-delà des approches simplement basées sur la signature. Les innovations en matière de Big Data et de technologies Machine Learning sont mises à profit pour évaluer des événements dans toute la structure du cloud, et permettent ainsi de détecter des menaces qui seraient impossibles à identifier à l’aide de méthodes manuelles et de prédire l’évolution des attaques.

Les fonctionnalités d’analyse de la sécurité fournies avec le niveau Standard sont les suivantes :

* **Informations sur les menaces** : recherche les éléments malveillants en exploitant des informations globales concernant les menaces provenant de produits et services Microsoft, de Microsoft Digital Crimes Unit, de Microsoft Security Response Center et de sources externes.
* **Analyse comportementale** : applique des modèles connus pour détecter les comportements malveillants.
* **Détection des anomalies** : utilise le profilage statistique pour créer une ligne de base historique. Il avertit sur les écarts par rapport aux lignes de base établies qui se conforment à un vecteur d’attaque potentielle.

Dans le panneau **Alertes de sécurité** ci-dessous, Security Center a détecté un **incident** de sécurité. Un incident de sécurité est un regroupement de toutes les alertes d’une ressource correspondant à des modèles de chaîne de destruction. Quand vous sélectionnez un incident de sécurité, vous affichez des informations détaillées sur l’incident, ainsi que la liste des alertes associées. Quand vous sélectionnez une alerte, vous affichez des informations supplémentaires sur celle-ci.

![Incident de sécurité][2]

L’alerte **Communication réseau** ci-dessous fournit des informations détaillées sur l’alerte. Ces informations incluent la description complète de l’alerte, son niveau de gravité, son état actuel (dans ce cas, l’alerte a été ignorée, ce qui implique que l’utilisateur a exécuté une action pour l’ignorer), la ressource attaquée, ainsi que les étapes de résolution. Elles comprennent également une liste de liens vers des rapports d’informations sur les menaces fournis par Microsoft. Ces rapports peuvent être utilisés à des fins de protection et de résolution des problèmes de sécurité.

![Détails des alertes de sécurité][3]

## <a name="enable-data-collection"></a>Activer la collecte des données
Pour activer l’analyse comportementale des machines virtuelles, vous devez activer la collecte des données.

Pour vérifier que la collecte des données est activée :

1. Sélectionnez la vignette **Stratégie**. Le panneau **Stratégie de sécurité** s’ouvre et affiche la liste de vos abonnements Azure.
2. Sélectionnez un abonnement.
3. Si la **Collecte des données** est désactivée, activez-la, puis enregistrez la modification.

> [!NOTE]
> Si vous utilisez le niveau Gratuit d’Azure Security Center, vous pouvez désactiver la collecte de données à partir des machines virtuelles dans la stratégie de sécurité. La collecte de données est obligatoire pour les abonnements du niveau Standard.
>
>

Pour plus d’informations, consultez [Activer la collecte des données dans Azure Security Center](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Étapes suivantes
* Ce document vous a présenté la tarification de Security Center. Pour plus d’informations sur la tarification, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.
* Pour plus d’informations sur les fonctionnalités de détection avancée de Security Center, consultez [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).
* Pour en savoir plus sur la gestion et la protection des données dans Security Center, consultez [Sécurité des données Azure Security Center](security-center-data-security.md).
* Si vous avez des questions sur l’utilisation d’Azure Security Center, consultez le [FAQ d’Azure Security Center](security-center-faq.md).
* Si vous avez des questions sur l’utilisation de Security Center ou d’Azure, consultez les [forums Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/standard.png
[2]: ./media/security-center-pricing/incident.png
[3]: ./media/security-center-pricing/network-alert.png

