---
title: Informations sur les menaces dans Azure Security Center | Microsoft Docs
description: "Découvrez comment utiliser la fonctionnalité d’informations sur les menaces dans Azure Security Center pour identifier les menaces potentielles sur vos machines virtuelles et ordinateurs."
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Informations sur les menaces dans Azure Security Center
Cet article vous aide à utiliser les informations sur les menaces d’Azure Security Center pour traiter les problèmes relatifs à la sécurité.

## <a name="what-is-threat-intelligence"></a>Que sont les informations sur les menaces ?
Grâce à l’option Informations sur les menaces disponible dans Security Center, les administrateurs informatiques peuvent détecter les menaces de sécurité pour l’environnement. Par exemple, ils peuvent déterminer si un ordinateur particulier fait partie d’un botnet. Un ordinateur peut devenir un nœud d’un botnet si un pirate installe de manière illicite un programme malveillant qui connecte secrètement l’ordinateur à la commande et au contrôle. Les informations sur les menaces peuvent également identifier les menaces potentielles provenant de canaux de communication obscurs, tel que le dark web.

Pour générer ces informations sur les menaces, Security Center utilise des données provenant de nombreuses sources au sein de Microsoft. Security Center utilise ces données afin d’identifier les menaces potentielles pour votre environnement. Le volet **Informations sur les menaces** est composé de trois options principales :

- Detected threat types (Types de menaces détectés)
- Origine de la menace
- Threat intelligence map (Carte d’informations sur les menaces)


## <a name="when-should-you-use-threat-intelligence"></a>Quand devez-vous utiliser les informations sur les menaces ?
Le [processus de réponse aux incidents de sécurité](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) contient une étape consistant à identifier la gravité du ou des systèmes compromis. Pendant cette phase, vous devez effectuer les tâches suivantes :

- Déterminer la nature de l’attaque.
- Déterminer le point d’origine de l’attaque.
- Déterminer le but de l’attaque L’attaque était-elle dirigée contre votre entreprise pour obtenir certaines informations à son sujet ou était-elle aléatoire ?
- Identifier les systèmes qui ont été compromis.
- Identifier les fichiers consultés et déterminer leur sensibilité.

Vous pouvez utiliser des informations sur les menaces dans Security Center pour ces tâches. 

## <a name="access-the-threat-intelligence"></a>Accéder aux informations sur les menaces
Pour visualiser les informations sur les menaces actuelles pour votre environnement, vous devez d’abord sélectionner l’espace de travail où résident vos informations. Si vous ne disposez pas de plusieurs espaces de travail, vous ne verrez pas le sélecteur d’espace de travail, et vous accéderez directement au tableau de bord **Informations sur les menaces**. Pour accéder au tableau de bord :

1. Ouvrez le tableau de bord **Security Center**.

2. Dans le volet gauche, dans **Détection**, cliquez sur **Informations sur les menaces**. Le tableau de bord **Informations sur les menaces** s’affiche.

    ![Tableau de bord Informations sur les menaces](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Si la colonne située complètement à droite affiche **PLAN DE MISE À NIVEAU**, cet espace de travail utilise l’abonnement gratuit. Effectuez une mise à niveau vers le niveau Standard pour utiliser cette fonctionnalité. Si la colonne située complètement à droite affiche **MISE À JOUR REQUISE**, mettez à jour [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour utiliser cette fonctionnalité. Pour en savoir plus sur le plan de tarification, consultez la tarification dans Azure Security Center. 
    > 
3. Si vous avez plusieurs espaces de travail à examiner, hiérarchisez l’examen en fonction de la colonne **Adresse IP malveillante**. Elle indique le nombre actuel d’adresses IP malveillantes dans cet espace de travail. Sélectionnez l’espace de travail que vous souhaitez utiliser. Le tableau de bord **Informations sur les menaces** s’affiche.

    ![Informations sur les menaces](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Le tableau de bord est divisé en quatre vignettes :

    a.  **Types de menaces**. récapitule les types de menaces détectés dans l’espace de travail sélectionné.

    b.  **Pays d’origine**. Agrège la quantité de trafic en fonction de l’emplacement d’origine.

    c.  **Emplacement de la menace**. vous aide à identifier les emplacements dans le monde qui communiquent actuellement avec votre environnement. Dans le mappage indiqué, les flèches orange (entrant) et rouges (sortant) identifient le sens du trafic. Sélectionnez une de ces flèches pour afficher le type de menace et la direction du trafic.

    d.  **Détails de la menace**. Montre plus de détails sur les menaces que vous avez sélectionnées dans le mappage.

Quelle que soit la vignette d’option sélectionnée, le tableau de bord qui s’affiche se base sur la requête de recherche dans les journaux. Les seules différences sont le type de requête et le résultat.

### <a name="threat-types"></a>Types de menaces
Sélectionnez la vignette **Types de menace** pour ouvrir le tableau de bord **Recherche dans les journaux**. Les options de filtre s’affichent à gauche, et les résultats de la requête à droite.

![Recherche dans les journaux](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Le résultat de la requête affiche les menaces par nom. Vous pouvez utiliser le volet de gauche pour sélectionner l’attribut que vous souhaitez filtrer. Par exemple, pour afficher uniquement les menaces qui sont actuellement connectées aux machines, dans **SESSIONSTATE**, sélectionnez **Connecté** > **Appliquer**.

![État de la session](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Pour les machines virtuelles Azure, le tableau de bord **Informations sur les menaces** n’affiche que les données de réseau filtrées par l’agent. Les types de données suivants sont également utilisés par les informations sur les menaces :

- Données CEF (Type = CommonSecurityLog)
- WireData (Type = WireData)
- Journaux IIS (Type = W3CIISLog)
- Pare-feu Windows (Type = WindowsFirewall)
- Événements DNS (Type = DnsEvents)


## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez découvert comment utiliser les informations sur les menaces dans Security Center pour vous aider à identifier une activité suspecte. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Comprendre les alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center. 
* [FAQ du Centre de sécurité Azure](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

