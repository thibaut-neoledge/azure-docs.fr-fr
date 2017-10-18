---
title: Examiner les incidents et les alertes dans Azure Security Center | Microsoft Docs
description: "Ce document vous aide à utiliser la fonctionnalité d’examen dans Azure Security Center pour examiner les incidents et alertes liés à la sécurité."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: 818c257d1959936f0dc326486e372677aacb065a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Examiner les incidents et les alertes dans Azure Security Center (version préliminaire)
Ce document vous aide à utiliser la fonctionnalité d’examen dans Azure Security Center pour examiner les incidents et alertes liés à la sécurité.

## <a name="what-is-investigation-in-security-center"></a>Qu’est-ce que la fonctionnalité d’examen dans Security Center ?
La fonctionnalité d’examen dans Security Center vous permet de trier, comprendre l’étendue et repérer la cause racine d’un potentiel [incident lié à la sécurité](https://docs.microsoft.com/azure/security-center/security-center-incident).
 
Le but est de facilité le processus d’examen en liant toutes les entités ([alertes de sécurité](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), utilisateurs, ordinateurs et incidents) impliquées dans l’incident en cours d’examen.  Security Center peut faire cela en mettant en corrélation des données pertinentes et n’importe quelle entité impliquée, et en exposant cette corrélation via un graphique en temps réel qui vous aide à naviguer entre les objets et à visualiser les informations importantes.


## <a name="how-investigation-works"></a>Comment fonctionne un examen ?
L’examen intègre un graphique qui occupe la zone centrale du tableau de bord d’examen. Le graphique se concentre toujours sur une entité spécifique et présente les entités qui sont associées. Une alerte de sécurité, un utilisateur, un ordinateur ou un incident sont des entités.
 
![Mappage](./media/security-center-investigation/security-center-investigation-fig1.png)

L’utilisateur peut naviguer d’une entité à une autre en cliquant dessus dans le graphique. Le graphique centralise automatiquement l’entité sélectionnée et celles qui lui sont associées. Les entités qui ne sont plus pertinentes peuvent être supprimées du graphique.

### <a name="investigation-path"></a>Chemin d’examen
Le chemin d’examen aide à suivre le contexte d’examen et permet une navigation rapide à l’utilisateur qui navigue entre les différentes entités. L’incident contenant les résultats de l’examen apparaît toujours tout à gauche dans le chemin d’examen.

![Chemin](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Informations générales
Lorsqu’une entité est présentée dans le graphique, les onglets affichent des informations supplémentaires la concernant. L’onglet **Informations** présente des informations générales sur l’entité, de différentes sources d’informations disponibles. 

![Informations générales](./media/security-center-investigation/security-center-investigation-fig3.png)

L’onglet Informations affiche des informations pertinentes concernant l’incident sélectionné sur la carte. L’incident est un conteneur qui comprend les résultats d’un examen. Chaque examen se déroule au sein du contexte d’un incident.

Un incident n’est créé que lorsqu’un utilisateur clique sur le bouton **Démarrer l’examen** pour une alerte spécifique. La fonctionnalité disponible de base pour l’investigation est le marquage d’entité telle qu’un utilisateur, un ordinateur ou une alerte. Lorsqu’une entité est marquée comme associée, une raison est fournie. À partir de là, l’entité apparaît directement sous l’incident dans le graphique, ainsi que dans la liste des entités Incident.

### <a name="entities"></a>Entités

L’onglet **Entités** affiche toutes les entités associées, regroupées par type. Cet onglet est utile dans deux cas : lorsqu’il y a trop d’entités à présenter dans le graphique, et lorsque les noms des entités sont trop longs. Il est alors plus simple de les consulter sous forme de tableau.

![Entités](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Search

L’onglet **Recherche** présente tous les types de journaux disponibles pour l’entité. Pour chaque type de journal, vous pouvez voir le nombre d’enregistrements disponibles. En cliquant sur n’importe quel type de journal, vous êtes amené à l’écran de recherche. Sur l’écran de recherche, vous pouvez affiner votre recherche et utiliser diverses fonctionnalités telles que la définition d’alertes. Dans la version actuelle, l’onglet Recherche n’est disponible que pour les entités Utilisateurs et Ordinateurs.

![Search](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Exploration

L’onglet **Exploration** permet à l’investigateur d’examiner les données associées à des problèmes divers, eux-mêmes associés à l’entité. Par exemple, lorsqu’une machine est examinée, la liste des processus exécutés est affichée dans l’onglet Exploration. Dans certains cas, l’onglet Exploration présente les données qui pourraient indiquer un problème suspect. L’investigateur peut examiner les données au sein de l’onglet ou les ouvrir dans l’écran de recherche pour en examiner un plus gros volume et utiliser des options de recherche avancées comme le filtrage ou l’exportation en fichier Excel.

![Exploration](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Chronologie

La plupart des données présentées dans le graphique et dans les divers onglets sont pertinentes durant une période donnée. Cette durée est définie à l’aide du sélecteur de durée, situé dans le coin supérieur gauche du graphique. L’investigateur dispose de plusieurs méthodes pour sélectionner la durée. 

![Chronologie](./media/security-center-investigation/security-center-investigation-fig7.png)

Les éléments suivants sont sensibles à la durée :

- Les relations utilisateur-ordinateur dans le graphique : seuls les utilisateurs connectés à l’ordinateur pendant cette durée sont présentés.
- Les alertes présentées lorsque vous examinez des ordinateurs et des utilisateurs : seules les alertes qui surviennent pendant cette durée sont présentées.
- L’onglet Entités suit la même logique que celle du graphique.

Les éléments suivants sont présentés, quelle que soit la durée définie :

- Lorsqu’une alerte est présentée, les entités qu’elle contient, comme les utilisateurs et les ordinateurs, sont toujours présentées.
- Si un incident contient une entité, elle est présentée.

> [!NOTE]
> Les onglets **Recherche** et **Exploration** n’affichent que les enregistrements pendant cette durée.

## <a name="how-to-perform-an-investigation"></a>Comment effectuer un examen ?

Vous pouvez démarrer un examen depuis un incident de sécurité, ou une alerte. L’option choisie varie selon vos besoins. La procédure suivante explique comment démarrer un examen depuis une alerte :

1.  Ouvrez le tableau de bord **Security Center**.
2.  Cliquez sur **Alertes de sécurité** puis sélectionnez l’incident que vous souhaitez examiner.
3.  Sur la page de l’incident, cliquez sur le bouton **Démarrer l’examen**. Le tableau de bord **Examen** apparaît alors.

    ![Alerte](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Dans ce tableau de bord, vous pouvez sélectionner l’entité sur la carte. Les informations pertinentes la concernant apparaissent sur la droite de l’écran.

    ![Tableau de bord Examen](./media/security-center-investigation/security-center-investigation-fig9.png)

À ce stade, vous pouvez explorer les entités impliquées dans l’incident et en découvrir davantage sur chacune d’elles. 

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez vu comment utiliser la fonctionnalité d’examen dans Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center. 
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

