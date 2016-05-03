<properties
   pageTitle="Gestion des solutions de partenaire dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous explique comment le Centre de sécurité Azure vous permet de surveiller en un clin d’œil l’intégrité de vos solutions de partenaires intégrées à votre abonnement Azure."
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
   ms.date="04/22/2016"
   ms.author="terrylan"/>

# Surveillance des solutions de partenaire avec le Centre de sécurité Azure

Ce document explique comment surveiller l’état d’intégrité de vos solutions de partenaire dans le Centre de sécurité Azure.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Qu’est-ce que le Centre de sécurité ?
 Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Surveillance des solutions de partenaire

La mosaïque **Solutions de partenaire** du panneau **Centre de sécurité** vous permet de surveiller en un clin d’œil l’intégrité de vos solutions de partenaire intégrées à votre abonnement Azure. ![Mosaïque Solutions de partenaire][1]

La mosaïque **Solutions de partenaire** affiche le nombre de solutions de partenaire et un récapitulatif de l’état de ces solutions.

L’**ÉTAT** d’une solution de partenaire peut être :

- Protégé (vert) : aucun problème d’intégrité.
- Défectueux (rouge) : problème d’intégrité nécessitant une action immédiate.
- Arrêté (orange) : état d’intégrité non signalé par la solution.
- Inconnu (orange) : intégrité de la solution inconnue pour l’instant en raison d’un échec de l’ajout d’une nouvelle ressource à la solution existante.
- Non signalé (gris) : état non encore signalé par la solution (ce qui peut être le cas si celle-ci vient d’être connectée et est en cours de déploiement).

Si aucune solution n’est intégrée à votre abonnement, la mosaïque indique qu’il n’y a aucune solution. Le fait de sélectionner la mosaïque **Solutions de partenaire** vous permet d’ouvrir le panneau **Recommandations** pour déployer des solutions de sécurité de partenaire. ![Aucune solution de partenaire][2]

Pour afficher l’intégrité de vos solutions de partenaire :

1. Sélectionnez la mosaïque **Solutions de partenaire**. Le panneau qui s’ouvre affiche la liste de vos solutions de partenaire connectées au Centre de sécurité. ![Solutions de partenaires][3]

2. Sélectionnez une solution de partenaire. Dans cet exemple, sélectionnons la solution **F5-WAF2**. Un panneau s'ouvre et affiche l'état de la solution partenaire et des ressources associées à cette solution. Sélectionnez **Console de solution** pour ouvrir l’expérience de gestion du partenaire pour cette solution. ![Détail de la solution partenaire][4]

3. Revenez au panneau **WAF2-F5** et sélectionnez **Associer**. Le panneau **Associer les applications** s’ouvre. Il vous permet de connecter des ressources à la solution de partenaire. ![Associer des ressources à la solution de partenaire][5]

## Étapes suivantes
Ce document vous a présenté la mosaïque **Solutions de partenaire** du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) : découvrez comment gérer les alertes de sécurité et y répondre.
- [FAQ du Centre de sécurité Azure](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

<!---HONumber=AcomDC_0427_2016-->