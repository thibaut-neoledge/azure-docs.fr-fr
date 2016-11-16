---
title: "Rapport d’informations sur les menaces d’Azure Security Center | Microsoft Docs"
description: "Ce document explique comment utiliser les rapports d’information sur les menaces d’Azure Security Center pendant un examen pour trouver plus d’informations sur une alerte de sécurité."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a80938106a54d9567bd15a8067c4b989b2eeedcc


---
# <a name="azure-security-center-threat-intelligence-report"></a>Rapport d’informations sur les menaces d’Azure Security Center
Ce document explique comment trouver des informations supplémentaires sur une menace ayant généré une alerte de sécurité dans les rapports d’informations sur les menaces d’Azure Security Center.

## <a name="what-is-a-threat-intelligence-report"></a>Qu’est-ce qu’un rapport d’informations sur les menaces ?
La détection des menaces d’Azure Security Center fonctionne en surveillant les informations de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires connectées. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces. Ce processus fait partie des [fonctionnalités de détection](security-center-detection-capabilities.md) d’Azure Security Center. 

Lorsqu’Azure Security Center identifie une menace, il déclenche une [alerte de sécurité](security-center-managing-and-responding-alerts.md), qui contient des informations détaillées sur un événement particulier, y compris des suggestions de correction. Pour aider les équipes de réponse aux incidents à examiner et à corriger les menaces, Azure Security Center inclut un rapport qui contient des informations sur la menace détectée, y compris des informations telles que les suivantes : 

* Identité ou associations des attaquants (si cette information est disponible)
* Objectifs des attaquants
* Campagnes d’attaque actuelles et historiques (si cette information est disponible)
* Tactiques, outils et procédures des attaquants
* Indicateurs de compromission (IoC) associés, tels que les URL et les hachages de fichier
* Victimologie, c’est-à-dire la prévalence industrielle et géographique, afin de vous aider à déterminer si vos ressources Azure sont exposées au risque
* Informations d’atténuation et de correction

> [!NOTE]
> La quantité d’informations dans un rapport donné peut varier. Le niveau de détail est basé sur l’activité et la prévalence du programme malveillant.
> 
> 

Azure Security Center propose trois types de rapports sur les menaces, qui peuvent varier en fonction de l’attaque. Les rapports disponibles sont les suivants :

* **Rapport sur le groupe d’activités** : fournit des informations détaillées sur les attaquants, leurs objectifs et leurs tactiques.
* **Rapport sur la campagne** : se concentre sur les détails des campagnes d’attaque spécifiques. 
* **Rapport de synthèse sur la menace** : couvre tous les éléments des deux rapports précédents.

Ce type d’information est très utile pendant le processus de [réponse aux incidents](security-center-incident-response.md), au cours duquel des examens sont effectués en continu afin de comprendre la source de l’attaque, les motivations de l’attaquant et les solutions d’atténuation de ce problème à l’avenir. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Comment accéder au rapport d’informations sur les menaces ?
Vous pouvez connaître vos alertes actuelles en consultant la vignette **Alertes de sécurité** . Accédez au Portail Azure et suivez les étapes ci-après pour obtenir plus d’informations sur chaque alerte :

1. La vignette **Alertes de sécurité** est affichée dans le tableau de bord Centre de sécurité.
2. Cliquez sur la mosaïque pour ouvrir le panneau **Alertes de sécurité** qui contient plus de détails sur les alertes et cliquez sur une alerte de sécurité pour en savoir plus à son sujet.
   
    ![Alertes de sécurité](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Dans ce cas, le panneau **Processus suspect exécuté** affiche les détails de l’alerte, comme illustré dans la figure ci-dessous :
   
    ![Détails des alertes de sécurité](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. La quantité d’informations disponibles pour chaque alerte de sécurité varie selon le type d’alerte. Le champ **RAPPORTS** contient un lien vers le rapport d’informations sur les menaces. Lorsque vous cliquez dessus, une autre fenêtre de navigateur s’affiche. Elle contient le fichier PDF.
   
   ![Sélection du stockage](./media/security-center-threat-report/security-center-threat-report-fig3.png)

À ce stade, vous pouvez télécharger le PDF de ce rapport et en savoir plus sur le problème de sécurité détecté et prendre des mesures en fonction des informations fournies.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris en quoi les rapports d’informations sur les menaces d’Azure Security Center peuvent vous être utiles lors de l’examen des alertes de sécurité. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Utilisation d’Azure Security Center pour la réponse aux incidents](security-center-incident-response.md)
* [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md). découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md). découvrez comment gérer et résoudre les alertes de sécurité.
* [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md)
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.




<!--HONumber=Nov16_HO2-->


