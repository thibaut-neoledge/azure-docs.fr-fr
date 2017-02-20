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
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: fd4603d7bcbd1d243c6dd37d810040ff0ee1b3ae
ms.openlocfilehash: e61a9fad0c241da30a615e03aa18c6fbb03baf35


---
# <a name="azure-security-center-pricing"></a>Tarification d’Azure Security Center
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## <a name="pricing-tiers"></a>Niveaux de tarification
Security Center est proposé en deux niveaux :

* Le **niveau Gratuit** est automatiquement activé dans tous les abonnements Azure. Le niveau Gratuit vous permet de voir l’état de sécurité de vos ressources Azure, vos stratégies de sécurité de base, vos recommandations de sécurité, ainsi que l’intégration des produits et services de partenaires.
* Le **niveau Standard** fournit des fonctionnalités de détection avancée des menaces, notamment des informations sur les menaces, une analyse comportementale, une détection des anomalies, des informations sur les incidents de sécurité et des rapports d’évaluation des menaces. Une **évaluation gratuite de&90; jours** est disponible pour le niveau Standard.

Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.

> [!NOTE]
> Security Center utilise le stockage Azure pour enregistrer les données de sécurité générées à partir de vos nœuds protégés. Les coûts associés à ce stockage ne sont pas inclus dans le prix du service. Ils sont facturés séparément au [tarif normal du stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Les frais de stockage s’appliquent également pendant la période d’évaluation.
>
>

## <a name="try-standard-free-for-90-days"></a>Essayez gratuitement la version Standard pendant 90 jours
Une évaluation gratuite de 90 jours est disponible pour le niveau Standard. Pour obtenir la version d’évaluation gratuite du niveau Standard, sélectionnez la mosaïque **Stratégie** dans le panneau **Security Center**. Sélectionnez l’abonnement que vous souhaitez mettre à niveau vers la version Standard. Dans le panneau **Stratégie de sécurité**, sélectionnez **Niveau tarifaire**. Dans le panneau **Choisir votre niveau tarifaire**, sélectionnez **Standard – Essai gratuit**.

![Essai gratuit][1]

Une fois les 90 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

## <a name="upgrade-to-standard"></a>Mise à niveau vers le niveau Standard
Passez au niveau Standard pour bénéficier d’une détection avancée des menaces. Pour passer au niveau Standard, sélectionnez la mosaïque **Stratégie** dans le panneau **Security Center**. Sélectionnez l’abonnement que vous souhaitez mettre à niveau vers la version Standard. Dans le panneau **Stratégie de sécurité**, sélectionnez **Niveau tarifaire**. Dans le panneau **Choisir votre niveau tarifaire**, sélectionnez **Standard**.

![Niveau standard][2]

## <a name="why-upgrade-to-standard"></a>Pourquoi passer au niveau Standard ?
Le niveau Standard de Security Center fournit toutes les fonctionnalités du niveau Gratuit, ainsi qu’une détection avancée des menaces. La détection avancée des menaces vous aide à identifier les menaces actives qui ciblent vos ressources Azure, et vous donne les informations nécessaires pour corriger rapidement le problème.

Azure Security Center emploie des analyses de sécurité avancées allant bien au-delà des approches simplement basées sur la signature. Les innovations en matière de Big Data et de technologies Machine Learning sont mises à profit pour évaluer des événements dans toute la structure du cloud, et permettent ainsi de détecter des menaces qui seraient impossibles à identifier à l’aide de méthodes manuelles et de prédire l’évolution des attaques.

Les fonctionnalités d’analyse de la sécurité fournies avec le niveau Standard sont les suivantes :

* **Informations sur les menaces** : recherche les éléments malveillants en exploitant des informations globales concernant les menaces provenant de produits et services Microsoft, de Microsoft Digital Crimes Unit, de Microsoft Security Response Center et de sources externes.
* **Analyse comportementale** : applique des modèles connus pour détecter les comportements malveillants.
* **Détection des anomalies** : utilise le profilage statistique pour créer une ligne de base historique. Il avertit sur les écarts par rapport aux lignes de base établies qui se conforment à un vecteur d’attaque potentielle.

Dans le panneau **Alertes de sécurité** ci-dessous, Security Center a détecté un **incident** de sécurité. Un incident de sécurité est un regroupement de toutes les alertes d’une ressource correspondant à des modèles de chaîne de destruction. Quand vous sélectionnez un incident de sécurité, vous affichez des informations détaillées sur l’incident, ainsi que la liste des alertes associées. Quand vous sélectionnez une alerte, vous affichez des informations supplémentaires sur celle-ci.

![Incident de sécurité][3]

L’alerte **Communication réseau** ci-dessous fournit des informations détaillées sur l’alerte. Ces informations incluent la description complète de l’alerte, son niveau de gravité, son état actuel (dans ce cas, l’alerte a été ignorée, ce qui implique que l’utilisateur a exécuté une action pour l’ignorer), la ressource attaquée, ainsi que les étapes de résolution. Elles comprennent également une liste de liens vers des rapports d’informations sur les menaces fournis par Microsoft. Ces rapports peuvent être utilisés à des fins de protection et de résolution des problèmes de sécurité.

![Détails des alertes de sécurité][4]

## <a name="enable-data-collection"></a>Activer la collecte des données
Pour activer l’analyse comportementale des machines virtuelles, vous devez activer la collecte des données. Il est possible que vous deviez activer la collecte des données lorsque vous accédez à Security Center pour la première fois ou lorsque vous créez une stratégie de sécurité.

Pour vérifier que la collecte des données est activée, sélectionnez la mosaïque **Stratégie**. Le panneau **Stratégie de sécurité** s’ouvre et affiche la liste de vos abonnements Azure. Sélectionnez un abonnement. Si la **Collecte des données** est désactivée, activez-la, puis enregistrez la modification. Consultez [Activer la collecte des données dans Azure Security Center](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Étapes suivantes
* Ce document vous a présenté la tarification de Security Center. Pour plus d’informations sur la tarification, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.
* Pour plus d’informations sur les fonctionnalités de détection avancée de Security Center, consultez [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).
* Si vous avez des questions sur l’utilisation d’Azure Security Center, consultez le [FAQ d’Azure Security Center](security-center-faq.md).
* Si vous avez des questions sur l’utilisation de Security Center ou d’Azure, consultez les [forums Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png



<!--HONumber=Feb17_HO1-->


