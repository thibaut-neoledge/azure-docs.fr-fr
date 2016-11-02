<properties
   pageTitle="Appliquer les mises à jour système dans Azure Security Center | Microsoft Azure"
   description="Ce document vous montre comment implémenter les recommandations d’Azure Security Center **Appliquer les mises à jour système** et **Redémarrer après l’application des mises à jour système**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# Appliquer les mises à jour système dans Azure Security Center

Azure Security Center recherche quotidiennement les mises à jour manquantes du système d’exploitation sur les machines virtuelles Windows et Linux. Security Center récupère une liste des mises à jour de sécurité et critiques disponibles dans Windows Update ou WSUS (Windows Server Update Services), selon le service configuré sur les machines virtuelles Windows. Security Center recherche également les dernières mises à jour dans les systèmes Linux. S’il manque une mise à jour système sur votre machine virtuelle, Security Center vous recommande de l’appliquer.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Implémenter la recommandation

1. Dans le panneau **Recommandations**, sélectionnez **Appliquer les mises à jour système**. ![Appliquer des mises à jour système][1]

2. Le panneau **Appliquer les mises à jour système** s’ouvre en affichant une liste des mises à jour système manquantes sur les machines virtuelles. Sélectionnez une machine virtuelle. ![Sélectionner une machine virtuelle][2]

3. Un panneau s’ouvre et affiche une liste des mises à jour manquantes sur cette machine. Sélectionnez une mise à jour système. Dans cet exemple, sélectionnons KB3156016. ![Mises à jour de sécurité manquantes][3]

4. Suivez les étapes du panneau **Mise à jour de sécurité** pour appliquer la mise à jour manquante. ![Mise à jour de sécurité][4]

## Redémarrer après l’application des mises à jour système

5. Retournons au panneau **Recommandations**. Une fois que vous avez appliqué les mises à jour système, une nouvelle entrée est générée, appelée **Redémarrer après l’application des mises à jour système**. Cette entrée vous permet de savoir que vous devez redémarrer la machine virtuelle pour terminer le processus d’application des mises à jour système. ![Redémarrer après l’application des mises à jour système][5]

6. Sélectionnez **Redémarrer après l’application des mises à jour système**. Cette opération ouvre le panneau **Un redémarrage est en attente pour terminer les mises à jour système** qui affiche une liste de machines virtuelles que vous devez redémarrer pour terminer le processus d’application des mises à jour système. ![Redémarrage en attente][6]

Redémarrez la machine virtuelle à partir d’Azure pour terminer le processus.

## Voir aussi

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]: ./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png

<!---HONumber=AcomDC_0803_2016-->