<properties
   pageTitle="Présentation du Centre de sécurité Azure | Microsoft Azure"
   description="Découvrez le Centre de sécurité Azure, ses fonctionnalités principales et son fonctionnement."
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
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Présentation du Centre de sécurité Azure

Découvrez le Centre de sécurité Azure, ses fonctionnalités principales et son fonctionnement.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document présente le service à l’aide d’un exemple de déploiement.

## Qu’est-ce que le Centre de sécurité Azure ?
 Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

##	Fonctionnalités clés
 Le Centre de sécurité propose des fonctions de prévention, de détection et de réponse aux menaces conviviales et efficaces, intégrées dans Azure. Ses fonctionnalités principales sont les suivantes :

| | |
|----- |-----|
| Prévention | Surveille l’état de sécurité de vos ressources Azure |
| | Définit des stratégies pour vos abonnements Azure en fonction des exigences en matière de sécurité de votre société, du type d’application que vous utilisez et de la confidentialité de vos données |
| | Utilise des recommandations de sécurité basées sur des stratégies pour guider les propriétaires de services dans le processus d’implémentation de contrôles nécessaires. |
| | Déploie rapidement des services de sécurité et les appliances de Microsoft et de partenaires |
| Détection |Collecte et analyse automatiquement des données de sécurité à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. |
| | Exploite des informations globales concernant des menaces provenant de produits et services Microsoft, de centres contre la cyber-criminalité et de réponses aux incidents et de sources externes |
| | Applique des analyses avancées, notamment l’apprentissage automatique (Machine Learning) et des études du comportement |
| Réponse | Fournit des notifications d’incidents/alertes de sécurité hiérarchisées |
| | Donne des informations sur la source de l’attaque et les ressources affectés |
| | Suggère des manières d’empêcher l’attaque en cours et d’empêcher des attaques futures |

## Introduction pas à pas
 Le Centre de sécurité est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour y accéder, [connectez-vous au portail](https://portal.azure.com), sélectionnez **Parcourir** et faites défiler la page jusqu’à l’option **Centre de sécurité**, ou sélectionnez la mosaïque **Centre de sécurité** préalablement épinglée au tableau de bord du portail.

![Mosaïque de sécurité dans le portail Azure][1]

Dans le Centre de sécurité, vous pouvez définir des stratégies de sécurité, surveiller des configurations de sécurité et afficher des alertes de sécurité.

### Stratégies de sécurité

Vous pouvez définir des stratégies pour vos abonnements Azure en fonction des besoins de votre entreprise en termes de sécurité. Vous pouvez également les personnaliser selon les types d’applications que vous utilisez ou de la sensibilité des données dans chaque abonnement. Par exemple, les ressources utilisées pour le développement ou le test peuvent présenter des exigences de sécurité différentes de celles qui sont utilisées pour les applications de production. De même, les applications dont les données sont réglementées telles que les informations d’identification personnelle (PII)) peuvent nécessiter un niveau de sécurité plus élevé.

> [AZURE.NOTE] Pour modifier une stratégie de sécurité, vous devez être le propriétaire d’un abonnement, ou l’un de ses contributeurs.

Cliquez sur la mosaïque **Stratégie de sécurité** pour obtenir une liste de vos abonnements, puis choisissez un abonnement pour afficher les détails de la stratégie.

![Mosaïque de stratégie de sécurité][2]

**Collecte des données** (voir ci-dessus) active la collecte de données pour une stratégie de sécurité. L’activation de cette option offre les avantages suivants : - analyse quotidienne de toutes les machines virtuelles prises en charge pour la surveillance de la sécurité et les recommandations ; - collecte des événements de sécurité pour l’analyse et la détection des menaces.

**Afficher les recommandations pour :** (voir ci-dessus) vous permet de choisir les contrôles de sécurité que vous souhaitez surveiller et recommander selon les besoins en matière de sécurité des ressources au sein de l’abonnement.

### Recommandations de sécurité

 Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires. Voici quelques exemples :

- Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
- Configuration de groupes de sécurité réseau et de règles associées pour contrôler le trafic vers les machines virtuelles
- Approvisionnement de pare-feu d’applications web pour protéger vos applications web contre les attaques ciblées
- Déploiement de mises à jour système manquantes
- Correction des configurations de système d’exploitation qui ne correspondent pas aux recommandations

Cliquez sur la mosaïque **Recommandations** pour obtenir une liste de recommandations. Cliquez sur chaque recommandation pour afficher plus d’informations ou prendre des mesures pour résoudre le problème.

![Recommandations de sécurité dans le Centre de sécurité Azure][3]

### Intégrité des ressources

La mosaïque **Intégrité des ressources** affiche la posture de sécurité globale de votre environnement par type de ressource (machines virtuelles, applications web et autres ressources).

Sélectionnez un type de ressource dans la mosaïque **Intégrité des ressources** pour afficher plus d’informations, notamment une liste des vulnérabilités de sécurité potentielles qui ont été identifiées. (Dans l’exemple ci-dessous, l’option **Machines virtuelles** est sélectionnée.)

![Mosaïque d’intégrité des ressources][4]

### Alertes de sécurité

 Le Centre de sécurité collecte, analyse et intègre automatiquement les données du journal à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. Quand des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détections :

- Des machines virtuelles compromises qui communiquent avec des adresses IP connues comme étant malveillantes
- Des programmes malveillants avancés qui sont détectés à l’aide du rapport d’erreurs Windows
- Des attaques par force brute contre des machines virtuelles
- Des alertes de sécurité de logiciels anti-programme malveillant et de pare-feu intégrés

Un clic sur la mosaïque **Alertes de sécurité** affiche une liste d’alertes prioritaires.

![Alertes de sécurité][5]

Si vous sélectionnez une alerte, des informations supplémentaires sur l’attaque et des suggestions sur la manière d’y remédier s’affichent.

![Détails des alertes de sécurité][6]

## Prise en main
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Le Centre de sécurité est activé avec votre abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

 Le Centre de sécurité est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour en savoir plus, consultez la [documentation sur le portail](https://azure.microsoft.com/documentation/services/azure-portal/).

La rubrique [Prise en main du Centre de sécurité Azure](security-center-get-started.md) vous guide à travers les composants de surveillance de sécurité et de gestion des stratégies de sécurité du Centre de sécurité.

## Étapes suivantes
Ce document vous a donné un aperçu du Centre de sécurité, de ses fonctions clés et de sa mise en route. Pour en savoir plus, consultez les articles suivants :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [FAQ du Centre de sécurité Azure](security-center-faq.md) : FAQ concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_0211_2016-->