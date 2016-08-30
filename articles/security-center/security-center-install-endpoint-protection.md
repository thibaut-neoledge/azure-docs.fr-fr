<properties
   pageTitle="Installer Endpoint Protection dans Azure Security Center | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation d’Azure Security Center **Installer Endpoint Protection**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# Installer Endpoint Protection dans Azure Security Center

Azure Security Center vous recommande de configurer un logiciel anti-programme malveillant sur vos machines virtuelles Azure, s’il n’est pas encore activé. Cette recommandation s'applique uniquement aux machines virtuelles Windows.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Implémenter la recommandation

1. Dans le panneau **Recommandations**, sélectionnez **Installer Endpoint Protection**. ![Sélectionner Installer Endpoint Protection][1]

2. Le panneau **Installer Endpoint Protection** s’ouvre en affichant une liste de machines virtuelles sans logiciel anti-programme malveillant activé. Dans cette liste, sélectionnez les machines virtuelles sur lesquelles vous souhaitez installer un logiciel anti-programme malveillant et cliquez sur **Installer sur les machines virtuelles**. ![Sélectionner les machines virtuelles sur lesquelles installer le logiciel anti-programme malveillant][2]

3. Le panneau **Sélectionner Endpoint Protection** s’ouvre et vous permet de sélectionner la solution anti-programme malveillant à utiliser. Dans cet exemple, nous allons sélectionner **Microsoft Antimalware**. ![Sélectionner Endpoint Protection][3]

4. Des informations supplémentaires sur la solution anti-programme malveillant s’affichent. Sélectionnez **Créer**. ![Créer une solution anti-programme malveillant][4]

5. Entrez les paramètres de configuration nécessaires dans le panneau **Ajouter une extension**, puis sélectionnez **OK**. Pour en savoir plus sur les paramètres de configuration, consultez [Configuration d’un logiciel anti-programme malveillant par défaut et personnalisée](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../azure-security-antimalware.md) est maintenant activé sur les machines virtuelles sélectionnées.

## Voir aussi

Cet article vous a montré comment implémenter la recommandation de Security Center « Installer Endpoint Protection ». Pour en savoir plus sur l'activation d'un logiciel anti-programme malveillant dans Azure, consultez les sections suivantes :

- [Microsoft Antimalware pour Cloud Services et Virtual Machines](../azure-security-antimalware.md) -- Découvrez comment déployer Microsoft Antimalware.

Pour plus d’informations sur Security Center, consultez :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
- [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]: ./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]: ./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]: ./media/security-center-install-endpoint-protection/create-antimalware-solution.png

<!---HONumber=AcomDC_0817_2016-->