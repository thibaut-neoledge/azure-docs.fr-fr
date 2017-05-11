---
title: "Sécurité des machines virtuelles Linux avec Azure Security Center | Microsoft Docs"
description: "Didacticiel - Sécurité des machines virtuelles avec Azure Security Center"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4c680ee63e1c2d8e858c725adc42bbcbc49e4045
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>Surveiller la sécurité des machines virtuelles avec Azure Security Center

Azure Security Center vous permet d’avoir un meilleur aperçu de la configuration des ressources Azure par rapport aux pratiques de sécurité. Il intègre par ailleurs une fonctionnalité d’analyse de la sécurité capable de détecter des menaces qui pourraient autrement passer inaperçues. Ce didacticiel contient une brève présentation d’Azure Security Center et explique comment l’utiliser avec les machines virtuelles Azure.   

## <a name="security-center-overview"></a>Vue d’ensemble de Security Center

Azure Security Center aide à identifier les problèmes potentiels de configuration de machines virtuelles, ainsi que certaines menaces de sécurité ciblées. Il permet notamment d’identifier les machines virtuelles dépourvues de groupes de sécurité réseau, les disques non chiffrés et les attaques RDP par force brute. Ces informations sont présentées dans le tableau de bord Azure Security Center sous forme de graphiques faciles à lire.

Pour accéder au tableau de bord Azure Security Center, il suffit de cliquer sur **Centre de sécurité** dans le volet de navigation gauche du portail Azure. Le tableau de bord propose une vue générale de l’état des ressources, des alertes de sécurité et des recommandations de configuration. Vous pouvez y consulter l’état de la sécurité de votre environnement Azure, diverses recommandations courantes, ainsi que l’état actuel des alertes de menaces. Chacun de ces graphiques généraux peut être développé pour afficher davantage de détails sur le domaine considéré.

![Tableau de bord ASC](./media/tutorial-azure-security/asc-dash.png)

Azure Security Center va au-delà de la simple découverte de données en fournissant des recommandations pour résoudre les problèmes détectés. Par exemple, si une machine virtuelle a été déployée sans avoir été jointe à un groupe de sécurité réseau, une recommandation assortie de mesures correctives est créée. Par ailleurs, ces recommandations assurent une automatisation des corrections sans quitter le contexte d’Azure Security Center.  

