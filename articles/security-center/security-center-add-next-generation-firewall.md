<properties
   pageTitle="Ajouter un pare-feu de nouvelle génération dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du Centre de sécurité **Azure Ajouter un pare-feu de nouvelle génération**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Ajouter un pare-feu de nouvelle génération dans le Centre de sécurité Azure

Le Centre de sécurité Azure peut vous recommander l’ajout d’un pare-feu de nouvelle génération d’un partenaire Microsoft afin de renforcer vos protections de sécurité. Ce document inclut un exemple vous expliquant comment procéder.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Implémenter la recommandation

1. Dans le panneau **Recommandations**, sélectionnez **Ajouter un pare-feu de nouvelle génération**. ![Ajouter un pare-feu de nouvelle génération][1]

2. Dans le panneau **Ajouter un pare-feu de nouvelle génération**, sélectionnez un point de terminaison. ![Sélectionner un point de terminaison][2]

3. Un second panneau **Ajouter un pare-feu de nouvelle génération** s’ouvre. Vous pouvez choisir d'utiliser une solution existante le cas échéant, ou en créer une. Dans cet exemple, il n'existe aucune solution existante, et nous allons donc en créer un nouveau pare-feu de nouvelle génération. ![Créer une solution de pare-feu de nouvelle génération][3]

4. Pour créer un pare-feu de nouvelle génération, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous allons sélectionner **Check Point**. ![Sélectionner une solution de pare-feu de nouvelle génération][4]

5. Le panneau **Check Point** s’ouvre et affiche des informations sur la solution du partenaire. Sélectionnez **Créer** dans le panneau d’informations. ![Panneau d’informations du pare-feu][5]

6. Le panneau **Créer une machine virtuelle** s’ouvre. Vous pouvez y entrer les informations nécessaires pour créer une machine virtuelle qui exécutera le pare-feu de nouvelle génération. Suivez les étapes et fournissez les informations requises concernant le pare-feu de nouvelle génération. Sélectionnez OK pour appliquer les modifications. ![Créer une machine virtuelle pour exécuter un pare-feu de nouvelle génération][6]

## Étapes suivantes

Ce document vous a montré comment implémenter la recommandation du Centre de sécurité « Add a Next Generation Firewall » (Ajouter un pare-feu de nouvelle génération). Pour en savoir plus sur les pare-feu de nouvelle génération et la solution du partenaire Check Point, consultez les rubriques suivantes :

- [Pare-feu de nouvelle génération](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Pour plus d’informations sur Security Center, consultez :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png

<!---HONumber=AcomDC_0720_2016-->