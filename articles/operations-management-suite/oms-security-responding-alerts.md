---
title: Surveiller et répondre aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite | Microsoft Docs
description: Ce document vous aide à utiliser l’option d’informations sur les menaces disponible dans la solution de sécurité et d’audit d’OMS afin de surveiller et de répondre aux alertes de sécurité.
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: operations-management-suite
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: yurid

---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Surveiller et répondre aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite
Ce document vous aide à utiliser l’option d’informations sur les menaces disponible dans la solution de sécurité et d’audit d’OMS afin de surveiller et de répondre aux alertes de sécurité.

## <a name="what-is-oms?"></a>Qu’est-ce qu’OMS ?
Microsoft Operations Management Suite (OMS) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud. Pour plus d’informations sur OMS, lisez l’article [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Informations sur les menaces
Dans un environnement d’entreprise où les utilisateurs ont un accès étendu au réseau et utilisent une variété de périphériques pour se connecter aux données d’entreprise, il est impératif que vous puissiez surveiller activement vos ressources et répondre rapidement aux incidents de sécurité. Ceci est particulièrement important du point de vue du cycle de vie de la sécurité, car certaines menaces de cybersécurité peuvent ne pas déclencher d’alertes ou d’activités suspectes pouvant être identifiées par les contrôles techniques de sécurité traditionnels. 

Grâce à l’option **Informations sur les menaces** disponible dans la solution de sécurité et d’audit d’OMS, les administrateurs informatiques peuvent détecter les menaces de sécurité pour l’environnement, par exemple, identifier si un ordinateur fait partie d’un [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Un ordinateur peut devenir un nœud d’un botnet si un pirate installe de manière illicite un programme malveillant qui connecte secrètement cet ordinateur à la commande et au contrôle. Cette option peut également identifier les menaces potentielles provenant de canaux de communication obscurs, tel que le [Darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Pour générer ces informations sur les menaces, la solution de sécurité et d’audit d’OMS utilise des données provenant de nombreuses sources au sein de Microsoft. La solution de sécurité et d’audit d’OMS utilisera ces données afin d’identifier les menaces potentielles pour votre environnement.

Le panneau Informations sur les menaces est composé de trois options principales :

* Servers with outbound malicious traffic (Serveurs avec trafic sortant malveillant)
* Detected threats types (Types de menaces détectés)
* Threat intelligence map (Carte d’informations sur les menaces)

> [!NOTE]
> pour avoir une vue d’ensemble de toutes ces options, consultez [Getting started with Operations Management Suite Security and Audit Solution](oms-security-getting-started.md)(Prise en main de la solution de sécurité et d’audit d’Operations Management Suite).
> 
> 

### <a name="responding-to-security-alerts"></a>Répondre à des alertes de sécurité
Le processus de [réponse aux incidents de sécurité](https://technet.microsoft.com/library/cc512623.aspx) contient une étape consistant à identifier la gravité du ou des systèmes compromis. Pendant cette phase, vous devez effectuer les tâches suivantes :

* Déterminer la nature de l’attaque
* Déterminer le point d’origine de l’attaque
* Déterminer le but de l’attaque L’attaque était-elle spécifiquement dirigée contre votre entreprise pour obtenir certaines informations à son sujet ou était-elle aléatoire ?
* Identifier les systèmes qui ont été compromis
* Identifier les fichiers consultés et déterminer leur sensibilité

Les **Informations sur les menaces** de la solution de sécurité et d’audit d’OMS vous aideront à effectuer ces tâches. Pour accéder à l’option **Informations sur les menaces** , suivez les étapes ci-dessous :

1. Dans le tableau de bord principal de **Microsoft Operations Management Suite**, cliquez sur la vignette **Sécurité et audit**.
   
    ![Sécurité et audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. Dans le tableau de bord **Sécurité et audit**, les options **Intelligence des menaces** se situent à droite, comme indiqué ci-dessous :
   
    ![Informations sur les menaces](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Ces trois mosaïques vous donnent une vue d’ensemble des menaces actuelles. Dans la partie **Servers with outbound malicious traffic** (Serveurs avec trafic sortant malveillant), vous pouvez savoir si l’un des ordinateurs que vous surveillez (au sein ou en dehors de votre réseau) génère du trafic malveillant sur Internet. 

La vignette **Types des menaces détectés** montre un résumé des menaces actuellement « dans la nature ». Cliquez sur cette vignette pour afficher plus de détails sur ces menaces, comme indiqué ci-dessous :

![Detected threat types (Types de menaces détectés)](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Vous pouvez obtenir plus d’informations sur chacune des menaces en cliquant dessus. L’exemple ci-dessous présente un Botnet plus en détail :

![plus de détails sur une menace](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Comme expliqué au début de cette section, ces informations peuvent s’avérer précieuses pour répondre aux incidents. Elles peuvent également être utiles dans le cadre d’une investigation, si vous devez rechercher la source de l’attaque, identifier le système compromis et établir une chronologie. Dans ce rapport, vous pouvez facilement identifier certains détails importants sur l’attaque, tels que : la source de l’attaque, l’adresse IP locale qui a été compromise et l’état actuel de la session de connexion. 

La carte d’**informations sur les menaces** vous indique les endroits du monde où du trafic malveillant est actuellement observé. Cette carte comporte des flèches orange (trafic entrant) et des flèches rouges (trafic sortant), qui indiquent la direction du trafic. Cliquez sur l’une de ces flèches pour afficher le type de menace et la direction du trafic, comme indiqué ci-dessous :

![carte d’informations sur les menaces](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à utiliser l’option **Informations sur les menaces** de la solution de sécurité et d’audit d’OMS pour répondre aux alertes de sécurité. Pour plus d’informations sur la sécurité OMS, consultez les articles suivants :

* [Présentation - Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Getting started with Operations Management Suite Security and Audit Solution](oms-security-getting-started.md)
* [Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-monitoring-resources.md)

<!--HONumber=Oct16_HO2-->