![Recommandations](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>Configurer la collecte de données

Avant de pouvoir examiner les configurations de sécurité des machines virtuelles, il convient de configurer la collecte de données d’Azure Security Center. Cela implique d’activer la collecte de données et de créer un compte de stockage Azure où seront conservées les données collectées. 

1. Dans le tableau de bord Azure Security Center, cliquez sur **Stratégie de sécurité**, puis sélectionnez votre abonnement. 
2. Sous **Collecte de données**, sélectionnez *Activé*.
3. Cliquez sur **Choisir un compte de stockage**, puis créez un nouveau compte de stockage. Cliquez sur **OK** quand vous avez terminé.
4. Cliquez sur **Enregistrer** dans le panneau **Stratégie de sécurité**. 

À l’issue de cette opération, l’agent de collecte de données Azure Security Center est installé sur toutes les machines virtuelles et la collecte de données débute. 

## <a name="configure-security-policy"></a>Configurer une stratégie de sécurité

La stratégie de sécurité permet de définir les éléments de stratégie de sécurité qui feront l’objet d’une collecte de données et d’une émission de recommandations. Par défaut, les ressources Azure sont évaluées par rapport à tous les éléments de stratégie. Chaque élément de stratégie individuel peut être désactivé globalement pour toutes les ressources Azure ou désactivé par groupe de ressources. Cette configuration vous permet d’appliquer des stratégies de sécurité différentes en fonction de l’ensemble de ressources Azure. Pour obtenir des informations détaillées sur les stratégies de sécurité Azure Security Center, consultez [Définir des stratégies de sécurité dans Azure Security Center](../../security-center/security-center-policies.md). 

Configurez une stratégie de sécurité pour toutes les ressources Azure :

1. Dans le tableau de bord Azure Security Center, cliquez sur **Stratégie de sécurité**, puis sélectionnez votre abonnement. 
2. Cliquez sur **Stratégie de prévention**.
3. Activez ou désactivez les éléments de stratégie à appliquer à toutes les ressources Azure.
4. Une fois l’opération terminée, cliquez sur **OK**.
5. Cliquez sur **Enregistrer** dans le panneau **Stratégie de sécurité**. 

Pour configurer une stratégie pour un groupe de ressources spécifique, suivez les mêmes étapes, sauf qu’au lieu de sélectionner l’abonnement dans le panneau Stratégie de sécurité, sélectionnez un groupe de ressources. Au moment de configurer la stratégie, sélectionnez *Unique* sous **Héritage**. Si vous voulez désactiver la collecte de données pour un groupe de ressources spécifique, vous pouvez effectuer cette configuration à cet emplacement.

Dans l’exemple suivant, une stratégie unique a été créée pour le groupe de ressources nommé *myResoureGroup*. Dans cette stratégie, les recommandations concernant le chiffrement de disque et le pare-feu d’applications web ont été désactivées.

![Stratégie unique](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Afficher l’état de configuration des machines virtuelles

Une fois que la collecte de données a été activée et qu’une stratégie de sécurité a été configurée, Azure Security Center commence à afficher des alertes et des recommandations. L’agent de collecte de données est installé sur les machines virtuelles à mesure qu’elles sont déployées, et Azure Security Center est rempli avec les données relatives à ces nouvelles machines virtuelles. Pour obtenir des informations détaillées sur l’état de configuration des machines virtuelles, consultez [Protection de vos machines virtuelles dans Azure Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

L’état des ressources de chaque machine virtuelle et des ressources Azure associées est agrégé et présenté dans un graphique facile à lire à mesure que les données sont collectées. Pour consulter l’état des ressources, retournez dans le tableau de bord Azure Security Center. Sous **Intégrité de la sécurité des ressources**, cliquez sur **Calculer**. Enfin, dans le panneau **Calculer**, cliquez sur **Machines virtuelles**. Cette vue fournit un récapitulatif de l’état de configuration de toutes les machines virtuelles.

![Calcul de l’état](./media/tutorial-azure-security/compute-health.png)

Le fait de sélectionner une machine virtuelle déterminée a pour effet d’afficher toutes les recommandations qui la concernent. Les recommandations sont détaillées dans la prochaine section de ce didacticiel.

## <a name="remediate-configuration-issues"></a>Corriger les problèmes de configuration

Dès lors qu’Azure Security Center se remplit de données de configuration, vous obtenez des recommandations en rapport avec la stratégie de sécurité configurée. Par exemple, si une machine virtuelle a été configurée sans avoir été associée à un groupe de sécurité réseau, une recommandation vous préconise d’en créer un. Pour afficher la liste de toutes les recommandations : 

1. Dans le tableau de bord Azure Security Center, cliquez sur **Recommandations**.
3. Sélectionnez une recommandation spécifique ; un panneau s’ouvre alors avec la liste de toutes les ressources visées par la recommandation.
4. Sélectionnez une ressource spécifique que vous voulez traiter.
5. Suivez les instructions qui s’affichent à l’écran pour mettre en œuvre les mesures correctives. 

Dans de nombreux cas, Azure Security Center propose des mesures pratiques pour appliquer la recommandation sans quitter le contexte d’Azure Security Center. Par exemple, dans l’exemple suivant, un groupe de sécurité réseau (NSG) a été détecté avec une règle de trafic entrant non limité. À partir de cette recommandation, le bouton **Modifier les règles de trafic entrant** peut être actionné de façon à accéder à l’interface d’utilisateur qui permet de modifier la règle. 

![Recommandations](./media/tutorial-azure-security/remediation.png)

Les recommandations sont marquées comme étant résolues à mesure qu’elles sont appliquées. 

## <a name="view-detected-threats"></a>Consulter les menaces détectées

En plus des recommandations de configuration de ressources, Azure Security Center présente aussi des alertes de détection de menaces. La fonctionnalité d’alerte de sécurité agrège les données collectées à partir de chaque machine virtuelle, des journaux de mise en réseau Azure et des solutions partenaires connectées pour détecter les menaces de sécurité au niveau des ressources Azure. Pour obtenir des informations plus détaillées sur les fonctionnalités de détection des menaces d’Azure Security Center, consultez [Fonctionnalités de détection d’Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Pour pouvoir bénéficier de la fonctionnalité d’alertes de sécurité, vous devez faire évoluer le niveau tarifaire Azure Security Center de *Gratuit* à *Standard*. À cette occasion, vous bénéficiez d’une **version d’évaluation gratuite** de 30 jours. Pour changer de niveau tarifaire :  

1. Dans le tableau de bord Azure Security Center, cliquez sur **Stratégie de sécurité**, puis sélectionnez votre abonnement.
2. Cliquez sur **Niveau tarifaire**.
3. Sélectionnez le nouveau niveau, puis cliquez sur **Sélectionner**.
5. Cliquez sur **Enregistrer** dans le panneau **Stratégie de sécurité**. 

Une fois activé, le graphique d’alertes de sécurité se remplit à mesure que des menaces de sécurité sont détectées.

![Alertes de sécurité](./media/tutorial-azure-security/security-alerts.png)

Sélectionnez une alerte pour afficher des informations, telles qu’une description de la menace, le temps de détection, les tentatives de menace et les mesures correctives recommandées. Dans cet exemple, une attaque par force brute RDP a été détectée avec 294 tentatives RDP avortées, et une recommandation de résolution est fournie.

![Attaque RDP](./media/tutorial-azure-security/rdp-attack.png)
