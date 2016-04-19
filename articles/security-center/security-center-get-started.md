<properties
   pageTitle="Prise en main du Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous aide à prendre rapidement en main le Centre de sécurité Azure en vous guidant à travers les composants de surveillance de sécurité et de gestion des stratégies de sécurité et vers les étapes suivantes."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/11/2016"
   ms.author="terrylan"/>

# Prise en main du Centre de sécurité Azure

Ce document vous aide à prendre rapidement en main le Centre de sécurité Azure en vous guidant à travers les composants de surveillance de sécurité et de gestion des stratégies de sécurité et vers les étapes suivantes.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Qu’est-ce que le Centre de sécurité Azure ?
 Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Composants requis

Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Le Centre de sécurité est activé avec votre abonnement. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

 Le Centre de sécurité est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour en savoir plus, consultez la [documentation sur le portail](https://azure.microsoft.com/documentation/services/azure-portal/).


## Accéder au Centre de sécurité

Dans le portail, procédez comme suit pour accéder au Centre de sécurité :

1. Sélectionnez **Parcourir**, puis faites défiler jusqu’à l’option **Centre de sécurité**. ![Centre de sécurité Azure Access dans le portail][1]

2. Sélectionnez **Centre de sécurité**. Le panneau **Centre de sécurité** s’ouvre.
3. Pour accéder facilement au panneau **Centre de sécurité** à l’avenir, sélectionnez l’option **Épingler le panneau au tableau de bord** (en haut à droite). ![Option Épingler le panneau au tableau de bord][2]

## Utiliser le Centre de sécurité

Vous pouvez configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure. Configurons une **stratégie** de sécurité pour votre abonnement :

1. Cliquez sur la vignette **Stratégie** dans le panneau **Centre de sécurité**. ![Centre de sécurité][3]

2. Dans le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou groupe de ressources**, sélectionnez un abonnement. ![Panneau de stratégie de sécurité dans le Centre de sécurité Azure][4]

3. Dans le panneau **stratégie de sécurité**, activez l’option **Collecte des données** pour collecter automatiquement les journaux. L’activation de l’option **Collecte des données** activera également l’extension de la surveillance sur toutes les machines virtuelles actuelles et nouvelles dans l’abonnement.
4. Sélectionnez **Choisir un compte de stockage par région**. Pour chaque région où s’exécutent des machines virtuelles, vous devez choisir le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Si vous ne choisissez pas un compte de stockage pour chaque région, il sera créé pour vous. Pour des raisons de sécurité, les données collectées sont isolées logiquement des autres données clients.

     > [AZURE.NOTE] Nous vous recommandons d’activer la collecte de données et de choisir un compte de stockage au niveau de l’abonnement en premier. Bien que vous puissiez définir les stratégies de sécurité au niveau du groupe de ressources et au niveau de l’abonnement Azure, la configuration de la collecte des données et du compte de stockage intervient uniquement au niveau de l’abonnement.

5. Activez les **Recommandations** à afficher dans le cadre de votre stratégie de sécurité. Exemples :

 - L’activation des **mises à jour du système** permet d’analyser toutes les machines virtuelles prises en charge pour les mises à jour du système d’exploitation manquantes.
 - L’activation des **règles de base** permet d’analyser toutes les machines virtuelles prises en charge pour identifier toute configuration du système d’exploitation pouvant rendre la machine virtuelle plus vulnérable aux attaques.

Afficher des **Recommandations** :

1. Retournez dans le panneau **Centre de sécurité** et sélectionnez la vignette **Recommandations**. Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Quand des failles de sécurité potentielles sont identifiées, une recommandation y est affichée.
2.	Sélectionnez chaque recommandation pour afficher plus d’informations et/ou prendre des mesures pour résoudre le problème. ![Recommandations dans le Centre de sécurité Azure][5]

Afficher l’état d’intégrité et de sécurité de vos ressources via l’**Intégrité des ressources** :

1.	Retournez au panneau **Centre de sécurité**.
2.	La mosaïque **Intégrité des ressources** contient des indicateurs de l’état de sécurité pour les **machines virtuelles**, la **mise en réseau**, **SQL**, et les **applications**.
3.	Pour plus d’informations, sélectionnez **Machines virtuelles**.
4.	Le panneau **Machines virtuelles** affiche un résumé présentant l’état des logiciels anti-programme malveillant, les mises à jour du système, les redémarrages et les règles de base de vos machines virtuelles.
5.	Sélectionnez un élément dans les **RECOMMANDATIONS POUR MACHINES VIRTUELLES** pour afficher des informations supplémentaires et/ou configurer les contrôles nécessaires.
6.	Affichez les informations supplémentaires pour des machines virtuelles spécifiques. ![La mosaïque d’intégrité des ressources dans le Centre de sécurité Azure][6]

Afficher des **Alertes de sécurité** :

1.	Retournez dans le panneau **Centre de sécurité** et sélectionnez la vignette **Alertes de sécurité**. Une liste d’alertes s’affiche dans le panneau **Alertes de sécurité**. Les alertes sont générées par l’analyse de vos journaux de sécurité et de l’activité réseau par le Centre de sécurité. Des alertes de solutions de partenaires intégrées sont également incluses. ![Alertes de sécurité dans le Centre de sécurité Azure][7]

2.	Sélectionnez une alerte pour afficher des informations supplémentaires. ![Détails des alertes de sécurité dans le Centre de sécurité Azure][8]

Affichez l'intégrité de vos **solutions de partenaires** :

1. Retournez au panneau **Centre de sécurité**. La vignette **Solutions de partenaires** permet de contrôler en un coup de œil l'intégrité de vos solutions de partenaires intégrées à votre abonnement Azure.
2. Sélectionnez la vignette **Solutions de partenaires**. Un panneau s'ouvre et affiche la liste de vos solutions de partenaires connectées au Centre de sécurité. ![Solutions de partenaires][9]

3. Sélectionnez une solution de partenaire. Dans cet exemple, sélectionnons la solution **F5-WAF2**. Un panneau s'ouvre et affiche l'état de la solution partenaire et des ressources associées à cette solution. Sélectionnez **Solution console** pour ouvrir l’expérience de gestion du partenaire pour cette solution. ![Détail de la solution partenaire][10]

L’**ETAT** d'une solution de partenaire peut être :

- Sain (vert) : aucun problème d'intégrité
- Défectueux (rouge) : problème d'intégrité nécessitant une action immédiate
- Arrêté (orange) : la solution ne transmet plus son état d'intégrité
- Inconnu (orange) : l'intégrité de la solution est inconnu pour l'instant en raison d'un échec du processus d'ajout d'une nouvelle ressource à la solution existante
- Non signalé (gris) : la solution n'a encore rien signalé ; la solution peut affiché l’état Non signalé si elle vient d'être connectée et est toujours en cours de déploiement


## Étapes suivantes
Ce document vous a présenté les composants de surveillance de sécurité et de gestion des stratégies de sécurité dans le Centre de sécurité. Pour en savoir plus, consultez les articles suivants :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md) -- Découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png

<!---HONumber=AcomDC_0413_2016-->