---
title: "Gestion des incidents de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document est conçu pour vous aider à utiliser les fonctionnalités de Azure Security Center pour gérer les incidents de sécurité."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9aeb08f0e4b4446fe8a4fc9ef7f4670ea08eacaa


---
# <a name="handling-security-incident-in-azure-security-center"></a>Gestion des incidents de sécurité dans Azure Security Center
Procéder au triage et à l’examen des alertes de sécurité peut prendre beaucoup de temps, même pour le plus compétent des analystes de sécurité, et bien souvent il est difficile de savoir par où commencer. En utilisant les [fonctions d’analyse](security-center-detection-capabilities.md) pour connecter les informations entre différentes [alertes de sécurité](security-center-managing-and-responding-alerts.md), le Centre de sécurité peut vous fournir une vue unique d’une campagne d’attaque et de l’ensemble des alertes associées. Vous pouvez donc rapidement savoir quelles actions l’attaquant a effectuées et quelles ressources ont été affectées.

Ce document explique comment utiliser la fonction d’alerte de sécurité dans le Centre de sécurité pour vous aider à gérer les incidents de sécurité.

## <a name="what-is-a-security-incident"></a>Qu’est-ce qu’un incident de sécurité ?
Dans le Centre de sécurité, un incident de sécurité est un regroupement de toutes les alertes d’une ressource correspondant à des modèles de [chaîne de destruction](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Les incidents apparaissent dans la vignette et le panneau [Alertes de sécurité](security-center-managing-and-responding-alerts.md) . Un incident affiche la liste des alertes associées, qui vous permet d’en savoir plus sur chaque occurrence.

## <a name="managing-security-incidents"></a>Gestion des incidents de sécurité
Vous pouvez connaître vos incidents de sécurité en cours en consultant la mosaïque Alertes de sécurité. Accédez au portail Azure et suivez les étapes ci-dessous pour en savoir plus sur chaque incident de sécurité :

1. La mosaïque **Alertes de sécurité** s’affiche dans le tableau de bord Centre de sécurité.
   
    ![Vignette Alertes de sécurité dans le Centre de sécurité](./media/security-center-incident/security-center-incident-fig1.png)
2. Cliquez sur cette mosaïque pour la développer si un incident de sécurité a été détecté. Il apparaîtra sous le graphique des alertes de sécurité comme indiqué ci-dessous :
   
   ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig2.png)
3. Notez que la description de l’incident de sécurité présente une autre icône que celle utilisée pour les autres alertes. Cliquez dessus pour afficher plus de détails sur cet incident.
   
   ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig3.png)
4. Sur le panneau de l’**incident**, vous trouverez davantage d’informations sur cet incident de sécurité, notamment sa description complète, son niveau de gravité (qui, dans ce cas, est élevé), son état actuel (dans ce cas, il est toujours *actif*, ce qui signifie que l’utilisateur n’a pris aucune mesure pour l’*ignorer*, ce qui peut se faire en cliquant sur l’incident avec le bouton droit dans le panneau **Alertes de sécurité**), la ressource attaquée (dans ce cas, *VM1*), les étapes de résolution de l’incident et, dans le panneau inférieur, les alertes qui ont été affichées au cours de l’incident. Si vous souhaitez obtenir plus d’informations sur chaque alerte, il vous suffit de cliquer dessus pour ouvrir un autre panneau, comme indiqué ci-dessous :
   
   ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig4.png)

Les informations sur ce panneau varient en fonction de l’alerte. Consultez [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) pour en savoir plus sur la gestion de ces alertes. Voici quelques considérations importantes concernant cette fonctionnalité :

* Un nouveau filtre vous permet de personnaliser votre affichage pour afficher uniquement les incidents, uniquement les alertes ou bien les deux. 
* Une même alerte peut s’afficher dans le cadre d’un incident (le cas échéant), mais également apparaître sous la forme d’une alerte autonome. 
* Ignorer un incident n’a pas pour effet d’ignorer les alertes associées.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez vu comment utiliser la fonctionnalité de gestion des incidents de sécurité du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
* [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
* [Guide des opérations et de planification du Centre de sécurité Azure](security-center-planning-and-operations-guide.md)
* [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/): trouvez des billets de blog sur la sécurité et la conformité Azure.




<!--HONumber=Nov16_HO2-->


