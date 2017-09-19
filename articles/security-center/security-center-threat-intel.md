---
title: "(Threat Intelligence in Azure Security Center) Informations sur les menaces dans Azure Security Center | Microsoft Docs"
description: "Ce document vous aide à utiliser la fonctionnalité d’informations sur les menaces dans Azure Security Center pour identifier les menaces potentielles sur vos machines virtuelles et ordinateurs."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Informations sur les menaces dans Azure Security Center
Ce document vous aide à utiliser les informations sur les menaces d’Azure Security Center pour adresser les problèmes relatifs à la sécurité.

## <a name="what-is-threat-intelligence"></a>Que sont les informations sur les menaces ?
Grâce à l’option Informations sur les menaces disponible dans Security Center, les administrateurs informatiques peuvent détecter les menaces de sécurité pour l’environnement, par exemple, identifier si un ordinateur fait partie d’un botnet. Un ordinateur peut devenir un nœud d’un botnet si un pirate installe de manière illicite un programme malveillant qui connecte secrètement cet ordinateur à la commande et au contrôle. Cette option peut également identifier les menaces potentielles provenant de canaux de communication obscurs, tel que le Darknet.

Pour générer ces informations sur les menaces, Security Center utilise des données provenant de nombreuses sources au sein de Microsoft. Security Center utilisera ces données afin d’identifier les menaces potentielles pour votre environnement. Le panneau Informations sur les menaces est composé de trois options principales :

- Detected threat types (Types de menaces détectés)
- Origine de la menace
- Threat intelligence map (Carte d’informations sur les menaces)


## <a name="when-should-i-use-threat-intelligence"></a>Quand dois-je utiliser les informations sur les menaces ?
Le [processus de réponse aux incidents de sécurité](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) contient une étape consistant à identifier la gravité du ou des systèmes compromis. Pendant cette phase, vous devez effectuer les tâches suivantes :

- Déterminer la nature de l’attaque
- Déterminer le point d’origine de l’attaque
- Déterminer le but de l’attaque L’attaque était-elle spécifiquement dirigée contre votre entreprise pour obtenir certaines informations à son sujet ou était-elle aléatoire ?
- Identifier les systèmes qui ont été compromis
- Identifiez les fichiers consultés et déterminez leur sensibilité. Vous pouvez utiliser les informations sur les menaces de Security Center pour vous aider dans ces tâches. 

## <a name="how-to-access-the-threat-intelligence"></a>Comment accéder aux informations sur les menaces ?
Pour visualiser les informations sur les menaces actuelles pour votre environnement, vous devez d’abord sélectionner l’espace de travail où résident vos informations. Si vous ne disposez pas de plusieurs espaces de travail, vous ne verrez pas le sélecteur d’espace de travail, et vous accéderez directement au tableau de bord **Informations sur les menaces**. Pour accéder au tableau de bord d’informations sur les menaces, suivez les étapes ci-dessous :

1.  Ouvrez le tableau de bord **Security Center**.
2.  Dans le volet gauche, dans **Détection**, cliquez sur **Informations sur les menaces**. Le tableau de bord **Informations sur les menaces** s’affiche.

    ![Informations sur les menaces](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Si **METTRE À NIVEAU LE PLAN** apparaît dans la dernière colonne, cet espace de travail utilise un abonnement gratuit. Vous devez le passer au niveau standard pour utiliser cette fonctionnalité. Si NÉCESSITE MISE À JOUR apparaît, vous devez mettre à jour [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour utiliser cette fonctionnalité. Pour en savoir plus sur le plan de tarification, consultez la tarification dans Azure Security Center. 
    > 
3. Si vous disposez de plus d’un espace de travail à analyser, privilégiez l’analyse en fonction de la colonne **IP MALVEILLANT**. Elle indique le nombre actuel d’adresses IP malveillantes dans cet espace de travail. Sélectionnez l’espace de travail que vous souhaitez utiliser. Le tableau de bord **Informations sur les menaces** s’affiche.

    ![Informations sur les menaces](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Ce tableau de bord est divisé en quatre vignettes :
    * **Types de menace** : récapitule les types de menaces détectés dans l’espace de travail sélectionné.
    * **Pays d’origine** : agrège la quantité de trafic en fonction de l’emplacement d’origine.
    * **Emplacement de la menace** : vous aide à identifier les emplacements dans le monde qui communiquent actuellement avec votre environnement. Cette carte comporte des flèches oranges (trafic entrant) et des flèches rouges (trafic sortant), qui indiquent la direction du trafic. Cliquez sur l’une de ces flèches pour afficher le type de menace et la direction du trafic.
    * **Détails de la menace** : montre plus de détails sur les menaces que vous avez sélectionnées dans le mappage.

Quelle que soit la vignette d’option choisie, le tableau de bord qui s’affiche se base sur la requête [Recherche dans les journaux](https://docs.microsoft.com/azure/security-center/security-center-search). La seule différence est le type de requête et son résultat.

### <a name="threat-types"></a>Types de menaces
Après avoir cliqué sur la vignette **Types de menace**, le tableau de bord **Recherche dans les journaux** s’affiche. Les options de filtre se situent sur la gauche, et les résultats de requêtes sur la droite.

![Recherche d’informations sur les menaces](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Le résultat de la requête affiche les menaces et les agrège par nom. Vous pouvez utiliser le volet gauche pour sélectionner l’attribut que vous souhaitez filtrer. Par exemple, pour voir uniquement les menaces qui sont actuellement connectées aux machines, sélectionnez **Connecté** dans **ÉTAT DE LA SESSION**, puis cliquez sur le bouton **Appliquer**.

![état de la session](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Pour les machines virtuelles Azure, le tableau de bord d’informations sur les menaces n’affiche que les données de réseau filtrées par l’agent. Les types de données suivants sont également utilisés par les informations sur les menaces :

- Données CEF (Type = CommonSecurityLog)
- WireData (Type = WireData)
- Journaux IIS (Type = W3CIISLog)
- Pare-feu Windows (Type = WindowsFirewall)
- Événements DNS (Type = DnsEvents)


## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez découvert comment utiliser les informations sur les menaces dans Security Center pour vous aider à identifier une activité suspecte. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center. 
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.


