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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Prise en main du Centre de sécurité Azure

Ce document vous aide à prendre rapidement en main le Centre de sécurité Azure en vous guidant à travers les composants de surveillance de sécurité et de gestion des stratégies de sécurité et vers les étapes suivantes.

> [AZURE.NOTE]Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document constitue une présentation du service et utilise un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Composants requis

Pour utiliser le Centre de sécurité Azure, vous devez disposer d’un abonnement à Microsoft Azure. Le Centre de sécurité Azure est activé avec votre abonnement. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial).

Le Centre de sécurité Azure est accessible à partir du [portail Azure](http://azure.microsoft.com/features/azure-portal/). Pour en savoir plus, consultez la [documentation sur le portail](https://azure.microsoft.com/documentation/services/azure-portal/).


## Accès au Centre de sécurité Azure

Dans le portail, procédez comme suit pour accéder au Centre de sécurité Azure :

1. Sélectionnez **Parcourir** et faites défiler jusqu’à l’option **Centre de sécurité**. ![][1]

2. Sélectionnez **Centre de sécurité**. Le panneau **Centre de sécurité** s’ouvre.
3. Pour accéder facilement au panneau **Centre de sécurité** à l’avenir, sélectionnez l’option **Épingler le panneau au tableau de bord** (en haut à droite). ![][2]

## Utilisation du Centre de sécurité Azure

Configurez une **stratégie** de sécurité pour vos abonnements :

4. Cliquez sur la mosaïque **Stratégie de sécurité** dans le panneau **Centre de sécurité**.
5. Dans le panneau **Stratégie de sécurité – Définir une stratégie par abonnement (Define policy per subscription)**, sélectionnez un abonnement.
6. Dans le panneau **stratégie de sécurité**, activez l’option **Collecte des données** pour collecter automatiquement les journaux. L’activation de l’option **Collecte des données** activera également l’extension de la surveillance sur toutes les machines virtuelles actuelles et nouvelles dans l’abonnement.
7. Cliquez sur **Choose storage accounts (Sélectionner des comptes de stockage)**. Pour chaque région dans lesquelles des machines virtuelles s’exécutent, vous devez choisir le compte de stockage où stocker les données collectées à partir de ces machines virtuelles. Si vous ne choisissez pas un compte de stockage pour chaque région, un compte sera créé. Pour des raisons de sécurité, les données collectées sont isolées logiquement des autres données clients.
8. Activez les **Recommandations** à afficher dans le cadre de votre stratégie de sécurité. Exemples :

  - L’activation des **mises à jour du système** permet d’analyser toutes les machines virtuelles prises en charge pour les mises à jour du système d’exploitation manquantes.
  - L’activation des **règles de base** permet d’analyser les machines virtuelles prises en charge pour identifier toute configuration du système d’exploitation pouvant rendre la machine virtuelle plus vulnérable aux attaques.

![][3]

Afficher des **Recommandations** :

9. Retournez dans le panneau **Centre de sécurité** et cliquez sur la mosaïque **Recommandations**.
10.	Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Quand des failles de sécurité potentielles sont identifiées, une recommandation y est affichée.
11.	Cliquez sur chaque recommandation pour afficher plus d’informations et/ou prendre des mesures pour résoudre le problème.

![][4]

Afficher l’état d’intégrité et de sécurité de vos ressources via l’**Intégrité des ressources** :

12.	Retournez au panneau **Centre de sécurité**.
13.	La mosaïque **Intégrité des ressources** contient des indicateurs de l’état de sécurité pour les **machines virtuelles**, la **mise en réseau**, **SQL**, et les **applications**.
14.	Pour plus d’informations, sélectionnez **Machines virtuelles**.
15.	Le panneau **Machines virtuelles** affiche un résumé de l’état contenant notamment les logiciels anti-programme malveillant, les mises à jour du système, le redémarrage et les règles de base de vos machines virtuelles.
16.	Sélectionnez un élément dans les **mesures préventives** pour afficher des informations supplémentaires et/ou configurer des contrôles nécessaires
17.	Affichez les informations supplémentaires pour des machines virtuelles spécifiques.

![][5]

Afficher des **Alertes de sécurité** :

19.	Retournez dans le panneau **Centre de sécurité** et cliquez sur la mosaïque **Alertes de sécurité**.
20.	Une liste d’alertes s’affiche dans le panneau **Alertes de sécurité**. Les alertes ont été détectées par l’analyse de vos journaux de sécurité et de l’activité réseau par le Centre de sécurité Azure. Des alertes de solutions de partenaires intégrées sont également incluses. ![][6]

21.	Cliquez sur une alerte pour afficher des informations supplémentaires.

  ![][7]

## Étapes suivantes
Ce document vous a présenté les composants de surveillance de sécurité et de gestion des stratégies de sécurité dans le Centre de sécurité Azure. Pour en savoir plus, consultez les articles suivants :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) – Découvrez comment configurer des stratégies de sécurité
- [Implémentation de recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md) – Découvrez comment des recommandations peuvent vous aider à protéger vos ressources Azure
- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) – Découvrez comment surveiller l’intégrité de vos ressources Azure
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) – Découvrez les dernières informations sur la sécurité Azure

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_1210_2015-->