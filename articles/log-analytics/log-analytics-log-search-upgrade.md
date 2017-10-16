---
title: "Mise à niveau d’Azure Log Analytics pour la nouvelle fonctionnalité de recherche dans les journaux | Microsoft Docs"
description: "Le nouveau langage de requête Log Analytics sera bientôt disponible. En attendant, vous pouvez participer à la préversion publique.  Cet article décrit les avantages du nouveau langage et explique comment convertir votre espace de travail."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 4a3ee3c4d1fa9b626a51f24997603adceed8311f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>Mettre à niveau de votre espace de travail Azure Log Analytics pour la nouvelle fonctionnalité de recherche dans les journaux

> [!NOTE]
> La mise à niveau vers le nouveau langage de requête Log Analytics est actuellement facultative, afin de vous laisser le temps de vous [familiariser avec le nouveau langage](https://go.microsoft.com/fwlink/?linkid=856078).  

Le nouveau langage de requête Log Analytics est désormais disponible et vous devez mettre à niveau votre espace de travail pour pouvoir l’utiliser.  Cet article décrit les avantages du nouveau langage et explique comment convertir votre espace de travail.  Si vous choisissez de ne pas procéder à la mise à niveau pour le moment, votre espace de travail continuera de fonctionner exactement comme avant. Toutefois, il sera automatiquement converti à une date ultérieure.  Vous serez averti bien à l’avance de cette conversion.

Cet article fournit des informations détaillées sur le nouveau langage et le processus de mise à niveau.

## <a name="why-the-new-language"></a>Pourquoi un nouveau langage ?
Nous sommes conscients que toute transition implique des efforts. Sachez, toutefois, que cette modification était nécessaire.  Cette modification présente de nombreux avantages pour les utilisateurs Log Analytics.

- **Simple, mais puissant.** Le nouveau langage est plus facile à comprendre et similaire au langage SQL, avec des constructions plus proches du langage naturel que l’ancien langage.
- **Des fonctionnalités de redirection complètes.**  Le nouveau langage comprend des fonctionnalités de redirection plus complètes que celles de l’ancien langage.  Quasiment tous les résultats peuvent être redirigés vers une autre commande pour créer des requêtes plus complexes qu’avec l’ancien langage.
- **Extraction des champs au moment de la recherche.**  Le nouveau langage prend en charge des champs calculés au moment du runtime plus avancés que ceux de l’ancien langage.  Vous pouvez utiliser des calculs complexes pour les champs étendus, puis utiliser les champs calculés dans des commandes supplémentaires telles que des jointures et des agrégations.
- **Jointures avancées.**  Le nouveau langage fournit des jointures plus avancées que celles de l’ancien langage. Il permet notamment de joindre les tables de plusieurs champs, d’utiliser des jointures internes et externes, et de joindre des champs étendus.
- **Fonctions de date et d’heure.**  Le nouveau langage comprend davantage de fonctions de date/heure que l’ancien langage.
- **Analyses intelligentes.**  Le nouveau langage comprend des algorithmes avancés permettant d’évaluer les modèles de jeux de données et de comparer des jeux de données.
- **Portail Analytique avancée.**  Le portail Analytique avancée comprend des fonctionnalités d’analyse qui ne sont pas disponibles dans le portail Log Analytics, notamment l’édition multiligne de requêtes, des visualisations supplémentaires et des diagnostics avancés.
- **Cohérence avec les autres applications.**  Le nouveau langage et le portail Analytique avancée sont déjà utilisés pour les analyses dans Application Insights.  L’implémentation du nouveau langage pour Log Analytics permet une cohérence entre les services Azure.
- **Meilleure intégration avec Power BI.** Les requêtes dans le nouveau langage peuvent être exportées vers Power BI Desktop : vous pouvez donc utiliser ses riches fonctionnalités de transformation des données.
- **Et bien plus encore...** Pour obtenir des informations détaillées et des didacticiels sur le nouveau langage, consultez le site [Langage de requête Azure Log Analytics](https://docs.loganalytics.io).


## <a name="when-can-i-upgrade"></a>À quel moment puis-je effectuer la mise à niveau ?
La mise à niveau va être déployée dans toutes les régions Azure, et peut être disponible dans certaines régions avant d’autres.  Vous saurez que votre espace de travail est prêt pour la mise à niveau lorsque vous verrez une bannière violette en haut de votre espace de travail vous invitant à effectuer la mise à niveau.

![Mise à niveau 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>Que se passe-t-il quand j’effectue la mise à niveau ?
Lorsque vous convertissez votre espace de travail, toutes les recherches enregistrées, les règles d’alerte et les vues que vous avez créées avec le Concepteur de vues sont automatiquement converties dans le nouveau langage.  Les recherches incluses dans les solutions de recherche ne sont pas automatiquement converties. Elles le sont, cependant, lorsque vous les ouvrez.  Cela se fait de manière totalement transparente.

## <a name="can-i-go-back-after-i-upgrade"></a>Puis-je annuler la mise à niveau ?
Lorsque vous effectuez la mise à niveau, une sauvegarde complète de votre espace de travail est créée. La sauvegarde est constituée de la capture instantanée de toutes vos recherches enregistrées, de vos règles d’alerte et de vos vues.  Vous pouvez ainsi restaurer votre ancien espace de travail si vous le souhaitez.

Pour restaurer votre ancien espace de travail, accédez à **Paramètres** dans votre espace de travail, puis à **Résumé de la mise à niveau**.  Vous pouvez ensuite sélectionner l’option **Restaurer l’espace de travail hérité**.  

![Restaurer l’espace de travail hérité](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>Comment effectuer la mise à niveau ?
Lorsque vous voyez la bannière violette en haut du portail, vous êtes prêt à mettre à niveau votre espace de travail.  

1.  Démarrer le processus de mise à niveau en cliquant sur la bannière violette intitulée **En savoir plus et mise à niveau**.<br>![Mise à niveau 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  Pour plus d’informations sur la mise à niveau, consultez la page d’informations sur la mise à niveau.<br>![Mise à niveau 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  Pour démarrer la mise à niveau, cliquez sur **Mettre à niveau maintenant**.<br>![Mettre à niveau 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>L’état est affiché dans la zone de notification située dans le coin supérieur droit.<br>![Mettre à niveau 5](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  Et voilà !  Passez en revue la page Recherche dans les journaux pour voir à quoi ressemble votre nouvel espace de travail.<br>![Mise à jour 6](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

Si vous rencontrez un problème qui provoque l’échec de la mise à niveau, vous pouvez accéder au [Forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et envoyer votre question. Vous pouvez également [créer une demande de support](../azure-supportability/how-to-create-azure-support-request.md) dans le portail Azure.

## <a name="how-do-i-learn-the-new-language"></a>Comment en savoir plus sur le nouveau langage ?
Dans la mesure où il est utilisé par plusieurs services, nous avons créé un [site externe pour héberger la documentation](https://docs.loganalytics.io/) relative au nouveau langage.  Cette documentation inclut des didacticiels, des exemples, ainsi qu’une référence complète sur le langage, afin de vous aider à améliorer vos connaissances. Pour lire un didacticiel sur le nouveau langage, consultez le [Guide pratique sur les requêtes](https://go.microsoft.com/fwlink/?linkid=856078). Pour accéder à la référence du langage, consultez [Langage de requête Log Analytics](https://go.microsoft.com/fwlink/?linkid=856079).  

Si vous êtes déjà familiarisé avec l’ancien langage de requête Log Analytics, vous pouvez utiliser le convertisseur de langage qui est ajouté à votre espace de travail dans le cadre de la mise à niveau.

Il vous suffit de taper votre requête dans l’ancien langage, puis de cliquer sur **Convertir** pour afficher la version traduite en nouveau langage.  Vous pouvez ensuite cliquer sur le bouton de recherche pour exécuter la recherche, ou copier-coller la requête convertie pour l’utiliser ailleurs (par exemple, dans une règle d’alerte).

![Convertisseur de langage](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Étapes suivantes
- Consultez le [didacticiel sur le nouveau langage](https://go.microsoft.com/fwlink/?linkid=856078).
- Suivez le [didacticiel sur l’utilisation du portail Recherche dans les journaux](log-analytics-log-search-log-search-portal.md) avec le nouveau langage de requête.
- Lisez la présentation du nouveau [portail Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587).
